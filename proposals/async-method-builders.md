---
ms.openlocfilehash: c1ff78f64b1529e6b648d5cbe5b80e507642fac8
ms.sourcegitcommit: ec31c2771e390305a430c407e29f72d8299a4c72
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/09/2021
ms.locfileid: "99985903"
---
# <a name="asyncmethodbuilder-override"></a>AsyncMethodBuilder 替代

* [x] 建议
* [] 原型：未启动
* [] 实现：未启动
* [] 规范：未启动

## <a name="summary"></a>总结
[summary]: #summary

允许使用异步方法生成器的每个方法重写。
对于某些异步方法，我们希望自定义的调用 `Builder.Create()` ，以使用不同的 _生成器类型_ ，并且可能会传递一些附加的状态信息。

```C#
[AsyncMethodBuilderOverride(typeof(PoolingAsyncValueTaskMethodBuilder<int>))] // new, referring to some custom builder type
static async ValueTask<int> ExampleAsync() { ... }
```

## <a name="motivation"></a>动机
[motivation]: #motivation

目前，异步方法生成器绑定到用作异步方法返回类型的给定类型。  例如，声明为使用的任何方法 `async Task` `AsyncTaskMethodBuilder` 以及声明为使用的任何方法 `async ValueTask<T>` `AsyncValueTaskMethodBuilder<T>` 。  这是因为 `[AsyncMethodBuilder(Type)]` 类型上的属性用作返回类型，例如， `ValueTask<T>` 被属性化为 `[AsyncMethodBuilder(typeof(AsyncValueTaskMethodBuilder<>))]` 。 这解决了大多数常见的情况，但对于高级方案，它留下了几个值得注意的漏洞。

在 .NET 5 中，提供了一项试验性功能，该功能提供了两种模式 `AsyncValueTaskMethodBuilder` `AsyncValueTaskMethodBuilder<T>` 。  默认情况下，该模式与在引入功能时相同：需要将状态机提升到堆时，将分配一个对象来存储状态，而异步方法返回 `ValueTask{<T>}` 由提供支持的 `Task{<T>}` 。  但是，如果设置了环境变量，则进程中的所有生成器都切换到一种模式，在该模式下， `ValueTask{<T>}` 实例由已建立池的可重用实现进行支持 `IValueTaskSource{<T>}` 。  每个 async 方法都具有其自己的池，该池具有已允许进行缓冲处理的最大实例数，只要该数目不超过该数量就会同时被汇集到池中，就 `async ValueTask<{T}>` 会有效地释放任何 GC 分配开销。

此实验模式有几个问题，不过，这是为什么) 默认情况下处于关闭状态的原因，而 b) 我们很可能会在将来的版本中将其删除，除非 (会发现非常引人注目的新信息 https://github.com/dotnet/runtime/issues/13633) 。
- `ValueTask{<T>}`如果 `ValueTask` 未根据规范使用，则会为返回的使用者引入行为差异。 当它由提供支持时，你可以使用来执行的操作 `Task` `ValueTask` `Task` ，如等待多次，同时等待，等待它完成等。 但当它由任意的支持时， `IValueTaskSource` 此类操作将被禁止，并自动从前者切换到后者可能导致错误。  在进程级别切换并影响进程中的所有 `async ValueTask` 方法时，无论是否控制它们，都是太大的 hammer。
- 这并不一定是性能入选，在某些情况下可能会表示回归。  这种实现方式是对 (访问池的池的成本进行交易，而不是使用 GC 的成本) ，在各种情况下，GC 就会入选。  同样，将池应用到 `async ValueTask` 进程中的所有方法，而不是选择性地将其最有利的方法 hammer。
- 它将添加到已剪裁应用程序的 IL 大小，即使未设置标志，然后也添加到生成的 asm 大小。  可能会解决实现的改进，使其能够向 it 人员介绍环境变量将始终为 false，但正如目前一样，每个 `async ValueTask` 方法都看到示例 ~ 2k 的二进制占用量会增加 aot 映像，因为此选项，并且同样适用于 `async ValueTask` 整个应用程序闭包中的所有方法。
- 不同的方法可能会受益于不同的控制级别，例如，由于了解方法和使用方式而使用的池大小，但相同的设置会应用于生成器的所有使用。  通过让生成器代码在运行时使用反射来查找某些属性（但这会增加重要的运行时支出，并且可能会在启动路径上），可以想象出这样的工作。

在现有池的所有这些问题的基础上，也会阻止开发人员为他们不拥有的类型编写自己的自定义生成器。  例如，如果开发人员想要实现其自己的池支持，则他们还必须引入全新的类似任务的类型，而不是仅能使用 `{Value}Task{<T>}` ，因为指定生成器的属性仅可指定返回类型的类型声明。

我们需要一种方法，让单独的异步方法选择特定的生成器。

## <a name="detailed-design"></a>详细设计
[design]: #detailed-design

#### <a name="p0-asyncmethodbuilderoverrideattribute-applied-on-async-methods"></a>P0： AsyncMethodBuilderOverrideAttribute 应用于异步方法

在中 `dotnet/runtime` ，添加 `AsyncMethodBuilderOverrideAttribute` ：
```csharp
namespace System.Runtime.CompilerServices
{
    /// <summary>
    /// Indicates the type of the async method builder that should be used by a language compiler to
    /// build the attributed method.
    /// </summary>
    [AttributeUsage(AttributeTargets.Class | AttributeTargets.Struct | AttributeTargets.Interface | AttributeTargets.Method | AttributeTargets.Module, Inherited = false, AllowMultiple = false)]
    public sealed class AsyncMethodBuilderOverrideAttribute : Attribute
    {
        /// <summary>Initializes the <see cref="AsyncMethodBuilderOverrideAttribute"/>.</summary>
        /// <param name="builderType">The <see cref="Type"/> of the associated builder.</param>
        public AsyncMethodBuilderOverrideAttribute(Type builderType) => BuilderType = builderType;

        // for scoped application (use property for targetReturnType? have compiler check that it's provided)
        public AsyncMethodBuilderOverrideAttribute(Type builderType, Type targetReturnType) => ...

        /// <summary>Gets the <see cref="Type"/> of the associated builder.</summary>
        public Type BuilderType { get; }
    }
}
```

在 c # 编译器中，在确定要对类型上定义的生成器使用哪一个生成器时，首选方法上的特性。  例如，今天如果方法定义为：
```C#
public async ValueTask<T> ExampleAsync() { ... }
```
编译器将生成类似于下面的代码：
```C#
[AsyncStateMachine(typeof(<ExampleAsync>d__29))]
[CompilerGenerated]
static ValueTask<int> ExampleAsync()
{
    <ExampleAsync>d__29 stateMachine;
    stateMachine.<>t__builder = AsyncValueTaskMethodBuilder<int>.Create();
    stateMachine.<>1__state = -1;
    stateMachine.<>t__builder.Start(ref stateMachine);
    return stateMachine.<>t__builder.Task;
}
```
如果开发人员编写了此更改，请执行以下操作：
```C#
[AsyncMethodBuilderOverride(typeof(PoolingAsyncValueTaskMethodBuilder<int>))] // new, referring to some custom builder type
static async ValueTask<int> ExampleAsync() { ... }
```
相反，它将编译为：
```C#
[AsyncStateMachine(typeof(<ExampleAsync>d__29))]
[CompilerGenerated]
[AsyncMethodBuilder(typeof(PoolingAsyncValueTaskMethodBuilder<int>))] // retained but not necessary anymore
static ValueTask<int> ExampleAsync()
{
    <ExampleAsync>d__29 stateMachine;
    stateMachine.<>t__builder = PoolingAsyncValueTaskMethodBuilder<int>.Create(); // <>t__builder now a different type
    stateMachine.<>1__state = -1;
    stateMachine.<>t__builder.Start(ref stateMachine);
    return stateMachine.<>t__builder.Task;
}
```

只需少量的添加操作即可：
- 任何人都可以编写自己的生成器，该生成器可应用于返回和的异步方法 `Task<T>``ValueTask<T>`
- 作为 "任何人"，运行时将实验性生成器支持作为新的公共生成器类型提供，可以选择在方法的基础上进行选择;现有的支持将从现有生成器中删除。   (包括核心库中的一些必要的方法) 随后可以根据具体情况进行特性化，以使用池支持，而不会影响任何其他无归属方法。

在编译器中，使用最小的面区更改或功能工作。


请注意，我们需要发出的代码，以允许从方法返回不同的类型 `Create` ：
```
AsyncPooledBuilder _builder = AsyncPooledBuilderWithSize4.Create();
```

#### <a name="p1-passing-state-to-the-builder-instantiation"></a>P1：将状态传递到生成器实例化

在某些情况下，向生成器传递某些信息会很有用。  这可能是静态 (例如，将池的大小配置为使用) 或动态 (例如，引用缓存或单独使用) 。

下面是 brainstormed 记录的几个选项 (记录) 但最终建议不执行任何操作 (选项 #0) ，直到我们确定支持动态状态值得。

##### <a name="option-0-no-extra-state"></a>选项0：无附加状态

可以通过包装生成器类型来近似传递静态信息。
例如，可以创建一个 hardcodes 特定配置的自定义生成器类型：

```C#
public struct AsyncPooledBuilderWithSize4
{
    AsyncPooledBuilder Create() => AsyncPooledBuilder.Create(4);
}
```

##### <a name="option-1-use-constants-in-attribute-as-arguments-for-create"></a>选项1：使用属性中的常量作为的参数 `Create`

`AsyncMethodBuilderOverrideAttribute`将接受一些其他信息：
```C#
    public AsyncMethodBuilderOverrideAttribute(Type builderType, params object[] args)
```

在特性中收集的参数：
```C#
[AsyncMethodBuilderOverride(typeof(AsyncPooledBuilder), 4)]
```
在调用方法时使用 `Create` ：
```C#
AsyncPooledBuilder.Create(4);
```

##### <a name="option-2-use-arguments-of-the-async-method"></a>选项2：使用 async 方法的参数

除此之外，还可以 `AsyncMethodBuilderOverrideAttribute` 使用属性来标记其中一个异步方法的参数：
```C#
[AsyncMethodBuilderOverride(typeof(AsyncPooledBuilder))]
async ValueTask MyMethodAsync(/* regular arguments */, [FOR_BUILDER] int i)
```

这会导致将该参数的值传递给 `Create` 调用：
```C#
BuilderType.Create(i);
```

在大多数情况下，用户最终会编写包装来实现所需的签名：
```C#
public ValueTask MyMethodWrapperAsync(/* regular parameters */)
{
    return MyMethodAsync(/* pass values from regular parameters through */, 4); // static or dynamic value for the builder
}
```

甚至可以通过这种方式传递缓存的委托：
```C#
.ctor()
{
    s_func_take = () => get_item();
    s_action_put = t => free_item(t);
}

public ValueTask MyMethodWrapperAsync(/* regular parameters */)
{
   return MyMethodAsync(/* pass values from regular parameters through */, (s_func_take, s_action_put));
}
```

此方法类似于 `EnumeratorCancellationAttribute` 工作方式。 但额外的参数对用户代码不起作用，因此我们将污染签名和状态机。
此方法与选项 #1 重叠，因此可能不需要同时支持这两种方法。

##### <a name="option-3-pass-a-lambda-that-instantiates-builders"></a>选项3：传递实例化生成器的 lambda

作为 (的替代 `AsyncMethodBuilderOverrideAttribute` 方法或可能的补充) ，我们将使用一个特性来标记其中一个异步方法的参数：
```C#
async ValueTask MyMethodAsync(/* regular parameters */, [FOR_BUILDER] Func<AsyncPooledBuilder> lambda)
```
该参数需要具有没有参数的委托类型并返回生成器类型。

编译器可以生成：
```C#
...
static ValueTask<int> MyMethodAsync(/* regular parameters */, [FOR_BUILDER] Func<AsyncPooledBuilder> lambda)
{
    <MyMethodAsync>d__29 stateMachine;
    stateMachine.<>t__builder = lambda();
    ...
}
```

我们仍然遇到了方法签名和状态机污染的问题。

如果需要包含两个缓存的委托的生成器

```C#
.ctor()
{
    s_func_take = () => get_item();
    s_action_put = t => free_item(t);
    s_func = () => Builder.Create(take: s_func_take, put: s_action_put);
}

public ValueTask MyMethodWrapperAsync(...)
{
    return MyMethodAsync(..., s_func);
}
```

#### <a name="p2-enable-at-the-module-and-type-level-as-well"></a>P2：在模块 (启用，并同时键入？ ) 级别

希望对其所有方法使用特定自定义生成器的开发人员可以通过对每个方法进行相关属性来实现此目的。  但我们也可以在模块或类型级别启用特性化，在这种情况下，该范围内的每个相关方法都将表现为直接批注，例如
```C#
[module: AsyncMethodBuilderOverride(typeof(PoolingAsyncValueTaskMethodBuilder), typeof(ValueTask)]
[module: AsyncMethodBuilderOverride(typeof(PoolingAsyncValueTaskMethodBuilder<>), typeof(ValueTask<>)]

class MyClass
{
    public async ValueTask Method1Async() { ... } // would use PoolingAsyncValueTaskMethodBuilder
    public async ValueTask<int> Method2Async() { ... } // would use PoolingAsyncValueTaskMethodBuilder<int>
    public async ValueTask<string> Method3Async() { ... } // would use PoolingAsyncValueTaskMethodBuilder<string>
}
```

为此，我们将向属性添加以下成员：
```C#
namespace System.Runtime.CompilerServices
{
    [AttributeUsage(AttributeTargets.Class | AttributeTargets.Struct | AttributeTargets.Interface | AttributeTargets.Method | AttributeTargets.Module, Inherited = false, AllowMultiple = false)]
    public sealed class AsyncMethodBuilderOverrideAttribute : Attribute
    {
        ...
        // for scoped application (use property for targetReturnType? have compiler check that it's provided)
        public AsyncMethodBuilderOverrideAttribute(Type builderType, Type targetReturnType) => ...

        public Type TargetReturnType { get; }
    }
}
```

这不仅比将属性置于每个方法更方便，还可以更轻松地使用不同生成器的不同版本。  例如，针对吞吐量进行优化的生成可能包括一个 .cs 文件，该文件在模块级特性中指定一个池生成器，而针对大小优化的生成可能包含一个 .cs 文件，该文件指定一个简单生成器，该生成器将使用更多分配/装箱，而不是导致代码膨胀的许多泛型专用化和吞吐量优化。


## <a name="drawbacks"></a>缺点
[drawbacks]: #drawbacks

* 将此类属性应用于方法的语法是详细的。  如果开发人员可以将其应用于多种方法，例如在类型或模块级别，则会降低这种影响。

## <a name="alternatives"></a>备选方法
[alternatives]: #alternatives

- 实现其他类似任务的类型，并向使用者公开此差异。  `ValueTask` 可通过接口进行扩展， `IValueTaskSource` 以避免这种需要。
- 通过将实验启用为默认的和仅限的实现来解决该问题的 ValueTask 池。  这并不能解决其他方面，例如配置池，或使其他人能够提供自己的生成器。

## <a name="unresolved-questions"></a>未解决的问题
[unresolved]: #unresolved-questions

1. **Attribute.** 是否应重用 `[AsyncMethodBuilder(typeof(...))]` 或引入其他属性？
2. **替换或创建。** 本方案中的所有示例都是关于替换类似可生成的生成器。  是否应将该功能的作用域限定为？ 或者，是否能够对具有不具有生成器的返回类型的方法使用此属性（例如，某些常见接口)  (？  这可能会影响重载解析。
3. **虚方法/接口。** 如果在接口方法上指定了属性，会出现什么情况？  我认为它应是 nop 或编译器警告/错误，但它不应影响接口的实现。  对于重写的基方法，存在一个类似的问题，再次又不认为基方法上的属性会影响替代实现的行为方式。 请注意，当前属性在其 AttributeUsage 上继承了 = false。
4. **低于.** 如果我们想要执行模块/类型级别的批注，则需要确定在应用了多个归属 (例如，在方法上，一个在包含模块) 上。  我们还需要确定这是否会有必要使用不同的属性 (参阅 (1) 上方) ，例如，如果类似于任务的类型在相同的作用域中，该行为是怎样的？  或者，如果类似可生成的任务本身具有 async 方法，它们是否会受到应用于类似于任务的类型的属性的影响，以指定其默认生成器？
5. **专用构建** 者。 编译器是否应支持非公共 async 方法生成器？ 这并不是目前的规范，但我们只支持公共的 experimentally。  当将特性应用于类型来控制与该类型一起使用的生成器时，这会非常有意义，因为任何使用该类型编写的异步方法作为返回类型都需要具有访问生成器的权限。  但是，使用这项新功能时，将该特性应用于方法时，它只会影响该方法的实现，因此可合理引用非公共生成器。  我们可能想要支持具有其要使用的非公共用户的库作者。
6. **传递状态，以实现更高效的池**。  请考虑一种类型，如 System.net.security.sslstream 或 WebSocket。  这些操作将公开读/写异步操作，并允许同时进行读写操作，但一次最多可以执行1个读取操作，并且一次最多可以执行1个写入操作。  这使得它们适用于池，因为每个 System.net.security.sslstream 或 WebSocket 实例最多需要一个用于读取的池对象，另一个用于写入。  而且，集中式池是多余的：无需支付需要进行管理和访问同步的中心池的开销，每个 System.net.security.sslstream/WebSocket 只需维护一个字段来存储读取器的单一实例，并为编写器提供单一实例，从而消除了池的所有争用，并消除了与池限制关联的所有管理。  问题在于，如何将实例上的任意字段连接到生成器所采用的池机制。  如果将异步方法的所有参数传递到生成器的 Create 方法上的相应签名 (或可能是单独的 Bind 方法，或者某些这样的操作) ，则至少可以进行此操作，而生成器则需要专门针对该特定类型，了解其字段。  Create 方法可能会采用出租委托和返回委托，异步方法可以专门创建以接受此类参数 (以及要传入) 的对象状态。  在这里提供一个不错的解决方案，因为这会使机制大大增强和有价值。

## <a name="design-meetings"></a>设计会议

链接到影响此建议的设计说明，并在一个句子中介绍它们所导致的更改。

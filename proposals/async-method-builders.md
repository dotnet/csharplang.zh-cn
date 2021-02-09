---
ms.openlocfilehash: c1ff78f64b1529e6b648d5cbe5b80e507642fac8
ms.sourcegitcommit: ec31c2771e390305a430c407e29f72d8299a4c72
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/09/2021
ms.locfileid: "99985903"
---
# <a name="asyncmethodbuilder-override"></a><span data-ttu-id="708dd-101">AsyncMethodBuilder 替代</span><span class="sxs-lookup"><span data-stu-id="708dd-101">AsyncMethodBuilder override</span></span>

* <span data-ttu-id="708dd-102">[x] 建议</span><span class="sxs-lookup"><span data-stu-id="708dd-102">[x] Proposed</span></span>
* <span data-ttu-id="708dd-103">[] 原型：未启动</span><span class="sxs-lookup"><span data-stu-id="708dd-103">[ ] Prototype: Not Started</span></span>
* <span data-ttu-id="708dd-104">[] 实现：未启动</span><span class="sxs-lookup"><span data-stu-id="708dd-104">[ ] Implementation: Not Started</span></span>
* <span data-ttu-id="708dd-105">[] 规范：未启动</span><span class="sxs-lookup"><span data-stu-id="708dd-105">[ ] Specification: Not Started</span></span>

## <a name="summary"></a><span data-ttu-id="708dd-106">总结</span><span class="sxs-lookup"><span data-stu-id="708dd-106">Summary</span></span>
[summary]: #summary

<span data-ttu-id="708dd-107">允许使用异步方法生成器的每个方法重写。</span><span class="sxs-lookup"><span data-stu-id="708dd-107">Allow per-method override of the async method builder to use.</span></span>
<span data-ttu-id="708dd-108">对于某些异步方法，我们希望自定义的调用 `Builder.Create()` ，以使用不同的 _生成器类型_ ，并且可能会传递一些附加的状态信息。</span><span class="sxs-lookup"><span data-stu-id="708dd-108">For some async methods we want to customize the invocation of `Builder.Create()` to use a different _builder type_ and possibly pass some additional state information.</span></span>

```C#
[AsyncMethodBuilderOverride(typeof(PoolingAsyncValueTaskMethodBuilder<int>))] // new, referring to some custom builder type
static async ValueTask<int> ExampleAsync() { ... }
```

## <a name="motivation"></a><span data-ttu-id="708dd-109">动机</span><span class="sxs-lookup"><span data-stu-id="708dd-109">Motivation</span></span>
[motivation]: #motivation

<span data-ttu-id="708dd-110">目前，异步方法生成器绑定到用作异步方法返回类型的给定类型。</span><span class="sxs-lookup"><span data-stu-id="708dd-110">Today, async method builders are tied to a given type used as a return type of an async method.</span></span>  <span data-ttu-id="708dd-111">例如，声明为使用的任何方法 `async Task` `AsyncTaskMethodBuilder` 以及声明为使用的任何方法 `async ValueTask<T>` `AsyncValueTaskMethodBuilder<T>` 。</span><span class="sxs-lookup"><span data-stu-id="708dd-111">For example, any method that's declared as `async Task` uses `AsyncTaskMethodBuilder`, and any method that's declared as `async ValueTask<T>` uses `AsyncValueTaskMethodBuilder<T>`.</span></span>  <span data-ttu-id="708dd-112">这是因为 `[AsyncMethodBuilder(Type)]` 类型上的属性用作返回类型，例如， `ValueTask<T>` 被属性化为 `[AsyncMethodBuilder(typeof(AsyncValueTaskMethodBuilder<>))]` 。</span><span class="sxs-lookup"><span data-stu-id="708dd-112">This is due to the `[AsyncMethodBuilder(Type)]` attribute on the type used as a return type, e.g. `ValueTask<T>` is attributed as `[AsyncMethodBuilder(typeof(AsyncValueTaskMethodBuilder<>))]`.</span></span> <span data-ttu-id="708dd-113">这解决了大多数常见的情况，但对于高级方案，它留下了几个值得注意的漏洞。</span><span class="sxs-lookup"><span data-stu-id="708dd-113">This addresses the majority common case, but it leaves a few notable holes for advanced scenarios.</span></span>

<span data-ttu-id="708dd-114">在 .NET 5 中，提供了一项试验性功能，该功能提供了两种模式 `AsyncValueTaskMethodBuilder` `AsyncValueTaskMethodBuilder<T>` 。</span><span class="sxs-lookup"><span data-stu-id="708dd-114">In .NET 5, an experimental feature was shipped that provides two modes in which `AsyncValueTaskMethodBuilder` and `AsyncValueTaskMethodBuilder<T>` operate.</span></span>  <span data-ttu-id="708dd-115">默认情况下，该模式与在引入功能时相同：需要将状态机提升到堆时，将分配一个对象来存储状态，而异步方法返回 `ValueTask{<T>}` 由提供支持的 `Task{<T>}` 。</span><span class="sxs-lookup"><span data-stu-id="708dd-115">The on-by-default mode is the same as has been there since the functionality was introduced: when the state machine needs to be lifted to the heap, an object is allocated to store the state, and the async method returns a `ValueTask{<T>}` backed by a `Task{<T>}`.</span></span>  <span data-ttu-id="708dd-116">但是，如果设置了环境变量，则进程中的所有生成器都切换到一种模式，在该模式下， `ValueTask{<T>}` 实例由已建立池的可重用实现进行支持 `IValueTaskSource{<T>}` 。</span><span class="sxs-lookup"><span data-stu-id="708dd-116">However, if an environment variable is set, all builders in the process switch to a mode where, instead, the `ValueTask{<T>}` instances are backed by reusable `IValueTaskSource{<T>}` implementations that are pooled.</span></span>  <span data-ttu-id="708dd-117">每个 async 方法都具有其自己的池，该池具有已允许进行缓冲处理的最大实例数，只要该数目不超过该数量就会同时被汇集到池中，就 `async ValueTask<{T}>` 会有效地释放任何 GC 分配开销。</span><span class="sxs-lookup"><span data-stu-id="708dd-117">Each async method has its own pool with a fixed maximum number of instances allowed to be pooled, and as long as no more than that number are ever returned to the pool to be pooled at the same time, `async ValueTask<{T}>` methods effectively become free of any GC allocation overhead.</span></span>

<span data-ttu-id="708dd-118">此实验模式有几个问题，不过，这是为什么) 默认情况下处于关闭状态的原因，而 b) 我们很可能会在将来的版本中将其删除，除非 (会发现非常引人注目的新信息 https://github.com/dotnet/runtime/issues/13633) 。</span><span class="sxs-lookup"><span data-stu-id="708dd-118">There are several problems with this experimental mode, however, which is both why a) it's off by default and b) we're likely to remove it in a future release unless very compelling new information emerges (https://github.com/dotnet/runtime/issues/13633).</span></span>
- <span data-ttu-id="708dd-119">`ValueTask{<T>}`如果 `ValueTask` 未根据规范使用，则会为返回的使用者引入行为差异。 当它由提供支持时，你可以使用来执行的操作 `Task` `ValueTask` `Task` ，如等待多次，同时等待，等待它完成等。 但当它由任意的支持时， `IValueTaskSource` 此类操作将被禁止，并自动从前者切换到后者可能导致错误。</span><span class="sxs-lookup"><span data-stu-id="708dd-119">It introduces a behavioral difference for consumers of the returned `ValueTask{<T>}` if that `ValueTask` isn't being consumed according to spec.  When it's backed by a `Task`, you can do with the `ValueTask` things you can do with a `Task`, like await it multiple times, await it concurrently, block waiting for it to complete, etc.  But when it's backed by an arbitrary `IValueTaskSource`, such operations are prohibited, and automatically switching from the former to the latter can lead to bugs.</span></span>  <span data-ttu-id="708dd-120">在进程级别切换并影响进程中的所有 `async ValueTask` 方法时，无论是否控制它们，都是太大的 hammer。</span><span class="sxs-lookup"><span data-stu-id="708dd-120">With the switch at the process level and affecting all `async ValueTask` methods in the process, whether you control them or not, it's too big a hammer.</span></span>
- <span data-ttu-id="708dd-121">这并不一定是性能入选，在某些情况下可能会表示回归。</span><span class="sxs-lookup"><span data-stu-id="708dd-121">It's not necessarily a performance win, and could represent a regression in some situations.</span></span>  <span data-ttu-id="708dd-122">这种实现方式是对 (访问池的池的成本进行交易，而不是使用 GC 的成本) ，在各种情况下，GC 就会入选。</span><span class="sxs-lookup"><span data-stu-id="708dd-122">The implementation is trading the cost of pooling (accessing a pool isn't free) with the cost of GC, and in various situations the GC can win.</span></span>  <span data-ttu-id="708dd-123">同样，将池应用到 `async ValueTask` 进程中的所有方法，而不是选择性地将其最有利的方法 hammer。</span><span class="sxs-lookup"><span data-stu-id="708dd-123">Again, applying the pooling to all `async ValueTask` methods in the process rather than being selective about the ones it would most benefit is too big a hammer.</span></span>
- <span data-ttu-id="708dd-124">它将添加到已剪裁应用程序的 IL 大小，即使未设置标志，然后也添加到生成的 asm 大小。</span><span class="sxs-lookup"><span data-stu-id="708dd-124">It adds to the IL size of a trimmed application, even if the flag isn't set, and then to the resulting asm size.</span></span>  <span data-ttu-id="708dd-125">可能会解决实现的改进，使其能够向 it 人员介绍环境变量将始终为 false，但正如目前一样，每个 `async ValueTask` 方法都看到示例 ~ 2k 的二进制占用量会增加 aot 映像，因为此选项，并且同样适用于 `async ValueTask` 整个应用程序闭包中的所有方法。</span><span class="sxs-lookup"><span data-stu-id="708dd-125">It's possible that can be worked around with improvements to the implementation to teach it that for a given deployment the environment variable will always be false, but as it stands today, every `async ValueTask` method saw for example an ~2K binary footprint increase in aot images due to this option, and, again, that applies to all `async ValueTask` methods in the whole application closure.</span></span>
- <span data-ttu-id="708dd-126">不同的方法可能会受益于不同的控制级别，例如，由于了解方法和使用方式而使用的池大小，但相同的设置会应用于生成器的所有使用。</span><span class="sxs-lookup"><span data-stu-id="708dd-126">Different methods may benefit from differing levels of control, e.g. the size of the pool employed because of knowledge of the method and how it's used, but the same setting is applied to all uses of the builder.</span></span>  <span data-ttu-id="708dd-127">通过让生成器代码在运行时使用反射来查找某些属性（但这会增加重要的运行时支出，并且可能会在启动路径上），可以想象出这样的工作。</span><span class="sxs-lookup"><span data-stu-id="708dd-127">One could imagine working around that by having the builder code use reflection at runtime to look for some attribute, but that adds significant run-time expense, and likely on the startup path.</span></span>

<span data-ttu-id="708dd-128">在现有池的所有这些问题的基础上，也会阻止开发人员为他们不拥有的类型编写自己的自定义生成器。</span><span class="sxs-lookup"><span data-stu-id="708dd-128">On top of all of these issues with the existing pooling, it's also the case that developers are prevented from writing their own customized builders for types they don't own.</span></span>  <span data-ttu-id="708dd-129">例如，如果开发人员想要实现其自己的池支持，则他们还必须引入全新的类似任务的类型，而不是仅能使用 `{Value}Task{<T>}` ，因为指定生成器的属性仅可指定返回类型的类型声明。</span><span class="sxs-lookup"><span data-stu-id="708dd-129">If, for example, a developer wants to implement their own pooling support, they also have to introduce a brand new task-like type, rather than just being able to use `{Value}Task{<T>}`, because the attribute specifying the builder is only specifiable on the type declaration of the return type.</span></span>

<span data-ttu-id="708dd-130">我们需要一种方法，让单独的异步方法选择特定的生成器。</span><span class="sxs-lookup"><span data-stu-id="708dd-130">We need a way to have an individual async method opt-in to a specific builder.</span></span>

## <a name="detailed-design"></a><span data-ttu-id="708dd-131">详细设计</span><span class="sxs-lookup"><span data-stu-id="708dd-131">Detailed design</span></span>
[design]: #detailed-design

#### <a name="p0-asyncmethodbuilderoverrideattribute-applied-on-async-methods"></a><span data-ttu-id="708dd-132">P0： AsyncMethodBuilderOverrideAttribute 应用于异步方法</span><span class="sxs-lookup"><span data-stu-id="708dd-132">P0: AsyncMethodBuilderOverrideAttribute applied on async methods</span></span>

<span data-ttu-id="708dd-133">在中 `dotnet/runtime` ，添加 `AsyncMethodBuilderOverrideAttribute` ：</span><span class="sxs-lookup"><span data-stu-id="708dd-133">In `dotnet/runtime`, add `AsyncMethodBuilderOverrideAttribute`:</span></span>
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

<span data-ttu-id="708dd-134">在 c # 编译器中，在确定要对类型上定义的生成器使用哪一个生成器时，首选方法上的特性。</span><span class="sxs-lookup"><span data-stu-id="708dd-134">In the C# compiler, prefer the attribute on the method when determining what builder to use over the one defined on the type.</span></span>  <span data-ttu-id="708dd-135">例如，今天如果方法定义为：</span><span class="sxs-lookup"><span data-stu-id="708dd-135">For example, today if a method is defined as:</span></span>
```C#
public async ValueTask<T> ExampleAsync() { ... }
```
<span data-ttu-id="708dd-136">编译器将生成类似于下面的代码：</span><span class="sxs-lookup"><span data-stu-id="708dd-136">the compiler will generate code akin to:</span></span>
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
<span data-ttu-id="708dd-137">如果开发人员编写了此更改，请执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="708dd-137">With this change, if the developer wrote:</span></span>
```C#
[AsyncMethodBuilderOverride(typeof(PoolingAsyncValueTaskMethodBuilder<int>))] // new, referring to some custom builder type
static async ValueTask<int> ExampleAsync() { ... }
```
<span data-ttu-id="708dd-138">相反，它将编译为：</span><span class="sxs-lookup"><span data-stu-id="708dd-138">it would instead be compiled to:</span></span>
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

<span data-ttu-id="708dd-139">只需少量的添加操作即可：</span><span class="sxs-lookup"><span data-stu-id="708dd-139">Just those small additions enable:</span></span>
- <span data-ttu-id="708dd-140">任何人都可以编写自己的生成器，该生成器可应用于返回和的异步方法 `Task<T>``ValueTask<T>`</span><span class="sxs-lookup"><span data-stu-id="708dd-140">Anyone to write their own builder that can be applied to async methods that return `Task<T>` and `ValueTask<T>`</span></span>
- <span data-ttu-id="708dd-141">作为 "任何人"，运行时将实验性生成器支持作为新的公共生成器类型提供，可以选择在方法的基础上进行选择;现有的支持将从现有生成器中删除。</span><span class="sxs-lookup"><span data-stu-id="708dd-141">As "anyone", the runtime to ship the experimental builder support as new public builder types that can be opted into on a method-by-method basis; the existing support would be removed from the existing builders.</span></span>  <span data-ttu-id="708dd-142"> (包括核心库中的一些必要的方法) 随后可以根据具体情况进行特性化，以使用池支持，而不会影响任何其他无归属方法。</span><span class="sxs-lookup"><span data-stu-id="708dd-142">Methods (including some we care about in the core libraries) can then be attributed on a case-by-case basis to use the pooling support, without impacting any other unattributed methods.</span></span>

<span data-ttu-id="708dd-143">在编译器中，使用最小的面区更改或功能工作。</span><span class="sxs-lookup"><span data-stu-id="708dd-143">and with minimal surface area changes or feature work in the compiler.</span></span>


<span data-ttu-id="708dd-144">请注意，我们需要发出的代码，以允许从方法返回不同的类型 `Create` ：</span><span class="sxs-lookup"><span data-stu-id="708dd-144">Note that we need the emitted code to allow a different type being returned from `Create` method:</span></span>
```
AsyncPooledBuilder _builder = AsyncPooledBuilderWithSize4.Create();
```

#### <a name="p1-passing-state-to-the-builder-instantiation"></a><span data-ttu-id="708dd-145">P1：将状态传递到生成器实例化</span><span class="sxs-lookup"><span data-stu-id="708dd-145">P1: Passing state to the builder instantiation</span></span>

<span data-ttu-id="708dd-146">在某些情况下，向生成器传递某些信息会很有用。</span><span class="sxs-lookup"><span data-stu-id="708dd-146">In some scenarios, it would be useful to pass some information to the builder.</span></span>  <span data-ttu-id="708dd-147">这可能是静态 (例如，将池的大小配置为使用) 或动态 (例如，引用缓存或单独使用) 。</span><span class="sxs-lookup"><span data-stu-id="708dd-147">This could be static (e.g. constants configuring the size of the pool to use) or dynamic (e.g. reference to cache or singleton to use).</span></span>

<span data-ttu-id="708dd-148">下面是 brainstormed 记录的几个选项 (记录) 但最终建议不执行任何操作 (选项 #0) ，直到我们确定支持动态状态值得。</span><span class="sxs-lookup"><span data-stu-id="708dd-148">We brainstormed a few options (documented below for the record) but end up recommending doing nothing (option #0) until we determine that supporting dynamic state would be worthwhile.</span></span>

##### <a name="option-0-no-extra-state"></a><span data-ttu-id="708dd-149">选项0：无附加状态</span><span class="sxs-lookup"><span data-stu-id="708dd-149">Option 0: no extra state</span></span>

<span data-ttu-id="708dd-150">可以通过包装生成器类型来近似传递静态信息。</span><span class="sxs-lookup"><span data-stu-id="708dd-150">It is possible to approximate passing static information by wrapping builder types.</span></span>
<span data-ttu-id="708dd-151">例如，可以创建一个 hardcodes 特定配置的自定义生成器类型：</span><span class="sxs-lookup"><span data-stu-id="708dd-151">For instance, one could create a custom builder type that hardcodes a certain configuration:</span></span>

```C#
public struct AsyncPooledBuilderWithSize4
{
    AsyncPooledBuilder Create() => AsyncPooledBuilder.Create(4);
}
```

##### <a name="option-1-use-constants-in-attribute-as-arguments-for-create"></a><span data-ttu-id="708dd-152">选项1：使用属性中的常量作为的参数 `Create`</span><span class="sxs-lookup"><span data-stu-id="708dd-152">Option 1: use constants in attribute as arguments for `Create`</span></span>

<span data-ttu-id="708dd-153">`AsyncMethodBuilderOverrideAttribute`将接受一些其他信息：</span><span class="sxs-lookup"><span data-stu-id="708dd-153">The `AsyncMethodBuilderOverrideAttribute` would have accept some additional information:</span></span>
```C#
    public AsyncMethodBuilderOverrideAttribute(Type builderType, params object[] args)
```

<span data-ttu-id="708dd-154">在特性中收集的参数：</span><span class="sxs-lookup"><span data-stu-id="708dd-154">The arguments collected in the attribute:</span></span>
```C#
[AsyncMethodBuilderOverride(typeof(AsyncPooledBuilder), 4)]
```
<span data-ttu-id="708dd-155">在调用方法时使用 `Create` ：</span><span class="sxs-lookup"><span data-stu-id="708dd-155">would be used in invocation of the `Create` method:</span></span>
```C#
AsyncPooledBuilder.Create(4);
```

##### <a name="option-2-use-arguments-of-the-async-method"></a><span data-ttu-id="708dd-156">选项2：使用 async 方法的参数</span><span class="sxs-lookup"><span data-stu-id="708dd-156">Option 2: use arguments of the async method</span></span>

<span data-ttu-id="708dd-157">除此之外，还可以 `AsyncMethodBuilderOverrideAttribute` 使用属性来标记其中一个异步方法的参数：</span><span class="sxs-lookup"><span data-stu-id="708dd-157">In addition to `AsyncMethodBuilderOverrideAttribute` we would have an attribute to tag one of the async method's parameters:</span></span>
```C#
[AsyncMethodBuilderOverride(typeof(AsyncPooledBuilder))]
async ValueTask MyMethodAsync(/* regular arguments */, [FOR_BUILDER] int i)
```

<span data-ttu-id="708dd-158">这会导致将该参数的值传递给 `Create` 调用：</span><span class="sxs-lookup"><span data-stu-id="708dd-158">This would result in the value for that parameter being passed to the `Create` invocation:</span></span>
```C#
BuilderType.Create(i);
```

<span data-ttu-id="708dd-159">在大多数情况下，用户最终会编写包装来实现所需的签名：</span><span class="sxs-lookup"><span data-stu-id="708dd-159">In most cases, the user would end up writing a wrapper to achieve the desired signature:</span></span>
```C#
public ValueTask MyMethodWrapperAsync(/* regular parameters */)
{
    return MyMethodAsync(/* pass values from regular parameters through */, 4); // static or dynamic value for the builder
}
```

<span data-ttu-id="708dd-160">甚至可以通过这种方式传递缓存的委托：</span><span class="sxs-lookup"><span data-stu-id="708dd-160">One could even pass cached delegates this way:</span></span>
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

<span data-ttu-id="708dd-161">此方法类似于 `EnumeratorCancellationAttribute` 工作方式。</span><span class="sxs-lookup"><span data-stu-id="708dd-161">This approach resembles how `EnumeratorCancellationAttribute` works.</span></span> <span data-ttu-id="708dd-162">但额外的参数对用户代码不起作用，因此我们将污染签名和状态机。</span><span class="sxs-lookup"><span data-stu-id="708dd-162">But the extra parameter isn't useful to user code, so we're polluting the signature and state machine.</span></span>
<span data-ttu-id="708dd-163">此方法与选项 #1 重叠，因此可能不需要同时支持这两种方法。</span><span class="sxs-lookup"><span data-stu-id="708dd-163">This approach overlaps with option #1, so we probably wouldn't want to support both.</span></span>

##### <a name="option-3-pass-a-lambda-that-instantiates-builders"></a><span data-ttu-id="708dd-164">选项3：传递实例化生成器的 lambda</span><span class="sxs-lookup"><span data-stu-id="708dd-164">Option 3: pass a lambda that instantiates builders</span></span>

<span data-ttu-id="708dd-165">作为 (的替代 `AsyncMethodBuilderOverrideAttribute` 方法或可能的补充) ，我们将使用一个特性来标记其中一个异步方法的参数：</span><span class="sxs-lookup"><span data-stu-id="708dd-165">As a replacement for `AsyncMethodBuilderOverrideAttribute` (or possibly in addition to it) we would have an attribute to tag one of the async method's parameters:</span></span>
```C#
async ValueTask MyMethodAsync(/* regular parameters */, [FOR_BUILDER] Func<AsyncPooledBuilder> lambda)
```
<span data-ttu-id="708dd-166">该参数需要具有没有参数的委托类型并返回生成器类型。</span><span class="sxs-lookup"><span data-stu-id="708dd-166">That parameter would need to have a delegate type with no parameters and returning a builder type.</span></span>

<span data-ttu-id="708dd-167">编译器可以生成：</span><span class="sxs-lookup"><span data-stu-id="708dd-167">The compiler could them generate:</span></span>
```C#
...
static ValueTask<int> MyMethodAsync(/* regular parameters */, [FOR_BUILDER] Func<AsyncPooledBuilder> lambda)
{
    <MyMethodAsync>d__29 stateMachine;
    stateMachine.<>t__builder = lambda();
    ...
}
```

<span data-ttu-id="708dd-168">我们仍然遇到了方法签名和状态机污染的问题。</span><span class="sxs-lookup"><span data-stu-id="708dd-168">We still have the problem of polluting the method signature and the state machine.</span></span>

<span data-ttu-id="708dd-169">如果需要包含两个缓存的委托的生成器</span><span class="sxs-lookup"><span data-stu-id="708dd-169">In the case where we want a builder holding two cached delegates</span></span>

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

#### <a name="p2-enable-at-the-module-and-type-level-as-well"></a><span data-ttu-id="708dd-170">P2：在模块 (启用，并同时键入？ ) 级别</span><span class="sxs-lookup"><span data-stu-id="708dd-170">P2: Enable at the module (and type?) level as well</span></span>

<span data-ttu-id="708dd-171">希望对其所有方法使用特定自定义生成器的开发人员可以通过对每个方法进行相关属性来实现此目的。</span><span class="sxs-lookup"><span data-stu-id="708dd-171">A developer that wants to using a specific custom builder for all of their methods can do so by putting the relevant attribute on each method.</span></span>  <span data-ttu-id="708dd-172">但我们也可以在模块或类型级别启用特性化，在这种情况下，该范围内的每个相关方法都将表现为直接批注，例如</span><span class="sxs-lookup"><span data-stu-id="708dd-172">But we could also enable attributing at the module or type level, in which case every relevant method within that scope would behave as if it were directly annotated, e.g.</span></span>
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

<span data-ttu-id="708dd-173">为此，我们将向属性添加以下成员：</span><span class="sxs-lookup"><span data-stu-id="708dd-173">For this we would add the following members to the attribute:</span></span>
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

<span data-ttu-id="708dd-174">这不仅比将属性置于每个方法更方便，还可以更轻松地使用不同生成器的不同版本。</span><span class="sxs-lookup"><span data-stu-id="708dd-174">This would not only make it more convenient than putting the attribute on every method, it would also make it easier to employ different builds that used different builders.</span></span>  <span data-ttu-id="708dd-175">例如，针对吞吐量进行优化的生成可能包括一个 .cs 文件，该文件在模块级特性中指定一个池生成器，而针对大小优化的生成可能包含一个 .cs 文件，该文件指定一个简单生成器，该生成器将使用更多分配/装箱，而不是导致代码膨胀的许多泛型专用化和吞吐量优化。</span><span class="sxs-lookup"><span data-stu-id="708dd-175">For example, a build optimized for throughput might include a .cs file that specifies a pooling builder in a module-level attribute, whereas a build optimized for size might a include a .cs file that specifies a minimalistic builder that opts to use more allocation/boxing instead of lots of generic specialization and throughput optimizations that lead to code bloat.</span></span>


## <a name="drawbacks"></a><span data-ttu-id="708dd-176">缺点</span><span class="sxs-lookup"><span data-stu-id="708dd-176">Drawbacks</span></span>
[drawbacks]: #drawbacks

* <span data-ttu-id="708dd-177">将此类属性应用于方法的语法是详细的。</span><span class="sxs-lookup"><span data-stu-id="708dd-177">The syntax for applying such an attribute to a method is verbose.</span></span>  <span data-ttu-id="708dd-178">如果开发人员可以将其应用于多种方法，例如在类型或模块级别，则会降低这种影响。</span><span class="sxs-lookup"><span data-stu-id="708dd-178">The impact of this is lessened if a developer can apply it to multiple methods en mass, e.g. at the type or module level.</span></span>

## <a name="alternatives"></a><span data-ttu-id="708dd-179">备选方法</span><span class="sxs-lookup"><span data-stu-id="708dd-179">Alternatives</span></span>
[alternatives]: #alternatives

- <span data-ttu-id="708dd-180">实现其他类似任务的类型，并向使用者公开此差异。</span><span class="sxs-lookup"><span data-stu-id="708dd-180">Implement a different task-like type and expose that difference to consumers.</span></span>  <span data-ttu-id="708dd-181">`ValueTask` 可通过接口进行扩展， `IValueTaskSource` 以避免这种需要。</span><span class="sxs-lookup"><span data-stu-id="708dd-181">`ValueTask` was made extensible via the `IValueTaskSource` interface to avoid that need, however.</span></span>
- <span data-ttu-id="708dd-182">通过将实验启用为默认的和仅限的实现来解决该问题的 ValueTask 池。</span><span class="sxs-lookup"><span data-stu-id="708dd-182">Address just the ValueTask pooling part of the issue by enabling the experiment as the on-by-default-and-only implementation.</span></span>  <span data-ttu-id="708dd-183">这并不能解决其他方面，例如配置池，或使其他人能够提供自己的生成器。</span><span class="sxs-lookup"><span data-stu-id="708dd-183">That doesn't address other aspects, such as configuring the pooling, or enabling someone else to provide their own builder.</span></span>

## <a name="unresolved-questions"></a><span data-ttu-id="708dd-184">未解决的问题</span><span class="sxs-lookup"><span data-stu-id="708dd-184">Unresolved questions</span></span>
[unresolved]: #unresolved-questions

1. <span data-ttu-id="708dd-185">**Attribute.**</span><span class="sxs-lookup"><span data-stu-id="708dd-185">**Attribute.**</span></span> <span data-ttu-id="708dd-186">是否应重用 `[AsyncMethodBuilder(typeof(...))]` 或引入其他属性？</span><span class="sxs-lookup"><span data-stu-id="708dd-186">Should we reuse `[AsyncMethodBuilder(typeof(...))]` or introduce yet another attribute?</span></span>
2. <span data-ttu-id="708dd-187">**替换或创建。**</span><span class="sxs-lookup"><span data-stu-id="708dd-187">**Replace or also create.**</span></span> <span data-ttu-id="708dd-188">本方案中的所有示例都是关于替换类似可生成的生成器。</span><span class="sxs-lookup"><span data-stu-id="708dd-188">All of the examples in this proposal are about replacing a buildable task-like's builder.</span></span>  <span data-ttu-id="708dd-189">是否应将该功能的作用域限定为？</span><span class="sxs-lookup"><span data-stu-id="708dd-189">Should the feature be scoped to just that?</span></span> <span data-ttu-id="708dd-190">或者，是否能够对具有不具有生成器的返回类型的方法使用此属性（例如，某些常见接口)  (？</span><span class="sxs-lookup"><span data-stu-id="708dd-190">Or should you be able to use this attribute on a method with a return type that doesn't already have a builder (e.g. some common interface)?</span></span>  <span data-ttu-id="708dd-191">这可能会影响重载解析。</span><span class="sxs-lookup"><span data-stu-id="708dd-191">That could impact overload resolution.</span></span>
3. <span data-ttu-id="708dd-192">**虚方法/接口。**</span><span class="sxs-lookup"><span data-stu-id="708dd-192">**Virtuals / Interfaces.**</span></span> <span data-ttu-id="708dd-193">如果在接口方法上指定了属性，会出现什么情况？</span><span class="sxs-lookup"><span data-stu-id="708dd-193">What is the behavior if the attribute is specified on an interface method?</span></span>  <span data-ttu-id="708dd-194">我认为它应是 nop 或编译器警告/错误，但它不应影响接口的实现。</span><span class="sxs-lookup"><span data-stu-id="708dd-194">I think it should either be a nop or a compiler warning/error, but it shouldn't impact implementations of the interface.</span></span>  <span data-ttu-id="708dd-195">对于重写的基方法，存在一个类似的问题，再次又不认为基方法上的属性会影响替代实现的行为方式。</span><span class="sxs-lookup"><span data-stu-id="708dd-195">A similar question exists for base methods that are overridden, and there again I don't think the attribute on the base method should impact how an override implementation behaves.</span></span> <span data-ttu-id="708dd-196">请注意，当前属性在其 AttributeUsage 上继承了 = false。</span><span class="sxs-lookup"><span data-stu-id="708dd-196">Note the current attribute has Inherited = false on its AttributeUsage.</span></span>
4. <span data-ttu-id="708dd-197">**低于.**</span><span class="sxs-lookup"><span data-stu-id="708dd-197">**Precedence.**</span></span> <span data-ttu-id="708dd-198">如果我们想要执行模块/类型级别的批注，则需要确定在应用了多个归属 (例如，在方法上，一个在包含模块) 上。</span><span class="sxs-lookup"><span data-stu-id="708dd-198">If we wanted to do the module/type-level annotation, we would need to decide on which attribution wins in the case where multiple ones applied (e.g. one on the method, one on the containing module).</span></span>  <span data-ttu-id="708dd-199">我们还需要确定这是否会有必要使用不同的属性 (参阅 (1) 上方) ，例如，如果类似于任务的类型在相同的作用域中，该行为是怎样的？</span><span class="sxs-lookup"><span data-stu-id="708dd-199">We would also need to determine if this would necessitate using a different attribute (see (1) above), e.g. what would the behavior be if a task-like type was in the same scope?</span></span>  <span data-ttu-id="708dd-200">或者，如果类似可生成的任务本身具有 async 方法，它们是否会受到应用于类似于任务的类型的属性的影响，以指定其默认生成器？</span><span class="sxs-lookup"><span data-stu-id="708dd-200">Or if a buildable task-like itself had async methods on it, would they be influenced by the attributed applied to the task-like type to specify its default builder?</span></span>
5. <span data-ttu-id="708dd-201">**专用构建** 者。</span><span class="sxs-lookup"><span data-stu-id="708dd-201">**Private Builders**.</span></span> <span data-ttu-id="708dd-202">编译器是否应支持非公共 async 方法生成器？</span><span class="sxs-lookup"><span data-stu-id="708dd-202">Should the compiler support non-public async method builders?</span></span> <span data-ttu-id="708dd-203">这并不是目前的规范，但我们只支持公共的 experimentally。</span><span class="sxs-lookup"><span data-stu-id="708dd-203">This is not spec'd today, but experimentally we only support public ones.</span></span>  <span data-ttu-id="708dd-204">当将特性应用于类型来控制与该类型一起使用的生成器时，这会非常有意义，因为任何使用该类型编写的异步方法作为返回类型都需要具有访问生成器的权限。</span><span class="sxs-lookup"><span data-stu-id="708dd-204">That makes some sense when the attribute is applied to a type to control what builder is used with that type, since anyone writing an async method with that type as the return type would need access to the builder.</span></span>  <span data-ttu-id="708dd-205">但是，使用这项新功能时，将该特性应用于方法时，它只会影响该方法的实现，因此可合理引用非公共生成器。</span><span class="sxs-lookup"><span data-stu-id="708dd-205">However, with this new feature, when that attribute is applied to a method, it only impacts the implementation of that method, and thus could reasonably reference a non-public builder.</span></span>  <span data-ttu-id="708dd-206">我们可能想要支持具有其要使用的非公共用户的库作者。</span><span class="sxs-lookup"><span data-stu-id="708dd-206">Likely we will want to support library authors who have non-public ones they want to use.</span></span>
6. <span data-ttu-id="708dd-207">**传递状态，以实现更高效的池**。</span><span class="sxs-lookup"><span data-stu-id="708dd-207">**Passthrough state to enable more efficient pooling**.</span></span>  <span data-ttu-id="708dd-208">请考虑一种类型，如 System.net.security.sslstream 或 WebSocket。</span><span class="sxs-lookup"><span data-stu-id="708dd-208">Consider a type like SslStream or WebSocket.</span></span>  <span data-ttu-id="708dd-209">这些操作将公开读/写异步操作，并允许同时进行读写操作，但一次最多可以执行1个读取操作，并且一次最多可以执行1个写入操作。</span><span class="sxs-lookup"><span data-stu-id="708dd-209">These expose read/write async operations, and allow for reading and writing to happen concurrently but at most 1 read operation at a time and at most 1 write operation at a time.</span></span>  <span data-ttu-id="708dd-210">这使得它们适用于池，因为每个 System.net.security.sslstream 或 WebSocket 实例最多需要一个用于读取的池对象，另一个用于写入。</span><span class="sxs-lookup"><span data-stu-id="708dd-210">That makes these ideal for pooling, as each SslStream or WebSocket instance would need at most one pooled object for reads and one pooled object for writes.</span></span>  <span data-ttu-id="708dd-211">而且，集中式池是多余的：无需支付需要进行管理和访问同步的中心池的开销，每个 System.net.security.sslstream/WebSocket 只需维护一个字段来存储读取器的单一实例，并为编写器提供单一实例，从而消除了池的所有争用，并消除了与池限制关联的所有管理。</span><span class="sxs-lookup"><span data-stu-id="708dd-211">Further, a centralized pool is overkill: rather than paying the costs of having a central pool that needs to be managed and access synchronized, every SslStream/WebSocket could just maintain a field to store the singleton for the reader and a singleton for the writer, eliminating all contention for pooling and eliminating all management associated with pool limits.</span></span>  <span data-ttu-id="708dd-212">问题在于，如何将实例上的任意字段连接到生成器所采用的池机制。</span><span class="sxs-lookup"><span data-stu-id="708dd-212">The problem is, how to connect an arbitrary field on the instance with the pooling mechanism employed by the builder.</span></span>  <span data-ttu-id="708dd-213">如果将异步方法的所有参数传递到生成器的 Create 方法上的相应签名 (或可能是单独的 Bind 方法，或者某些这样的操作) ，则至少可以进行此操作，而生成器则需要专门针对该特定类型，了解其字段。</span><span class="sxs-lookup"><span data-stu-id="708dd-213">We could at least make it possible if we passed through all arguments to the async method into a corresponding signature on the builder's Create method (or maybe a separate Bind method, or some such thing), but then the builder would need to be specialized for that specific type, knowing about its fields.</span></span>  <span data-ttu-id="708dd-214">Create 方法可能会采用出租委托和返回委托，异步方法可以专门创建以接受此类参数 (以及要传入) 的对象状态。</span><span class="sxs-lookup"><span data-stu-id="708dd-214">The Create method could potentially take a rent delegate and a return delegate, and the async method could be specially crafted to accept such arguments (along with an object state to be passed in).</span></span>  <span data-ttu-id="708dd-215">在这里提供一个不错的解决方案，因为这会使机制大大增强和有价值。</span><span class="sxs-lookup"><span data-stu-id="708dd-215">It would be great to come up with a good solution here, as it would make the mechanism significantly more powerful and valuable.</span></span>

## <a name="design-meetings"></a><span data-ttu-id="708dd-216">设计会议</span><span class="sxs-lookup"><span data-stu-id="708dd-216">Design meetings</span></span>

<span data-ttu-id="708dd-217">链接到影响此建议的设计说明，并在一个句子中介绍它们所导致的更改。</span><span class="sxs-lookup"><span data-stu-id="708dd-217">Link to design notes that affect this proposal, and describe in one sentence for each what changes they led to.</span></span>

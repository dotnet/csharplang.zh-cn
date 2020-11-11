---
ms.openlocfilehash: 26e9407830d125c4c68efc162943fcc4f960301e
ms.sourcegitcommit: 1fccd6246b9806faee3c8265036fb936ee4a0337
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/11/2020
ms.locfileid: "94507672"
---
# <a name="per-method-asyncmethodbuilders"></a><span data-ttu-id="29a83-101">Per-Method AsyncMethodBuilders</span><span class="sxs-lookup"><span data-stu-id="29a83-101">Per-Method AsyncMethodBuilders</span></span>

* <span data-ttu-id="29a83-102">[x] 建议</span><span class="sxs-lookup"><span data-stu-id="29a83-102">[x] Proposed</span></span>
* <span data-ttu-id="29a83-103">[] 原型：未启动</span><span class="sxs-lookup"><span data-stu-id="29a83-103">[ ] Prototype: Not Started</span></span>
* <span data-ttu-id="29a83-104">[] 实现：未启动</span><span class="sxs-lookup"><span data-stu-id="29a83-104">[ ] Implementation: Not Started</span></span>
* <span data-ttu-id="29a83-105">[] 规范：未启动</span><span class="sxs-lookup"><span data-stu-id="29a83-105">[ ] Specification: Not Started</span></span>

## <a name="summary"></a><span data-ttu-id="29a83-106">摘要</span><span class="sxs-lookup"><span data-stu-id="29a83-106">Summary</span></span>
[summary]: #summary

<span data-ttu-id="29a83-107">扩展现有的异步方法生成器，以支持除现有的按返回类型支持之外的每个方法的特性。</span><span class="sxs-lookup"><span data-stu-id="29a83-107">Extend the existing async method builder to support attribution per-method in addition to the existing per-return type support.</span></span>

## <a name="motivation"></a><span data-ttu-id="29a83-108">动机</span><span class="sxs-lookup"><span data-stu-id="29a83-108">Motivation</span></span>
[motivation]: #motivation

<span data-ttu-id="29a83-109">目前，异步方法生成器绑定到用作异步方法返回类型的给定类型。</span><span class="sxs-lookup"><span data-stu-id="29a83-109">Today, async method builders are tied to a given type used as a return type of an async method.</span></span>  <span data-ttu-id="29a83-110">例如，声明为使用的任何方法 `async Task` `AsyncTaskMethodBuilder` 以及声明为使用的任何方法 `async ValueTask<T>` `AsyncValueTaskMethodBuilder<T>` 。</span><span class="sxs-lookup"><span data-stu-id="29a83-110">For example, any method that's declared as `async Task` uses `AsyncTaskMethodBuilder`, and any method that's declared as `async ValueTask<T>` uses `AsyncValueTaskMethodBuilder<T>`.</span></span>  <span data-ttu-id="29a83-111">这是因为 `[AsyncMethodBuilder(Type)]` 类型上的属性用作返回类型，例如， `ValueTask<T>` 被属性化为 `[AsyncMethodBuilder(typeof(AsyncValueTaskMethodBuilder<>))]` 。</span><span class="sxs-lookup"><span data-stu-id="29a83-111">This is due to the `[AsyncMethodBuilder(Type)]` attribute on the type used as a return type, e.g. `ValueTask<T>` is attributed as `[AsyncMethodBuilder(typeof(AsyncValueTaskMethodBuilder<>))]`.</span></span> <span data-ttu-id="29a83-112">这解决了大多数常见的情况，但对于高级方案，它留下了几个值得注意的漏洞。</span><span class="sxs-lookup"><span data-stu-id="29a83-112">This addresses the majority common case, but it leaves a few notable holes for advanced scenarios.</span></span>

<span data-ttu-id="29a83-113">在 .NET 5 中，提供了一项试验性功能，该功能提供了两种模式 `AsyncValueTaskMethodBuilder` `AsyncValueTaskMethodBuilder<T>` 。</span><span class="sxs-lookup"><span data-stu-id="29a83-113">In .NET 5, an experimental feature was shipped that provides two modes in which `AsyncValueTaskMethodBuilder` and `AsyncValueTaskMethodBuilder<T>` operate.</span></span>  <span data-ttu-id="29a83-114">默认情况下，该模式与在引入功能时相同：需要将状态机提升到堆时，将分配一个对象来存储状态，而异步方法返回 `ValueTask{<T>}` 由提供支持的 `Task{<T>}` 。</span><span class="sxs-lookup"><span data-stu-id="29a83-114">The on-by-default mode is the same as has been there since the functionality was introduced: when the state machine needs to be lifted to the heap, an object is allocated to store the state, and the async method returns a `ValueTask{<T>}` backed by a `Task{<T>}`.</span></span>  <span data-ttu-id="29a83-115">但是，如果设置了环境变量，则进程中的所有生成器都切换到一种模式，在该模式下， `ValueTask{<T>}` 实例由已建立池的可重用实现进行支持 `IValueTaskSource{<T>}` 。</span><span class="sxs-lookup"><span data-stu-id="29a83-115">However, if an environment variable is set, all builders in the process switch to a mode where, instead, the `ValueTask{<T>}` instances are backed by reusable `IValueTaskSource{<T>}` implementations that are pooled.</span></span>  <span data-ttu-id="29a83-116">每个 async 方法都具有其自己的池，该池具有已允许进行缓冲处理的最大实例数，只要该数目不超过该数量就会同时被汇集到池中，就 `async ValueTask<{T}>` 会有效地释放任何 GC 分配开销。</span><span class="sxs-lookup"><span data-stu-id="29a83-116">Each async method has its own pool with a fixed maximum number of instances allowed to be pooled, and as long as no more than that number are ever returned to the pool to be pooled at the same time, `async ValueTask<{T}>` methods effectively become free of any GC allocation overhead.</span></span>

<span data-ttu-id="29a83-117">此实验模式有几个问题，不过，这是为什么) 默认情况下处于关闭状态的原因，而 b) 我们很可能会在将来的版本中将其删除，除非 (会发现非常引人注目的新信息 https://github.com/dotnet/runtime/issues/13633) 。</span><span class="sxs-lookup"><span data-stu-id="29a83-117">There are several problems with this experimental mode, however, which is both why a) it's off by default and b) we're likely to remove it in a future release unless very compelling new information emerges (https://github.com/dotnet/runtime/issues/13633).</span></span>  
- <span data-ttu-id="29a83-118">`ValueTask{<T>}`如果 `ValueTask` 未根据规范使用，则会为返回的使用者引入行为差异。 当它由提供支持时，你可以使用来执行的操作 `Task` `ValueTask` `Task` ，如等待多次，同时等待，等待它完成等。 但当它由任意的支持时， `IValueTaskSource` 此类操作将被禁止，并自动从前者切换到后者可能导致错误。</span><span class="sxs-lookup"><span data-stu-id="29a83-118">It introduces a behavioral difference for consumers of the returned `ValueTask{<T>}` if that `ValueTask` isn't being consumed according to spec.  When it's backed by a `Task`, you can do with the `ValueTask` things you can do with a `Task`, like await it multiple times, await it concurrently, block waiting for it to complete, etc.  But when it's backed by an arbitrary `IValueTaskSource`, such operations are prohibited, and automatically switching from the former to the latter can lead to bugs.</span></span>  <span data-ttu-id="29a83-119">在进程级别切换并影响进程中的所有 `async ValueTask` 方法时，无论是否控制它们，都是太大的 hammer。</span><span class="sxs-lookup"><span data-stu-id="29a83-119">With the switch at the process level and affecting all `async ValueTask` methods in the process, whether you control them or not, it's too big a hammer.</span></span>
- <span data-ttu-id="29a83-120">这并不一定是性能入选，在某些情况下可能会表示回归。</span><span class="sxs-lookup"><span data-stu-id="29a83-120">It's not necessarily a performance win, and could represent a regression in some situations.</span></span>  <span data-ttu-id="29a83-121">这种实现方式是对 (访问池的池的成本进行交易，而不是使用 GC 的成本) ，在各种情况下，GC 就会入选。</span><span class="sxs-lookup"><span data-stu-id="29a83-121">The implementation is trading the cost of pooling (accessing a pool isn't free) with the cost of GC, and in various situations the GC can win.</span></span>  <span data-ttu-id="29a83-122">同样，将池应用到 `async ValueTask` 进程中的所有方法，而不是选择性地将其最有利的方法 hammer。</span><span class="sxs-lookup"><span data-stu-id="29a83-122">Again, applying the pooling to all `async ValueTask` methods in the process rather than being selective about the ones it would most benefit is too big a hammer.</span></span>
- <span data-ttu-id="29a83-123">它将添加到已剪裁应用程序的 IL 大小，即使未设置标志，然后也添加到生成的 asm 大小。</span><span class="sxs-lookup"><span data-stu-id="29a83-123">It adds to the IL size of a trimmed application, even if the flag isn't set, and then to the resulting asm size.</span></span>  <span data-ttu-id="29a83-124">可能会解决实现的改进，使其能够向 it 人员介绍环境变量将始终为 false，但正如目前一样，每个 `async ValueTask` 方法都看到示例 ~ 2k 的二进制占用量会增加 aot 映像，因为此选项，并且同样适用于 `async ValueTask` 整个应用程序闭包中的所有方法。</span><span class="sxs-lookup"><span data-stu-id="29a83-124">It's possible that can be worked around with improvements to the implementation to teach it that for a given deployment the environment variable will always be false, but as it stands today, every `async ValueTask` method saw for example an ~2K binary footprint increase in aot images due to this option, and, again, that applies to all `async ValueTask` methods in the whole application closure.</span></span>
- <span data-ttu-id="29a83-125">不同的方法可能会受益于不同的控制级别，例如，由于了解方法和使用方式而使用的池大小，但相同的设置会应用于生成器的所有使用。</span><span class="sxs-lookup"><span data-stu-id="29a83-125">Different methods may benefit from differing levels of control, e.g. the size of the pool employed because of knowledge of the method and how it's used, but the same setting is applied to all uses of the builder.</span></span>  <span data-ttu-id="29a83-126">通过让生成器代码在运行时使用反射来查找某些属性（但这会增加重要的运行时支出，并且可能会在启动路径上），可以想象出这样的工作。</span><span class="sxs-lookup"><span data-stu-id="29a83-126">One could imagine working around that by having the builder code use reflection at runtime to look for some attribute, but that adds significant run-time expense, and likely on the startup path.</span></span>

<span data-ttu-id="29a83-127">在现有池的所有这些问题的基础上，也会阻止开发人员为他们不拥有的类型编写自己的自定义生成器。</span><span class="sxs-lookup"><span data-stu-id="29a83-127">On top of all of these issues with the existing pooling, it's also the case that developers are prevented from writing their own customized builders for types they don't own.</span></span>  <span data-ttu-id="29a83-128">例如，如果开发人员想要实现其自己的池支持，则他们还必须引入全新的类似任务的类型，而不是仅能使用 `{Value}Task{<T>}` ，因为指定生成器的属性仅可指定返回类型的类型声明。</span><span class="sxs-lookup"><span data-stu-id="29a83-128">If, for example, a developer wants to implement their own pooling support, they also have to introduce a brand new task-like type, rather than just being able to use `{Value}Task{<T>}`, because the attribute specifying the builder is only specifiable on the type declaration of the return type.</span></span>

<span data-ttu-id="29a83-129">我们需要一种方法，让单独的异步方法选择特定的生成器。</span><span class="sxs-lookup"><span data-stu-id="29a83-129">We need a way to have an individual async method opt-in to a specific builder.</span></span>

## <a name="detailed-design"></a><span data-ttu-id="29a83-130">详细设计</span><span class="sxs-lookup"><span data-stu-id="29a83-130">Detailed design</span></span>
[design]: #detailed-design

#### <a name="p0-asyncmethodbuilderattribute-applicable-to-methods"></a><span data-ttu-id="29a83-131">P0：适用于方法的 AsyncMethodBuilderAttribute</span><span class="sxs-lookup"><span data-stu-id="29a83-131">P0: AsyncMethodBuilderAttribute applicable to methods</span></span>

<span data-ttu-id="29a83-132">在 dotnet/运行时中，更改 `AsyncMethodBuilderAttribute` 的 AttributeUsage 来自：</span><span class="sxs-lookup"><span data-stu-id="29a83-132">In dotnet/runtime, change `AsyncMethodBuilderAttribute`'s AttributeUsage from:</span></span>
```C#
AttributeTargets.Class | AttributeTargets.Struct | AttributeTargets.Interface | AttributeTargets.Delegate | AttributeTargets.Enum
```
<span data-ttu-id="29a83-133">还包括方法：</span><span class="sxs-lookup"><span data-stu-id="29a83-133">to also include Method:</span></span>
```C#
AttributeTargets.Class | AttributeTargets.Struct | AttributeTargets.Interface | AttributeTargets.Delegate | AttributeTargets.Enum | AttributeTargets.Method
```
<span data-ttu-id="29a83-134">也可以将其应用于方法。</span><span class="sxs-lookup"><span data-stu-id="29a83-134">so that it may be applied to methods as well.</span></span>  <span data-ttu-id="29a83-135"> (或者，引入特定于方法的新属性（如果由于某种原因认为该属性更好）。 ) </span><span class="sxs-lookup"><span data-stu-id="29a83-135">(Alternatively, introduce a new attribute specific to methods, if that's deemed better for some reason.)</span></span>

<span data-ttu-id="29a83-136">在 c # 编译器中，在确定要对类型上定义的生成器使用哪一个生成器时，首选方法上的特性。</span><span class="sxs-lookup"><span data-stu-id="29a83-136">In the C# compiler, prefer the attribute on the method when determining what builder to use over the one defined on the type.</span></span>  <span data-ttu-id="29a83-137">例如，今天如果方法定义为：</span><span class="sxs-lookup"><span data-stu-id="29a83-137">For example, today if a method is defined as:</span></span>
```C#
public async ValueTask<T> ExampleAsync() { ... }
```
<span data-ttu-id="29a83-138">编译器将生成类似于下面的代码：</span><span class="sxs-lookup"><span data-stu-id="29a83-138">the compiler will generate code akin to:</span></span>
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
<span data-ttu-id="29a83-139">如果开发人员编写了此更改，请执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="29a83-139">With this change, if the developer wrote:</span></span>
```C#
[AsyncMethodBuilder(typeof(PoolingAsyncValueTaskMethodBuilder<int>))] // new, referring to some custom builder type
static async ValueTask<int> ExampleAsync() { ... }
```
<span data-ttu-id="29a83-140">相反，它将编译为：</span><span class="sxs-lookup"><span data-stu-id="29a83-140">it would instead be compiled to:</span></span>
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

<span data-ttu-id="29a83-141">只需少量的添加操作即可：</span><span class="sxs-lookup"><span data-stu-id="29a83-141">Just those small additions enable:</span></span>
- <span data-ttu-id="29a83-142">任何人都可以编写自己的生成器，该生成器可应用于返回和的异步方法 `Task{T}``ValueTask{<T>}`</span><span class="sxs-lookup"><span data-stu-id="29a83-142">Anyone to write their own builder that can be applied to async methods that return `Task{T}` and `ValueTask{<T>}`</span></span>
- <span data-ttu-id="29a83-143">作为 "任何人"，运行时将实验性生成器支持作为新的公共生成器类型提供，可以选择在方法的基础上进行选择;现有的支持将从现有生成器中删除。</span><span class="sxs-lookup"><span data-stu-id="29a83-143">As "anyone", the runtime to ship the experimental builder support as new public builder types that can be opted into on a method-by-method basis; the existing support would be removed from the existing builders.</span></span>  <span data-ttu-id="29a83-144"> (包括核心库中的一些必要的方法) 随后可以根据具体情况进行特性化，以使用池支持，而不会影响任何其他无归属方法。</span><span class="sxs-lookup"><span data-stu-id="29a83-144">Methods (including some we care about in the core libraries) can then be attributed on a case-by-case basis to use the pooling support, without impacting any other unattributed methods.</span></span>

<span data-ttu-id="29a83-145">在编译器中，使用最小的面区更改或功能工作。</span><span class="sxs-lookup"><span data-stu-id="29a83-145">and with minimal surface area changes or feature work in the compiler.</span></span>

#### <a name="p1-asyncmethodbuilderattribute-arguments-forward-to-create"></a><span data-ttu-id="29a83-146">P1： AsyncMethodBuilderAttribute 参数转发给 Create</span><span class="sxs-lookup"><span data-stu-id="29a83-146">P1: AsyncMethodBuilderAttribute arguments forward to Create</span></span>

<span data-ttu-id="29a83-147">将为属性提供附加构造函数：</span><span class="sxs-lookup"><span data-stu-id="29a83-147">The attribute would be given an additional constructor:</span></span>
```C#
public AsyncMethodBuilderAttribute(Type builderType, params object[] createArguments);
```
<span data-ttu-id="29a83-148">如果指定了任何此类自变量，则编译器会预计生成器具有一个 `Create` 可以与这些参数绑定的方法，例如，如果开发人员使用：</span><span class="sxs-lookup"><span data-stu-id="29a83-148">If any such arguments are specified, the compiler would expect the builder to have a `Create` method that could bind with those arguments, e.g. if a developer used:</span></span>
```C#
[AsyncMethodBuilder(typeof(PoolingAsyncValueTaskMethodBuilder<>), 16)]
```
<span data-ttu-id="29a83-149">由于公开以下重载，编译器将允许编译 `PoolingAsyncValueTaskMethodBuilder<>` `Create` ：</span><span class="sxs-lookup"><span data-stu-id="29a83-149">the compiler would allow compilation due to `PoolingAsyncValueTaskMethodBuilder<>` exposing the following `Create` overload:</span></span>
```
public static PoolingAsyncValueTaskMethodBuilder<T> Create(int poolCapacity);
```
<span data-ttu-id="29a83-150">和将使用该 `Create` 重载，而不是 `Create` 它会预期和使用的无参数，例如：</span><span class="sxs-lookup"><span data-stu-id="29a83-150">and would use that `Create` overload instead of a parameterless `Create` that it would otherwise expect and use, e.g. this:</span></span>
```C#
[AsyncMethodBuilder(typeof(PoolingAsyncValueTaskMethodBuilder<>), 16)]
static async ValueTask<int> ExampleAsync() { ... }
```
<span data-ttu-id="29a83-151">将编译为：</span><span class="sxs-lookup"><span data-stu-id="29a83-151">would be compiled to:</span></span>
```C#
[AsyncStateMachine(typeof(<ExampleAsync>d__29))]
[CompilerGenerated]
[AsyncMethodBuilder(typeof(PoolingAsyncValueTaskMethodBuilder<>), 16)]
static ValueTask<int> ExampleAsync()
{
    <ExampleAsync>d__29 stateMachine;
    stateMachine.<>t__builder = PoolingAsyncValueTaskMethodBuilder<int>.Create(16); // attr arguments passed to Create
    stateMachine.<>1__state = -1;
    stateMachine.<>t__builder.Start(ref stateMachine);
    return stateMachine.<>t__builder.Task;
}
```
<span data-ttu-id="29a83-152">此类支持允许每个调用站点参数化自定义生成器，而无需生成器来执行复杂而昂贵的反射。</span><span class="sxs-lookup"><span data-stu-id="29a83-152">Such support would enable custom builders to be parameterized per call site, without requiring the builder to perform complicated and expensive reflection.</span></span>

#### <a name="p2-enable-at-the-module-and-type-level-as-well"></a><span data-ttu-id="29a83-153">P2：在模块 (启用，并同时键入？ ) 级别</span><span class="sxs-lookup"><span data-stu-id="29a83-153">P2: Enable at the module (and type?) level as well</span></span>

<span data-ttu-id="29a83-154">希望对其所有方法使用特定自定义生成器的开发人员可以通过对每个方法进行相关属性来实现此目的。</span><span class="sxs-lookup"><span data-stu-id="29a83-154">A developer that wants to using a specific custom builder for all of their methods can do so by putting the relevant attribute on each method.</span></span>  <span data-ttu-id="29a83-155">但我们也可以在模块或类型级别启用特性化，在这种情况下，该范围内的每个相关方法都将表现为直接批注，例如</span><span class="sxs-lookup"><span data-stu-id="29a83-155">But we could also enable attributing at the module or type level, in which case every relevant method within that scope would behave as if it were directly annotated, e.g.</span></span>
```C#
[module: PoolingAsyncValueTaskMethodBuilder]
[module: PoolingAsyncValueTaskMethodBuilder<>]

class MyClass
{
    public async ValueTask Method1Async() { ... } // would use PoolingAsyncValueTaskMethodBuilder
    public async ValueTask<int> Method2Async() { ... } // would use PoolingAsyncValueTaskMethodBuilder<int>
    public async ValueTask<string> Method3Async() { ... } // would use PoolingAsyncValueTaskMethodBuilder<string>
}
```

<span data-ttu-id="29a83-156">这不仅比将属性置于每个方法更方便，还可以更轻松地使用不同生成器的不同版本。</span><span class="sxs-lookup"><span data-stu-id="29a83-156">This would not only make it more convenient than putting the attribute on every method, it would also make it easier to employ different builds that used different builders.</span></span>  <span data-ttu-id="29a83-157">例如，针对吞吐量进行优化的生成可能包括一个 .cs 文件，该文件在模块级特性中指定一个池生成器，而针对大小优化的生成可能包含一个 .cs 文件，该文件指定一个简单生成器，该生成器将使用更多分配/装箱，而不是导致代码膨胀的许多泛型专用化和吞吐量优化。</span><span class="sxs-lookup"><span data-stu-id="29a83-157">For example, a build optimized for throughput might include a .cs file that specifies a pooling builder in a module-level attribute, whereas a build optimized for size might a include a .cs file that specifies a minimalistic builder that opts to use more allocation/boxing instead of lots of generic specialization and throughput optimizations that lead to code bloat.</span></span>


## <a name="drawbacks"></a><span data-ttu-id="29a83-158">缺点</span><span class="sxs-lookup"><span data-stu-id="29a83-158">Drawbacks</span></span>
[drawbacks]: #drawbacks

* <span data-ttu-id="29a83-159">将此类属性应用于方法的语法是详细的。</span><span class="sxs-lookup"><span data-stu-id="29a83-159">The syntax for applying such an attribute to a method is verbose.</span></span>  <span data-ttu-id="29a83-160">如果开发人员可以将其应用于多种方法，例如在类型或模块级别，则会降低这种影响。</span><span class="sxs-lookup"><span data-stu-id="29a83-160">The impact of this is lessened if a developer can apply it to multiple methods en mass, e.g. at the type or module level.</span></span>

## <a name="alternatives"></a><span data-ttu-id="29a83-161">备选项</span><span class="sxs-lookup"><span data-stu-id="29a83-161">Alternatives</span></span>
[alternatives]: #alternatives

- <span data-ttu-id="29a83-162">实现其他类似任务的类型，并向使用者公开此差异。</span><span class="sxs-lookup"><span data-stu-id="29a83-162">Implement a different task-like type and expose that difference to consumers.</span></span>  <span data-ttu-id="29a83-163">`ValueTask` 可通过接口进行扩展， `IValueTaskSource` 以避免这种需要。</span><span class="sxs-lookup"><span data-stu-id="29a83-163">`ValueTask` was made extensible via the `IValueTaskSource` interface to avoid that need, however.</span></span>
- <span data-ttu-id="29a83-164">通过将实验启用为默认的和仅限的实现来解决该问题的 ValueTask 池。</span><span class="sxs-lookup"><span data-stu-id="29a83-164">Address just the ValueTask pooling part of the issue by enabling the experiment as the on-by-default-and-only implementation.</span></span>  <span data-ttu-id="29a83-165">这并不能解决其他方面，例如配置池，或使其他人能够提供自己的生成器。</span><span class="sxs-lookup"><span data-stu-id="29a83-165">That doesn't address other aspects, such as configuring the pooling, or enabling someone else to provide their own builder.</span></span>

## <a name="unresolved-questions"></a><span data-ttu-id="29a83-166">未解决的问题</span><span class="sxs-lookup"><span data-stu-id="29a83-166">Unresolved questions</span></span>
[unresolved]: #unresolved-questions

1. <span data-ttu-id="29a83-167">**Attribute.**</span><span class="sxs-lookup"><span data-stu-id="29a83-167">**Attribute.**</span></span> <span data-ttu-id="29a83-168">是否应重用 `[AsyncMethodBuilder(typeof(...))]` 或引入其他属性？</span><span class="sxs-lookup"><span data-stu-id="29a83-168">Should we reuse `[AsyncMethodBuilder(typeof(...))]` or introduce yet another attribute?</span></span>
2. <span data-ttu-id="29a83-169">**替换或创建。**</span><span class="sxs-lookup"><span data-stu-id="29a83-169">**Replace or also create.**</span></span> <span data-ttu-id="29a83-170">本方案中的所有示例都是关于替换类似可生成的生成器。</span><span class="sxs-lookup"><span data-stu-id="29a83-170">All of the examples in this proposal are about replacing a buildable task-like's builder.</span></span>  <span data-ttu-id="29a83-171">是否应将该功能的作用域限定为？</span><span class="sxs-lookup"><span data-stu-id="29a83-171">Should the feature be scoped to just that?</span></span> <span data-ttu-id="29a83-172">或者，是否能够对具有不具有生成器的返回类型的方法使用此属性（例如，某些常见接口)  (？</span><span class="sxs-lookup"><span data-stu-id="29a83-172">Or should you be able to use this attribute on a method with a return type that doesn't already have a builder (e.g. some common interface)?</span></span>  <span data-ttu-id="29a83-173">这可能会影响重载解析。</span><span class="sxs-lookup"><span data-stu-id="29a83-173">That could impact overload resolution.</span></span>
3. <span data-ttu-id="29a83-174">**虚方法/接口。**</span><span class="sxs-lookup"><span data-stu-id="29a83-174">**Virtuals / Interfaces.**</span></span> <span data-ttu-id="29a83-175">如果在接口方法上指定了属性，会出现什么情况？</span><span class="sxs-lookup"><span data-stu-id="29a83-175">What is the behavior if the attribute is specified on an interface method?</span></span>  <span data-ttu-id="29a83-176">我认为它应是 nop 或编译器警告/错误，但它不应影响接口的实现。</span><span class="sxs-lookup"><span data-stu-id="29a83-176">I think it should either be a nop or a compiler warning/error, but it shouldn't impact implementations of the interface.</span></span>  <span data-ttu-id="29a83-177">对于重写的基方法，存在一个类似的问题，再次又不认为基方法上的属性会影响替代实现的行为方式。</span><span class="sxs-lookup"><span data-stu-id="29a83-177">A similar question exists for base methods that are overridden, and there again I don't think the attribute on the base method should impact how an override implementation behaves.</span></span> <span data-ttu-id="29a83-178">请注意，当前属性在其 AttributeUsage 上继承了 = false。</span><span class="sxs-lookup"><span data-stu-id="29a83-178">Note the current attribute has Inherited = false on its AttributeUsage.</span></span>
4. <span data-ttu-id="29a83-179">**低于.**</span><span class="sxs-lookup"><span data-stu-id="29a83-179">**Precedence.**</span></span> <span data-ttu-id="29a83-180">如果我们想要执行模块/类型级别的批注，则需要确定在应用了多个归属 (例如，在方法上，一个在包含模块) 上。</span><span class="sxs-lookup"><span data-stu-id="29a83-180">If we wanted to do the module/type-level annotation, we would need to decide on which attribution wins in the case where multiple ones applied (e.g. one on the method, one on the containing module).</span></span>  <span data-ttu-id="29a83-181">我们还需要确定这是否会有必要使用不同的属性 (参阅 (1) 上方) ，例如，如果类似于任务的类型在相同的作用域中，该行为是怎样的？</span><span class="sxs-lookup"><span data-stu-id="29a83-181">We would also need to determine if this would necessitate using a different attribute (see (1) above), e.g. what would the behavior be if a task-like type was in the same scope?</span></span>  <span data-ttu-id="29a83-182">或者，如果类似可生成的任务本身具有 async 方法，它们是否会受到应用于类似于任务的类型的属性的影响，以指定其默认生成器？</span><span class="sxs-lookup"><span data-stu-id="29a83-182">Or if a buildable task-like itself had async methods on it, would they be influenced by the attributed applied to the task-like type to specify its default builder?</span></span>
5. <span data-ttu-id="29a83-183">**专用构建** 者。</span><span class="sxs-lookup"><span data-stu-id="29a83-183">**Private Builders**.</span></span> <span data-ttu-id="29a83-184">编译器是否应支持非公共 async 方法生成器？</span><span class="sxs-lookup"><span data-stu-id="29a83-184">Should the compiler support non-public async method builders?</span></span> <span data-ttu-id="29a83-185">这并不是目前的规范，但我们只支持公共的 experimentally。</span><span class="sxs-lookup"><span data-stu-id="29a83-185">This is not spec'd today, but experimentally we only support public ones.</span></span>  <span data-ttu-id="29a83-186">当将特性应用于类型来控制与该类型一起使用的生成器时，这会非常有意义，因为任何使用该类型编写的异步方法作为返回类型都需要具有访问生成器的权限。</span><span class="sxs-lookup"><span data-stu-id="29a83-186">That makes some sense when the attribute is applied to a type to control what builder is used with that type, since anyone writing an async method with that type as the return type would need access to the builder.</span></span>  <span data-ttu-id="29a83-187">但是，使用这项新功能时，将该特性应用于方法时，它只会影响该方法的实现，因此可合理引用非公共生成器。</span><span class="sxs-lookup"><span data-stu-id="29a83-187">However, with this new feature, when that attribute is applied to a method, it only impacts the implementation of that method, and thus could reasonably reference a non-public builder.</span></span>  <span data-ttu-id="29a83-188">我们可能想要支持具有其要使用的非公共用户的库作者。</span><span class="sxs-lookup"><span data-stu-id="29a83-188">Likely we will want to support library authors who have non-public ones they want to use.</span></span>
6. <span data-ttu-id="29a83-189">**传递状态，以实现更高效的池** 。</span><span class="sxs-lookup"><span data-stu-id="29a83-189">**Passthrough state to enable more efficient pooling**.</span></span>  <span data-ttu-id="29a83-190">请考虑一种类型，如 System.net.security.sslstream 或 WebSocket。</span><span class="sxs-lookup"><span data-stu-id="29a83-190">Consider a type like SslStream or WebSocket.</span></span>  <span data-ttu-id="29a83-191">这些操作将公开读/写异步操作，并允许同时进行读写操作，但一次最多可以执行1个读取操作，并且一次最多可以执行1个写入操作。</span><span class="sxs-lookup"><span data-stu-id="29a83-191">These expose read/write async operations, and allow for reading and writing to happen concurrently but at most 1 read operation at a time and at most 1 write operation at a time.</span></span>  <span data-ttu-id="29a83-192">这使得它们适用于池，因为每个 System.net.security.sslstream 或 WebSocket 实例最多需要一个用于读取的池对象，另一个用于写入。</span><span class="sxs-lookup"><span data-stu-id="29a83-192">That makes these ideal for pooling, as each SslStream or WebSocket instance would need at most one pooled object for reads and one pooled object for writes.</span></span>  <span data-ttu-id="29a83-193">而且，集中式池是多余的：无需支付需要进行管理和访问同步的中心池的开销，每个 System.net.security.sslstream/WebSocket 只需维护一个字段来存储读取器的单一实例，并为编写器提供单一实例，从而消除了池的所有争用，并消除了与池限制关联的所有管理。</span><span class="sxs-lookup"><span data-stu-id="29a83-193">Further, a centralized pool is overkill: rather than paying the costs of having a central pool that needs to be managed and access synchronized, every SslStream/WebSocket could just maintain a field to store the singleton for the reader and a singleton for the writer, eliminating all contention for pooling and eliminating all management associated with pool limits.</span></span>  <span data-ttu-id="29a83-194">问题在于，如何将实例上的任意字段连接到生成器所采用的池机制。</span><span class="sxs-lookup"><span data-stu-id="29a83-194">The problem is, how to connect an arbitrary field on the instance with the pooling mechanism employed by the builder.</span></span>  <span data-ttu-id="29a83-195">如果将异步方法的所有参数传递到生成器的 Create 方法上的相应签名 (或可能是单独的 Bind 方法，或者某些这样的操作) ，则至少可以进行此操作，而生成器则需要专门针对该特定类型，了解其字段。</span><span class="sxs-lookup"><span data-stu-id="29a83-195">We could at least make it possible if we passed through all arguments to the async method into a corresponding signature on the builder's Create method (or maybe a separate Bind method, or some such thing), but then the builder would need to be specialized for that specific type, knowing about its fields.</span></span>  <span data-ttu-id="29a83-196">Create 方法可能会采用出租委托和返回委托，异步方法可以专门创建以接受此类参数 (以及要传入) 的对象状态。</span><span class="sxs-lookup"><span data-stu-id="29a83-196">The Create method could potentially take a rent delegate and a return delegate, and the async method could be specially crafted to accept such arguments (along with an object state to be passed in).</span></span>  <span data-ttu-id="29a83-197">在这里提供一个不错的解决方案，因为这会使机制大大增强和有价值。</span><span class="sxs-lookup"><span data-stu-id="29a83-197">It would be great to come up with a good solution here, as it would make the mechanism significantly more powerful and valuable.</span></span>

## <a name="design-meetings"></a><span data-ttu-id="29a83-198">设计会议</span><span class="sxs-lookup"><span data-stu-id="29a83-198">Design meetings</span></span>

<span data-ttu-id="29a83-199">链接到影响此建议的设计说明，并在一个句子中介绍它们所导致的更改。</span><span class="sxs-lookup"><span data-stu-id="29a83-199">Link to design notes that affect this proposal, and describe in one sentence for each what changes they led to.</span></span>

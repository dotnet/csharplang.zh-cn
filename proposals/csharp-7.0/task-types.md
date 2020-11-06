---
ms.openlocfilehash: 802722e4331a10228eb77676d28338ace5ef07b2
ms.sourcegitcommit: 4c8b0a1c815f6ed5f69e2bdff94da354b2908fed
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/05/2020
ms.locfileid: "93406298"
---
<a name="async-task-types-in-c"></a><span data-ttu-id="5b531-101">C 中的异步任务类型#</span><span class="sxs-lookup"><span data-stu-id="5b531-101">Async Task Types in C#</span></span>
======================
<span data-ttu-id="5b531-102">扩展 `async` 以支持与特定模式匹配的 _任务类型_ ，以及已知类型 `System.Threading.Tasks.Task` 和 `System.Threading.Tasks.Task<T>` 。</span><span class="sxs-lookup"><span data-stu-id="5b531-102">Extend `async` to support _task types_ that match a specific pattern, in addition to the well known types `System.Threading.Tasks.Task` and `System.Threading.Tasks.Task<T>`.</span></span>

## <a name="task-type"></a><span data-ttu-id="5b531-103">任务类型</span><span class="sxs-lookup"><span data-stu-id="5b531-103">Task Type</span></span>
<span data-ttu-id="5b531-104">_任务类型_ 为 `class` 或 `struct` ，其关联的 _生成器类型_ 使用标识 `System.Runtime.CompilerServices.AsyncMethodBuilderAttribute` 。</span><span class="sxs-lookup"><span data-stu-id="5b531-104">A _task type_ is a `class` or `struct` with an associated _builder type_ identified with `System.Runtime.CompilerServices.AsyncMethodBuilderAttribute`.</span></span>
<span data-ttu-id="5b531-105">对于返回值的方法， _任务类型_ 可以为非泛型，对于不返回值的异步方法或泛型。</span><span class="sxs-lookup"><span data-stu-id="5b531-105">The _task type_ may be non-generic, for async methods that do not return a value, or generic, for methods that return a value.</span></span>

<span data-ttu-id="5b531-106">若要支持 `await` ， _任务类型_ 必须具有相应的可访问 `GetAwaiter()` 方法，该方法返回 _awaiter 类型_ 的实例 (参阅 _c # 7.7.7.1 可等待表达式_ ) 。</span><span class="sxs-lookup"><span data-stu-id="5b531-106">To support `await`, the _task type_ must have a corresponding, accessible `GetAwaiter()` method that returns an instance of an _awaiter type_ (see _C# 7.7.7.1 Awaitable expressions_ ).</span></span>
```cs
[AsyncMethodBuilder(typeof(MyTaskMethodBuilder<>))]
class MyTask<T>
{
    public Awaiter<T> GetAwaiter();
}

class Awaiter<T> : INotifyCompletion
{
    public bool IsCompleted { get; }
    public T GetResult();
    public void OnCompleted(Action completion);
}
```
## <a name="builder-type"></a><span data-ttu-id="5b531-107">生成器类型</span><span class="sxs-lookup"><span data-stu-id="5b531-107">Builder Type</span></span>
<span data-ttu-id="5b531-108">_生成器类型_ 为 `class` 或 `struct` ，它对应于特定的 _任务类型_ 。</span><span class="sxs-lookup"><span data-stu-id="5b531-108">The _builder type_ is a `class` or `struct` that corresponds to the specific _task type_.</span></span>
<span data-ttu-id="5b531-109">_生成器类型_ 最多可以有1个类型参数，并且不得嵌套在泛型类型中。</span><span class="sxs-lookup"><span data-stu-id="5b531-109">The _builder type_ can have at most 1 type parameter and must not be nested in a generic type.</span></span>
<span data-ttu-id="5b531-110">_生成器类型_ 具有以下 `public` 方法。</span><span class="sxs-lookup"><span data-stu-id="5b531-110">The _builder type_ has the following `public` methods.</span></span>
<span data-ttu-id="5b531-111">对于非泛型 _生成器类型_ ，没有 `SetResult()` 参数。</span><span class="sxs-lookup"><span data-stu-id="5b531-111">For non-generic _builder types_ , `SetResult()` has no parameters.</span></span>
```cs
class MyTaskMethodBuilder<T>
{
    public static MyTaskMethodBuilder<T> Create();

    public void Start<TStateMachine>(ref TStateMachine stateMachine)
        where TStateMachine : IAsyncStateMachine;

    public void SetStateMachine(IAsyncStateMachine stateMachine);
    public void SetException(Exception exception);
    public void SetResult(T result);

    public void AwaitOnCompleted<TAwaiter, TStateMachine>(
        ref TAwaiter awaiter, ref TStateMachine stateMachine)
        where TAwaiter : INotifyCompletion
        where TStateMachine : IAsyncStateMachine;
    public void AwaitUnsafeOnCompleted<TAwaiter, TStateMachine>(
        ref TAwaiter awaiter, ref TStateMachine stateMachine)
        where TAwaiter : ICriticalNotifyCompletion
        where TStateMachine : IAsyncStateMachine;

    public MyTask<T> Task { get; }
}
```
## <a name="execution"></a><span data-ttu-id="5b531-112">执行</span><span class="sxs-lookup"><span data-stu-id="5b531-112">Execution</span></span>
<span data-ttu-id="5b531-113">编译器使用以上类型为方法的状态机生成代码 `async` 。</span><span class="sxs-lookup"><span data-stu-id="5b531-113">The types above are used by the compiler to generate the code for the state machine of an `async` method.</span></span>
<span data-ttu-id="5b531-114"> (生成的代码等效于为返回、或的异步方法生成的 `Task` 代码 `Task<T>` `void` 。</span><span class="sxs-lookup"><span data-stu-id="5b531-114">(The generated code is equivalent to the code generated for async methods that return `Task`, `Task<T>`, or `void`.</span></span>
<span data-ttu-id="5b531-115">不同之处在于，对于这些已知类型， _生成器类型_ 对编译器也是已知的。 ) </span><span class="sxs-lookup"><span data-stu-id="5b531-115">The difference is, for those well known types, the _builder types_ are also known to the compiler.)</span></span>

<span data-ttu-id="5b531-116">`Builder.Create()` 调用以创建 _生成器类型_ 的实例。</span><span class="sxs-lookup"><span data-stu-id="5b531-116">`Builder.Create()` is invoked to create an instance of the _builder type_.</span></span>

<span data-ttu-id="5b531-117">如果状态机实现为 `struct` ，则 `builder.SetStateMachine(stateMachine)` 将使用状态机的已装箱实例调用，如有必要，生成器可以缓存该实例。</span><span class="sxs-lookup"><span data-stu-id="5b531-117">If the state machine is implemented as a `struct`, then `builder.SetStateMachine(stateMachine)` is called with a boxed instance of the state machine that the builder can cache if necessary.</span></span>

<span data-ttu-id="5b531-118">`builder.Start(ref stateMachine)` 调用将生成器与编译器生成的状态机实例相关联。</span><span class="sxs-lookup"><span data-stu-id="5b531-118">`builder.Start(ref stateMachine)` is invoked to associate the builder with compiler-generated state machine instance.</span></span>
<span data-ttu-id="5b531-119">生成器必须 `stateMachine.MoveNext()` 在 `Start()` `Start()` 返回以提升状态机之前或之后调用。</span><span class="sxs-lookup"><span data-stu-id="5b531-119">The builder must call `stateMachine.MoveNext()` either in `Start()` or after `Start()` has returned to advance the state machine.</span></span>
<span data-ttu-id="5b531-120">`Start()`返回后，方法将调用以使 `async` `builder.Task` 任务从异步方法返回。</span><span class="sxs-lookup"><span data-stu-id="5b531-120">After `Start()` returns, the `async` method calls `builder.Task` for the task to return from the async method.</span></span>

<span data-ttu-id="5b531-121">每次调用 `stateMachine.MoveNext()` 都会提升状态机。</span><span class="sxs-lookup"><span data-stu-id="5b531-121">Each call to `stateMachine.MoveNext()` will advance the state machine.</span></span>
<span data-ttu-id="5b531-122">如果状态机成功完成， `builder.SetResult()` 则调用，方法返回值（如果有）。</span><span class="sxs-lookup"><span data-stu-id="5b531-122">If the state machine completes successfully, `builder.SetResult()` is called, with  the method return value if any.</span></span>
<span data-ttu-id="5b531-123">如果在状态机中引发了异常， `builder.SetException(exception)` 则会调用。</span><span class="sxs-lookup"><span data-stu-id="5b531-123">If an exception is thrown in the state machine, `builder.SetException(exception)` is called.</span></span>

<span data-ttu-id="5b531-124">如果状态机到达 `await expr` 表达式， `expr.GetAwaiter()` 则调用。</span><span class="sxs-lookup"><span data-stu-id="5b531-124">If the state machine reaches an `await expr` expression, `expr.GetAwaiter()` is invoked.</span></span>
<span data-ttu-id="5b531-125">如果 awaiter 实现 `ICriticalNotifyCompletion` 并且 `IsCompleted` 为 false，则状态机调用 `builder.AwaitUnsafeOnCompleted(ref awaiter, ref stateMachine)` 。</span><span class="sxs-lookup"><span data-stu-id="5b531-125">If the awaiter implements `ICriticalNotifyCompletion` and `IsCompleted` is false, the state machine invokes `builder.AwaitUnsafeOnCompleted(ref awaiter, ref stateMachine)`.</span></span>
<span data-ttu-id="5b531-126">`AwaitUnsafeOnCompleted()` 应调用 `awaiter.OnCompleted(action)` `stateMachine.MoveNext()` awaiter 完成时调用的操作。</span><span class="sxs-lookup"><span data-stu-id="5b531-126">`AwaitUnsafeOnCompleted()` should call `awaiter.OnCompleted(action)` with an action that calls `stateMachine.MoveNext()` when the awaiter completes.</span></span> <span data-ttu-id="5b531-127">`INotifyCompletion`与和类似 `builder.AwaitOnCompleted()` 。</span><span class="sxs-lookup"><span data-stu-id="5b531-127">Similarly for `INotifyCompletion` and `builder.AwaitOnCompleted()`.</span></span>

## <a name="overload-resolution"></a><span data-ttu-id="5b531-128">重载决策</span><span class="sxs-lookup"><span data-stu-id="5b531-128">Overload Resolution</span></span>
<span data-ttu-id="5b531-129">重载决策扩展为可识别除和外的 _任务类型_ `Task` `Task<T>` 。</span><span class="sxs-lookup"><span data-stu-id="5b531-129">Overload resolution is extended to recognize _task types_ in addition to `Task` and `Task<T>`.</span></span>

<span data-ttu-id="5b531-130">`async`不返回值的 lambda 对于非泛型 _任务类型_ 的重载候选参数是完全匹配项，并且 `async` 具有返回类型的 lambda `T` 是泛型 _任务类型_ 的重载候选项参数的完全匹配项。</span><span class="sxs-lookup"><span data-stu-id="5b531-130">An `async` lambda with no return value is an exact match for an overload candidate parameter of non-generic _task type_ , and an `async` lambda with return type `T` is an exact match for an overload candidate parameter of generic _task type_.</span></span> 

<span data-ttu-id="5b531-131">否则，如果 `async` lambda 不是 _任务类型_ 的两个候选参数之一的完全匹配项，或者两者的完全匹配项，并且有一个从候选类型到另一个候选类型的隐式转换，则从候选 wins。</span><span class="sxs-lookup"><span data-stu-id="5b531-131">Otherwise if an `async` lambda is not an exact match for either of two candidate parameters of _task types_ , or an exact match for both, and there is an implicit conversion from one candidate type to the other, the from candidate wins.</span></span> <span data-ttu-id="5b531-132">否则，将以递归方式评估类型和内的类型， `A` `B` `Task1<A>` `Task2<B>` 以便更好地匹配。</span><span class="sxs-lookup"><span data-stu-id="5b531-132">Otherwise recursively evaluate the types `A` and `B` within `Task1<A>` and `Task2<B>` for better match.</span></span>

<span data-ttu-id="5b531-133">否则，如果 `async` lambda 不是 _任务类型_ 的两个候选参数中的任何一个候选参数的完全匹配项，而另一个候选项是比另一个候选参数更专用的类型，则更专用的候选入选。</span><span class="sxs-lookup"><span data-stu-id="5b531-133">Otherwise if an `async` lambda is not an exact match for either of two candidate parameters of _task types_ , but one candidate is a more specialized type than the other, the more specialized candidate wins.</span></span>

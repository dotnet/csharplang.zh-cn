---
ms.openlocfilehash: 802722e4331a10228eb77676d28338ace5ef07b2
ms.sourcegitcommit: 4c8b0a1c815f6ed5f69e2bdff94da354b2908fed
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/05/2020
ms.locfileid: "93406298"
---
<a name="async-task-types-in-c"></a>C 中的异步任务类型#
======================
扩展 `async` 以支持与特定模式匹配的 _任务类型_ ，以及已知类型 `System.Threading.Tasks.Task` 和 `System.Threading.Tasks.Task<T>` 。

## <a name="task-type"></a>任务类型
_任务类型_ 为 `class` 或 `struct` ，其关联的 _生成器类型_ 使用标识 `System.Runtime.CompilerServices.AsyncMethodBuilderAttribute` 。
对于返回值的方法， _任务类型_ 可以为非泛型，对于不返回值的异步方法或泛型。

若要支持 `await` ， _任务类型_ 必须具有相应的可访问 `GetAwaiter()` 方法，该方法返回 _awaiter 类型_ 的实例 (参阅 _c # 7.7.7.1 可等待表达式_ ) 。
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
## <a name="builder-type"></a>生成器类型
_生成器类型_ 为 `class` 或 `struct` ，它对应于特定的 _任务类型_ 。
_生成器类型_ 最多可以有1个类型参数，并且不得嵌套在泛型类型中。
_生成器类型_ 具有以下 `public` 方法。
对于非泛型 _生成器类型_ ，没有 `SetResult()` 参数。
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
## <a name="execution"></a>执行
编译器使用以上类型为方法的状态机生成代码 `async` 。
 (生成的代码等效于为返回、或的异步方法生成的 `Task` 代码 `Task<T>` `void` 。
不同之处在于，对于这些已知类型， _生成器类型_ 对编译器也是已知的。 ) 

`Builder.Create()` 调用以创建 _生成器类型_ 的实例。

如果状态机实现为 `struct` ，则 `builder.SetStateMachine(stateMachine)` 将使用状态机的已装箱实例调用，如有必要，生成器可以缓存该实例。

`builder.Start(ref stateMachine)` 调用将生成器与编译器生成的状态机实例相关联。
生成器必须 `stateMachine.MoveNext()` 在 `Start()` `Start()` 返回以提升状态机之前或之后调用。
`Start()`返回后，方法将调用以使 `async` `builder.Task` 任务从异步方法返回。

每次调用 `stateMachine.MoveNext()` 都会提升状态机。
如果状态机成功完成， `builder.SetResult()` 则调用，方法返回值（如果有）。
如果在状态机中引发了异常， `builder.SetException(exception)` 则会调用。

如果状态机到达 `await expr` 表达式， `expr.GetAwaiter()` 则调用。
如果 awaiter 实现 `ICriticalNotifyCompletion` 并且 `IsCompleted` 为 false，则状态机调用 `builder.AwaitUnsafeOnCompleted(ref awaiter, ref stateMachine)` 。
`AwaitUnsafeOnCompleted()` 应调用 `awaiter.OnCompleted(action)` `stateMachine.MoveNext()` awaiter 完成时调用的操作。 `INotifyCompletion`与和类似 `builder.AwaitOnCompleted()` 。

## <a name="overload-resolution"></a>重载决策
重载决策扩展为可识别除和外的 _任务类型_ `Task` `Task<T>` 。

`async`不返回值的 lambda 对于非泛型 _任务类型_ 的重载候选参数是完全匹配项，并且 `async` 具有返回类型的 lambda `T` 是泛型 _任务类型_ 的重载候选项参数的完全匹配项。 

否则，如果 `async` lambda 不是 _任务类型_ 的两个候选参数之一的完全匹配项，或者两者的完全匹配项，并且有一个从候选类型到另一个候选类型的隐式转换，则从候选 wins。 否则，将以递归方式评估类型和内的类型， `A` `B` `Task1<A>` `Task2<B>` 以便更好地匹配。

否则，如果 `async` lambda 不是 _任务类型_ 的两个候选参数中的任何一个候选参数的完全匹配项，而另一个候选项是比另一个候选参数更专用的类型，则更专用的候选入选。

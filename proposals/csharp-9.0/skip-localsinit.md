---
ms.openlocfilehash: 52b43abd2d8fb56088a68c7169289a63c43ce96f
ms.sourcegitcommit: 0c25406d8a99064bb85d934bb32ffcf547753acc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/28/2020
ms.locfileid: "87297254"
---
# <a name="suppress-emitting-of-localsinit-flag"></a>禁止发出 `localsinit` 标志。

* [x] 建议
* [] 原型：未启动
* [] 实现：未启动
* [] 规范：未启动

## <a name="summary"></a>摘要
[summary]: #summary

允许禁止 `localsinit` 通过属性发出标志 `SkipLocalsInitAttribute` 。 

## <a name="motivation"></a>动机
[motivation]: #motivation


### <a name="background"></a>背景
每个不包含引用的 CLR 规范局部变量不会通过 VM/JIT 初始化为特定值。 在不初始化的情况下读取此类变量是类型安全的，但此行为是未定义的，并且是特定于实现的。 通常未初始化的局部变量包含保留在堆栈帧现在占用的内存中的任何值。 这可能导致不确定的行为，并且难以重现 bug。 

可以通过两种方法来 "分配" 局部变量： 
- 通过存储值或 
- 通过指定 `localsinit` 标志，可将分配给本地内存池的所有内容转换为零初始化说明：这包括本地变量和 `stackalloc` 数据。    

不建议使用未初始化的数据，并且在可验证代码中不允许使用。 尽管可以通过流分析的方法来证明这一点，但允许验证算法非常保守，只需要 `localsinit` 设置。

在过去的 c # 编译器 `localsinit` 中，对声明局部变量的所有方法发出标志。

尽管 c # 使用的是明确的赋值分析，这比 CLR 规范所需的方法更严格（c # 还需要考虑对局部变量的范围），但并不是严格保证最终的代码是可进行正式验证的：
- CLR 和 c # 规则可能不同意传递 local as 参数是否为 `out` `use` 。
- 已知条件时，CLR 和 c # 规则可能不同意条件分支的处理（常量传播）。
- CLR 可能也只需要 `localinits` ，因为这是允许的。  

### <a name="problem"></a>问题
在高性能的应用程序中，强制的零初始化的成本可能比较明显。 使用时，此方法尤其明显 `stackalloc` 。

在某些情况下，在后续赋值时，JIT 可以 elide 单个局部变量的初始零初始化。 并非所有 Jit 都这样做，因此这种优化有限制。 它不有助于 `stackalloc` 。

为了说明问题是实际问题，有一个已知 bug，其中不包含任何局部变量的方法 `IL` 不会有 `localsinit` 标志。 用户通过 `stackalloc` 将此类方法加入到此类方法中来利用 bug，以避免初始化成本。 这是因为，缺少 `IL` 局部变量是不稳定的指标，可能因 codegen 策略中的更改而异。 Bug 应该是固定的，用户应获得更多记录并可靠的方式来禁止标志。 

## <a name="detailed-design"></a>详细设计

允许将指定 `System.Runtime.CompilerServices.SkipLocalsInitAttribute` 为指示编译器不发出标志的方式 `localsinit` 。
 
这种情况的最终结果将是 JIT 不能初始化局部变量，这在大多数情况下都是在 c # 中 unobservable。  
除了该数据以外，还 `stackalloc` 不会初始化为零。 这无疑是显而易见的，但也是最具打动的方案。

允许和识别的属性目标为： `Method` 、 `Property` 、 `Module` 、、、 `Class` `Struct` `Interface` 、 `Constructor` 。 但是，编译器不需要为列出的目标定义该属性，也不会小心定义该属性的程序集。 

如果在容器上指定了 attribute （ `class` ，并且 `module` 包含嵌套方法的包含方法，则为 ...），标志将影响容器中包含的所有方法。

合成方法从逻辑容器/所有者继承标志。 

标志仅影响实际方法体的 codegen 策略。 亦. 标志对抽象方法不起作用，并且不会传播到重写/实现方法。

这是显式的**_编译器功能_**，而**_不是语言功能_**。  
类似于编译器命令行开关，该功能控制特定 codegen 策略的实现细节，而无需 c # 规范。

## <a name="drawbacks"></a>缺点
[drawbacks]: #drawbacks

- 旧的/其他编译器可能不接受属性。
忽略属性的行为是兼容的。 只有可能会导致轻微的性能下降。

- 不带标志的代码 `localinits` 可能会触发验证失败。
要求提供此功能的用户通常不关心具有可验证性。 
 
- 在更高级别上应用属性，而不是单个方法具有非本地效果，使用时可观察到此效果 `stackalloc` 。 但这是最常请求的方案。

## <a name="alternatives"></a>备选项
[alternatives]: #alternatives

- `localinits`在上下文中声明方法时省略标志 `unsafe` 。 这可能会导致在的情况下，从确定性到不确定性的无提示和危险行为更改 `stackalloc` 。

- 忽略 `localinits` 标志 always。
甚至更糟。

- 省略 `localinits` 标志 `stackalloc` ，除非在方法体中使用。
不处理请求最多的方案，可能会打开无法验证的代码，而无需恢复返回的选项。

## <a name="unresolved-questions"></a>未解决的问题
[unresolved]: #unresolved-questions

- 该属性是否应实际发出到元数据？ 

## <a name="design-meetings"></a>设计会议

尚无。 
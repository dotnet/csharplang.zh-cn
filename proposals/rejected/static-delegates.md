---
ms.openlocfilehash: ff1e135451b1c911c36022f6e55fc4bc1bfe4557
ms.sourcegitcommit: 1f5b1dc19d21038b59bfce169fd49e121a5a1f4e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/18/2021
ms.locfileid: "101101818"
---
# <a name="static-delegates-superseded-by-csharp-90function-pointersmd"></a>静态委托 (由取代 [。/csharp-9.0/function-pointers.md](../csharp-9.0/function-pointers.md)) 

* [x] 建议
* [] 原型：未启动
* [] 实现：未启动
* [] 规范：未启动

## <a name="summary"></a>摘要
[summary]: #summary

为 c # 语言提供常规用途的轻型回调功能。

## <a name="motivation"></a>动机
[motivation]: #motivation

现在，用户可以使用类型创建回调 `System.Delegate` 。 不过，这些都是相当的重型 (例如，需要一个堆分配，并始终将处理链接回调) 在一起。

此外，不 `System.Delegate` 提供与非托管函数指针的最佳互操作性，也就是说，它在跨托管/非托管边界时无需直接复制并需要进行封送处理。

只需稍作调整，就能提供一种新类型的委托，该委托类型为轻型、通用和 interops，适用于本机代码。

## <a name="detailed-design"></a>详细设计
[design]: #detailed-design

可以通过以下方法声明静态委托：

```C#
static delegate int Func()
```

另外，还可以通过类似于的内容来特性声明 `System.Runtime.InteropServices.UnmanagedFunctionPointer` ，以便可以控制调用约定、字符串封送处理和设置的上一错误行为。 注意：使用 `System.Runtime.InteropServices.UnmanagedFunctionPointer` 自身将不起作用，因为它仅可用于委托。

编译器会将声明转换为类似于以下内容的内部表示形式

```C#
struct <Func>e__StaticDelegate
{
    IntPtr pFunction;

    static int WellKnownCompilerName();
}
```

也就是说，它由一个结构内部表示，该结构具有类型为的单个成员 `IntPtr` (这种结构是直接复制的，并且不会产生任何堆分配) ：
* 成员包含要作为回调的函数的地址。
* 类型声明与回调的方法签名匹配的方法。
* 结构的名称不应是用户可构造 (与其他内部生成的后备结构) 相同。
 * 例如：固定大小缓冲区以 (格式生成名称为的结构，并将其作为 `<name>e__FixedBuffer` `<` 标识符的一部分， `>` 并使标识符在 c # 中不可构造，但在 IL) 中仍可用。
* 方法声明的名称应为在所有静态委托类型中使用的已知名称 (这允许编译器知道在确定签名) 时要查找的名称。

静态委托的值只能绑定到与回调签名匹配的静态方法。

不支持将回调链接在一起。

回调的调用将由 `calli` 指令实现。

## <a name="drawbacks"></a>缺点
[drawbacks]: #drawbacks

静态委托不能与使用常规委托的现有 Api 一起使用 (一个需要在同一签名) 的常规委托中包装说静态委托。
* 给定，在 `System.Delegate` 内部表示为一组 `object` `IntPtr` (字段，可以 http://source.dot.net/#System.Private.CoreLib/src/System/Delegate.cs) 允许将静态委托隐式转换为 `System.Delegate` 具有匹配方法签名的。 还可以按相反的方向提供显式转换，前提是符合 `System.Delegate` 作为静态委托的所有要求。

需要额外的工作，以使静态委托在核心框架中轻松使用。

## <a name="alternatives"></a>备选方法
[alternatives]: #alternatives

TBD

## <a name="unresolved-questions"></a>未解决的问题
[unresolved]: #unresolved-questions

设计的哪些部分仍是待定的？

## <a name="design-meetings"></a>设计会议

链接到影响此建议的设计说明，并在一个句子中介绍它们所导致的更改。



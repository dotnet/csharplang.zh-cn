---
ms.openlocfilehash: 02ebf35fc43fee1183be5d0118e03e02b17f41a9
ms.sourcegitcommit: 38908f71e529669d1e049db6c6cce2cad65c1727
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/11/2020
ms.locfileid: "94498291"
---
# <a name="pattern-match-spanchar-on-a-constant-string"></a>常量字符串的模式匹配 `Span<char>`

* [x] 建议
* [x] 原型：已完成
* [x] 实现：正在进行
* [] 规范：未启动

## <a name="summary"></a>总结
[summary]: #summary

允许 `Span<char>` 对常量字符串匹配和的模式 `ReadOnlySpan<char>` 。

## <a name="motivation"></a>动机
[motivation]: #motivation

对于性能， `Span<char>` 在许多情况下，和的使用优先 `ReadOnlySpan<char>` 于字符串。 框架添加了许多新的 Api，以允许你使用来 `ReadOnlySpan<char>` 替代 `string` 。

对字符串执行的常见操作是使用开关来测试它是否为特定值，并且编译器会优化此类开关。 不过，目前没有办法有效地执行相同的操作，而不是 `ReadOnlySpan<char>` 手动实现开关和优化。

为了鼓励采用 `ReadOnlySpan<char>` ，我们允许在一个常数上对进行模式匹配 `ReadOnlySpan<char>` ， `string` 因此还允许在开关中使用它。

## <a name="detailed-design"></a>详细设计
[design]: #detailed-design

我们按如下所示为固定模式改变 [规格](../csharp-7.0/pattern-matching.md#constant-pattern) (以粗体显示) ：

> 常数模式根据常数值测试表达式的值。 常数可以是任何常数表达式，如文本、声明的变量的名称 `const` 、枚举常量或 `typeof` 表达式等。
>
> 如果 *e* 和 *c* 均为整型类型，则当表达式的结果为时，该模式将被视为匹配 `e == c` `true` 。
>
> **如果 *e* 的类型为 `System.Span<char>` 或 `System.ReadOnlySpan<char>` ，并且 *c* 是常量字符串，并且 *c* 不具有常数值 `null` ，则在返回时，模式会被视为匹配 `System.MemoryExtensions.SequenceEqual<char>(e, System.MemoryExtensions.AsSpan(c))` `true` 。**
> 
> 否则，如果返回，则认为模式是匹配的 `object.Equals(e, c)` `true` 。 在这种情况下，如果 *e* 的静态类型与常量的类型不 *兼容* ，则会发生编译时错误。

`System.Span<T>` 和 `System.ReadOnlySpan<T>` 通过名称匹配，必须是 `ref struct` ，并且可以在 corlib 外部定义。 `System.MemoryExtensions` 是按名称匹配的，可以在 corlib 外部定义。 的签名 `System.MemoryExtensions.SequenceEqual` 必须分别匹配 `public static bool SequenceEqual<T>(System.Span<T>, System.ReadOnlySpan<T>)` 和 `public static bool SequenceEqual<T>(System.ReadOnlySpan<T>, System.ReadOnlySpan<T>)` ，并且的签名 `System.MemoryExtensions.AsSpan` 必须匹配 `public static System.ReadOnlySpan<char> AsSpan(string)` 。 不考虑带可选参数的方法。

## <a name="drawbacks"></a>缺点
[drawbacks]: #drawbacks

None

## <a name="alternatives"></a>备选项
[alternatives]: #alternatives

None

## <a name="unresolved-questions"></a>未解决的问题
[unresolved]: #unresolved-questions

None

## <a name="design-meetings"></a>设计会议

https://github.com/dotnet/csharplang/blob/master/meetings/2020/LDM-2020-10-07.md#readonlyspanchar-patterns

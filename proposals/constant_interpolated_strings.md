---
ms.openlocfilehash: bcf6db3f0eecdce5cf82a738571ef77695a3ec77
ms.sourcegitcommit: ae69a53cf0cd57b3bcd17263a33a317a2c1dd205
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/11/2021
ms.locfileid: "103026064"
---
# <a name="constant-interpolated-strings"></a>常数内插字符串

* [x] 建议
* [] 原型： [未启动](https://github.com/kevinsun-dev/roslyn/BRANCH_NAME)
* [] 实现： [未启动](https://github.com/dotnet/roslyn/BRANCH_NAME)
* [] 规范： [未启动](pr/1)

## <a name="summary"></a>总结
[summary]: #summary

允许从 string 常量类型的内插字符串生成常量。

## <a name="motivation"></a>动机
[motivation]: #motivation

以下代码已合法：
```
public class C
{
    const string S1 = "Hello world";
    const string S2 = "Hello" + " " + "World";
    const string S3 = S1 + " Kevin, welcome to the team!";
}
```
然而，有很多社区请求使以下也是合法的：
```
public class C
{
    const string S1 = $"Hello world";
    const string S2 = $"Hello{" "}World";
    const string S3 = $"{S1} Kevin, welcome to the team!";
}
```
此建议表示了常量字符串生成的下一个逻辑步骤，在该步骤中，在其他情况下使用的现有字符串语法适用于常量。

## <a name="detailed-design"></a>详细设计
[design]: #detailed-design

下面表示此新建议下的常量表达式的更新规范。 可在 [此处](https://github.com/dotnet/csharplang/blob/master/spec/expressions.md#constant-expressions)找到从此直接基于的当前规范。

### <a name="constant-expressions"></a>常量表达式

*Constant_expression* 是可以在编译时完全计算的表达式。

```antlr
constant_expression
    : expression
    ;
```

常数表达式必须是 `null` 文本或具有以下类型之一的值：、、、、、、、、、、、、、、 `sbyte` `byte` `short` `ushort` `int` `uint` `long` `ulong` `char` `float` `double` `decimal` `bool` `object` `string` 或任何枚举类型。 常量表达式中仅允许使用以下构造：

*  文本 (包括 `null` 文本) 。
*  对 `const` 类和结构类型成员的引用。
*  对枚举类型的成员的引用。
*  对 `const` 参数或局部变量的引用
*  带括号的子表达式，它们本身就是常量表达式。
*  如果目标类型是上面列出的类型之一，则强制转换表达式。
*  `checked` 和 `unchecked` 表达式
*  默认值表达式
*  Nameof 表达式
*  预定义的 `+` 、、 `-` `!` 和 `~` 一元运算符。
*  预定义的、、、、、、、、、、、、、、、、、、、、、、、 `+` `-` `*` `/` `%` `<<` `>>` `&` `|` `^` `&&` `||` `==` `!=` `<` `>` `<=` 和 `>=` 二元运算符，前提是每个操作数都是上面列出的类型。
*  `?:`条件运算符。
*  *内插字符串 `${}` ，前提是所有组件都是类型的常量表达式 `string` ，所有内插组件都缺少对齐和格式说明符。*

常数表达式中允许以下转换：

*  标识转换
*  数值转换
*  枚举转换
*  常数表达式转换
*  隐式和显式引用转换，前提是转换的源是计算结果为 null 值的常量表达式。

不允许在常数表达式中使用其他转换，包括非 null 值的装箱、取消装箱和隐式引用转换。 例如：
```csharp
class C 
{
    const object i = 5;         // error: boxing conversion not permitted
    const object str = "hello"; // error: implicit reference conversion
}
```
i 的初始化是错误的，因为需要装箱转换。 Str 的初始化是错误的，因为需要从非 null 值进行隐式引用转换。

只要表达式满足上面列出的要求，就会在编译时计算表达式。 即使表达式是包含非常量构造的更大的表达式的子表达式，也是如此。

常数表达式的编译时计算使用与非常量表达式的运行时计算相同的规则，只不过运行时计算会引发异常，编译时计算会导致发生编译时错误。

除非将常量表达式显式放置在上下文中 `unchecked` ，否则在表达式的编译时计算过程中发生的溢出将始终导致编译时错误 ([常数表达式](../spec/expressions.md#constant-expressions)) 。

常数表达式出现在下面列出的上下文中。 在这些上下文中，如果表达式在编译时无法完全计算，则会发生编译时错误。

*  常数声明 ([常量](../spec/classes.md#constants)) 。
*  枚举成员声明 (枚举 [成员](../spec/enums.md#enum-members)) 。
*  形参的默认实参列出 ([方法形参](../spec/classes.md#method-parameters)) 
*  `case``switch` [switch 语句](../spec/statements.md#the-switch-statement) () 的语句的标签。
*  `goto case`[goto 语句 (的](../spec/statements.md#the-goto-statement)语句) 。
*  数组创建表达式中的维度长度 ([数组创建](../spec/expressions.md#array-creation-expressions) 表达式) 包含初始值设定项。
*  特性 ([特性](../spec/attributes.md)) 。

隐式常量表达式转换 ([隐式常量表达式转换](conversions.md#implicit-constant-expression-conversions)) 允许将类型的常量表达式 `int` 转换为 `sbyte` 、、、、 `byte` `short` `ushort` `uint` 或 `ulong` ，前提是常量表达式的值在目标类型的范围内。

## <a name="drawbacks"></a>缺点
[drawbacks]: #drawbacks

此建议增加了 exchange 中编译器的额外复杂性，以更广泛地了解内插字符串。 由于这些字符串是在编译时完全计算的，因此，内插字符串的重要自动格式设置功能不必要。 大多数用例都可以通过下面的替代方法进行复制。

## <a name="alternatives"></a>备选方法
[alternatives]: #alternatives

`+`String concatnation 的 current 运算符可将字符串以类似方式合并到当前提议。

## <a name="unresolved-questions"></a>未解决的问题
[unresolved]: #unresolved-questions

设计的哪些部分仍 undecided？

## <a name="design-meetings"></a>设计会议

链接到影响此建议的设计说明，并在一个句子中介绍它们所导致的更改。



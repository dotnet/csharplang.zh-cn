---
ms.openlocfilehash: bcf6db3f0eecdce5cf82a738571ef77695a3ec77
ms.sourcegitcommit: ae69a53cf0cd57b3bcd17263a33a317a2c1dd205
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/11/2021
ms.locfileid: "103026064"
---
# <a name="constant-interpolated-strings"></a><span data-ttu-id="0c048-101">常数内插字符串</span><span class="sxs-lookup"><span data-stu-id="0c048-101">Constant Interpolated Strings</span></span>

* <span data-ttu-id="0c048-102">[x] 建议</span><span class="sxs-lookup"><span data-stu-id="0c048-102">[x] Proposed</span></span>
* <span data-ttu-id="0c048-103">[] 原型： [未启动](https://github.com/kevinsun-dev/roslyn/BRANCH_NAME)</span><span class="sxs-lookup"><span data-stu-id="0c048-103">[ ] Prototype: [Not Started](https://github.com/kevinsun-dev/roslyn/BRANCH_NAME)</span></span>
* <span data-ttu-id="0c048-104">[] 实现： [未启动](https://github.com/dotnet/roslyn/BRANCH_NAME)</span><span class="sxs-lookup"><span data-stu-id="0c048-104">[ ] Implementation: [Not Started](https://github.com/dotnet/roslyn/BRANCH_NAME)</span></span>
* <span data-ttu-id="0c048-105">[] 规范： [未启动](pr/1)</span><span class="sxs-lookup"><span data-stu-id="0c048-105">[ ] Specification: [Not Started](pr/1)</span></span>

## <a name="summary"></a><span data-ttu-id="0c048-106">总结</span><span class="sxs-lookup"><span data-stu-id="0c048-106">Summary</span></span>
[summary]: #summary

<span data-ttu-id="0c048-107">允许从 string 常量类型的内插字符串生成常量。</span><span class="sxs-lookup"><span data-stu-id="0c048-107">Enables constants to be generated from interpolated strings of type string constant.</span></span>

## <a name="motivation"></a><span data-ttu-id="0c048-108">动机</span><span class="sxs-lookup"><span data-stu-id="0c048-108">Motivation</span></span>
[motivation]: #motivation

<span data-ttu-id="0c048-109">以下代码已合法：</span><span class="sxs-lookup"><span data-stu-id="0c048-109">The following code is already legal:</span></span>
```
public class C
{
    const string S1 = "Hello world";
    const string S2 = "Hello" + " " + "World";
    const string S3 = S1 + " Kevin, welcome to the team!";
}
```
<span data-ttu-id="0c048-110">然而，有很多社区请求使以下也是合法的：</span><span class="sxs-lookup"><span data-stu-id="0c048-110">However, there have been many community requests to make the following also legal:</span></span>
```
public class C
{
    const string S1 = $"Hello world";
    const string S2 = $"Hello{" "}World";
    const string S3 = $"{S1} Kevin, welcome to the team!";
}
```
<span data-ttu-id="0c048-111">此建议表示了常量字符串生成的下一个逻辑步骤，在该步骤中，在其他情况下使用的现有字符串语法适用于常量。</span><span class="sxs-lookup"><span data-stu-id="0c048-111">This proposal represents the next logical step for constant string generation, where existing string syntax that works in other situations is made to work for constants.</span></span>

## <a name="detailed-design"></a><span data-ttu-id="0c048-112">详细设计</span><span class="sxs-lookup"><span data-stu-id="0c048-112">Detailed design</span></span>
[design]: #detailed-design

<span data-ttu-id="0c048-113">下面表示此新建议下的常量表达式的更新规范。</span><span class="sxs-lookup"><span data-stu-id="0c048-113">The following represent the updated specifications for constant expressions under this new proposal.</span></span> <span data-ttu-id="0c048-114">可在 [此处](https://github.com/dotnet/csharplang/blob/master/spec/expressions.md#constant-expressions)找到从此直接基于的当前规范。</span><span class="sxs-lookup"><span data-stu-id="0c048-114">Current specifications from which this was directly based on can be found [here](https://github.com/dotnet/csharplang/blob/master/spec/expressions.md#constant-expressions).</span></span>

### <a name="constant-expressions"></a><span data-ttu-id="0c048-115">常量表达式</span><span class="sxs-lookup"><span data-stu-id="0c048-115">Constant Expressions</span></span>

<span data-ttu-id="0c048-116">*Constant_expression* 是可以在编译时完全计算的表达式。</span><span class="sxs-lookup"><span data-stu-id="0c048-116">A *constant_expression* is an expression that can be fully evaluated at compile-time.</span></span>

```antlr
constant_expression
    : expression
    ;
```

<span data-ttu-id="0c048-117">常数表达式必须是 `null` 文本或具有以下类型之一的值：、、、、、、、、、、、、、、 `sbyte` `byte` `short` `ushort` `int` `uint` `long` `ulong` `char` `float` `double` `decimal` `bool` `object` `string` 或任何枚举类型。</span><span class="sxs-lookup"><span data-stu-id="0c048-117">A constant expression must be the `null` literal or a value with one of  the following types: `sbyte`, `byte`, `short`, `ushort`, `int`, `uint`, `long`, `ulong`, `char`, `float`, `double`, `decimal`, `bool`, `object`, `string`, or any enumeration type.</span></span> <span data-ttu-id="0c048-118">常量表达式中仅允许使用以下构造：</span><span class="sxs-lookup"><span data-stu-id="0c048-118">Only the following constructs are permitted in constant expressions:</span></span>

*  <span data-ttu-id="0c048-119">文本 (包括 `null` 文本) 。</span><span class="sxs-lookup"><span data-stu-id="0c048-119">Literals (including the `null` literal).</span></span>
*  <span data-ttu-id="0c048-120">对 `const` 类和结构类型成员的引用。</span><span class="sxs-lookup"><span data-stu-id="0c048-120">References to `const` members of class and struct types.</span></span>
*  <span data-ttu-id="0c048-121">对枚举类型的成员的引用。</span><span class="sxs-lookup"><span data-stu-id="0c048-121">References to members of enumeration types.</span></span>
*  <span data-ttu-id="0c048-122">对 `const` 参数或局部变量的引用</span><span class="sxs-lookup"><span data-stu-id="0c048-122">References to `const` parameters or local variables</span></span>
*  <span data-ttu-id="0c048-123">带括号的子表达式，它们本身就是常量表达式。</span><span class="sxs-lookup"><span data-stu-id="0c048-123">Parenthesized sub-expressions, which are themselves constant expressions.</span></span>
*  <span data-ttu-id="0c048-124">如果目标类型是上面列出的类型之一，则强制转换表达式。</span><span class="sxs-lookup"><span data-stu-id="0c048-124">Cast expressions, provided the target type is one of the types listed above.</span></span>
*  <span data-ttu-id="0c048-125">`checked` 和 `unchecked` 表达式</span><span class="sxs-lookup"><span data-stu-id="0c048-125">`checked` and `unchecked` expressions</span></span>
*  <span data-ttu-id="0c048-126">默认值表达式</span><span class="sxs-lookup"><span data-stu-id="0c048-126">Default value expressions</span></span>
*  <span data-ttu-id="0c048-127">Nameof 表达式</span><span class="sxs-lookup"><span data-stu-id="0c048-127">Nameof expressions</span></span>
*  <span data-ttu-id="0c048-128">预定义的 `+` 、、 `-` `!` 和 `~` 一元运算符。</span><span class="sxs-lookup"><span data-stu-id="0c048-128">The predefined `+`, `-`, `!`, and `~` unary operators.</span></span>
*  <span data-ttu-id="0c048-129">预定义的、、、、、、、、、、、、、、、、、、、、、、、 `+` `-` `*` `/` `%` `<<` `>>` `&` `|` `^` `&&` `||` `==` `!=` `<` `>` `<=` 和 `>=` 二元运算符，前提是每个操作数都是上面列出的类型。</span><span class="sxs-lookup"><span data-stu-id="0c048-129">The predefined `+`, `-`, `*`, `/`, `%`, `<<`, `>>`, `&`, `|`, `^`, `&&`, `||`, `==`, `!=`, `<`, `>`, `<=`, and `>=` binary operators, provided each operand is of a type listed above.</span></span>
*  <span data-ttu-id="0c048-130">`?:`条件运算符。</span><span class="sxs-lookup"><span data-stu-id="0c048-130">The `?:` conditional operator.</span></span>
*  <span data-ttu-id="0c048-131">*内插字符串 `${}` ，前提是所有组件都是类型的常量表达式 `string` ，所有内插组件都缺少对齐和格式说明符。*</span><span class="sxs-lookup"><span data-stu-id="0c048-131">*Interpolated strings `${}`, provided that all components are constant expressions of type `string` and all interpolated components lack alignment and format specifiers.*</span></span>

<span data-ttu-id="0c048-132">常数表达式中允许以下转换：</span><span class="sxs-lookup"><span data-stu-id="0c048-132">The following conversions are permitted in constant expressions:</span></span>

*  <span data-ttu-id="0c048-133">标识转换</span><span class="sxs-lookup"><span data-stu-id="0c048-133">Identity conversions</span></span>
*  <span data-ttu-id="0c048-134">数值转换</span><span class="sxs-lookup"><span data-stu-id="0c048-134">Numeric conversions</span></span>
*  <span data-ttu-id="0c048-135">枚举转换</span><span class="sxs-lookup"><span data-stu-id="0c048-135">Enumeration conversions</span></span>
*  <span data-ttu-id="0c048-136">常数表达式转换</span><span class="sxs-lookup"><span data-stu-id="0c048-136">Constant expression conversions</span></span>
*  <span data-ttu-id="0c048-137">隐式和显式引用转换，前提是转换的源是计算结果为 null 值的常量表达式。</span><span class="sxs-lookup"><span data-stu-id="0c048-137">Implicit and explicit reference conversions, provided that the source of the conversions is a constant expression that evaluates to the null value.</span></span>

<span data-ttu-id="0c048-138">不允许在常数表达式中使用其他转换，包括非 null 值的装箱、取消装箱和隐式引用转换。</span><span class="sxs-lookup"><span data-stu-id="0c048-138">Other conversions including boxing, unboxing and implicit reference conversions of non-null values are not permitted in constant expressions.</span></span> <span data-ttu-id="0c048-139">例如：</span><span class="sxs-lookup"><span data-stu-id="0c048-139">For example:</span></span>
```csharp
class C 
{
    const object i = 5;         // error: boxing conversion not permitted
    const object str = "hello"; // error: implicit reference conversion
}
```
<span data-ttu-id="0c048-140">i 的初始化是错误的，因为需要装箱转换。</span><span class="sxs-lookup"><span data-stu-id="0c048-140">the initialization of i is an error because a boxing conversion is required.</span></span> <span data-ttu-id="0c048-141">Str 的初始化是错误的，因为需要从非 null 值进行隐式引用转换。</span><span class="sxs-lookup"><span data-stu-id="0c048-141">The initialization of str is an error because an implicit reference conversion from a non-null value is required.</span></span>

<span data-ttu-id="0c048-142">只要表达式满足上面列出的要求，就会在编译时计算表达式。</span><span class="sxs-lookup"><span data-stu-id="0c048-142">Whenever an expression fulfills the requirements listed above, the expression is evaluated at compile-time.</span></span> <span data-ttu-id="0c048-143">即使表达式是包含非常量构造的更大的表达式的子表达式，也是如此。</span><span class="sxs-lookup"><span data-stu-id="0c048-143">This is true even if the expression is a sub-expression of a larger expression that contains non-constant constructs.</span></span>

<span data-ttu-id="0c048-144">常数表达式的编译时计算使用与非常量表达式的运行时计算相同的规则，只不过运行时计算会引发异常，编译时计算会导致发生编译时错误。</span><span class="sxs-lookup"><span data-stu-id="0c048-144">The compile-time evaluation of constant expressions uses the same rules as run-time evaluation of non-constant expressions, except that where run-time evaluation would have thrown an exception, compile-time evaluation causes a compile-time error to occur.</span></span>

<span data-ttu-id="0c048-145">除非将常量表达式显式放置在上下文中 `unchecked` ，否则在表达式的编译时计算过程中发生的溢出将始终导致编译时错误 ([常数表达式](../spec/expressions.md#constant-expressions)) 。</span><span class="sxs-lookup"><span data-stu-id="0c048-145">Unless a constant expression is explicitly placed in an `unchecked` context, overflows that occur in integral-type arithmetic operations and conversions during the compile-time evaluation of the expression always cause compile-time errors ([Constant expressions](../spec/expressions.md#constant-expressions)).</span></span>

<span data-ttu-id="0c048-146">常数表达式出现在下面列出的上下文中。</span><span class="sxs-lookup"><span data-stu-id="0c048-146">Constant expressions occur in the contexts listed below.</span></span> <span data-ttu-id="0c048-147">在这些上下文中，如果表达式在编译时无法完全计算，则会发生编译时错误。</span><span class="sxs-lookup"><span data-stu-id="0c048-147">In these contexts, a compile-time error occurs if an expression cannot be fully evaluated at compile-time.</span></span>

*  <span data-ttu-id="0c048-148">常数声明 ([常量](../spec/classes.md#constants)) 。</span><span class="sxs-lookup"><span data-stu-id="0c048-148">Constant declarations ([Constants](../spec/classes.md#constants)).</span></span>
*  <span data-ttu-id="0c048-149">枚举成员声明 (枚举 [成员](../spec/enums.md#enum-members)) 。</span><span class="sxs-lookup"><span data-stu-id="0c048-149">Enumeration member declarations ([Enum members](../spec/enums.md#enum-members)).</span></span>
*  <span data-ttu-id="0c048-150">形参的默认实参列出 ([方法形参](../spec/classes.md#method-parameters)) </span><span class="sxs-lookup"><span data-stu-id="0c048-150">Default arguments of formal parameter lists ([Method parameters](../spec/classes.md#method-parameters))</span></span>
*  <span data-ttu-id="0c048-151">`case``switch` [switch 语句](../spec/statements.md#the-switch-statement) () 的语句的标签。</span><span class="sxs-lookup"><span data-stu-id="0c048-151">`case` labels of a `switch` statement ([The switch statement](../spec/statements.md#the-switch-statement)).</span></span>
*  <span data-ttu-id="0c048-152">`goto case`[goto 语句 (的](../spec/statements.md#the-goto-statement)语句) 。</span><span class="sxs-lookup"><span data-stu-id="0c048-152">`goto case` statements ([The goto statement](../spec/statements.md#the-goto-statement)).</span></span>
*  <span data-ttu-id="0c048-153">数组创建表达式中的维度长度 ([数组创建](../spec/expressions.md#array-creation-expressions) 表达式) 包含初始值设定项。</span><span class="sxs-lookup"><span data-stu-id="0c048-153">Dimension lengths in an array creation expression ([Array creation expressions](../spec/expressions.md#array-creation-expressions)) that includes an initializer.</span></span>
*  <span data-ttu-id="0c048-154">特性 ([特性](../spec/attributes.md)) 。</span><span class="sxs-lookup"><span data-stu-id="0c048-154">Attributes ([Attributes](../spec/attributes.md)).</span></span>

<span data-ttu-id="0c048-155">隐式常量表达式转换 ([隐式常量表达式转换](conversions.md#implicit-constant-expression-conversions)) 允许将类型的常量表达式 `int` 转换为 `sbyte` 、、、、 `byte` `short` `ushort` `uint` 或 `ulong` ，前提是常量表达式的值在目标类型的范围内。</span><span class="sxs-lookup"><span data-stu-id="0c048-155">An implicit constant expression conversion ([Implicit constant expression conversions](conversions.md#implicit-constant-expression-conversions)) permits a constant expression of type `int` to be converted to `sbyte`, `byte`, `short`, `ushort`, `uint`, or `ulong`, provided the value of the constant expression is within the range of the destination type.</span></span>

## <a name="drawbacks"></a><span data-ttu-id="0c048-156">缺点</span><span class="sxs-lookup"><span data-stu-id="0c048-156">Drawbacks</span></span>
[drawbacks]: #drawbacks

<span data-ttu-id="0c048-157">此建议增加了 exchange 中编译器的额外复杂性，以更广泛地了解内插字符串。</span><span class="sxs-lookup"><span data-stu-id="0c048-157">This proposal adds additional complexity to the compiler in exchange for broader applicability of interpolated strings.</span></span> <span data-ttu-id="0c048-158">由于这些字符串是在编译时完全计算的，因此，内插字符串的重要自动格式设置功能不必要。</span><span class="sxs-lookup"><span data-stu-id="0c048-158">As these strings are fully evaluated at compile time, the valuable automatic formatting features of interpolated strings are less neccesary.</span></span> <span data-ttu-id="0c048-159">大多数用例都可以通过下面的替代方法进行复制。</span><span class="sxs-lookup"><span data-stu-id="0c048-159">Most use cases can be largely replicated through the alternatives below.</span></span>

## <a name="alternatives"></a><span data-ttu-id="0c048-160">备选方法</span><span class="sxs-lookup"><span data-stu-id="0c048-160">Alternatives</span></span>
[alternatives]: #alternatives

<span data-ttu-id="0c048-161">`+`String concatnation 的 current 运算符可将字符串以类似方式合并到当前提议。</span><span class="sxs-lookup"><span data-stu-id="0c048-161">The current `+` operator for string concatnation can combine strings in a similar manner to the current proposal.</span></span>

## <a name="unresolved-questions"></a><span data-ttu-id="0c048-162">未解决的问题</span><span class="sxs-lookup"><span data-stu-id="0c048-162">Unresolved questions</span></span>
[unresolved]: #unresolved-questions

<span data-ttu-id="0c048-163">设计的哪些部分仍 undecided？</span><span class="sxs-lookup"><span data-stu-id="0c048-163">What parts of the design are still undecided?</span></span>

## <a name="design-meetings"></a><span data-ttu-id="0c048-164">设计会议</span><span class="sxs-lookup"><span data-stu-id="0c048-164">Design meetings</span></span>

<span data-ttu-id="0c048-165">链接到影响此建议的设计说明，并在一个句子中介绍它们所导致的更改。</span><span class="sxs-lookup"><span data-stu-id="0c048-165">Link to design notes that affect this proposal, and describe in one sentence for each what changes they led to.</span></span>



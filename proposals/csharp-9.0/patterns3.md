---
ms.openlocfilehash: e3027c3fe9b05d3e5379535c010e148e4fbbc761
ms.sourcegitcommit: 2661a4b3950d2ec5c557b025af13e52ba200fd05
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/16/2020
ms.locfileid: "90687170"
---
# <a name="pattern-matching-changes-for-c-90"></a><span data-ttu-id="5b528-101">C # 9.0 的模式匹配更改</span><span class="sxs-lookup"><span data-stu-id="5b528-101">Pattern-matching changes for C# 9.0</span></span>

<span data-ttu-id="5b528-102">我们正在考虑对 c # 9.0 的模式匹配的一小部分增强功能，这些增强功能具有自然协作，并且能够正常处理许多常见的编程问题：</span><span class="sxs-lookup"><span data-stu-id="5b528-102">We are considering a small handful of enhancements to pattern-matching for C# 9.0 that have natural synergy and work well to address a number of common programming problems:</span></span>
- <span data-ttu-id="5b528-103">https://github.com/dotnet/csharplang/issues/2925 类型模式</span><span class="sxs-lookup"><span data-stu-id="5b528-103">https://github.com/dotnet/csharplang/issues/2925 Type patterns</span></span>
- <span data-ttu-id="5b528-104">https://github.com/dotnet/csharplang/issues/1350 用圆括号模式强制或强调新组合器的优先级</span><span class="sxs-lookup"><span data-stu-id="5b528-104">https://github.com/dotnet/csharplang/issues/1350 Parenthesized patterns to enforce or emphasize precedence of the new combinators</span></span>
- <span data-ttu-id="5b528-105">https://github.com/dotnet/csharplang/issues/1350 需要两个 `and` 不同模式来匹配的联合模式;</span><span class="sxs-lookup"><span data-stu-id="5b528-105">https://github.com/dotnet/csharplang/issues/1350 Conjunctive `and` patterns that require both of two different patterns to match;</span></span>
- <span data-ttu-id="5b528-106">https://github.com/dotnet/csharplang/issues/1350`or`需要匹配两种不同模式的析取范式模式;</span><span class="sxs-lookup"><span data-stu-id="5b528-106">https://github.com/dotnet/csharplang/issues/1350 Disjunctive `or` patterns that require either of two different patterns to match;</span></span>
- <span data-ttu-id="5b528-107">https://github.com/dotnet/csharplang/issues/1350`not`需要指定模式*不*匹配的求反模式; 和</span><span class="sxs-lookup"><span data-stu-id="5b528-107">https://github.com/dotnet/csharplang/issues/1350 Negated `not` patterns that require a given pattern *not* to match; and</span></span>
- <span data-ttu-id="5b528-108">https://github.com/dotnet/csharplang/issues/812 需要输入值小于、小于或等于的关系模式，等等。</span><span class="sxs-lookup"><span data-stu-id="5b528-108">https://github.com/dotnet/csharplang/issues/812 Relational patterns that require the input value to be less than, less than or equal to, etc a given constant.</span></span>

## <a name="parenthesized-patterns"></a><span data-ttu-id="5b528-109">圆括号模式</span><span class="sxs-lookup"><span data-stu-id="5b528-109">Parenthesized Patterns</span></span>

<span data-ttu-id="5b528-110">圆括号模式允许编程人员在任何模式两边加上括号。</span><span class="sxs-lookup"><span data-stu-id="5b528-110">Parenthesized patterns permit the programmer to put parentheses around any pattern.</span></span>  <span data-ttu-id="5b528-111">这对于 c # 8.0 中的现有模式不起作用，但新模式组合器引入了程序员可能要重写的优先级。</span><span class="sxs-lookup"><span data-stu-id="5b528-111">This is not so useful with the existing patterns in C# 8.0, however the new pattern combinators introduce a precedence that the programmer may want to override.</span></span>

```antlr
primary_pattern
    : parenthesized_pattern
    | // all of the existing forms
    ;
parenthesized_pattern
    : '(' pattern ')'
    ;
```

## <a name="type-patterns"></a><span data-ttu-id="5b528-112">类型模式</span><span class="sxs-lookup"><span data-stu-id="5b528-112">Type Patterns</span></span>

<span data-ttu-id="5b528-113">我们允许一种类型作为模式：</span><span class="sxs-lookup"><span data-stu-id="5b528-113">We permit a type as a pattern:</span></span>

``` antlr
primary_pattern
    : type-pattern
    | // all of the existing forms
    ;
type_pattern
    : type
    ;
```

<span data-ttu-id="5b528-114">这 retcons 将现有的 *类型表达式* 作为模式 *表达式* ，其中模式为 *类型模式*，但我们不会更改编译器生成的语法树。</span><span class="sxs-lookup"><span data-stu-id="5b528-114">This retcons the existing *is-type-expression* to be an *is-pattern-expression* in which the pattern is a *type-pattern*, though we would not change the syntax tree produced by the compiler.</span></span>

<span data-ttu-id="5b528-115">一个微妙的实现问题是，此语法是不明确的。</span><span class="sxs-lookup"><span data-stu-id="5b528-115">One subtle implementation issue is that this grammar is ambiguous.</span></span>  <span data-ttu-id="5b528-116">例如，可以在 `a.b` 类型上下文中将字符串分析为限定 (名，) 或在表达式上下文) 中 (点表达式。</span><span class="sxs-lookup"><span data-stu-id="5b528-116">A string such as `a.b` can be parsed either as a qualified name (in a type context) or a dotted expression (in an expression context).</span></span>  <span data-ttu-id="5b528-117">编译器已经能够处理与点式表达式相同的限定名，以便处理类似的内容 `e is Color.Red` 。</span><span class="sxs-lookup"><span data-stu-id="5b528-117">The compiler is already capable of treating a qualified name the same as a dotted expression in order to handle something like `e is Color.Red`.</span></span>  <span data-ttu-id="5b528-118">编译器的语义分析将进一步扩展，以便能够绑定 (语法) 常量模式 (例如，点表达式) 为类型，以便将其视为绑定类型模式，以便支持此构造。</span><span class="sxs-lookup"><span data-stu-id="5b528-118">The compiler's semantic analysis would be further extended to be capable of binding a (syntactic) constant pattern (e.g. a dotted expression) as a type in order to treat it as a bound type pattern in order to support this construct.</span></span>

<span data-ttu-id="5b528-119">完成此更改后，你将能够编写</span><span class="sxs-lookup"><span data-stu-id="5b528-119">After this change, you would be able to write</span></span>
```csharp
void M(object o1, object o2)
{
    var t = (o1, o2);
    if (t is (int, string)) {} // test if o1 is an int and o2 is a string
    switch (o1) {
        case int: break; // test if o1 is an int
        case System.String: break; // test if o1 is a string
    }
}
```

## <a name="relational-patterns"></a><span data-ttu-id="5b528-120">关系模式</span><span class="sxs-lookup"><span data-stu-id="5b528-120">Relational Patterns</span></span>

<span data-ttu-id="5b528-121">与常数值相比，关系模式允许程序员表达输入值必须满足关系约束：</span><span class="sxs-lookup"><span data-stu-id="5b528-121">Relational patterns permit the programmer to express that an input value must satisfy a relational constraint when compared to a constant value:</span></span>

``` C#
    public static LifeStage LifeStageAtAge(int age) => age switch
    {
        < 0 =>  LifeStage.Prenatal,
        < 2 =>  LifeStage.Infant,
        < 4 =>  LifeStage.Toddler,
        < 6 =>  LifeStage.EarlyChild,
        < 12 => LifeStage.MiddleChild,
        < 20 => LifeStage.Adolescent,
        < 40 => LifeStage.EarlyAdult,
        < 65 => LifeStage.MiddleAdult,
        _ =>    LifeStage.LateAdult,
    };
```

<span data-ttu-id="5b528-122">关系模式支持 `<` 所有内置类型上的关系运算符、、 `<=` 和， `>` `>=` 它们支持在表达式中使用两个具有相同类型的操作数的内置类型。</span><span class="sxs-lookup"><span data-stu-id="5b528-122">Relational patterns support the relational operators `<`, `<=`, `>`, and `>=` on all of the built-in types that support such binary relational operators with two operands of the same type in an expression.</span></span> <span data-ttu-id="5b528-123">具体而言，我们支持、、、 `sbyte` `byte` `short` `ushort` 、 `int` 、 `uint` 、 `long` `ulong` `char` `float` `double` `decimal` `nint` `nuint` 、、、、、、、、、、、、、和的所有关系模式。</span><span class="sxs-lookup"><span data-stu-id="5b528-123">Specifically, we support all of these relational patterns for `sbyte`, `byte`, `short`, `ushort`, `int`, `uint`, `long`, `ulong`, `char`, `float`, `double`, `decimal`, `nint`, and `nuint`.</span></span>

```antlr
primary_pattern
    : relational_pattern
    ;
relational_pattern
    : '<' relational_expression
    | '<=' relational_expression
    | '>' relational_expression
    | '>=' relational_expression
    ;
```

<span data-ttu-id="5b528-124">表达式需要计算为常量值。</span><span class="sxs-lookup"><span data-stu-id="5b528-124">The expression is required to evaluate to a constant value.</span></span>  <span data-ttu-id="5b528-125">如果该常数值为或，则是错误的 `double.NaN` `float.NaN` 。</span><span class="sxs-lookup"><span data-stu-id="5b528-125">It is an error if that constant value is `double.NaN` or `float.NaN`.</span></span>  <span data-ttu-id="5b528-126">如果表达式为 null 常量，则是错误的。</span><span class="sxs-lookup"><span data-stu-id="5b528-126">It is an error if the expression is a null constant.</span></span>

<span data-ttu-id="5b528-127">如果输入的类型为，并且定义了适当的内置二元关系运算符，并且该运算符适用于输入作为其左操作数，而给定常量作为其右操作数，则该运算符的计算将被视为关系模式的意义。</span><span class="sxs-lookup"><span data-stu-id="5b528-127">When the input is a type for which a suitable built-in binary relational operator is defined that is applicable with the input as its left operand and the given constant as its right operand, the evaluation of that operator is taken as the meaning of the relational pattern.</span></span>  <span data-ttu-id="5b528-128">否则，使用显式的可为 null 或取消装箱转换将输入转换为表达式的类型。</span><span class="sxs-lookup"><span data-stu-id="5b528-128">Otherwise we convert the input to the type of the expression using an explicit nullable or unboxing conversion.</span></span>  <span data-ttu-id="5b528-129">如果不存在这样的转换，则会发生编译时错误。</span><span class="sxs-lookup"><span data-stu-id="5b528-129">It is a compile-time error if no such conversion exists.</span></span>  <span data-ttu-id="5b528-130">如果转换失败，该模式将被视为不匹配。</span><span class="sxs-lookup"><span data-stu-id="5b528-130">The pattern is considered not to match if the conversion fails.</span></span>  <span data-ttu-id="5b528-131">如果转换成功，则模式匹配操作的结果是计算表达式的结果 `e OP v` ，其中为转换后的 `e` 输入， `OP` 是关系运算符， `v` 是常数表达式。</span><span class="sxs-lookup"><span data-stu-id="5b528-131">If the conversion succeeds then the result of the pattern-matching operation is the result of evaluating the expression `e OP v` where `e` is the converted input, `OP` is the relational operator, and `v` is the constant expression.</span></span>

## <a name="pattern-combinators"></a><span data-ttu-id="5b528-132">模式组合器</span><span class="sxs-lookup"><span data-stu-id="5b528-132">Pattern Combinators</span></span>

<span data-ttu-id="5b528-133">模式 *组合器* 允许使用 (匹配两个不同模式 `and` ，这可以通过重复使用) 来扩展到任意数量的模式，方法是 `and` 使用 `or` (ditto) ，或者使用的是模式的 *求反* `not` 。</span><span class="sxs-lookup"><span data-stu-id="5b528-133">Pattern *combinators* permit matching both of two different patterns using `and` (this can be extended to any number of patterns by the repeated use of `and`), either of two different patterns using `or` (ditto), or the *negation* of a pattern using `not`.</span></span>

<span data-ttu-id="5b528-134">连结符的常见用途是使用</span><span class="sxs-lookup"><span data-stu-id="5b528-134">A common use of a combinator will be the idiom</span></span>

``` c#
if (e is not null) ...
```

<span data-ttu-id="5b528-135">比当前用法更具可读性 `e is object` ，此模式清楚地表明它正在检查非 null 值。</span><span class="sxs-lookup"><span data-stu-id="5b528-135">More readable than the current idiom `e is object`, this pattern clearly expresses that one is checking for a non-null value.</span></span>

<span data-ttu-id="5b528-136">`and`和 `or` 组合器对于测试值范围很有用</span><span class="sxs-lookup"><span data-stu-id="5b528-136">The `and` and `or` combinators will be useful for testing ranges of values</span></span>

``` c#
bool IsLetter(char c) => c is >= 'a' and <= 'z' or >= 'A' and <= 'Z';
```

<span data-ttu-id="5b528-137">此示例演示 `and` 将具有更高的分析优先级 (例如，绑定的) 将更严格 `or` 。</span><span class="sxs-lookup"><span data-stu-id="5b528-137">This example illustrates that `and` will have a higher parsing priority (i.e. will bind more closely) than `or`.</span></span>  <span data-ttu-id="5b528-138">程序员可以使用 *带括号的模式* 来使优先显式：</span><span class="sxs-lookup"><span data-stu-id="5b528-138">The programmer can use the *parenthesized pattern* to make the precedence explicit:</span></span>

``` c#
bool IsLetter(char c) => c is (>= 'a' and <= 'z') or (>= 'A' and <= 'Z');
```

<span data-ttu-id="5b528-139">与所有模式一样，这些组合器可用于需要模式的任何上下文中，其中包括嵌套模式、 *is 模式表达式*、 *switch 表达式*和 switch 语句的 case 标签模式。</span><span class="sxs-lookup"><span data-stu-id="5b528-139">Like all patterns, these combinators can be used in any context in which a pattern is expected, including nested patterns, the *is-pattern-expression*, the *switch-expression*, and the pattern of a switch statement's case label.</span></span>

```antlr
pattern
    : disjunctive_pattern
    ;
disjunctive_pattern
    : disjunctive_pattern 'or' conjunctive_pattern
    | conjunctive_pattern
    ;
conjunctive_pattern
    : conjunctive_pattern 'and' negated_pattern
    | negated_pattern
    ;
negated_pattern
    : 'not' negated_pattern
    | primary_pattern
    ;
primary_pattern
    : // all of the patterns forms previously defined
    ;
```

## <a name="change-to-7542-grammar-ambiguities"></a><span data-ttu-id="5b528-140">更改为7.5.4.2 语法多义性</span><span class="sxs-lookup"><span data-stu-id="5b528-140">Change to 7.5.4.2 Grammar Ambiguities</span></span>

<span data-ttu-id="5b528-141">由于引入了 *type 模式*，因此泛型类型可能出现在标记之前 `=>` 。</span><span class="sxs-lookup"><span data-stu-id="5b528-141">Due to the introduction of the *type pattern*, it is possible for a generic type to appear before the token `=>`.</span></span>  <span data-ttu-id="5b528-142">因此，我们将添加 `=>` 到 *7.5.4.2 语法多义性* 中列出的一组标记，以允许消除 `<` 开始类型参数列表的的歧义。</span><span class="sxs-lookup"><span data-stu-id="5b528-142">We therefore add `=>` to the set of tokens listed in *7.5.4.2 Grammar Ambiguities* to permit disambiguation of the `<` that begins the type argument list.</span></span>  <span data-ttu-id="5b528-143">另请参阅 https://github.com/dotnet/roslyn/issues/47614。</span><span class="sxs-lookup"><span data-stu-id="5b528-143">See also https://github.com/dotnet/roslyn/issues/47614.</span></span>

## <a name="open-issues-with-proposed-changes"></a><span data-ttu-id="5b528-144">打开建议更改的问题</span><span class="sxs-lookup"><span data-stu-id="5b528-144">Open Issues with Proposed Changes</span></span>

### <a name="syntax-for-relational-operators"></a><span data-ttu-id="5b528-145">关系运算符的语法</span><span class="sxs-lookup"><span data-stu-id="5b528-145">Syntax for relational operators</span></span>

<span data-ttu-id="5b528-146">是否为 `and` 、 `or` 和 `not` 某种上下文关键字？</span><span class="sxs-lookup"><span data-stu-id="5b528-146">Are `and`, `or`, and `not` some kind of contextual keyword?</span></span>  <span data-ttu-id="5b528-147">如果是这样，则是否存在重大更改 (例如，将其用作 *声明模式*) 中的指示符。</span><span class="sxs-lookup"><span data-stu-id="5b528-147">If so, is there a breaking change (e.g. compared to their use as a designator in a *declaration-pattern*).</span></span>

### <a name="semantics-eg-type-for-relational-operators"></a><span data-ttu-id="5b528-148">语义 (例如，类型) 关系运算符</span><span class="sxs-lookup"><span data-stu-id="5b528-148">Semantics (e.g. type) for relational operators</span></span>

<span data-ttu-id="5b528-149">我们希望支持使用关系运算符在表达式中进行比较的所有基元类型。</span><span class="sxs-lookup"><span data-stu-id="5b528-149">We expect to support all of the primitive types that can be compared in an expression using a relational operator.</span></span>  <span data-ttu-id="5b528-150">简单情况下的含义是清晰的</span><span class="sxs-lookup"><span data-stu-id="5b528-150">The meaning in simple cases is clear</span></span>

``` c#
bool IsValidPercentage(int x) => x is >= 0 and <= 100;
```

<span data-ttu-id="5b528-151">但如果输入不是基元类型，我们会尝试将其转换为哪种类型？</span><span class="sxs-lookup"><span data-stu-id="5b528-151">But when the input is not such a primitive type, what type do we attempt to convert it to?</span></span>

``` c#
bool IsValidPercentage(object x) => x is >= 0 and <= 100;
```

<span data-ttu-id="5b528-152">我们建议，当输入类型已经是比较的类型时，这是比较的类型。</span><span class="sxs-lookup"><span data-stu-id="5b528-152">We have proposed that when the input type is already a comparable primitive, that is the type of the comparison.</span></span> <span data-ttu-id="5b528-153">但是，当输入不是可比较的基元时，我们将关系视为包含对关系右侧的常量类型的隐式类型测试。</span><span class="sxs-lookup"><span data-stu-id="5b528-153">However, when the input is not a comparable primitive, we treat the relational as including an implicit type test to the type of the constant on the right-hand-side of the relational.</span></span>  <span data-ttu-id="5b528-154">如果程序员打算支持多个输入类型，则必须显式完成：</span><span class="sxs-lookup"><span data-stu-id="5b528-154">If the programmer intends to support more than one input type, that must be done explicitly:</span></span>

``` c#
bool IsValidPercentage(object x) => x is
    >= 0 and <= 100 or    // integer tests
    >= 0F and <= 100F or  // float tests
    >= 0D and <= 100D;    // double tests
```

### <a name="flowing-type-information-from-the-left-to-the-right-of-and"></a><span data-ttu-id="5b528-155">从左侧的左侧流向类型信息 `and`</span><span class="sxs-lookup"><span data-stu-id="5b528-155">Flowing type information from the left to the right of `and`</span></span>

<span data-ttu-id="5b528-156">建议在编写 `and` 连结符时，在左侧了解的有关顶级类型的信息可以流向右侧。</span><span class="sxs-lookup"><span data-stu-id="5b528-156">It has been suggested that when you write an `and` combinator, type information learned on the left about the top-level type could flow to the right.</span></span>  <span data-ttu-id="5b528-157">例如：</span><span class="sxs-lookup"><span data-stu-id="5b528-157">For example</span></span>

```csharp
bool isSmallByte(object o) => o is byte and < 100;
```

<span data-ttu-id="5b528-158">此处，第二个模式的 *输入类型* 将被缩小的 *类型的收缩* 要求范围内 `and` 。</span><span class="sxs-lookup"><span data-stu-id="5b528-158">Here, the *input type* to the second pattern is narrowed by the *type narrowing* requirements of left of the `and`.</span></span>  <span data-ttu-id="5b528-159">我们将为所有模式定义类型收缩语义，如下所示。</span><span class="sxs-lookup"><span data-stu-id="5b528-159">We would define type narrowing semantics for all patterns as follows.</span></span>  <span data-ttu-id="5b528-160">模式的 *缩小类型* `P` 定义如下：</span><span class="sxs-lookup"><span data-stu-id="5b528-160">The *narrowed type* of a pattern `P` is defined as follows:</span></span>
1. <span data-ttu-id="5b528-161">如果 `P` 为类型模式，则 *缩小的类型* 为类型模式类型的类型。</span><span class="sxs-lookup"><span data-stu-id="5b528-161">If `P` is a type pattern, the *narrowed type* is the type of the type pattern's type.</span></span>
2. <span data-ttu-id="5b528-162">如果 `P` 是声明模式，则缩小后的 *类型* 为声明模式类型的类型。</span><span class="sxs-lookup"><span data-stu-id="5b528-162">If `P` is a declaration pattern, the *narrowed type* is the type of the declaration pattern's type.</span></span>
3. <span data-ttu-id="5b528-163">如果 `P` 是提供显式类型的递归模式，则缩小后的 *类型* 为该类型。</span><span class="sxs-lookup"><span data-stu-id="5b528-163">If `P` is a recursive pattern that gives an explicit type, the *narrowed type* is that type.</span></span>
4. <span data-ttu-id="5b528-164">如果 `P` [通过的规则匹配](https://github.com/dotnet/csharplang/blob/master/proposals/csharp-8.0/patterns.md#positional-pattern) `ITuple` ，则缩小的 *类型* 为类型 `System.Runtime.CompilerServices.ITuple` 。</span><span class="sxs-lookup"><span data-stu-id="5b528-164">If `P` is [matched via the rules for](https://github.com/dotnet/csharplang/blob/master/proposals/csharp-8.0/patterns.md#positional-pattern) `ITuple`, the *narrowed type* is the type `System.Runtime.CompilerServices.ITuple`.</span></span>
5. <span data-ttu-id="5b528-165">如果 `P` 是常量模式，其中的常量不是 null 常量，并且表达式没有到*输入类型*的*常量表达式转换*，则*缩小的类型*是常量的类型。</span><span class="sxs-lookup"><span data-stu-id="5b528-165">If `P` is a constant pattern where the constant is not the null constant and where the expression has no *constant expression conversion* to the *input type*, the *narrowed type* is the type of the constant.</span></span>
6. <span data-ttu-id="5b528-166">如果 `P` 是一个关系模式，其中的常量表达式没有到*输入类型*的*常量表达式转换*，则*缩小*后的类型是常量的类型。</span><span class="sxs-lookup"><span data-stu-id="5b528-166">If `P` is a relational pattern where the constant expression has no *constant expression conversion* to the *input type*, the *narrowed type* is the type of the constant.</span></span>
7. <span data-ttu-id="5b528-167">如果 `P` 是 `or` 模式，则如果存在此类通用类型，则 *缩小的类型* 是 subpatterns 的 *缩小类型* 的通用类型。</span><span class="sxs-lookup"><span data-stu-id="5b528-167">If `P` is an `or` pattern, the *narrowed type* is the common type of the *narrowed type* of the subpatterns if such a common type exists.</span></span> <span data-ttu-id="5b528-168">出于此目的，通用类型算法只考虑标识、装箱和隐式引用转换，并将一系列模式的所有 subpatterns 都视为 `or` (忽略带圆括号的模式) 。</span><span class="sxs-lookup"><span data-stu-id="5b528-168">For this purpose, the common type algorithm considers only identity, boxing, and implicit reference conversions, and it considers all subpatterns of a sequence of `or` patterns (ignoring parenthesized patterns).</span></span>
8. <span data-ttu-id="5b528-169">如果 `P` 是 `and` 模式，则缩小后的 *类型* 为适当模式的 *缩小类型* 。</span><span class="sxs-lookup"><span data-stu-id="5b528-169">If `P` is an `and` pattern, the *narrowed type* is the *narrowed type* of the right pattern.</span></span> <span data-ttu-id="5b528-170">而且，左侧模式的 *缩小类型* 是正确模式的 *输入类型* 。</span><span class="sxs-lookup"><span data-stu-id="5b528-170">Moreover, the *narrowed type* of the left pattern is the *input type* of the right pattern.</span></span>
9. <span data-ttu-id="5b528-171">否则，的 *缩小类型* `P` 为 `P` 的输入类型。</span><span class="sxs-lookup"><span data-stu-id="5b528-171">Otherwise the *narrowed type* of `P` is `P`'s input type.</span></span>

### <a name="variable-definitions-and-definite-assignment"></a><span data-ttu-id="5b528-172">变量定义和明确赋值</span><span class="sxs-lookup"><span data-stu-id="5b528-172">Variable definitions and definite assignment</span></span>

<span data-ttu-id="5b528-173">添加 `or` 和模式会在 `not` 模式变量和明确赋值方面产生一些有趣的新问题。</span><span class="sxs-lookup"><span data-stu-id="5b528-173">The addition of `or` and `not` patterns creates some interesting new problems around pattern variables and definite assignment.</span></span>  <span data-ttu-id="5b528-174">由于变量通常可以最多声明一次，因此，在模式的一侧声明的任何模式变量都 `or` 不会在模式匹配时明确赋值。</span><span class="sxs-lookup"><span data-stu-id="5b528-174">Since variables can normally be declared at most once, it would seem any pattern variable declared on one side of an `or` pattern would not be definitely assigned when the pattern matches.</span></span>  <span data-ttu-id="5b528-175">同样，在模式匹配时，不应将在模式中声明的变量 `not` 明确赋值。</span><span class="sxs-lookup"><span data-stu-id="5b528-175">Similarly, a variable declared inside a `not` pattern would not be expected to be definitely assigned when the pattern matches.</span></span>  <span data-ttu-id="5b528-176">解决这种情况的最简单方法是禁止在这些上下文中声明模式变量。</span><span class="sxs-lookup"><span data-stu-id="5b528-176">The simplest way to address this is to forbid declaring pattern variables in these contexts.</span></span>  <span data-ttu-id="5b528-177">但是，这可能过于严格。</span><span class="sxs-lookup"><span data-stu-id="5b528-177">However, this may be too restrictive.</span></span>  <span data-ttu-id="5b528-178">还有其他方法可以考虑。</span><span class="sxs-lookup"><span data-stu-id="5b528-178">There are other approaches to consider.</span></span>

<span data-ttu-id="5b528-179">值得考虑的一种情况是，</span><span class="sxs-lookup"><span data-stu-id="5b528-179">One scenario that is worth considering is this</span></span>

``` csharp
if (e is not int i) return;
M(i); // is i definitely assigned here?
```

<span data-ttu-id="5b528-180">目前这不起作用，因为对于 *is 模式表达式*，只会将模式变量视为 *明确分配* ，其中模式 *变量为 true* ( ") 时明确赋值。</span><span class="sxs-lookup"><span data-stu-id="5b528-180">This does not work today because, for an *is-pattern-expression*, the pattern variables are considered *definitely assigned* only where the *is-pattern-expression* is true ("definitely assigned when true").</span></span>

<span data-ttu-id="5b528-181">从程序员的角度来看，支持这一 (更简单，) 同时也添加了对求反条件语句的支持 `if` 。</span><span class="sxs-lookup"><span data-stu-id="5b528-181">Supporting this would be simpler (from the programmer's perspective) than also adding support for a negated-condition `if` statement.</span></span>  <span data-ttu-id="5b528-182">即使我们添加了此类支持，程序员也可能想知道上述代码片段不起作用。</span><span class="sxs-lookup"><span data-stu-id="5b528-182">Even if we add such support, programmers would wonder why the above snippet does not work.</span></span>  <span data-ttu-id="5b528-183">另一方面，在中，这种情况并不 `switch` 太合理，因为 *当 false* 会有意义时，程序中没有相应的点可明确赋值。</span><span class="sxs-lookup"><span data-stu-id="5b528-183">On the other hand, the same scenario in a `switch` makes less sense, as there is no corresponding point in the program where *definitely assigned when false* would be meaningful.</span></span>  <span data-ttu-id="5b528-184">是否允许在模式表达式中使用它 *，* 但在允许模式的其他上下文中不允许这样做？</span><span class="sxs-lookup"><span data-stu-id="5b528-184">Would we permit this in an *is-pattern-expression* but not in other contexts where patterns are permitted?</span></span>  <span data-ttu-id="5b528-185">这似乎是不规则的。</span><span class="sxs-lookup"><span data-stu-id="5b528-185">That seems irregular.</span></span>

<span data-ttu-id="5b528-186">与此相关的是在 *析取范式模式*下明确赋值的问题。</span><span class="sxs-lookup"><span data-stu-id="5b528-186">Related to this is the problem of definite assignment in a *disjunctive-pattern*.</span></span>

```csharp
if (e is 0 or int i)
{
    M(i); // is i definitely assigned here?
}
```

<span data-ttu-id="5b528-187">仅 `i` 当输入不为零时，才应明确赋值。</span><span class="sxs-lookup"><span data-stu-id="5b528-187">We would only expect `i` to be definitely assigned when the input is not zero.</span></span>  <span data-ttu-id="5b528-188">但由于我们不知道输入值是否为零或不在块中，因此 `i` 未明确赋值。</span><span class="sxs-lookup"><span data-stu-id="5b528-188">But since we don't know whether the input is zero or not inside the block, `i` is not definitely assigned.</span></span>  <span data-ttu-id="5b528-189">但是，如果我们允许 `i` 在不同的互相排斥模式下声明怎么办？</span><span class="sxs-lookup"><span data-stu-id="5b528-189">However, what if we permit `i` to be declared in different mutually exclusive patterns?</span></span>

```csharp
if ((e1, e2) is (0, int i) or (int i, 0))
{
    M(i);
}
```

<span data-ttu-id="5b528-190">此处，变量在 `i` 块中是明确赋值的，当找到零个元素时，将从该元组的另一个元素中获取该变量的值。</span><span class="sxs-lookup"><span data-stu-id="5b528-190">Here, the variable `i` is definitely assigned inside the block, and takes it value from the other element of the tuple when a zero element is found.</span></span>

<span data-ttu-id="5b528-191">还建议您允许变量 (在 case 块的每个事例中定义) ：</span><span class="sxs-lookup"><span data-stu-id="5b528-191">It has also been suggested to permit variables to be (multiply) defined in every case of a case block:</span></span>

```csharp
    case (0, int x):
    case (int x, 0):
        Console.WriteLine(x);
```

<span data-ttu-id="5b528-192">若要进行任何此类工作，必须仔细定义允许此类多个定义的位置，并在哪些条件下将变量视为明确赋值。</span><span class="sxs-lookup"><span data-stu-id="5b528-192">To make any of this work, we would have to carefully define where such multiple definitions are permitted and under what conditions such a variable is considered definitely assigned.</span></span>

<span data-ttu-id="5b528-193">如果以后 (建议) ，我们会选择推迟此类工作</span><span class="sxs-lookup"><span data-stu-id="5b528-193">Should we elect to defer such work until later (which I advise), we could say in C# 9</span></span>
- <span data-ttu-id="5b528-194">在 `not` 或下 `or` ，不能声明模式变量。</span><span class="sxs-lookup"><span data-stu-id="5b528-194">beneath a `not` or `or`, pattern variables may not be declared.</span></span>

<span data-ttu-id="5b528-195">接下来，我们有时间开发一些经验，这些体验可让您更好地了解稍后的可能值。</span><span class="sxs-lookup"><span data-stu-id="5b528-195">Then, we would have time to develop some experience that would provide insight into the possible value of relaxing that later.</span></span>

### <a name="diagnostics-subsumption-and-exhaustiveness"></a><span data-ttu-id="5b528-196">诊断、subsumption 和 exhaustiveness</span><span class="sxs-lookup"><span data-stu-id="5b528-196">Diagnostics, subsumption, and exhaustiveness</span></span>

<span data-ttu-id="5b528-197">这些新模式窗体为 diagnosable 程序员错误引入了许多新机会。</span><span class="sxs-lookup"><span data-stu-id="5b528-197">These new pattern forms introduce many new opportunities for diagnosable programmer error.</span></span>  <span data-ttu-id="5b528-198">我们需要确定将诊断哪些类型的错误，以及如何执行此操作。</span><span class="sxs-lookup"><span data-stu-id="5b528-198">We will need to decide what kinds of errors we will diagnose, and how to do so.</span></span>  <span data-ttu-id="5b528-199">下面是一些示例：</span><span class="sxs-lookup"><span data-stu-id="5b528-199">Here are some examples:</span></span>

``` csharp
case >= 0 and <= 100D:
```

<span data-ttu-id="5b528-200">这种情况永远不会与 (匹配，因为输入不能同时为 `int` 和 `double`) 。</span><span class="sxs-lookup"><span data-stu-id="5b528-200">This case can never match (because the input cannot be both an `int` and a `double`).</span></span>  <span data-ttu-id="5b528-201">我们检测到不能匹配的情况时，我们已遇到错误，但其措辞 ( "switch case 已被上一事例处理"，并且 "模式已经由 switch 表达式的前一 arm 处理" ) 可能会在新方案中产生误导。</span><span class="sxs-lookup"><span data-stu-id="5b528-201">We already have an error when we detect a case that can never match, but its wording ("The switch case has already been handled by a previous case" and "The pattern has already been handled by a previous arm of the switch expression") may be misleading in new scenarios.</span></span>  <span data-ttu-id="5b528-202">我们可能必须修改措辞，只是说该模式将永远不会与输入匹配。</span><span class="sxs-lookup"><span data-stu-id="5b528-202">We may have to modify the wording to just say that the pattern will never match the input.</span></span>

``` csharp
case 1 and 2:
```

<span data-ttu-id="5b528-203">同样，这会是一个错误，因为值不能同时为 `1` 和 `2` 。</span><span class="sxs-lookup"><span data-stu-id="5b528-203">Similarly, this would be an error because a value cannot be both `1` and `2`.</span></span>

``` csharp
case 1 or 2 or 3 or 1:
```

<span data-ttu-id="5b528-204">这种情况可以匹配，但 `or 1` 在结束时，不会对模式添加任何意义。</span><span class="sxs-lookup"><span data-stu-id="5b528-204">This case is possible to match, but the `or 1` at the end adds no meaning to the pattern.</span></span>  <span data-ttu-id="5b528-205">我建议，每当复合模式的某些对或 disjunct 不定义模式变量或影响匹配值集时，将产生错误。</span><span class="sxs-lookup"><span data-stu-id="5b528-205">I suggest we should aim to produce an error whenever some conjunct or disjunct of a compound pattern does not either define a pattern variable or affect the set of matched values.</span></span>

``` csharp
case < 2: break;
case 0 or 1 or 2 or 3 or 4 or 5: break;
```

<span data-ttu-id="5b528-206">此处，不向 `0 or 1 or` 第二种情况添加任何内容，因为这些值已被第一种情况处理。</span><span class="sxs-lookup"><span data-stu-id="5b528-206">Here, `0 or 1 or` adds nothing to the second case, as those values would have been handled by the first case.</span></span>  <span data-ttu-id="5b528-207">这也是一个错误。</span><span class="sxs-lookup"><span data-stu-id="5b528-207">This too deserves an error.</span></span>

``` csharp
byte b = ...;
int x = b switch { <100 => 0, 100 => 1, 101 => 2, >101 => 3 };
```

<span data-ttu-id="5b528-208">诸如这样的 switch 表达式应被视为 *详尽* 的 (它处理) 的所有可能输入值。</span><span class="sxs-lookup"><span data-stu-id="5b528-208">A switch expression such as this should be considered *exhaustive* (it handles all possible input values).</span></span>

<span data-ttu-id="5b528-209">在 c # 8.0 中，如果具有类型的输入的 switch 表达式 `byte` 包含的最终 arm 的模式与 *丢弃模式* 或 *var 模式*)  (，则该表达式将被视为穷举。</span><span class="sxs-lookup"><span data-stu-id="5b528-209">In C# 8.0, a switch expression with an input of type `byte` is only considered exhaustive if it contains a final arm whose pattern matches everything (a *discard-pattern* or *var-pattern*).</span></span>  <span data-ttu-id="5b528-210">即使是在 c # 8 中，对每个非重复值具有 arm 的 switch 表达式也 `byte` 不会被视为详尽。</span><span class="sxs-lookup"><span data-stu-id="5b528-210">Even a switch expression that has an arm for every distinct `byte` value is not considered exhaustive in C# 8.</span></span>  <span data-ttu-id="5b528-211">为了正确地处理关系模式的 exhaustiveness，我们还需要处理这种情况。</span><span class="sxs-lookup"><span data-stu-id="5b528-211">In order to properly handle exhaustiveness of relational patterns, we will have to handle this case too.</span></span>  <span data-ttu-id="5b528-212">从技术上讲，这会是一项重大更改，但用户不可能注意到。</span><span class="sxs-lookup"><span data-stu-id="5b528-212">This will technically be a breaking change, but no user is likely to notice.</span></span>

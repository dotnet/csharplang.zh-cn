---
ms.openlocfilehash: 7ee72a2e3171940e93a2dc7048136a7ddc6b7698
ms.sourcegitcommit: 75ea1d741f92d6fda117caa189db17370f1b9f1c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/27/2020
ms.locfileid: "92684067"
---
# <a name="recursive-pattern-matching"></a><span data-ttu-id="76657-101">递归模式匹配</span><span class="sxs-lookup"><span data-stu-id="76657-101">Recursive Pattern Matching</span></span>

## <a name="summary"></a><span data-ttu-id="76657-102">摘要</span><span class="sxs-lookup"><span data-stu-id="76657-102">Summary</span></span>
[summary]: #summary

<span data-ttu-id="76657-103">C # 的模式匹配扩展可实现与功能语言的代数数据类型和模式匹配的许多优势，但这种方式与基础语言的外观顺畅集成。</span><span class="sxs-lookup"><span data-stu-id="76657-103">Pattern matching extensions for C# enable many of the benefits of algebraic data types and pattern matching from functional languages, but in a way that smoothly integrates with the feel of the underlying language.</span></span> <span data-ttu-id="76657-104">此方法的元素通过编程语言 [F #](http://www.msr-waypoint.net/pubs/79947/p29-syme.pdf "通过轻型语言进行的可扩展模式匹配") 和 [Scala](https://link.springer.com/content/pdf/10.1007%2F978-3-540-73589-2.pdf "与模式匹配的对象，页273")中的相关功能来实现。</span><span class="sxs-lookup"><span data-stu-id="76657-104">Elements of this approach are inspired by related features in the programming languages [F#](http://www.msr-waypoint.net/pubs/79947/p29-syme.pdf "Extensible Pattern Matching Via a Lightweight Language") and [Scala](https://link.springer.com/content/pdf/10.1007%2F978-3-540-73589-2.pdf "Matching Objects With Patterns, page 273").</span></span>

## <a name="detailed-design"></a><span data-ttu-id="76657-105">详细设计</span><span class="sxs-lookup"><span data-stu-id="76657-105">Detailed design</span></span>
[design]: #detailed-design

### <a name="is-expression"></a><span data-ttu-id="76657-106">为 Expression</span><span class="sxs-lookup"><span data-stu-id="76657-106">Is Expression</span></span>

<span data-ttu-id="76657-107">`is`扩展运算符以针对 *模式* 测试表达式。</span><span class="sxs-lookup"><span data-stu-id="76657-107">The `is` operator is extended to test an expression against a *pattern* .</span></span>

```antlr
relational_expression
    : is_pattern_expression
    ;
is_pattern_expression
    : relational_expression 'is' pattern
    ;
```

<span data-ttu-id="76657-108">这种形式的 *relational_expression* 除了 c # 规范中的现有窗体外。</span><span class="sxs-lookup"><span data-stu-id="76657-108">This form of *relational_expression* is in addition to the existing forms in the C# specification.</span></span> <span data-ttu-id="76657-109">如果标记左侧的 *relational_expression* `is` 未指定值或没有类型，则会发生编译时错误。</span><span class="sxs-lookup"><span data-stu-id="76657-109">It is a compile-time error if the *relational_expression* to the left of the `is` token does not designate a value or does not have a type.</span></span>

<span data-ttu-id="76657-110">该模式的每个 *标识符* 都引入一个新的局部变量，该局部变量在运算符 (后是 *明确* 赋值的， `is` `true` 即 *在) true 时明确赋值* 。</span><span class="sxs-lookup"><span data-stu-id="76657-110">Every *identifier* of the pattern introduces a new local variable that is *definitely assigned* after the `is` operator is `true` (i.e. *definitely assigned when true* ).</span></span>

> <span data-ttu-id="76657-111">注意：在技术上，和 constant_pattern 中的 *类型* 之间存在二义性 `is-expression` ，其中可能是 *constant_pattern* 限定标识符的有效分析。</span><span class="sxs-lookup"><span data-stu-id="76657-111">Note: There is technically an ambiguity between *type* in an `is-expression` and *constant_pattern* , either of which might be a valid parse of a qualified identifier.</span></span> <span data-ttu-id="76657-112">我们尝试将其绑定为类型以与以前版本的语言兼容;只有当这种情况失败时，我们才会解决此问题，因为我们在其他上下文中执行了表达式，这 (必须是常量或) 类型。</span><span class="sxs-lookup"><span data-stu-id="76657-112">We try to bind it as a type for compatibility with previous versions of the language; only if that fails do we resolve it as we do an expression in other contexts, to the first thing found (which must be either a constant or a type).</span></span> <span data-ttu-id="76657-113">这种多义性仅出现在表达式的右侧 `is` 。</span><span class="sxs-lookup"><span data-stu-id="76657-113">This ambiguity is only present on the right-hand-side of an `is` expression.</span></span>

### <a name="patterns"></a><span data-ttu-id="76657-114">模式</span><span class="sxs-lookup"><span data-stu-id="76657-114">Patterns</span></span>

<span data-ttu-id="76657-115">在 *is_pattern* 运算符、 *switch_statement* 和 *switch_expression* 中使用模式来表示数据的形状，而在这种情况下，将对其调用输入值) 的传入 (数据进行比较。</span><span class="sxs-lookup"><span data-stu-id="76657-115">Patterns are used in the *is_pattern* operator, in a *switch_statement* , and in a *switch_expression* to express the shape of data against which incoming data  (which we call the input value) is to be compared.</span></span> <span data-ttu-id="76657-116">模式可以是递归的，以便可以将部分数据与子模式进行匹配。</span><span class="sxs-lookup"><span data-stu-id="76657-116">Patterns may be recursive so that parts of the data may be matched against sub-patterns.</span></span>

```antlr
pattern
    : declaration_pattern
    | constant_pattern
    | var_pattern
    | positional_pattern
    | property_pattern
    | discard_pattern
    ;
declaration_pattern
    : type simple_designation
    ;
constant_pattern
    : constant_expression
    ;
var_pattern
    : 'var' designation
    ;
positional_pattern
    : type? '(' subpatterns? ')' property_subpattern? simple_designation?
    ;
subpatterns
    : subpattern
    | subpattern ',' subpatterns
    ;
subpattern
    : pattern
    | identifier ':' pattern
    ;
property_subpattern
    : '{' '}'
    | '{' subpatterns ','? '}'
    ;
property_pattern
    : type? property_subpattern simple_designation?
    ;
simple_designation
    : single_variable_designation
    | discard_designation
    ;
discard_pattern
    : '_'
    ;
```

#### <a name="declaration-pattern"></a><span data-ttu-id="76657-117">声明模式</span><span class="sxs-lookup"><span data-stu-id="76657-117">Declaration Pattern</span></span>

```antlr
declaration_pattern
    : type simple_designation
    ;
```

<span data-ttu-id="76657-118">如果测试成功，则 *declaration_pattern* 两个测试表达式是否为给定的类型并将其转换为该类型。</span><span class="sxs-lookup"><span data-stu-id="76657-118">The *declaration_pattern* both tests that an expression is of a given type and casts it to that type if the test succeeds.</span></span> <span data-ttu-id="76657-119">如果指定的是 *single_variable_designation* ，则这可能会引入给定标识符命名的给定类型的局部变量。</span><span class="sxs-lookup"><span data-stu-id="76657-119">This may introduce a local variable of the given type named by the given identifier, if the designation is a *single_variable_designation* .</span></span> <span data-ttu-id="76657-120">在模式匹配操作的结果为时，该局部变量是 *明确赋值* 的 `true` 。</span><span class="sxs-lookup"><span data-stu-id="76657-120">That local variable is *definitely assigned* when the result of the pattern-matching operation is `true`.</span></span>

<span data-ttu-id="76657-121">此表达式的运行时语义是根据模式中的 *类型* 测试左侧 *relational_expression* 操作数的运行时类型。</span><span class="sxs-lookup"><span data-stu-id="76657-121">The runtime semantic of this expression is that it tests the runtime type of the left-hand *relational_expression* operand against the *type* in the pattern.</span></span>  <span data-ttu-id="76657-122">如果它属于此运行时类型 (或某些子类型) 而不是，则的 `null` 结果 `is operator` 为 `true` 。</span><span class="sxs-lookup"><span data-stu-id="76657-122">If it is of that runtime type (or some subtype) and not `null`, the result of the `is operator` is `true`.</span></span>

<span data-ttu-id="76657-123">左侧和给定类型的静态类型的某些组合被视为不兼容并导致编译时错误。</span><span class="sxs-lookup"><span data-stu-id="76657-123">Certain combinations of static type of the left-hand-side and the given type are considered incompatible and result in compile-time error.</span></span> <span data-ttu-id="76657-124">`E` *pattern-compatible* `T` 如果存在标识转换、隐式引用转换、装箱转换、显式引用转换或从到的取消装箱转换， `E` `T` 或者其中一个类型是开放类型，则将静态类型的值称为模式与类型兼容。</span><span class="sxs-lookup"><span data-stu-id="76657-124">A value of static type `E` is said to be *pattern-compatible* with a type `T` if there exists an identity conversion, an implicit reference conversion, a boxing conversion, an explicit reference conversion, or an unboxing conversion from `E` to `T`, or if one of those types is an open type.</span></span> <span data-ttu-id="76657-125">如果类型的输入 `E` 不与它所匹配的类型模式中的 *类型\*\*模式兼容* ，则会发生编译时错误。</span><span class="sxs-lookup"><span data-stu-id="76657-125">It is a compile-time error if an input of type `E` is not *pattern-compatible* with the *type* in a type pattern that it is matched with.</span></span>

<span data-ttu-id="76657-126">类型模式对于执行引用类型的运行时类型测试非常有用，并且替换了方法</span><span class="sxs-lookup"><span data-stu-id="76657-126">The type pattern is useful for performing run-time type tests of reference types, and replaces the idiom</span></span>

```csharp
var v = expr as Type;
if (v != null) { // code using v
```

<span data-ttu-id="76657-127">稍微简单一些</span><span class="sxs-lookup"><span data-stu-id="76657-127">With the slightly more concise</span></span>

```csharp
if (expr is Type v) { // code using v
```

<span data-ttu-id="76657-128">如果 *类型* 是可以为 null 的值类型，则是错误的。</span><span class="sxs-lookup"><span data-stu-id="76657-128">It is an error if *type* is a nullable value type.</span></span>

<span data-ttu-id="76657-129">Type 模式可用于测试可为 null 的类型的值： `Nullable<T>` `T` `T2 id` 如果值为非 null，并且的类型 `T2` 为 `T` 或的某个基类型或接口， `T` 则类型为 (或装箱) 类型的值匹配。</span><span class="sxs-lookup"><span data-stu-id="76657-129">The type pattern can be used to test values of nullable types: a value of type `Nullable<T>` (or a boxed `T`) matches a type pattern `T2 id` if the value is non-null and the type of `T2` is `T`, or some base type or interface of `T`.</span></span> <span data-ttu-id="76657-130">例如，在代码片段中</span><span class="sxs-lookup"><span data-stu-id="76657-130">For example, in the code fragment</span></span>

```csharp
int? x = 3;
if (x is int v) { // code using v
```

<span data-ttu-id="76657-131">语句的条件 `if` 是 `true` 在运行时，变量在 `v` `3` 块内保存类型的值 `int` 。</span><span class="sxs-lookup"><span data-stu-id="76657-131">The condition of the `if` statement is `true` at runtime and the variable `v` holds the value `3` of type `int` inside the block.</span></span> <span data-ttu-id="76657-132">块后，变量 `v` 处于范围内但未明确赋值。</span><span class="sxs-lookup"><span data-stu-id="76657-132">After the block the variable `v` is in scope but not definitely assigned.</span></span>

#### <a name="constant-pattern"></a><span data-ttu-id="76657-133">常量模式</span><span class="sxs-lookup"><span data-stu-id="76657-133">Constant Pattern</span></span>

```antlr
constant_pattern
    : constant_expression
    ;
```

<span data-ttu-id="76657-134">常数模式根据常数值测试表达式的值。</span><span class="sxs-lookup"><span data-stu-id="76657-134">A constant pattern tests the value of an expression against a constant value.</span></span> <span data-ttu-id="76657-135">常数可以是任何常数表达式，如文本、声明的变量的名称 `const` 或枚举常量。</span><span class="sxs-lookup"><span data-stu-id="76657-135">The constant may be any constant expression, such as a literal, the name of a declared `const` variable, or an enumeration constant.</span></span> <span data-ttu-id="76657-136">如果输入值不是开放类型，则常量表达式将隐式转换为匹配的表达式的类型;如果输入值的类型与常量表达式的类型不 *兼容* ，则模式匹配操作为错误。</span><span class="sxs-lookup"><span data-stu-id="76657-136">When the input value is not an open type, the constant expression is implicitly converted to the type of the matched expression; if the type of the input value is not *pattern-compatible* with the type of the constant expression, the pattern-matching operation is an error.</span></span>

<span data-ttu-id="76657-137">如果返回，模式 *c* 被视为与转换后的输入值 *e* 匹配 `object.Equals(c, e)` `true` 。</span><span class="sxs-lookup"><span data-stu-id="76657-137">The pattern *c* is considered matching the converted input value *e* if `object.Equals(c, e)` would return `true`.</span></span>

<span data-ttu-id="76657-138">`e is null`在新编写的代码中，我们希望看到最常见的测试方法 `null` ，因为它无法调用用户定义的代码 `operator==` 。</span><span class="sxs-lookup"><span data-stu-id="76657-138">We expect to see `e is null` as the most common way to test for `null` in newly written code, as it cannot invoke a user-defined `operator==`.</span></span>

#### <a name="var-pattern"></a><span data-ttu-id="76657-139">Var 模式</span><span class="sxs-lookup"><span data-stu-id="76657-139">Var Pattern</span></span>

```antlr
var_pattern
    : 'var' designation
    ;
designation
    : simple_designation
    | tuple_designation
    ;
simple_designation
    : single_variable_designation
    | discard_designation
    ;
single_variable_designation
    : identifier
    ;
discard_designation
    : _
    ;
tuple_designation
    : '(' designations? ')'
    ;
designations
    : designation
    | designations ',' designation
    ;
```

<span data-ttu-id="76657-140">如果 *指定* 为 *simple_designation* ，则表达式 *e* 与模式匹配。</span><span class="sxs-lookup"><span data-stu-id="76657-140">If the *designation* is a *simple_designation* , an expression *e* matches the pattern.</span></span> <span data-ttu-id="76657-141">换言之，与 *var 模式* 的匹配始终会成功，并提供 *simple_designation* 。</span><span class="sxs-lookup"><span data-stu-id="76657-141">In other words, a match to a *var pattern* always succeeds with a *simple_designation* .</span></span> <span data-ttu-id="76657-142">如果 *simple_designation* 为 *single_variable_designation* ，则 *e* 的值将绑定到新引入的局部变量。</span><span class="sxs-lookup"><span data-stu-id="76657-142">If the *simple_designation* is a *single_variable_designation* , the value of *e* is bounds to a newly introduced local variable.</span></span> <span data-ttu-id="76657-143">局部变量的类型为 *e* 的静态类型。</span><span class="sxs-lookup"><span data-stu-id="76657-143">The type of the local variable is the static type of *e* .</span></span>

<span data-ttu-id="76657-144">如果 *指定* 的是 *tuple_designation* ，则该模式等效于 "格式指定 ..." 的 *positional_pattern* `(var` *designation* ， `)` 其中， *指定* 是在 *tuple_designation* 中找到的。</span><span class="sxs-lookup"><span data-stu-id="76657-144">If the *designation* is a *tuple_designation* , then the pattern is equivalent to a *positional_pattern* of the form `(var` *designation* , ... `)` where the *designation* s are those found within the *tuple_designation* .</span></span>  <span data-ttu-id="76657-145">例如，模式与 `var (x, (y, z))` 等效 `(var x, (var y, var z))` 。</span><span class="sxs-lookup"><span data-stu-id="76657-145">For example, the pattern `var (x, (y, z))` is equivalent to `(var x, (var y, var z))`.</span></span>

<span data-ttu-id="76657-146">如果名称绑定到类型，则是错误的 `var` 。</span><span class="sxs-lookup"><span data-stu-id="76657-146">It is an error if the name `var` binds to a type.</span></span>

#### <a name="discard-pattern"></a><span data-ttu-id="76657-147">丢弃模式</span><span class="sxs-lookup"><span data-stu-id="76657-147">Discard Pattern</span></span>

```antlr
discard_pattern
    : '_'
    ;
```

<span data-ttu-id="76657-148">表达式 *e* 始终匹配模式 `_` 。</span><span class="sxs-lookup"><span data-stu-id="76657-148">An expression *e* matches the pattern `_` always.</span></span> <span data-ttu-id="76657-149">换言之，每个表达式都与丢弃模式匹配。</span><span class="sxs-lookup"><span data-stu-id="76657-149">In other words, every expression matches the discard pattern.</span></span>

<span data-ttu-id="76657-150">丢弃模式不能用作 *is_pattern_expression* 模式。</span><span class="sxs-lookup"><span data-stu-id="76657-150">A discard pattern may not be used as the pattern of an *is_pattern_expression* .</span></span>

#### <a name="positional-pattern"></a><span data-ttu-id="76657-151">位置模式</span><span class="sxs-lookup"><span data-stu-id="76657-151">Positional Pattern</span></span>

<span data-ttu-id="76657-152">位置模式检查输入值是否不是 `null` ，调用适当的 `Deconstruct` 方法，并对生成的值执行进一步的模式匹配。</span><span class="sxs-lookup"><span data-stu-id="76657-152">A positional pattern checks that the input value is not `null`, invokes an appropriate `Deconstruct` method, and performs further pattern matching on the resulting values.</span></span>  <span data-ttu-id="76657-153">如果输入值的类型与包含的类型相同，或者输入值的类型是元组类型，或者输入值的类型为 `Deconstruct` `object` 或 `ITuple` 且表达式的运行时类型为或，则它还支持类似元组的模式语法)  (`ITuple` 。</span><span class="sxs-lookup"><span data-stu-id="76657-153">It also supports a tuple-like pattern syntax (without the type being provided) when the type of the input value is the same as the type containing `Deconstruct`, or if the type of the input value is a tuple type, or if the type of the input value is `object` or `ITuple` and the runtime type of the expression implements `ITuple`.</span></span>

```antlr
positional_pattern
    : type? '(' subpatterns? ')' property_subpattern? simple_designation?
    ;
subpatterns
    : subpattern
    | subpattern ',' subpatterns
    ;
subpattern
    : pattern
    | identifier ':' pattern
    ;
```

<span data-ttu-id="76657-154">如果省略该 *类型* ，则会将其作为输入值的静态类型。</span><span class="sxs-lookup"><span data-stu-id="76657-154">If the *type* is omitted, we take it to be the static type of the input value.</span></span>

<span data-ttu-id="76657-155">给定模式 *类型* subpattern_list 的输入值匹配 `(` *subpattern_list* `)` 时，将通过在 *类型* 中搜索 `Deconstruct` ，并使用与析构声明相同的规则在其中选择一个方法，从而选择一个方法。</span><span class="sxs-lookup"><span data-stu-id="76657-155">Given a match of an input value to the pattern *type* `(` *subpattern_list* `)`, a method is selected by searching in *type* for accessible declarations of `Deconstruct` and selecting one among them using the same rules as for the deconstruction declaration.</span></span>

<span data-ttu-id="76657-156">如果 *positional_pattern* 省略了类型，具有单个无 *标识符* 的子 *模式* ，则没有任何 *property_subpattern* ，并且没有 *simple_designation* 。</span><span class="sxs-lookup"><span data-stu-id="76657-156">It is an error if a *positional_pattern* omits the type, has a single *subpattern* without an *identifier* , has no *property_subpattern* and has no *simple_designation* .</span></span> <span data-ttu-id="76657-157">此消除是带圆括号和 *positional_pattern* 的 *constant_pattern* 之间的。</span><span class="sxs-lookup"><span data-stu-id="76657-157">This disambiguates between a *constant_pattern* that is parenthesized and a *positional_pattern* .</span></span>

<span data-ttu-id="76657-158">为了提取要与列表中的模式匹配的值，</span><span class="sxs-lookup"><span data-stu-id="76657-158">In order to extract the values to match against the patterns in the list,</span></span>
- <span data-ttu-id="76657-159">如果省略了 *type* 并且输入值的类型是元组类型，则 subpatterns 的数目需要与该元组的基数相同。</span><span class="sxs-lookup"><span data-stu-id="76657-159">If *type* was omitted and the input value's type is a tuple type, then the number of subpatterns is required to be the same as the cardinality of the tuple.</span></span> <span data-ttu-id="76657-160">每个元组元素与相应的子 *模式* 相匹配，如果所有这些元组元素都成功，则匹配成功。</span><span class="sxs-lookup"><span data-stu-id="76657-160">Each tuple element is matched against the corresponding *subpattern* , and the match succeeds if all of these succeed.</span></span> <span data-ttu-id="76657-161">如果任何子 *模式* 包含 *标识符* ，则必须在元组类型中的相应位置命名元组元素。</span><span class="sxs-lookup"><span data-stu-id="76657-161">If any *subpattern* has an *identifier* , then that must name a tuple element at the corresponding position in the tuple type.</span></span>
- <span data-ttu-id="76657-162">否则，如果适当的 `Deconstruct` 作为 *类型* 的成员存在，则在输入值的类型与 *类型* 不 *兼容* 的情况下，会发生编译时错误。</span><span class="sxs-lookup"><span data-stu-id="76657-162">Otherwise, if a suitable `Deconstruct` exists as a member of *type* , it is a compile-time error if the type of the input value is not *pattern-compatible* with *type* .</span></span> <span data-ttu-id="76657-163">在运行时，将根据 *类型* 测试输入值。</span><span class="sxs-lookup"><span data-stu-id="76657-163">At runtime the input value is tested against *type* .</span></span> <span data-ttu-id="76657-164">如果此操作失败，则位置模式匹配失败。</span><span class="sxs-lookup"><span data-stu-id="76657-164">If this fails then the positional pattern match fails.</span></span> <span data-ttu-id="76657-165">如果成功，则会将输入值转换为此类型，并 `Deconstruct` 通过全新的编译器生成的变量调用来接收 `out` 参数。</span><span class="sxs-lookup"><span data-stu-id="76657-165">If it succeeds,  the input value is converted to this type and `Deconstruct` is invoked with fresh compiler-generated variables to receive the `out` parameters.</span></span> <span data-ttu-id="76657-166">收到的每个值都与相应的子 *模式* 相匹配，如果所有这些值都成功，则匹配成功。</span><span class="sxs-lookup"><span data-stu-id="76657-166">Each value that was received is matched against the corresponding *subpattern* , and the match succeeds if all of these succeed.</span></span> <span data-ttu-id="76657-167">如果任何子 *模式* 包含 *标识符* ，则必须在相应的位置为参数命名 `Deconstruct` 。</span><span class="sxs-lookup"><span data-stu-id="76657-167">If any *subpattern* has an *identifier* , then that must name a parameter at the corresponding position of `Deconstruct`.</span></span>
- <span data-ttu-id="76657-168">否则，如果省略了 *type* ，并且输入值的类型为 `object` 或， `ITuple` 或者是可通过隐式引用转换转换为的某种类型， `ITuple` 则在 subpatterns 中不会显示任何 *标识符* ，因此，我们将使用进行匹配 `ITuple` 。</span><span class="sxs-lookup"><span data-stu-id="76657-168">Otherwise if *type* was omitted, and the input value is of type `object` or `ITuple` or some type that can be converted to `ITuple` by an implicit reference conversion, and no *identifier* appears among the subpatterns, then we match using `ITuple`.</span></span>
- <span data-ttu-id="76657-169">否则，模式为编译时错误。</span><span class="sxs-lookup"><span data-stu-id="76657-169">Otherwise the pattern is a compile-time error.</span></span>

<span data-ttu-id="76657-170">Subpatterns 在运行时匹配的顺序未指定，并且失败的匹配可能不会尝试匹配所有 subpatterns。</span><span class="sxs-lookup"><span data-stu-id="76657-170">The order in which subpatterns are matched at runtime is unspecified, and a failed match may not attempt to match all subpatterns.</span></span>

##### <a name="example"></a><span data-ttu-id="76657-171">示例</span><span class="sxs-lookup"><span data-stu-id="76657-171">Example</span></span>

<span data-ttu-id="76657-172">此示例使用此规范中所述的许多功能</span><span class="sxs-lookup"><span data-stu-id="76657-172">This example uses many of the features described in this specification</span></span>

``` c#
    var newState = (GetState(), action, hasKey) switch {
        (DoorState.Closed, Action.Open, _) => DoorState.Opened,
        (DoorState.Opened, Action.Close, _) => DoorState.Closed,
        (DoorState.Closed, Action.Lock, true) => DoorState.Locked,
        (DoorState.Locked, Action.Unlock, true) => DoorState.Closed,
        (var state, _, _) => state };
```

#### <a name="property-pattern"></a><span data-ttu-id="76657-173">属性模式</span><span class="sxs-lookup"><span data-stu-id="76657-173">Property Pattern</span></span>

<span data-ttu-id="76657-174">属性模式检查输入的值是否不是 `null` ，并以递归方式匹配通过使用可访问的属性或字段提取的值。</span><span class="sxs-lookup"><span data-stu-id="76657-174">A property pattern checks that the input value is not `null` and recursively matches values extracted by the use of accessible properties or fields.</span></span>

```antlr
property_pattern
    : type? property_subpattern simple_designation?
    ;
property_subpattern
    : '{' '}'
    | '{' subpatterns ','? '}'
    ;
```

<span data-ttu-id="76657-175">如果 _property_pattern_ 的任何子 _模式_ 不包含 _标识符_ (它必须为第二种形式，而该窗体具有 _标识符_ ) ，则是错误的。</span><span class="sxs-lookup"><span data-stu-id="76657-175">It is an error if any _subpattern_ of a _property_pattern_ does not contain an _identifier_ (it must be of the second form, which has an _identifier_ ).</span></span>  <span data-ttu-id="76657-176">最后一个子模式后的尾随逗号是可选的。</span><span class="sxs-lookup"><span data-stu-id="76657-176">A trailing comma after the last subpattern is optional.</span></span>

<span data-ttu-id="76657-177">请注意，空检查模式不太常用。</span><span class="sxs-lookup"><span data-stu-id="76657-177">Note that a null-checking pattern falls out of a trivial property pattern.</span></span> <span data-ttu-id="76657-178">若要检查字符串 `s` 是否为非 null，可以编写以下任何形式</span><span class="sxs-lookup"><span data-stu-id="76657-178">To check if the string `s` is non-null, you can write any of the following forms</span></span>

```csharp
if (s is object o) ... // o is of type object
if (s is string x) ... // x is of type string
if (s is {} x) ... // x is of type string
if (s is {}) ...
```

<span data-ttu-id="76657-179">假设 *表达式 e 与* *type* `{` *property_pattern_list* `}` *类型* T 指定的类型 *T* 的 *模式不兼容* ，则在给定模式类型 property_pattern_list 的情况 *e* 下，它是编译时错误。</span><span class="sxs-lookup"><span data-stu-id="76657-179">Given a match of an expression *e* to the pattern *type* `{` *property_pattern_list* `}`, it is a compile-time error if the expression *e* is not *pattern-compatible* with the type *T* designated by *type* .</span></span> <span data-ttu-id="76657-180">如果该类型不存在，则会将其作为 *e* 的静态类型。</span><span class="sxs-lookup"><span data-stu-id="76657-180">If the type is absent, we take it to be the static type of *e* .</span></span> <span data-ttu-id="76657-181">如果 *标识符* 存在，则 *声明类型为* 的模式变量。</span><span class="sxs-lookup"><span data-stu-id="76657-181">If the *identifier* is present, it declares a pattern variable of type *type* .</span></span> <span data-ttu-id="76657-182">显示在其 *property_pattern_list* 左侧的每个标识符都必须指定可访问的可读属性或 *T* 的字段。如果 *property_pattern* 的 *simple_designation* 存在，它将定义类型为 *T* 的模式变量。</span><span class="sxs-lookup"><span data-stu-id="76657-182">Each of the identifiers appearing on the left-hand-side of its *property_pattern_list* must designate an accessible readable property or field of *T* . If the *simple_designation* of the *property_pattern* is present, it defines a pattern variable of type *T* .</span></span>

<span data-ttu-id="76657-183">在运行时，将根据 *T* 对表达式进行测试。如果此操作失败，则属性模式匹配失败，结果为 `false` 。</span><span class="sxs-lookup"><span data-stu-id="76657-183">At runtime, the expression is tested against *T* . If this fails then the property pattern match fails and the result is `false`.</span></span> <span data-ttu-id="76657-184">如果成功，则将读取每个 *property_subpattern* 字段或属性，并将其值与其对应的模式相匹配。</span><span class="sxs-lookup"><span data-stu-id="76657-184">If it succeeds, then each *property_subpattern* field or property is read and its value matched against its corresponding pattern.</span></span> <span data-ttu-id="76657-185">整个匹配的结果仅适用于 `false` 其中任何一个的结果 `false` 。</span><span class="sxs-lookup"><span data-stu-id="76657-185">The result of the whole match is `false` only if the result of any of these is `false`.</span></span> <span data-ttu-id="76657-186">未指定 subpatterns 匹配的顺序，并且失败的匹配可能与运行时的所有 subpatterns 都不匹配。</span><span class="sxs-lookup"><span data-stu-id="76657-186">The order in which subpatterns are matched is not specified, and a failed match may not match all subpatterns at runtime.</span></span> <span data-ttu-id="76657-187">如果匹配成功并且 *property_pattern* 的 *simple_designation* 为 *single_variable_designation* ，则它会定义一个类型为 *T* 的变量，该变量被分配了匹配的值。</span><span class="sxs-lookup"><span data-stu-id="76657-187">If the match succeeds and the *simple_designation* of the *property_pattern* is a *single_variable_designation* , it defines a variable of type *T* that is assigned the matched value.</span></span>

> <span data-ttu-id="76657-188">注意：属性模式可用于与匿名类型匹配。</span><span class="sxs-lookup"><span data-stu-id="76657-188">Note: The property pattern can be used to pattern-match with anonymous types.</span></span>

##### <a name="example"></a><span data-ttu-id="76657-189">示例</span><span class="sxs-lookup"><span data-stu-id="76657-189">Example</span></span>

```csharp
if (o is string { Length: 5 } s)
```

### <a name="switch-expression"></a><span data-ttu-id="76657-190">Switch 表达式</span><span class="sxs-lookup"><span data-stu-id="76657-190">Switch Expression</span></span>

<span data-ttu-id="76657-191">将 *switch_expression* 添加到 `switch` 表达式上下文的支持类似语义。</span><span class="sxs-lookup"><span data-stu-id="76657-191">A *switch_expression* is added to support `switch`-like semantics for an expression context.</span></span>

<span data-ttu-id="76657-192">C # 语言语法增加了以下语法：</span><span class="sxs-lookup"><span data-stu-id="76657-192">The C# language syntax is augmented with the following syntactic productions:</span></span>

```antlr
multiplicative_expression
    : switch_expression
    | multiplicative_expression '*' switch_expression
    | multiplicative_expression '/' switch_expression
    | multiplicative_expression '%' switch_expression
    ;
switch_expression
    : range_expression 'switch' '{' '}'
    | range_expression 'switch' '{' switch_expression_arms ','? '}'
    ;
switch_expression_arms
    : switch_expression_arm
    | switch_expression_arms ',' switch_expression_arm
    ;
switch_expression_arm
    : pattern case_guard? '=>' expression
    ;
case_guard
    : 'when' null_coalescing_expression
    ;
```

<span data-ttu-id="76657-193">*Switch_expression* 不允许作为 *expression_statement* 。</span><span class="sxs-lookup"><span data-stu-id="76657-193">The *switch_expression* is not permitted as an *expression_statement* .</span></span>

> <span data-ttu-id="76657-194">在未来的版本中，我们正在考虑这种情况。</span><span class="sxs-lookup"><span data-stu-id="76657-194">We are looking at relaxing this in a future revision.</span></span>

<span data-ttu-id="76657-195">*Switch_expression* 的类型是出现在 switch_expression_arm s 的标记右侧的表达式的 [*最佳通用类型*](https://github.com/dotnet/csharplang/blob/master/spec/expressions.md#finding-the-best-common-type-of-a-set-of-expressions) `=>` （如果存在此类 *switch_expression_arm* 类型），并且 switch 表达式的每个 arm 中的表达式都可以隐式转换为该类型。</span><span class="sxs-lookup"><span data-stu-id="76657-195">The type of the *switch_expression* is the [*best common type*](https://github.com/dotnet/csharplang/blob/master/spec/expressions.md#finding-the-best-common-type-of-a-set-of-expressions) of the expressions appearing to the right of the `=>` tokens of the *switch_expression_arm* s if such a type exists and the expression in every arm of the switch expression can be implicitly converted to that type.</span></span>  <span data-ttu-id="76657-196">此外，我们还添加了一个新的 *switch expression 转换* ，该转换是一种预定义的隐式转换，它是从 switch 表达式到每个类型的预定义隐式转换 `T` `T` 。</span><span class="sxs-lookup"><span data-stu-id="76657-196">In addition, we add a new *switch expression conversion* , which is a predefined implicit conversion from a switch expression to every type `T` for which there exists an implicit conversion from each arm's expression to `T`.</span></span>

<span data-ttu-id="76657-197">如果某些 *switch_expression_arm* 模式不会影响结果，则是错误的，因为某些以前的模式和临界将始终匹配。</span><span class="sxs-lookup"><span data-stu-id="76657-197">It is an error if some *switch_expression_arm* 's pattern cannot affect the result because some previous pattern and guard will always match.</span></span>

<span data-ttu-id="76657-198">如果 switch 表达式的某些 arm 处理其输入的每个值 *，则可以* 使用 switch 表达式。</span><span class="sxs-lookup"><span data-stu-id="76657-198">A switch expression is said to be *exhaustive* if some arm of the switch expression handles every value of its input.</span></span>  <span data-ttu-id="76657-199">如果 switch 表达式并非 *详尽* ，则编译器将生成警告。</span><span class="sxs-lookup"><span data-stu-id="76657-199">The compiler shall produce a warning if a switch expression is not *exhaustive* .</span></span>

<span data-ttu-id="76657-200">在运行时， *switch_expression* 的结果是第一个 *switch_expression_arm* 的 *表达式* 的值， *switch_expression* 的左侧的表达式与 *switch_expression_arm* 的模式匹配，并且 *case_guard* 的 switch_expression_arm （如果存在 *）的计算* 结果为 `true` 。</span><span class="sxs-lookup"><span data-stu-id="76657-200">At runtime, the result of the *switch_expression* is the value of the *expression* of the first *switch_expression_arm* for which the expression on the left-hand-side of the *switch_expression* matches the *switch_expression_arm* 's pattern, and for which the *case_guard* of the *switch_expression_arm* , if present, evaluates to `true`.</span></span> <span data-ttu-id="76657-201">如果没有这样的 *switch_expression_arm* ，则 *switch_expression* 会引发异常的实例 `System.Runtime.CompilerServices.SwitchExpressionException` 。</span><span class="sxs-lookup"><span data-stu-id="76657-201">If there is no such *switch_expression_arm* , the *switch_expression* throws an instance of the exception `System.Runtime.CompilerServices.SwitchExpressionException`.</span></span>

### <a name="optional-parens-when-switching-on-a-tuple-literal"></a><span data-ttu-id="76657-202">切换元组文本时的可选括号</span><span class="sxs-lookup"><span data-stu-id="76657-202">Optional parens when switching on a tuple literal</span></span>

<span data-ttu-id="76657-203">若要使用 *switch_statement* 在元组文本上切换，你必须编写看似冗余的括号</span><span class="sxs-lookup"><span data-stu-id="76657-203">In order to switch on a tuple literal using the *switch_statement* , you have to write what appear to be redundant parens</span></span>

```csharp
switch ((a, b))
{
```

<span data-ttu-id="76657-204">允许</span><span class="sxs-lookup"><span data-stu-id="76657-204">To permit</span></span>

```csharp
switch (a, b)
{
```

<span data-ttu-id="76657-205">当要打开的表达式是元组文本时，switch 语句的括号是可选的。</span><span class="sxs-lookup"><span data-stu-id="76657-205">the parentheses of the switch statement are optional when the expression being switched on is a tuple literal.</span></span>

### <a name="order-of-evaluation-in-pattern-matching"></a><span data-ttu-id="76657-206">模式匹配中的求值顺序</span><span class="sxs-lookup"><span data-stu-id="76657-206">Order of evaluation in pattern-matching</span></span>

<span data-ttu-id="76657-207">为了更灵活地对模式匹配过程中执行的操作进行重新排序，可允许使用灵活性来提高模式匹配的效率。</span><span class="sxs-lookup"><span data-stu-id="76657-207">Giving the compiler flexibility in reordering the operations executed during pattern-matching can permit flexibility that can be used to improve the efficiency of pattern-matching.</span></span> <span data-ttu-id="76657-208"> (不强制执行的) 要求是在模式中访问的属性和析构方法，必须是 "纯" (副作用、幂等) 。</span><span class="sxs-lookup"><span data-stu-id="76657-208">The (unenforced) requirement would be that properties accessed in a pattern, and the Deconstruct methods, are required to be "pure" (side-effect free, idempotent, etc).</span></span> <span data-ttu-id="76657-209">这并不意味着我们会将纯度作为语言概念添加，只是我们允许编译器灵活地进行重新排序操作。</span><span class="sxs-lookup"><span data-stu-id="76657-209">That doesn't mean that we would add purity as a language concept, only that we would allow the compiler flexibility in reordering operations.</span></span>

<span data-ttu-id="76657-210">**解决方法 2018-04-04 LDM** ：已确认：允许编译器对中的方法的调用重新排序 `Deconstruct` 、属性访问和调用， `ITuple` 并且可能会假设返回的值与多个调用的值相同。</span><span class="sxs-lookup"><span data-stu-id="76657-210">**Resolution 2018-04-04 LDM** : confirmed: the compiler is permitted to reorder calls to `Deconstruct`, property accesses, and invocations of methods in `ITuple`, and may assume that returned values are the same from multiple calls.</span></span> <span data-ttu-id="76657-211">编译器不应调用无法影响结果的函数，在以后对编译器生成的计算顺序进行任何更改之前，我们将非常小心。</span><span class="sxs-lookup"><span data-stu-id="76657-211">The compiler should not invoke functions that cannot affect the result, and we will be very careful before making any changes to the compiler-generated order of evaluation in the future.</span></span>

### <a name="some-possible-optimizations"></a><span data-ttu-id="76657-212">一些可能的优化</span><span class="sxs-lookup"><span data-stu-id="76657-212">Some Possible Optimizations</span></span>

<span data-ttu-id="76657-213">模式匹配的编译可以利用模式的常见部分。</span><span class="sxs-lookup"><span data-stu-id="76657-213">The compilation of pattern matching can take advantage of common parts of patterns.</span></span> <span data-ttu-id="76657-214">例如，如果 *switch_statement* 中两个连续模式的顶级类型测试为同一类型，则生成的代码可以跳过第二个模式的类型测试。</span><span class="sxs-lookup"><span data-stu-id="76657-214">For example, if the top-level type test of two successive patterns in a *switch_statement* is the same type, the generated code can skip the type test for the second pattern.</span></span>

<span data-ttu-id="76657-215">当某些模式为整数或字符串时，编译器可以为该语言的早期版本中的 switch 语句生成相同类型的代码。</span><span class="sxs-lookup"><span data-stu-id="76657-215">When some of the patterns are integers or strings, the compiler can generate the same kind of code it generates for a switch-statement in earlier versions of the language.</span></span>

<span data-ttu-id="76657-216">有关这些类型的优化的详细信息，请参阅 [[Scott 和 Ramsey (2000) ]](https://www.cs.tufts.edu/~nr/cs257/archive/norman-ramsey/match.pdf "Match-Compilation 试探是否重要？")。</span><span class="sxs-lookup"><span data-stu-id="76657-216">For more on these kinds of optimizations, see [[Scott and Ramsey (2000)]](https://www.cs.tufts.edu/~nr/cs257/archive/norman-ramsey/match.pdf "When Do Match-Compilation Heuristics Matter?").</span></span>

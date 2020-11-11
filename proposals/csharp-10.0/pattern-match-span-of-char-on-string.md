---
ms.openlocfilehash: 02ebf35fc43fee1183be5d0118e03e02b17f41a9
ms.sourcegitcommit: 38908f71e529669d1e049db6c6cce2cad65c1727
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/11/2020
ms.locfileid: "94498291"
---
# <a name="pattern-match-spanchar-on-a-constant-string"></a><span data-ttu-id="ee283-101">常量字符串的模式匹配 `Span<char>`</span><span class="sxs-lookup"><span data-stu-id="ee283-101">Pattern match `Span<char>` on a constant string</span></span>

* <span data-ttu-id="ee283-102">[x] 建议</span><span class="sxs-lookup"><span data-stu-id="ee283-102">[x] Proposed</span></span>
* <span data-ttu-id="ee283-103">[x] 原型：已完成</span><span class="sxs-lookup"><span data-stu-id="ee283-103">[x] Prototype: Completed</span></span>
* <span data-ttu-id="ee283-104">[x] 实现：正在进行</span><span class="sxs-lookup"><span data-stu-id="ee283-104">[x] Implementation: In Progress</span></span>
* <span data-ttu-id="ee283-105">[] 规范：未启动</span><span class="sxs-lookup"><span data-stu-id="ee283-105">[ ] Specification: Not Started</span></span>

## <a name="summary"></a><span data-ttu-id="ee283-106">总结</span><span class="sxs-lookup"><span data-stu-id="ee283-106">Summary</span></span>
[summary]: #summary

<span data-ttu-id="ee283-107">允许 `Span<char>` 对常量字符串匹配和的模式 `ReadOnlySpan<char>` 。</span><span class="sxs-lookup"><span data-stu-id="ee283-107">Permit pattern matching a `Span<char>` and a `ReadOnlySpan<char>` on a constant string.</span></span>

## <a name="motivation"></a><span data-ttu-id="ee283-108">动机</span><span class="sxs-lookup"><span data-stu-id="ee283-108">Motivation</span></span>
[motivation]: #motivation

<span data-ttu-id="ee283-109">对于性能， `Span<char>` 在许多情况下，和的使用优先 `ReadOnlySpan<char>` 于字符串。</span><span class="sxs-lookup"><span data-stu-id="ee283-109">For perfomance, usage of `Span<char>` and `ReadOnlySpan<char>` is preferred over string in many scenarios.</span></span> <span data-ttu-id="ee283-110">框架添加了许多新的 Api，以允许你使用来 `ReadOnlySpan<char>` 替代 `string` 。</span><span class="sxs-lookup"><span data-stu-id="ee283-110">The framework has added many new APIs to allow you to use `ReadOnlySpan<char>` in place of a `string`.</span></span>

<span data-ttu-id="ee283-111">对字符串执行的常见操作是使用开关来测试它是否为特定值，并且编译器会优化此类开关。</span><span class="sxs-lookup"><span data-stu-id="ee283-111">A common operation on strings is to use a switch to test if it is a particular value, and the compiler optimizes such a switch.</span></span> <span data-ttu-id="ee283-112">不过，目前没有办法有效地执行相同的操作，而不是 `ReadOnlySpan<char>` 手动实现开关和优化。</span><span class="sxs-lookup"><span data-stu-id="ee283-112">However there is currently no way to do the same on a `ReadOnlySpan<char>` efficiently, other than implementing the switch and the optimization manually.</span></span>

<span data-ttu-id="ee283-113">为了鼓励采用 `ReadOnlySpan<char>` ，我们允许在一个常数上对进行模式匹配 `ReadOnlySpan<char>` ， `string` 因此还允许在开关中使用它。</span><span class="sxs-lookup"><span data-stu-id="ee283-113">In order to encourage adoption of `ReadOnlySpan<char>` we allow pattern matching a `ReadOnlySpan<char>`, on a constant `string`, thus also allowing it to be used in a switch.</span></span>

## <a name="detailed-design"></a><span data-ttu-id="ee283-114">详细设计</span><span class="sxs-lookup"><span data-stu-id="ee283-114">Detailed design</span></span>
[design]: #detailed-design

<span data-ttu-id="ee283-115">我们按如下所示为固定模式改变 [规格](../csharp-7.0/pattern-matching.md#constant-pattern) (以粗体显示) ：</span><span class="sxs-lookup"><span data-stu-id="ee283-115">We alter the [spec](../csharp-7.0/pattern-matching.md#constant-pattern) for constant patterns as follows (the proposed addition is shown in bold):</span></span>

> <span data-ttu-id="ee283-116">常数模式根据常数值测试表达式的值。</span><span class="sxs-lookup"><span data-stu-id="ee283-116">A constant pattern tests the value of an expression against a constant value.</span></span> <span data-ttu-id="ee283-117">常数可以是任何常数表达式，如文本、声明的变量的名称 `const` 、枚举常量或 `typeof` 表达式等。</span><span class="sxs-lookup"><span data-stu-id="ee283-117">The constant may be any constant expression, such as a literal, the name of a declared `const` variable, or an enumeration constant, or a `typeof` expression etc.</span></span>
>
> <span data-ttu-id="ee283-118">如果 *e* 和 *c* 均为整型类型，则当表达式的结果为时，该模式将被视为匹配 `e == c` `true` 。</span><span class="sxs-lookup"><span data-stu-id="ee283-118">If both *e* and *c* are of integral types, the pattern is considered matched if the result of the expression `e == c` is `true`.</span></span>
>
> <span data-ttu-id="ee283-119">**如果 *e* 的类型为 `System.Span<char>` 或 `System.ReadOnlySpan<char>` ，并且 *c* 是常量字符串，并且 *c* 不具有常数值 `null` ，则在返回时，模式会被视为匹配 `System.MemoryExtensions.SequenceEqual<char>(e, System.MemoryExtensions.AsSpan(c))` `true` 。**</span><span class="sxs-lookup"><span data-stu-id="ee283-119">**If *e* is of type `System.Span<char>` or `System.ReadOnlySpan<char>`, and *c* is a constant string, and *c* does not have a constant value of `null`, then the pattern is considered matching if `System.MemoryExtensions.SequenceEqual<char>(e, System.MemoryExtensions.AsSpan(c))` returns `true`.**</span></span>
> 
> <span data-ttu-id="ee283-120">否则，如果返回，则认为模式是匹配的 `object.Equals(e, c)` `true` 。</span><span class="sxs-lookup"><span data-stu-id="ee283-120">Otherwise the pattern is considered matching if `object.Equals(e, c)` returns `true`.</span></span> <span data-ttu-id="ee283-121">在这种情况下，如果 *e* 的静态类型与常量的类型不 *兼容* ，则会发生编译时错误。</span><span class="sxs-lookup"><span data-stu-id="ee283-121">In this case it is a compile-time error if the static type of *e* is not *pattern compatible* with the type of the constant.</span></span>

<span data-ttu-id="ee283-122">`System.Span<T>` 和 `System.ReadOnlySpan<T>` 通过名称匹配，必须是 `ref struct` ，并且可以在 corlib 外部定义。</span><span class="sxs-lookup"><span data-stu-id="ee283-122">`System.Span<T>` and `System.ReadOnlySpan<T>` are matched by name, must be `ref struct`s, and can be defined outside corlib.</span></span> <span data-ttu-id="ee283-123">`System.MemoryExtensions` 是按名称匹配的，可以在 corlib 外部定义。</span><span class="sxs-lookup"><span data-stu-id="ee283-123">`System.MemoryExtensions` is matched by name and can be defined outside corlib.</span></span> <span data-ttu-id="ee283-124">的签名 `System.MemoryExtensions.SequenceEqual` 必须分别匹配 `public static bool SequenceEqual<T>(System.Span<T>, System.ReadOnlySpan<T>)` 和 `public static bool SequenceEqual<T>(System.ReadOnlySpan<T>, System.ReadOnlySpan<T>)` ，并且的签名 `System.MemoryExtensions.AsSpan` 必须匹配 `public static System.ReadOnlySpan<char> AsSpan(string)` 。</span><span class="sxs-lookup"><span data-stu-id="ee283-124">The signature of `System.MemoryExtensions.SequenceEqual` must match `public static bool SequenceEqual<T>(System.Span<T>, System.ReadOnlySpan<T>)` and `public static bool SequenceEqual<T>(System.ReadOnlySpan<T>, System.ReadOnlySpan<T>)` respectively, and the signature of `System.MemoryExtensions.AsSpan` must match `public static System.ReadOnlySpan<char> AsSpan(string)`.</span></span> <span data-ttu-id="ee283-125">不考虑带可选参数的方法。</span><span class="sxs-lookup"><span data-stu-id="ee283-125">Methods with optional parameters are excluded from consideration.</span></span>

## <a name="drawbacks"></a><span data-ttu-id="ee283-126">缺点</span><span class="sxs-lookup"><span data-stu-id="ee283-126">Drawbacks</span></span>
[drawbacks]: #drawbacks

<span data-ttu-id="ee283-127">None</span><span class="sxs-lookup"><span data-stu-id="ee283-127">None</span></span>

## <a name="alternatives"></a><span data-ttu-id="ee283-128">备选项</span><span class="sxs-lookup"><span data-stu-id="ee283-128">Alternatives</span></span>
[alternatives]: #alternatives

<span data-ttu-id="ee283-129">None</span><span class="sxs-lookup"><span data-stu-id="ee283-129">None</span></span>

## <a name="unresolved-questions"></a><span data-ttu-id="ee283-130">未解决的问题</span><span class="sxs-lookup"><span data-stu-id="ee283-130">Unresolved questions</span></span>
[unresolved]: #unresolved-questions

<span data-ttu-id="ee283-131">None</span><span class="sxs-lookup"><span data-stu-id="ee283-131">None</span></span>

## <a name="design-meetings"></a><span data-ttu-id="ee283-132">设计会议</span><span class="sxs-lookup"><span data-stu-id="ee283-132">Design meetings</span></span>

https://github.com/dotnet/csharplang/blob/master/meetings/2020/LDM-2020-10-07.md#readonlyspanchar-patterns

---
ms.openlocfilehash: 0ad15753b0bfbaa6901a443fbbb461b56ed88b4d
ms.sourcegitcommit: 6f244db799642274fafce6897dae169edae0c788
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/25/2020
ms.locfileid: "85372010"
---
# <a name="support-for--and--on-tuple-types"></a><span data-ttu-id="e4315-101">元组类型支持 = = 和！ =</span><span class="sxs-lookup"><span data-stu-id="e4315-101">Support for == and != on tuple types</span></span>

<span data-ttu-id="e4315-102">允许 `t1 == t2` `t1` `t2` 具有相同基数的元组或可为 null 的元组类型的表达式，并将其计算大致为 `temp1.Item1 == temp2.Item1 && temp1.Item2 == temp2.Item2` （假设 `var temp1 = t1; var temp2 = t2;` ）。</span><span class="sxs-lookup"><span data-stu-id="e4315-102">Allow expressions `t1 == t2` where `t1` and `t2` are tuple or nullable tuple types of same cardinality, and evaluate them roughly as `temp1.Item1 == temp2.Item1 && temp1.Item2 == temp2.Item2` (assuming `var temp1 = t1; var temp2 = t2;`).</span></span>

<span data-ttu-id="e4315-103">相反，它会允许 `t1 != t2` 和评估为 `temp1.Item1 != temp2.Item1 || temp1.Item2 != temp2.Item2` 。</span><span class="sxs-lookup"><span data-stu-id="e4315-103">Conversely it would allow `t1 != t2` and evaluate it as `temp1.Item1 != temp2.Item1 || temp1.Item2 != temp2.Item2`.</span></span>

<span data-ttu-id="e4315-104">在可为 null 的情况下， `temp1.HasValue` 将使用针对和的附加检查 `temp2.HasValue` 。</span><span class="sxs-lookup"><span data-stu-id="e4315-104">In the nullable case, additional checks for `temp1.HasValue` and `temp2.HasValue` are used.</span></span> <span data-ttu-id="e4315-105">例如，将 `nullableT1 == nullableT2` 计算为 `temp1.HasValue == temp2.HasValue ? (temp1.HasValue ? ... : true) : false` 。</span><span class="sxs-lookup"><span data-stu-id="e4315-105">For instance, `nullableT1 == nullableT2` evaluates as `temp1.HasValue == temp2.HasValue ? (temp1.HasValue ? ... : true) : false`.</span></span>

<span data-ttu-id="e4315-106">当元素比较返回非 bool 结果时（例如，在非 bool 用户定义 `operator ==` 或 `operator !=` 使用或在动态比较中）时，该结果将转换为 `bool` 或通过 `operator true` 或运行 `operator false` 以获取 `bool` 。</span><span class="sxs-lookup"><span data-stu-id="e4315-106">When an element-wise comparison returns a non-bool result (for instance, when a non-bool user-defined `operator ==` or `operator !=` is used, or in a dynamic comparison), then that result will be either converted to `bool` or run through `operator true` or `operator false` to get a `bool`.</span></span> <span data-ttu-id="e4315-107">元组比较始终最终返回 `bool` 。</span><span class="sxs-lookup"><span data-stu-id="e4315-107">The tuple comparison always ends up returning a `bool`.</span></span>

<span data-ttu-id="e4315-108">从 c # 7.2，此类代码会产生错误（ `error CS0019: Operator '==' cannot be applied to operands of type '(...)' and '(...)'` ），除非存在用户定义的 `operator==` 。</span><span class="sxs-lookup"><span data-stu-id="e4315-108">As of C# 7.2, such code produces an error (`error CS0019: Operator '==' cannot be applied to operands of type '(...)' and '(...)'`), unless there is a user-defined `operator==`.</span></span>

## <a name="details"></a><span data-ttu-id="e4315-109">详细信息</span><span class="sxs-lookup"><span data-stu-id="e4315-109">Details</span></span>

<span data-ttu-id="e4315-110">绑定 `==` （or `!=` ）运算符时，现有规则为：（1）动态大小写，（2）重载决策，（3）失败。</span><span class="sxs-lookup"><span data-stu-id="e4315-110">When binding the `==` (or `!=`) operator, the existing rules are: (1) dynamic case, (2) overload resolution, and (3) fail.</span></span>
<span data-ttu-id="e4315-111">此建议在（1）和（2）之间添加一个元组事例：如果比较运算符的两个操作数都是元组（具有元组类型或是元组文本），并且具有匹配基数，则比较是按元素执行的。</span><span class="sxs-lookup"><span data-stu-id="e4315-111">This proposal adds a tuple case between (1) and (2): if both operands of a comparison operator are tuples (have tuple types or are tuple literals) and have matching cardinality, then the comparison is performed element-wise.</span></span> <span data-ttu-id="e4315-112">此元组相等性也会提升到可为 null 的元组。</span><span class="sxs-lookup"><span data-stu-id="e4315-112">This tuple equality is also lifted onto nullable tuples.</span></span>

<span data-ttu-id="e4315-113">将按从左到右的顺序计算两个操作数（在元组文本的情况下，以及它们的元素）。</span><span class="sxs-lookup"><span data-stu-id="e4315-113">Both operands (and, in the case of tuple literals, their elements) are evaluated in order from left to right.</span></span> <span data-ttu-id="e4315-114">然后，将每对元素用作操作数以递归方式绑定运算符 `==` （或 `!=` ）。</span><span class="sxs-lookup"><span data-stu-id="e4315-114">Each pair of elements is then used as operands to bind the operator `==` (or `!=`), recursively.</span></span> <span data-ttu-id="e4315-115">具有编译时类型的任何元素都 `dynamic` 将导致错误。</span><span class="sxs-lookup"><span data-stu-id="e4315-115">Any elements with compile-time type `dynamic` cause an error.</span></span> <span data-ttu-id="e4315-116">这些元素的比较结果将用作条件 AND （or）运算符链中的操作数。</span><span class="sxs-lookup"><span data-stu-id="e4315-116">The results of those element-wise comparisons are used as operands in a chain of conditional AND (or OR) operators.</span></span>

<span data-ttu-id="e4315-117">例如，在的上下文中 `(int, (int, int)) t1, t2;` ， `t1 == (1, (2, 3))` 将计算为 `temp1.Item1 == temp2.Item1 && temp1.Item2.Item1 == temp2.Item2.Item1 && temp1.Item2.Item2 == temp2.Item2.Item2` 。</span><span class="sxs-lookup"><span data-stu-id="e4315-117">For instance, in the context of `(int, (int, int)) t1, t2;`, `t1 == (1, (2, 3))` would evaluate as `temp1.Item1 == temp2.Item1 && temp1.Item2.Item1 == temp2.Item2.Item1 && temp1.Item2.Item2 == temp2.Item2.Item2`.</span></span>

<span data-ttu-id="e4315-118">当使用元组文本作为操作数（在任一侧）时，它会接收经过转换的元组类型，这些转换是在绑定运算符 `==` （或）元素时引入的按元素转换构成的 `!=` 。</span><span class="sxs-lookup"><span data-stu-id="e4315-118">When a tuple literal is used as operand (on either side), it receives a converted tuple type formed by the element-wise conversions which are introduced when binding the operator `==` (or `!=`) element-wise.</span></span> 

<span data-ttu-id="e4315-119">例如，在中 `(1L, 2, "hello") == (1, 2L, null)` ，这两个元组文本的转换类型为， `(long, long, string)` 第二个文本没有自然类型。</span><span class="sxs-lookup"><span data-stu-id="e4315-119">For instance, in `(1L, 2, "hello") == (1, 2L, null)`, the converted type for both tuple literals is `(long, long, string)` and the second literal has no natural type.</span></span>


### <a name="deconstruction-and-conversions-to-tuple"></a><span data-ttu-id="e4315-120">析构和到元组的转换</span><span class="sxs-lookup"><span data-stu-id="e4315-120">Deconstruction and conversions to tuple</span></span>
<span data-ttu-id="e4315-121">在中 `(a, b) == x` ， `x` 可以析构到两个元素中的事实不起作用。</span><span class="sxs-lookup"><span data-stu-id="e4315-121">In `(a, b) == x`, the fact that `x` can deconstruct into two elements does not play a role.</span></span> <span data-ttu-id="e4315-122">当然，这可能是在未来的建议中，尽管它会提出相关问题 `x == y` （这是一个简单的比较或一个元素的比较，如果使用基数呢？）。</span><span class="sxs-lookup"><span data-stu-id="e4315-122">That could conceivably be in a future proposal, although it would raise questions about `x == y` (is this a simple comparison or an element-wise comparison, and if so using what cardinality?).</span></span>
<span data-ttu-id="e4315-123">同样，转换为元组不起作用。</span><span class="sxs-lookup"><span data-stu-id="e4315-123">Similarly, conversions to tuple play no role.</span></span>

### <a name="tuple-element-names"></a><span data-ttu-id="e4315-124">元组元素名称</span><span class="sxs-lookup"><span data-stu-id="e4315-124">Tuple element names</span></span>

<span data-ttu-id="e4315-125">转换元组文本时，如果在文本中提供了显式元组元素名称，则会发出警告，但它不与目标元组元素名称匹配。</span><span class="sxs-lookup"><span data-stu-id="e4315-125">When converting a tuple literal, we warn when an explicit tuple element name was provided in the literal, but it doesn't match the target tuple element name.</span></span>
<span data-ttu-id="e4315-126">我们在元组比较中使用相同的规则，以便假设 `(int a, int b) t` 我们 `d` 在中发出警告 `t == (c, d: 0)` 。</span><span class="sxs-lookup"><span data-stu-id="e4315-126">We use the same rule in tuple comparison, so that assuming `(int a, int b) t` we warn on `d` in `t == (c, d: 0)`.</span></span>

### <a name="non-bool-element-wise-comparison-results"></a><span data-ttu-id="e4315-127">非布尔元素比较结果</span><span class="sxs-lookup"><span data-stu-id="e4315-127">Non-bool element-wise comparison results</span></span>

<span data-ttu-id="e4315-128">如果元素比较在元组相等性中是动态的，我们将使用运算符的动态调用， `false` 并将其取反以获取 `bool` 并继续进行更多元素比较。</span><span class="sxs-lookup"><span data-stu-id="e4315-128">If an element-wise comparison is dynamic in a tuple equality, we use a dynamic invocation of the operator `false` and negate that to get a `bool` and continue with further element-wise comparisons.</span></span> 

<span data-ttu-id="e4315-129">如果按元素比较在元组相等性中返回某些其他非布尔类型，则有两种情况：</span><span class="sxs-lookup"><span data-stu-id="e4315-129">If an element-wise comparison returns some other non-bool type in a tuple equality, there are two cases:</span></span>
- <span data-ttu-id="e4315-130">如果非 bool 类型转换为，则 `bool` 应用该转换，</span><span class="sxs-lookup"><span data-stu-id="e4315-130">if the non-bool type converts to `bool`, we apply that conversion,</span></span>
- <span data-ttu-id="e4315-131">如果没有此类转换，但该类型有运算符 `false` ，我们将使用该运算符并对结果求反。</span><span class="sxs-lookup"><span data-stu-id="e4315-131">if there is no such conversion, but the type has an operator `false`, we'll use that and negate the result.</span></span>

<span data-ttu-id="e4315-132">在元组不相等时，相同的规则同样适用，但我们将使用运算符 `true` （不含否定）而不是运算符 `false` 。</span><span class="sxs-lookup"><span data-stu-id="e4315-132">In a tuple inequality, the same rules apply except that we'll use the operator `true` (without negation) instead of the operator `false`.</span></span>

<span data-ttu-id="e4315-133">这些规则类似于在语句中使用非 bool 类型 `if` 和其他一些现有上下文所涉及的规则。</span><span class="sxs-lookup"><span data-stu-id="e4315-133">Those rules are similar to the rules involved for using a non-bool type in an `if` statement and some other existing contexts.</span></span>

## <a name="evaluation-order-and-special-cases"></a><span data-ttu-id="e4315-134">计算顺序和特殊情况</span><span class="sxs-lookup"><span data-stu-id="e4315-134">Evaluation order and special cases</span></span>
<span data-ttu-id="e4315-135">首先计算左侧的值，然后计算右侧的值，然后根据从左到右的顺序进行元素比较（包括转换，并根据条件和/或运算符的现有规则提前退出）。</span><span class="sxs-lookup"><span data-stu-id="e4315-135">The left-hand-side value is evaluated first, then the right-hand-side value, then the element-wise comparisons from left to right (including conversions, and with early exit based on existing rules for conditional AND/OR operators).</span></span>

<span data-ttu-id="e4315-136">例如，如果存在从类型 `A` 到类型和方法的转换 `B` `(A, A) GetTuple()` ，则计算 `(new A(1), (new B(2), new B(3))) == (new B(4), GetTuple())` 方法：</span><span class="sxs-lookup"><span data-stu-id="e4315-136">For instance, if there is a conversion from type `A` to type `B` and a method `(A, A) GetTuple()`, evaluating `(new A(1), (new B(2), new B(3))) == (new B(4), GetTuple())` means:</span></span>
- `new A(1)`
- `new B(2)`
- `new B(3)`
- `new B(4)`
- `GetTuple()`
- <span data-ttu-id="e4315-137">然后计算元素的转换和比较和条件逻辑（转换 `new A(1)` 为类型 `B` ，然后将其与进行比较 `new B(4)` ，等等）。</span><span class="sxs-lookup"><span data-stu-id="e4315-137">then the element-wise conversions and comparisons and conditional logic is evaluated (convert `new A(1)` to type `B`, then compare it with `new B(4)`, and so on).</span></span>

### <a name="comparing-null-to-null"></a><span data-ttu-id="e4315-138">比较 `null``null`</span><span class="sxs-lookup"><span data-stu-id="e4315-138">Comparing `null` to `null`</span></span>

<span data-ttu-id="e4315-139">这是常规比较中的一种特殊情况，可以执行元组比较。</span><span class="sxs-lookup"><span data-stu-id="e4315-139">This is a special case from regular comparisons, that carries over to tuple comparisons.</span></span> <span data-ttu-id="e4315-140">`null == null`允许进行比较， `null` 文本不会获得任何类型。</span><span class="sxs-lookup"><span data-stu-id="e4315-140">The `null == null` comparison is allowed, and the `null` literals do not get any type.</span></span>
<span data-ttu-id="e4315-141">在元组相等性中，这意味着 `(0, null) == (0, null)` 也允许，并且 `null` 和元组文本也不会获取类型。</span><span class="sxs-lookup"><span data-stu-id="e4315-141">In tuple equality, this means, `(0, null) == (0, null)` is also allowed and the `null` and tuple literals don't get a type either.</span></span>

### <a name="comparing-a-nullable-struct-to-null-without-operator"></a><span data-ttu-id="e4315-142">将可为 null 的结构比较为 `null` 不`operator==`</span><span class="sxs-lookup"><span data-stu-id="e4315-142">Comparing a nullable struct to `null` without `operator==`</span></span>

<span data-ttu-id="e4315-143">这是常规比较的另一种特殊情况，它会执行元组比较。</span><span class="sxs-lookup"><span data-stu-id="e4315-143">This is another special case from regular comparisons, that carries over to tuple comparisons.</span></span>
<span data-ttu-id="e4315-144">如果没有，则 `struct S` `operator==` 允许进行 `(S?)x == null` 比较，并将其解释为 `((S?).x).HasValue` 。</span><span class="sxs-lookup"><span data-stu-id="e4315-144">If you have a `struct S` without `operator==`, the `(S?)x == null` comparison is allowed, and it is interpreted as `((S?).x).HasValue`.</span></span>
<span data-ttu-id="e4315-145">在元组相等时，将应用相同的规则，因此 `(0, (S?)x) == (0, null)` 允许。</span><span class="sxs-lookup"><span data-stu-id="e4315-145">In tuple equality, the same rule is applied, so `(0, (S?)x) == (0, null)` is allowed.</span></span>

## <a name="compatibility"></a><span data-ttu-id="e4315-146">兼容性</span><span class="sxs-lookup"><span data-stu-id="e4315-146">Compatibility</span></span>

<span data-ttu-id="e4315-147">如果有人 `ValueTuple` 使用比较运算符的实现来编写自己的类型，则它以前会被重载决策选取。</span><span class="sxs-lookup"><span data-stu-id="e4315-147">If someone wrote their own `ValueTuple` types with  an implementation of the comparison operator, it would have previously been picked up by overload resolution.</span></span> <span data-ttu-id="e4315-148">但由于新的元组事例出现在重载决策之前，我们将用元组比较来处理这种情况，而不是依赖于用户定义的比较。</span><span class="sxs-lookup"><span data-stu-id="e4315-148">But since the new tuple case comes before overload resolution, we would handle this case with tuple comparison instead of relying on the user-defined comparison.</span></span>

----

<span data-ttu-id="e4315-149">与[关系和类型测试运算符](../../spec/expressions.md#relational-and-type-testing-operators)相关，并与[#190](https://github.com/dotnet/csharplang/issues/190)相关</span><span class="sxs-lookup"><span data-stu-id="e4315-149">Relates to [relational and type testing operators](../../spec/expressions.md#relational-and-type-testing-operators) Relates to [#190](https://github.com/dotnet/csharplang/issues/190)</span></span>

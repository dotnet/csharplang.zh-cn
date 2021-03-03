---
ms.openlocfilehash: 44980f4dadfb80a5ef1fccb09ef6490f80a12c5c
ms.sourcegitcommit: 3f8f57e294e2952af19a5231e6b9c7ff85d0ed56
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/02/2021
ms.locfileid: "101751635"
---
# <a name="improved-definite-assignment-analysis"></a><span data-ttu-id="71db6-101">改进的明确赋值分析</span><span class="sxs-lookup"><span data-stu-id="71db6-101">Improved Definite Assignment Analysis</span></span>

## <a name="summary"></a><span data-ttu-id="71db6-102">总结</span><span class="sxs-lookup"><span data-stu-id="71db6-102">Summary</span></span>
<span data-ttu-id="71db6-103">指定的[明确赋值分析](../spec/variables.md#definite-assignment)具有几个缺口，导致用户无法使用。</span><span class="sxs-lookup"><span data-stu-id="71db6-103">[Definite assignment analysis](../spec/variables.md#definite-assignment) as specified has a few gaps which have caused users inconvenience.</span></span> <span data-ttu-id="71db6-104">具体而言，涉及与布尔常量、条件性访问和 null 合并的比较。</span><span class="sxs-lookup"><span data-stu-id="71db6-104">In particular, scenarios involving comparison to boolean constants, conditional-access, and null coalescing.</span></span>

## <a name="related-discussions-and-issues"></a><span data-ttu-id="71db6-105">相关讨论和问题</span><span class="sxs-lookup"><span data-stu-id="71db6-105">Related discussions and issues</span></span>
<span data-ttu-id="71db6-106">此建议的 csharplang 讨论： https://github.com/dotnet/csharplang/discussions/4240</span><span class="sxs-lookup"><span data-stu-id="71db6-106">csharplang discussion of this proposal: https://github.com/dotnet/csharplang/discussions/4240</span></span>

<span data-ttu-id="71db6-107">可能是通过此查询或类似 (查询（例如，搜索 "明确分配" 而不是 "CS0165"）或在 csharplang) 中搜索来查找用户报表。</span><span class="sxs-lookup"><span data-stu-id="71db6-107">Probably a dozen or so user reports can be found via this or similar queries (i.e. search for "definite assignment" instead of "CS0165", or search in csharplang).</span></span>
<span data-ttu-id="71db6-108">https://github.com/dotnet/roslyn/issues?q=is%3Aclosed+is%3Aissue+label%3A%22Resolution-By+Design%22+cs0165</span><span class="sxs-lookup"><span data-stu-id="71db6-108">https://github.com/dotnet/roslyn/issues?q=is%3Aclosed+is%3Aissue+label%3A%22Resolution-By+Design%22+cs0165</span></span>

<span data-ttu-id="71db6-109">我在下面的方案中包含了相关的问题，让你了解每个方案的相对影响。</span><span class="sxs-lookup"><span data-stu-id="71db6-109">I have included related issues in the scenarios below to give a sense of the relative impact of each scenario.</span></span>

## <a name="scenarios"></a><span data-ttu-id="71db6-110">方案</span><span class="sxs-lookup"><span data-stu-id="71db6-110">Scenarios</span></span>

<span data-ttu-id="71db6-111">作为参考，让我们从一个众所周知的 "满意情况" 开始，该示例在明确赋值和可以为 null 的情况下工作。</span><span class="sxs-lookup"><span data-stu-id="71db6-111">As a point of reference, let's start with a well-known "happy case" that does work in definite assignment and in nullable.</span></span>
```cs
#nullable enable

C c = new C();
if (c != null && c.M(out object obj0))
{
    obj0.ToString(); // ok
}

public class C
{
    public bool M(out object obj)
    {
        obj = new object();
        return true;
    }
}
```

### <a name="comparison-to-bool-constant"></a><span data-ttu-id="71db6-112">与 bool 常量的比较</span><span class="sxs-lookup"><span data-stu-id="71db6-112">Comparison to bool constant</span></span>
- https://github.com/dotnet/csharplang/discussions/801
- https://github.com/dotnet/roslyn/issues/45582
  - <span data-ttu-id="71db6-113">链接到其他用户受此影响的其他问题。</span><span class="sxs-lookup"><span data-stu-id="71db6-113">Links to 4 other issues where people were affected by this.</span></span>
```cs
if ((c != null && c.M(out object obj1)) == true)
{
    obj1.ToString(); // undesired error
}

if ((c != null && c.M(out object obj2)) is true)
{
    obj2.ToString(); // undesired error
}
```

### <a name="comparison-between-a-conditional-access-and-a-constant-value"></a><span data-ttu-id="71db6-114">条件访问和常数值之间的比较</span><span class="sxs-lookup"><span data-stu-id="71db6-114">Comparison between a conditional access and a constant value</span></span>

- https://github.com/dotnet/roslyn/issues/33559
- https://github.com/dotnet/csharplang/discussions/4214
- https://github.com/dotnet/csharplang/issues/3659
- https://github.com/dotnet/csharplang/issues/3485
- https://github.com/dotnet/csharplang/issues/3659

<span data-ttu-id="71db6-115">这种情况可能是最大的。</span><span class="sxs-lookup"><span data-stu-id="71db6-115">This scenario is probably the biggest one.</span></span> <span data-ttu-id="71db6-116">我们确实支持可以为 null，但不支持明确赋值。</span><span class="sxs-lookup"><span data-stu-id="71db6-116">We do support this in nullable but not in definite assignment.</span></span>

```cs
if (c?.M(out object obj3) == true)
{
    obj3.ToString(); // undesired error
}
```

### <a name="conditional-access-coalesced-to-a-bool-constant"></a><span data-ttu-id="71db6-117">与 bool 常量合并的条件性访问</span><span class="sxs-lookup"><span data-stu-id="71db6-117">Conditional access coalesced to a bool constant</span></span>

- https://github.com/dotnet/csharplang/discussions/916
- https://github.com/dotnet/csharplang/issues/3365

<span data-ttu-id="71db6-118">此方案与上一个方案非常类似。</span><span class="sxs-lookup"><span data-stu-id="71db6-118">This scenario is very similar to the previous one.</span></span> <span data-ttu-id="71db6-119">这也支持可以为 null，但不能在明确赋值中进行。</span><span class="sxs-lookup"><span data-stu-id="71db6-119">This is also supported in nullable but not in definite assignment.</span></span>

```cs
if (c?.M(out object obj4) ?? false)
{
    obj4.ToString(); // undesired error
}
```

### <a name="conditional-expressions-where-one-arm-is-a-bool-constant"></a><span data-ttu-id="71db6-120">一个 arm 为 bool 常量的条件表达式</span><span class="sxs-lookup"><span data-stu-id="71db6-120">Conditional expressions where one arm is a bool constant</span></span>
- https://github.com/dotnet/roslyn/issues/4272

<span data-ttu-id="71db6-121">值得一提的是，如果条件表达式是常量 (即) ，则已经具有特殊行为。 `true ? a : b`</span><span class="sxs-lookup"><span data-stu-id="71db6-121">It's worth pointing out that we already have special behavior for when the condition expression is constant (i.e. `true ? a : b`).</span></span> <span data-ttu-id="71db6-122">我们只是无条件地访问常量条件所指示的 arm，并忽略其他 arm。</span><span class="sxs-lookup"><span data-stu-id="71db6-122">We just unconditionally visit the arm indicated by the constant condition and ignore the other arm.</span></span>

<span data-ttu-id="71db6-123">另请注意，我们尚未处理此方案，这是可以为 null 的。</span><span class="sxs-lookup"><span data-stu-id="71db6-123">Also note that we haven't handled this scenario in nullable.</span></span>

```cs
if (c != null ? c.M(out object obj4) : false)
{
    obj4.ToString(); // undesired error
}
```

# <a name="specification"></a><span data-ttu-id="71db6-124">规范</span><span class="sxs-lookup"><span data-stu-id="71db6-124">Specification</span></span>

## <a name="-null-conditional-operator-expressions"></a><span data-ttu-id="71db6-125">?.</span><span class="sxs-lookup"><span data-stu-id="71db6-125">?.</span></span> <span data-ttu-id="71db6-126"> () 表达式的 null 条件运算符</span><span class="sxs-lookup"><span data-stu-id="71db6-126">(null-conditional operator) expressions</span></span>
<span data-ttu-id="71db6-127">我们引入了一个新部分 **？。 () 表达式的 null 条件运算符**。</span><span class="sxs-lookup"><span data-stu-id="71db6-127">We introduce a new section **?. (null-conditional operator) expressions**.</span></span> <span data-ttu-id="71db6-128">请参阅适用于上下文的 [null 条件运算符](../spec/expressions.md#null-conditional-operator) 规范和 [明确赋值规则](../spec/variables.md#precise-rules-for-determining-definite-assignment) 。</span><span class="sxs-lookup"><span data-stu-id="71db6-128">See the [null-conditional operator](../spec/expressions.md#null-conditional-operator) specification and [definite assignment rules](../spec/variables.md#precise-rules-for-determining-definite-assignment) for context.</span></span>

<span data-ttu-id="71db6-129">与上面链接的明确赋值规则一样，我们将给定的初始未赋值变量称为 *v*。</span><span class="sxs-lookup"><span data-stu-id="71db6-129">As in the definite assignment rules linked above, we refer to a given initially unassigned variable as *v*.</span></span>

<span data-ttu-id="71db6-130">我们引入了 "直接包含" 这一概念。</span><span class="sxs-lookup"><span data-stu-id="71db6-130">We introduce the concept of "directly contains".</span></span> <span data-ttu-id="71db6-131">如果满足以下条件之一，则表达式 *e* 称为 "直接包含子表达式 *e <sub>1</sub>* "：</span><span class="sxs-lookup"><span data-stu-id="71db6-131">An expression *E* is said to "directly contain" a subexpression *E<sub>1</sub>* if one of the following conditions holds:</span></span>
- <span data-ttu-id="71db6-132">*E* 为 *e <sub>1</sub>*。</span><span class="sxs-lookup"><span data-stu-id="71db6-132">*E* is *E<sub>1</sub>*.</span></span> <span data-ttu-id="71db6-133">例如， `a?.b()` 直接包含表达式 `a?.b()` 。</span><span class="sxs-lookup"><span data-stu-id="71db6-133">For example, `a?.b()` directly contains the expression `a?.b()`.</span></span>
- <span data-ttu-id="71db6-134">如果 *e* 是带括号的表达式 `(E2)` ，则 *e <sub>2</sub>* 直接包含 *e <sub>1</sub>*。</span><span class="sxs-lookup"><span data-stu-id="71db6-134">If *E* is a parenthesized expression `(E2)`, and *E <sub>2</sub>* directly contains *E<sub>1</sub>*.</span></span>
- <span data-ttu-id="71db6-135">如果 *e* 是包容性运算符表达式，则 e `E2!` *<sub>2</sub>* 直接包含 *e <sub>1</sub>*。</span><span class="sxs-lookup"><span data-stu-id="71db6-135">If *E* is a null-forgiving operator expression `E2!`, and *E <sub>2</sub>* directly contains *E<sub>1</sub>*.</span></span>

<span data-ttu-id="71db6-136">对于窗体的表达式 *E* `primary_expression null_conditional_operations` ，让 *E <sub>0</sub>* 是通过按时间删除前导的表达式：</span><span class="sxs-lookup"><span data-stu-id="71db6-136">For an expression *E* of the form `primary_expression null_conditional_operations`, let *E<sub>0</sub>* be the expression obtained by textually removing the leading ?</span></span> <span data-ttu-id="71db6-137">从每个包含 *E* 的 *null_conditional_operations* ，如以上链接的规范中所述。</span><span class="sxs-lookup"><span data-stu-id="71db6-137">from each of the *null_conditional_operations* of *E* that have one, as in the linked specification above.</span></span>

<span data-ttu-id="71db6-138">在后面的部分中，我们会将 *E <sub>0</sub>* 引用为与 null 条件表达式 *对应的非条件对应* 项。</span><span class="sxs-lookup"><span data-stu-id="71db6-138">In subsequent sections we will refer to *E <sub>0</sub>* as the *non-conditional counterpart* to the null-conditional expression.</span></span> <span data-ttu-id="71db6-139">请注意，后续部分中的某些表达式将服从其他规则，这些规则仅当其中一个操作数直接包含空条件表达式时才适用。</span><span class="sxs-lookup"><span data-stu-id="71db6-139">Note that some expressions in subsequent sections are subject to additional rules that only apply when one of the operands directly contains a null-conditional expression.</span></span>

- <span data-ttu-id="71db6-140">在 *E* 内的任何时间点， *v* 的明确赋值状态与 *E0* 内相应点上的明确赋值状态相同。</span><span class="sxs-lookup"><span data-stu-id="71db6-140">The definite assignment state of *v* at any point within *E* is the same as the definite assignment state at the corresponding point within *E0*.</span></span>
- <span data-ttu-id="71db6-141">*E* 后 *v* 的明确赋值状态与 *primary_expression* 后 *v* 的明确分配状态相同。</span><span class="sxs-lookup"><span data-stu-id="71db6-141">The definite assignment state of *v* after *E* is the same as the definite assignment state of *v* after *primary_expression*.</span></span>

### <a name="remarks"></a><span data-ttu-id="71db6-142">备注</span><span class="sxs-lookup"><span data-stu-id="71db6-142">Remarks</span></span>
<span data-ttu-id="71db6-143">我们使用 "直接包含" 这一概念，以使我们能够在分析与其他值比较的条件访问时跳过相对简单的 "包装器" 表达式。</span><span class="sxs-lookup"><span data-stu-id="71db6-143">We use the concept of "directly contains" to allow us to skip over relatively simple "wrapper" expressions when analyzing conditional accesses that are compared to other values.</span></span> <span data-ttu-id="71db6-144">例如， `((a?.b(out x))!) == true` 预计会产生与一般相同的流状态 `a?.b == true` 。</span><span class="sxs-lookup"><span data-stu-id="71db6-144">For example, `((a?.b(out x))!) == true` is expected to result in the same flow state as `a?.b == true` in general.</span></span>

<span data-ttu-id="71db6-145">当我们考虑在 null 条件表达式中的给定点上分配变量时，只需假设在相同的 null 条件表达式内的任何前面的 null 条件运算都已成功。</span><span class="sxs-lookup"><span data-stu-id="71db6-145">When we consider whether a variable is assigned at a given point within a null-conditional expression, we simply assume that any preceding null-conditional operations within the same null-conditional expression succeeded.</span></span>

<span data-ttu-id="71db6-146">例如，给定一个条件表达式 `a?.b(out x)?.c(x)` ，非条件对应的是 `a.b(out x).c(x)` 。</span><span class="sxs-lookup"><span data-stu-id="71db6-146">For example, given a conditional expression `a?.b(out x)?.c(x)`, the non-conditional counterpart is `a.b(out x).c(x)`.</span></span> <span data-ttu-id="71db6-147">例如，如果我们想要了解之前的明确赋值状态 `x` `?.c(x)` ，则执行的 "假设" 分析， `a.b(out x)` 并使用生成的状态作为的输入 `?.c(x)` 。</span><span class="sxs-lookup"><span data-stu-id="71db6-147">If we want to know the definite assignment state of `x` before `?.c(x)`, for example, then we perform a "hypothetical" analysis of `a.b(out x)` and use the resulting state as an input to `?.c(x)`.</span></span>

## <a name="boolean-constant-expressions"></a><span data-ttu-id="71db6-148">布尔常量表达式</span><span class="sxs-lookup"><span data-stu-id="71db6-148">Boolean constant expressions</span></span>
<span data-ttu-id="71db6-149">我们引入了一个新的 "布尔常量表达式" 部分：</span><span class="sxs-lookup"><span data-stu-id="71db6-149">We introduce a new section "Boolean constant expressions":</span></span>

<span data-ttu-id="71db6-150">对于 expression *expr* ，其中 *expr* 是带有 bool 值的常量表达式：</span><span class="sxs-lookup"><span data-stu-id="71db6-150">For an expression *expr* where *expr* is a constant expression with a bool value:</span></span>
- <span data-ttu-id="71db6-151">*Expr* 后 *v* 的明确赋值状态由确定：</span><span class="sxs-lookup"><span data-stu-id="71db6-151">The definite assignment state of *v* after *expr* is determined by:</span></span>
  - <span data-ttu-id="71db6-152">如果 *expr* 是值 *为 true* 的常量表达式，且 *expr* 之前的 *v* 状态为 "未明确赋值"，则 *expr* 之后的 *v* 状态是 "在 false 时明确赋值"。</span><span class="sxs-lookup"><span data-stu-id="71db6-152">If *expr* is a constant expression with value *true*, and the state of *v* before *expr* is "not definitely assigned", then the state of *v* after *expr* is "definitely assigned when false".</span></span>
  - <span data-ttu-id="71db6-153">如果 *expr* 是值 *为 false* 的常量表达式，且 *expr* 之前的 *v* 的状态为 "未明确赋值"，则 *expr* 后 *v* 的状态为 "如果为 true，则明确赋值"。</span><span class="sxs-lookup"><span data-stu-id="71db6-153">If *expr* is a constant expression with value *false*, and the state of *v* before *expr* is "not definitely assigned", then the state of *v* after *expr* is "definitely assigned when true".</span></span>

### <a name="remarks"></a><span data-ttu-id="71db6-154">备注</span><span class="sxs-lookup"><span data-stu-id="71db6-154">Remarks</span></span>

<span data-ttu-id="71db6-155">例如，如果表达式的布尔值为布尔值 `false` ，则不可能到达需要表达式返回的任何分支 `true` 。</span><span class="sxs-lookup"><span data-stu-id="71db6-155">We assume that if an expression has a constant value bool `false`, for example, it's impossible to reach any branch that requires the expression to return `true`.</span></span> <span data-ttu-id="71db6-156">因此，在此类分支中，变量被假定为明确赋值。</span><span class="sxs-lookup"><span data-stu-id="71db6-156">Therefore variables are assumed to be definitely assigned in such branches.</span></span> <span data-ttu-id="71db6-157">这最终会与和等表达式的规格变化非常结合 `??` ， `?:` 并启用很多有用方案。</span><span class="sxs-lookup"><span data-stu-id="71db6-157">This ends up combining nicely with the spec changes for expressions like `??` and `?:` and enabling a lot of useful scenarios.</span></span>

<span data-ttu-id="71db6-158">还值得注意的是，在访问常量表达式 *之前* ，永远不会出现条件状态。</span><span class="sxs-lookup"><span data-stu-id="71db6-158">It's also worth noting that we never expect to be in a conditional state *before* visiting a constant expression.</span></span> <span data-ttu-id="71db6-159">这就是我们不考虑这种情况的原因，例如 "*expr* 是值 *为 true* 的常量表达式，而 *expr* 之前的 *v* 状态是" 在 true 时明确赋值 "。</span><span class="sxs-lookup"><span data-stu-id="71db6-159">That's why we do not account for scenarios such as "*expr* is a constant expression with value *true*, and the state of *v* before *expr* is "definitely assigned when true".</span></span>

## <a name="-null-coalescing-expressions-augment"></a><span data-ttu-id="71db6-160">??</span><span class="sxs-lookup"><span data-stu-id="71db6-160">??</span></span> <span data-ttu-id="71db6-161">) 增加 (null 合并表达式</span><span class="sxs-lookup"><span data-stu-id="71db6-161">(null-coalescing expressions) augment</span></span>
<span data-ttu-id="71db6-162">我们按如下所示增加了节 [ (null 合并) 表达式](../spec/variables.md#-null-coalescing-expressions) ：</span><span class="sxs-lookup"><span data-stu-id="71db6-162">We augment the section [?? (null coalescing) expressions](../spec/variables.md#-null-coalescing-expressions) as follows:</span></span>

<span data-ttu-id="71db6-163">对于以下形式 *的表达式表达式* `expr_first ?? expr_second` ：</span><span class="sxs-lookup"><span data-stu-id="71db6-163">For an expression *expr* of the form `expr_first ?? expr_second`:</span></span>
- <span data-ttu-id="71db6-164">...</span><span class="sxs-lookup"><span data-stu-id="71db6-164">...</span></span>
- <span data-ttu-id="71db6-165">*Expr* 后 *v* 的明确赋值状态由确定：</span><span class="sxs-lookup"><span data-stu-id="71db6-165">The definite assignment state of *v* after *expr* is determined by:</span></span>
  - <span data-ttu-id="71db6-166">...</span><span class="sxs-lookup"><span data-stu-id="71db6-166">...</span></span>
  - <span data-ttu-id="71db6-167">如果 *expr_first* 直接包含一个 null 条件表达式 *e*，并且在非条件对应的 *e <sub>0</sub>* 后， *v* 是明确赋值的，则在 *expr* 后 *v* 的明确赋值状态与 *expr_second* 后 *v* 的明确赋值状态相同。</span><span class="sxs-lookup"><span data-stu-id="71db6-167">If *expr_first* directly contains a null-conditional expression *E*, and *v* is definitely assigned after the non-conditional counterpart *E <sub>0</sub>*, then the definite assignment state of *v* after *expr* is the same as the definite assignment state of *v* after *expr_second*.</span></span>

### <a name="remarks"></a><span data-ttu-id="71db6-168">备注</span><span class="sxs-lookup"><span data-stu-id="71db6-168">Remarks</span></span>
<span data-ttu-id="71db6-169">上面的规则将针对类似于的表达式进行了假设 `a?.M(out x) ?? (x = false)` ， `a?.M(out x)` 已完全计算并生成了一个非 null 值，在这种情况下，已 `x` 赋值或 `x = false` 已计算，在这种情况下， `x` 也分配了。</span><span class="sxs-lookup"><span data-stu-id="71db6-169">The above rule formalizes that for an expression like `a?.M(out x) ?? (x = false)`, either the `a?.M(out x)` was fully evaluated and produced a non-null value, in which case `x` was assigned, or the `x = false` was evaluated, in which case `x` was also assigned.</span></span> <span data-ttu-id="71db6-170">因此 `x` ，在此表达式之后始终赋值。</span><span class="sxs-lookup"><span data-stu-id="71db6-170">Therefore `x` is always assigned after this expression.</span></span>

<span data-ttu-id="71db6-171">此方法还处理 `dict?.TryGetValue(key, out var value) ?? false` 方案，方法是：观察 *v* 在之后明确赋值 `dict.TryGetValue(key, out var value)` ， *v* 为 "true 时明确赋值"，最后结束时， `false` *v* 必须是 "true 赋值"。</span><span class="sxs-lookup"><span data-stu-id="71db6-171">This also handles the `dict?.TryGetValue(key, out var value) ?? false` scenario, by observing that *v* is definitely assigned after `dict.TryGetValue(key, out var value)`, and *v* is "definitely assigned when true" after `false`, and concluding that *v* must be "definitely assigned when true".</span></span>

<span data-ttu-id="71db6-172">更常见的表述还允许我们处理一些更不常见的方案，例如：</span><span class="sxs-lookup"><span data-stu-id="71db6-172">The more general formulation also allows us to handle some more unusual scenarios, such as:</span></span>
- `if (x?.M(out y) ?? (b && z.M(out y))) y.ToString();`
- `if (x?.M(out y) ?? z?.M(out y) ?? false) y.ToString();`

## <a name="-conditional-expressions"></a><span data-ttu-id="71db6-173">？： (条件) 表达式</span><span class="sxs-lookup"><span data-stu-id="71db6-173">?: (conditional) expressions</span></span>
<span data-ttu-id="71db6-174">我们将对此部分进行扩展： [**： (条件) 表达式**](../spec/variables.md#-conditional-expressions) ，如下所示：</span><span class="sxs-lookup"><span data-stu-id="71db6-174">We augment the section [**?: (conditional) expressions**](../spec/variables.md#-conditional-expressions) as follows:</span></span>

<span data-ttu-id="71db6-175">对于以下形式 *的表达式表达式* `expr_cond ? expr_true : expr_false` ：</span><span class="sxs-lookup"><span data-stu-id="71db6-175">For an expression *expr* of the form `expr_cond ? expr_true : expr_false`:</span></span>
- <span data-ttu-id="71db6-176">...</span><span class="sxs-lookup"><span data-stu-id="71db6-176">...</span></span>
- <span data-ttu-id="71db6-177">*Expr* 后 *v* 的明确赋值状态由确定：</span><span class="sxs-lookup"><span data-stu-id="71db6-177">The definite assignment state of *v* after *expr* is determined by:</span></span>
  - <span data-ttu-id="71db6-178">...</span><span class="sxs-lookup"><span data-stu-id="71db6-178">...</span></span>
  - <span data-ttu-id="71db6-179">如果 *expr_true* 后的 *v* 状态是 "在 true 时明确赋值"，并且在 *expr_false* 后 *v* 的状态为 "当为 true 时明确赋值"，则在 *expr* 后 *v* 的状态为 "当为 true 时明确赋值"。</span><span class="sxs-lookup"><span data-stu-id="71db6-179">If the state of *v* after *expr_true* is "definitely assigned when true", and the state of *v* after *expr_false* is "definitely assigned when true", then the state of *v* after *expr* is "definitely assigned when true".</span></span>
  - <span data-ttu-id="71db6-180">如果 *expr_true* 后的 *v* 状态为 "false 时明确赋值"，并且在 *expr_false* 后 *v* 的状态为 "当为 false 时明确赋值"，则在 *expr* 后 *v* 的状态为 "当为 false 时明确赋值"。</span><span class="sxs-lookup"><span data-stu-id="71db6-180">If the state of *v* after *expr_true* is "definitely assigned when false", and the state of *v* after *expr_false* is "definitely assigned when false", then the state of *v* after *expr* is "definitely assigned when false".</span></span>

### <a name="remarks"></a><span data-ttu-id="71db6-181">备注</span><span class="sxs-lookup"><span data-stu-id="71db6-181">Remarks</span></span>

<span data-ttu-id="71db6-182">这样一来，如果条件表达式的两个扶手都产生条件状态，我们就会加入相应的条件状态，并将其传播而不是 unsplitting 状态，并允许最终状态为非条件。</span><span class="sxs-lookup"><span data-stu-id="71db6-182">This makes it so when both arms of a conditional expression result in a conditional state, we join the corresponding conditional states and propagate it out instead of unsplitting the state and allowing the final state to be non-conditional.</span></span> <span data-ttu-id="71db6-183">这会启用如下方案：</span><span class="sxs-lookup"><span data-stu-id="71db6-183">This enables scenarios like the following:</span></span>

```cs
bool b = true;
object x = null;
int y;
if (b ? x != null && Set(out y) : x != null && Set(out y))
{
  y.ToString();
}

bool Set(out int x) { x = 0; return true; }
```

<span data-ttu-id="71db6-184">这是一种无可否认的方案，在本机编译器中编译时不会出错，但会在 Roslyn 中断开以匹配该规范。</span><span class="sxs-lookup"><span data-stu-id="71db6-184">This is an admittedly niche scenario, that compiles without error in the native compiler, but was broken in Roslyn in order to match the specification at the time.</span></span> <span data-ttu-id="71db6-185">查看 [内部问题](http://vstfdevdiv:8080/DevDiv2/DevDiv/_workitems/edit/529603)。</span><span class="sxs-lookup"><span data-stu-id="71db6-185">See [internal issue](http://vstfdevdiv:8080/DevDiv2/DevDiv/_workitems/edit/529603).</span></span>

## <a name="-relational-equality-operator-expressions"></a><span data-ttu-id="71db6-186">= =/！ = (关系相等运算符) 表达式</span><span class="sxs-lookup"><span data-stu-id="71db6-186">==/!= (relational equality operator) expressions</span></span>
<span data-ttu-id="71db6-187">我们引入了一个新的节 **= =/！ = (关系相等运算符) 表达式**。</span><span class="sxs-lookup"><span data-stu-id="71db6-187">We introduce a new section **==/!= (relational equality operator) expressions**.</span></span>

<span data-ttu-id="71db6-188">除了下述方案， [使用嵌入表达式的表达式的一般规则](../spec/variables.md#general-rules-for-expressions-with-embedded-expressions) 都适用。</span><span class="sxs-lookup"><span data-stu-id="71db6-188">The [general rules for expressions with embedded expressions](../spec/variables.md#general-rules-for-expressions-with-embedded-expressions) apply, except for the scenarios described below.</span></span>

<span data-ttu-id="71db6-189">对于窗体 *的表达式表达式* `expr_first == expr_second` （其中 `==` 是 [预定义的比较运算符](../spec/expressions.md#relational-and-type-testing-operators)或 [提升运算符](../spec/expressions.md#lifted-operators)）， *expr* 后 *v* 的明确赋值状态由确定：</span><span class="sxs-lookup"><span data-stu-id="71db6-189">For an expression *expr* of the form `expr_first == expr_second`, where `==` is a [predefined comparison operator](../spec/expressions.md#relational-and-type-testing-operators) or a [lifted operator](../spec/expressions.md#lifted-operators), the definite assignment state of *v* after *expr* is determined by:</span></span>
  - <span data-ttu-id="71db6-190">如果 *expr_first* 直接包含 null 条件表达式 *E* 并且 *expr_second* 是值 *为 null* 的常量表达式，而与非条件对应的 *E <sub>0</sub>* 的状态为 "明确赋值"，则 *expr* 后 *v* 的状态为 "当为 false 时明确赋值"。</span><span class="sxs-lookup"><span data-stu-id="71db6-190">If *expr_first* directly contains a null-conditional expression *E* and *expr_second* is a constant expression with value *null*, and the state of *v* after the non-conditional counterpart *E <sub>0</sub>* is "definitely assigned", then the state of *v* after *expr* is "definitely assigned when false".</span></span>
  - <span data-ttu-id="71db6-191">如果 *expr_first* 直接包含 null 条件表达式 *E* 并且 *expr_second* 是不可以为 null 的值类型的表达式，或者是具有非 null 值的常量表达式，而与非 null 值 *对应的常量* 表达式为 "明确赋值"，则在 *expr* 后 *v* 的 *状态为 "<sub></sub>* 在 true 时明确赋值"。</span><span class="sxs-lookup"><span data-stu-id="71db6-191">If *expr_first* directly contains a null-conditional expression *E* and *expr_second* is an expression of a non-nullable value type, or a constant expression with a non-null value, and the state of *v* after the non-conditional counterpart *E <sub>0</sub>* is "definitely assigned", then the state of *v* after *expr* is "definitely assigned when true".</span></span>
  - <span data-ttu-id="71db6-192">如果 *expr_first* 的类型为 *boolean*， *expr_second* 是值 *为 true* 的常量表达式，则 *expr* 之后的明确赋值状态与 *expr_first* 后的明确赋值状态相同。</span><span class="sxs-lookup"><span data-stu-id="71db6-192">If *expr_first* is of type *boolean*, and *expr_second* is a constant expression with value *true*, then the definite assignment state after *expr* is the same as the definite assignment state after *expr_first*.</span></span>
  - <span data-ttu-id="71db6-193">如果 *expr_first* 的类型为 *boolean*，并且 *expr_second* 是值 *为 false* 的常量表达式，则 *expr* 之后的明确赋值状态与逻辑求反表达式后 *v* 的明确赋值状态相同 `!expr_first` 。</span><span class="sxs-lookup"><span data-stu-id="71db6-193">If *expr_first* is of type *boolean*, and *expr_second* is a constant expression with value *false*, then the definite assignment state after *expr* is the same as the definite assignment state of *v* after the logical negation expression `!expr_first`.</span></span>

<span data-ttu-id="71db6-194">对于窗体 *的表达式表达式* `expr_first != expr_second` （其中 `!=` 是 [预定义的比较运算符](../spec/expressions.md#relational-and-type-testing-operators)或 [提升运算符](../spec/expressions.md#lifted-operators)）， *expr* 后 *v* 的明确赋值状态由确定：</span><span class="sxs-lookup"><span data-stu-id="71db6-194">For an expression *expr* of the form `expr_first != expr_second`, where `!=` is a [predefined comparison operator](../spec/expressions.md#relational-and-type-testing-operators) or a [lifted operator](../spec/expressions.md#lifted-operators), the definite assignment state of *v* after *expr* is determined by:</span></span>
  - <span data-ttu-id="71db6-195">如果 *expr_first* 直接包含 null 条件表达式 *E* 并且 *expr_second* 是值 *为 null* 的常量表达式，而与非条件对应的 *E <sub>0</sub>* 的状态为 "明确赋值"，则 *expr* 后 *v* 的状态为 "在 true 时明确赋值"。</span><span class="sxs-lookup"><span data-stu-id="71db6-195">If *expr_first* directly contains a null-conditional expression *E* and *expr_second* is a constant expression with value *null*, and the state of *v* after the non-conditional counterpart *E <sub>0</sub>* is "definitely assigned", then the state of *v* after *expr* is "definitely assigned when true".</span></span>
  - <span data-ttu-id="71db6-196">如果 *expr_first* 直接包含 null 条件表达式 *E* 并且 *expr_second* 是不可以为 null 的值类型的表达式，或者是具有非 null 值的常量表达式，而与非 null 值 *对应的常量* 表达式为 "明确赋值"，则在 *expr* 后 *v* 的 *状态为 "<sub></sub>* 在 false 时明确赋值"。</span><span class="sxs-lookup"><span data-stu-id="71db6-196">If *expr_first* directly contains a null-conditional expression *E* and *expr_second* is an expression of a non-nullable value type, or a constant expression with a non-null value, and the state of *v* after the non-conditional counterpart *E <sub>0</sub>* is "definitely assigned", then the state of *v* after *expr* is "definitely assigned when false".</span></span>
  - <span data-ttu-id="71db6-197">如果 *expr_first* 的类型为 *boolean*，并且 *expr_second* 是值 *为 true* 的常量表达式，则 *expr* 之后的明确赋值状态与逻辑求反表达式后 *v* 的明确赋值状态相同 `!expr_first` 。</span><span class="sxs-lookup"><span data-stu-id="71db6-197">If *expr_first* is of type *boolean*, and *expr_second* is a constant expression with value *true*, then the definite assignment state after *expr* is the same as the definite assignment state of *v* after the logical negation expression `!expr_first`.</span></span>
  - <span data-ttu-id="71db6-198">如果 *expr_first* 的类型为 *boolean*， *expr_second* 是值为 *false* 的常量表达式，则 *expr* 之后的明确赋值状态与 *expr_first* 后的明确赋值状态相同。</span><span class="sxs-lookup"><span data-stu-id="71db6-198">If *expr_first* is of type *boolean*, and *expr_second* is a constant expression with value *false*, then the definite assignment state after *expr* is the same as the definite assignment state after *expr_first*.</span></span>

<span data-ttu-id="71db6-199">本部分中的所有上述规则都是可交换的，这意味着，如果规则在窗体中计算时应用 `expr_second op expr_first` ，则它也适用于格式 `expr_first op expr_second` 。</span><span class="sxs-lookup"><span data-stu-id="71db6-199">All of the above rules in this section are commutative, meaning that if a rule applies when evaluated in the form `expr_second op expr_first`, it also applies in the form `expr_first op expr_second`.</span></span>

### <a name="remarks"></a><span data-ttu-id="71db6-200">备注</span><span class="sxs-lookup"><span data-stu-id="71db6-200">Remarks</span></span>
<span data-ttu-id="71db6-201">这些规则表示的一般理念是：</span><span class="sxs-lookup"><span data-stu-id="71db6-201">The general idea expressed by these rules is:</span></span>
- <span data-ttu-id="71db6-202">如果将条件访问与进行比较 `null` ，则我们知道如果比较结果为 `false`</span><span class="sxs-lookup"><span data-stu-id="71db6-202">if a conditional access is compared to `null`, then we know the operations definitely occurred if the result of the comparison is `false`</span></span>
- <span data-ttu-id="71db6-203">如果将条件访问与不可以为 null 的值类型或非 null 常量进行比较，则我们知道如果比较结果为，则确实会发生运算 `true` 。</span><span class="sxs-lookup"><span data-stu-id="71db6-203">if a conditional access is compared to a non-nullable value type or a non-null constant, then we know the operations definitely occurred if the result of the comparison is `true`.</span></span>
- <span data-ttu-id="71db6-204">由于我们无法信任用户定义的运算符来提供有关初始化安全问题的可靠答案，因此仅当使用预定义运算符时，新规则才适用 `==` / `!=` 。</span><span class="sxs-lookup"><span data-stu-id="71db6-204">since we can't trust user-defined operators to provide reliable answers where initialization safety is concerned, the new rules only apply when a predefined `==`/`!=` operator is in use.</span></span>

<span data-ttu-id="71db6-205">我们可能最终会希望将这些规则细化到在成员访问或调用结束时出现的条件状态的线程。</span><span class="sxs-lookup"><span data-stu-id="71db6-205">We may eventually want to refine these rules to thread through conditional state which is present at the end of a member access or call.</span></span> <span data-ttu-id="71db6-206">这种情况在明确的赋值中并不真正发生，但在存在和类似的属性中，它们会以可为 null 的形式出现 `[NotNullWhen(true)]` 。</span><span class="sxs-lookup"><span data-stu-id="71db6-206">Such scenarios don't really happen in definite assignment, but they do happen in nullable in the presence of `[NotNullWhen(true)]` and similar attributes.</span></span> <span data-ttu-id="71db6-207">`bool`除了处理/non-null 常量外，这还需要对常量进行特殊处理 `null` 。</span><span class="sxs-lookup"><span data-stu-id="71db6-207">This would require special handling for `bool` constants in addition to just handling for `null`/non-null constants.</span></span>

<span data-ttu-id="71db6-208">这些规则的一些后果：</span><span class="sxs-lookup"><span data-stu-id="71db6-208">Some consequences of these rules:</span></span>
- <span data-ttu-id="71db6-209">`if (a?.b(out var x) == true)) x() else x();` "else" 分支中会出错</span><span class="sxs-lookup"><span data-stu-id="71db6-209">`if (a?.b(out var x) == true)) x() else x();` will error in the 'else' branch</span></span>
- <span data-ttu-id="71db6-210">`if (a?.b(out var x) == 42)) x() else x();` "else" 分支中会出错</span><span class="sxs-lookup"><span data-stu-id="71db6-210">`if (a?.b(out var x) == 42)) x() else x();` will error in the 'else' branch</span></span>
- <span data-ttu-id="71db6-211">`if (a?.b(out var x) == false)) x() else x();` "else" 分支中会出错</span><span class="sxs-lookup"><span data-stu-id="71db6-211">`if (a?.b(out var x) == false)) x() else x();` will error in the 'else' branch</span></span>
- <span data-ttu-id="71db6-212">`if (a?.b(out var x) == null)) x() else x();` "then" 分支中将出错</span><span class="sxs-lookup"><span data-stu-id="71db6-212">`if (a?.b(out var x) == null)) x() else x();` will error in the 'then' branch</span></span>
- <span data-ttu-id="71db6-213">`if (a?.b(out var x) != true)) x() else x();` "then" 分支中将出错</span><span class="sxs-lookup"><span data-stu-id="71db6-213">`if (a?.b(out var x) != true)) x() else x();` will error in the 'then' branch</span></span>
- <span data-ttu-id="71db6-214">`if (a?.b(out var x) != 42)) x() else x();` "then" 分支中将出错</span><span class="sxs-lookup"><span data-stu-id="71db6-214">`if (a?.b(out var x) != 42)) x() else x();` will error in the 'then' branch</span></span>
- <span data-ttu-id="71db6-215">`if (a?.b(out var x) != false)) x() else x();` "then" 分支中将出错</span><span class="sxs-lookup"><span data-stu-id="71db6-215">`if (a?.b(out var x) != false)) x() else x();` will error in the 'then' branch</span></span>
- <span data-ttu-id="71db6-216">`if (a?.b(out var x) != null)) x() else x();` "else" 分支中会出错</span><span class="sxs-lookup"><span data-stu-id="71db6-216">`if (a?.b(out var x) != null)) x() else x();` will error in the 'else' branch</span></span>

## <a name="is-operator-and-is-pattern-expressions"></a><span data-ttu-id="71db6-217">`is` 运算符和 `is` 模式表达式</span><span class="sxs-lookup"><span data-stu-id="71db6-217">`is` operator and `is` pattern expressions</span></span>
<span data-ttu-id="71db6-218">我们引入了一个新的区段 **`is` 运算符和 `is` 模式表达式**。</span><span class="sxs-lookup"><span data-stu-id="71db6-218">We introduce a new section **`is` operator and `is` pattern expressions**.</span></span>

<span data-ttu-id="71db6-219">对于窗体 *的表达式表达式* `E is T` ，其中 *T* 是任意类型或模式</span><span class="sxs-lookup"><span data-stu-id="71db6-219">For an expression *expr* of the form `E is T`, where *T* is any type or pattern</span></span>
- <span data-ttu-id="71db6-220">*E* 前 *v* 的明确赋值状态与 *expr* 之前的 *v* 的明确赋值状态相同。</span><span class="sxs-lookup"><span data-stu-id="71db6-220">The definite assignment state of *v* before *E* is the same as the definite assignment state of *v* before *expr*.</span></span>
- <span data-ttu-id="71db6-221">*Expr* 后 *v* 的明确赋值状态由确定：</span><span class="sxs-lookup"><span data-stu-id="71db6-221">The definite assignment state of *v* after *expr* is determined by:</span></span>
  - <span data-ttu-id="71db6-222">如果 *E* 直接包含一个 null 条件表达式，而与非条件对应的 *e <sub>0</sub>* 的 *状态为 "* 明确赋值"，并且 `T` 是仅匹配非 null 输入的任何类型或模式，则在 *expr* 后 *v* 的状态为 "在 true 时明确赋值"。</span><span class="sxs-lookup"><span data-stu-id="71db6-222">If *E* directly contains a null-conditional expression, and the state of *v* after the non-conditional counterpart *E <sub>0</sub>* is "definitely assigned", and `T` is any type or a pattern that only matches a non-null input, then the state of *v* after *expr* is "definitely assigned when true".</span></span>
  - <span data-ttu-id="71db6-223">如果 *E* 直接包含一个 null 条件表达式，而与非条件对应的 *e <sub>0</sub>* 的 *状态为 "* 明确赋值"，并且 `T` 是仅匹配 null 输入的模式，则在 *expr* 后 *v* 的状态为 "当为 false 时明确赋值"。</span><span class="sxs-lookup"><span data-stu-id="71db6-223">If *E* directly contains a null-conditional expression, and the state of *v* after the non-conditional counterpart *E <sub>0</sub>* is "definitely assigned", and `T` is a pattern which only matches a null input, then the state of *v* after *expr* is "definitely assigned when false".</span></span>
  - <span data-ttu-id="71db6-224">如果 *E* 的类型为布尔值并且 `T` 是常量模式 `true` ，则在 *expr* 后 *v* 的明确赋值状态与 E 后 *v* 的明确赋值状态相同。</span><span class="sxs-lookup"><span data-stu-id="71db6-224">If *E* is of type boolean and `T` is the constant pattern `true`, then the definite assignment state of *v* after *expr* is the same as the definite assignment state of *v* after E.</span></span>
  - <span data-ttu-id="71db6-225">如果 *E* 的类型为布尔值并且 `T` 是常量模式 `false` ，则在 *expr* 后 *v* 的明确赋值状态与逻辑求反表达式后 *v* 的明确赋值状态相同 `!expr` 。</span><span class="sxs-lookup"><span data-stu-id="71db6-225">If *E* is of type boolean and `T` is the constant pattern `false`, then the definite assignment state of *v* after *expr* is the same as the definite assignment state of *v* after the logical negation expression `!expr`.</span></span>
  - <span data-ttu-id="71db6-226">否则，如果在 E 后， *v* 的明确赋值状态为 "明确赋值"，则在 *expr* 后 *v* 的明确赋值状态为 "明确赋值"。</span><span class="sxs-lookup"><span data-stu-id="71db6-226">Otherwise, if the definite assignment state of *v* after E is "definitely assigned", then the definite assignment state of *v* after *expr* is "definitely assigned".</span></span>

### <a name="remarks"></a><span data-ttu-id="71db6-227">备注</span><span class="sxs-lookup"><span data-stu-id="71db6-227">Remarks</span></span>

<span data-ttu-id="71db6-228">本部分旨在解决与上述部分类似的情形 `==` / `!=` 。</span><span class="sxs-lookup"><span data-stu-id="71db6-228">This section is meant to address similar scenarios as in the `==`/`!=` section above.</span></span>
<span data-ttu-id="71db6-229">此规范不处理递归模式，例如 `(a?.b(out x), c?.d(out y)) is (object, object)` 。</span><span class="sxs-lookup"><span data-stu-id="71db6-229">This specification does not address recursive patterns, e.g. `(a?.b(out x), c?.d(out y)) is (object, object)`.</span></span> <span data-ttu-id="71db6-230">如果时间允许，则会出现此类支持。</span><span class="sxs-lookup"><span data-stu-id="71db6-230">Such support may come later if time permits.</span></span>

# <a name="additional-scenarios"></a><span data-ttu-id="71db6-231">其他方案</span><span class="sxs-lookup"><span data-stu-id="71db6-231">Additional scenarios</span></span>

<span data-ttu-id="71db6-232">此规范当前不处理涉及模式开关表达式和 switch 语句的方案。</span><span class="sxs-lookup"><span data-stu-id="71db6-232">This specification doesn't currently address scenarios involving pattern switch expressions and switch statements.</span></span> <span data-ttu-id="71db6-233">例如：</span><span class="sxs-lookup"><span data-stu-id="71db6-233">For example:</span></span>

```cs
_ = c?.M(out object obj4) switch
{
    not null => obj4.ToString() // undesired error
};
```

<span data-ttu-id="71db6-234">如果时间允许，可能会出现这种情况。</span><span class="sxs-lookup"><span data-stu-id="71db6-234">It seems like support for this could come later if time permits.</span></span>

<span data-ttu-id="71db6-235">存在几种可为 null 的 bug 类别，这需要我们实质上增加模式分析的复杂性。</span><span class="sxs-lookup"><span data-stu-id="71db6-235">There have been several categories of bugs filed for nullable which require we essentially increase the sophistication of pattern analysis.</span></span> <span data-ttu-id="71db6-236">我们所做的任何决定会提高明确的分配，还会将其转到可为 null。</span><span class="sxs-lookup"><span data-stu-id="71db6-236">It is likely that any ruling we make which improves definite assignment would also be carried over to nullable.</span></span>

https://github.com/dotnet/roslyn/issues/49353  
https://github.com/dotnet/roslyn/issues/46819  
https://github.com/dotnet/roslyn/issues/44127

## <a name="drawbacks"></a><span data-ttu-id="71db6-237">缺点</span><span class="sxs-lookup"><span data-stu-id="71db6-237">Drawbacks</span></span>
[drawbacks]: #drawbacks

<span data-ttu-id="71db6-238">当一般流分析状态应该向上传播时，分析 "向下传递" 并具有条件访问的特殊识别会感到奇怪。</span><span class="sxs-lookup"><span data-stu-id="71db6-238">It feels odd to have the analysis "reach down" and have special recognition of conditional accesses, when typically flow analysis state is supposed to propagate upward.</span></span> <span data-ttu-id="71db6-239">我们关心此类解决方案如何与可能会执行 null 检查的未来语言功能不厌其烦相交。</span><span class="sxs-lookup"><span data-stu-id="71db6-239">We are concerned about how a solution like this could intersect painfully with possible future language features that do null checks.</span></span>

## <a name="alternatives"></a><span data-ttu-id="71db6-240">备选方法</span><span class="sxs-lookup"><span data-stu-id="71db6-240">Alternatives</span></span>
[alternatives]: #alternatives

<span data-ttu-id="71db6-241">此建议的两种替代方法：</span><span class="sxs-lookup"><span data-stu-id="71db6-241">Two alternatives to this proposal:</span></span>
1. <span data-ttu-id="71db6-242">向语言和编译器介绍 "null 时的状态" 和 "非 null 时的状态"。</span><span class="sxs-lookup"><span data-stu-id="71db6-242">Introduce "state when null" and "state when not null" to the language and compiler.</span></span> <span data-ttu-id="71db6-243">对于我们尝试解决的方案来说，这是一项很大的努力，但我们仍有可能实现上述建议，然后在不中断人员的情况下，转到 "null/not null" 模型。</span><span class="sxs-lookup"><span data-stu-id="71db6-243">This has been judged to be too much effort for the scenarios we are trying to solve, but that we could potentially implement the above proposal and then move to a "state when null/not null" model later on without breaking people.</span></span>
2. <span data-ttu-id="71db6-244">不执行任何操作。</span><span class="sxs-lookup"><span data-stu-id="71db6-244">Do nothing.</span></span>

## <a name="unresolved-questions"></a><span data-ttu-id="71db6-245">未解决的问题</span><span class="sxs-lookup"><span data-stu-id="71db6-245">Unresolved questions</span></span>
[unresolved]: #unresolved-questions

<span data-ttu-id="71db6-246">应指定的 switch 表达式有影响： https://github.com/dotnet/csharplang/discussions/4240#discussioncomment-343395</span><span class="sxs-lookup"><span data-stu-id="71db6-246">There are impacts on switch expressions that should be specified: https://github.com/dotnet/csharplang/discussions/4240#discussioncomment-343395</span></span>

## <a name="design-meetings"></a><span data-ttu-id="71db6-247">设计会议</span><span class="sxs-lookup"><span data-stu-id="71db6-247">Design meetings</span></span>

https://github.com/dotnet/csharplang/discussions/4243

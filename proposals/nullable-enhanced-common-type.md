---
ms.openlocfilehash: 7d98694ef42884cc508558955f068f59181d9568
ms.sourcegitcommit: ae69a53cf0cd57b3bcd17263a33a317a2c1dd205
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/11/2021
ms.locfileid: "103026025"
---
# <a name="nullable-enhanced-common-type"></a><span data-ttu-id="ecfe3-101">Nullable-Enhanced 通用类型</span><span class="sxs-lookup"><span data-stu-id="ecfe3-101">Nullable-Enhanced Common Type</span></span>

* <span data-ttu-id="ecfe3-102">[x] 建议</span><span class="sxs-lookup"><span data-stu-id="ecfe3-102">[x] Proposed</span></span>
* <span data-ttu-id="ecfe3-103">[] 原型：无</span><span class="sxs-lookup"><span data-stu-id="ecfe3-103">[ ] Prototype: None</span></span>
* <span data-ttu-id="ecfe3-104">[] 实现：无</span><span class="sxs-lookup"><span data-stu-id="ecfe3-104">[ ] Implementation: None</span></span>
* <span data-ttu-id="ecfe3-105">[] 规范：请参见下面</span><span class="sxs-lookup"><span data-stu-id="ecfe3-105">[ ] Specification: See below</span></span>

## <a name="summary"></a><span data-ttu-id="ecfe3-106">总结</span><span class="sxs-lookup"><span data-stu-id="ecfe3-106">Summary</span></span>
[summary]: #summary

<span data-ttu-id="ecfe3-107">在某些情况下，当前通用类型算法结果是非常直观的，并会导致程序员将冗余强制转换为代码。</span><span class="sxs-lookup"><span data-stu-id="ecfe3-107">There is a situation in which the current common-type algorithm results are counter-intuitive, and results in the programmer adding what feels like a redundant cast to the code.</span></span> <span data-ttu-id="ecfe3-108">进行此更改后，表达式（如） `condition ? 1 : null` 将导致类型的值，例如， `int?` `condition ? x : 1.0` where `x` 为的类型 `int?` 将导致类型的值 `double?` 。</span><span class="sxs-lookup"><span data-stu-id="ecfe3-108">With this change, an expression such as `condition ? 1 : null` would result in a value of type `int?`, and an expression such as `condition ? x : 1.0` where `x` is of type `int?` would result in a value of type `double?`.</span></span>

## <a name="motivation"></a><span data-ttu-id="ecfe3-109">动机</span><span class="sxs-lookup"><span data-stu-id="ecfe3-109">Motivation</span></span>
[motivation]: #motivation

<span data-ttu-id="ecfe3-110">这是类似于程序员的常见原因，如不必要的样板代码。</span><span class="sxs-lookup"><span data-stu-id="ecfe3-110">This is a common cause of what feels to the programmer like needless boilerplate code.</span></span>

## <a name="detailed-design"></a><span data-ttu-id="ecfe3-111">详细设计</span><span class="sxs-lookup"><span data-stu-id="ecfe3-111">Detailed design</span></span>
[design]: #detailed-design

<span data-ttu-id="ecfe3-112">我们修改了用于 [查找一组表达式的最佳通用类型的](https://github.com/dotnet/csharplang/blob/master/spec/expressions.md#finding-the-best-common-type-of-a-set-of-expressions) 规范，以影响下列情况：</span><span class="sxs-lookup"><span data-stu-id="ecfe3-112">We modify the specification for [finding the best common type of a set of expressions](https://github.com/dotnet/csharplang/blob/master/spec/expressions.md#finding-the-best-common-type-of-a-set-of-expressions) to affect the following situations:</span></span>

- <span data-ttu-id="ecfe3-113">如果一个表达式是不可以为 null 的值类型 `T` ，另一个表达式为 null 文本，则结果为类型 `T?` 。</span><span class="sxs-lookup"><span data-stu-id="ecfe3-113">If one expression is of a non-nullable value type `T` and the other is a null literal, the result is of type `T?`.</span></span>
- <span data-ttu-id="ecfe3-114">如果一个表达式是可以为 null 的值类型，另一个表达式是 `T?` 值类型 `U` ，并且存在从到的隐式 `T` 转换 `U` ，则结果为类型 `U?` 。</span><span class="sxs-lookup"><span data-stu-id="ecfe3-114">If one expression is of a nullable value type `T?` and the other is of a value type `U`, and there is an implicit conversion from `T` to `U`, then the result is of type `U?`.</span></span>

<span data-ttu-id="ecfe3-115">这应该会影响语言的以下方面：</span><span class="sxs-lookup"><span data-stu-id="ecfe3-115">This is expected to affect the following aspects of the language:</span></span>

- <span data-ttu-id="ecfe3-116">[三元表达式](https://github.com/dotnet/csharplang/blob/master/spec/expressions.md#conditional-operator)</span><span class="sxs-lookup"><span data-stu-id="ecfe3-116">the [ternary expression](https://github.com/dotnet/csharplang/blob/master/spec/expressions.md#conditional-operator)</span></span>
- <span data-ttu-id="ecfe3-117">隐式类型的 [数组创建表达式](https://github.com/dotnet/csharplang/blob/master/spec/expressions.md#array-creation-expressions)</span><span class="sxs-lookup"><span data-stu-id="ecfe3-117">implicitly typed [array creation expression](https://github.com/dotnet/csharplang/blob/master/spec/expressions.md#array-creation-expressions)</span></span>
- <span data-ttu-id="ecfe3-118">推断 [lambda 的返回类型](https://github.com/dotnet/csharplang/blob/master/spec/expressions.md#inferred-return-type) 推断类型</span><span class="sxs-lookup"><span data-stu-id="ecfe3-118">inferring the [return type of a lambda](https://github.com/dotnet/csharplang/blob/master/spec/expressions.md#inferred-return-type) for type inference</span></span>
- <span data-ttu-id="ecfe3-119">涉及泛型的事例，如调用 `M<T>(T a, T b)` 为 `M(1, null)` 。</span><span class="sxs-lookup"><span data-stu-id="ecfe3-119">cases involving generics, such as invoking `M<T>(T a, T b)` as `M(1, null)`.</span></span>

<span data-ttu-id="ecfe3-120">更准确地讲，我们更改了规范的以下部分 (以粗体显示，删除线删除) ：</span><span class="sxs-lookup"><span data-stu-id="ecfe3-120">More precisely, we change the following sections of the specification (insertions in bold, deletions in strikethrough):</span></span>

> #### <a name="output-type-inferences"></a><span data-ttu-id="ecfe3-121">输出类型推断</span><span class="sxs-lookup"><span data-stu-id="ecfe3-121">Output type inferences</span></span>
> 
> <span data-ttu-id="ecfe3-122">*从* 表达式到类型的 *输出类型推理* `E`  `T` 按以下方式进行：</span><span class="sxs-lookup"><span data-stu-id="ecfe3-122">An *output type inference* is made *from* an expression `E` *to* a type `T` in the following way:</span></span>
> 
> *  <span data-ttu-id="ecfe3-123">如果 `E` 是具有推断返回类型的匿名函数 `U` ([推断出的返回](../spec/expressions.md#inferred-return-type)类型) 并且 `T` 是具有返回类型的委托类型或表达式树类型 `Tb` ，则 *从* 到进行) [下限](../spec/expressions.md#lower-bound-inferences)*推理* (`U`  `Tb` 。</span><span class="sxs-lookup"><span data-stu-id="ecfe3-123">If `E` is an anonymous function with inferred return type  `U` ([Inferred return type](../spec/expressions.md#inferred-return-type)) and `T` is a delegate type or expression tree type with return type `Tb`, then a *lower-bound inference* ([Lower-bound inferences](../spec/expressions.md#lower-bound-inferences)) is made *from* `U` *to* `Tb`.</span></span>
> *  <span data-ttu-id="ecfe3-124">否则，如果 `E` 是方法组，并且 `T` 是具有参数类型和返回类型的委托类型或表达式树类型 `T1...Tk` `Tb` ，并且具有类型的重载解析 `E` `T1...Tk` 生成具有返回类型的单个方法 `U` ，则将 *从* 到进行 *下限推理* `U`  `Tb` 。</span><span class="sxs-lookup"><span data-stu-id="ecfe3-124">Otherwise, if `E` is a method group and `T` is a delegate type or expression tree type with parameter types `T1...Tk` and return type `Tb`, and overload resolution of `E` with the types `T1...Tk` yields a single method with return type `U`, then a *lower-bound inference* is made *from* `U` *to* `Tb`.</span></span>
> *  <span data-ttu-id="ecfe3-125">\* \* 否则，如果 `E` 是具有可为 null 的值类型的表达式 `U?` ，则将 *从* 到进行 *下限推理*， `U`  `T` 并将 *null 绑定* 添加到中 `T` 。</span><span class="sxs-lookup"><span data-stu-id="ecfe3-125">\*\*Otherwise, if `E` is an expression with nullable value type `U?`, then a *lower-bound inference* is made *from* `U` *to* `T` and a *null bound* is added to `T`.</span></span> **
> *  <span data-ttu-id="ecfe3-126">否则，如果 `E` 是类型为的表达式 `U` ，则将 *从* 到进行 *下限推理* `U`  `T` 。</span><span class="sxs-lookup"><span data-stu-id="ecfe3-126">Otherwise, if `E` is an expression with type `U`, then a *lower-bound inference* is made *from* `U` *to* `T`.</span></span>
> *  <span data-ttu-id="ecfe3-127">**否则，如果 `E` 是具有值的常量表达式 `null` ，则会将 *null 绑定* 添加到 `T`**</span><span class="sxs-lookup"><span data-stu-id="ecfe3-127">**Otherwise, if `E` is a constant expression with value `null`, then a *null bound* is added to `T`**</span></span> 
> *  <span data-ttu-id="ecfe3-128">否则，不进行推断。</span><span class="sxs-lookup"><span data-stu-id="ecfe3-128">Otherwise, no inferences are made.</span></span>

> #### <a name="fixing"></a><span data-ttu-id="ecfe3-129">修正 </span><span class="sxs-lookup"><span data-stu-id="ecfe3-129">Fixing</span></span>
> 
> <span data-ttu-id="ecfe3-130">具有一组界限的未 *固定* 类型变量 `Xi` *固定* 如下：</span><span class="sxs-lookup"><span data-stu-id="ecfe3-130">An *unfixed* type variable `Xi` with a set of bounds is *fixed* as follows:</span></span>
> 
> *  <span data-ttu-id="ecfe3-131">*候选类型* 集 `Uj` 作为的边界集中的所有类型的集合开始 `Xi` 。</span><span class="sxs-lookup"><span data-stu-id="ecfe3-131">The set of *candidate types* `Uj` starts out as the set of all types in the set of bounds for `Xi`.</span></span>
> *  <span data-ttu-id="ecfe3-132">接下来，我们依次检查每个绑定 `Xi` ：对于与 `U` `Xi` `Uj` `U` 从候选集删除的所有类型不同的每个完全绑定，都是如此。</span><span class="sxs-lookup"><span data-stu-id="ecfe3-132">We then examine each bound for `Xi` in turn: For each exact bound `U` of `Xi` all types `Uj` which are not identical to `U` are removed from the candidate set.</span></span> <span data-ttu-id="ecfe3-133">对于 `U` `Xi` 不从其隐式转换的所有类型的每个下限， `Uj`  `U` 从候选集删除。</span><span class="sxs-lookup"><span data-stu-id="ecfe3-133">For each lower bound `U` of `Xi` all types `Uj` to which there is *not* an implicit conversion from `U` are removed from the candidate set.</span></span> <span data-ttu-id="ecfe3-134">对于 `U` 不将隐式转换为的所有类型的每个上限，从 `Xi` `Uj`  `U` 候选集删除。</span><span class="sxs-lookup"><span data-stu-id="ecfe3-134">For each upper bound `U` of `Xi` all types `Uj` from which there is *not* an implicit conversion to `U` are removed from the candidate set.</span></span>
> *  <span data-ttu-id="ecfe3-135">如果其余的候选类型中 `Uj` 有一个唯一类型 `V` ，其中存在到所有其他候选类型的隐式转换，则~~ `Xi` 修复为 `V` 。~~</span><span class="sxs-lookup"><span data-stu-id="ecfe3-135">If among the remaining candidate types `Uj` there is a unique type `V` from which there is an implicit conversion to all the other candidate types, then ~~`Xi` is fixed to `V`.~~</span></span>
>     -  <span data-ttu-id="ecfe3-136">**如果 `V` 是值类型且存在 *null 界限* `Xi` ，则 `Xi` 固定到 `V?`**</span><span class="sxs-lookup"><span data-stu-id="ecfe3-136">**If `V` is a value type and there is a *null bound* for `Xi`, then `Xi` is fixed to `V?`**</span></span>
>     -  <span data-ttu-id="ecfe3-137">**否则   `Xi` ，固定到 `V`**</span><span class="sxs-lookup"><span data-stu-id="ecfe3-137">**Otherwise   `Xi` is fixed to `V`**</span></span>
> *  <span data-ttu-id="ecfe3-138">否则，类型推理将失败。</span><span class="sxs-lookup"><span data-stu-id="ecfe3-138">Otherwise, type inference fails.</span></span>

## <a name="drawbacks"></a><span data-ttu-id="ecfe3-139">缺点</span><span class="sxs-lookup"><span data-stu-id="ecfe3-139">Drawbacks</span></span>
[drawbacks]: #drawbacks

<span data-ttu-id="ecfe3-140">此建议可能会导致某些不兼容问题。</span><span class="sxs-lookup"><span data-stu-id="ecfe3-140">There may be some incompatibilities introduced by this proposal.</span></span>

## <a name="alternatives"></a><span data-ttu-id="ecfe3-141">备选方法</span><span class="sxs-lookup"><span data-stu-id="ecfe3-141">Alternatives</span></span>
[alternatives]: #alternatives

<span data-ttu-id="ecfe3-142">无。</span><span class="sxs-lookup"><span data-stu-id="ecfe3-142">None.</span></span>

## <a name="unresolved-questions"></a><span data-ttu-id="ecfe3-143">未解决的问题</span><span class="sxs-lookup"><span data-stu-id="ecfe3-143">Unresolved questions</span></span>
[unresolved]: #unresolved-questions

- <span data-ttu-id="ecfe3-144">[] 此提议引入的不兼容性严重性（如果有）以及如何进行仲裁？</span><span class="sxs-lookup"><span data-stu-id="ecfe3-144">[ ] What is the severity of incompatibility introduced by this proposal, if any, and how can it be moderated?</span></span>

## <a name="design-meetings"></a><span data-ttu-id="ecfe3-145">设计会议</span><span class="sxs-lookup"><span data-stu-id="ecfe3-145">Design meetings</span></span>

<span data-ttu-id="ecfe3-146">无。</span><span class="sxs-lookup"><span data-stu-id="ecfe3-146">None.</span></span>

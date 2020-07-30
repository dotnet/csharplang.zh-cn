---
ms.openlocfilehash: f5a97f699b552094b8b21aeceabe2aac0a478d98
ms.sourcegitcommit: a88d56e3131d7a94c65e637c276379541a3cd491
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/30/2020
ms.locfileid: "87434459"
---
# <a name="target-typed-conditional-expression"></a><span data-ttu-id="93f7a-101">目标类型的条件表达式</span><span class="sxs-lookup"><span data-stu-id="93f7a-101">Target-Typed Conditional Expression</span></span>

## <a name="conditional-expression-conversion"></a><span data-ttu-id="93f7a-102">条件表达式转换</span><span class="sxs-lookup"><span data-stu-id="93f7a-102">Conditional Expression Conversion</span></span>

<span data-ttu-id="93f7a-103">对于条件表达式 `c ? e1 : e2` ，当</span><span class="sxs-lookup"><span data-stu-id="93f7a-103">For a conditional expression `c ? e1 : e2`, when</span></span>

1. <span data-ttu-id="93f7a-104">和没有通用类型 `e1` `e2` ，或</span><span class="sxs-lookup"><span data-stu-id="93f7a-104">there is no common type for `e1` and `e2`, or</span></span>
2. <span data-ttu-id="93f7a-105">，它的通用类型为，但其中一个表达式 `e1` 为 `e2` ，或者没有到该类型的隐式转换</span><span class="sxs-lookup"><span data-stu-id="93f7a-105">for which a common type exists but one of the expressions `e1` or `e2` has no implicit conversion to that type</span></span>

<span data-ttu-id="93f7a-106">我们定义了一个新的隐式*条件表达式转换*，该转换允许从条件表达式到的任何类型的隐式转换 `T` （从到的转换，以及从到的转换） `e1` `T` `e2` `T` 。</span><span class="sxs-lookup"><span data-stu-id="93f7a-106">we define a new implicit *conditional expression conversion* that permits an implicit conversion from the conditional expression to any type `T` for which there is a conversion-from-expression from `e1` to `T` and also from `e2` to `T`.</span></span>  <span data-ttu-id="93f7a-107">如果条件表达式在和之间既没有通用类型，也不符合 `e1` `e2` *条件表达式转换*，则是错误的。</span><span class="sxs-lookup"><span data-stu-id="93f7a-107">It is an error if a conditional expression neither has a common type between `e1` and `e2` nor is subject to a *conditional expression conversion*.</span></span>

## <a name="better-conversion-from-expression"></a><span data-ttu-id="93f7a-108">表达式的更好转换</span><span class="sxs-lookup"><span data-stu-id="93f7a-108">Better Conversion from Expression</span></span>

<span data-ttu-id="93f7a-109">我们更改</span><span class="sxs-lookup"><span data-stu-id="93f7a-109">We change</span></span>

> #### <a name="better-conversion-from-expression"></a><span data-ttu-id="93f7a-110">表达式的更好转换</span><span class="sxs-lookup"><span data-stu-id="93f7a-110">Better conversion from expression</span></span>
> 
> <span data-ttu-id="93f7a-111">给定了 `C1` 从表达式转换为类型的隐式转换 `E` `T1` ，以及 `C2` 从表达式转换为类型的隐式转换， `E` `T2` `C1` 是比***better conversion*** `C2` `E` 不完全匹配 `T2` 且至少包含以下其中一项的更好的转换：</span><span class="sxs-lookup"><span data-stu-id="93f7a-111">Given an implicit conversion `C1` that converts from an expression `E` to a type `T1`, and an implicit conversion `C2` that converts from an expression `E` to a type `T2`, `C1` is a ***better conversion*** than `C2` if `E` does not exactly match `T2` and at least one of the following holds:</span></span>
> 
> * <span data-ttu-id="93f7a-112">`E`完全匹配 `T1` （[完全匹配的表达式](../../spec/expressions.md#exactly-matching-expression)）</span><span class="sxs-lookup"><span data-stu-id="93f7a-112">`E` exactly matches `T1` ([Exactly matching Expression](../../spec/expressions.md#exactly-matching-expression))</span></span>
> * <span data-ttu-id="93f7a-113">`T1`比更好的转换目标 `T2` （[更好的转换目标](../../spec/expressions.md#better-conversion-target)）</span><span class="sxs-lookup"><span data-stu-id="93f7a-113">`T1` is a better conversion target than `T2` ([Better conversion target](../../spec/expressions.md#better-conversion-target))</span></span>

<span data-ttu-id="93f7a-114">to</span><span class="sxs-lookup"><span data-stu-id="93f7a-114">to</span></span>

> #### <a name="better-conversion-from-expression"></a><span data-ttu-id="93f7a-115">表达式的更好转换</span><span class="sxs-lookup"><span data-stu-id="93f7a-115">Better conversion from expression</span></span>
> 
> <span data-ttu-id="93f7a-116">给定了 `C1` 从表达式转换为类型的隐式转换 `E` `T1` ，以及 `C2` 从表达式转换为类型的隐式转换， `E` `T2` `C1` 是比***better conversion*** `C2` `E` 不完全匹配 `T2` 且至少包含以下其中一项的更好的转换：</span><span class="sxs-lookup"><span data-stu-id="93f7a-116">Given an implicit conversion `C1` that converts from an expression `E` to a type `T1`, and an implicit conversion `C2` that converts from an expression `E` to a type `T2`, `C1` is a ***better conversion*** than `C2` if `E` does not exactly match `T2` and at least one of the following holds:</span></span>
> 
> * <span data-ttu-id="93f7a-117">`E`完全匹配 `T1` （[完全匹配的表达式](../../spec/expressions.md#exactly-matching-expression)）</span><span class="sxs-lookup"><span data-stu-id="93f7a-117">`E` exactly matches `T1` ([Exactly matching Expression](../../spec/expressions.md#exactly-matching-expression))</span></span>
> * <span data-ttu-id="93f7a-118">\**`C1`不是一个*条件表达式转换\*，并且 `C2` 是一个 \* 条件表达式转换 \* \* \*。</span><span class="sxs-lookup"><span data-stu-id="93f7a-118">\*\*`C1` is not a *conditional expression conversion* and `C2` is a \*conditional expression conversion\*\*\*.</span></span>
> * <span data-ttu-id="93f7a-119">`T1`比 `T2` （[更好的转换目标](../../spec/expressions.md#better-conversion-target)） \* \* 更好的转换目标，并且 `C1` 和 `C2` 都是*条件表达式*转换，或者两者都不是 \* 条件表达式转换 \* \* \*。</span><span class="sxs-lookup"><span data-stu-id="93f7a-119">`T1` is a better conversion target than `T2` ([Better conversion target](../../spec/expressions.md#better-conversion-target)) \*\*and either `C1` and `C2` are both *conditional expression conversions* or neither is a \*conditional expression conversion\*\*\*.</span></span>

## <a name="cast-expression"></a><span data-ttu-id="93f7a-120">Cast 表达式</span><span class="sxs-lookup"><span data-stu-id="93f7a-120">Cast Expression</span></span>

<span data-ttu-id="93f7a-121">当前 c # 语言规范显示</span><span class="sxs-lookup"><span data-stu-id="93f7a-121">The current C# language specification says</span></span>

> <span data-ttu-id="93f7a-122">窗体的*cast_expression* `(T)E` ，其中 `T` 是一个*类型*并且 `E` 是一个*unary_expression*，它执行到类型的值的显式转换（[显式转换](../../spec/conversions.md#explicit-conversions)） `E` `T` 。</span><span class="sxs-lookup"><span data-stu-id="93f7a-122">A *cast_expression* of the form `(T)E`, where `T` is a *type* and `E` is a *unary_expression*, performs an explicit conversion ([Explicit conversions](../../spec/conversions.md#explicit-conversions)) of the value of `E` to type `T`.</span></span>

<span data-ttu-id="93f7a-123">存在*条件表达式转换*时，可能有多个从到的转换可能 `E` `T` 。</span><span class="sxs-lookup"><span data-stu-id="93f7a-123">In the presence of the *conditional expression conversion* there may be more than one possible conversion from `E` to `T`.</span></span> <span data-ttu-id="93f7a-124">通过添加*条件表达式转换*，我们更喜欢将任何其他转换转换为*条件表达式转换*，并将*条件表达式转换*仅用作最后的手段。</span><span class="sxs-lookup"><span data-stu-id="93f7a-124">With the addition of *conditional expression conversion*, we prefer any other conversion to a *conditional expression conversion*, and use the *conditional expression conversion* only as a last resort.</span></span>

## <a name="design-notes"></a><span data-ttu-id="93f7a-125">设计纪要</span><span class="sxs-lookup"><span data-stu-id="93f7a-125">Design Notes</span></span>

<span data-ttu-id="93f7a-126">*从表达式转换到更好的转换*的原因是处理以下情况：</span><span class="sxs-lookup"><span data-stu-id="93f7a-126">The reason for the change to *Better conversion from expression* is to handle a case such as this:</span></span>

```csharp
M(b ? 1 : 2);

void M(short);
void M(long);
```

<span data-ttu-id="93f7a-127">此方法有两个小缺点。</span><span class="sxs-lookup"><span data-stu-id="93f7a-127">This approach does have two small downsides.</span></span>  <span data-ttu-id="93f7a-128">首先，它与 switch 表达式并不完全相同：</span><span class="sxs-lookup"><span data-stu-id="93f7a-128">First, it is not quite the same as the switch expression:</span></span>

```csharp
M(b ? 1 : 2); // calls M(long)
M(b switch { true => 1, false => 2 }); // calls M(short)
```

<span data-ttu-id="93f7a-129">这仍是一项重大更改，但其作用域不太可能影响实际程序：</span><span class="sxs-lookup"><span data-stu-id="93f7a-129">This is still a breaking change, but its scope is less likely to affect real programs:</span></span>

```csharp
M(b ? 1 : 2, 1); // calls M(long, long) without this feature; ambiguous with this feature.

M(short, short);
M(long, long);
```

<span data-ttu-id="93f7a-130">这种方法变得不明确，因为的转换 `long` 更适合第一个参数（因为它不使用*条件表达式转换*），但转换为 `short` 更适合于第二个参数（因为 `short` 比*更好的转换目标* `long` ）。</span><span class="sxs-lookup"><span data-stu-id="93f7a-130">This becomes ambiguous because the conversion to `long` is better for the first argument (because it does not use the *conditional expression conversion*), but the conversion to `short` is better for the second argument (because `short` is a *better conversion target* than `long`).</span></span> <span data-ttu-id="93f7a-131">此重大更改看起来不太严重，因为它不会在无提示的情况下更改现有程序的行为。</span><span class="sxs-lookup"><span data-stu-id="93f7a-131">This breaking change seems less serious because it does not silently change the behavior of an existing program.</span></span>

<span data-ttu-id="93f7a-132">转换表达式中的注释的原因是处理以下情况：</span><span class="sxs-lookup"><span data-stu-id="93f7a-132">The reason for the notes on the cast expression is to handle a case such as this:</span></span>

```csharp
_ = (short)(b ? 1 : 2);
```

<span data-ttu-id="93f7a-133">此程序当前使用从到的显式 `int` 转换 `short` ，并且我们希望保留此程序当前的语言含义。</span><span class="sxs-lookup"><span data-stu-id="93f7a-133">This program currently uses the explicit conversion from `int` to `short`, and we want to preserve the current language meaning of this program.</span></span>  <span data-ttu-id="93f7a-134">此更改将在运行时 unobservable，但在以下情况下，更改将可观察：</span><span class="sxs-lookup"><span data-stu-id="93f7a-134">The change would be unobservable at runtime, but with the following program the change would be observable:</span></span>

```csharp
_ = (A)(b ? c : d);
```

<span data-ttu-id="93f7a-135">其中 `c` 的类型为 `C` `d` ，其类型为 `D` ，并且存在从到的隐式用户定义的 `C` 转换 `D` ， `D` `A` `C` `A` 以及从到的隐式用户定义的转换，以及从到的隐式用户定义的转换。</span><span class="sxs-lookup"><span data-stu-id="93f7a-135">where `c` is of type `C`, `d` is of type `D`, and there is an implicit user-defined conversion from `C` to `D`, and an implicit user-defined conversion from `D` to `A`, and an implicit user-defined conversion from `C` to `A`.</span></span> <span data-ttu-id="93f7a-136">如果在 c # 9.0 之前编译此代码，则在为 true 时， `b` 我们会将从转换为 `c` `D` `A` 。</span><span class="sxs-lookup"><span data-stu-id="93f7a-136">If this code is compiled before C# 9.0, when `b` is true we convert from `c` to `D` then to `A`.</span></span> <span data-ttu-id="93f7a-137">如果使用*条件表达式转换*，则当 `b` 为 true 时，将直接从转换 `c` 为 `A` ，这将执行一系列不同的用户代码。</span><span class="sxs-lookup"><span data-stu-id="93f7a-137">If we use the *conditional expression conversion*, then when `b` is true we convert from `c` to `A` directly, which executes a different sequence of user code.</span></span> <span data-ttu-id="93f7a-138">因此，我们将*条件表达式转换*视为转换中的最后一个手段，以保留现有行为。</span><span class="sxs-lookup"><span data-stu-id="93f7a-138">Therefore we treat the *conditional expression conversion* as a last resort in a cast, to preserve existing behavior.</span></span>

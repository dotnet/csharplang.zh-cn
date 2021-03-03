---
ms.openlocfilehash: 76ca46dbeb912924de85d11da9c89e3865671106
ms.sourcegitcommit: f921de697a44d3ae827f168a8371d7dc3ca66f2d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/02/2021
ms.locfileid: "101641994"
---
# <a name="conversions"></a><span data-ttu-id="84656-101">转换</span><span class="sxs-lookup"><span data-stu-id="84656-101">Conversions</span></span>

<span data-ttu-id="84656-102">\***转换** _ 允许将表达式视为特定类型。</span><span class="sxs-lookup"><span data-stu-id="84656-102">A \***conversion** _ enables an expression to be treated as being of a particular type.</span></span> <span data-ttu-id="84656-103">转换可能会导致将给定类型的表达式视为具有不同的类型，也可能导致不具有类型的表达式获得类型。</span><span class="sxs-lookup"><span data-stu-id="84656-103">A conversion may cause an expression of a given type to be treated as having a different type, or it may cause an expression without a type to get a type.</span></span> <span data-ttu-id="84656-104">转换可以是 _\*_隐式_\*_ 或 _ *_explicit_* \*，这确定是否需要显式强制转换。</span><span class="sxs-lookup"><span data-stu-id="84656-104">Conversions can be _*_implicit_*_ or _\*_explicit_\*\*, and this determines whether an explicit cast is required.</span></span> <span data-ttu-id="84656-105">例如，从类型 `int` 到类型的转换 `long` 是隐式的，因此类型的表达式 `int` 可以隐式对待为类型 `long` 。</span><span class="sxs-lookup"><span data-stu-id="84656-105">For instance, the conversion from type `int` to type `long` is implicit, so expressions of type `int` can implicitly be treated as type `long`.</span></span> <span data-ttu-id="84656-106">相反，从类型 `long` 到类型的转换 `int` 是显式的，因此需要显式强制转换。</span><span class="sxs-lookup"><span data-stu-id="84656-106">The opposite conversion, from type `long` to type `int`, is explicit and so an explicit cast is required.</span></span>

```csharp
int a = 123;
long b = a;         // implicit conversion from int to long
int c = (int) b;    // explicit conversion from long to int
```

<span data-ttu-id="84656-107">某些转换是由语言定义的。</span><span class="sxs-lookup"><span data-stu-id="84656-107">Some conversions are defined by the language.</span></span> <span data-ttu-id="84656-108">程序还可以定义自己的转换 ([用户定义的转换](conversions.md#user-defined-conversions)) 。</span><span class="sxs-lookup"><span data-stu-id="84656-108">Programs may also define their own conversions ([User-defined conversions](conversions.md#user-defined-conversions)).</span></span>

## <a name="implicit-conversions"></a><span data-ttu-id="84656-109">隐式转换</span><span class="sxs-lookup"><span data-stu-id="84656-109">Implicit conversions</span></span>

<span data-ttu-id="84656-110">以下转换归类为隐式转换：</span><span class="sxs-lookup"><span data-stu-id="84656-110">The following conversions are classified as implicit conversions:</span></span>

*  <span data-ttu-id="84656-111">标识转换</span><span class="sxs-lookup"><span data-stu-id="84656-111">Identity conversions</span></span>
*  <span data-ttu-id="84656-112">隐式数值转换</span><span class="sxs-lookup"><span data-stu-id="84656-112">Implicit numeric conversions</span></span>
*  <span data-ttu-id="84656-113">隐式枚举转换</span><span class="sxs-lookup"><span data-stu-id="84656-113">Implicit enumeration conversions</span></span>
*  <span data-ttu-id="84656-114">隐式内插字符串转换</span><span class="sxs-lookup"><span data-stu-id="84656-114">Implicit interpolated string conversions</span></span>
*  <span data-ttu-id="84656-115">隐式可为空转换</span><span class="sxs-lookup"><span data-stu-id="84656-115">Implicit nullable conversions</span></span>
*  <span data-ttu-id="84656-116">Null 文本转换</span><span class="sxs-lookup"><span data-stu-id="84656-116">Null literal conversions</span></span>
*  <span data-ttu-id="84656-117">隐式引用转换</span><span class="sxs-lookup"><span data-stu-id="84656-117">Implicit reference conversions</span></span>
*  <span data-ttu-id="84656-118">装箱转换</span><span class="sxs-lookup"><span data-stu-id="84656-118">Boxing conversions</span></span>
*  <span data-ttu-id="84656-119">隐式动态转换</span><span class="sxs-lookup"><span data-stu-id="84656-119">Implicit dynamic conversions</span></span>
*  <span data-ttu-id="84656-120">隐式常量表达式转换</span><span class="sxs-lookup"><span data-stu-id="84656-120">Implicit constant expression conversions</span></span>
*  <span data-ttu-id="84656-121">用户定义的隐式转换</span><span class="sxs-lookup"><span data-stu-id="84656-121">User-defined implicit conversions</span></span>
*  <span data-ttu-id="84656-122">匿名函数转换</span><span class="sxs-lookup"><span data-stu-id="84656-122">Anonymous function conversions</span></span>
*  <span data-ttu-id="84656-123">方法组转换</span><span class="sxs-lookup"><span data-stu-id="84656-123">Method group conversions</span></span>

<span data-ttu-id="84656-124">隐式转换可能会在多种情况下发生，包括函数成员调用 ([编译时检查动态重载解析](expressions.md#compile-time-checking-of-dynamic-overload-resolution)) 、强制转换表达式 ([强制转换表达式](expressions.md#cast-expressions)) ，并 ([赋值运算符) 赋值运算符](expressions.md#assignment-operators) 。</span><span class="sxs-lookup"><span data-stu-id="84656-124">Implicit conversions can occur in a variety of situations, including function member invocations ([Compile-time checking of dynamic overload resolution](expressions.md#compile-time-checking-of-dynamic-overload-resolution)), cast expressions ([Cast expressions](expressions.md#cast-expressions)), and assignments ([Assignment operators](expressions.md#assignment-operators)).</span></span>

<span data-ttu-id="84656-125">预定义的隐式转换始终会成功，并且永远不会引发异常。</span><span class="sxs-lookup"><span data-stu-id="84656-125">The pre-defined implicit conversions always succeed and never cause exceptions to be thrown.</span></span> <span data-ttu-id="84656-126">正确设计的用户定义隐式转换也应显示这些特征。</span><span class="sxs-lookup"><span data-stu-id="84656-126">Properly designed user-defined implicit conversions should exhibit these characteristics as well.</span></span>

<span data-ttu-id="84656-127">出于转换目的，类型 `object` 和被 `dynamic` 视为等效的。</span><span class="sxs-lookup"><span data-stu-id="84656-127">For the purposes of conversion, the types `object` and `dynamic` are considered equivalent.</span></span>

<span data-ttu-id="84656-128">但是，动态转换 ([隐式动态转换](conversions.md#implicit-dynamic-conversions) 和 [显式动态转换](conversions.md#explicit-dynamic-conversions)) 仅应用于 `dynamic` ([动态类型](types.md#the-dynamic-type)) 的类型的表达式。</span><span class="sxs-lookup"><span data-stu-id="84656-128">However, dynamic conversions ([Implicit dynamic conversions](conversions.md#implicit-dynamic-conversions) and [Explicit dynamic conversions](conversions.md#explicit-dynamic-conversions)) apply only to expressions of type `dynamic` ([The dynamic type](types.md#the-dynamic-type)).</span></span>

### <a name="identity-conversion"></a><span data-ttu-id="84656-129">标识转换</span><span class="sxs-lookup"><span data-stu-id="84656-129">Identity conversion</span></span>

<span data-ttu-id="84656-130">标识转换从任何类型转换为同一类型。</span><span class="sxs-lookup"><span data-stu-id="84656-130">An identity conversion converts from any type to the same type.</span></span> <span data-ttu-id="84656-131">此转换存在，因此，已具有所需类型的实体可被视为可转换为该类型。</span><span class="sxs-lookup"><span data-stu-id="84656-131">This conversion exists such that an entity that already has a required type can be said to be convertible to that type.</span></span>

*  <span data-ttu-id="84656-132">因为和被视为等效的，所以在 `object` `dynamic` 和之间存在一个标识转换， `object` `dynamic` 在将的所有匹配项替换为相同的构造类型之间 `dynamic` `object` 。</span><span class="sxs-lookup"><span data-stu-id="84656-132">Because `object` and `dynamic` are considered equivalent there is an identity conversion between `object` and `dynamic`, and between constructed types that are the same when replacing all occurrences of `dynamic` with `object`.</span></span>

### <a name="implicit-numeric-conversions"></a><span data-ttu-id="84656-133">隐式数值转换</span><span class="sxs-lookup"><span data-stu-id="84656-133">Implicit numeric conversions</span></span>

<span data-ttu-id="84656-134">隐式数值转换为：</span><span class="sxs-lookup"><span data-stu-id="84656-134">The implicit numeric conversions are:</span></span>

*  <span data-ttu-id="84656-135">从 `sbyte` 到、、、、 `short` `int` `long` `float` `double` 或 `decimal` 。</span><span class="sxs-lookup"><span data-stu-id="84656-135">From `sbyte` to `short`, `int`, `long`, `float`, `double`, or `decimal`.</span></span>
*  <span data-ttu-id="84656-136">从到、、、、、、、 `byte` `short` `ushort` `int` `uint` `long` `ulong` `float` `double` 或 `decimal` 。</span><span class="sxs-lookup"><span data-stu-id="84656-136">From `byte` to `short`, `ushort`, `int`, `uint`, `long`, `ulong`, `float`, `double`, or `decimal`.</span></span>
*  <span data-ttu-id="84656-137">从 `short` 到 `int` 、、、 `long` `float` `double` 或 `decimal` 。</span><span class="sxs-lookup"><span data-stu-id="84656-137">From `short` to `int`, `long`, `float`, `double`, or `decimal`.</span></span>
*  <span data-ttu-id="84656-138">从到、、、、、 `ushort` `int` `uint` `long` `ulong` `float` `double` 或 `decimal` 。</span><span class="sxs-lookup"><span data-stu-id="84656-138">From `ushort` to `int`, `uint`, `long`, `ulong`, `float`, `double`, or `decimal`.</span></span>
*  <span data-ttu-id="84656-139">从 `int` 到 `long` 、 `float` 、 `double` 或 `decimal` 。</span><span class="sxs-lookup"><span data-stu-id="84656-139">From `int` to `long`, `float`, `double`, or `decimal`.</span></span>
*  <span data-ttu-id="84656-140">从 `uint` 到 `long` 、、、 `ulong` `float` `double` 或 `decimal` 。</span><span class="sxs-lookup"><span data-stu-id="84656-140">From `uint` to `long`, `ulong`, `float`, `double`, or `decimal`.</span></span>
*  <span data-ttu-id="84656-141">从 `long` 到 `float` 、 `double` 或 `decimal` 。</span><span class="sxs-lookup"><span data-stu-id="84656-141">From `long` to `float`, `double`, or `decimal`.</span></span>
*  <span data-ttu-id="84656-142">从 `ulong` 到 `float` 、 `double` 或 `decimal` 。</span><span class="sxs-lookup"><span data-stu-id="84656-142">From `ulong` to `float`, `double`, or `decimal`.</span></span>
*  <span data-ttu-id="84656-143">从到、、、、、、 `char` `ushort` `int` `uint` `long` `ulong` `float` `double` 或 `decimal` 。</span><span class="sxs-lookup"><span data-stu-id="84656-143">From `char` to `ushort`, `int`, `uint`, `long`, `ulong`, `float`, `double`, or `decimal`.</span></span>
*  <span data-ttu-id="84656-144">从 `float` 到 `double`。</span><span class="sxs-lookup"><span data-stu-id="84656-144">From `float` to `double`.</span></span>

<span data-ttu-id="84656-145">从 `int` 、、 `uint` `long` 或到之间的转换 `ulong` `float` `long` `ulong` `double` 可能会导致精度损失，但永远不会导致数量级损失。</span><span class="sxs-lookup"><span data-stu-id="84656-145">Conversions from `int`, `uint`, `long`, or `ulong` to `float` and from `long` or `ulong` to `double` may cause a loss of precision, but will never cause a loss of magnitude.</span></span> <span data-ttu-id="84656-146">其他隐式数值转换不会丢失任何信息。</span><span class="sxs-lookup"><span data-stu-id="84656-146">The other implicit numeric conversions never lose any information.</span></span>

<span data-ttu-id="84656-147">不存在对类型的隐式转换 `char` ，因此其他整型类型的值不会自动转换为 `char` 类型。</span><span class="sxs-lookup"><span data-stu-id="84656-147">There are no implicit conversions to the `char` type, so values of the other integral types do not automatically convert to the `char` type.</span></span>

### <a name="implicit-enumeration-conversions"></a><span data-ttu-id="84656-148">隐式枚举转换</span><span class="sxs-lookup"><span data-stu-id="84656-148">Implicit enumeration conversions</span></span>

<span data-ttu-id="84656-149">隐式枚举转换允许 *decimal_integer_literal* `0` 转换为任何 *enum_type* ，以及基础类型为 *enum_type* 的任何 *nullable_type* 。</span><span class="sxs-lookup"><span data-stu-id="84656-149">An implicit enumeration conversion permits the *decimal_integer_literal* `0` to be converted to any *enum_type* and to any *nullable_type* whose underlying type is an *enum_type*.</span></span> <span data-ttu-id="84656-150">在后一种情况下，转换将通过转换为基础 *enum_type* 进行计算，并将结果包装 ([可以为 null 的类型](types.md#nullable-types)) 。</span><span class="sxs-lookup"><span data-stu-id="84656-150">In the latter case the conversion is evaluated by converting to the underlying *enum_type* and wrapping the result ([Nullable types](types.md#nullable-types)).</span></span>

### <a name="implicit-interpolated-string-conversions"></a><span data-ttu-id="84656-151">隐式内插字符串转换</span><span class="sxs-lookup"><span data-stu-id="84656-151">Implicit interpolated string conversions</span></span>

<span data-ttu-id="84656-152">隐式内插字符串转换允许 *interpolated_string_expression* (内 [插字符串](expressions.md#interpolated-strings)) 转换为 `System.IFormattable` `System.FormattableString` 实现) 的或 (`System.IFormattable` 。</span><span class="sxs-lookup"><span data-stu-id="84656-152">An implicit interpolated string conversion permits an *interpolated_string_expression* ([Interpolated strings](expressions.md#interpolated-strings)) to be converted to `System.IFormattable` or `System.FormattableString` (which implements `System.IFormattable`).</span></span>

<span data-ttu-id="84656-153">应用此转换时，字符串值不是由内插字符串组成的。</span><span class="sxs-lookup"><span data-stu-id="84656-153">When this conversion is applied a string value is not composed from the interpolated string.</span></span> <span data-ttu-id="84656-154">相反 `System.FormattableString` ，将创建的实例，如内 [插字符串](expressions.md#interpolated-strings)中所述。</span><span class="sxs-lookup"><span data-stu-id="84656-154">Instead an instance of `System.FormattableString` is created, as further described in [Interpolated strings](expressions.md#interpolated-strings).</span></span>

### <a name="implicit-nullable-conversions"></a><span data-ttu-id="84656-155">隐式可为空转换</span><span class="sxs-lookup"><span data-stu-id="84656-155">Implicit nullable conversions</span></span>

<span data-ttu-id="84656-156">对不可以为 null 的值类型进行操作的预定义隐式转换也可用于这些类型的可以为 null 的形式。</span><span class="sxs-lookup"><span data-stu-id="84656-156">Predefined implicit conversions that operate on non-nullable value types can also be used with nullable forms of those types.</span></span> <span data-ttu-id="84656-157">对于从不可为 null 的值类型转换为不可为 null 的值类型的每个预定义隐式标识和数值转换 `S` `T` ，存在以下隐式可为 null 的转换：</span><span class="sxs-lookup"><span data-stu-id="84656-157">For each of the predefined implicit identity and numeric conversions that convert from a non-nullable value type `S` to a non-nullable value type `T`, the following implicit nullable conversions exist:</span></span>

*  <span data-ttu-id="84656-158">从到的隐式转换 `S?` `T?` 。</span><span class="sxs-lookup"><span data-stu-id="84656-158">An implicit conversion from `S?` to `T?`.</span></span>
*  <span data-ttu-id="84656-159">从到的隐式转换 `S` `T?` 。</span><span class="sxs-lookup"><span data-stu-id="84656-159">An implicit conversion from `S` to `T?`.</span></span>

<span data-ttu-id="84656-160">根据中的基础转换，计算隐式可为 null 的转换， `S` `T` 如下所示：</span><span class="sxs-lookup"><span data-stu-id="84656-160">Evaluation of an implicit nullable conversion based on an underlying conversion from `S` to `T` proceeds as follows:</span></span>

*  <span data-ttu-id="84656-161">如果可以为 null 的转换从 `S?` 到 `T?` ：</span><span class="sxs-lookup"><span data-stu-id="84656-161">If the nullable conversion is from `S?` to `T?`:</span></span>
    * <span data-ttu-id="84656-162">如果源值为 null (`HasValue` 属性为 false) ，则结果为类型的 null 值 `T?` 。</span><span class="sxs-lookup"><span data-stu-id="84656-162">If the source value is null (`HasValue` property is false), the result is the null value of type `T?`.</span></span>
    * <span data-ttu-id="84656-163">否则，转换将作为从到的解包进行计算 `S?` `S` ，后跟从到的基础转换 `S` `T` ，然后) 从到的包装 ([可以为 null 的类型](types.md#nullable-types) `T` `T?` 。</span><span class="sxs-lookup"><span data-stu-id="84656-163">Otherwise, the conversion is evaluated as an unwrapping from `S?` to `S`, followed by the underlying conversion from `S` to `T`, followed by a wrapping ([Nullable types](types.md#nullable-types)) from `T` to `T?`.</span></span>

*  <span data-ttu-id="84656-164">如果可以为 null 的转换从 `S` 到 `T?` ，则会将转换计算为从到的基础转换， `S` 并将从到 `T` 的换行 `T` `T?` 。</span><span class="sxs-lookup"><span data-stu-id="84656-164">If the nullable conversion is from `S` to `T?`, the conversion is evaluated as the underlying conversion from `S` to `T` followed by a wrapping from `T` to `T?`.</span></span>

### <a name="null-literal-conversions"></a><span data-ttu-id="84656-165">Null 文本转换</span><span class="sxs-lookup"><span data-stu-id="84656-165">Null literal conversions</span></span>

<span data-ttu-id="84656-166">存在从 `null` 文本到任何可以为 null 的类型的隐式转换。</span><span class="sxs-lookup"><span data-stu-id="84656-166">An implicit conversion exists from the `null` literal to any nullable type.</span></span> <span data-ttu-id="84656-167">这种转换会生成 null 值， (给定可以为 null 的类型) [可](types.md#nullable-types) 为 null 的类型。</span><span class="sxs-lookup"><span data-stu-id="84656-167">This conversion produces the null value ([Nullable types](types.md#nullable-types)) of the given nullable type.</span></span>

### <a name="implicit-reference-conversions"></a><span data-ttu-id="84656-168">隐式引用转换</span><span class="sxs-lookup"><span data-stu-id="84656-168">Implicit reference conversions</span></span>

<span data-ttu-id="84656-169">隐式引用转换为：</span><span class="sxs-lookup"><span data-stu-id="84656-169">The implicit reference conversions are:</span></span>

*  <span data-ttu-id="84656-170">从任何 *reference_type* 到 `object` 和 `dynamic` 。</span><span class="sxs-lookup"><span data-stu-id="84656-170">From any *reference_type* to `object` and `dynamic`.</span></span>
*  <span data-ttu-id="84656-171">从任何 *class_type* `S` 到任何 *class_type* `T` ， `S` 都是从派生的 `T` 。</span><span class="sxs-lookup"><span data-stu-id="84656-171">From any *class_type* `S` to any *class_type* `T`, provided `S` is derived from `T`.</span></span>
*  <span data-ttu-id="84656-172">从任何 *class_type* `S` 到任何 *interface_type* `T` ，提供的 `S` 实现 `T` 。</span><span class="sxs-lookup"><span data-stu-id="84656-172">From any *class_type* `S` to any *interface_type* `T`, provided `S` implements `T`.</span></span>
*  <span data-ttu-id="84656-173">从任何 *interface_type* `S` 到任何 *interface_type* `T` ， `S` 都是从派生的 `T` 。</span><span class="sxs-lookup"><span data-stu-id="84656-173">From any *interface_type* `S` to any *interface_type* `T`, provided `S` is derived from `T`.</span></span>
*  <span data-ttu-id="84656-174">在 array_type 具有元素类型的 *array_type* 中 `S` ，如果 `SE`  `T` `TE` 满足以下所有条件：</span><span class="sxs-lookup"><span data-stu-id="84656-174">From an *array_type* `S` with an element type `SE` to an *array_type* `T` with an element type `TE`, provided all of the following are true:</span></span>
    * <span data-ttu-id="84656-175">`S` 和 `T` 仅因元素类型而异。</span><span class="sxs-lookup"><span data-stu-id="84656-175">`S` and `T` differ only in element type.</span></span> <span data-ttu-id="84656-176">换言之， `S` 和 `T` 具有相同的维数。</span><span class="sxs-lookup"><span data-stu-id="84656-176">In other words, `S` and `T` have the same number of dimensions.</span></span>
    * <span data-ttu-id="84656-177">`SE`和 `TE` 都是 *reference_type*。</span><span class="sxs-lookup"><span data-stu-id="84656-177">Both `SE` and `TE` are *reference_type* s.</span></span>
    * <span data-ttu-id="84656-178">存在从到的隐式引用转换 `SE` `TE` 。</span><span class="sxs-lookup"><span data-stu-id="84656-178">An implicit reference conversion exists from `SE` to `TE`.</span></span>
*  <span data-ttu-id="84656-179">从任何 *array_type* `System.Array` 和它实现的接口。</span><span class="sxs-lookup"><span data-stu-id="84656-179">From any *array_type* to `System.Array` and the interfaces it implements.</span></span>
*  <span data-ttu-id="84656-180">从一维数组类型 `S[]` 到 `System.Collections.Generic.IList<T>` 及其基接口，前提是存在隐式标识或从到的引用转换 `S` `T` 。</span><span class="sxs-lookup"><span data-stu-id="84656-180">From a single-dimensional array type `S[]` to `System.Collections.Generic.IList<T>` and its base interfaces, provided that there is an implicit identity or reference conversion from `S` to `T`.</span></span>
*  <span data-ttu-id="84656-181">从任何 *delegate_type* `System.Delegate` 和它实现的接口。</span><span class="sxs-lookup"><span data-stu-id="84656-181">From any *delegate_type* to `System.Delegate` and the interfaces it implements.</span></span>
*  <span data-ttu-id="84656-182">从 null 文本到任何 *reference_type*。</span><span class="sxs-lookup"><span data-stu-id="84656-182">From the null literal to any *reference_type*.</span></span>
*  <span data-ttu-id="84656-183">从任何 *reference_type* 到 *reference_type* ， `T` 如果它具有隐式标识或到 *reference_type* 的引用转换 `T0` ，并且 `T0` 具有到的标识转换 `T` 。</span><span class="sxs-lookup"><span data-stu-id="84656-183">From any *reference_type* to a *reference_type* `T` if it has an implicit identity or reference conversion to a *reference_type* `T0` and `T0` has an identity conversion to `T`.</span></span>
*  <span data-ttu-id="84656-184">从任何 *reference_type* 到接口或委托类型（ `T` 如果它具有隐式标识或到接口或委托类型的引用转换 `T0` ），并且) 到之间存在可转换的 `T0` ([方差转换](interfaces.md#variance-conversion) `T` 。</span><span class="sxs-lookup"><span data-stu-id="84656-184">From any *reference_type* to an interface or delegate type `T` if it has an implicit identity or reference conversion to an interface or delegate type `T0` and `T0` is variance-convertible ([Variance conversion](interfaces.md#variance-conversion)) to `T`.</span></span>
*  <span data-ttu-id="84656-185">涉及称为引用类型的类型参数的隐式转换。</span><span class="sxs-lookup"><span data-stu-id="84656-185">Implicit conversions involving type parameters that are known to be reference types.</span></span> <span data-ttu-id="84656-186">有关涉及类型参数的隐式转换的更多详细信息，请参阅 [涉及类型参数的隐式转换](conversions.md#implicit-conversions-involving-type-parameters) 。</span><span class="sxs-lookup"><span data-stu-id="84656-186">See [Implicit conversions involving type parameters](conversions.md#implicit-conversions-involving-type-parameters) for more details on implicit conversions involving type parameters.</span></span>

<span data-ttu-id="84656-187">隐式引用转换是 *reference_type* 之间的转换，这些转换可证明始终成功，因此不需要在运行时进行检查。</span><span class="sxs-lookup"><span data-stu-id="84656-187">The implicit reference conversions are those conversions between *reference_type* s that can be proven to always succeed, and therefore require no checks at run-time.</span></span>

<span data-ttu-id="84656-188">引用转换、隐式或显式转换决不会更改正在转换的对象的引用标识。</span><span class="sxs-lookup"><span data-stu-id="84656-188">Reference conversions, implicit or explicit, never change the referential identity of the object being converted.</span></span> <span data-ttu-id="84656-189">换言之，虽然引用转换可以更改引用的类型，但它不会更改引用的对象的类型或值。</span><span class="sxs-lookup"><span data-stu-id="84656-189">In other words, while a reference conversion may change the type of the reference, it never changes the type or value of the object being referred to.</span></span>

### <a name="boxing-conversions"></a><span data-ttu-id="84656-190">装箱转换</span><span class="sxs-lookup"><span data-stu-id="84656-190">Boxing conversions</span></span>

<span data-ttu-id="84656-191">装箱转换允许 *value_type* 隐式转换为引用类型。</span><span class="sxs-lookup"><span data-stu-id="84656-191">A boxing conversion permits a *value_type* to be implicitly converted to a reference type.</span></span> <span data-ttu-id="84656-192">对 `object` `dynamic` `System.ValueType` *non_nullable_value_type* 实现的任何 *interface_type* ，都存在从到、、以及的任何 non_nullable_value_type 的装箱转换。</span><span class="sxs-lookup"><span data-stu-id="84656-192">A boxing conversion exists from any *non_nullable_value_type* to `object` and `dynamic`, to `System.ValueType` and to any *interface_type* implemented by the *non_nullable_value_type*.</span></span> <span data-ttu-id="84656-193">此外，可以将 *enum_type* 转换为类型 `System.Enum` 。</span><span class="sxs-lookup"><span data-stu-id="84656-193">Furthermore an *enum_type* can be converted to the type `System.Enum`.</span></span>

<span data-ttu-id="84656-194">如果且仅当存在从基础 *non_nullable_value_type* 到引用类型的装箱转换，则从 *nullable_type* 到引用类型的装箱转换。</span><span class="sxs-lookup"><span data-stu-id="84656-194">A boxing conversion exists from a *nullable_type* to a reference type, if and only if a boxing conversion exists from the underlying *non_nullable_value_type* to the reference type.</span></span>

<span data-ttu-id="84656-195">如果值类型 `I` 具有到接口类型的装箱转换 `I0` ，并且其 `I0` 标识转换为，则该值类型具有到接口类型的装箱转换 `I` 。</span><span class="sxs-lookup"><span data-stu-id="84656-195">A value type has a boxing conversion to an interface type `I` if it has a boxing conversion to an interface type `I0` and `I0` has an identity conversion to `I`.</span></span>

<span data-ttu-id="84656-196">如果值类型 `I` 具有到接口类型的装箱转换或委托类型的装箱转换 `I0` 并且 `I0` ([方差转换](interfaces.md#variance-conversion)) 为变体，则该值类型具有到的装箱转换 `I` 。</span><span class="sxs-lookup"><span data-stu-id="84656-196">A value type has a boxing conversion to an interface type `I` if it has a boxing conversion to an interface or delegate type `I0` and `I0` is variance-convertible ([Variance conversion](interfaces.md#variance-conversion)) to `I`.</span></span>

<span data-ttu-id="84656-197">将 *non_nullable_value_type* 的值装箱包括分配对象实例并将 *value_type* 值复制到该实例中。</span><span class="sxs-lookup"><span data-stu-id="84656-197">Boxing a value of a *non_nullable_value_type* consists of allocating an object instance and copying the *value_type* value into that instance.</span></span> <span data-ttu-id="84656-198">结构可以装箱到类型 `System.ValueType` ，因为这是所有结构的基类 ([继承](structs.md#inheritance)) 。</span><span class="sxs-lookup"><span data-stu-id="84656-198">A struct can be boxed to the type `System.ValueType`, since that is a base class for all structs ([Inheritance](structs.md#inheritance)).</span></span>

<span data-ttu-id="84656-199">将 *nullable_type* 的值装箱将继续执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="84656-199">Boxing a value of a *nullable_type* proceeds as follows:</span></span>

*  <span data-ttu-id="84656-200">如果源值为 null (`HasValue` 属性为 false) ，则结果将为目标类型的空引用。</span><span class="sxs-lookup"><span data-stu-id="84656-200">If the source value is null (`HasValue` property is false), the result is a null reference of the target type.</span></span>
*  <span data-ttu-id="84656-201">否则，结果将是对 `T` 源值解包和装箱而生成的装箱的引用。</span><span class="sxs-lookup"><span data-stu-id="84656-201">Otherwise, the result is a reference to a boxed `T` produced by unwrapping and boxing the source value.</span></span>

<span data-ttu-id="84656-202">[装箱转换中进一步](types.md#boxing-conversions)介绍了装箱转换。</span><span class="sxs-lookup"><span data-stu-id="84656-202">Boxing conversions are described further in [Boxing conversions](types.md#boxing-conversions).</span></span>

### <a name="implicit-dynamic-conversions"></a><span data-ttu-id="84656-203">隐式动态转换</span><span class="sxs-lookup"><span data-stu-id="84656-203">Implicit dynamic conversions</span></span>

<span data-ttu-id="84656-204">存在从类型为的表达式到任何类型的隐式动态转换 `dynamic` `T` 。</span><span class="sxs-lookup"><span data-stu-id="84656-204">An implicit dynamic conversion exists from an expression of type `dynamic` to any type `T`.</span></span> <span data-ttu-id="84656-205">转换是动态绑定 ([动态绑定](expressions.md#dynamic-binding)) ，这意味着将在运行时从表达式的运行时类型中查找隐式转换 `T` 。</span><span class="sxs-lookup"><span data-stu-id="84656-205">The conversion is dynamically bound ([Dynamic binding](expressions.md#dynamic-binding)), which means that an implicit conversion will be sought at run-time from the run-time type of the expression to `T`.</span></span> <span data-ttu-id="84656-206">如果未找到任何转换，则会引发运行时异常。</span><span class="sxs-lookup"><span data-stu-id="84656-206">If no conversion is found, a run-time exception is thrown.</span></span>

<span data-ttu-id="84656-207">请注意，此隐式转换似乎违反了隐式转换开始时的 [建议，隐](conversions.md#implicit-conversions) 式转换应永远不会引发异常。</span><span class="sxs-lookup"><span data-stu-id="84656-207">Note that this implicit conversion seemingly violates the advice in the beginning of [Implicit conversions](conversions.md#implicit-conversions) that an implicit conversion should never cause an exception.</span></span> <span data-ttu-id="84656-208">但它不是转换本身，而是 *查找* 导致异常的转换。</span><span class="sxs-lookup"><span data-stu-id="84656-208">However it is not the conversion itself, but the *finding* of the conversion that causes the exception.</span></span> <span data-ttu-id="84656-209">运行时异常的风险在使用动态绑定时是固有的。</span><span class="sxs-lookup"><span data-stu-id="84656-209">The risk of run-time exceptions is inherent in the use of dynamic binding.</span></span> <span data-ttu-id="84656-210">如果不需要转换的动态绑定，则可以先将该表达式转换为 `object` ，然后再转换为所需的类型。</span><span class="sxs-lookup"><span data-stu-id="84656-210">If dynamic binding of the conversion is not desired, the expression can be first converted to `object`, and then to the desired type.</span></span>

<span data-ttu-id="84656-211">下面的示例阐释了隐式动态转换：</span><span class="sxs-lookup"><span data-stu-id="84656-211">The following example illustrates implicit dynamic conversions:</span></span>

```csharp
object o  = "object"
dynamic d = "dynamic";

string s1 = o; // Fails at compile-time -- no conversion exists
string s2 = d; // Compiles and succeeds at run-time
int i     = d; // Compiles but fails at run-time -- no conversion exists
```

<span data-ttu-id="84656-212">和中的 `s2` 分配 `i` 都采用隐式动态转换，在这种情况下，将在运行时暂停操作的绑定。</span><span class="sxs-lookup"><span data-stu-id="84656-212">The assignments to `s2` and `i` both employ implicit dynamic conversions, where the binding of the operations is suspended until run-time.</span></span> <span data-ttu-id="84656-213">在运行时，隐式转换从的运行时类型中查找 `d`  --  `string` 到目标类型。</span><span class="sxs-lookup"><span data-stu-id="84656-213">At run-time, implicit conversions are sought from the run-time type of `d` -- `string` -- to the target type.</span></span> <span data-ttu-id="84656-214">找到到， `string` 但不能转换为 `int` 。</span><span class="sxs-lookup"><span data-stu-id="84656-214">A conversion is found to `string` but not to `int`.</span></span>

### <a name="implicit-constant-expression-conversions"></a><span data-ttu-id="84656-215">隐式常量表达式转换</span><span class="sxs-lookup"><span data-stu-id="84656-215">Implicit constant expression conversions</span></span>

<span data-ttu-id="84656-216">隐式常数表达式转换允许以下转换：</span><span class="sxs-lookup"><span data-stu-id="84656-216">An implicit constant expression conversion permits the following conversions:</span></span>

*  <span data-ttu-id="84656-217"> [](expressions.md#constant-expressions) `int` `sbyte` `byte` `short` `ushort` `uint` `ulong` 如果 *constant_expression* 的值在目标类型的范围内，则可以将类型的 constant_expression (常数表达式) 转换为类型、、、、或。</span><span class="sxs-lookup"><span data-stu-id="84656-217">A *constant_expression* ([Constant expressions](expressions.md#constant-expressions)) of type `int` can be converted to type `sbyte`, `byte`, `short`, `ushort`, `uint`, or `ulong`, provided the value of the *constant_expression* is within the range of the destination type.</span></span>
*  <span data-ttu-id="84656-218"> `long` `ulong` 如果 *constant_expression* 的值不为负数，则可以将类型的 constant_expression 转换为类型。</span><span class="sxs-lookup"><span data-stu-id="84656-218">A *constant_expression* of type `long` can be converted to type `ulong`, provided the value of the *constant_expression* is not negative.</span></span>

### <a name="implicit-conversions-involving-type-parameters"></a><span data-ttu-id="84656-219">涉及类型参数的隐式转换</span><span class="sxs-lookup"><span data-stu-id="84656-219">Implicit conversions involving type parameters</span></span>

<span data-ttu-id="84656-220">给定的类型参数存在以下隐式转换 `T` ：</span><span class="sxs-lookup"><span data-stu-id="84656-220">The following implicit conversions exist for a given type parameter `T`:</span></span>

*  <span data-ttu-id="84656-221">从到 `T` 其有效的基类 `C` ，从到的任何基类，从到所 `T` `C` `T` 实现的任何接口 `C` 。</span><span class="sxs-lookup"><span data-stu-id="84656-221">From `T` to its effective base class `C`, from `T` to any base class of `C`, and from `T` to any interface implemented by `C`.</span></span> <span data-ttu-id="84656-222">在运行时，如果 `T` 是值类型，则转换将作为装箱转换执行。</span><span class="sxs-lookup"><span data-stu-id="84656-222">At run-time, if `T` is a value type, the conversion is executed as a boxing conversion.</span></span> <span data-ttu-id="84656-223">否则，转换将作为隐式引用转换或标识转换执行。</span><span class="sxs-lookup"><span data-stu-id="84656-223">Otherwise, the conversion is executed as an implicit reference conversion or identity conversion.</span></span>
*  <span data-ttu-id="84656-224">从 `T` `I` `T` 设置为有效接口中的接口类型，并从设置 `T` 为的任何基接口 `I` 。</span><span class="sxs-lookup"><span data-stu-id="84656-224">From `T` to an interface type `I` in `T`'s effective interface set and from `T` to any base interface of `I`.</span></span> <span data-ttu-id="84656-225">在运行时，如果 `T` 是值类型，则转换将作为装箱转换执行。</span><span class="sxs-lookup"><span data-stu-id="84656-225">At run-time, if `T` is a value type, the conversion is executed as a boxing conversion.</span></span> <span data-ttu-id="84656-226">否则，转换将作为隐式引用转换或标识转换执行。</span><span class="sxs-lookup"><span data-stu-id="84656-226">Otherwise, the conversion is executed as an implicit reference conversion or identity conversion.</span></span>
*  <span data-ttu-id="84656-227">从 `T` 到类型参数 `U` ， `T` 具体取决于 `U`)  ([类型参数约束](classes.md#type-parameter-constraints) 。</span><span class="sxs-lookup"><span data-stu-id="84656-227">From `T` to a type parameter `U`, provided `T` depends on `U` ([Type parameter constraints](classes.md#type-parameter-constraints)).</span></span> <span data-ttu-id="84656-228">在运行时，如果 `U` 是值类型，则和的 `T` `U` 类型必须相同，并且不执行任何转换。</span><span class="sxs-lookup"><span data-stu-id="84656-228">At run-time, if `U` is a value type, then `T` and `U` are necessarily the same type and no conversion is performed.</span></span> <span data-ttu-id="84656-229">否则，如果 `T` 是值类型，则转换将作为装箱转换执行。</span><span class="sxs-lookup"><span data-stu-id="84656-229">Otherwise, if `T` is a value type, the conversion is executed as a boxing conversion.</span></span> <span data-ttu-id="84656-230">否则，转换将作为隐式引用转换或标识转换执行。</span><span class="sxs-lookup"><span data-stu-id="84656-230">Otherwise, the conversion is executed as an implicit reference conversion or identity conversion.</span></span>
*  <span data-ttu-id="84656-231">从 null 文本到 `T` ，已知为 `T` 引用类型。</span><span class="sxs-lookup"><span data-stu-id="84656-231">From the null literal to `T`, provided `T` is known to be a reference type.</span></span>
*  <span data-ttu-id="84656-232">`T`向引用类型（ `I` 如果它具有到引用类型的隐式转换 `S0` 和到的 `S0` 标识转换） `S` 。</span><span class="sxs-lookup"><span data-stu-id="84656-232">From `T` to a reference type `I` if it has an implicit conversion to a reference type `S0` and `S0` has an identity conversion to `S`.</span></span> <span data-ttu-id="84656-233">在运行时，转换的执行方式与转换到的方式相同 `S0` 。</span><span class="sxs-lookup"><span data-stu-id="84656-233">At run-time the conversion is executed the same way as the conversion to `S0`.</span></span>
*  <span data-ttu-id="84656-234">从 `T` 到接口类型 `I` （如果它具有到接口或委托类型的隐式转换 `I0` ）并且可以转换为 `I0` `I` ([方差转换](interfaces.md#variance-conversion)) 。</span><span class="sxs-lookup"><span data-stu-id="84656-234">From `T` to an interface type `I` if it has an implicit conversion to an interface or delegate type `I0` and `I0` is variance-convertible to `I` ([Variance conversion](interfaces.md#variance-conversion)).</span></span> <span data-ttu-id="84656-235">在运行时，如果 `T` 是值类型，则转换将作为装箱转换执行。</span><span class="sxs-lookup"><span data-stu-id="84656-235">At run-time, if `T` is a value type, the conversion is executed as a boxing conversion.</span></span> <span data-ttu-id="84656-236">否则，转换将作为隐式引用转换或标识转换执行。</span><span class="sxs-lookup"><span data-stu-id="84656-236">Otherwise, the conversion is executed as an implicit reference conversion or identity conversion.</span></span>

<span data-ttu-id="84656-237">如果 `T` 已知 ([类型参数约束](classes.md#type-parameter-constraints)) 为引用类型，则上述转换全都归类为隐式引用转换 ([隐式引用转换](conversions.md#implicit-reference-conversions)) 。</span><span class="sxs-lookup"><span data-stu-id="84656-237">If `T` is known to be a reference type ([Type parameter constraints](classes.md#type-parameter-constraints)), the conversions above are all classified as implicit reference conversions ([Implicit reference conversions](conversions.md#implicit-reference-conversions)).</span></span> <span data-ttu-id="84656-238">如果 `T` 不知道是引用类型，上面的转换归类为装箱转换 ([装箱转换](conversions.md#boxing-conversions)) 。</span><span class="sxs-lookup"><span data-stu-id="84656-238">If `T` is not known to be a reference type, the conversions above are classified as boxing conversions ([Boxing conversions](conversions.md#boxing-conversions)).</span></span>

### <a name="user-defined-implicit-conversions"></a><span data-ttu-id="84656-239">用户定义的隐式转换</span><span class="sxs-lookup"><span data-stu-id="84656-239">User-defined implicit conversions</span></span>

<span data-ttu-id="84656-240">用户定义的隐式转换包括一个可选的标准隐式转换，然后执行用户定义的隐式转换运算符，然后执行另一个可选的标准隐式转换。</span><span class="sxs-lookup"><span data-stu-id="84656-240">A user-defined implicit conversion consists of an optional standard implicit conversion, followed by execution of a user-defined implicit conversion operator, followed by another optional standard implicit conversion.</span></span> <span data-ttu-id="84656-241">用于评估用户定义的隐式转换的确切规则在 [处理用户定义的隐式转换](conversions.md#processing-of-user-defined-implicit-conversions)中进行了介绍。</span><span class="sxs-lookup"><span data-stu-id="84656-241">The exact rules for evaluating user-defined implicit conversions are described in [Processing of user-defined implicit conversions](conversions.md#processing-of-user-defined-implicit-conversions).</span></span>

### <a name="anonymous-function-conversions-and-method-group-conversions"></a><span data-ttu-id="84656-242">匿名函数转换和方法组转换</span><span class="sxs-lookup"><span data-stu-id="84656-242">Anonymous function conversions and method group conversions</span></span>

<span data-ttu-id="84656-243">匿名函数和方法组本身没有类型，但可能会隐式转换为委托类型或表达式树类型。</span><span class="sxs-lookup"><span data-stu-id="84656-243">Anonymous functions and method groups do not have types in and of themselves, but may be implicitly converted to delegate types or expression tree types.</span></span> <span data-ttu-id="84656-244">[匿名函数转换和](conversions.md#anonymous-function-conversions)[方法组转换](conversions.md#method-group-conversions)中的方法组转换更详细地介绍了匿名函数转换。</span><span class="sxs-lookup"><span data-stu-id="84656-244">Anonymous function conversions are described in more detail in [Anonymous function conversions](conversions.md#anonymous-function-conversions) and method group conversions in [Method group conversions](conversions.md#method-group-conversions).</span></span>

## <a name="explicit-conversions"></a><span data-ttu-id="84656-245">显式转换</span><span class="sxs-lookup"><span data-stu-id="84656-245">Explicit conversions</span></span>

<span data-ttu-id="84656-246">以下转换归类为显式转换：</span><span class="sxs-lookup"><span data-stu-id="84656-246">The following conversions are classified as explicit conversions:</span></span>

*  <span data-ttu-id="84656-247">所有隐式转换。</span><span class="sxs-lookup"><span data-stu-id="84656-247">All implicit conversions.</span></span>
*  <span data-ttu-id="84656-248">显式数值转换。</span><span class="sxs-lookup"><span data-stu-id="84656-248">Explicit numeric conversions.</span></span>
*  <span data-ttu-id="84656-249">显式枚举转换。</span><span class="sxs-lookup"><span data-stu-id="84656-249">Explicit enumeration conversions.</span></span>
*  <span data-ttu-id="84656-250">可以为 null 的显式转换。</span><span class="sxs-lookup"><span data-stu-id="84656-250">Explicit nullable conversions.</span></span>
*  <span data-ttu-id="84656-251">显式引用转换。</span><span class="sxs-lookup"><span data-stu-id="84656-251">Explicit reference conversions.</span></span>
*  <span data-ttu-id="84656-252">显式接口转换。</span><span class="sxs-lookup"><span data-stu-id="84656-252">Explicit interface conversions.</span></span>
*  <span data-ttu-id="84656-253">取消装箱转换。</span><span class="sxs-lookup"><span data-stu-id="84656-253">Unboxing conversions.</span></span>
*  <span data-ttu-id="84656-254">显式动态转换</span><span class="sxs-lookup"><span data-stu-id="84656-254">Explicit dynamic conversions</span></span>
*  <span data-ttu-id="84656-255">用户定义的显式转换。</span><span class="sxs-lookup"><span data-stu-id="84656-255">User-defined explicit conversions.</span></span>

<span data-ttu-id="84656-256">显式转换可在转换表达式 () [转换表达式](expressions.md#cast-expressions) 中发生。</span><span class="sxs-lookup"><span data-stu-id="84656-256">Explicit conversions can occur in cast expressions ([Cast expressions](expressions.md#cast-expressions)).</span></span>

<span data-ttu-id="84656-257">显式转换集包括所有隐式转换。</span><span class="sxs-lookup"><span data-stu-id="84656-257">The set of explicit conversions includes all implicit conversions.</span></span> <span data-ttu-id="84656-258">这意味着允许冗余强制转换表达式。</span><span class="sxs-lookup"><span data-stu-id="84656-258">This means that redundant cast expressions are allowed.</span></span>

<span data-ttu-id="84656-259">不是隐式转换的显式转换是转换，这种转换不能始终成功、已知的转换可能会丢失信息，并且跨类型的域转换的类型充分不同，以确保显式表示法。</span><span class="sxs-lookup"><span data-stu-id="84656-259">The explicit conversions that are not implicit conversions are conversions that cannot be proven to always succeed, conversions that are known to possibly lose information, and conversions across domains of types sufficiently different to merit explicit notation.</span></span>

### <a name="explicit-numeric-conversions"></a><span data-ttu-id="84656-260">显式数值转换</span><span class="sxs-lookup"><span data-stu-id="84656-260">Explicit numeric conversions</span></span>

<span data-ttu-id="84656-261">显式数值转换是指从 *numeric_type* 到另一个 *numeric_type* 的转换， ([隐式](conversions.md#implicit-numeric-conversions) 数值转换) 尚不存在：</span><span class="sxs-lookup"><span data-stu-id="84656-261">The explicit numeric conversions are the conversions from a *numeric_type* to another *numeric_type* for which an implicit numeric conversion ([Implicit numeric conversions](conversions.md#implicit-numeric-conversions)) does not already exist:</span></span>

*  <span data-ttu-id="84656-262">从 `sbyte` 到 `byte` 、、、 `ushort` `uint` `ulong` 或 `char` 。</span><span class="sxs-lookup"><span data-stu-id="84656-262">From `sbyte` to `byte`, `ushort`, `uint`, `ulong`, or `char`.</span></span>
*  <span data-ttu-id="84656-263">从 `byte` 到 `sbyte` 和 `char` 。</span><span class="sxs-lookup"><span data-stu-id="84656-263">From `byte` to `sbyte` and `char`.</span></span>
*  <span data-ttu-id="84656-264">从 `short` 到、、、、 `sbyte` `byte` `ushort` `uint` `ulong` 或 `char` 。</span><span class="sxs-lookup"><span data-stu-id="84656-264">From `short` to `sbyte`, `byte`, `ushort`, `uint`, `ulong`, or `char`.</span></span>
*  <span data-ttu-id="84656-265">从 `ushort` 到 `sbyte` 、 `byte` 、 `short` 或 `char` 。</span><span class="sxs-lookup"><span data-stu-id="84656-265">From `ushort` to `sbyte`, `byte`, `short`, or `char`.</span></span>
*  <span data-ttu-id="84656-266">从到、、、、、 `int` `sbyte` `byte` `short` `ushort` `uint` `ulong` 或 `char` 。</span><span class="sxs-lookup"><span data-stu-id="84656-266">From `int` to `sbyte`, `byte`, `short`, `ushort`, `uint`, `ulong`, or `char`.</span></span>
*  <span data-ttu-id="84656-267">从 `uint` 到、、、、 `sbyte` `byte` `short` `ushort` `int` 或 `char` 。</span><span class="sxs-lookup"><span data-stu-id="84656-267">From `uint` to `sbyte`, `byte`, `short`, `ushort`, `int`, or `char`.</span></span>
*  <span data-ttu-id="84656-268">从到、、、、、、 `long` `sbyte` `byte` `short` `ushort` `int` `uint` `ulong` 或 `char` 。</span><span class="sxs-lookup"><span data-stu-id="84656-268">From `long` to `sbyte`, `byte`, `short`, `ushort`, `int`, `uint`, `ulong`, or `char`.</span></span>
*  <span data-ttu-id="84656-269">从到、、、、、、 `ulong` `sbyte` `byte` `short` `ushort` `int` `uint` `long` 或 `char` 。</span><span class="sxs-lookup"><span data-stu-id="84656-269">From `ulong` to `sbyte`, `byte`, `short`, `ushort`, `int`, `uint`, `long`, or `char`.</span></span>
*  <span data-ttu-id="84656-270">从 `char` 到 `sbyte` 、 `byte` 或 `short` 。</span><span class="sxs-lookup"><span data-stu-id="84656-270">From `char` to `sbyte`, `byte`, or `short`.</span></span>
*  <span data-ttu-id="84656-271">从到、、、、、、、、 `float` `sbyte` `byte` `short` `ushort` `int` `uint` `long` `ulong` `char` 或 `decimal` 。</span><span class="sxs-lookup"><span data-stu-id="84656-271">From `float` to `sbyte`, `byte`, `short`, `ushort`, `int`, `uint`, `long`, `ulong`, `char`, or `decimal`.</span></span>
*  <span data-ttu-id="84656-272">从到、、、、、、、、、 `double` `sbyte` `byte` `short` `ushort` `int` `uint` `long` `ulong` `char` `float` 或 `decimal` 。</span><span class="sxs-lookup"><span data-stu-id="84656-272">From `double` to `sbyte`, `byte`, `short`, `ushort`, `int`, `uint`, `long`, `ulong`, `char`, `float`, or `decimal`.</span></span>
*  <span data-ttu-id="84656-273">从到、、、、、、、、、 `decimal` `sbyte` `byte` `short` `ushort` `int` `uint` `long` `ulong` `char` `float` 或 `double` 。</span><span class="sxs-lookup"><span data-stu-id="84656-273">From `decimal` to `sbyte`, `byte`, `short`, `ushort`, `int`, `uint`, `long`, `ulong`, `char`, `float`, or `double`.</span></span>

<span data-ttu-id="84656-274">由于显式转换包括所有隐式和显式的数值转换，因此始终可以使用强制转换表达式 (强制转换) [表达式](expressions.md#cast-expressions)将任何 *numeric_type* 转换为任何其他 *numeric_type* 。</span><span class="sxs-lookup"><span data-stu-id="84656-274">Because the explicit conversions include all implicit and explicit numeric conversions, it is always possible to convert from any *numeric_type* to any other *numeric_type* using a cast expression ([Cast expressions](expressions.md#cast-expressions)).</span></span>

<span data-ttu-id="84656-275">显式数值转换可能会丢失信息，或可能导致引发异常。</span><span class="sxs-lookup"><span data-stu-id="84656-275">The explicit numeric conversions possibly lose information or possibly cause exceptions to be thrown.</span></span> <span data-ttu-id="84656-276">显式数值转换的处理方式如下：</span><span class="sxs-lookup"><span data-stu-id="84656-276">An explicit numeric conversion is processed as follows:</span></span>

*  <span data-ttu-id="84656-277">对于从整型转换为另一整型类型的转换，该处理依赖于溢出检查上下文 ([已检查和未检查的运算符](expressions.md#the-checked-and-unchecked-operators)) 发生转换的：</span><span class="sxs-lookup"><span data-stu-id="84656-277">For a conversion from an integral type to another integral type, the processing depends on the overflow checking context ([The checked and unchecked operators](expressions.md#the-checked-and-unchecked-operators)) in which the conversion takes place:</span></span>
    * <span data-ttu-id="84656-278">在 `checked` 上下文中，如果源操作数的值在目标类型的范围内，则转换成功; 但 `System.OverflowException` 如果源操作数的值超出了目标类型的范围，则会引发。</span><span class="sxs-lookup"><span data-stu-id="84656-278">In a `checked` context, the conversion succeeds if the value of the source operand is within the range of the destination type, but throws a `System.OverflowException` if the value of the source operand is outside the range of the destination type.</span></span>
    * <span data-ttu-id="84656-279">在 `unchecked` 上下文中，转换始终成功，并按如下所示继续。</span><span class="sxs-lookup"><span data-stu-id="84656-279">In an `unchecked` context, the conversion always succeeds, and proceeds as follows.</span></span>
        * <span data-ttu-id="84656-280">如果源类型大于目标类型，则通过放弃其“额外”最高有效位来截断源值。</span><span class="sxs-lookup"><span data-stu-id="84656-280">If the source type is larger than the destination type, then the source value is truncated by discarding its "extra" most significant bits.</span></span> <span data-ttu-id="84656-281">结果会被视为目标类型的值。</span><span class="sxs-lookup"><span data-stu-id="84656-281">The result is then treated as a value of the destination type.</span></span>
        * <span data-ttu-id="84656-282">如果源类型小于目标类型，则源值是符号扩展或零扩展，以使其与目标类型的大小相同。</span><span class="sxs-lookup"><span data-stu-id="84656-282">If the source type is smaller than the destination type, then the source value is either sign-extended or zero-extended so that it is the same size as the destination type.</span></span> <span data-ttu-id="84656-283">如果源类型带符号，则是符号扩展；如果源类型是无符号的，则是零扩展。</span><span class="sxs-lookup"><span data-stu-id="84656-283">Sign-extension is used if the source type is signed; zero-extension is used if the source type is unsigned.</span></span> <span data-ttu-id="84656-284">结果会被视为目标类型的值。</span><span class="sxs-lookup"><span data-stu-id="84656-284">The result is then treated as a value of the destination type.</span></span>
        * <span data-ttu-id="84656-285">如果源类型与目标类型的大小相同，则源值将被视为目标类型的值。</span><span class="sxs-lookup"><span data-stu-id="84656-285">If the source type is the same size as the destination type, then the source value is treated as a value of the destination type.</span></span>
*  <span data-ttu-id="84656-286">对于从 `decimal` 到整数类型的转换，源值向零舍入到最接近的整数值，并且此整数值将成为转换的结果。</span><span class="sxs-lookup"><span data-stu-id="84656-286">For a conversion from `decimal` to an integral type, the source value is rounded towards zero to the nearest integral value, and this integral value becomes the result of the conversion.</span></span> <span data-ttu-id="84656-287">如果生成的整数值超出了目标类型的范围， `System.OverflowException` 则会引发。</span><span class="sxs-lookup"><span data-stu-id="84656-287">If the resulting integral value is outside the range of the destination type, a `System.OverflowException` is thrown.</span></span>
*  <span data-ttu-id="84656-288">对于从 `float` 或 `double` 到整型类型的转换，处理操作依赖于溢出检查上下文 ([已检查和未检查的运算符](expressions.md#the-checked-and-unchecked-operators)) 发生转换的：</span><span class="sxs-lookup"><span data-stu-id="84656-288">For a conversion from `float` or `double` to an integral type, the processing depends on the overflow checking context ([The checked and unchecked operators](expressions.md#the-checked-and-unchecked-operators)) in which the conversion takes place:</span></span>
    * <span data-ttu-id="84656-289">在 `checked` 上下文中，转换过程如下所示：</span><span class="sxs-lookup"><span data-stu-id="84656-289">In a `checked` context, the conversion proceeds as follows:</span></span>
        * <span data-ttu-id="84656-290">如果操作数的值为 NaN 或无穷大，则 `System.OverflowException` 会引发。</span><span class="sxs-lookup"><span data-stu-id="84656-290">If the value of the operand is NaN or infinite, a `System.OverflowException` is thrown.</span></span>
        * <span data-ttu-id="84656-291">否则，源操作数向零舍入到最接近的整数值。</span><span class="sxs-lookup"><span data-stu-id="84656-291">Otherwise, the source operand is rounded towards zero to the nearest integral value.</span></span> <span data-ttu-id="84656-292">如果此整数值在目标类型的范围内，则此值为转换的结果。</span><span class="sxs-lookup"><span data-stu-id="84656-292">If this integral value is within the range of the destination type then this value is the result of the conversion.</span></span>
        * <span data-ttu-id="84656-293">否则，将会引发 `System.OverflowException`。</span><span class="sxs-lookup"><span data-stu-id="84656-293">Otherwise, a `System.OverflowException` is thrown.</span></span>
    * <span data-ttu-id="84656-294">在 `unchecked` 上下文中，转换始终成功，并按如下所示继续。</span><span class="sxs-lookup"><span data-stu-id="84656-294">In an `unchecked` context, the conversion always succeeds, and proceeds as follows.</span></span>
        * <span data-ttu-id="84656-295">如果操作数的值为 NaN 或无穷大，则转换的结果是目标类型的未指定值。</span><span class="sxs-lookup"><span data-stu-id="84656-295">If the value of the operand is NaN or infinite, the result of the conversion is an unspecified value of the destination type.</span></span>
        * <span data-ttu-id="84656-296">否则，源操作数向零舍入到最接近的整数值。</span><span class="sxs-lookup"><span data-stu-id="84656-296">Otherwise, the source operand is rounded towards zero to the nearest integral value.</span></span> <span data-ttu-id="84656-297">如果此整数值在目标类型的范围内，则此值为转换的结果。</span><span class="sxs-lookup"><span data-stu-id="84656-297">If this integral value is within the range of the destination type then this value is the result of the conversion.</span></span>
        * <span data-ttu-id="84656-298">否则，转换的结果是目标类型的未指定值。</span><span class="sxs-lookup"><span data-stu-id="84656-298">Otherwise, the result of the conversion is an unspecified value of the destination type.</span></span>
*  <span data-ttu-id="84656-299">对于从到的 `double` 转换 `float` ，此 `double` 值舍入为最接近的 `float` 值。</span><span class="sxs-lookup"><span data-stu-id="84656-299">For a conversion from `double` to `float`, the `double` value is rounded to the nearest `float` value.</span></span> <span data-ttu-id="84656-300">如果 `double` 值太小而无法表示为 `float` ，则结果将变为正零或负零。</span><span class="sxs-lookup"><span data-stu-id="84656-300">If the `double` value is too small to represent as a `float`, the result becomes positive zero or negative zero.</span></span> <span data-ttu-id="84656-301">如果 `double` 值太大而无法表示为 `float` ，则结果将变为正无穷或负无穷。</span><span class="sxs-lookup"><span data-stu-id="84656-301">If the `double` value is too large to represent as a `float`, the result becomes positive infinity or negative infinity.</span></span> <span data-ttu-id="84656-302">如果 `double` 该值为 nan，则结果也为 nan。</span><span class="sxs-lookup"><span data-stu-id="84656-302">If the `double` value is NaN, the result is also NaN.</span></span>
*  <span data-ttu-id="84656-303">对于从 `float` 或到的 `double` 转换 `decimal` ，将源值转换为 `decimal` 表示形式，并在第28位小数后舍入为最接近的数（如果需要 ([decimal 类型](types.md#the-decimal-type)) 。</span><span class="sxs-lookup"><span data-stu-id="84656-303">For a conversion from `float` or `double` to `decimal`, the source value is converted to `decimal` representation and rounded to the nearest number after the 28th decimal place if required ([The decimal type](types.md#the-decimal-type)).</span></span> <span data-ttu-id="84656-304">如果源值太小而无法表示为 `decimal` ，则结果将变为零。</span><span class="sxs-lookup"><span data-stu-id="84656-304">If the source value is too small to represent as a `decimal`, the result becomes zero.</span></span> <span data-ttu-id="84656-305">如果源值为 NaN、无限大或太大而无法表示为 `decimal` ， `System.OverflowException` 则会引发。</span><span class="sxs-lookup"><span data-stu-id="84656-305">If the source value is NaN, infinity, or too large to represent as a `decimal`, a `System.OverflowException` is thrown.</span></span>
*  <span data-ttu-id="84656-306">对于从 `decimal` 到或的 `float` 转换 `double` ，将 `decimal` 值舍入为最接近 `double` 的 `float` 值或值。</span><span class="sxs-lookup"><span data-stu-id="84656-306">For a conversion from `decimal` to `float` or `double`, the `decimal` value is rounded to the nearest `double` or `float` value.</span></span> <span data-ttu-id="84656-307">虽然这种转换可能会丢失精度，但它永远不会引发异常。</span><span class="sxs-lookup"><span data-stu-id="84656-307">While this conversion may lose precision, it never causes an exception to be thrown.</span></span>

### <a name="explicit-enumeration-conversions"></a><span data-ttu-id="84656-308">显式枚举转换</span><span class="sxs-lookup"><span data-stu-id="84656-308">Explicit enumeration conversions</span></span>

<span data-ttu-id="84656-309">显式枚举转换为：</span><span class="sxs-lookup"><span data-stu-id="84656-309">The explicit enumeration conversions are:</span></span>

*  <span data-ttu-id="84656-310">从 `sbyte` 、 `byte` 、、、、、、、、、 `short` `ushort` `int` `uint` `long` `ulong` `char` `float` `double` 或 `decimal` 到任何 *enum_type*。</span><span class="sxs-lookup"><span data-stu-id="84656-310">From `sbyte`, `byte`, `short`, `ushort`, `int`, `uint`, `long`, `ulong`, `char`, `float`, `double`, or `decimal` to any *enum_type*.</span></span>
*  <span data-ttu-id="84656-311">从任何 *enum_type* 到、、、、、、、、、、 `sbyte` `byte` `short` `ushort` `int` `uint` `long` `ulong` `char` `float` `double` 或 `decimal` 。</span><span class="sxs-lookup"><span data-stu-id="84656-311">From any *enum_type* to `sbyte`, `byte`, `short`, `ushort`, `int`, `uint`, `long`, `ulong`, `char`, `float`, `double`, or `decimal`.</span></span>
*  <span data-ttu-id="84656-312">从任何 *enum_type* 到任何其他 *enum_type*。</span><span class="sxs-lookup"><span data-stu-id="84656-312">From any *enum_type* to any other *enum_type*.</span></span>

<span data-ttu-id="84656-313">处理两种类型之间的显式枚举转换的方式是将任何参与的 *enum_type* 视为 *enum_type* 的基础类型，然后在生成的类型之间执行隐式或显式数字转换。</span><span class="sxs-lookup"><span data-stu-id="84656-313">An explicit enumeration conversion between two types is processed by treating any participating *enum_type* as the underlying type of that *enum_type*, and then performing an implicit or explicit numeric conversion between the resulting types.</span></span> <span data-ttu-id="84656-314">例如，假设有一个 `E` 具有和基础类型的 enum_type，则从到的显式数字转换 `int` 会将从到的转换 `E` `byte` 作为显式数字转换处理 (从到的) [显式](conversions.md#explicit-numeric-conversions)数字转换 `int` `byte` ，并将从) 到的 `byte` `E` [隐式](conversions.md#implicit-numeric-conversions)数值转换作为 (隐 `byte` `int` 式数值转换处理。</span><span class="sxs-lookup"><span data-stu-id="84656-314">For example, given an *enum_type* `E` with and underlying type of `int`, a conversion from `E` to `byte` is processed as an explicit numeric conversion ([Explicit numeric conversions](conversions.md#explicit-numeric-conversions)) from `int` to `byte`, and a conversion from `byte` to `E` is processed as an implicit numeric conversion ([Implicit numeric conversions](conversions.md#implicit-numeric-conversions)) from `byte` to `int`.</span></span>

### <a name="explicit-nullable-conversions"></a><span data-ttu-id="84656-315">显式可为空转换</span><span class="sxs-lookup"><span data-stu-id="84656-315">Explicit nullable conversions</span></span>

<span data-ttu-id="84656-316">***显式可以为 null 的转换*** 允许对不可以为 null 的值类型进行操作的预定义显式转换也可用于这些类型的可以为 null 的形式。</span><span class="sxs-lookup"><span data-stu-id="84656-316">***Explicit nullable conversions*** permit predefined explicit conversions that operate on non-nullable value types to also be used with nullable forms of those types.</span></span> <span data-ttu-id="84656-317">对于从不可为 null 的值类型转换为不可为 null 的值类型的每个预定义的显式转换 `S` `T` ([标识转换](conversions.md#identity-conversion)、 [隐式数值转换](conversions.md#implicit-numeric-conversions)、 [隐式枚举转换](conversions.md#implicit-enumeration-conversions)、 [显式数字转换](conversions.md#explicit-numeric-conversions)和 [显式枚举转换](conversions.md#explicit-enumeration-conversions)) ，可以为 null 的转换存在：</span><span class="sxs-lookup"><span data-stu-id="84656-317">For each of the predefined explicit conversions that convert from a non-nullable value type `S` to a non-nullable value type `T` ([Identity conversion](conversions.md#identity-conversion), [Implicit numeric conversions](conversions.md#implicit-numeric-conversions), [Implicit enumeration conversions](conversions.md#implicit-enumeration-conversions), [Explicit numeric conversions](conversions.md#explicit-numeric-conversions), and [Explicit enumeration conversions](conversions.md#explicit-enumeration-conversions)), the following nullable conversions exist:</span></span>

*  <span data-ttu-id="84656-318">从到的显式 `S?` 转换 `T?` 。</span><span class="sxs-lookup"><span data-stu-id="84656-318">An explicit conversion from `S?` to `T?`.</span></span>
*  <span data-ttu-id="84656-319">从到的显式 `S` 转换 `T?` 。</span><span class="sxs-lookup"><span data-stu-id="84656-319">An explicit conversion from `S` to `T?`.</span></span>
*  <span data-ttu-id="84656-320">从到的显式 `S?` 转换 `T` 。</span><span class="sxs-lookup"><span data-stu-id="84656-320">An explicit conversion from `S?` to `T`.</span></span>

<span data-ttu-id="84656-321">基于从到的基础转换计算可以为 null 的转换的 `S` `T` 过程如下所示：</span><span class="sxs-lookup"><span data-stu-id="84656-321">Evaluation of a nullable conversion based on an underlying conversion from `S` to `T` proceeds as follows:</span></span>

*  <span data-ttu-id="84656-322">如果可以为 null 的转换从 `S?` 到 `T?` ：</span><span class="sxs-lookup"><span data-stu-id="84656-322">If the nullable conversion is from `S?` to `T?`:</span></span>
    * <span data-ttu-id="84656-323">如果源值为 null (`HasValue` 属性为 false) ，则结果为类型的 null 值 `T?` 。</span><span class="sxs-lookup"><span data-stu-id="84656-323">If the source value is null (`HasValue` property is false), the result is the null value of type `T?`.</span></span>
    * <span data-ttu-id="84656-324">否则，转换将作为从到的解包进行计算 `S?` `S` ，后跟从到的 `S` 转换 `T` ，后跟从到的换行 `T` `T?` 。</span><span class="sxs-lookup"><span data-stu-id="84656-324">Otherwise, the conversion is evaluated as an unwrapping from `S?` to `S`, followed by the underlying conversion from `S` to `T`, followed by a wrapping from `T` to `T?`.</span></span>
*  <span data-ttu-id="84656-325">如果可以为 null 的转换从 `S` 到 `T?` ，则会将转换计算为从到的基础转换， `S` 并将从到 `T` 的换行 `T` `T?` 。</span><span class="sxs-lookup"><span data-stu-id="84656-325">If the nullable conversion is from `S` to `T?`, the conversion is evaluated as the underlying conversion from `S` to `T` followed by a wrapping from `T` to `T?`.</span></span>
*  <span data-ttu-id="84656-326">如果从到的可为 null 的转换为 `S?` `T` ，则会将转换计算为从到的解包， `S?` `S` 后跟从到的基础转换 `S` `T` 。</span><span class="sxs-lookup"><span data-stu-id="84656-326">If the nullable conversion is from `S?` to `T`, the conversion is evaluated as an unwrapping from `S?` to `S` followed by the underlying conversion from `S` to `T`.</span></span>

<span data-ttu-id="84656-327">请注意，如果值为，则对可以为 null 的值进行解包的尝试将引发异常 `null` 。</span><span class="sxs-lookup"><span data-stu-id="84656-327">Note that an attempt to unwrap a nullable value will throw an exception if the value is `null`.</span></span>

### <a name="explicit-reference-conversions"></a><span data-ttu-id="84656-328">显式引用转换</span><span class="sxs-lookup"><span data-stu-id="84656-328">Explicit reference conversions</span></span>

<span data-ttu-id="84656-329">显式引用转换为：</span><span class="sxs-lookup"><span data-stu-id="84656-329">The explicit reference conversions are:</span></span>

*  <span data-ttu-id="84656-330">从 `object` 和 `dynamic` 到任何其他 *reference_type*。</span><span class="sxs-lookup"><span data-stu-id="84656-330">From `object` and `dynamic` to any other *reference_type*.</span></span>
*  <span data-ttu-id="84656-331">从任何 *class_type* `S` 到任何 *class_type* `T` ，提供的 `S` 是的基类 `T` 。</span><span class="sxs-lookup"><span data-stu-id="84656-331">From any *class_type* `S` to any *class_type* `T`, provided `S` is a base class of `T`.</span></span>
*  <span data-ttu-id="84656-332">从任何 *class_type* `S` 到任何 *interface_type* `T` ，提供 `S` 的不是密封的，并且不 `S` 会实现 `T` 。</span><span class="sxs-lookup"><span data-stu-id="84656-332">From any *class_type* `S` to any *interface_type* `T`, provided `S` is not sealed and provided `S` does not implement `T`.</span></span>
*  <span data-ttu-id="84656-333">从任何 *interface_type* `S` 到任何 *class_type* `T` `T` 均未密封或未提供 `T` 实现 `S` 。</span><span class="sxs-lookup"><span data-stu-id="84656-333">From any *interface_type* `S` to any *class_type* `T`, provided `T` is not sealed or provided `T` implements `S`.</span></span>
*  <span data-ttu-id="84656-334">从任何 *interface_type* `S` 到任何 *interface_type* `T` ，提供 `S` 的不是从派生的 `T` 。</span><span class="sxs-lookup"><span data-stu-id="84656-334">From any *interface_type* `S` to any *interface_type* `T`, provided `S` is not derived from `T`.</span></span>
*  <span data-ttu-id="84656-335">在 array_type 具有元素类型的 *array_type* 中 `S` ，如果 `SE`  `T` `TE` 满足以下所有条件：</span><span class="sxs-lookup"><span data-stu-id="84656-335">From an *array_type* `S` with an element type `SE` to an *array_type* `T` with an element type `TE`, provided all of the following are true:</span></span>
    * <span data-ttu-id="84656-336">`S` 和 `T` 仅因元素类型而异。</span><span class="sxs-lookup"><span data-stu-id="84656-336">`S` and `T` differ only in element type.</span></span> <span data-ttu-id="84656-337">换言之， `S` 和 `T` 具有相同的维数。</span><span class="sxs-lookup"><span data-stu-id="84656-337">In other words, `S` and `T` have the same number of dimensions.</span></span>
    * <span data-ttu-id="84656-338">`SE`和 `TE` 都是 *reference_type*。</span><span class="sxs-lookup"><span data-stu-id="84656-338">Both `SE` and `TE` are *reference_type* s.</span></span>
    * <span data-ttu-id="84656-339">存在从到的显式引用 `SE` 转换 `TE` 。</span><span class="sxs-lookup"><span data-stu-id="84656-339">An explicit reference conversion exists from `SE` to `TE`.</span></span>
*  <span data-ttu-id="84656-340">从 `System.Array` 及其实现的接口到任何 *array_type*。</span><span class="sxs-lookup"><span data-stu-id="84656-340">From `System.Array` and the interfaces it implements to any *array_type*.</span></span>
*  <span data-ttu-id="84656-341">从一维数组类型 `S[]` 到 `System.Collections.Generic.IList<T>` 及其基接口，前提是存在从到的显式引用转换 `S` `T` 。</span><span class="sxs-lookup"><span data-stu-id="84656-341">From a single-dimensional array type `S[]` to `System.Collections.Generic.IList<T>` and its base interfaces, provided that there is an explicit reference conversion from `S` to `T`.</span></span>
*  <span data-ttu-id="84656-342">从 `System.Collections.Generic.IList<S>` 及其基接口到一维数组类型 `T[]` ，前提是存在从到的显式标识或引用转换 `S` `T` 。</span><span class="sxs-lookup"><span data-stu-id="84656-342">From `System.Collections.Generic.IList<S>` and its base interfaces to a single-dimensional array type `T[]`, provided that there is an explicit identity or reference conversion from `S` to `T`.</span></span>
*  <span data-ttu-id="84656-343">从 `System.Delegate` 及其实现的接口到任何 *delegate_type*。</span><span class="sxs-lookup"><span data-stu-id="84656-343">From `System.Delegate` and the interfaces it implements to any *delegate_type*.</span></span>
*  <span data-ttu-id="84656-344">如果引用类型 `T` 具有到引用类型的显式引用转换 `T0` 并且 `T0` 具有标识转换，则从引用类型转换为引用类型 `T` 。</span><span class="sxs-lookup"><span data-stu-id="84656-344">From a reference type to a reference type `T` if it has an explicit reference conversion to a reference type `T0` and `T0` has an identity conversion `T`.</span></span>
*  <span data-ttu-id="84656-345">如果引用类型 `T` 具有到接口或委托类型的显式引用转换，则从引用类型转换为接口或委托类型 `T0` ，或者可以转换为 (方差 `T0` `T` `T` `T0` [转换](interfaces.md#variance-conversion)) 的变体。</span><span class="sxs-lookup"><span data-stu-id="84656-345">From a reference type to an interface or delegate type `T` if it has an explicit reference conversion to an interface or delegate type `T0` and either `T0` is variance-convertible to `T` or `T` is variance-convertible to `T0` ([Variance conversion](interfaces.md#variance-conversion)).</span></span>
*  <span data-ttu-id="84656-346">从 `D<S1...Sn>` 到， `D<T1...Tn>` 其中 `D<X1...Xn>` 是泛型委托类型，与 `D<S1...Sn>` `D<T1...Tn>` 以下类型的每个类型参数均不兼容或不相同 `Xi` `D` ：</span><span class="sxs-lookup"><span data-stu-id="84656-346">From `D<S1...Sn>` to `D<T1...Tn>` where `D<X1...Xn>` is a generic delegate type, `D<S1...Sn>` is not compatible with or identical to `D<T1...Tn>`, and for each type parameter `Xi` of `D` the following holds:</span></span>
    * <span data-ttu-id="84656-347">如果 `Xi` 是固定的，则与 `Si` 相同 `Ti` 。</span><span class="sxs-lookup"><span data-stu-id="84656-347">If `Xi` is invariant, then `Si` is identical to `Ti`.</span></span>
    * <span data-ttu-id="84656-348">如果 `Xi` 是协变的，则存在隐式或显式标识或从到的引用转换 `Si` `Ti` 。</span><span class="sxs-lookup"><span data-stu-id="84656-348">If `Xi` is covariant, then there is an implicit or explicit identity or reference conversion from `Si` to `Ti`.</span></span>
    * <span data-ttu-id="84656-349">如果 `Xi` 为逆变，则 `Si` 和 `Ti` 都是相同或同时为这两个引用类型。</span><span class="sxs-lookup"><span data-stu-id="84656-349">If `Xi` is contravariant, then `Si` and `Ti` are either identical or both reference types.</span></span>
*  <span data-ttu-id="84656-350">涉及称为引用类型的类型参数的显式转换。</span><span class="sxs-lookup"><span data-stu-id="84656-350">Explicit conversions involving type parameters that are known to be reference types.</span></span> <span data-ttu-id="84656-351">有关涉及类型参数的显式转换的详细信息，请参阅 [涉及类型参数的显式转换](conversions.md#explicit-conversions-involving-type-parameters)。</span><span class="sxs-lookup"><span data-stu-id="84656-351">For more details on explicit conversions involving type parameters, see [Explicit conversions involving type parameters](conversions.md#explicit-conversions-involving-type-parameters).</span></span>

<span data-ttu-id="84656-352">显式引用转换是需要运行时检查以确保它们正确的引用类型之间的转换。</span><span class="sxs-lookup"><span data-stu-id="84656-352">The explicit reference conversions are those conversions between reference-types that require run-time checks to ensure they are correct.</span></span>

<span data-ttu-id="84656-353">若要在运行时成功进行显式引用转换，源操作数的值必须为 `null` ，或者源操作数引用的对象的实际类型必须是可通过隐式引用转换转换为目标类型的类型， ([隐式引用](conversions.md#implicit-reference-conversions) 转换) 或装箱转换 ([装箱](conversions.md#boxing-conversions) 转换) 。</span><span class="sxs-lookup"><span data-stu-id="84656-353">For an explicit reference conversion to succeed at run-time, the value of the source operand must be `null`, or the actual type of the object referenced by the source operand must be a type that can be converted to the destination type by an implicit reference conversion ([Implicit reference conversions](conversions.md#implicit-reference-conversions)) or boxing conversion ([Boxing conversions](conversions.md#boxing-conversions)).</span></span> <span data-ttu-id="84656-354">如果显式引用转换失败， `System.InvalidCastException` 将引发。</span><span class="sxs-lookup"><span data-stu-id="84656-354">If an explicit reference conversion fails, a `System.InvalidCastException` is thrown.</span></span>

<span data-ttu-id="84656-355">引用转换、隐式或显式转换决不会更改正在转换的对象的引用标识。</span><span class="sxs-lookup"><span data-stu-id="84656-355">Reference conversions, implicit or explicit, never change the referential identity of the object being converted.</span></span> <span data-ttu-id="84656-356">换言之，虽然引用转换可以更改引用的类型，但它不会更改引用的对象的类型或值。</span><span class="sxs-lookup"><span data-stu-id="84656-356">In other words, while a reference conversion may change the type of the reference, it never changes the type or value of the object being referred to.</span></span>

### <a name="unboxing-conversions"></a><span data-ttu-id="84656-357">取消装箱转换</span><span class="sxs-lookup"><span data-stu-id="84656-357">Unboxing conversions</span></span>

<span data-ttu-id="84656-358">取消装箱转换允许将引用类型显式转换为 *value_type*。</span><span class="sxs-lookup"><span data-stu-id="84656-358">An unboxing conversion permits a reference type to be explicitly converted to a *value_type*.</span></span> <span data-ttu-id="84656-359">从类型 `object` `dynamic` 和 `System.ValueType` 任何 *non_nullable_value_type*，以及从任何 *interface_type* 到实现 *interface_type* 的任何 *non_nullable_value_type* 的取消装箱转换。</span><span class="sxs-lookup"><span data-stu-id="84656-359">An unboxing conversion exists from the types `object`, `dynamic` and `System.ValueType` to any *non_nullable_value_type*, and from any *interface_type* to any *non_nullable_value_type* that implements the *interface_type*.</span></span> <span data-ttu-id="84656-360">此外 `System.Enum` ，可以将类型取消装箱为任意 *enum_type*。</span><span class="sxs-lookup"><span data-stu-id="84656-360">Furthermore type `System.Enum` can be unboxed to any *enum_type*.</span></span>

<span data-ttu-id="84656-361">如果从引用类型到 *nullable_type* 的基础 *non_nullable_value_type* 的取消装箱转换存在，则取消装箱转换将从引用类型转换为 *nullable_type* 。</span><span class="sxs-lookup"><span data-stu-id="84656-361">An unboxing conversion exists from a reference type to a *nullable_type* if an unboxing conversion exists from the reference type to the underlying *non_nullable_value_type* of the *nullable_type*.</span></span>

<span data-ttu-id="84656-362">如果值类型 `S` `I` 具有从接口类型的取消装箱转换 `I0` ，并且其 `I0` 标识转换为，则值类型会从接口类型进行取消装箱转换 `I` 。</span><span class="sxs-lookup"><span data-stu-id="84656-362">A value type `S` has an unboxing conversion from an interface type `I` if it has an unboxing conversion from an interface type `I0` and `I0` has an identity conversion to `I`.</span></span>

<span data-ttu-id="84656-363">如果某个值类型 `S` `I` 具有从接口或委托类型的取消装箱转换，并且该类型的变体转换为 `I0` `I0` `I` `I` `I0` ([方差转换](interfaces.md#variance-conversion)) ，则它会从接口类型进行取消装箱转换。</span><span class="sxs-lookup"><span data-stu-id="84656-363">A value type `S` has an unboxing conversion from an interface type `I` if it has an unboxing conversion from an interface or delegate type `I0` and either `I0` is variance-convertible to `I` or `I` is variance-convertible to `I0` ([Variance conversion](interfaces.md#variance-conversion)).</span></span>

<span data-ttu-id="84656-364">取消装箱操作包括：首先检查对象实例是否是给定 *value_type* 的装箱值，然后将值复制到该实例之外。</span><span class="sxs-lookup"><span data-stu-id="84656-364">An unboxing operation consists of first checking that the object instance is a boxed value of the given *value_type*, and then copying the value out of the instance.</span></span> <span data-ttu-id="84656-365">取消装箱对 *nullable_type* 的空引用将生成 *nullable_type* 的 null 值。</span><span class="sxs-lookup"><span data-stu-id="84656-365">Unboxing a null reference to a *nullable_type* produces the null value of the *nullable_type*.</span></span> <span data-ttu-id="84656-366">结构可以从类型取消装箱 `System.ValueType` ，因为这是所有结构的基类 ([继承](structs.md#inheritance)) 。</span><span class="sxs-lookup"><span data-stu-id="84656-366">A struct can be unboxed from the type `System.ValueType`, since that is a base class for all structs ([Inheritance](structs.md#inheritance)).</span></span>

<span data-ttu-id="84656-367">取消装箱转换中进一步介绍 [了取消装箱](types.md#unboxing-conversions)转换。</span><span class="sxs-lookup"><span data-stu-id="84656-367">Unboxing conversions are described further in [Unboxing conversions](types.md#unboxing-conversions).</span></span>

### <a name="explicit-dynamic-conversions"></a><span data-ttu-id="84656-368">显式动态转换</span><span class="sxs-lookup"><span data-stu-id="84656-368">Explicit dynamic conversions</span></span>

<span data-ttu-id="84656-369">存在从类型为的表达式 `dynamic` 到任何类型的显式动态转换 `T` 。</span><span class="sxs-lookup"><span data-stu-id="84656-369">An explicit dynamic conversion exists from an expression of type `dynamic` to any type `T`.</span></span> <span data-ttu-id="84656-370">转换是动态绑定 ([动态绑定](expressions.md#dynamic-binding)) ，这意味着将在运行时从表达式的运行时类型中查找显式转换 `T` 。</span><span class="sxs-lookup"><span data-stu-id="84656-370">The conversion is dynamically bound ([Dynamic binding](expressions.md#dynamic-binding)), which means that an explicit conversion will be sought at run-time from the run-time type of the expression to `T`.</span></span> <span data-ttu-id="84656-371">如果未找到任何转换，则会引发运行时异常。</span><span class="sxs-lookup"><span data-stu-id="84656-371">If no conversion is found, a run-time exception is thrown.</span></span>

<span data-ttu-id="84656-372">如果不需要转换的动态绑定，则可以先将该表达式转换为 `object` ，然后再转换为所需的类型。</span><span class="sxs-lookup"><span data-stu-id="84656-372">If dynamic binding of the conversion is not desired, the expression can be first converted to `object`, and then to the desired type.</span></span>

<span data-ttu-id="84656-373">假定定义了下面的类：</span><span class="sxs-lookup"><span data-stu-id="84656-373">Assume the following class is defined:</span></span>
```csharp
class C
{
    int i;

    public C(int i) { this.i = i; }

    public static explicit operator C(string s) 
    {
        return new C(int.Parse(s));
    }
}
```

<span data-ttu-id="84656-374">下面的示例阐释了显式动态转换：</span><span class="sxs-lookup"><span data-stu-id="84656-374">The following example illustrates explicit dynamic conversions:</span></span>
```csharp
object o  = "1";
dynamic d = "2";

var c1 = (C)o; // Compiles, but explicit reference conversion fails
var c2 = (C)d; // Compiles and user defined conversion succeeds
```

<span data-ttu-id="84656-375">在编译时，将转换为的最佳转换为 `o` `C` 显式引用转换。</span><span class="sxs-lookup"><span data-stu-id="84656-375">The best conversion of `o` to `C` is found at compile-time to be an explicit reference conversion.</span></span> <span data-ttu-id="84656-376">这会在运行时失败，因为 `"1"` 事实上并不是 `C` 。</span><span class="sxs-lookup"><span data-stu-id="84656-376">This fails at run-time, because `"1"` is not in fact a `C`.</span></span> <span data-ttu-id="84656-377">但是，将转换为，以 `d` `C` 显式动态转换为运行时，将在其中找到用户定义的运行时类型到的转换 `d`  --  `string` `C` ，并成功。</span><span class="sxs-lookup"><span data-stu-id="84656-377">The conversion of `d` to `C` however, as an explicit dynamic conversion, is suspended to run-time, where a user defined conversion from the run-time type of `d` -- `string` -- to `C` is found, and succeeds.</span></span>

### <a name="explicit-conversions-involving-type-parameters"></a><span data-ttu-id="84656-378">涉及类型参数的显式转换</span><span class="sxs-lookup"><span data-stu-id="84656-378">Explicit conversions involving type parameters</span></span>

<span data-ttu-id="84656-379">给定类型参数存在以下显式转换 `T` ：</span><span class="sxs-lookup"><span data-stu-id="84656-379">The following explicit conversions exist for a given type parameter `T`:</span></span>

*  <span data-ttu-id="84656-380">从的有效基类 `C` `T` ，到的 `T` 任何基类 `C` `T` 。</span><span class="sxs-lookup"><span data-stu-id="84656-380">From the effective base class `C` of `T` to `T` and from any base class of `C` to `T`.</span></span> <span data-ttu-id="84656-381">在运行时，如果 `T` 是值类型，则转换将作为取消装箱转换执行。</span><span class="sxs-lookup"><span data-stu-id="84656-381">At run-time, if `T` is a value type, the conversion is executed as an unboxing conversion.</span></span> <span data-ttu-id="84656-382">否则，转换将作为显式引用转换或标识转换执行。</span><span class="sxs-lookup"><span data-stu-id="84656-382">Otherwise, the conversion is executed as an explicit reference conversion or identity conversion.</span></span>
*  <span data-ttu-id="84656-383">从任何接口类型到 `T` 。</span><span class="sxs-lookup"><span data-stu-id="84656-383">From any interface type to `T`.</span></span> <span data-ttu-id="84656-384">在运行时，如果 `T` 是值类型，则转换将作为取消装箱转换执行。</span><span class="sxs-lookup"><span data-stu-id="84656-384">At run-time, if `T` is a value type, the conversion is executed as an unboxing conversion.</span></span> <span data-ttu-id="84656-385">否则，转换将作为显式引用转换或标识转换执行。</span><span class="sxs-lookup"><span data-stu-id="84656-385">Otherwise, the conversion is executed as an explicit reference conversion or identity conversion.</span></span>
*  <span data-ttu-id="84656-386">`T`  `I` 如果尚未从到的隐式转换，则从到任何 `T` interface_type `I` 。</span><span class="sxs-lookup"><span data-stu-id="84656-386">From `T` to any *interface_type* `I` provided there is not already an implicit conversion from `T` to `I`.</span></span> <span data-ttu-id="84656-387">在运行时，如果 `T` 是值类型，则转换将作为装箱转换执行，后跟显式引用转换。</span><span class="sxs-lookup"><span data-stu-id="84656-387">At run-time, if `T` is a value type, the conversion is executed as a boxing conversion followed by an explicit reference conversion.</span></span> <span data-ttu-id="84656-388">否则，转换将作为显式引用转换或标识转换执行。</span><span class="sxs-lookup"><span data-stu-id="84656-388">Otherwise, the conversion is executed as an explicit reference conversion or identity conversion.</span></span>
*  <span data-ttu-id="84656-389">从类型参数 `U` 到 `T` ，前提条件 `T` 取决于 `U`)  ([类型参数约束](classes.md#type-parameter-constraints) 。</span><span class="sxs-lookup"><span data-stu-id="84656-389">From a type parameter `U` to `T`, provided `T` depends on `U` ([Type parameter constraints](classes.md#type-parameter-constraints)).</span></span> <span data-ttu-id="84656-390">在运行时，如果 `U` 是值类型，则和的 `T` `U` 类型必须相同，并且不执行任何转换。</span><span class="sxs-lookup"><span data-stu-id="84656-390">At run-time, if `U` is a value type, then `T` and `U` are necessarily the same type and no conversion is performed.</span></span> <span data-ttu-id="84656-391">否则，如果 `T` 是值类型，则转换将作为取消装箱转换执行。</span><span class="sxs-lookup"><span data-stu-id="84656-391">Otherwise, if `T` is a value type, the conversion is executed as an unboxing conversion.</span></span> <span data-ttu-id="84656-392">否则，转换将作为显式引用转换或标识转换执行。</span><span class="sxs-lookup"><span data-stu-id="84656-392">Otherwise, the conversion is executed as an explicit reference conversion or identity conversion.</span></span>

<span data-ttu-id="84656-393">如果 `T` 已知为引用类型，则上述转换均分类为显式引用转换 () 的 [显式引用转换](conversions.md#explicit-reference-conversions) 。</span><span class="sxs-lookup"><span data-stu-id="84656-393">If `T` is known to be a reference type, the conversions above are all classified as explicit reference conversions ([Explicit reference conversions](conversions.md#explicit-reference-conversions)).</span></span> <span data-ttu-id="84656-394">如果 `T` 未知类型为引用类型，则上述转换归类为取消装箱转换 ([取消装箱转换](conversions.md#unboxing-conversions)) 。</span><span class="sxs-lookup"><span data-stu-id="84656-394">If `T` is not known to be a reference type, the conversions above are classified as unboxing conversions ([Unboxing conversions](conversions.md#unboxing-conversions)).</span></span>

<span data-ttu-id="84656-395">以上规则不允许从不受约束的类型形参直接转换为非接口类型，这可能会令人吃惊。</span><span class="sxs-lookup"><span data-stu-id="84656-395">The above rules do not permit a direct explicit conversion from an unconstrained type parameter to a non-interface type, which might be surprising.</span></span> <span data-ttu-id="84656-396">此规则的原因是为了防止混淆并使此类转换的语义清晰。</span><span class="sxs-lookup"><span data-stu-id="84656-396">The reason for this rule is to prevent confusion and make the semantics of such conversions clear.</span></span> <span data-ttu-id="84656-397">例如，请考虑以下声明：</span><span class="sxs-lookup"><span data-stu-id="84656-397">For example, consider the following declaration:</span></span>
```csharp
class X<T>
{
    public static long F(T t) {
        return (long)t;                // Error 
    }
}
```

<span data-ttu-id="84656-398">如果允许将的直接显式转换 `t` 为 `int` ，则很可能会出现这样的情况 `X<int>.F(7)`  `7L` 。</span><span class="sxs-lookup"><span data-stu-id="84656-398">If the direct explicit conversion of `t` to `int` were permitted, one might easily expect that `X<int>.F(7)` would return `7L`.</span></span> <span data-ttu-id="84656-399">但是，它不会这样，因为仅当已知类型在绑定时是数字时才考虑标准数字转换。</span><span class="sxs-lookup"><span data-stu-id="84656-399">However, it would not, because the standard numeric conversions are only considered when the types are known to be numeric at binding-time.</span></span> <span data-ttu-id="84656-400">为了使语义清晰明了，必须改为编写以上示例：</span><span class="sxs-lookup"><span data-stu-id="84656-400">In order to make the semantics clear, the above example must instead be written:</span></span>
```csharp
class X<T>
{
    public static long F(T t) {
        return (long)(object)t;        // Ok, but will only work when T is long
    }
}
```

<span data-ttu-id="84656-401">此代码现在将进行编译，但执行 `X<int>.F(7)` 会在运行时引发异常，因为装箱 `int` 无法直接转换为 `long` 。</span><span class="sxs-lookup"><span data-stu-id="84656-401">This code will now compile but executing `X<int>.F(7)` would then throw an exception at run-time, since a boxed `int` cannot be converted directly to a `long`.</span></span>

### <a name="user-defined-explicit-conversions"></a><span data-ttu-id="84656-402">用户定义的显式转换</span><span class="sxs-lookup"><span data-stu-id="84656-402">User-defined explicit conversions</span></span>

<span data-ttu-id="84656-403">用户定义的显式转换包含一个可选的标准显式转换，然后执行用户定义的隐式或显式转换运算符，然后执行另一个可选的标准显式转换。</span><span class="sxs-lookup"><span data-stu-id="84656-403">A user-defined explicit conversion consists of an optional standard explicit conversion, followed by execution of a user-defined implicit or explicit conversion operator, followed by another optional standard explicit conversion.</span></span> <span data-ttu-id="84656-404">用于评估用户定义的显式转换的确切规则在 [处理用户定义的显式转换](conversions.md#processing-of-user-defined-explicit-conversions)中进行了介绍。</span><span class="sxs-lookup"><span data-stu-id="84656-404">The exact rules for evaluating user-defined explicit conversions are described in [Processing of user-defined explicit conversions](conversions.md#processing-of-user-defined-explicit-conversions).</span></span>

## <a name="standard-conversions"></a><span data-ttu-id="84656-405">标准转换</span><span class="sxs-lookup"><span data-stu-id="84656-405">Standard conversions</span></span>

<span data-ttu-id="84656-406">标准转换是那些预定义的转换，这些转换可作为用户定义的转换的一部分出现。</span><span class="sxs-lookup"><span data-stu-id="84656-406">The standard conversions are those pre-defined conversions that can occur as part of a user-defined conversion.</span></span>

### <a name="standard-implicit-conversions"></a><span data-ttu-id="84656-407">标准隐式转换</span><span class="sxs-lookup"><span data-stu-id="84656-407">Standard implicit conversions</span></span>

<span data-ttu-id="84656-408">以下隐式转换归类为标准隐式转换：</span><span class="sxs-lookup"><span data-stu-id="84656-408">The following implicit conversions are classified as standard implicit conversions:</span></span>

*  <span data-ttu-id="84656-409">标识转换 ([标识转换](conversions.md#identity-conversion)) </span><span class="sxs-lookup"><span data-stu-id="84656-409">Identity conversions ([Identity conversion](conversions.md#identity-conversion))</span></span>
*  <span data-ttu-id="84656-410">隐式数值转换 ([隐式数值转换](conversions.md#implicit-numeric-conversions)) </span><span class="sxs-lookup"><span data-stu-id="84656-410">Implicit numeric conversions ([Implicit numeric conversions](conversions.md#implicit-numeric-conversions))</span></span>
*  <span data-ttu-id="84656-411">隐式可为 null 转换 ([隐式可为 null 转换](conversions.md#implicit-nullable-conversions)) </span><span class="sxs-lookup"><span data-stu-id="84656-411">Implicit nullable conversions ([Implicit nullable conversions](conversions.md#implicit-nullable-conversions))</span></span>
*  <span data-ttu-id="84656-412">隐 [式引用转换 (隐](conversions.md#implicit-reference-conversions) 式引用转换) </span><span class="sxs-lookup"><span data-stu-id="84656-412">Implicit reference conversions ([Implicit reference conversions](conversions.md#implicit-reference-conversions))</span></span>
*  <span data-ttu-id="84656-413">[装箱转换 (装箱](conversions.md#boxing-conversions)转换) </span><span class="sxs-lookup"><span data-stu-id="84656-413">Boxing conversions ([Boxing conversions](conversions.md#boxing-conversions))</span></span>
*  <span data-ttu-id="84656-414">隐式常量表达式转换 ([隐式常量表达式转换](conversions.md#implicit-constant-expression-conversions)) </span><span class="sxs-lookup"><span data-stu-id="84656-414">Implicit constant expression conversions ([Implicit constant expression conversions](conversions.md#implicit-constant-expression-conversions))</span></span>
*  <span data-ttu-id="84656-415">涉及类型参数的隐式转换 ([涉及类型参数的隐式转换](conversions.md#implicit-conversions-involving-type-parameters)) </span><span class="sxs-lookup"><span data-stu-id="84656-415">Implicit conversions involving type parameters ([Implicit conversions involving type parameters](conversions.md#implicit-conversions-involving-type-parameters))</span></span>

<span data-ttu-id="84656-416">标准隐式转换专门排除用户定义的隐式转换。</span><span class="sxs-lookup"><span data-stu-id="84656-416">The standard implicit conversions specifically exclude user-defined implicit conversions.</span></span>

### <a name="standard-explicit-conversions"></a><span data-ttu-id="84656-417">标准显式转换</span><span class="sxs-lookup"><span data-stu-id="84656-417">Standard explicit conversions</span></span>

<span data-ttu-id="84656-418">标准显式转换均为标准隐式转换，以及与之相反的标准隐式转换的显式转换的子集。</span><span class="sxs-lookup"><span data-stu-id="84656-418">The standard explicit conversions are all standard implicit conversions plus the subset of the explicit conversions for which an opposite standard implicit conversion exists.</span></span> <span data-ttu-id="84656-419">换言之，如果存在从类型到类型的标准隐式转换 `A` `B` ，则从类型 `A` 到类型 `B` 以及从类型到类型的标准显式转换 `B` `A` 。</span><span class="sxs-lookup"><span data-stu-id="84656-419">In other words, if a standard implicit conversion exists from a type `A` to a type `B`, then a standard explicit conversion exists from type `A` to type `B` and from type `B` to type `A`.</span></span>

## <a name="user-defined-conversions"></a><span data-ttu-id="84656-420">用户定义的转换</span><span class="sxs-lookup"><span data-stu-id="84656-420">User-defined conversions</span></span>

<span data-ttu-id="84656-421">C # 允许使用 ***用户定义的转换*** 来扩充预定义的隐式和显式转换。</span><span class="sxs-lookup"><span data-stu-id="84656-421">C# allows the pre-defined implicit and explicit conversions to be augmented by ***user-defined conversions***.</span></span> <span data-ttu-id="84656-422">用户定义的转换是通过在类和结构类型中 ([转换运算符](classes.md#conversion-operators)) 声明转换运算符引入的。</span><span class="sxs-lookup"><span data-stu-id="84656-422">User-defined conversions are introduced by declaring conversion operators ([Conversion operators](classes.md#conversion-operators)) in class and struct types.</span></span>

### <a name="permitted-user-defined-conversions"></a><span data-ttu-id="84656-423">允许的用户定义的转换</span><span class="sxs-lookup"><span data-stu-id="84656-423">Permitted user-defined conversions</span></span>

<span data-ttu-id="84656-424">C # 只允许声明某些用户定义的转换。</span><span class="sxs-lookup"><span data-stu-id="84656-424">C# permits only certain user-defined conversions to be declared.</span></span> <span data-ttu-id="84656-425">特别是，不能重新定义已存在的隐式或显式转换。</span><span class="sxs-lookup"><span data-stu-id="84656-425">In particular, it is not possible to redefine an already existing implicit or explicit conversion.</span></span>

<span data-ttu-id="84656-426">对于给定的源类型 `S` 和目标类型 `T` ，如果 `S` 或 `T` 是可以为 null 的类型，则让 `S0` 和 `T0` 引用它们的基础类型，否则， `S0` 并 `T0` `S` `T` 分别为和。</span><span class="sxs-lookup"><span data-stu-id="84656-426">For a given source type `S` and target type `T`, if `S` or `T` are nullable types, let `S0` and `T0` refer to their underlying types, otherwise `S0` and `T0` are equal to `S` and `T` respectively.</span></span> <span data-ttu-id="84656-427">`S` `T` 仅当满足以下所有条件时，才允许类或结构声明从源类型到目标类型的转换：</span><span class="sxs-lookup"><span data-stu-id="84656-427">A class or struct is permitted to declare a conversion from a source type `S` to a target type `T` only if all of the following are true:</span></span>

*  <span data-ttu-id="84656-428">`S0` 和 `T0` 是不同的类型。</span><span class="sxs-lookup"><span data-stu-id="84656-428">`S0` and `T0` are different types.</span></span>
*  <span data-ttu-id="84656-429">`S0`或 `T0` 是发生运算符声明的类或结构类型。</span><span class="sxs-lookup"><span data-stu-id="84656-429">Either `S0` or `T0` is the class or struct type in which the operator declaration takes place.</span></span>
*  <span data-ttu-id="84656-430">`S0`和 `T0` 都不是 *interface_type*。</span><span class="sxs-lookup"><span data-stu-id="84656-430">Neither `S0` nor `T0` is an *interface_type*.</span></span>
*  <span data-ttu-id="84656-431">如果不包括用户定义的转换，则从到的转换不存在 `S` `T` `T` `S` 。</span><span class="sxs-lookup"><span data-stu-id="84656-431">Excluding user-defined conversions, a conversion does not exist from `S` to `T` or from `T` to `S`.</span></span>

<span data-ttu-id="84656-432">适用于用户定义的转换的限制将在 [转换运算符](classes.md#conversion-operators)中进一步讨论。</span><span class="sxs-lookup"><span data-stu-id="84656-432">The restrictions that apply to user-defined conversions are discussed further in [Conversion operators](classes.md#conversion-operators).</span></span>

### <a name="lifted-conversion-operators"></a><span data-ttu-id="84656-433">提升的转换运算符</span><span class="sxs-lookup"><span data-stu-id="84656-433">Lifted conversion operators</span></span>

<span data-ttu-id="84656-434">给定用户定义的转换运算符，该运算符将不可以为 null 的值类型转换 `S` 为不可为 null 的值类型 `T` ，并且存在将从转换为的 ***提升转换运算符*** `S?` `T?` 。</span><span class="sxs-lookup"><span data-stu-id="84656-434">Given a user-defined conversion operator that converts from a non-nullable value type `S` to a non-nullable value type `T`, a ***lifted conversion operator*** exists that converts from `S?` to `T?`.</span></span> <span data-ttu-id="84656-435">此提升转换运算符执行从到的解包，然后执行从到的 `S?` `S` 用户定义的转换 `S` `T` ，后跟从到的换行 `T` `T?` ，只不过空值 `S?` 直接转换为 null 值 `T?` 。</span><span class="sxs-lookup"><span data-stu-id="84656-435">This lifted conversion operator performs an unwrapping from `S?` to `S` followed by the user-defined conversion from `S` to `T` followed by a wrapping from `T` to `T?`, except that a null valued `S?` converts directly to a null valued `T?`.</span></span>

<span data-ttu-id="84656-436">提升的转换运算符与其基础用户定义转换运算符具有相同的隐式或显式分类。</span><span class="sxs-lookup"><span data-stu-id="84656-436">A lifted conversion operator has the same implicit or explicit classification as its underlying user-defined conversion operator.</span></span> <span data-ttu-id="84656-437">"用户定义的转换" 一词适用于用户定义的转换运算符和提升的转换运算符。</span><span class="sxs-lookup"><span data-stu-id="84656-437">The term "user-defined conversion" applies to the use of both user-defined and lifted conversion operators.</span></span>

### <a name="evaluation-of-user-defined-conversions"></a><span data-ttu-id="84656-438">计算用户定义的转换</span><span class="sxs-lookup"><span data-stu-id="84656-438">Evaluation of user-defined conversions</span></span>

<span data-ttu-id="84656-439">用户定义的转换将其类型（称为 ***源类型** _）的值转换为另一种类型，称为 _*_目标类型_\*_。</span><span class="sxs-lookup"><span data-stu-id="84656-439">A user-defined conversion converts a value from its type, called the ***source type** _, to another type, called the _*_target type_\*_.</span></span> <span data-ttu-id="84656-440">计算用户定义的转换中心，查找特定源和目标类型的 _ *_最具体_* 的 \* 用户定义转换运算符。</span><span class="sxs-lookup"><span data-stu-id="84656-440">Evaluation of a user-defined conversion centers on finding the _ *_most specific_*\* user-defined conversion operator for the particular source and target types.</span></span> <span data-ttu-id="84656-441">此确定分为几个步骤：</span><span class="sxs-lookup"><span data-stu-id="84656-441">This determination is broken into several steps:</span></span>

*  <span data-ttu-id="84656-442">查找要将用户定义的转换运算符视为其中的一组类和结构。</span><span class="sxs-lookup"><span data-stu-id="84656-442">Finding the set of classes and structs from which user-defined conversion operators will be considered.</span></span> <span data-ttu-id="84656-443">此集由源类型及其基类和目标类型及其基类 (，这些隐式假设只有类和结构可以声明用户定义的运算符，并且非类类型没有基类) 。</span><span class="sxs-lookup"><span data-stu-id="84656-443">This set consists of the source type and its base classes and the target type and its base classes (with the implicit assumptions that only classes and structs can declare user-defined operators, and that non-class types have no base classes).</span></span> <span data-ttu-id="84656-444">对于此步骤，如果源类型或目标类型为 *nullable_type*，则改为使用它们的基础类型。</span><span class="sxs-lookup"><span data-stu-id="84656-444">For the purposes of this step, if either the source or target type is a *nullable_type*, their underlying type is used instead.</span></span>
*  <span data-ttu-id="84656-445">从该类型集中确定哪些用户定义的转换运算符适用。</span><span class="sxs-lookup"><span data-stu-id="84656-445">From that set of types, determining which user-defined and lifted conversion operators are applicable.</span></span> <span data-ttu-id="84656-446">要使转换运算符适用，必须可以执行标准转换 ([标准](conversions.md#standard-conversions) 转换) 从源类型转换为运算符的操作数类型，并且必须能够执行从运算符的结果类型到目标类型的标准转换操作。</span><span class="sxs-lookup"><span data-stu-id="84656-446">For a conversion operator to be applicable, it must be possible to perform a standard conversion ([Standard conversions](conversions.md#standard-conversions)) from the source type to the operand type of the operator, and it must be possible to perform a standard conversion from the result type of the operator to the target type.</span></span>
*  <span data-ttu-id="84656-447">从一组适用的用户定义的运算符，确定哪个运算符明确是最具体的。</span><span class="sxs-lookup"><span data-stu-id="84656-447">From the set of applicable user-defined operators, determining which operator is unambiguously the most specific.</span></span> <span data-ttu-id="84656-448">一般来说，最特定的运算符是操作数类型与源类型 "最接近" 的运算符，其结果类型与目标类型 "最近"。</span><span class="sxs-lookup"><span data-stu-id="84656-448">In general terms, the most specific operator is the operator whose operand type is "closest" to the source type and whose result type is "closest" to the target type.</span></span> <span data-ttu-id="84656-449">用户定义的转换运算符优先于提升转换运算符。</span><span class="sxs-lookup"><span data-stu-id="84656-449">User-defined conversion operators are preferred over lifted conversion operators.</span></span> <span data-ttu-id="84656-450">以下部分定义了用于建立最具体的用户定义转换运算符的确切规则。</span><span class="sxs-lookup"><span data-stu-id="84656-450">The exact rules for establishing the most specific user-defined conversion operator are defined in the following sections.</span></span>

<span data-ttu-id="84656-451">确定了最特定的用户定义转换运算符后，用户定义的转换的实际执行操作包括多达三个步骤：</span><span class="sxs-lookup"><span data-stu-id="84656-451">Once a most specific user-defined conversion operator has been identified, the actual execution of the user-defined conversion involves up to three steps:</span></span>

*  <span data-ttu-id="84656-452">首先，如果需要，执行从源类型到用户定义或提升的转换运算符的操作数类型的标准转换。</span><span class="sxs-lookup"><span data-stu-id="84656-452">First, if required, performing a standard conversion from the source type to the operand type of the user-defined or lifted conversion operator.</span></span>
*  <span data-ttu-id="84656-453">接下来，调用用户定义或提升的转换运算符来执行转换。</span><span class="sxs-lookup"><span data-stu-id="84656-453">Next, invoking the user-defined or lifted conversion operator to perform the conversion.</span></span>
*  <span data-ttu-id="84656-454">最后，如果需要，执行从用户定义转换运算符的结果类型到目标类型的标准转换。</span><span class="sxs-lookup"><span data-stu-id="84656-454">Finally, if required, performing a standard conversion from the result type of the user-defined or lifted conversion operator to the target type.</span></span>

<span data-ttu-id="84656-455">用户定义的转换的计算从不涉及多个用户定义的转换运算符或提升的转换运算符。</span><span class="sxs-lookup"><span data-stu-id="84656-455">Evaluation of a user-defined conversion never involves more than one user-defined or lifted conversion operator.</span></span> <span data-ttu-id="84656-456">换句话说，从类型到类型的转换 `S` `T` 从不首先执行从到的用户定义的转换 `S` `X` ，然后执行从到的用户定义的转换 `X` `T` 。</span><span class="sxs-lookup"><span data-stu-id="84656-456">In other words, a conversion from type `S` to type `T` will never first execute a user-defined conversion from `S` to `X` and then execute a user-defined conversion from `X` to `T`.</span></span>

<span data-ttu-id="84656-457">以下各节提供了用户定义的隐式或显式转换的确切计算定义。</span><span class="sxs-lookup"><span data-stu-id="84656-457">Exact definitions of evaluation of user-defined implicit or explicit conversions are given in the following sections.</span></span> <span data-ttu-id="84656-458">定义使用以下术语：</span><span class="sxs-lookup"><span data-stu-id="84656-458">The definitions make use of the following terms:</span></span>

*  <span data-ttu-id="84656-459">如果标准隐式转换 (从类型到类型的标准 [隐式](conversions.md#standard-implicit-conversions)转换) `A` `B` ，并且和都不 `A` `B` *interface_type*，则称为 `A` \***包含** _ `B` ，并 `B` 被称为 _ *_包含_* \*  `A` 。</span><span class="sxs-lookup"><span data-stu-id="84656-459">If a standard implicit conversion ([Standard implicit conversions](conversions.md#standard-implicit-conversions)) exists from a type `A` to a type `B`, and if neither `A` nor `B` are *interface_type* s, then `A` is said to be ***encompassed by** _ `B`, and `B` is said to _ *_encompass_** `A`.</span></span>
*  <span data-ttu-id="84656-460">一组类型中包含的 ***最大的类型*** 是一种包含集内所有其他类型的类型。</span><span class="sxs-lookup"><span data-stu-id="84656-460">The ***most encompassing type*** in a set of types is the one type that encompasses all other types in the set.</span></span> <span data-ttu-id="84656-461">如果没有一种类型包含所有其他类型，则该集没有最大的包含类型。</span><span class="sxs-lookup"><span data-stu-id="84656-461">If no single type encompasses all other types, then the set has no most encompassing type.</span></span> <span data-ttu-id="84656-462">更直观地说，最包含的类型是集中的 "最大" 类型，每个其他类型都可以隐式转换为一种类型。</span><span class="sxs-lookup"><span data-stu-id="84656-462">In more intuitive terms, the most encompassing type is the "largest" type in the set—the one type to which each of the other types can be implicitly converted.</span></span>
*  <span data-ttu-id="84656-463">类型集中 ***最常被包含的类型*** 是一种类型，它由集中的所有其他类型所包含。</span><span class="sxs-lookup"><span data-stu-id="84656-463">The ***most encompassed type*** in a set of types is the one type that is encompassed by all other types in the set.</span></span> <span data-ttu-id="84656-464">如果所有其他类型都不包含任何一种类型，则该集不包含大多数被包含的类型。</span><span class="sxs-lookup"><span data-stu-id="84656-464">If no single type is encompassed by all other types, then the set has no most encompassed type.</span></span> <span data-ttu-id="84656-465">更直观地说，最包含的类型是集中的 "最小" 类型，这种类型可以隐式转换为其他类型。</span><span class="sxs-lookup"><span data-stu-id="84656-465">In more intuitive terms, the most encompassed type is the "smallest" type in the set—the one type that can be implicitly converted to each of the other types.</span></span>

### <a name="processing-of-user-defined-implicit-conversions"></a><span data-ttu-id="84656-466">处理用户定义的隐式转换</span><span class="sxs-lookup"><span data-stu-id="84656-466">Processing of user-defined implicit conversions</span></span>

<span data-ttu-id="84656-467">用户定义的从类型到类型的隐式转换的 `S` `T` 处理方式如下：</span><span class="sxs-lookup"><span data-stu-id="84656-467">A user-defined implicit conversion from type `S` to type `T` is processed as follows:</span></span>

*  <span data-ttu-id="84656-468">确定类型 `S0` 和 `T0` 。</span><span class="sxs-lookup"><span data-stu-id="84656-468">Determine the types `S0` and `T0`.</span></span> <span data-ttu-id="84656-469">如果 `S` 或 `T` 是可以为 null 的类型， `S0` 并且 `T0` 是其基础类型，则为; 否则 `S0` `T0` 分别为和 `S` `T` 。</span><span class="sxs-lookup"><span data-stu-id="84656-469">If `S` or `T` are nullable types, `S0` and `T0` are their underlying types, otherwise `S0` and `T0` are equal to `S` and `T` respectively.</span></span>
*  <span data-ttu-id="84656-470">查找类型集， `D` 用户定义的转换运算符将从其考虑。</span><span class="sxs-lookup"><span data-stu-id="84656-470">Find the set of types, `D`, from which user-defined conversion operators will be considered.</span></span> <span data-ttu-id="84656-471">`S0`如果是类或结构) ，则此集由 (组成 `S0` ， (的基类（ `S0` 如果 `S0` 是类) ）和 `T0` (（如果 `T0` 是类或结构) ）。</span><span class="sxs-lookup"><span data-stu-id="84656-471">This set consists of `S0` (if `S0` is a class or struct), the base classes of `S0` (if `S0` is a class), and `T0` (if `T0` is a class or struct).</span></span>
*  <span data-ttu-id="84656-472">查找适用的用户定义转换运算符和提升转换运算符集 `U` 。</span><span class="sxs-lookup"><span data-stu-id="84656-472">Find the set of applicable user-defined and lifted conversion operators, `U`.</span></span> <span data-ttu-id="84656-473">此集由中的类或结构所声明的用户定义的和提升的隐式转换运算符组成，这些类或结构从包含的类型包含在中 `D` `S` `T` 。</span><span class="sxs-lookup"><span data-stu-id="84656-473">This set consists of the user-defined and lifted implicit conversion operators declared by the classes or structs in `D` that convert from a type encompassing `S` to a type encompassed by `T`.</span></span> <span data-ttu-id="84656-474">如果 `U` 为空，则不定义转换，并发生编译时错误。</span><span class="sxs-lookup"><span data-stu-id="84656-474">If `U` is empty, the conversion is undefined and a compile-time error occurs.</span></span>
*  <span data-ttu-id="84656-475">查找中运算符的最特定源类型 `SX` `U` ：</span><span class="sxs-lookup"><span data-stu-id="84656-475">Find the most specific source type, `SX`, of the operators in `U`:</span></span>
    * <span data-ttu-id="84656-476">如果中的任何运算符 `U` `S` 都为，则 `SX` 为 `S` 。</span><span class="sxs-lookup"><span data-stu-id="84656-476">If any of the operators in `U` convert from `S`, then `SX` is `S`.</span></span>
    * <span data-ttu-id="84656-477">否则， `SX` 是中运算符的组合源类型集内包含程度最高的类型 `U` 。</span><span class="sxs-lookup"><span data-stu-id="84656-477">Otherwise, `SX` is the most encompassed type in the combined set of source types of the operators in `U`.</span></span> <span data-ttu-id="84656-478">如果找不到完全包含的类型，则转换是不明确的，并发生编译时错误。</span><span class="sxs-lookup"><span data-stu-id="84656-478">If exactly one most encompassed type cannot be found, then the conversion is ambiguous and a compile-time error occurs.</span></span>
*  <span data-ttu-id="84656-479">查找中运算符的最特定目标类型 `TX` `U` ：</span><span class="sxs-lookup"><span data-stu-id="84656-479">Find the most specific target type, `TX`, of the operators in `U`:</span></span>
    * <span data-ttu-id="84656-480">如果中的任何运算符 `U` 转换为 `T` ，则 `TX` 为 `T` 。</span><span class="sxs-lookup"><span data-stu-id="84656-480">If any of the operators in `U` convert to `T`, then `TX` is `T`.</span></span>
    * <span data-ttu-id="84656-481">否则， `TX` 是中运算符的组合目标类型集中的最包含的类型 `U` 。</span><span class="sxs-lookup"><span data-stu-id="84656-481">Otherwise, `TX` is the most encompassing type in the combined set of target types of the operators in `U`.</span></span> <span data-ttu-id="84656-482">如果找不到完全包含的类型，则转换是不明确的，并发生编译时错误。</span><span class="sxs-lookup"><span data-stu-id="84656-482">If exactly one most encompassing type cannot be found, then the conversion is ambiguous and a compile-time error occurs.</span></span>
*  <span data-ttu-id="84656-483">查找最具体的转换运算符：</span><span class="sxs-lookup"><span data-stu-id="84656-483">Find the most specific conversion operator:</span></span>
    * <span data-ttu-id="84656-484">如果 `U` 只包含一个从转换为的用户定义的转换 `SX` 运算符 `TX` ，则这是最具体的转换运算符。</span><span class="sxs-lookup"><span data-stu-id="84656-484">If `U` contains exactly one user-defined conversion operator that converts from `SX` to `TX`, then this is the most specific conversion operator.</span></span>
    * <span data-ttu-id="84656-485">否则，如果 `U` 只包含一个从转换为的提升转换 `SX` 运算符 `TX` ，则这是最具体的转换运算符。</span><span class="sxs-lookup"><span data-stu-id="84656-485">Otherwise, if `U` contains exactly one lifted conversion operator that converts from `SX` to `TX`, then this is the most specific conversion operator.</span></span>
    * <span data-ttu-id="84656-486">否则，转换是不明确的，并发生编译时错误。</span><span class="sxs-lookup"><span data-stu-id="84656-486">Otherwise, the conversion is ambiguous and a compile-time error occurs.</span></span>
*  <span data-ttu-id="84656-487">最后，应用转换：</span><span class="sxs-lookup"><span data-stu-id="84656-487">Finally, apply the conversion:</span></span>
    * <span data-ttu-id="84656-488">如果不 `S` 为 `SX` ，则执行从到的标准隐式转换 `S` `SX` 。</span><span class="sxs-lookup"><span data-stu-id="84656-488">If `S` is not `SX`, then a standard implicit conversion from `S` to `SX` is performed.</span></span>
    * <span data-ttu-id="84656-489">调用最特定的转换运算符，以将转换 `SX` 为 `TX` 。</span><span class="sxs-lookup"><span data-stu-id="84656-489">The most specific conversion operator is invoked to convert from `SX` to `TX`.</span></span>
    * <span data-ttu-id="84656-490">如果不 `TX` 为 `T` ，则执行从到的标准隐式转换 `TX` `T` 。</span><span class="sxs-lookup"><span data-stu-id="84656-490">If `TX` is not `T`, then a standard implicit conversion from `TX` to `T` is performed.</span></span>

### <a name="processing-of-user-defined-explicit-conversions"></a><span data-ttu-id="84656-491">处理用户定义的显式转换</span><span class="sxs-lookup"><span data-stu-id="84656-491">Processing of user-defined explicit conversions</span></span>

<span data-ttu-id="84656-492">用户定义的从类型到类型的显式转换的 `S` `T` 处理方式如下：</span><span class="sxs-lookup"><span data-stu-id="84656-492">A user-defined explicit conversion from type `S` to type `T` is processed as follows:</span></span>

*  <span data-ttu-id="84656-493">确定类型 `S0` 和 `T0` 。</span><span class="sxs-lookup"><span data-stu-id="84656-493">Determine the types `S0` and `T0`.</span></span> <span data-ttu-id="84656-494">如果 `S` 或 `T` 是可以为 null 的类型， `S0` 并且 `T0` 是其基础类型，则为; 否则 `S0` `T0` 分别为和 `S` `T` 。</span><span class="sxs-lookup"><span data-stu-id="84656-494">If `S` or `T` are nullable types, `S0` and `T0` are their underlying types, otherwise `S0` and `T0` are equal to `S` and `T` respectively.</span></span>
*  <span data-ttu-id="84656-495">查找类型集， `D` 用户定义的转换运算符将从其考虑。</span><span class="sxs-lookup"><span data-stu-id="84656-495">Find the set of types, `D`, from which user-defined conversion operators will be considered.</span></span> <span data-ttu-id="84656-496">`S0`如果是类或结构) ，则此集由 (，如果是类 `S0` 或结构、 (的基类（ `S0` 如果是类 `S0`) ）、 `T0` (if `T0` 是类或结构) ，以及 (的基类（ `T0` 如果 `T0` 是类) ）。</span><span class="sxs-lookup"><span data-stu-id="84656-496">This set consists of `S0` (if `S0` is a class or struct), the base classes of `S0` (if `S0` is a class), `T0` (if `T0` is a class or struct), and the base classes of `T0` (if `T0` is a class).</span></span>
*  <span data-ttu-id="84656-497">查找适用的用户定义转换运算符和提升转换运算符集 `U` 。</span><span class="sxs-lookup"><span data-stu-id="84656-497">Find the set of applicable user-defined and lifted conversion operators, `U`.</span></span> <span data-ttu-id="84656-498">此集由中由的类或结构声明的用户定义的隐式或显式转换运算符，由中的 `D` 类型转换 `S` 为包含或包含的类型 `T` 。</span><span class="sxs-lookup"><span data-stu-id="84656-498">This set consists of the user-defined and lifted implicit or explicit conversion operators declared by the classes or structs in `D` that convert from a type encompassing or encompassed by `S` to a type encompassing or encompassed by `T`.</span></span> <span data-ttu-id="84656-499">如果 `U` 为空，则不定义转换，并发生编译时错误。</span><span class="sxs-lookup"><span data-stu-id="84656-499">If `U` is empty, the conversion is undefined and a compile-time error occurs.</span></span>
*  <span data-ttu-id="84656-500">查找中运算符的最特定源类型 `SX` `U` ：</span><span class="sxs-lookup"><span data-stu-id="84656-500">Find the most specific source type, `SX`, of the operators in `U`:</span></span>
    * <span data-ttu-id="84656-501">如果中的任何运算符 `U` `S` 都为，则 `SX` 为 `S` 。</span><span class="sxs-lookup"><span data-stu-id="84656-501">If any of the operators in `U` convert from `S`, then `SX` is `S`.</span></span>
    * <span data-ttu-id="84656-502">否则，如果中的任何运算符 `U` 从包含的类型转换 `S` ，则 `SX` 是这些运算符的组合源类型集中包含程度最高的类型。</span><span class="sxs-lookup"><span data-stu-id="84656-502">Otherwise, if any of the operators in `U` convert from types that encompass `S`, then `SX` is the most encompassed type in the combined set of source types of those operators.</span></span> <span data-ttu-id="84656-503">如果找不到最能包含的类型，则转换是不明确的，并发生编译时错误。</span><span class="sxs-lookup"><span data-stu-id="84656-503">If no most encompassed type can be found, then the conversion is ambiguous and a compile-time error occurs.</span></span>
    * <span data-ttu-id="84656-504">否则， `SX` 是中运算符的组合源类型集中的最包含的类型 `U` 。</span><span class="sxs-lookup"><span data-stu-id="84656-504">Otherwise, `SX` is the most encompassing type in the combined set of source types of the operators in `U`.</span></span> <span data-ttu-id="84656-505">如果找不到完全包含的类型，则转换是不明确的，并发生编译时错误。</span><span class="sxs-lookup"><span data-stu-id="84656-505">If exactly one most encompassing type cannot be found, then the conversion is ambiguous and a compile-time error occurs.</span></span>
*  <span data-ttu-id="84656-506">查找中运算符的最特定目标类型 `TX` `U` ：</span><span class="sxs-lookup"><span data-stu-id="84656-506">Find the most specific target type, `TX`, of the operators in `U`:</span></span>
    * <span data-ttu-id="84656-507">如果中的任何运算符 `U` 转换为 `T` ，则 `TX` 为 `T` 。</span><span class="sxs-lookup"><span data-stu-id="84656-507">If any of the operators in `U` convert to `T`, then `TX` is `T`.</span></span>
    * <span data-ttu-id="84656-508">否则，如果中的任何运算符 `U` 转换为所包含的类型，则 `T` `TX` 是这些运算符的组合目标类型集中最包含的类型。</span><span class="sxs-lookup"><span data-stu-id="84656-508">Otherwise, if any of the operators in `U` convert to types that are encompassed by `T`, then `TX` is the most encompassing type in the combined set of target types of those operators.</span></span> <span data-ttu-id="84656-509">如果找不到完全包含的类型，则转换是不明确的，并发生编译时错误。</span><span class="sxs-lookup"><span data-stu-id="84656-509">If exactly one most encompassing type cannot be found, then the conversion is ambiguous and a compile-time error occurs.</span></span>
    * <span data-ttu-id="84656-510">否则， `TX` 是中运算符的组合目标类型集中的包含程度最高的类型 `U` 。</span><span class="sxs-lookup"><span data-stu-id="84656-510">Otherwise, `TX` is the most encompassed type in the combined set of target types of the operators in `U`.</span></span> <span data-ttu-id="84656-511">如果找不到最能包含的类型，则转换是不明确的，并发生编译时错误。</span><span class="sxs-lookup"><span data-stu-id="84656-511">If no most encompassed type can be found, then the conversion is ambiguous and a compile-time error occurs.</span></span>
*  <span data-ttu-id="84656-512">查找最具体的转换运算符：</span><span class="sxs-lookup"><span data-stu-id="84656-512">Find the most specific conversion operator:</span></span>
    * <span data-ttu-id="84656-513">如果 `U` 只包含一个从转换为的用户定义的转换 `SX` 运算符 `TX` ，则这是最具体的转换运算符。</span><span class="sxs-lookup"><span data-stu-id="84656-513">If `U` contains exactly one user-defined conversion operator that converts from `SX` to `TX`, then this is the most specific conversion operator.</span></span>
    * <span data-ttu-id="84656-514">否则，如果 `U` 只包含一个从转换为的提升转换 `SX` 运算符 `TX` ，则这是最具体的转换运算符。</span><span class="sxs-lookup"><span data-stu-id="84656-514">Otherwise, if `U` contains exactly one lifted conversion operator that converts from `SX` to `TX`, then this is the most specific conversion operator.</span></span>
    * <span data-ttu-id="84656-515">否则，转换是不明确的，并发生编译时错误。</span><span class="sxs-lookup"><span data-stu-id="84656-515">Otherwise, the conversion is ambiguous and a compile-time error occurs.</span></span>
*  <span data-ttu-id="84656-516">最后，应用转换：</span><span class="sxs-lookup"><span data-stu-id="84656-516">Finally, apply the conversion:</span></span>
    * <span data-ttu-id="84656-517">如果不 `S` 为 `SX` ，则执行从到的标准显式转换 `S` `SX` 。</span><span class="sxs-lookup"><span data-stu-id="84656-517">If `S` is not `SX`, then a standard explicit conversion from `S` to `SX` is performed.</span></span>
    * <span data-ttu-id="84656-518">调用最特定的用户定义转换运算符，以将从转换 `SX` 为 `TX` 。</span><span class="sxs-lookup"><span data-stu-id="84656-518">The most specific user-defined conversion operator is invoked to convert from `SX` to `TX`.</span></span>
    * <span data-ttu-id="84656-519">如果不 `TX` 为 `T` ，则执行从到的标准显式转换 `TX` `T` 。</span><span class="sxs-lookup"><span data-stu-id="84656-519">If `TX` is not `T`, then a standard explicit conversion from `TX` to `T` is performed.</span></span>

## <a name="anonymous-function-conversions"></a><span data-ttu-id="84656-520">匿名函数转换</span><span class="sxs-lookup"><span data-stu-id="84656-520">Anonymous function conversions</span></span>

<span data-ttu-id="84656-521">*Anonymous_method_expression* 或 *lambda_expression* 归类为匿名函数 ([匿名函数表达式](expressions.md#anonymous-function-expressions)) 。</span><span class="sxs-lookup"><span data-stu-id="84656-521">An *anonymous_method_expression* or *lambda_expression* is classified as an anonymous function ([Anonymous function expressions](expressions.md#anonymous-function-expressions)).</span></span> <span data-ttu-id="84656-522">表达式没有类型，但可以隐式转换为兼容的委托类型或表达式树类型。</span><span class="sxs-lookup"><span data-stu-id="84656-522">The expression does not have a type but can be implicitly converted to a compatible delegate type or expression tree type.</span></span> <span data-ttu-id="84656-523">具体而言，匿名函数 `F` 与所提供的委托类型兼容 `D` ：</span><span class="sxs-lookup"><span data-stu-id="84656-523">Specifically, an anonymous function `F` is compatible with a delegate type `D` provided:</span></span>

*  <span data-ttu-id="84656-524">如果 `F` 包含一个 *anonymous_function_signature*，则 `D` 和 `F` 具有相同数量的参数。</span><span class="sxs-lookup"><span data-stu-id="84656-524">If `F` contains an *anonymous_function_signature*, then `D` and `F` have the same number of parameters.</span></span>
*  <span data-ttu-id="84656-525">如果不 `F` 包含 *anonymous_function_signature*，则 `D` 只要没有的参数 `D` 具有参数修饰符，就可以具有零个或多个任意类型的参数 `out` 。</span><span class="sxs-lookup"><span data-stu-id="84656-525">If `F` does not contain an *anonymous_function_signature*, then `D` may have zero or more parameters of any type, as long as no parameter of `D` has the `out` parameter modifier.</span></span>
*  <span data-ttu-id="84656-526">如果 `F` 具有显式类型化参数列表，则中的每个参数 `D` 都具有与中相应参数相同的类型和修饰符 `F` 。</span><span class="sxs-lookup"><span data-stu-id="84656-526">If `F` has an explicitly typed parameter list, each parameter in `D` has the same type and modifiers as the corresponding parameter in `F`.</span></span>
*  <span data-ttu-id="84656-527">如果 `F` 具有隐式类型的参数列表，则 `D` 不包含 `ref` 或 `out` 参数。</span><span class="sxs-lookup"><span data-stu-id="84656-527">If `F` has an implicitly typed parameter list, `D` has no `ref` or `out` parameters.</span></span>
*  <span data-ttu-id="84656-528">如果的主体 `F` 是一个表达式，并且 `D` 具有 `void` 返回类型或 `F` 为 async 并且 `D` 具有返回类型 `Task` ，则当的每个参数都为 `F` 指定中的相应参数的类型时，的 `D` 主体 `F` 为 (wrt [表达式](expressions.md)) 的有效表达式，该表达式将允许作为) 的 *statement_expression* ([表达式语句](statements.md#expression-statements) 。</span><span class="sxs-lookup"><span data-stu-id="84656-528">If the body of `F` is an expression, and either `D` has a `void` return type or `F` is async and `D` has the return type `Task`, then when each parameter of `F` is given the type of the corresponding parameter in `D`, the body of `F` is a valid expression (wrt [Expressions](expressions.md)) that would be permitted as a *statement_expression* ([Expression statements](statements.md#expression-statements)).</span></span>
*  <span data-ttu-id="84656-529">如果的主体 `F` 是一个语句块，并且 `D` 具有 `void` 返回类型或 `F` 为 async 并且 `D` 具有返回类型 `Task` ，则当的每个参数都为 `F` 指定中的相应参数的类型时，的 `D` 正文 `F` 为有效语句块 (wrt [块](statements.md#blocks)) ，其中没有 `return` 语句指定表达式。</span><span class="sxs-lookup"><span data-stu-id="84656-529">If the body of `F` is a statement block, and either `D` has a `void` return type or `F` is async and `D` has the return type `Task`, then when each parameter of `F` is given the type of the corresponding parameter in `D`, the body of `F` is a valid statement block (wrt [Blocks](statements.md#blocks)) in which no `return` statement specifies an expression.</span></span>
*  <span data-ttu-id="84656-530">如果的主体 `F` 是一个表达式，并且 `F` 为非 async 并且 `D` 具有非 void 返回类型 `T` ，*或者* `F` 是异步的，并且 `D` 具有返回类型，则 `Task<T>` 当的每个参数 `F` 都为指定中的相应参数的类型时，的 `D` 主体 `F` 将是有效的表达式， (可隐 [](expressions.md)式转换为) 的 wrt 表达式 `T` 。</span><span class="sxs-lookup"><span data-stu-id="84656-530">If the body of `F` is an expression, and *either* `F` is non-async and `D` has a non-void return type `T`, *or* `F` is async and `D` has a return type `Task<T>`, then when each parameter of `F` is given the type of the corresponding parameter in `D`, the body of `F` is a valid expression (wrt [Expressions](expressions.md)) that is implicitly convertible to `T`.</span></span>
*  <span data-ttu-id="84656-531">如果的主体 `F` 是语句块，和 *均* `F` 为非 async 并且 `D` 具有非 void 返回类型 `T` ， *或者* `F` 是异步的，并且 `D` 具有返回类型，则 `Task<T>` 当的每个参数的类型为 `F` 中的相应参数时 `D` ，的主体 `F` 为有效语句块 (wrt 块 [，](statements.md#blocks) 其中每个语句都) `return` 指定一个可隐式转换为的表达式 `T` 。</span><span class="sxs-lookup"><span data-stu-id="84656-531">If the body of `F` is a statement block, and *either* `F` is non-async and `D` has a non-void return type `T`, *or* `F` is async and `D` has a return type `Task<T>`, then when each parameter of `F` is given the type of the corresponding parameter in `D`, the body of `F` is a valid statement block (wrt [Blocks](statements.md#blocks)) with a non-reachable end point in which each `return` statement specifies an expression that is implicitly convertible to `T`.</span></span>

<span data-ttu-id="84656-532">为了简洁起见，本部分对任务类型 `Task` 和 `Task<T>` ([异步函数](classes.md#async-functions)) 使用缩写形式。</span><span class="sxs-lookup"><span data-stu-id="84656-532">For the purpose of brevity, this section uses the short form for the task types `Task` and `Task<T>` ([Async functions](classes.md#async-functions)).</span></span>

<span data-ttu-id="84656-533">`F` `Expression<D>` 如果与 `F` 委托类型兼容，则 lambda 表达式与表达式树类型兼容 `D` 。</span><span class="sxs-lookup"><span data-stu-id="84656-533">A lambda expression `F` is compatible with an expression tree type `Expression<D>` if `F` is compatible with the delegate type `D`.</span></span> <span data-ttu-id="84656-534">请注意，这不适用于匿名方法，只适用于 lambda 表达式。</span><span class="sxs-lookup"><span data-stu-id="84656-534">Note that this does not apply to anonymous methods, only lambda expressions.</span></span>

<span data-ttu-id="84656-535">某些 lambda 表达式不能转换为表达式树类型：即使转换 *存在*，它也会在编译时失败。</span><span class="sxs-lookup"><span data-stu-id="84656-535">Certain lambda expressions cannot be converted to expression tree types: Even though the conversion *exists*, it fails at compile-time.</span></span> <span data-ttu-id="84656-536">如果 lambda 表达式为，则为这种情况：</span><span class="sxs-lookup"><span data-stu-id="84656-536">This is the case if the lambda expression:</span></span>

*  <span data-ttu-id="84656-537">具有 *块* 体</span><span class="sxs-lookup"><span data-stu-id="84656-537">Has a *block* body</span></span>
*  <span data-ttu-id="84656-538">包含简单赋值运算符或复合赋值运算符</span><span class="sxs-lookup"><span data-stu-id="84656-538">Contains simple or compound assignment operators</span></span>
*  <span data-ttu-id="84656-539">包含动态绑定的表达式</span><span class="sxs-lookup"><span data-stu-id="84656-539">Contains a dynamically bound expression</span></span>
*  <span data-ttu-id="84656-540">为异步</span><span class="sxs-lookup"><span data-stu-id="84656-540">Is async</span></span>

<span data-ttu-id="84656-541">下面的示例使用泛型委托类型， `Func<A,R>` 该类型表示一个函数，该函数采用类型的参数 `A` 并返回类型的值 `R` ：</span><span class="sxs-lookup"><span data-stu-id="84656-541">The examples that follow use a generic delegate type `Func<A,R>` which represents a function that takes an argument of type `A` and returns a value of type `R`:</span></span>
```csharp
delegate R Func<A,R>(A arg);
```

<span data-ttu-id="84656-542">在分配中</span><span class="sxs-lookup"><span data-stu-id="84656-542">In the assignments</span></span>
```csharp
Func<int,int> f1 = x => x + 1;                 // Ok

Func<int,double> f2 = x => x + 1;              // Ok

Func<double,int> f3 = x => x + 1;              // Error

Func<int, Task<int>> f4 = async x => x + 1;    // Ok
```
<span data-ttu-id="84656-543">每个匿名函数的参数和返回类型都是从匿名函数所分配到的变量类型确定的。</span><span class="sxs-lookup"><span data-stu-id="84656-543">the parameter and return types of each anonymous function are determined from the type of the variable to which the anonymous function is assigned.</span></span>

<span data-ttu-id="84656-544">第一次分配成功将匿名函数转换为委托类型 `Func<int,int>` ，因为当 `x` 给定类型时 `int` ， `x+1` 是可隐式转换为类型的有效表达式 `int` 。</span><span class="sxs-lookup"><span data-stu-id="84656-544">The first assignment successfully converts the anonymous function to the delegate type `Func<int,int>` because, when `x` is given type `int`, `x+1` is a valid expression that is implicitly convertible to type `int`.</span></span>

<span data-ttu-id="84656-545">同样，第二个赋值成功将匿名函数转换为委托类型， `Func<int,double>` 因为) 类型的 (的结果可 `x+1` `int` 隐式转换为 `double` 类型。</span><span class="sxs-lookup"><span data-stu-id="84656-545">Likewise, the second assignment successfully converts the anonymous function to the delegate type `Func<int,double>` because the result of `x+1` (of type `int`) is implicitly convertible to type `double`.</span></span>

<span data-ttu-id="84656-546">但是，第三个赋值是编译时错误，因为当 `x` 给定类型时， `double` `x+1` 类型)  (的结果 `double` 无法隐式转换为类型 `int` 。</span><span class="sxs-lookup"><span data-stu-id="84656-546">However, the third assignment is a compile-time error because, when `x` is given type `double`, the result of `x+1` (of type `double`) is not implicitly convertible to type `int`.</span></span>

<span data-ttu-id="84656-547">第四个赋值成功将匿名 async 函数转换为委托类型， `Func<int, Task<int>>` 因为) 类型的 (的结果可 `x+1` `int` 隐式转换为该 `int` 任务类型的结果类型 `Task<int>` 。</span><span class="sxs-lookup"><span data-stu-id="84656-547">The fourth assignment successfully converts the anonymous async function to the delegate type `Func<int, Task<int>>` because the result of `x+1` (of type `int`) is implicitly convertible to the result type `int` of the task type `Task<int>`.</span></span>

<span data-ttu-id="84656-548">匿名函数可能会影响重载决策，并参与类型推理。</span><span class="sxs-lookup"><span data-stu-id="84656-548">Anonymous functions may influence overload resolution, and participate in type inference.</span></span> <span data-ttu-id="84656-549">有关更多详细信息，请参阅 [函数成员](expressions.md#function-members) 。</span><span class="sxs-lookup"><span data-stu-id="84656-549">See [Function members](expressions.md#function-members) for further details.</span></span>

### <a name="evaluation-of-anonymous-function-conversions-to-delegate-types"></a><span data-ttu-id="84656-550">匿名函数转换到委托类型的计算</span><span class="sxs-lookup"><span data-stu-id="84656-550">Evaluation of anonymous function conversions to delegate types</span></span>

<span data-ttu-id="84656-551">将匿名函数转换为委托类型会生成一个委托实例，该实例引用匿名函数，并且 (可能为空，这是在计算时处于活动状态的已捕获外部变量的) 集。</span><span class="sxs-lookup"><span data-stu-id="84656-551">Conversion of an anonymous function to a delegate type produces a delegate instance which references the anonymous function and the (possibly empty) set of captured outer variables that are active at the time of the evaluation.</span></span> <span data-ttu-id="84656-552">调用委托时，将执行匿名函数的主体。</span><span class="sxs-lookup"><span data-stu-id="84656-552">When the delegate is invoked, the body of the anonymous function is executed.</span></span> <span data-ttu-id="84656-553">使用委托引用的捕获外部变量集来执行正文中的代码。</span><span class="sxs-lookup"><span data-stu-id="84656-553">The code in the body is executed using the set of captured outer variables referenced by the delegate.</span></span>

<span data-ttu-id="84656-554">从匿名函数生成的委托的调用列表包含单个项。</span><span class="sxs-lookup"><span data-stu-id="84656-554">The invocation list of a delegate produced from an anonymous function contains a single entry.</span></span> <span data-ttu-id="84656-555">委托的确切目标对象和目标方法未指定。</span><span class="sxs-lookup"><span data-stu-id="84656-555">The exact target object and target method of the delegate are unspecified.</span></span> <span data-ttu-id="84656-556">具体而言，它是未指定的，即委托的目标对象是 `null` 、 `this` 封闭函数成员的值还是其他某个对象。</span><span class="sxs-lookup"><span data-stu-id="84656-556">In particular, it is unspecified whether the target object of the delegate is `null`, the `this` value of the enclosing function member, or some other object.</span></span>

<span data-ttu-id="84656-557">允许 (但不要求) 返回同一个委托实例，但不允许将语义相同的匿名函数（具有相同的)  (）转换为同一委托类型。</span><span class="sxs-lookup"><span data-stu-id="84656-557">Conversions of semantically identical anonymous functions with the same (possibly empty) set of captured outer variable instances to the same delegate types are permitted (but not required) to return the same delegate instance.</span></span> <span data-ttu-id="84656-558">此处使用的术语在语义上是相同的，这意味着在所有情况下，匿名函数的执行将在给定相同参数的情况下生成相同的效果。</span><span class="sxs-lookup"><span data-stu-id="84656-558">The term semantically identical is used here to mean that execution of the anonymous functions will, in all cases, produce the same effects given the same arguments.</span></span> <span data-ttu-id="84656-559">此规则允许对如下代码进行优化。</span><span class="sxs-lookup"><span data-stu-id="84656-559">This rule permits code such as the following to be optimized.</span></span>

```csharp
delegate double Function(double x);

class Test
{
    static double[] Apply(double[] a, Function f) {
        double[] result = new double[a.Length];
        for (int i = 0; i < a.Length; i++) result[i] = f(a[i]);
        return result;
    }

    static void F(double[] a, double[] b) {
        a = Apply(a, (double x) => Math.Sin(x));
        b = Apply(b, (double y) => Math.Sin(y));
        ...
    }
}
```

<span data-ttu-id="84656-560">由于两个匿名函数委托具有相同的 (空) 一组捕获的外部变量，并且由于匿名函数在语义上是相同的，因此允许编译器使委托引用相同的目标方法。</span><span class="sxs-lookup"><span data-stu-id="84656-560">Since the two anonymous function delegates have the same (empty) set of captured outer variables, and since the anonymous functions are semantically identical, the compiler is permitted to have the delegates refer to the same target method.</span></span> <span data-ttu-id="84656-561">的确，允许编译器从两个匿名函数表达式返回完全相同的委托实例。</span><span class="sxs-lookup"><span data-stu-id="84656-561">Indeed, the compiler is permitted to return the very same delegate instance from both anonymous function expressions.</span></span>

### <a name="evaluation-of-anonymous-function-conversions-to-expression-tree-types"></a><span data-ttu-id="84656-562">对表达式树类型的匿名函数转换的计算</span><span class="sxs-lookup"><span data-stu-id="84656-562">Evaluation of anonymous function conversions to expression tree types</span></span>

<span data-ttu-id="84656-563">将匿名函数转换为表达式树类型会生成表达式树， ([表达式树类型](types.md#expression-tree-types)) 。</span><span class="sxs-lookup"><span data-stu-id="84656-563">Conversion of an anonymous function to an expression tree type produces an expression tree ([Expression tree types](types.md#expression-tree-types)).</span></span> <span data-ttu-id="84656-564">更准确地说，对匿名函数转换的评估导致了表示匿名函数本身的结构的对象结构。</span><span class="sxs-lookup"><span data-stu-id="84656-564">More precisely, evaluation of the anonymous function conversion leads to the construction of an object structure that represents the structure of the anonymous function itself.</span></span> <span data-ttu-id="84656-565">表达式树的准确结构以及用于创建它的确切过程是定义的实现。</span><span class="sxs-lookup"><span data-stu-id="84656-565">The precise structure of the expression tree, as well as the exact process for creating it, are implementation defined.</span></span>

### <a name="implementation-example"></a><span data-ttu-id="84656-566">实现示例</span><span class="sxs-lookup"><span data-stu-id="84656-566">Implementation example</span></span>

<span data-ttu-id="84656-567">本部分介绍了使用其他 c # 构造实现的匿名函数转换的可能实现。</span><span class="sxs-lookup"><span data-stu-id="84656-567">This section describes a possible implementation of anonymous function conversions in terms of other C# constructs.</span></span> <span data-ttu-id="84656-568">此处所述的实现基于 Microsoft c # 编译器所使用的相同原则，但它并不是强制性实现，也不是唯一可行的方法。</span><span class="sxs-lookup"><span data-stu-id="84656-568">The implementation described here is based on the same principles used by the Microsoft C# compiler, but it is by no means a mandated implementation, nor is it the only one possible.</span></span> <span data-ttu-id="84656-569">它只是简单地提到了转换为表达式树，因为其确切语义超出了本规范的范围。</span><span class="sxs-lookup"><span data-stu-id="84656-569">It only briefly mentions conversions to expression trees, as their exact semantics are outside the scope of this specification.</span></span>

<span data-ttu-id="84656-570">本部分的其余部分提供了一些代码示例，其中包含具有不同特征的匿名函数。</span><span class="sxs-lookup"><span data-stu-id="84656-570">The remainder of this section gives several examples of code that contains anonymous functions with different characteristics.</span></span> <span data-ttu-id="84656-571">对于每个示例，提供了对仅使用其他 c # 构造的代码的相应转换。</span><span class="sxs-lookup"><span data-stu-id="84656-571">For each example, a corresponding translation to code that uses only other C# constructs is provided.</span></span> <span data-ttu-id="84656-572">在这些示例中， `D` 假定使用标识符表示以下委托类型：</span><span class="sxs-lookup"><span data-stu-id="84656-572">In the examples, the identifier `D` is assumed by represent the following delegate type:</span></span>
```csharp
public delegate void D();
```

<span data-ttu-id="84656-573">匿名函数的最简单形式是捕获无外部变量：</span><span class="sxs-lookup"><span data-stu-id="84656-573">The simplest form of an anonymous function is one that captures no outer variables:</span></span>
```csharp
class Test
{
    static void F() {
        D d = () => { Console.WriteLine("test"); };
    }
}
```

<span data-ttu-id="84656-574">这可以转换为引用编译器生成的静态方法（在其中放置匿名函数的代码）的委托实例化：</span><span class="sxs-lookup"><span data-stu-id="84656-574">This can be translated to a delegate instantiation that references a compiler generated static method in which the code of the anonymous function is placed:</span></span>
```csharp
class Test
{
    static void F() {
        D d = new D(__Method1);
    }

    static void __Method1() {
        Console.WriteLine("test");
    }
}
```

<span data-ttu-id="84656-575">在下面的示例中，匿名函数引用的实例成员 `this` ：</span><span class="sxs-lookup"><span data-stu-id="84656-575">In the following example, the anonymous function references instance members of `this`:</span></span>
```csharp
class Test
{
    int x;

    void F() {
        D d = () => { Console.WriteLine(x); };
    }
}
```

<span data-ttu-id="84656-576">这可以转换为包含匿名函数代码的编译器生成的实例方法：</span><span class="sxs-lookup"><span data-stu-id="84656-576">This can be translated to a compiler generated instance method containing the code of the anonymous function:</span></span>
```csharp
class Test
{
    int x;

    void F() {
        D d = new D(__Method1);
    }

    void __Method1() {
        Console.WriteLine(x);
    }
}
```

<span data-ttu-id="84656-577">在此示例中，匿名函数捕获本地变量：</span><span class="sxs-lookup"><span data-stu-id="84656-577">In this example, the anonymous function captures a local variable:</span></span>
```csharp
class Test
{
    void F() {
        int y = 123;
        D d = () => { Console.WriteLine(y); };
    }
}
```

<span data-ttu-id="84656-578">局部变量的生存期现在必须至少扩展到匿名函数委托的生存期。</span><span class="sxs-lookup"><span data-stu-id="84656-578">The lifetime of the local variable must now be extended to at least the lifetime of the anonymous function delegate.</span></span> <span data-ttu-id="84656-579">这可以通过将本地变量 "提升" 到编译器生成的类的字段来实现。</span><span class="sxs-lookup"><span data-stu-id="84656-579">This can be achieved by "hoisting" the local variable into a field of a compiler generated class.</span></span> <span data-ttu-id="84656-580">实例化局部变量 (实例 [化](expressions.md#instantiation-of-local-variables) 局部变量) 然后与创建编译器生成的类的实例，并且访问本地变量对应于访问编译器生成的类的实例中的字段。</span><span class="sxs-lookup"><span data-stu-id="84656-580">Instantiation of the local variable ([Instantiation of local variables](expressions.md#instantiation-of-local-variables)) then corresponds to creating an instance of the compiler generated class, and accessing the local variable corresponds to accessing a field in the instance of the compiler generated class.</span></span> <span data-ttu-id="84656-581">此外，匿名函数会成为编译器生成的类的实例方法：</span><span class="sxs-lookup"><span data-stu-id="84656-581">Furthermore, the anonymous function becomes an instance method of the compiler generated class:</span></span>
```csharp
class Test
{
    void F() {
        __Locals1 __locals1 = new __Locals1();
        __locals1.y = 123;
        D d = new D(__locals1.__Method1);
    }

    class __Locals1
    {
        public int y;

        public void __Method1() {
            Console.WriteLine(y);
        }
    }
}
```

<span data-ttu-id="84656-582">最后，下面的匿名函数捕获和 `this` 两个具有不同生存期的局部变量：</span><span class="sxs-lookup"><span data-stu-id="84656-582">Finally, the following anonymous function captures `this` as well as two local variables with different lifetimes:</span></span>
```csharp
class Test
{
    int x;

    void F() {
        int y = 123;
        for (int i = 0; i < 10; i++) {
            int z = i * 2;
            D d = () => { Console.WriteLine(x + y + z); };
        }
    }
}
```

<span data-ttu-id="84656-583">在这里，将为每个在其中捕获局部变量的语句块创建一个编译器生成的类，以便不同块中的局部变量可以具有独立生存期。</span><span class="sxs-lookup"><span data-stu-id="84656-583">Here, a compiler generated class is created for each statement block in which locals are captured such that the locals in the different blocks can have independent lifetimes.</span></span> <span data-ttu-id="84656-584">的实例 `__Locals2` （内部语句块的编译器生成的类）包含局部变量 `z` 和引用实例的字段 `__Locals1` 。</span><span class="sxs-lookup"><span data-stu-id="84656-584">An instance of `__Locals2`, the compiler generated class for the inner statement block, contains the local variable `z` and a field that references an instance of `__Locals1`.</span></span>  <span data-ttu-id="84656-585">的一个实例 `__Locals1` ，它是外部语句块的编译器生成的类，其中包含局部变量 `y` 和引用 `this` 封闭函数成员的字段。</span><span class="sxs-lookup"><span data-stu-id="84656-585">An instance of `__Locals1`, the compiler generated class for the outer statement block, contains the local variable `y` and a field that references `this` of the enclosing function member.</span></span> <span data-ttu-id="84656-586">利用这些数据结构，可以通过的实例访问所有捕获的外部变量 `__Local2` ，匿名函数的代码就可以作为该类的实例方法实现。</span><span class="sxs-lookup"><span data-stu-id="84656-586">With these data structures it is possible to reach all captured outer variables through an instance of `__Local2`, and the code of the anonymous function can thus be implemented as an instance method of that class.</span></span>

```csharp
class Test
{
    void F() {
        __Locals1 __locals1 = new __Locals1();
        __locals1.__this = this;
        __locals1.y = 123;
        for (int i = 0; i < 10; i++) {
            __Locals2 __locals2 = new __Locals2();
            __locals2.__locals1 = __locals1;
            __locals2.z = i * 2;
            D d = new D(__locals2.__Method1);
        }
    }

    class __Locals1
    {
        public Test __this;
        public int y;
    }

    class __Locals2
    {
        public __Locals1 __locals1;
        public int z;

        public void __Method1() {
            Console.WriteLine(__locals1.__this.x + __locals1.y + z);
        }
    }
}
```

<span data-ttu-id="84656-587">在此处用于捕获局部变量的相同技术也可以在将匿名函数转换为表达式树时使用：对编译器生成的对象的引用可以存储在表达式树中，对局部变量的访问可表示为这些对象上的字段访问。</span><span class="sxs-lookup"><span data-stu-id="84656-587">The same technique applied here to capture local variables can also be used when converting anonymous functions to expression trees: References to the compiler generated objects can be stored in the expression tree, and access to the local variables can be represented as field accesses on these objects.</span></span> <span data-ttu-id="84656-588">此方法的优点是允许在委托和表达式树之间共享 "提升" 的局部变量。</span><span class="sxs-lookup"><span data-stu-id="84656-588">The advantage of this approach is that it allows the "lifted" local variables to be shared between delegates and expression trees.</span></span>

## <a name="method-group-conversions"></a><span data-ttu-id="84656-589">方法组转换</span><span class="sxs-lookup"><span data-stu-id="84656-589">Method group conversions</span></span>

<span data-ttu-id="84656-590">隐式转换 () 存在从方法组 ([表达式分类](expressions.md#expression-classifications)) 到兼容的委托类型的隐[式](conversions.md#implicit-conversions)转换。</span><span class="sxs-lookup"><span data-stu-id="84656-590">An implicit conversion ([Implicit conversions](conversions.md#implicit-conversions)) exists from a method group ([Expression classifications](expressions.md#expression-classifications)) to a compatible delegate type.</span></span> <span data-ttu-id="84656-591">给定委托类型 `D` 和 `E` 归类为方法组的表达式时，存在从到的隐式转换 `E` `D` `E` 。如果包含至少一个适用于其正常窗体的方法 (适用的 [函数成员](expressions.md#applicable-function-member)) 到通过使用的参数类型和修饰符构造的参数列表 `D` ，如下所述。</span><span class="sxs-lookup"><span data-stu-id="84656-591">Given a delegate type `D` and an expression `E` that is classified as a method group, an implicit conversion exists from `E` to `D` if `E` contains at least one method that is applicable in its normal form ([Applicable function member](expressions.md#applicable-function-member)) to an argument list constructed by use of the parameter types and modifiers of `D`, as described in the following.</span></span>

<span data-ttu-id="84656-592">以下中描述了从方法组转换为委托类型的编译时应用程序 `E` `D` 。</span><span class="sxs-lookup"><span data-stu-id="84656-592">The compile-time application of a conversion from a method group `E` to a delegate type `D` is described in the following.</span></span> <span data-ttu-id="84656-593">请注意，是否存在从到的隐式转换， `E` `D` 并不保证转换的编译时应用程序不会出错。</span><span class="sxs-lookup"><span data-stu-id="84656-593">Note that the existence of an implicit conversion from `E` to `D` does not guarantee that the compile-time application of the conversion will succeed without error.</span></span>

*  <span data-ttu-id="84656-594">`M`选择与方法调用对应的单个方法 ([方法](expressions.md#method-invocations)调用) 的方法调用 `E(A)` ，并进行以下修改：</span><span class="sxs-lookup"><span data-stu-id="84656-594">A single method `M` is selected corresponding to a method invocation ([Method invocations](expressions.md#method-invocations)) of the form `E(A)`, with the following modifications:</span></span>
    * <span data-ttu-id="84656-595">参数列表 `A` 是表达式的列表，每个表达式都归类为变量，并且具有类型和修饰符 (`ref` 或的 `out` *formal_parameter_list* 中的相应参数) `D` 。</span><span class="sxs-lookup"><span data-stu-id="84656-595">The argument list `A` is a list of expressions, each classified as a variable and with the type and modifier (`ref` or `out`) of the corresponding parameter in the *formal_parameter_list* of `D`.</span></span>
    * <span data-ttu-id="84656-596">考虑的候选方法只是那些适用于其正常形式 ([适用的函数成员](expressions.md#applicable-function-member)) ，而不是仅在其展开形式中适用的方法。</span><span class="sxs-lookup"><span data-stu-id="84656-596">The candidate methods considered are only those methods that are applicable in their normal form ([Applicable function member](expressions.md#applicable-function-member)), not those applicable only in their expanded form.</span></span>
*  <span data-ttu-id="84656-597">如果 [方法调用](expressions.md#method-invocations) 的算法产生错误，则会发生编译时错误。</span><span class="sxs-lookup"><span data-stu-id="84656-597">If the algorithm of [Method invocations](expressions.md#method-invocations) produces an error, then a compile-time error occurs.</span></span> <span data-ttu-id="84656-598">否则，该算法将生成单个最佳方法， `M` 该方法的参数数目与相同 `D` ，转换被视为存在。</span><span class="sxs-lookup"><span data-stu-id="84656-598">Otherwise the algorithm produces a single best method `M` having the same number of parameters as `D` and the conversion is considered to exist.</span></span>
*  <span data-ttu-id="84656-599">所选方法 `M` 必须兼容 (委托类型) [委托兼容性](delegates.md#delegate-compatibility) `D` ，否则将发生编译时错误。</span><span class="sxs-lookup"><span data-stu-id="84656-599">The selected method `M` must be compatible ([Delegate compatibility](delegates.md#delegate-compatibility)) with the delegate type `D`, or otherwise, a compile-time error occurs.</span></span>
*  <span data-ttu-id="84656-600">如果所选方法 `M` 是实例方法，则与关联的实例表达式 `E` 确定委托的目标对象。</span><span class="sxs-lookup"><span data-stu-id="84656-600">If the selected method `M` is an instance method, the instance expression associated with `E` determines the target object of the delegate.</span></span>
*  <span data-ttu-id="84656-601">如果所选方法 M 是通过实例表达式上的成员访问方式表示的扩展方法，则该实例表达式将确定委托的目标对象。</span><span class="sxs-lookup"><span data-stu-id="84656-601">If the selected method M is an extension method which is denoted by means of a member access on an instance expression, that instance expression determines the target object of the delegate.</span></span>
*  <span data-ttu-id="84656-602">转换结果为类型的值  `D` ，即引用所选方法和目标对象的新创建的委托。</span><span class="sxs-lookup"><span data-stu-id="84656-602">The result of the conversion is a value of type `D`, namely a newly created delegate that refers to the selected method and target object.</span></span>
*  <span data-ttu-id="84656-603">请注意，如果 [方法调用](expressions.md#method-invocations) 的算法找不到实例方法，但在将调用 `E(A)` 作为扩展方法调用处理 ([扩展](expressions.md#extension-method-invocations) 方法调用) ，则此过程可能会导致创建扩展方法的委托。</span><span class="sxs-lookup"><span data-stu-id="84656-603">Note that this process can lead to the creation of a delegate to an extension method, if the algorithm of [Method invocations](expressions.md#method-invocations) fails to find an instance method but succeeds in processing the invocation of `E(A)` as an extension method invocation ([Extension method invocations](expressions.md#extension-method-invocations)).</span></span> <span data-ttu-id="84656-604">因此，创建的委托将捕获扩展方法以及其第一个参数。</span><span class="sxs-lookup"><span data-stu-id="84656-604">A delegate thus created captures the extension method as well as its first argument.</span></span>

<span data-ttu-id="84656-605">下面的示例演示方法组转换：</span><span class="sxs-lookup"><span data-stu-id="84656-605">The following example demonstrates method group conversions:</span></span>
```csharp
delegate string D1(object o);

delegate object D2(string s);

delegate object D3();

delegate string D4(object o, params object[] a);

delegate string D5(int i);

class Test
{
    static string F(object o) {...}

    static void G() {
        D1 d1 = F;            // Ok
        D2 d2 = F;            // Ok
        D3 d3 = F;            // Error -- not applicable
        D4 d4 = F;            // Error -- not applicable in normal form
        D5 d5 = F;            // Error -- applicable but not compatible

    }
}
```

<span data-ttu-id="84656-606">赋值 `d1` 隐式将方法组转换 `F` 为类型的值 `D1` 。</span><span class="sxs-lookup"><span data-stu-id="84656-606">The assignment to `d1` implicitly converts the method group `F` to a value of type `D1`.</span></span>

<span data-ttu-id="84656-607">的赋值 `d2` 演示了如何创建一个委托，该委托指向 (逆变) 参数类型和派生程度更高的 (协变) 返回类型的派生方法。</span><span class="sxs-lookup"><span data-stu-id="84656-607">The assignment to `d2` shows how it is possible to create a delegate to a method that has less derived (contravariant) parameter types and a more derived (covariant) return type.</span></span>

<span data-ttu-id="84656-608">`d3`如果该方法不适用，则分配为显示不存在转换的情况。</span><span class="sxs-lookup"><span data-stu-id="84656-608">The assignment to `d3` shows how no conversion exists if the method is not applicable.</span></span>

<span data-ttu-id="84656-609">用于 `d4` 显示此方法必须以其正常形式适用的方式的赋值。</span><span class="sxs-lookup"><span data-stu-id="84656-609">The assignment to `d4` shows how the method must be applicable in its normal form.</span></span>

<span data-ttu-id="84656-610">用于 `d5` 显示如何允许委托和方法的参数和返回类型不同于引用类型的赋值。</span><span class="sxs-lookup"><span data-stu-id="84656-610">The assignment to `d5` shows how parameter and return types of the delegate and method are allowed to differ only for reference types.</span></span>

<span data-ttu-id="84656-611">与所有其他隐式和显式转换一样，转换运算符可用于显式执行方法组转换。</span><span class="sxs-lookup"><span data-stu-id="84656-611">As with all other implicit and explicit conversions, the cast operator can be used to explicitly perform a method group conversion.</span></span> <span data-ttu-id="84656-612">因此，示例</span><span class="sxs-lookup"><span data-stu-id="84656-612">Thus, the example</span></span>
```csharp
object obj = new EventHandler(myDialog.OkClick);
```
<span data-ttu-id="84656-613">可以改为写入</span><span class="sxs-lookup"><span data-stu-id="84656-613">could instead be written</span></span>
```csharp
object obj = (EventHandler)myDialog.OkClick;
```

<span data-ttu-id="84656-614">方法组可能会影响重载决策，并参与类型推理。</span><span class="sxs-lookup"><span data-stu-id="84656-614">Method groups may influence overload resolution, and participate in type inference.</span></span> <span data-ttu-id="84656-615">有关更多详细信息，请参阅 [函数成员](expressions.md#function-members) 。</span><span class="sxs-lookup"><span data-stu-id="84656-615">See [Function members](expressions.md#function-members) for further details.</span></span>

<span data-ttu-id="84656-616">方法组转换的运行时计算如下所示：</span><span class="sxs-lookup"><span data-stu-id="84656-616">The run-time evaluation of a method group conversion proceeds as follows:</span></span>

*  <span data-ttu-id="84656-617">如果在编译时选择的方法是实例方法，或者它是作为实例方法访问的扩展方法，则将从与关联的实例表达式确定委托的目标对象 `E` ：</span><span class="sxs-lookup"><span data-stu-id="84656-617">If the method selected at compile-time is an instance method, or it is an extension method which is accessed as an instance method, the target object of the delegate is determined from the instance expression associated with `E`:</span></span>
    * <span data-ttu-id="84656-618">计算实例表达式。</span><span class="sxs-lookup"><span data-stu-id="84656-618">The instance expression is evaluated.</span></span> <span data-ttu-id="84656-619">如果此计算导致异常，则不执行其他步骤。</span><span class="sxs-lookup"><span data-stu-id="84656-619">If this evaluation causes an exception, no further steps are executed.</span></span>
    * <span data-ttu-id="84656-620">如果实例表达式为 *reference_type*，则由实例表达式计算的值将成为目标对象。</span><span class="sxs-lookup"><span data-stu-id="84656-620">If the instance expression is of a *reference_type*, the value computed by the instance expression becomes the target object.</span></span> <span data-ttu-id="84656-621">如果所选的方法是实例方法，并且目标对象为 `null` ， `System.NullReferenceException` 则会引发，而不执行进一步的步骤。</span><span class="sxs-lookup"><span data-stu-id="84656-621">If the selected method is an instance method and the target object is `null`, a `System.NullReferenceException` is thrown and no further steps are executed.</span></span>
    * <span data-ttu-id="84656-622">如果实例表达式为 *value_type*，则会执行装箱运算 ([装箱转换](types.md#boxing-conversions) ，) 将值转换为对象，此对象将成为目标对象。</span><span class="sxs-lookup"><span data-stu-id="84656-622">If the instance expression is of a *value_type*, a boxing operation ([Boxing conversions](types.md#boxing-conversions)) is performed to convert the value to an object, and this object becomes the target object.</span></span>
*  <span data-ttu-id="84656-623">否则，所选方法为静态方法调用的一部分，并且委托的目标对象是 `null` 。</span><span class="sxs-lookup"><span data-stu-id="84656-623">Otherwise the selected method is part of a static method call, and the target object of the delegate is `null`.</span></span>
*  <span data-ttu-id="84656-624">分配委托类型的新实例 `D` 。</span><span class="sxs-lookup"><span data-stu-id="84656-624">A new instance of the delegate type `D` is allocated.</span></span> <span data-ttu-id="84656-625">如果没有足够的内存可用于分配新的实例， `System.OutOfMemoryException` 则会引发，而不执行进一步的步骤。</span><span class="sxs-lookup"><span data-stu-id="84656-625">If there is not enough memory available to allocate the new instance, a `System.OutOfMemoryException` is thrown and no further steps are executed.</span></span>
*  <span data-ttu-id="84656-626">使用对在编译时确定的方法以及对上面计算的目标对象的引用来初始化新的委托实例。</span><span class="sxs-lookup"><span data-stu-id="84656-626">The new delegate instance is initialized with a reference to the method that was determined at compile-time and a reference to the target object computed above.</span></span>

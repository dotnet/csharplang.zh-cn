---
ms.openlocfilehash: c5f9750d07c6324b6db04ab83c95622f7b9a1c13
ms.sourcegitcommit: a9b70c6ee1117df36eb66cf5b8e45c47e6c4f12e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/17/2021
ms.locfileid: "98536225"
---

# <a name="records"></a><span data-ttu-id="5fa6d-101">记录</span><span class="sxs-lookup"><span data-stu-id="5fa6d-101">Records</span></span>

<span data-ttu-id="5fa6d-102">本建议将跟踪 c # 9 记录功能的规范（由 c # 语言设计团队同意）。</span><span class="sxs-lookup"><span data-stu-id="5fa6d-102">This proposal tracks the specification for the C# 9 records feature, as agreed to by the C# language design team.</span></span>

<span data-ttu-id="5fa6d-103">记录的语法如下所示：</span><span class="sxs-lookup"><span data-stu-id="5fa6d-103">The syntax for a record is as follows:</span></span>

```antlr
record_declaration
    : attributes? class_modifier* 'partial'? 'record' identifier type_parameter_list?
      parameter_list? record_base? type_parameter_constraints_clause* record_body
    ;

record_base
    : ':' class_type argument_list?
    | ':' interface_type_list
    | ':' class_type argument_list? ',' interface_type_list
    ;

record_body
    : '{' class_member_declaration* '}' ';'?
    | ';'
    ;
```

<span data-ttu-id="5fa6d-104">记录类型是引用类型，类似于类声明。</span><span class="sxs-lookup"><span data-stu-id="5fa6d-104">Record types are reference types, similar to a class declaration.</span></span> <span data-ttu-id="5fa6d-105">如果不包含，记录将提供一个错误 `record_base` `argument_list` `record_declaration` `parameter_list` 。</span><span class="sxs-lookup"><span data-stu-id="5fa6d-105">It is an error for a record to provide a `record_base` `argument_list` if the `record_declaration` does not contain a `parameter_list`.</span></span>
<span data-ttu-id="5fa6d-106">部分记录最多只能有一个分部类型声明提供 `parameter_list` 。</span><span class="sxs-lookup"><span data-stu-id="5fa6d-106">At most one partial type declaration of a partial record may provide a `parameter_list`.</span></span>

<span data-ttu-id="5fa6d-107">记录参数不能 `ref` 使用 `out` 或 `this` 修饰符 (但 `in` `params` 允许) 。</span><span class="sxs-lookup"><span data-stu-id="5fa6d-107">Record parameters cannot use `ref`, `out` or `this` modifiers (but `in` and `params` are allowed).</span></span>

## <a name="inheritance"></a><span data-ttu-id="5fa6d-108">继承</span><span class="sxs-lookup"><span data-stu-id="5fa6d-108">Inheritance</span></span>

<span data-ttu-id="5fa6d-109">除非类为 `object` ，且类不能从记录继承，否则记录不能从类继承。</span><span class="sxs-lookup"><span data-stu-id="5fa6d-109">Records cannot inherit from classes, unless the class is `object`, and classes cannot inherit from records.</span></span> <span data-ttu-id="5fa6d-110">记录可以继承自其他记录。</span><span class="sxs-lookup"><span data-stu-id="5fa6d-110">Records can inherit from other records.</span></span>

## <a name="members-of-a-record-type"></a><span data-ttu-id="5fa6d-111">记录类型的成员</span><span class="sxs-lookup"><span data-stu-id="5fa6d-111">Members of a record type</span></span>

<span data-ttu-id="5fa6d-112">除了记录体中声明的成员，记录类型还具有附加的合成成员。</span><span class="sxs-lookup"><span data-stu-id="5fa6d-112">In addition to the members declared in the record body, a record type has additional synthesized members.</span></span>
<span data-ttu-id="5fa6d-113">如果在记录正文中声明了具有 "匹配" 签名的成员，或者继承了具有 "匹配" 签名的可访问的具体非虚拟成员，则将合成成员。</span><span class="sxs-lookup"><span data-stu-id="5fa6d-113">Members are synthesized unless a member with a "matching" signature is declared in the record body or an accessible concrete non-virtual member with a "matching" signature is inherited.</span></span>
<span data-ttu-id="5fa6d-114">如果两个成员具有相同的签名，或在继承方案中被视为 "隐藏"，则会将其视为匹配项。</span><span class="sxs-lookup"><span data-stu-id="5fa6d-114">Two members are considered matching if they have the same signature or would be considered "hiding" in an inheritance scenario.</span></span>
<span data-ttu-id="5fa6d-115">将记录的成员命名为 "Clone" 是错误的。</span><span class="sxs-lookup"><span data-stu-id="5fa6d-115">It is an error for a member of a record to be named "Clone".</span></span>
<span data-ttu-id="5fa6d-116">如果记录的实例字段具有不安全类型，则是错误的。</span><span class="sxs-lookup"><span data-stu-id="5fa6d-116">It is an error for an instance field of a record to have an unsafe type.</span></span>

<span data-ttu-id="5fa6d-117">合成成员如下所示：</span><span class="sxs-lookup"><span data-stu-id="5fa6d-117">The synthesized members are as follows:</span></span>

### <a name="equality-members"></a><span data-ttu-id="5fa6d-118">相等成员</span><span class="sxs-lookup"><span data-stu-id="5fa6d-118">Equality members</span></span>

<span data-ttu-id="5fa6d-119">如果记录是从派生的 `object` ，则记录类型包括合成的 readonly 属性，该属性等效于声明的属性，如下所示：</span><span class="sxs-lookup"><span data-stu-id="5fa6d-119">If the record is derived from `object`, the record type includes a synthesized readonly property equivalent to a property declared as follows:</span></span>
```C#
Type EqualityContract { get; };
```
<span data-ttu-id="5fa6d-120">`private`如果记录类型为，则属性为 `sealed` 。</span><span class="sxs-lookup"><span data-stu-id="5fa6d-120">The property is `private` if the record type is `sealed`.</span></span> <span data-ttu-id="5fa6d-121">否则，属性为 `virtual` 和 `protected` 。</span><span class="sxs-lookup"><span data-stu-id="5fa6d-121">Otherwise, the property is `virtual` and `protected`.</span></span>
<span data-ttu-id="5fa6d-122">可以显式声明属性。</span><span class="sxs-lookup"><span data-stu-id="5fa6d-122">The property can be declared explicitly.</span></span> <span data-ttu-id="5fa6d-123">如果显式声明与预期的签名或辅助功能不匹配，或者如果显式声明不允许在派生类型中重写它并且记录类型不是，则是错误的 `sealed` 。</span><span class="sxs-lookup"><span data-stu-id="5fa6d-123">It is an error if the explicit declaration does not match the expected signature or accessibility, or if the explicit declaration doesn't allow overriding it in a derived type and the record type is not `sealed`.</span></span>

<span data-ttu-id="5fa6d-124">如果记录类型是从基本记录类型派生的 `Base` ，则记录类型包括合成 readonly 属性，该属性等效于声明为的属性，如下所示：</span><span class="sxs-lookup"><span data-stu-id="5fa6d-124">If the record type is derived from a base record type `Base`, the record type includes a synthesized readonly property equivalent to a property declared as follows:</span></span>
```C#
protected override Type EqualityContract { get; };
```

<span data-ttu-id="5fa6d-125">可以显式声明属性。</span><span class="sxs-lookup"><span data-stu-id="5fa6d-125">The property can be declared explicitly.</span></span> <span data-ttu-id="5fa6d-126">如果显式声明与预期的签名或辅助功能不匹配，或者如果显式声明不允许在派生类型中重写它并且记录类型不是，则是错误的 `sealed` 。</span><span class="sxs-lookup"><span data-stu-id="5fa6d-126">It is an error if the explicit declaration does not match the expected signature or accessibility, or if the explicit declaration doesn't allow overriding it in a derived type and the record type is not `sealed`.</span></span> <span data-ttu-id="5fa6d-127">如果合成的或显式声明的属性未在记录类型中重写具有此签名的属性，则该属性是错误的， `Base` 例如，如果在 `Base` 、密封或不是 ) 虚拟等中缺少该属性，则该属性 (。</span><span class="sxs-lookup"><span data-stu-id="5fa6d-127">It is an error if either synthesized, or explicitly declared property doesn't override a property with this signature in the record type `Base` (for example, if the property is missing in the `Base`, or sealed, or not virtual, etc.).</span></span>
<span data-ttu-id="5fa6d-128">合成属性返回 `typeof(R)` ，其中 `R` 是记录类型。</span><span class="sxs-lookup"><span data-stu-id="5fa6d-128">The synthesized property returns `typeof(R)` where `R` is the record type.</span></span>

<span data-ttu-id="5fa6d-129">记录类型实现 `System.IEquatable<R>` 并包含合成型强类型重载， `Equals(R? other)` 其中 `R` 是记录类型。</span><span class="sxs-lookup"><span data-stu-id="5fa6d-129">The record type implements `System.IEquatable<R>` and includes a synthesized strongly-typed overload of `Equals(R? other)` where `R` is the record type.</span></span>
<span data-ttu-id="5fa6d-130">方法为 `public` ，并且方法为， `virtual` 除非记录类型为 `sealed` 。</span><span class="sxs-lookup"><span data-stu-id="5fa6d-130">The method is `public`, and the method is `virtual` unless the record type is `sealed`.</span></span>
<span data-ttu-id="5fa6d-131">可以显式声明方法。</span><span class="sxs-lookup"><span data-stu-id="5fa6d-131">The method can be declared explicitly.</span></span> <span data-ttu-id="5fa6d-132">如果显式声明与预期的签名或辅助功能不匹配，或显式声明不允许在派生类型中重写，并且记录类型不是，则是错误的 `sealed` 。</span><span class="sxs-lookup"><span data-stu-id="5fa6d-132">It is an error if the explicit declaration does not match the expected signature or accessibility, or the explicit declaration doesn't allow overriding it in a derived type and the record type is not `sealed`.</span></span>

<span data-ttu-id="5fa6d-133">如果 `Equals(R? other)` 是用户定义的 (未合成) 但 `GetHashCode` 不是，则会生成警告。</span><span class="sxs-lookup"><span data-stu-id="5fa6d-133">If `Equals(R? other)` is user-defined (not synthesized) but `GetHashCode` is not, a warning is produced.</span></span>

```C#
public virtual bool Equals(R? other);
```
<span data-ttu-id="5fa6d-134">`Equals(R?)` `true` 当且仅当以下各项都为时，合成返回 `true` ：</span><span class="sxs-lookup"><span data-stu-id="5fa6d-134">The synthesized `Equals(R?)` returns `true` if and only if each of the following are `true`:</span></span>
- <span data-ttu-id="5fa6d-135">`other` 不是 `null` ，并且</span><span class="sxs-lookup"><span data-stu-id="5fa6d-135">`other` is not `null`, and</span></span>
- <span data-ttu-id="5fa6d-136">对于 `fieldN` 记录类型中不是继承的每个实例字段，其中的值 `System.Collections.Generic.EqualityComparer<TN>.Default.Equals(fieldN, other.fieldN)` `TN` 为字段类型，而</span><span class="sxs-lookup"><span data-stu-id="5fa6d-136">For each instance field `fieldN` in the record type that is not inherited, the value of `System.Collections.Generic.EqualityComparer<TN>.Default.Equals(fieldN, other.fieldN)` where `TN` is the field type, and</span></span>
- <span data-ttu-id="5fa6d-137">如果有基本记录类型，则的值 `base.Equals(other)` (对) 的非虚拟调用 `public virtual bool Equals(Base? other)` ; 否则为的值 `EqualityContract == other.EqualityContract` 。</span><span class="sxs-lookup"><span data-stu-id="5fa6d-137">If there is a base record type, the value of `base.Equals(other)` (a non-virtual call to `public virtual bool Equals(Base? other)`); otherwise the value of `EqualityContract == other.EqualityContract`.</span></span>

<span data-ttu-id="5fa6d-138">记录类型包括合成 `==` 运算符和与 `!=` 运算符等效的运算符，如下所示：</span><span class="sxs-lookup"><span data-stu-id="5fa6d-138">The record type includes synthesized `==` and `!=` operators equivalent to operators declared as follows:</span></span>
```C#
public static bool operator==(R? r1, R? r2)
    => (object)r1 == r2 || (r1?.Equals(r2) ?? false);
public static bool operator!=(R? r1, R? r2)
    => !(r1 == r2);
```
<span data-ttu-id="5fa6d-139">`Equals`运算符调用的方法 `==` 是 `Equals(R? other)` 上面指定的方法。</span><span class="sxs-lookup"><span data-stu-id="5fa6d-139">The `Equals` method called by the `==` operator is the `Equals(R? other)` method specified above.</span></span> <span data-ttu-id="5fa6d-140">`!=`运算符委托给 `==` 运算符。</span><span class="sxs-lookup"><span data-stu-id="5fa6d-140">The `!=` operator delegates to the `==` operator.</span></span> <span data-ttu-id="5fa6d-141">如果显式声明了运算符，则是错误的。</span><span class="sxs-lookup"><span data-stu-id="5fa6d-141">It is an error if the operators are declared explicitly.</span></span>
    
<span data-ttu-id="5fa6d-142">如果记录类型是从基本记录类型派生的 `Base` ，则记录类型包括合成重写等效于如下所示的方法：</span><span class="sxs-lookup"><span data-stu-id="5fa6d-142">If the record type is derived from a base record type `Base`, the record type includes a synthesized override equivalent to a method declared as follows:</span></span>
```C#
public sealed override bool Equals(Base? other);
```
<span data-ttu-id="5fa6d-143">如果显式声明了重写，则是错误的。</span><span class="sxs-lookup"><span data-stu-id="5fa6d-143">It is an error if the override is declared explicitly.</span></span> <span data-ttu-id="5fa6d-144">如果方法在记录类型中未重写具有相同签名的方法，则是错误的 `Base` (例如，如果方法在中缺失， `Base` 或者密封或不是虚拟的，等等 ) 。</span><span class="sxs-lookup"><span data-stu-id="5fa6d-144">It is an error if the method doesn't override a method with same signature in record type `Base` (for example, if the method is missing in the `Base`, or sealed, or not virtual, etc.).</span></span>
<span data-ttu-id="5fa6d-145">合成重写返回 `Equals((object?)other)` 。</span><span class="sxs-lookup"><span data-stu-id="5fa6d-145">The synthesized override returns `Equals((object?)other)`.</span></span>

<span data-ttu-id="5fa6d-146">记录类型包括合成重写等效于如下所示的方法：</span><span class="sxs-lookup"><span data-stu-id="5fa6d-146">The record type includes a synthesized override equivalent to a method declared as follows:</span></span>
```C#
public override bool Equals(object? obj);
```
<span data-ttu-id="5fa6d-147">如果显式声明了重写，则是错误的。</span><span class="sxs-lookup"><span data-stu-id="5fa6d-147">It is an error if the override is declared explicitly.</span></span> <span data-ttu-id="5fa6d-148">如果该方法不重写 `object.Equals(object? obj)` (例如，由于中间基类型中的隐藏，等等 ) ，则是错误的。</span><span class="sxs-lookup"><span data-stu-id="5fa6d-148">It is an error if the method doesn't override `object.Equals(object? obj)` (for example, due to shadowing in intermediate base types, etc.).</span></span>
<span data-ttu-id="5fa6d-149">合成重写返回， `Equals(other as R)` 其中 `R` 是记录类型。</span><span class="sxs-lookup"><span data-stu-id="5fa6d-149">The synthesized override returns `Equals(other as R)` where `R` is the record type.</span></span>

<span data-ttu-id="5fa6d-150">记录类型包括合成重写等效于如下所示的方法：</span><span class="sxs-lookup"><span data-stu-id="5fa6d-150">The record type includes a synthesized override equivalent to a method declared as follows:</span></span>
```C#
public override int GetHashCode();
```
<span data-ttu-id="5fa6d-151">可以显式声明方法。</span><span class="sxs-lookup"><span data-stu-id="5fa6d-151">The method can be declared explicitly.</span></span>
<span data-ttu-id="5fa6d-152">如果显式声明不允许在派生类型中重写它并且记录类型不是，则是错误的 `sealed` 。</span><span class="sxs-lookup"><span data-stu-id="5fa6d-152">It is an error if the explicit declaration doesn't allow overriding it in a derived type and the record type is not `sealed`.</span></span> <span data-ttu-id="5fa6d-153">如果合成的或显式声明的方法不会重写 `object.GetHashCode()` (例如，由于中间基类型中的隐藏等 ) ，则是错误的。</span><span class="sxs-lookup"><span data-stu-id="5fa6d-153">It is an error if either synthesized, or explicitly declared method doesn't override `object.GetHashCode()` (for example, due to shadowing in intermediate base types, etc.).</span></span>
 
<span data-ttu-id="5fa6d-154">如果 `Equals(R?)` 和中 `GetHashCode()` 的一个是显式声明的，而另一个方法不是显式的，则会报告警告。</span><span class="sxs-lookup"><span data-stu-id="5fa6d-154">A warning is reported if one of `Equals(R?)` and `GetHashCode()` is explicitly declared but the other method is not explicit.</span></span>

<span data-ttu-id="5fa6d-155">的合成重写 `GetHashCode()` 返回将 `int` 以下值组合在一起的确定性函数的结果：</span><span class="sxs-lookup"><span data-stu-id="5fa6d-155">The synthesized override of `GetHashCode()` returns an `int` result of a deterministic function combining the following values:</span></span>
- <span data-ttu-id="5fa6d-156">对于 `fieldN` 记录类型中不是继承的每个实例字段，其中的值 `System.Collections.Generic.EqualityComparer<TN>.Default.GetHashCode(fieldN)` `TN` 为字段类型，而</span><span class="sxs-lookup"><span data-stu-id="5fa6d-156">For each instance field `fieldN` in the record type that is not inherited, the value of `System.Collections.Generic.EqualityComparer<TN>.Default.GetHashCode(fieldN)` where `TN` is the field type, and</span></span>
- <span data-ttu-id="5fa6d-157">如果有基本记录类型，则的值 `base.GetHashCode()` 为; 否则为的值 `System.Collections.Generic.EqualityComparer<System.Type>.Default.GetHashCode(EqualityContract)` 。</span><span class="sxs-lookup"><span data-stu-id="5fa6d-157">If there is a base record type, the value of `base.GetHashCode()`; otherwise the value of `System.Collections.Generic.EqualityComparer<System.Type>.Default.GetHashCode(EqualityContract)`.</span></span>

<span data-ttu-id="5fa6d-158">例如，请考虑以下记录类型：</span><span class="sxs-lookup"><span data-stu-id="5fa6d-158">For example, consider the following record types:</span></span>
```C#
record R1(T1 P1);
record R2(T1 P1, T2 P2) : R1(P1);
record R3(T1 P1, T2 P2, T3 P3) : R2(P1, P2);
```

<span data-ttu-id="5fa6d-159">对于这些记录类型，合成相等性成员将如下所示：</span><span class="sxs-lookup"><span data-stu-id="5fa6d-159">For those record types, the synthesized equality members would be something like:</span></span>
```C#
class R1 : IEquatable<R1>
{
    public T1 P1 { get; init; }
    protected virtual Type EqualityContract => typeof(R1);
    public override bool Equals(object? obj) => Equals(obj as R1);
    public virtual bool Equals(R1? other)
    {
        return !(other is null) &&
            EqualityContract == other.EqualityContract &&
            EqualityComparer<T1>.Default.Equals(P1, other.P1);
    }
    public static bool operator==(R1? r1, R1? r2)
        => (object)r1 == r2 || (r1?.Equals(r2) ?? false);
    public static bool operator!=(R1? r1, R1? r2)
        => !(r1 == r2);    
    public override int GetHashCode()
    {
        return Combine(EqualityComparer<Type>.Default.GetHashCode(EqualityContract),
            EqualityComparer<T1>.Default.GetHashCode(P1));
    }
}

class R2 : R1, IEquatable<R2>
{
    public T2 P2 { get; init; }
    protected override Type EqualityContract => typeof(R2);
    public override bool Equals(object? obj) => Equals(obj as R2);
    public sealed override bool Equals(R1? other) => Equals((object?)other);
    public virtual bool Equals(R2? other)
    {
        return base.Equals((R1?)other) &&
            EqualityComparer<T2>.Default.Equals(P2, other.P2);
    }
    public static bool operator==(R2? r1, R2? r2)
        => (object)r1 == r2 || (r1?.Equals(r2) ?? false);
    public static bool operator!=(R2? r1, R2? r2)
        => !(r1 == r2);    
    public override int GetHashCode()
    {
        return Combine(base.GetHashCode(),
            EqualityComparer<T2>.Default.GetHashCode(P2));
    }
}

class R3 : R2, IEquatable<R3>
{
    public T3 P3 { get; init; }
    protected override Type EqualityContract => typeof(R3);
    public override bool Equals(object? obj) => Equals(obj as R3);
    public sealed override bool Equals(R2? other) => Equals((object?)other);
    public virtual bool Equals(R3? other)
    {
        return base.Equals((R2?)other) &&
            EqualityComparer<T3>.Default.Equals(P3, other.P3);
    }
    public static bool operator==(R3? r1, R3? r2)
        => (object)r1 == r2 || (r1?.Equals(r2) ?? false);
    public static bool operator!=(R3? r1, R3? r2)
        => !(r1 == r2);    
    public override int GetHashCode()
    {
        return Combine(base.GetHashCode(),
            EqualityComparer<T3>.Default.GetHashCode(P3));
    }
}
```

### <a name="copy-and-clone-members"></a><span data-ttu-id="5fa6d-160">复制和克隆成员</span><span class="sxs-lookup"><span data-stu-id="5fa6d-160">Copy and Clone members</span></span>

<span data-ttu-id="5fa6d-161">记录类型包含两个复制成员：</span><span class="sxs-lookup"><span data-stu-id="5fa6d-161">A record type contains two copying members:</span></span>

* <span data-ttu-id="5fa6d-162">采用记录类型的单个自变量的构造函数。</span><span class="sxs-lookup"><span data-stu-id="5fa6d-162">A constructor taking a single argument of the record type.</span></span> <span data-ttu-id="5fa6d-163">它被称为 "复制构造函数"。</span><span class="sxs-lookup"><span data-stu-id="5fa6d-163">It is referred to as a "copy constructor".</span></span>
* <span data-ttu-id="5fa6d-164">使用编译器保留名称的合成公共无参数实例 "clone" 方法</span><span class="sxs-lookup"><span data-stu-id="5fa6d-164">A synthesized public parameterless instance "clone" method with a compiler-reserved name</span></span>

<span data-ttu-id="5fa6d-165">复制构造函数的目的是将状态从参数复制到正在创建的新实例。</span><span class="sxs-lookup"><span data-stu-id="5fa6d-165">The purpose of the copy constructor is to copy the state from the parameter to the new instance being created.</span></span> <span data-ttu-id="5fa6d-166">此构造函数不会运行记录声明中存在的任何实例字段/属性初始值设定项。</span><span class="sxs-lookup"><span data-stu-id="5fa6d-166">This constructor doesn't run any instance field/property initializers present in the record declaration.</span></span> <span data-ttu-id="5fa6d-167">如果未显式声明构造函数，则编译器将合成构造函数。</span><span class="sxs-lookup"><span data-stu-id="5fa6d-167">If the constructor is not explicitly declared, a constructor will be synthesized by the compiler.</span></span> <span data-ttu-id="5fa6d-168">如果记录是密封的，则构造函数将为私有，否则将受到保护。</span><span class="sxs-lookup"><span data-stu-id="5fa6d-168">If the record is sealed, the constructor will be private, otherwise it will be protected.</span></span>
<span data-ttu-id="5fa6d-169">显式声明的复制构造函数必须是公共的或受保护的，除非记录是密封的。</span><span class="sxs-lookup"><span data-stu-id="5fa6d-169">An explicitly declared copy constructor must be either public or protected, unless the record is sealed.</span></span>
<span data-ttu-id="5fa6d-170">构造函数必须执行的第一件事是调用基的复制构造函数，如果记录继承自 object，则调用无参数的对象构造函数。</span><span class="sxs-lookup"><span data-stu-id="5fa6d-170">The first thing the constructor must do, is to call a copy constructor of the base, or a parameter-less object constructor if the record inherits from object.</span></span> <span data-ttu-id="5fa6d-171">如果用户定义的复制构造函数使用不满足此要求的隐式或显式构造函数初始值设定项，则会报告错误。</span><span class="sxs-lookup"><span data-stu-id="5fa6d-171">An error is reported if a user-defined copy constructor uses an implicit or explicit constructor initializer that doesn't fulfill this requirement.</span></span>
<span data-ttu-id="5fa6d-172">在调用基复制构造函数后，合成复制构造函数将在记录类型内隐式或显式声明的所有实例字段的值。</span><span class="sxs-lookup"><span data-stu-id="5fa6d-172">After a base copy constructor is invoked, a synthesized copy constructor copies values for all instance fields implicitly or explicitly declared within the record type.</span></span> <span data-ttu-id="5fa6d-173">无论是显式还是隐式，复制构造函数的唯一存在都不会阻止自动添加默认实例构造函数。</span><span class="sxs-lookup"><span data-stu-id="5fa6d-173">The sole presence of a copy constructor, whether explicit or implicit, doesn't prevent an automatic addition of a default instance constructor.</span></span>

<span data-ttu-id="5fa6d-174">如果基本记录中存在虚拟 "clone" 方法，合成的 "clone" 方法将重写它，并且如果支持 "协变返回" 功能和重写返回类型，则该方法的返回类型为当前的包含类型。</span><span class="sxs-lookup"><span data-stu-id="5fa6d-174">If a virtual "clone" method is present in the base record, the synthesized "clone" method overrides it and the return type of the method is the current containing type if the "covariant returns" feature is supported and the override return type otherwise.</span></span> <span data-ttu-id="5fa6d-175">如果基本记录克隆方法是密封的，则会生成错误。</span><span class="sxs-lookup"><span data-stu-id="5fa6d-175">An error is produced if the base record clone method is sealed.</span></span>
<span data-ttu-id="5fa6d-176">如果基本记录中不存在虚拟 "clone" 方法，则克隆方法的返回类型为包含类型，并且方法为虚拟，除非该记录是密封或抽象的。</span><span class="sxs-lookup"><span data-stu-id="5fa6d-176">If a virtual "clone" method is not present in the base record, the return type of the clone method is the containing type and the method is virtual, unless the record is sealed or abstract.</span></span>
<span data-ttu-id="5fa6d-177">如果包含的记录是抽象的，合成克隆方法也是抽象的。</span><span class="sxs-lookup"><span data-stu-id="5fa6d-177">If the containing record is abstract, the synthesized clone method is also abstract.</span></span>
<span data-ttu-id="5fa6d-178">如果 "clone" 方法不是抽象的，则会返回对复制构造函数的调用结果。</span><span class="sxs-lookup"><span data-stu-id="5fa6d-178">If the "clone" method is not abstract, it returns the result of a call to a copy constructor.</span></span> 


### <a name="printing-members-printmembers-and-tostring-methods"></a><span data-ttu-id="5fa6d-179">打印成员： PrintMembers 和 ToString 方法</span><span class="sxs-lookup"><span data-stu-id="5fa6d-179">Printing members: PrintMembers and ToString methods</span></span>

<span data-ttu-id="5fa6d-180">如果记录是从派生的 `object` ，则记录包含一个合成方法，该方法等效于声明为的方法：</span><span class="sxs-lookup"><span data-stu-id="5fa6d-180">If the record is derived from `object`, the record includes a synthesized method equivalent to a method declared as follows:</span></span>
```C#
bool PrintMembers(System.Text.StringBuilder builder);
```
<span data-ttu-id="5fa6d-181">`private`如果记录类型为，则方法为 `sealed` 。</span><span class="sxs-lookup"><span data-stu-id="5fa6d-181">The method is `private` if the record type is `sealed`.</span></span> <span data-ttu-id="5fa6d-182">否则，方法为 `virtual` 和 `protected` 。</span><span class="sxs-lookup"><span data-stu-id="5fa6d-182">Otherwise, the method is `virtual` and `protected`.</span></span>

<span data-ttu-id="5fa6d-183">方法如下：</span><span class="sxs-lookup"><span data-stu-id="5fa6d-183">The method:</span></span>
1. <span data-ttu-id="5fa6d-184">对于每个记录的可打印成员 (非静态公共字段和可读属性成员) ，追加该成员的名称后跟 "="，后跟以 "，" 分隔的成员值，</span><span class="sxs-lookup"><span data-stu-id="5fa6d-184">for each of the record's printable members (non-static public field and readable property members), appends that member's name followed by " = " followed by the member's value separated with ", ",</span></span>
2. <span data-ttu-id="5fa6d-185">如果记录具有可打印成员，则返回 true。</span><span class="sxs-lookup"><span data-stu-id="5fa6d-185">return true if the record has printable members.</span></span>

<span data-ttu-id="5fa6d-186">对于具有值类型的成员，我们将使用可用于目标平台的最有效方法将其值转换为字符串表示形式。</span><span class="sxs-lookup"><span data-stu-id="5fa6d-186">For a member that has a value type, we will convert its value to a string representation using the most efficient method available to the target platform.</span></span> <span data-ttu-id="5fa6d-187">目前意味着在 `ToString` 传递到之前调用 `StringBuilder.Append` 。</span><span class="sxs-lookup"><span data-stu-id="5fa6d-187">At present that means calling `ToString` before passing to `StringBuilder.Append`.</span></span>

<span data-ttu-id="5fa6d-188">如果记录类型是从基本记录派生的 `Base` ，则记录包括合成重写等效于声明为的方法：</span><span class="sxs-lookup"><span data-stu-id="5fa6d-188">If the record type is derived from a base record `Base`, the record includes a synthesized override equivalent to a method declared as follows:</span></span>
```C#
protected override bool PrintMembers(StringBuilder builder);
```

<span data-ttu-id="5fa6d-189">如果记录没有可打印成员，则方法将调用 `PrintMembers` 具有一个参数的基方法， (其 `builder` 参数) 并返回结果。</span><span class="sxs-lookup"><span data-stu-id="5fa6d-189">If the record has no printable members, the method calls the base `PrintMembers` method with one argument (its `builder` parameter) and returns the result.</span></span>

<span data-ttu-id="5fa6d-190">否则，方法为：</span><span class="sxs-lookup"><span data-stu-id="5fa6d-190">Otherwise, the method:</span></span>
1. <span data-ttu-id="5fa6d-191">调用基 `PrintMembers` 方法，其中一个参数 (其 `builder` 参数) 。</span><span class="sxs-lookup"><span data-stu-id="5fa6d-191">calls the base `PrintMembers` method with one argument (its `builder` parameter),</span></span>
2. <span data-ttu-id="5fa6d-192">如果该 `PrintMembers` 方法返回 true，则将 "，" 追加到生成器中，</span><span class="sxs-lookup"><span data-stu-id="5fa6d-192">if the `PrintMembers` method returned true, append ", " to the builder,</span></span>
3. <span data-ttu-id="5fa6d-193">对于每个记录的可打印成员，将追加后跟 "=" 后跟成员值的成员的名称： `this.member` (或 `this.member.ToString()` 值类型) ，用 "，" 分隔）。</span><span class="sxs-lookup"><span data-stu-id="5fa6d-193">for each of the record's printable members, appends that member's name followed by " = " followed by the member's value: `this.member` (or `this.member.ToString()` for value types), separated with ", ",</span></span>
4. <span data-ttu-id="5fa6d-194">返回 true。</span><span class="sxs-lookup"><span data-stu-id="5fa6d-194">return true.</span></span>

<span data-ttu-id="5fa6d-195">`PrintMembers`可以显式声明方法。</span><span class="sxs-lookup"><span data-stu-id="5fa6d-195">The `PrintMembers` method can be declared explicitly.</span></span>
<span data-ttu-id="5fa6d-196">如果显式声明与预期的签名或辅助功能不匹配，或者如果显式声明不允许在派生类型中重写它并且记录类型不是，则是错误的 `sealed` 。</span><span class="sxs-lookup"><span data-stu-id="5fa6d-196">It is an error if the explicit declaration does not match the expected signature or accessibility, or if the explicit declaration doesn't allow overriding it in a derived type and the record type is not `sealed`.</span></span>

<span data-ttu-id="5fa6d-197">记录包含与声明方法等效的合成方法，如下所示：</span><span class="sxs-lookup"><span data-stu-id="5fa6d-197">The record includes a synthesized method equivalent to a method declared as follows:</span></span>
```C#
public override string ToString();
```

<span data-ttu-id="5fa6d-198">可以显式声明方法。</span><span class="sxs-lookup"><span data-stu-id="5fa6d-198">The method can be declared explicitly.</span></span> <span data-ttu-id="5fa6d-199">如果显式声明与预期的签名或辅助功能不匹配，或者如果显式声明不允许在派生类型中重写它并且记录类型不是，则是错误的 `sealed` 。</span><span class="sxs-lookup"><span data-stu-id="5fa6d-199">It is an error if the explicit declaration does not match the expected signature or accessibility, or if the explicit declaration doesn't allow overriding it in a derived type and the record type is not `sealed`.</span></span> <span data-ttu-id="5fa6d-200">如果合成的或显式声明的方法不会重写 `object.ToString()` (例如，由于中间基类型中的隐藏等 ) ，则是错误的。</span><span class="sxs-lookup"><span data-stu-id="5fa6d-200">It is an error if either synthesized, or explicitly declared method doesn't override `object.ToString()` (for example, due to shadowing in intermediate base types, etc.).</span></span>

<span data-ttu-id="5fa6d-201">合成方法：</span><span class="sxs-lookup"><span data-stu-id="5fa6d-201">The synthesized method:</span></span>
1. <span data-ttu-id="5fa6d-202">创建一个 `StringBuilder` 实例，</span><span class="sxs-lookup"><span data-stu-id="5fa6d-202">creates a `StringBuilder` instance,</span></span>
2. <span data-ttu-id="5fa6d-203">将记录名称追加到生成器，后面跟有 "{"、</span><span class="sxs-lookup"><span data-stu-id="5fa6d-203">appends the record name to the builder, followed by " { ",</span></span>
3. <span data-ttu-id="5fa6d-204">调用记录的 `PrintMembers` 方法，为其提供生成器，后跟 "" （如果它返回 true），</span><span class="sxs-lookup"><span data-stu-id="5fa6d-204">invokes the record's `PrintMembers` method giving it the builder, followed by " " if it returned true,</span></span>
4. <span data-ttu-id="5fa6d-205">追加 "}"，</span><span class="sxs-lookup"><span data-stu-id="5fa6d-205">appends "}",</span></span>
3. <span data-ttu-id="5fa6d-206">返回生成器的内容 `builder.ToString()` 。</span><span class="sxs-lookup"><span data-stu-id="5fa6d-206">returns the builder's contents with `builder.ToString()`.</span></span>

<span data-ttu-id="5fa6d-207">例如，请考虑以下记录类型：</span><span class="sxs-lookup"><span data-stu-id="5fa6d-207">For example, consider the following record types:</span></span>

``` csharp
record R1(T1 P1);
record R2(T1 P1, T2 P2, T3 P3) : R1(P1);
```

<span data-ttu-id="5fa6d-208">对于这些记录类型，合成打印成员应类似于：</span><span class="sxs-lookup"><span data-stu-id="5fa6d-208">For those record types, the synthesized printing members would be something like:</span></span>

```C#
class R1 : IEquatable<R1>
{
    public T1 P1 { get; init; }
    
    protected virtual bool PrintMembers(StringBuilder builder)
    {
        builder.Append(nameof(P1));
        builder.Append(" = ");
        builder.Append(this.P1); // or builder.Append(this.P1.ToString()); if P1 has a value type
        
        return true;
    }
    
    public override string ToString()
    {
        var builder = new StringBuilder();
        builder.Append(nameof(R1));
        builder.Append(" { ");

        if (PrintMembers(builder))
            builder.Append(" ");

        builder.Append("}");
        return builder.ToString();
    }
}

class R2 : R1, IEquatable<R2>
{
    public T2 P2 { get; init; }
    public T3 P3 { get; init; }
    
    protected override void PrintMembers(StringBuilder builder)
    {
        if (base.PrintMembers(builder))
            builder.Append(", ");
            
        builder.Append(nameof(P2));
        builder.Append(" = ");
        builder.Append(this.P2); // or builder.Append(this.P2); if P2 has a value type
        
        builder.Append(", ");
        
        builder.Append(nameof(P3));
        builder.Append(" = ");
        builder.Append(this.P3); // or builder.Append(this.P3); if P3 has a value type
        
        return true;
    }
    
    public override string ToString()
    {
        var builder = new StringBuilder();
        builder.Append(nameof(R2));
        builder.Append(" { ");

        if (PrintMembers(builder))
            builder.Append(" ");

        builder.Append("}");
        return builder.ToString();
    }
}
```

## <a name="positional-record-members"></a><span data-ttu-id="5fa6d-209">位置记录成员</span><span class="sxs-lookup"><span data-stu-id="5fa6d-209">Positional record members</span></span>

<span data-ttu-id="5fa6d-210">除了以上成员以外，具有参数列表 ( "位置记录" 的记录 ) 合成其他成员与上述成员具有相同的条件。</span><span class="sxs-lookup"><span data-stu-id="5fa6d-210">In addition to the above members, records with a parameter list ("positional records") synthesize additional members with the same conditions as the members above.</span></span>

### <a name="primary-constructor"></a><span data-ttu-id="5fa6d-211">主构造函数</span><span class="sxs-lookup"><span data-stu-id="5fa6d-211">Primary Constructor</span></span>

<span data-ttu-id="5fa6d-212">记录类型具有公共构造函数，该构造函数的签名对应于类型声明的值参数。</span><span class="sxs-lookup"><span data-stu-id="5fa6d-212">A record type has a public constructor whose signature corresponds to the value parameters of the type declaration.</span></span> <span data-ttu-id="5fa6d-213">这称为类型的主构造函数，并导致禁止显示隐式声明的默认类构造函数（如果存在）。</span><span class="sxs-lookup"><span data-stu-id="5fa6d-213">This is called the primary constructor for the type, and causes the implicitly declared default class constructor, if present, to be suppressed.</span></span> <span data-ttu-id="5fa6d-214">类中已存在具有相同签名的主构造函数和构造函数是错误的。</span><span class="sxs-lookup"><span data-stu-id="5fa6d-214">It is an error to have a primary constructor and a constructor with the same signature already present in the class.</span></span>

<span data-ttu-id="5fa6d-215">在运行时，主构造函数</span><span class="sxs-lookup"><span data-stu-id="5fa6d-215">At runtime the primary constructor</span></span>

1. <span data-ttu-id="5fa6d-216">执行出现在类体中的实例初始值设定项</span><span class="sxs-lookup"><span data-stu-id="5fa6d-216">executes the instance initializers appearing in the class-body</span></span>

1. <span data-ttu-id="5fa6d-217">用子句中提供的参数 `record_base` （如果存在）调用基类构造函数</span><span class="sxs-lookup"><span data-stu-id="5fa6d-217">invokes the base class constructor with the arguments provided in the `record_base` clause, if present</span></span>

<span data-ttu-id="5fa6d-218">如果记录具有主构造函数，则任何用户定义的构造函数（"复制构造函数" 除外）都必须具有显式 `this` 构造函数初始值设定项。</span><span class="sxs-lookup"><span data-stu-id="5fa6d-218">If a record has a primary constructor, any user-defined constructor, except "copy constructor" must have an explicit `this` constructor initializer.</span></span> 

<span data-ttu-id="5fa6d-219">主构造函数的参数以及记录的成员位于 `argument_list` `record_base` 子句的和实例字段或属性的初始值设定项中的范围内。</span><span class="sxs-lookup"><span data-stu-id="5fa6d-219">Parameters of the primary constructor as well as members of the record are in scope within the `argument_list` of the `record_base` clause and within initializers of instance fields or properties.</span></span> <span data-ttu-id="5fa6d-220">实例成员将是这些位置中的错误 (类似于当前在常规构造函数初始值设定项的作用域中的方式，但使用) 的错误，但主构造函数的参数将在范围内并且可用，并将隐藏成员。</span><span class="sxs-lookup"><span data-stu-id="5fa6d-220">Instance members would be an error in these locations (similar to how instance members are in scope in regular constructor initializers today, but an error to use), but the parameters of the primary constructor would be in scope and useable and would shadow members.</span></span> <span data-ttu-id="5fa6d-221">静态成员还可以使用，类似于目前普通构造函数中的基调用和初始值设定项的工作方式。</span><span class="sxs-lookup"><span data-stu-id="5fa6d-221">Static members would also be useable, similar to how base calls and initializers work in ordinary constructors today.</span></span>

<span data-ttu-id="5fa6d-222">如果未读取主构造函数的参数，则会生成一个警告。</span><span class="sxs-lookup"><span data-stu-id="5fa6d-222">A warning is produced if a parameter of the primary constructor is not read.</span></span>

<span data-ttu-id="5fa6d-223">在中声明的表达式变量 `argument_list` 在范围内 `argument_list` 。</span><span class="sxs-lookup"><span data-stu-id="5fa6d-223">Expression variables declared in the `argument_list` are in scope within the `argument_list`.</span></span> <span data-ttu-id="5fa6d-224">与规则构造函数初始值设定项的参数列表中相同的隐藏规则也适用。</span><span class="sxs-lookup"><span data-stu-id="5fa6d-224">The same shadowing rules as within an argument list of a regular constructor initializer apply.</span></span>

### <a name="properties"></a><span data-ttu-id="5fa6d-225">属性</span><span class="sxs-lookup"><span data-stu-id="5fa6d-225">Properties</span></span>

<span data-ttu-id="5fa6d-226">对于记录类型声明的每个记录参数，都有一个对应的公共属性成员，其名称和类型取自值参数声明。</span><span class="sxs-lookup"><span data-stu-id="5fa6d-226">For each record parameter of a record type declaration there is a corresponding public property member whose name and type are taken from the value parameter declaration.</span></span>

<span data-ttu-id="5fa6d-227">对于 a 记录：</span><span class="sxs-lookup"><span data-stu-id="5fa6d-227">For a record:</span></span>

* <span data-ttu-id="5fa6d-228">创建公共 `get` 和 `init` 自动属性 (参阅单独的 `init` 取值函数说明) 。</span><span class="sxs-lookup"><span data-stu-id="5fa6d-228">A public `get` and `init` auto-property is created (see separate `init` accessor specification).</span></span>
  <span data-ttu-id="5fa6d-229">`abstract`具有匹配类型的继承属性被重写。</span><span class="sxs-lookup"><span data-stu-id="5fa6d-229">An inherited `abstract` property with matching type is overridden.</span></span>
  <span data-ttu-id="5fa6d-230">如果继承的属性没有可 `public` 重写的 `get` 和 `init` 访问器，则是错误的。</span><span class="sxs-lookup"><span data-stu-id="5fa6d-230">It is an error if the inherited property does not have `public` overridable `get` and `init` accessors.</span></span>
  <span data-ttu-id="5fa6d-231">自动属性初始化为相应主构造函数参数的值。</span><span class="sxs-lookup"><span data-stu-id="5fa6d-231">The auto-property is initialized to the value of the corresponding primary constructor parameter.</span></span>
  <span data-ttu-id="5fa6d-232">特性可应用于合成自动属性及其支持字段，方法是使用 `property:` 或 `field:` 语法应用于相应记录参数的属性的目标。</span><span class="sxs-lookup"><span data-stu-id="5fa6d-232">Attributes can be applied to the synthesized auto-property and its backing field by using `property:` or `field:` targets for attributes syntactically applied to the corresponding record parameter.</span></span>  

### <a name="deconstruct"></a><span data-ttu-id="5fa6d-233">析构</span><span class="sxs-lookup"><span data-stu-id="5fa6d-233">Deconstruct</span></span>

<span data-ttu-id="5fa6d-234">具有至少一个参数的位置记录合成名为析构的公共 void 返回实例方法，其中包含主构造函数声明的每个参数的 out 参数声明。</span><span class="sxs-lookup"><span data-stu-id="5fa6d-234">A positional record with at least one parameter synthesizes a public void-returning instance method called Deconstruct with an out parameter declaration for each parameter of the primary constructor declaration.</span></span> <span data-ttu-id="5fa6d-235">析构方法的每个参数都具有与主构造函数声明的相应参数相同的类型。</span><span class="sxs-lookup"><span data-stu-id="5fa6d-235">Each parameter of the Deconstruct method has the same type as the corresponding parameter of the primary constructor declaration.</span></span> <span data-ttu-id="5fa6d-236">方法的主体将析构方法的每个参数分配给一个实例成员访问同名的成员的值。</span><span class="sxs-lookup"><span data-stu-id="5fa6d-236">The body of the method assigns each parameter of the Deconstruct method to the value from an instance member access to a member of the same name.</span></span>
<span data-ttu-id="5fa6d-237">可以显式声明方法。</span><span class="sxs-lookup"><span data-stu-id="5fa6d-237">The method can be declared explicitly.</span></span> <span data-ttu-id="5fa6d-238">如果显式声明与预期的签名或辅助功能不匹配，或者是静态的，则是错误的。</span><span class="sxs-lookup"><span data-stu-id="5fa6d-238">It is an error if the explicit declaration does not match the expected signature or accessibility, or is static.</span></span>

## <a name="with-expression"></a><span data-ttu-id="5fa6d-239">`with` 表达式</span><span class="sxs-lookup"><span data-stu-id="5fa6d-239">`with` expression</span></span>

<span data-ttu-id="5fa6d-240">`with`表达式是使用以下语法的新表达式。</span><span class="sxs-lookup"><span data-stu-id="5fa6d-240">A `with` expression is a new expression using the following syntax.</span></span>

```antlr
with_expression
    : switch_expression
    | switch_expression 'with' '{' member_initializer_list? '}'
    ;

member_initializer_list
    : member_initializer (',' member_initializer)*
    ;

member_initializer
    : identifier '=' expression
    ;
```
<span data-ttu-id="5fa6d-241">`with`表达式不允许作为语句。</span><span class="sxs-lookup"><span data-stu-id="5fa6d-241">A `with` expression is not permitted as a statement.</span></span>

<span data-ttu-id="5fa6d-242">`with`表达式允许 "非破坏性转变"，旨在生成接收方表达式的副本，并在中对赋值进行修改 `member_initializer_list` 。</span><span class="sxs-lookup"><span data-stu-id="5fa6d-242">A `with` expression allows for "non-destructive mutation", designed to produce a copy of the receiver expression with modifications in assignments in the `member_initializer_list`.</span></span>

<span data-ttu-id="5fa6d-243">有效的 `with` 表达式包含具有非 void 类型的接收方。</span><span class="sxs-lookup"><span data-stu-id="5fa6d-243">A valid `with` expression has a receiver with a non-void type.</span></span> <span data-ttu-id="5fa6d-244">接收方类型必须是一条记录。</span><span class="sxs-lookup"><span data-stu-id="5fa6d-244">The receiver type must be a record.</span></span>

<span data-ttu-id="5fa6d-245">表达式的右侧 `with` 是一个 `member_initializer_list` 带有 *标识符* 的赋值序列的，它必须是接收方类型的可访问实例字段或属性。</span><span class="sxs-lookup"><span data-stu-id="5fa6d-245">On the right hand side of the `with` expression is a `member_initializer_list` with a sequence of assignments to *identifier*, which must be an accessible instance field or property of the receiver's type.</span></span>

<span data-ttu-id="5fa6d-246">首先，调用接收方的 "clone" 方法 (调用) ，并将其结果转换为接收方的类型。</span><span class="sxs-lookup"><span data-stu-id="5fa6d-246">First, receiver's "clone" method (specified above) is invoked and its result is converted to the receiver's type.</span></span> <span data-ttu-id="5fa6d-247">然后，每个 `member_initializer` 处理方式与对转换结果的字段或属性访问的赋值相同。</span><span class="sxs-lookup"><span data-stu-id="5fa6d-247">Then, each `member_initializer` is processed the same way as an assignment to a field or property access of the result of the conversion.</span></span> <span data-ttu-id="5fa6d-248">按词法顺序处理赋值。</span><span class="sxs-lookup"><span data-stu-id="5fa6d-248">Assignments are processed in lexical order.</span></span>

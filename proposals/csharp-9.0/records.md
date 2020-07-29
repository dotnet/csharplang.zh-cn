---
ms.openlocfilehash: 8a888c789909c3e818328e5521e47f4dcbbd5a88
ms.sourcegitcommit: 0c25406d8a99064bb85d934bb32ffcf547753acc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/28/2020
ms.locfileid: "87297256"
---

# <a name="records"></a><span data-ttu-id="a8be4-101">记录</span><span class="sxs-lookup"><span data-stu-id="a8be4-101">Records</span></span>

<span data-ttu-id="a8be4-102">本建议将跟踪 c # 9 记录功能的规范（由 c # 语言设计团队同意）。</span><span class="sxs-lookup"><span data-stu-id="a8be4-102">This proposal tracks the specification for the C# 9 records feature, as agreed to by the C# language design team.</span></span>

<span data-ttu-id="a8be4-103">记录的语法如下所示：</span><span class="sxs-lookup"><span data-stu-id="a8be4-103">The syntax for a record is as follows:</span></span>

```antlr
record_declaration
    : attributes? class_modifier* 'partial'? 'record' identifier type_parameter_list?
      parameter_list? record_base? type_parameter_constraints_clause* record_body
    ;

record_base
    : ':' class_type argument_list?
    | ':' interface_type_list
    | ':' class_type argument_list? interface_type_list
    ;

record_body
    : '{' class_member_declaration* '}'
    | ';'
    ;
```

<span data-ttu-id="a8be4-104">记录类型是引用类型，类似于类声明。</span><span class="sxs-lookup"><span data-stu-id="a8be4-104">Record types are reference types, similar to a class declaration.</span></span> <span data-ttu-id="a8be4-105">如果不包含，记录将提供一个错误 `record_base` `argument_list` `record_declaration` `parameter_list` 。</span><span class="sxs-lookup"><span data-stu-id="a8be4-105">It is an error for a record to provide a `record_base` `argument_list` if the `record_declaration` does not contain a `parameter_list`.</span></span>

<span data-ttu-id="a8be4-106">记录参数不能 `ref` 使用 `out` 或 `this` 修饰符（但 `in` `params` 允许和）。</span><span class="sxs-lookup"><span data-stu-id="a8be4-106">Record parameters cannot use `ref`, `out` or `this` modifiers (but `in` and `params` are allowed).</span></span>

## <a name="inheritance"></a><span data-ttu-id="a8be4-107">继承</span><span class="sxs-lookup"><span data-stu-id="a8be4-107">Inheritance</span></span>

<span data-ttu-id="a8be4-108">除非类为 `object` ，且类不能从记录继承，否则记录不能从类继承。</span><span class="sxs-lookup"><span data-stu-id="a8be4-108">Records cannot inherit from classes, unless the class is `object`, and classes cannot inherit from records.</span></span>

## <a name="members-of-a-record-type"></a><span data-ttu-id="a8be4-109">记录类型的成员</span><span class="sxs-lookup"><span data-stu-id="a8be4-109">Members of a record type</span></span>

<span data-ttu-id="a8be4-110">除了记录体中声明的成员，记录类型还具有附加的合成成员。</span><span class="sxs-lookup"><span data-stu-id="a8be4-110">In addition to the members declared in the record body, a record type has additional synthesized members.</span></span>
<span data-ttu-id="a8be4-111">如果在记录正文中声明了具有 "匹配" 签名的成员，或者继承了具有 "匹配" 签名的可访问的具体非虚拟成员，则将合成成员。</span><span class="sxs-lookup"><span data-stu-id="a8be4-111">Members are synthesized unless a member with a "matching" signature is declared in the record body or an accessible concrete non-virtual member with a "matching" signature is inherited.</span></span>
<span data-ttu-id="a8be4-112">如果两个成员具有相同的签名，或在继承方案中被视为 "隐藏"，则会将其视为匹配项。</span><span class="sxs-lookup"><span data-stu-id="a8be4-112">Two members are considered matching if they have the same signature or would be considered "hiding" in an inheritance scenario.</span></span>
<span data-ttu-id="a8be4-113">将记录的成员命名为 "Clone" 是错误的。</span><span class="sxs-lookup"><span data-stu-id="a8be4-113">It is an error for a member of a record to be named "Clone".</span></span>

<span data-ttu-id="a8be4-114">合成成员如下所示：</span><span class="sxs-lookup"><span data-stu-id="a8be4-114">The synthesized members are as follows:</span></span>

### <a name="equality-members"></a><span data-ttu-id="a8be4-115">相等成员</span><span class="sxs-lookup"><span data-stu-id="a8be4-115">Equality members</span></span>

<span data-ttu-id="a8be4-116">如果记录是从派生的 `object` ，则记录类型包括合成的 readonly 属性，该属性等效于声明的属性，如下所示：</span><span class="sxs-lookup"><span data-stu-id="a8be4-116">If the record is derived from `object`, the record type includes a synthesized readonly property equivalent to a property declared as follows:</span></span>
```C#
Type EqualityContract { get; };
```
<span data-ttu-id="a8be4-117">`private`如果记录类型为，则属性为 `sealed` 。</span><span class="sxs-lookup"><span data-stu-id="a8be4-117">The property is `private` if the record type is `sealed`.</span></span> <span data-ttu-id="a8be4-118">否则，属性为 `virtual` 和 `protected` 。</span><span class="sxs-lookup"><span data-stu-id="a8be4-118">Otherwise, the property is `virtual` and `protected`.</span></span>
<span data-ttu-id="a8be4-119">可以显式声明属性。</span><span class="sxs-lookup"><span data-stu-id="a8be4-119">The property can be declared explicitly.</span></span> <span data-ttu-id="a8be4-120">如果显式声明与预期的签名或辅助功能不匹配，或者如果显式声明不允许在派生类型中 overiding 它且记录类型不匹配，则是错误的 `sealed` 。</span><span class="sxs-lookup"><span data-stu-id="a8be4-120">It is an error if the explicit declaration does not match the expected signature or accessibility, or if the explicit declaration doesn't allow overiding it in a derived type and the record type is not `sealed`.</span></span>

<span data-ttu-id="a8be4-121">如果记录类型是从基本记录类型派生的 `Base` ，则记录类型包括合成 readonly 属性，该属性等效于声明为的属性，如下所示：</span><span class="sxs-lookup"><span data-stu-id="a8be4-121">If the record type is derived from a base record type `Base`, the record type includes a synthesized readonly property equivalent to a property declared as follows:</span></span>
```C#
protected override Type EqualityContract { get; };
```

<span data-ttu-id="a8be4-122">可以显式声明属性。</span><span class="sxs-lookup"><span data-stu-id="a8be4-122">The property can be declared explicitly.</span></span> <span data-ttu-id="a8be4-123">如果显式声明与预期的签名或辅助功能不匹配，或者如果显式声明不允许在派生类型中 overiding 它且记录类型不匹配，则是错误的 `sealed` 。</span><span class="sxs-lookup"><span data-stu-id="a8be4-123">It is an error if the explicit declaration does not match the expected signature or accessibility, or if the explicit declaration doesn't allow overiding it in a derived type and the record type is not `sealed`.</span></span> <span data-ttu-id="a8be4-124">如果合成的或显式声明的属性未在记录类型中重写具有此签名的属性 `Base` （例如，在 `Base` 、密封或非虚拟的情况下，或者在不是虚拟的情况下），则是错误的。</span><span class="sxs-lookup"><span data-stu-id="a8be4-124">It is an error if either synthesized, or explicitly declared property doesn't override a property with this signature in the record type `Base` (for example, if the property is missing in the `Base`, or sealed, or not virtual, etc.).</span></span>
<span data-ttu-id="a8be4-125">合成属性返回 `typeof(R)` ，其中 `R` 是记录类型。</span><span class="sxs-lookup"><span data-stu-id="a8be4-125">The synthesized property returns `typeof(R)` where `R` is the record type.</span></span>

<span data-ttu-id="a8be4-126">_`EqualityContract`如果记录类型为 `sealed` 且派生自，是否可以忽略 `System.Object` ？_</span><span class="sxs-lookup"><span data-stu-id="a8be4-126">_Can we omit `EqualityContract` if the record type is `sealed` and derives from `System.Object`?_</span></span>

<span data-ttu-id="a8be4-127">记录类型实现 `System.IEquatable<R>` 并包含合成型强类型重载， `Equals(R? other)` 其中 `R` 是记录类型。</span><span class="sxs-lookup"><span data-stu-id="a8be4-127">The record type implements `System.IEquatable<R>` and includes a synthesized strongly-typed overload of `Equals(R? other)` where `R` is the record type.</span></span>
<span data-ttu-id="a8be4-128">方法为 `public` ，并且方法为， `virtual` 除非记录类型为 `sealed` 。</span><span class="sxs-lookup"><span data-stu-id="a8be4-128">The method is `public`, and the method is `virtual` unless the record type is `sealed`.</span></span>
<span data-ttu-id="a8be4-129">可以显式声明方法。</span><span class="sxs-lookup"><span data-stu-id="a8be4-129">The method can be declared explicitly.</span></span> <span data-ttu-id="a8be4-130">如果显式声明与预期的签名或辅助功能不匹配，或显式声明不允许在派生类型中 overiding 它，并且记录类型不是，则是错误的 `sealed` 。</span><span class="sxs-lookup"><span data-stu-id="a8be4-130">It is an error if the explicit declaration does not match the expected signature or accessibility, or the explicit declaration doesn't allow overiding it in a derived type and the record type is not `sealed`.</span></span>
```C#
public virtual bool Equals(R? other);
```
<span data-ttu-id="a8be4-131">`Equals(R?)` `true` 当且仅当以下各项都为时，合成返回 `true` ：</span><span class="sxs-lookup"><span data-stu-id="a8be4-131">The synthesized `Equals(R?)` returns `true` if and only if each of the following are `true`:</span></span>
- <span data-ttu-id="a8be4-132">`other`不是 `null` ，并且</span><span class="sxs-lookup"><span data-stu-id="a8be4-132">`other` is not `null`, and</span></span>
- <span data-ttu-id="a8be4-133">对于 `fieldN` 记录类型中不是继承的每个实例字段，其中的值 `System.Collections.Generic.EqualityComparer<TN>.Default.Equals(fieldN, other.fieldN)` `TN` 为字段类型，而</span><span class="sxs-lookup"><span data-stu-id="a8be4-133">For each instance field `fieldN` in the record type that is not inherited, the value of `System.Collections.Generic.EqualityComparer<TN>.Default.Equals(fieldN, other.fieldN)` where `TN` is the field type, and</span></span>
- <span data-ttu-id="a8be4-134">如果有基本记录类型，则的值 `base.Equals(other)` （对的非虚拟调用 `public virtual bool Equals(Base? other)` ）; 否则为的值 `EqualityContract == other.EqualityContract` 。</span><span class="sxs-lookup"><span data-stu-id="a8be4-134">If there is a base record type, the value of `base.Equals(other)` (a non-virtual call to `public virtual bool Equals(Base? other)`); otherwise the value of `EqualityContract == other.EqualityContract`.</span></span>

<span data-ttu-id="a8be4-135">如果记录类型是从基本记录类型派生的 `Base` ，则记录类型包括合成重写等效于如下所示的方法：</span><span class="sxs-lookup"><span data-stu-id="a8be4-135">If the record type is derived from a base record type `Base`, the record type includes a synthesized override equivalent to a method declared as follows:</span></span>
```C#
public sealed override bool Equals(Base? other);
```
<span data-ttu-id="a8be4-136">如果显式声明了重写，则是错误的。</span><span class="sxs-lookup"><span data-stu-id="a8be4-136">It is an error if the override is declared explicitly.</span></span> <span data-ttu-id="a8be4-137">如果此方法不会在记录类型中重写具有相同签名的方法 `Base` （例如，如果中缺少该方法， `Base` 或者密封了或不是虚拟的等），则是错误的。</span><span class="sxs-lookup"><span data-stu-id="a8be4-137">It is an error if the method doesn't override a method with same signature in record type `Base` (for example, if the method is missing in the `Base`, or sealed, or not virtual, etc.).</span></span>
<span data-ttu-id="a8be4-138">合成重写返回 `Equals((object?)other)` 。</span><span class="sxs-lookup"><span data-stu-id="a8be4-138">The synthesized override returns `Equals((object?)other)`.</span></span>

<span data-ttu-id="a8be4-139">记录类型包括合成重写等效于如下所示的方法：</span><span class="sxs-lookup"><span data-stu-id="a8be4-139">The record type includes a synthesized override equivalent to a method declared as follows:</span></span>
```C#
public override bool Equals(object? obj);
```
<span data-ttu-id="a8be4-140">如果显式声明了重写，则是错误的。</span><span class="sxs-lookup"><span data-stu-id="a8be4-140">It is an error if the override is declared explicitly.</span></span> <span data-ttu-id="a8be4-141">如果该方法不能重写 `object.Equals(object? obj)` （例如，由于中间基类型中的隐藏，等等），则是错误的。</span><span class="sxs-lookup"><span data-stu-id="a8be4-141">It is an error if the method doesn't override `object.Equals(object? obj)` (for example, due to shadowing in intermediate base types, etc.).</span></span>
<span data-ttu-id="a8be4-142">合成重写返回， `Equals(other as R)` 其中 `R` 是记录类型。</span><span class="sxs-lookup"><span data-stu-id="a8be4-142">The synthesized override returns `Equals(other as R)` where `R` is the record type.</span></span>

<span data-ttu-id="a8be4-143">记录类型包括合成重写等效于如下所示的方法：</span><span class="sxs-lookup"><span data-stu-id="a8be4-143">The record type includes a synthesized override equivalent to a method declared as follows:</span></span>
```C#
public override int GetHashCode();
```
<span data-ttu-id="a8be4-144">可以显式声明方法。</span><span class="sxs-lookup"><span data-stu-id="a8be4-144">The method can be declared explicitly.</span></span>
<span data-ttu-id="a8be4-145">如果显式声明不允许在派生类型中 overiding 它，并且记录类型不是，则是错误的 `sealed` 。</span><span class="sxs-lookup"><span data-stu-id="a8be4-145">It is an error if the explicit declaration doesn't allow overiding it in a derived type and the record type is not `sealed`.</span></span> <span data-ttu-id="a8be4-146">如果合成或显式声明方法未重写 `object.GetHashCode()` （例如，由于中间基类型中的隐藏，等等），则是错误的。</span><span class="sxs-lookup"><span data-stu-id="a8be4-146">It is an error if either synthesized, or explicitly declared method doesn't override `object.GetHashCode()` (for example, due to shadowing in intermediate base types, etc.).</span></span>
 
<span data-ttu-id="a8be4-147">如果 `Equals(R?)` 和中 `GetHashCode()` 的一个是显式声明的，而另一个方法不是显式的，则会报告警告。</span><span class="sxs-lookup"><span data-stu-id="a8be4-147">A warning is reported if one of `Equals(R?)` and `GetHashCode()` is explicitly declared but the other method is not explicit.</span></span>

<span data-ttu-id="a8be4-148">的合成重写 `GetHashCode()` 返回将 `int` 以下值组合在一起的确定性函数的结果：</span><span class="sxs-lookup"><span data-stu-id="a8be4-148">The synthesized override of `GetHashCode()` returns an `int` result of a deterministic function combining the following values:</span></span>
- <span data-ttu-id="a8be4-149">对于 `fieldN` 记录类型中不是继承的每个实例字段，其中的值 `System.Collections.Generic.EqualityComparer<TN>.Default.GetHashCode(fieldN)` `TN` 为字段类型，而</span><span class="sxs-lookup"><span data-stu-id="a8be4-149">For each instance field `fieldN` in the record type that is not inherited, the value of `System.Collections.Generic.EqualityComparer<TN>.Default.GetHashCode(fieldN)` where `TN` is the field type, and</span></span>
- <span data-ttu-id="a8be4-150">如果有基本记录类型，则的值 `base.GetHashCode()` 为; 否则为的值 `System.Collections.Generic.EqualityComparer<System.Type>.Default.GetHashCode(EqualityContract)` 。</span><span class="sxs-lookup"><span data-stu-id="a8be4-150">If there is a base record type, the value of `base.GetHashCode()`; otherwise the value of `System.Collections.Generic.EqualityComparer<System.Type>.Default.GetHashCode(EqualityContract)`.</span></span>

<span data-ttu-id="a8be4-151">例如，请考虑以下记录类型：</span><span class="sxs-lookup"><span data-stu-id="a8be4-151">For example, consider the following record types:</span></span>
```C#
record R1(T1 P1);
record R2(T1 P1, T2 P2) : R1(P1);
record R2(T1 P1, T2 P2, T3 P3) : R2(P1, P2);
```

<span data-ttu-id="a8be4-152">对于这些记录类型，合成成员将如下所示：</span><span class="sxs-lookup"><span data-stu-id="a8be4-152">For those record types, the synthesized members would be something like:</span></span>
```C#
class R1 : IEquatable<R1>
{
    public T1 P1 { get; set; }
    protected virtual Type EqualityContract => typeof(R1);
    public override bool Equals(object? obj) => Equals(obj as R1);
    public virtual bool Equals(R1? other)
    {
        return !(other is null) &&
            EqualityContract == other.EqualityContract &&
            EqualityComparer<T1>.Default.Equals(P1, other.P1);
    }
    public override int GetHashCode()
    {
        return Combine(EqualityComparer<Type>.Default.GetHashCode(EqualityContract),
            EqualityComparer<T1>.Default.GetHashCode(P1));
    }
}

class R2 : R1, IEquatable<R2>
{
    public T2 P2 { get; set; }
    protected override Type EqualityContract => typeof(R2);
    public override bool Equals(object? obj) => Equals(obj as R2);
    public sealed override bool Equals(R1? other) => Equals((object?)other);
    public virtual bool Equals(R2? other)
    {
        return base.Equals((R1?)other) &&
            EqualityComparer<T2>.Default.Equals(P2, other.P2);
    }
    public override int GetHashCode()
    {
        return Combine(base.GetHashCode(),
            EqualityComparer<T2>.Default.GetHashCode(P2));
    }
}

class R3 : R2, IEquatable<R3>
{
    public T3 P3 { get; set; }
    protected override Type EqualityContract => typeof(R3);
    public override bool Equals(object? obj) => Equals(obj as R3);
    public sealed override bool Equals(R2? other) => Equals((object?)other);
    public virtual bool Equals(R3? other)
    {
        return base.Equals((R2?)other) &&
            EqualityComparer<T3>.Default.Equals(P3, other.P3);
    }
    public override int GetHashCode()
    {
        return Combine(base.GetHashCode(),
            EqualityComparer<T3>.Default.GetHashCode(P3));
    }
}
```

### <a name="copy-and-clone-members"></a><span data-ttu-id="a8be4-153">复制和克隆成员</span><span class="sxs-lookup"><span data-stu-id="a8be4-153">Copy and Clone members</span></span>

<span data-ttu-id="a8be4-154">记录类型包含两个复制成员：</span><span class="sxs-lookup"><span data-stu-id="a8be4-154">A record type contains two copying members:</span></span>

* <span data-ttu-id="a8be4-155">采用记录类型的单个自变量的构造函数。</span><span class="sxs-lookup"><span data-stu-id="a8be4-155">A constructor taking a single argument of the record type.</span></span> <span data-ttu-id="a8be4-156">它被称为 "复制构造函数"。</span><span class="sxs-lookup"><span data-stu-id="a8be4-156">It is referred to as a "copy constructor".</span></span>
* <span data-ttu-id="a8be4-157">使用编译器保留名称的合成公共无参数实例 "clone" 方法</span><span class="sxs-lookup"><span data-stu-id="a8be4-157">A synthesized public parameterless instance "clone" method with a compiler-reserved name</span></span>

<span data-ttu-id="a8be4-158">复制构造函数的目的是将状态从参数复制到正在创建的新实例。</span><span class="sxs-lookup"><span data-stu-id="a8be4-158">The purpose of the copy constructor is to copy the state from the parameter to the new instance being created.</span></span> <span data-ttu-id="a8be4-159">此构造函数不会运行记录声明中存在的任何实例字段/属性初始值设定项。</span><span class="sxs-lookup"><span data-stu-id="a8be4-159">This constructor doesn't run any instance field/property initializers present in the record declaration.</span></span> <span data-ttu-id="a8be4-160">如果未显式声明构造函数，则编译器将合成构造函数。</span><span class="sxs-lookup"><span data-stu-id="a8be4-160">If the constructor is not explicitly declared, a constructor will be synthesized by the compiler.</span></span> <span data-ttu-id="a8be4-161">如果记录是密封的，则构造函数将为私有，否则将受到保护。</span><span class="sxs-lookup"><span data-stu-id="a8be4-161">If the record is sealed, the constructor will be private, otherwise it will be protected.</span></span>
<span data-ttu-id="a8be4-162">显式声明的复制构造函数必须是公共的或受保护的，除非记录是密封的。</span><span class="sxs-lookup"><span data-stu-id="a8be4-162">An explicitly declared copy constructor must be either public or protected, unless the record is sealed.</span></span>
<span data-ttu-id="a8be4-163">构造函数必须执行的第一件事是调用基的复制构造函数，如果记录继承自 object，则调用无参数的对象构造函数。</span><span class="sxs-lookup"><span data-stu-id="a8be4-163">The first thing the constructor must do, is to call a copy constructor of the base, or a parameter-less object constructor if the record inherits from object.</span></span> <span data-ttu-id="a8be4-164">如果用户定义的复制构造函数使用不满足此要求的隐式或显式构造函数初始值设定项，则会报告错误。</span><span class="sxs-lookup"><span data-stu-id="a8be4-164">An error is reported if a user-defined copy constructor uses an implicit or explicit constructor initializer that doesn't fulfill this requirement.</span></span>
<span data-ttu-id="a8be4-165">在调用基复制构造函数后，合成复制构造函数将在记录类型内隐式或显式声明的所有实例字段的值。</span><span class="sxs-lookup"><span data-stu-id="a8be4-165">After a base copy constructor is invoked, a synthesized copy constructor copies values for all instance fields implicitly or explicitly declared within the record type.</span></span>

<span data-ttu-id="a8be4-166">如果基本记录中存在虚拟 "clone" 方法，合成的 "clone" 方法将重写它，并且如果支持 "协变返回" 功能和重写返回类型，则该方法的返回类型为当前的包含类型。</span><span class="sxs-lookup"><span data-stu-id="a8be4-166">If a virtual "clone" method is present in the base record, the synthesized "clone" method overrides it and the return type of the method is the current containing type if the "covariant returns" feature is supported and the override return type otherwise.</span></span> <span data-ttu-id="a8be4-167">如果基本记录克隆方法是密封的，则会生成错误。</span><span class="sxs-lookup"><span data-stu-id="a8be4-167">An error is produced if the base record clone method is sealed.</span></span>
<span data-ttu-id="a8be4-168">如果基本记录中不存在虚拟 "clone" 方法，则克隆方法的返回类型为包含类型，并且方法为虚拟，除非该记录是密封或抽象的。</span><span class="sxs-lookup"><span data-stu-id="a8be4-168">If a virtual "clone" method is not present in the base record, the return type of the clone method is the containing type and the method is virtual, unless the record is sealed or abstract.</span></span>
<span data-ttu-id="a8be4-169">如果包含的记录是抽象的，合成克隆方法也是抽象的。</span><span class="sxs-lookup"><span data-stu-id="a8be4-169">If the containing record is abstract, the synthesized clone method is also abstract.</span></span>
<span data-ttu-id="a8be4-170">如果 "clone" 方法不是抽象的，则会返回对复制构造函数的调用结果。</span><span class="sxs-lookup"><span data-stu-id="a8be4-170">If the "clone" method is not abstract, it returns the result of a call to a copy constructor.</span></span> 


## <a name="positional-record-members"></a><span data-ttu-id="a8be4-171">位置记录成员</span><span class="sxs-lookup"><span data-stu-id="a8be4-171">Positional record members</span></span>

<span data-ttu-id="a8be4-172">除了以上成员以外，具有参数列表的记录（"位置记录"）合成其他成员与上述成员具有相同的条件。</span><span class="sxs-lookup"><span data-stu-id="a8be4-172">In addition to the above members, records with a parameter list ("positional records") synthesize additional members with the same conditions as the members above.</span></span>

### <a name="primary-constructor"></a><span data-ttu-id="a8be4-173">主构造函数</span><span class="sxs-lookup"><span data-stu-id="a8be4-173">Primary Constructor</span></span>

<span data-ttu-id="a8be4-174">记录类型具有公共构造函数，该构造函数的签名对应于类型声明的值参数。</span><span class="sxs-lookup"><span data-stu-id="a8be4-174">A record type has a public constructor whose signature corresponds to the value parameters of the type declaration.</span></span> <span data-ttu-id="a8be4-175">这称为类型的主构造函数，并导致禁止显示隐式声明的默认类构造函数（如果存在）。</span><span class="sxs-lookup"><span data-stu-id="a8be4-175">This is called the primary constructor for the type, and causes the implicitly declared default class constructor, if present, to be suppressed.</span></span> <span data-ttu-id="a8be4-176">类中已存在具有相同签名的主构造函数和构造函数是错误的。</span><span class="sxs-lookup"><span data-stu-id="a8be4-176">It is an error to have a primary constructor and a constructor with the same signature already present in the class.</span></span>

<span data-ttu-id="a8be4-177">在运行时，主构造函数</span><span class="sxs-lookup"><span data-stu-id="a8be4-177">At runtime the primary constructor</span></span>

1. <span data-ttu-id="a8be4-178">执行出现在类体中的实例初始值设定项</span><span class="sxs-lookup"><span data-stu-id="a8be4-178">executes the instance initializers appearing in the class-body</span></span>

1. <span data-ttu-id="a8be4-179">用子句中提供的参数 `record_base` （如果存在）调用基类构造函数</span><span class="sxs-lookup"><span data-stu-id="a8be4-179">invokes the base class constructor with the arguments provided in the `record_base` clause, if present</span></span>

<span data-ttu-id="a8be4-180">如果记录具有主构造函数，则任何用户定义的构造函数（"复制构造函数" 除外）都必须具有显式 `this` 构造函数初始值设定项。</span><span class="sxs-lookup"><span data-stu-id="a8be4-180">If a record has a primary constructor, any user-defined constructor, except "copy constructor" must have an explicit `this` constructor initializer.</span></span> 

<span data-ttu-id="a8be4-181">主构造函数的参数以及记录的成员位于 `argument_list` `record_base` 子句的和实例字段或属性的初始值设定项中的范围内。</span><span class="sxs-lookup"><span data-stu-id="a8be4-181">Parameters of the primary constructor as well as members of the record are in scope within the `argument_list` of the `record_base` clause and within initializers of instance fields or properties.</span></span> <span data-ttu-id="a8be4-182">实例成员将是这些位置中的错误（类似于当前在常规构造函数初始值设定项的范围内的方式，但使用的是错误），但主构造函数的参数在范围内并且可用，并将隐藏成员。</span><span class="sxs-lookup"><span data-stu-id="a8be4-182">Instance members would be an error in these locations (similar to how instance members are in scope in regular constructor initializers today, but an error to use), but the parameters of the primary constructor would be in scope and useable and would shadow members.</span></span> <span data-ttu-id="a8be4-183">静态成员还可以使用，类似于目前普通构造函数中的基调用和初始值设定项的工作方式。</span><span class="sxs-lookup"><span data-stu-id="a8be4-183">Static members would also be useable, similar to how base calls and initializers work in ordinary constructors today.</span></span> 

<span data-ttu-id="a8be4-184">在中声明的表达式变量 `argument_list` 在范围内 `argument_list` 。</span><span class="sxs-lookup"><span data-stu-id="a8be4-184">Expression variables declared in the `argument_list` are in scope within the `argument_list`.</span></span> <span data-ttu-id="a8be4-185">与规则构造函数初始值设定项的参数列表中相同的隐藏规则也适用。</span><span class="sxs-lookup"><span data-stu-id="a8be4-185">The same shadowing rules as within an argument list of a regular constructor initializer apply.</span></span>

### <a name="properties"></a><span data-ttu-id="a8be4-186">属性</span><span class="sxs-lookup"><span data-stu-id="a8be4-186">Properties</span></span>

<span data-ttu-id="a8be4-187">对于记录类型声明的每个记录参数，都有一个对应的公共属性成员，其名称和类型取自值参数声明。</span><span class="sxs-lookup"><span data-stu-id="a8be4-187">For each record parameter of a record type declaration there is a corresponding public property member whose name and type are taken from the value parameter declaration.</span></span>

<span data-ttu-id="a8be4-188">对于 a 记录：</span><span class="sxs-lookup"><span data-stu-id="a8be4-188">For a record:</span></span>

* <span data-ttu-id="a8be4-189">创建公共 `get` 和 `init` 自动属性（请参阅单独的 `init` 访问器规范）。</span><span class="sxs-lookup"><span data-stu-id="a8be4-189">A public `get` and `init` auto-property is created (see separate `init` accessor specification).</span></span>
  <span data-ttu-id="a8be4-190">`abstract`具有匹配类型的继承属性被重写。</span><span class="sxs-lookup"><span data-stu-id="a8be4-190">An inherited `abstract` property with matching type is overridden.</span></span>
  <span data-ttu-id="a8be4-191">如果继承的属性没有可 `public` 重写的 `get` 和 `init` 访问器，则是错误的。</span><span class="sxs-lookup"><span data-stu-id="a8be4-191">It is an error if the inherited property does not have `public` overridable `get` and `init` accessors.</span></span>
  <span data-ttu-id="a8be4-192">自动属性初始化为相应主构造函数参数的值。</span><span class="sxs-lookup"><span data-stu-id="a8be4-192">The auto-property is initialized to the value of the corresponding primary constructor parameter.</span></span>
  <span data-ttu-id="a8be4-193">特性可应用于合成自动属性及其支持字段，方法是使用 `property:` 或 `field:` 语法应用于相应记录参数的属性的目标。</span><span class="sxs-lookup"><span data-stu-id="a8be4-193">Attributes can be applied to the synthesized auto-property and its backing field by using `property:` or `field:` targets for attributes syntactically applied to the corresponding record parameter.</span></span>  

### <a name="deconstruct"></a><span data-ttu-id="a8be4-194">析构</span><span class="sxs-lookup"><span data-stu-id="a8be4-194">Deconstruct</span></span>

<span data-ttu-id="a8be4-195">位置记录合成名为析构的公共 void 返回实例方法，其中包含主构造函数声明的每个参数的 out 参数声明。</span><span class="sxs-lookup"><span data-stu-id="a8be4-195">A positional record synthesizes a public void-returning instance method called Deconstruct with an out parameter declaration for each parameter of the primary constructor declaration.</span></span> <span data-ttu-id="a8be4-196">析构方法的每个参数都具有与主构造函数声明的相应参数相同的类型。</span><span class="sxs-lookup"><span data-stu-id="a8be4-196">Each parameter of the Deconstruct method has the same type as the corresponding parameter of the primary constructor declaration.</span></span> <span data-ttu-id="a8be4-197">方法的主体将析构方法的每个参数分配给一个实例成员访问同名的成员的值。</span><span class="sxs-lookup"><span data-stu-id="a8be4-197">The body of the method assigns each parameter of the Deconstruct method to the value from an instance member access to a member of the same name.</span></span>
<span data-ttu-id="a8be4-198">可以显式声明方法。</span><span class="sxs-lookup"><span data-stu-id="a8be4-198">The method can be declared explicitly.</span></span> <span data-ttu-id="a8be4-199">如果显式声明与预期的签名或辅助功能不匹配，或者是静态的，则是错误的。</span><span class="sxs-lookup"><span data-stu-id="a8be4-199">It is an error if the explicit declaration does not match the expected signature or accessibility, or is static.</span></span>

## <a name="with-expression"></a><span data-ttu-id="a8be4-200">`with` 表达式</span><span class="sxs-lookup"><span data-stu-id="a8be4-200">`with` expression</span></span>

<span data-ttu-id="a8be4-201">`with`表达式是使用以下语法的新表达式。</span><span class="sxs-lookup"><span data-stu-id="a8be4-201">A `with` expression is a new expression using the following syntax.</span></span>

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

<span data-ttu-id="a8be4-202">`with`表达式允许 "非破坏性转变"，旨在生成接收方表达式的副本，并在中对赋值进行修改 `member_initializer_list` 。</span><span class="sxs-lookup"><span data-stu-id="a8be4-202">A `with` expression allows for "non-destructive mutation", designed to produce a copy of the receiver expression with modifications in assignments in the `member_initializer_list`.</span></span>

<span data-ttu-id="a8be4-203">有效的 `with` 表达式包含具有非 void 类型的接收方。</span><span class="sxs-lookup"><span data-stu-id="a8be4-203">A valid `with` expression has a receiver with a non-void type.</span></span> <span data-ttu-id="a8be4-204">接收方类型必须是一条记录。</span><span class="sxs-lookup"><span data-stu-id="a8be4-204">The receiver type must be a record.</span></span>

<span data-ttu-id="a8be4-205">表达式的右侧 `with` 是一个 `member_initializer_list` 带有*标识符*的赋值序列的，它必须是接收方类型的可访问实例字段或属性。</span><span class="sxs-lookup"><span data-stu-id="a8be4-205">On the right hand side of the `with` expression is a `member_initializer_list` with a sequence of assignments to *identifier*, which must be an accessible instance field or property of the receiver's type.</span></span>

<span data-ttu-id="a8be4-206">首先，调用接收方的 "clone" 方法，并将其结果转换为接收方的类型。</span><span class="sxs-lookup"><span data-stu-id="a8be4-206">First, receiver's "clone" method (specified above) is invoked and its result is converted to the receiver's type.</span></span> <span data-ttu-id="a8be4-207">然后，每个 `member_initializer` 处理方式与对转换结果的字段或属性访问的赋值相同。</span><span class="sxs-lookup"><span data-stu-id="a8be4-207">Then, each `member_initializer` is processed the same way as an assignment to a field or property access of the result of the conversion.</span></span> <span data-ttu-id="a8be4-208">按词法顺序处理赋值。</span><span class="sxs-lookup"><span data-stu-id="a8be4-208">Assignments are processed in lexical order.</span></span>

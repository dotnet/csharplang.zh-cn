---
ms.openlocfilehash: a62f2efa5b0a38a408fc4aaba2860be22f5d161b
ms.sourcegitcommit: a9b70c6ee1117df36eb66cf5b8e45c47e6c4f12e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/17/2021
ms.locfileid: "98536254"
---
# <a name="record-structs"></a><span data-ttu-id="c0aff-101">记录结构</span><span class="sxs-lookup"><span data-stu-id="c0aff-101">Record structs</span></span>

<span data-ttu-id="c0aff-102">记录结构的语法如下所示：</span><span class="sxs-lookup"><span data-stu-id="c0aff-102">The syntax for a record struct is as follows:</span></span>

```antlr
record_struct_declaration
    : attributes? struct_modifier* 'partial'? 'record' 'struct' identifier type_parameter_list?
      parameter_list? struct_interfaces? type_parameter_constraints_clause* record_struct_body
    ;

record_struct_body
    : struct_body
    | ';'
    ;
```

<span data-ttu-id="c0aff-103">记录结构类型是值类型，如其他结构类型。</span><span class="sxs-lookup"><span data-stu-id="c0aff-103">Record struct types are value types, like other struct types.</span></span> <span data-ttu-id="c0aff-104">它们隐式继承自类 `System.ValueType` 。</span><span class="sxs-lookup"><span data-stu-id="c0aff-104">They implicitly inherit from the class `System.ValueType`.</span></span>
<span data-ttu-id="c0aff-105">记录结构的修饰符和成员受到的限制与结构 (可访问性的限制相同，成员、无参数实例构造函数、 `base(...)` 实例构造函数初始值设定项、构造函数中的明确赋值、 `this` 析构函数 ) 。</span><span class="sxs-lookup"><span data-stu-id="c0aff-105">The modifiers and members of a record struct are subject to the same restrictions as those of structs (accessibility on type, modifiers on members, parameterless instance constructors, `base(...)` instance constructor initializers, definite assignment for `this` in constructor, destructors, ...).</span></span>

<span data-ttu-id="c0aff-106">请参见https://github.com/dotnet/csharplang/blob/master/spec/structs.md</span><span class="sxs-lookup"><span data-stu-id="c0aff-106">See https://github.com/dotnet/csharplang/blob/master/spec/structs.md</span></span>

<span data-ttu-id="c0aff-107">但如果存在主构造函数，则允许记录结构的实例字段声明包含变量初始值设定项。</span><span class="sxs-lookup"><span data-stu-id="c0aff-107">But instance field declarations for a record struct are permitted to include variable initializers when there is a primary constructor.</span></span>

<span data-ttu-id="c0aff-108">记录结构不能使用 `ref` 修饰符。</span><span class="sxs-lookup"><span data-stu-id="c0aff-108">Record structs cannot use `ref` modifier.</span></span>

<span data-ttu-id="c0aff-109">部分记录结构最多只能有一个分部类型声明可以提供 `parameter_list` 。</span><span class="sxs-lookup"><span data-stu-id="c0aff-109">At most one partial type declaration of a partial record struct may provide a `parameter_list`.</span></span>
<span data-ttu-id="c0aff-110">`parameter_list`不能为空。</span><span class="sxs-lookup"><span data-stu-id="c0aff-110">The `parameter_list` may not be empty.</span></span>

<span data-ttu-id="c0aff-111">记录结构参数不能 `ref` 使用 `out` 或 `this` 修饰符 (但 `in` `params` 允许) 。</span><span class="sxs-lookup"><span data-stu-id="c0aff-111">Record struct parameters cannot use `ref`, `out` or `this` modifiers (but `in` and `params` are allowed).</span></span>

## <a name="members-of-a-record-struct"></a><span data-ttu-id="c0aff-112">Record 结构的成员</span><span class="sxs-lookup"><span data-stu-id="c0aff-112">Members of a record struct</span></span>

<span data-ttu-id="c0aff-113">除了在 record 结构体中声明的成员外，记录结构类型还具有附加的合成成员。</span><span class="sxs-lookup"><span data-stu-id="c0aff-113">In addition to the members declared in the record struct body, a record struct type has additional synthesized members.</span></span>
<span data-ttu-id="c0aff-114">除非在 record 结构主体中声明了具有 "匹配" 签名的成员，或者继承了具有 "匹配" 签名的可访问的具体非虚拟成员，否则将合成成员。</span><span class="sxs-lookup"><span data-stu-id="c0aff-114">Members are synthesized unless a member with a "matching" signature is declared in the record struct body or an accessible concrete non-virtual member with a "matching" signature is inherited.</span></span>
<span data-ttu-id="c0aff-115">如果两个成员具有相同的签名，或在继承方案中被视为 "隐藏"，则会将其视为匹配项。</span><span class="sxs-lookup"><span data-stu-id="c0aff-115">Two members are considered matching if they have the same signature or would be considered "hiding" in an inheritance scenario.</span></span>
<span data-ttu-id="c0aff-116">请参见https://github.com/dotnet/csharplang/blob/master/spec/basic-concepts.md#signatures-and-overloading</span><span class="sxs-lookup"><span data-stu-id="c0aff-116">See https://github.com/dotnet/csharplang/blob/master/spec/basic-concepts.md#signatures-and-overloading</span></span>

<span data-ttu-id="c0aff-117">记录结构的成员命名为 "Clone" 是错误的。</span><span class="sxs-lookup"><span data-stu-id="c0aff-117">It is an error for a member of a record struct to be named "Clone".</span></span>

<span data-ttu-id="c0aff-118">记录结构的实例字段具有不安全类型是错误的。</span><span class="sxs-lookup"><span data-stu-id="c0aff-118">It is an error for an instance field of a record struct to have an unsafe type.</span></span>

<span data-ttu-id="c0aff-119">不允许记录结构声明析构函数。</span><span class="sxs-lookup"><span data-stu-id="c0aff-119">A record struct is not permitted to declare a destructor.</span></span>

<span data-ttu-id="c0aff-120">合成成员如下所示：</span><span class="sxs-lookup"><span data-stu-id="c0aff-120">The synthesized members are as follows:</span></span>

### <a name="equality-members"></a><span data-ttu-id="c0aff-121">相等成员</span><span class="sxs-lookup"><span data-stu-id="c0aff-121">Equality members</span></span>

<span data-ttu-id="c0aff-122">合成相等性成员类似于此类型 (的记录类 `Equals` 、 `Equals` `object` 类型 `==` 和 `!=` 此类型的运算符) ，</span><span class="sxs-lookup"><span data-stu-id="c0aff-122">The synthesized equality members are similar as in a record class (`Equals` for this type, `Equals` for `object` type, `==` and `!=` operators for this type),</span></span>\
<span data-ttu-id="c0aff-123">除缺少以外 `EqualityContract` ，空检查或继承。</span><span class="sxs-lookup"><span data-stu-id="c0aff-123">except for the lack of `EqualityContract`, null checks or inheritance.</span></span>

<span data-ttu-id="c0aff-124">Record 结构实现 `System.IEquatable<R>` 并包含合成的强类型重载， `Equals(R other)` 其中 `R` 是记录结构。</span><span class="sxs-lookup"><span data-stu-id="c0aff-124">The record struct implements `System.IEquatable<R>` and includes a synthesized strongly-typed overload of `Equals(R other)` where `R` is the record struct.</span></span>
<span data-ttu-id="c0aff-125">方法为 `public` 。</span><span class="sxs-lookup"><span data-stu-id="c0aff-125">The method is `public`.</span></span>
<span data-ttu-id="c0aff-126">可以显式声明方法。</span><span class="sxs-lookup"><span data-stu-id="c0aff-126">The method can be declared explicitly.</span></span> <span data-ttu-id="c0aff-127">如果显式声明与预期的签名或辅助功能不匹配，则是错误的。</span><span class="sxs-lookup"><span data-stu-id="c0aff-127">It is an error if the explicit declaration does not match the expected signature or accessibility.</span></span>

<span data-ttu-id="c0aff-128">如果 `Equals(R other)` 是用户定义的 (未合成) 但 `GetHashCode` 不是，则会生成警告。</span><span class="sxs-lookup"><span data-stu-id="c0aff-128">If `Equals(R other)` is user-defined (not synthesized) but `GetHashCode` is not, a warning is produced.</span></span>

```C#
public bool Equals(R other);
```

<span data-ttu-id="c0aff-129">`Equals(R)` `true` 当且仅当记录结构中的每个实例字段的值为时，才会返回， `fieldN` `System.Collections.Generic.EqualityComparer<TN>.Default.Equals(fieldN, other.fieldN)` 其中， `TN` 字段类型为 `true` 。</span><span class="sxs-lookup"><span data-stu-id="c0aff-129">The synthesized `Equals(R)` returns `true` if and only if for each instance field `fieldN` in the record struct the value of `System.Collections.Generic.EqualityComparer<TN>.Default.Equals(fieldN, other.fieldN)` where `TN` is the field type is `true`.</span></span>

<span data-ttu-id="c0aff-130">记录结构包括合成 `==` 运算符和与 `!=` 运算符等效的运算符，如下所示：</span><span class="sxs-lookup"><span data-stu-id="c0aff-130">The record struct includes synthesized `==` and `!=` operators equivalent to operators declared as follows:</span></span>
```C#
public static bool operator==(R r1, R r2)
    => r1.Equals(r2);
public static bool operator!=(R r1, R r2)
    => !(r1 == r2);
```
<span data-ttu-id="c0aff-131">`Equals`运算符调用的方法 `==` 是 `Equals(R other)` 上面指定的方法。</span><span class="sxs-lookup"><span data-stu-id="c0aff-131">The `Equals` method called by the `==` operator is the `Equals(R other)` method specified above.</span></span> <span data-ttu-id="c0aff-132">`!=`运算符委托给 `==` 运算符。</span><span class="sxs-lookup"><span data-stu-id="c0aff-132">The `!=` operator delegates to the `==` operator.</span></span> <span data-ttu-id="c0aff-133">如果显式声明了运算符，则是错误的。</span><span class="sxs-lookup"><span data-stu-id="c0aff-133">It is an error if the operators are declared explicitly.</span></span>

<span data-ttu-id="c0aff-134">Record 结构包括合成重写等效于声明为的方法：</span><span class="sxs-lookup"><span data-stu-id="c0aff-134">The record struct includes a synthesized override equivalent to a method declared as follows:</span></span>
```C#
public override bool Equals(object? obj);
```
<span data-ttu-id="c0aff-135">如果显式声明了重写，则是错误的。</span><span class="sxs-lookup"><span data-stu-id="c0aff-135">It is an error if the override is declared explicitly.</span></span> <span data-ttu-id="c0aff-136">合成重写返回， `other is R temp && Equals(temp)` 其中 `R` 是记录结构。</span><span class="sxs-lookup"><span data-stu-id="c0aff-136">The synthesized override returns `other is R temp && Equals(temp)` where `R` is the record struct.</span></span>

<span data-ttu-id="c0aff-137">Record 结构包括合成重写等效于声明为的方法：</span><span class="sxs-lookup"><span data-stu-id="c0aff-137">The record struct includes a synthesized override equivalent to a method declared as follows:</span></span>
```C#
public override int GetHashCode();
```
<span data-ttu-id="c0aff-138">可以显式声明方法。</span><span class="sxs-lookup"><span data-stu-id="c0aff-138">The method can be declared explicitly.</span></span>

<span data-ttu-id="c0aff-139">如果 `Equals(R)` 和中 `GetHashCode()` 的一个是显式声明的，而另一个方法不是显式的，则会报告警告。</span><span class="sxs-lookup"><span data-stu-id="c0aff-139">A warning is reported if one of `Equals(R)` and `GetHashCode()` is explicitly declared but the other method is not explicit.</span></span>

<span data-ttu-id="c0aff-140">的合成重写 `GetHashCode()` 返回 `int` 一个确定性函数的结果，该函数将 `System.Collections.Generic.EqualityComparer<TN>.Default.GetHashCode(fieldN)` 每个实例字段的值与的类型组合在 `fieldN` 一起 `TN` `fieldN` 。</span><span class="sxs-lookup"><span data-stu-id="c0aff-140">The synthesized override of `GetHashCode()` returns an `int` result of a deterministic function combining the values of `System.Collections.Generic.EqualityComparer<TN>.Default.GetHashCode(fieldN)` for each instance field `fieldN` with `TN` being the type of `fieldN`.</span></span>

<span data-ttu-id="c0aff-141">例如，请考虑以下 record 结构：</span><span class="sxs-lookup"><span data-stu-id="c0aff-141">For example, consider the following record struct:</span></span>
```C#
record struct R1(T1 P1, T2 P2);
```

<span data-ttu-id="c0aff-142">对于此记录结构，合成相等性成员将如下所示：</span><span class="sxs-lookup"><span data-stu-id="c0aff-142">For this record struct, the synthesized equality members would be something like:</span></span>
```C#
struct R1 : IEquatable<R1>
{
    public T1 P1 { get; set; }
    public T2 P2 { get; set; }
    public override bool Equals(object? obj) => obj is R1 temp && Equals(temp);
    public bool Equals(R1 other)
    {
        return
            EqualityComparer<T1>.Default.Equals(P1, other.P1) &&
            EqualityComparer<T2>.Default.Equals(P2, other.P2);
    }
    public static bool operator==(R1 r1, R1 r2)
        => r1.Equals(r2);
    public static bool operator!=(R1 r1, R1 r2)
        => !(r1 == r2);    
    public override int GetHashCode()
    {
        return Combine(
            EqualityComparer<T1>.Default.GetHashCode(P1),
            EqualityComparer<T2>.Default.GetHashCode(P2));
    }
}
```

### <a name="printing-members-printmembers-and-tostring-methods"></a><span data-ttu-id="c0aff-143">打印成员： PrintMembers 和 ToString 方法</span><span class="sxs-lookup"><span data-stu-id="c0aff-143">Printing members: PrintMembers and ToString methods</span></span>

<span data-ttu-id="c0aff-144">Record 结构包含与声明方法等效的合成方法：</span><span class="sxs-lookup"><span data-stu-id="c0aff-144">The record struct includes a synthesized method equivalent to a method declared as follows:</span></span>
```C#
private bool PrintMembers(System.Text.StringBuilder builder);
```

<span data-ttu-id="c0aff-145">该方法将执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="c0aff-145">The method does the following:</span></span>
1. <span data-ttu-id="c0aff-146">对于每个记录结构的可打印成员 (非静态公共字段和可读属性成员) ，追加该成员的名称后跟 "="，后跟成员的值（用 "，" 分隔），</span><span class="sxs-lookup"><span data-stu-id="c0aff-146">for each of the record struct's printable members (non-static public field and readable property members), appends that member's name followed by " = " followed by the member's value separated with ", ",</span></span>
2. <span data-ttu-id="c0aff-147">如果 record 结构具有可打印成员，则返回 true。</span><span class="sxs-lookup"><span data-stu-id="c0aff-147">return true if the record struct has printable members.</span></span>

<span data-ttu-id="c0aff-148">对于具有值类型的成员，我们将使用可用于目标平台的最有效方法将其值转换为字符串表示形式。</span><span class="sxs-lookup"><span data-stu-id="c0aff-148">For a member that has a value type, we will convert its value to a string representation using the most efficient method available to the target platform.</span></span> <span data-ttu-id="c0aff-149">目前意味着在 `ToString` 传递到之前调用 `StringBuilder.Append` 。</span><span class="sxs-lookup"><span data-stu-id="c0aff-149">At present that means calling `ToString` before passing to `StringBuilder.Append`.</span></span>

<span data-ttu-id="c0aff-150">`PrintMembers`可以显式声明方法。</span><span class="sxs-lookup"><span data-stu-id="c0aff-150">The `PrintMembers` method can be declared explicitly.</span></span>
<span data-ttu-id="c0aff-151">如果显式声明与预期的签名或辅助功能不匹配，则是错误的。</span><span class="sxs-lookup"><span data-stu-id="c0aff-151">It is an error if the explicit declaration does not match the expected signature or accessibility.</span></span>

<span data-ttu-id="c0aff-152">Record 结构包含与声明方法等效的合成方法：</span><span class="sxs-lookup"><span data-stu-id="c0aff-152">The record struct includes a synthesized method equivalent to a method declared as follows:</span></span>
```C#
public override string ToString();
```

<span data-ttu-id="c0aff-153">可以显式声明方法。</span><span class="sxs-lookup"><span data-stu-id="c0aff-153">The method can be declared explicitly.</span></span> <span data-ttu-id="c0aff-154">如果显式声明与预期的签名或辅助功能不匹配，则是错误的。</span><span class="sxs-lookup"><span data-stu-id="c0aff-154">It is an error if the explicit declaration does not match the expected signature or accessibility.</span></span>

<span data-ttu-id="c0aff-155">合成方法：</span><span class="sxs-lookup"><span data-stu-id="c0aff-155">The synthesized method:</span></span>
1. <span data-ttu-id="c0aff-156">创建一个 `StringBuilder` 实例，</span><span class="sxs-lookup"><span data-stu-id="c0aff-156">creates a `StringBuilder` instance,</span></span>
2. <span data-ttu-id="c0aff-157">将记录结构名称追加到生成器，后跟 "{"，</span><span class="sxs-lookup"><span data-stu-id="c0aff-157">appends the record struct name to the builder, followed by " { ",</span></span>
3. <span data-ttu-id="c0aff-158">调用记录结构的 `PrintMembers` 方法，为其提供生成器，后跟 "" （如果它返回 true），</span><span class="sxs-lookup"><span data-stu-id="c0aff-158">invokes the record struct's `PrintMembers` method giving it the builder, followed by " " if it returned true,</span></span>
4. <span data-ttu-id="c0aff-159">追加 "}"，</span><span class="sxs-lookup"><span data-stu-id="c0aff-159">appends "}",</span></span>
5. <span data-ttu-id="c0aff-160">返回生成器的内容 `builder.ToString()` 。</span><span class="sxs-lookup"><span data-stu-id="c0aff-160">returns the builder's contents with `builder.ToString()`.</span></span>

<span data-ttu-id="c0aff-161">例如，请考虑以下 record 结构：</span><span class="sxs-lookup"><span data-stu-id="c0aff-161">For example, consider the following record struct:</span></span>

``` csharp
record struct R1(T1 P1, T2 P2);
```

<span data-ttu-id="c0aff-162">对于此记录结构，合成打印成员应类似于：</span><span class="sxs-lookup"><span data-stu-id="c0aff-162">For this record struct, the synthesized printing members would be something like:</span></span>

```C#
struct R1 : IEquatable<R1>
{
    public T1 P1 { get; set; }
    public T2 P2 { get; set; }

    private bool PrintMembers(StringBuilder builder)
    {
        builder.Append(nameof(P1));
        builder.Append(" = ");
        builder.Append(this.P1); // or builder.Append(this.P1.ToString()); if P1 has a value type
        builder.Append(", ");

        builder.Append(nameof(P2));
        builder.Append(" = ");
        builder.Append(this.P2); // or builder.Append(this.P2.ToString()); if P2 has a value type

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
```

## <a name="positional-record-struct-members"></a><span data-ttu-id="c0aff-163">位置记录结构成员</span><span class="sxs-lookup"><span data-stu-id="c0aff-163">Positional record struct members</span></span>

<span data-ttu-id="c0aff-164">除了以上成员以外，使用参数列表记录结构 ( "位置记录" ) 合成其他成员与上述成员具有相同的条件。</span><span class="sxs-lookup"><span data-stu-id="c0aff-164">In addition to the above members, record structs with a parameter list ("positional records") synthesize additional members with the same conditions as the members above.</span></span>

### <a name="primary-constructor"></a><span data-ttu-id="c0aff-165">主构造函数</span><span class="sxs-lookup"><span data-stu-id="c0aff-165">Primary Constructor</span></span>

<span data-ttu-id="c0aff-166">记录结构有一个公共构造函数，该构造函数的签名对应于类型声明的值参数。</span><span class="sxs-lookup"><span data-stu-id="c0aff-166">A record struct has a public constructor whose signature corresponds to the value parameters of the type declaration.</span></span> <span data-ttu-id="c0aff-167">这称为类型的主构造函数。</span><span class="sxs-lookup"><span data-stu-id="c0aff-167">This is called the primary constructor for the type.</span></span> <span data-ttu-id="c0aff-168">如果结构中已存在具有相同签名的主构造函数和构造函数，则是错误的。</span><span class="sxs-lookup"><span data-stu-id="c0aff-168">It is an error to have a primary constructor and a constructor with the same signature already present in the struct.</span></span>
<span data-ttu-id="c0aff-169">不允许记录结构声明无参数的主构造函数。</span><span class="sxs-lookup"><span data-stu-id="c0aff-169">A record struct is not permitted to declare a parameterless primary constructor.</span></span>

<span data-ttu-id="c0aff-170">如果存在主构造函数，则允许记录结构的实例字段声明包含变量初始值设定项。</span><span class="sxs-lookup"><span data-stu-id="c0aff-170">Instance field declarations for a record struct are permitted to include variable initializers when there is a primary constructor.</span></span> <span data-ttu-id="c0aff-171">在运行时，主构造函数会执行在记录结构体中显示的实例初始值设定项。</span><span class="sxs-lookup"><span data-stu-id="c0aff-171">At runtime the primary constructor executes the instance initializers appearing in the record-struct-body.</span></span>

<span data-ttu-id="c0aff-172">如果 record 结构具有主构造函数，则任何用户定义的构造函数（"复制构造函数" 除外）都必须具有显式 `this` 构造函数初始值设定项。</span><span class="sxs-lookup"><span data-stu-id="c0aff-172">If a record struct has a primary constructor, any user-defined constructor, except "copy constructor" must have an explicit `this` constructor initializer.</span></span>

<span data-ttu-id="c0aff-173">主构造函数的参数以及 record 结构的成员位于实例字段或属性的初始值设定项的范围内。</span><span class="sxs-lookup"><span data-stu-id="c0aff-173">Parameters of the primary constructor as well as members of the record struct are in scope within initializers of instance fields or properties.</span></span> <span data-ttu-id="c0aff-174">实例成员将是这些位置中的错误 (类似于当前在常规构造函数初始值设定项的作用域中的方式，但使用) 的错误，但主构造函数的参数将在范围内并且可用，并将隐藏成员。</span><span class="sxs-lookup"><span data-stu-id="c0aff-174">Instance members would be an error in these locations (similar to how instance members are in scope in regular constructor initializers today, but an error to use), but the parameters of the primary constructor would be in scope and useable and would shadow members.</span></span> <span data-ttu-id="c0aff-175">静态成员也是可用的。</span><span class="sxs-lookup"><span data-stu-id="c0aff-175">Static members would also be useable.</span></span>

<span data-ttu-id="c0aff-176">如果未读取主构造函数的参数，则会生成一个警告。</span><span class="sxs-lookup"><span data-stu-id="c0aff-176">A warning is produced if a parameter of the primary constructor is not read.</span></span>

<span data-ttu-id="c0aff-177">结构实例构造函数的明确分配规则适用于记录结构的主构造函数。</span><span class="sxs-lookup"><span data-stu-id="c0aff-177">The definite assigment rules for struct instance constructors apply to the primary constructor of record structs.</span></span> <span data-ttu-id="c0aff-178">例如，以下是一个错误：</span><span class="sxs-lookup"><span data-stu-id="c0aff-178">For instance, the following is an error:</span></span>

```csharp
record struct Pos(int X) // definite assignment error in primary constructor
{
    private int x;
    public int X { get { return x; } set { x = value; } } = X;
}
```

### <a name="properties"></a><span data-ttu-id="c0aff-179">属性</span><span class="sxs-lookup"><span data-stu-id="c0aff-179">Properties</span></span>

<span data-ttu-id="c0aff-180">对于 record 结构声明的每个记录 struct 参数，都有一个对应的公共属性成员，其名称和类型取自值参数声明。</span><span class="sxs-lookup"><span data-stu-id="c0aff-180">For each record struct parameter of a record struct declaration there is a corresponding public property member whose name and type are taken from the value parameter declaration.</span></span>

<span data-ttu-id="c0aff-181">对于 record 结构：</span><span class="sxs-lookup"><span data-stu-id="c0aff-181">For a record struct:</span></span>

* <span data-ttu-id="c0aff-182">`get` `init` 如果 record 结构具有修饰符，则创建公共和自动属性 `readonly` ，否则创建 `get` `set` 。</span><span class="sxs-lookup"><span data-stu-id="c0aff-182">A public `get` and `init` auto-property is created if the record struct has `readonly` modifier, `get` and `set` otherwise.</span></span>
  <span data-ttu-id="c0aff-183">两种类型的 set 访问器都 (`set` 和 `init`) 视为 "匹配"。</span><span class="sxs-lookup"><span data-stu-id="c0aff-183">Both kinds of set accessors (`set` and `init`) are considered "matching".</span></span> <span data-ttu-id="c0aff-184">因此，用户可以声明仅限初始属性来代替合成可变的属性。</span><span class="sxs-lookup"><span data-stu-id="c0aff-184">So the user may declare an init-only property in place of a synthesized mutable one.</span></span>
  <span data-ttu-id="c0aff-185">`abstract`具有匹配类型的继承属性被重写。</span><span class="sxs-lookup"><span data-stu-id="c0aff-185">An inherited `abstract` property with matching type is overridden.</span></span>
  <span data-ttu-id="c0aff-186">如果继承的属性没有 `public` `get` 和 `set` / `init` 访问器，则是错误的。</span><span class="sxs-lookup"><span data-stu-id="c0aff-186">It is an error if the inherited property does not have `public` `get` and `set`/`init` accessors.</span></span>
  <span data-ttu-id="c0aff-187">自动属性初始化为相应主构造函数参数的值。</span><span class="sxs-lookup"><span data-stu-id="c0aff-187">The auto-property is initialized to the value of the corresponding primary constructor parameter.</span></span>
  <span data-ttu-id="c0aff-188">特性可应用于合成自动属性及其支持字段，方法是使用 `property:` 或 `field:` 在语法上应用于相应记录结构参数的属性的目标。</span><span class="sxs-lookup"><span data-stu-id="c0aff-188">Attributes can be applied to the synthesized auto-property and its backing field by using `property:` or `field:` targets for attributes syntactically applied to the corresponding record struct parameter.</span></span>  

### <a name="deconstruct"></a><span data-ttu-id="c0aff-189">析构</span><span class="sxs-lookup"><span data-stu-id="c0aff-189">Deconstruct</span></span>

<span data-ttu-id="c0aff-190">一个位置记录结构，其中至少有一个参数合成一个公共 void 返回实例方法， `Deconstruct` 该方法由主构造函数声明的每个参数的 out 参数声明调用。</span><span class="sxs-lookup"><span data-stu-id="c0aff-190">A positional record struct with at least one parameter synthesizes a public void-returning instance method called `Deconstruct` with an out parameter declaration for each parameter of the primary constructor declaration.</span></span> <span data-ttu-id="c0aff-191">析构方法的每个参数都具有与主构造函数声明的相应参数相同的类型。</span><span class="sxs-lookup"><span data-stu-id="c0aff-191">Each parameter of the Deconstruct method has the same type as the corresponding parameter of the primary constructor declaration.</span></span> <span data-ttu-id="c0aff-192">方法的主体将析构方法的每个参数分配给一个实例成员访问同名的成员的值。</span><span class="sxs-lookup"><span data-stu-id="c0aff-192">The body of the method assigns each parameter of the Deconstruct method to the value from an instance member access to a member of the same name.</span></span>
<span data-ttu-id="c0aff-193">可以显式声明方法。</span><span class="sxs-lookup"><span data-stu-id="c0aff-193">The method can be declared explicitly.</span></span> <span data-ttu-id="c0aff-194">如果显式声明与预期的签名或辅助功能不匹配，或者是静态的，则是错误的。</span><span class="sxs-lookup"><span data-stu-id="c0aff-194">It is an error if the explicit declaration does not match the expected signature or accessibility, or is static.</span></span>

# <a name="allow-with-expression-on-structs"></a><span data-ttu-id="c0aff-195">允许 `with` 在结构上的表达式</span><span class="sxs-lookup"><span data-stu-id="c0aff-195">Allow `with` expression on structs</span></span>

<span data-ttu-id="c0aff-196">现在，表达式中的接收方可以 `with` 使用结构类型。</span><span class="sxs-lookup"><span data-stu-id="c0aff-196">It is now valid for the receiver in a `with` expression to have a struct type.</span></span>

<span data-ttu-id="c0aff-197">表达式的右侧 `with` 是一个 `member_initializer_list` 带有 *标识符* 的赋值序列的，它必须是接收方类型的可访问实例字段或属性。</span><span class="sxs-lookup"><span data-stu-id="c0aff-197">On the right hand side of the `with` expression is a `member_initializer_list` with a sequence of assignments to *identifier*, which must be an accessible instance field or property of the receiver's type.</span></span>

<span data-ttu-id="c0aff-198">对于结构类型为的接收方，接收方首先复制，然后 `member_initializer` 处理的方法与对转换结果的字段或属性访问的赋值相同。</span><span class="sxs-lookup"><span data-stu-id="c0aff-198">For a receiver with struct type, the receiver is first copied, then each `member_initializer` is processed the same way as an assignment to a field or property access of the result of the conversion.</span></span> <span data-ttu-id="c0aff-199">按词法顺序处理赋值。</span><span class="sxs-lookup"><span data-stu-id="c0aff-199">Assignments are processed in lexical order.</span></span>

# <a name="improvements-on-records"></a><span data-ttu-id="c0aff-200">记录改进</span><span class="sxs-lookup"><span data-stu-id="c0aff-200">Improvements on records</span></span>

## <a name="allow-record-class"></a><span data-ttu-id="c0aff-201">禁止 `record class`</span><span class="sxs-lookup"><span data-stu-id="c0aff-201">Allow `record class`</span></span>

<span data-ttu-id="c0aff-202">记录类型的现有语法允许 `record class` 具有与相同的含义 `record` ：</span><span class="sxs-lookup"><span data-stu-id="c0aff-202">The existing syntax for record types allows `record class` with the same meaning as `record`:</span></span>

```antlr
record_declaration
    : attributes? class_modifier* 'partial'? 'record' 'class'? identifier type_parameter_list?
      parameter_list? record_base? type_parameter_constraints_clause* record_body
    ;
```

## <a name="allow-user-defined-positional-members-to-be-fields"></a><span data-ttu-id="c0aff-203">允许用户定义的位置成员作为字段</span><span class="sxs-lookup"><span data-stu-id="c0aff-203">Allow user-defined positional members to be fields</span></span>

<span data-ttu-id="c0aff-204">请参见https://github.com/dotnet/csharplang/blob/master/meetings/2020/LDM-2020-10-05.md#changing-the-member-type-of-a-primary-constructor-parameter</span><span class="sxs-lookup"><span data-stu-id="c0aff-204">See https://github.com/dotnet/csharplang/blob/master/meetings/2020/LDM-2020-10-05.md#changing-the-member-type-of-a-primary-constructor-parameter</span></span>

<span data-ttu-id="c0aff-205">存在一种后端兼容性问题，因此，我们可能会删除此功能，或者我们需要尽快实现它 (作为 bug 修复) 。</span><span class="sxs-lookup"><span data-stu-id="c0aff-205">There is a back compat issue, so we may either drop this feature or we need to implement it fast (as a bug fix).</span></span>

```csharp
public record Base
{
    public int Field;
}
public record Derived(int Field);
```

# <a name="open-questions"></a><span data-ttu-id="c0aff-206">打开问题</span><span class="sxs-lookup"><span data-stu-id="c0aff-206">Open questions</span></span>

- <span data-ttu-id="c0aff-207">是否应禁止使用复制构造函数签名的用户定义的构造函数？</span><span class="sxs-lookup"><span data-stu-id="c0aff-207">should we disallow a user-defined constructor with a copy constructor signature?</span></span>
- <span data-ttu-id="c0aff-208">确认是否要将 PrintMembers 设计保留 (单独方法返回 `bool`) </span><span class="sxs-lookup"><span data-stu-id="c0aff-208">confirm that we want to keep PrintMembers design (separate method returning `bool`)</span></span>
- <span data-ttu-id="c0aff-209">确认是否要禁止名为 "Clone" 的成员。</span><span class="sxs-lookup"><span data-stu-id="c0aff-209">confirm that we want to disallow members named "Clone".</span></span>
- <span data-ttu-id="c0aff-210">为什么在记录的实例字段中不允许安全类型？</span><span class="sxs-lookup"><span data-stu-id="c0aff-210">why did we disallow unsafe types in instance fields in records?</span></span> <span data-ttu-id="c0aff-211">我假设我们还希望在记录结构中禁止。</span><span class="sxs-lookup"><span data-stu-id="c0aff-211">I assume we also want to disallow in record structs.</span></span>
- <span data-ttu-id="c0aff-212">`with` 在泛型上？</span><span class="sxs-lookup"><span data-stu-id="c0aff-212">`with` on generics?</span></span> <span data-ttu-id="c0aff-213"> (可能会影响记录结构的设计) </span><span class="sxs-lookup"><span data-stu-id="c0aff-213">(may affect the design for record structs)</span></span>
- <span data-ttu-id="c0aff-214">确认不会允许 `record ref struct` (`IEquatable<RefStruct>` 和引用字段的问题) </span><span class="sxs-lookup"><span data-stu-id="c0aff-214">confirm we won't allow `record ref struct` (issue with `IEquatable<RefStruct>` and ref fields)</span></span>
- <span data-ttu-id="c0aff-215">确认相等性成员的实现。</span><span class="sxs-lookup"><span data-stu-id="c0aff-215">confirm implementation of equality members.</span></span> <span data-ttu-id="c0aff-216">替代方法是合成 `bool Equals(R other)` `bool Equals(object? other)` 和运算符，只委托给 `ValueType.Equals` 。</span><span class="sxs-lookup"><span data-stu-id="c0aff-216">Alternative is that synthesized `bool Equals(R other)`, `bool Equals(object? other)` and operators all just delegate to `ValueType.Equals`.</span></span>
- <span data-ttu-id="c0aff-217">确认是否要在存在主构造函数时允许字段初始值设定项。</span><span class="sxs-lookup"><span data-stu-id="c0aff-217">confirm that we want to allow field initializers when there is a primary constructor.</span></span> <span data-ttu-id="c0aff-218">我们还想要在) 时，允许无参数结构构造函数 (的激活器问题已明确固定？</span><span class="sxs-lookup"><span data-stu-id="c0aff-218">Do we also want to allow parameterless struct constructors while we're at it (the Activator issue was apparently fixed)?</span></span>
- <span data-ttu-id="c0aff-219">我们想要对方法说多少 `Combine` ？</span><span class="sxs-lookup"><span data-stu-id="c0aff-219">how much do we want to say about `Combine` method?</span></span>


---
ms.openlocfilehash: 35f6836e20776450ce5f776e7fdb66ca634d04a0
ms.sourcegitcommit: 0f56445e250ddf82b88848b94c59870f13ab8ffc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/10/2020
ms.locfileid: "84663262"
---

# <a name="records"></a><span data-ttu-id="61258-101">记录</span><span class="sxs-lookup"><span data-stu-id="61258-101">Records</span></span>

<span data-ttu-id="61258-102">本建议将跟踪 c # 9 记录功能的规范（由 c # 语言设计团队同意）。</span><span class="sxs-lookup"><span data-stu-id="61258-102">This proposal tracks the specification for the C# 9 records feature, as agreed to by the C# language design team.</span></span>

<span data-ttu-id="61258-103">记录的语法如下所示：</span><span class="sxs-lookup"><span data-stu-id="61258-103">The syntax for a record is as follows:</span></span>

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

<span data-ttu-id="61258-104">记录类型是引用类型，类似于类声明。</span><span class="sxs-lookup"><span data-stu-id="61258-104">Record types are reference types, similar to a class declaration.</span></span> <span data-ttu-id="61258-105">如果不包含，记录将提供一个错误 `record_base` `argument_list` `record_declaration` `parameter_list` 。</span><span class="sxs-lookup"><span data-stu-id="61258-105">It is an error for a record to provide a `record_base` `argument_list` if the `record_declaration` does not contain a `parameter_list`.</span></span>

## <a name="members-of-a-record-type"></a><span data-ttu-id="61258-106">记录类型的成员</span><span class="sxs-lookup"><span data-stu-id="61258-106">Members of a record type</span></span>

<span data-ttu-id="61258-107">除了记录体中声明的成员，记录类型还具有附加的合成成员。</span><span class="sxs-lookup"><span data-stu-id="61258-107">In addition to the members declared in the record body, a record type has additional synthesized members.</span></span>
<span data-ttu-id="61258-108">如果在记录正文中声明了具有 "匹配" 签名的成员，或者继承了具有 "匹配" 签名的可访问的具体非虚拟成员，则将合成成员。</span><span class="sxs-lookup"><span data-stu-id="61258-108">Members are synthesized unless a member with a "matching" signature is declared in the record body or an accessible concrete non-virtual member with a "matching" signature is inherited.</span></span>
<span data-ttu-id="61258-109">如果两个成员具有相同的签名，或在继承方案中被视为 "隐藏"，则会将其视为匹配项。</span><span class="sxs-lookup"><span data-stu-id="61258-109">Two members are considered matching if they have the same signature or would be considered "hiding" in an inheritance scenario.</span></span>

<span data-ttu-id="61258-110">合成成员如下所示：</span><span class="sxs-lookup"><span data-stu-id="61258-110">The synthesized members are as follows:</span></span>

### <a name="equality-members"></a><span data-ttu-id="61258-111">相等成员</span><span class="sxs-lookup"><span data-stu-id="61258-111">Equality members</span></span>

<span data-ttu-id="61258-112">记录类型生成以下方法的合成实现，其中 `T` 是包含类型：</span><span class="sxs-lookup"><span data-stu-id="61258-112">Record types produce synthesized implementations of the following methods, where `T` is the containing type:</span></span>
```C#
public override int GetHashCode();
public override bool Equals(object other);
public virtual bool Equals(T other);
```
<span data-ttu-id="61258-113">`GetHashCode()`和 `Equals(object other)` 是中的虚方法的重写 `System.Object` 。</span><span class="sxs-lookup"><span data-stu-id="61258-113">`GetHashCode()` and `Equals(object other)` are overrides of the virtual methods in `System.Object`.</span></span>
<span data-ttu-id="61258-114">重写时，将忽略中间基类上隐藏这些方法的任何方法。</span><span class="sxs-lookup"><span data-stu-id="61258-114">Any methods on intermediate base classes that would hide those methods are ignored when overriding.</span></span>

<span data-ttu-id="61258-115">派生记录类型还会重写 `Equals(TBase other)` 每个基本记录类型中的方法。</span><span class="sxs-lookup"><span data-stu-id="61258-115">Derived record types also override the `Equals(TBase other)` method from each base record type.</span></span>

<span data-ttu-id="61258-116">记录类型合成 `System.IEquatable<T>` 由隐式实现的实现， `Equals(T other)` 其中 `T` 是包含类型。</span><span class="sxs-lookup"><span data-stu-id="61258-116">The record type synthesizes an implementation of `System.IEquatable<T>` that is implicitly implemented by `Equals(T other)` where `T` is the containing type.</span></span>
<span data-ttu-id="61258-117">记录类型不会合成 `System.IEquatable<TBase>` 任何基类型的实现 `TBase` ，即使这些接口是由基本记录类型实现的。</span><span class="sxs-lookup"><span data-stu-id="61258-117">Record types do not synthesize implementations of `System.IEquatable<TBase>` for any base type `TBase`, even if those interfaces are implemented by the base record types.</span></span>

<span data-ttu-id="61258-118">基本记录类合成 `EqualityContract` 属性。</span><span class="sxs-lookup"><span data-stu-id="61258-118">The base record class synthesizes an `EqualityContract` property.</span></span> <span data-ttu-id="61258-119">属性在派生的记录类中被重写。</span><span class="sxs-lookup"><span data-stu-id="61258-119">The property is overridden in derived record classes.</span></span> <span data-ttu-id="61258-120">合成实现返回 `typeof(T)` ，其中 `T` 包含类型。</span><span class="sxs-lookup"><span data-stu-id="61258-120">The synthesized implementations return `typeof(T)` where `T` is containing type.</span></span>
```C#
protected virtual Type EqualityContract { get; }
```

<span data-ttu-id="61258-121">如果任何重写的成员的基实现是密封的或非虚拟的，或者不符合预期的签名和可访问性，则是错误的。</span><span class="sxs-lookup"><span data-stu-id="61258-121">It is an error if the base implementations of any of the overridden members is sealed or non-virtual, or do not match the expected signature and accessibility.</span></span>

<span data-ttu-id="61258-122">`Equals(T other)`当且仅当以下每个条件都为 true 时，返回 true：</span><span class="sxs-lookup"><span data-stu-id="61258-122">`Equals(T other)` returns true if and only if each of the following terms are true:</span></span>
- <span data-ttu-id="61258-123">`other`不是 `null` ，并且</span><span class="sxs-lookup"><span data-stu-id="61258-123">`other` is not `null`, and</span></span>
- <span data-ttu-id="61258-124">对于在记录类型中声明的每个字段，的 `System.Collections.Generic.EqualityComparer<TN>.Default.Equals(fieldN, other.fieldN)` 值 `TN` 为，其中为字段类型，</span><span class="sxs-lookup"><span data-stu-id="61258-124">For each field declared in the record type, the value of `System.Collections.Generic.EqualityComparer<TN>.Default.Equals(fieldN, other.fieldN)` where `TN` is the field type, and</span></span>
- <span data-ttu-id="61258-125">如果有基本记录类型，则的值 `base.Equals(other)` 为; 否则为的值 `EqualityContract.Equals(other.EqualityContract)` 。</span><span class="sxs-lookup"><span data-stu-id="61258-125">If there is a base record type, the value of `base.Equals(other)`; otherwise the value of `EqualityContract.Equals(other.EqualityContract)`.</span></span>

<span data-ttu-id="61258-126">`Equals(T other)`基本方法（包括）的的重写 `object.Equals(object other)` 执行的等效操作：</span><span class="sxs-lookup"><span data-stu-id="61258-126">The overrides of `Equals(T other)` for the base methods, including `object.Equals(object other)`, perform the equivalent of:</span></span>
```C#
public override bool Equals(object other) => Equals(other as T);
```

<span data-ttu-id="61258-127">`GetHashCode()`返回 `int` 采用以下值的确定性函数的结果：</span><span class="sxs-lookup"><span data-stu-id="61258-127">`GetHashCode()` returns the `int` result of a deterministic function taking the following values:</span></span>
- <span data-ttu-id="61258-128">对于在记录类型中声明的每个字段，的 `System.Collections.Generic.EqualityComparer<TN>.Default.GetHashCode(fieldN)` 值 `TN` 为，其中为字段类型，</span><span class="sxs-lookup"><span data-stu-id="61258-128">For each field declared in the record type, the value of `System.Collections.Generic.EqualityComparer<TN>.Default.GetHashCode(fieldN)` where `TN` is the field type, and</span></span>
- <span data-ttu-id="61258-129">如果有基本记录类型，则的值 `base.GetHashCode()` 为; 否则为的值 `System.Collections.Generic.EqualityComparer<System.Type>.Default.GetHashCode(EqualityContract)` 。</span><span class="sxs-lookup"><span data-stu-id="61258-129">If there is a base record type, the value of `base.GetHashCode()`; otherwise the value of `System.Collections.Generic.EqualityComparer<System.Type>.Default.GetHashCode(EqualityContract)`.</span></span>

### <a name="copy-and-clone-members"></a><span data-ttu-id="61258-130">复制和克隆成员</span><span class="sxs-lookup"><span data-stu-id="61258-130">Copy and Clone members</span></span>

<span data-ttu-id="61258-131">记录类型包含两个复制成员：</span><span class="sxs-lookup"><span data-stu-id="61258-131">A record type contains two copying members:</span></span>

* <span data-ttu-id="61258-132">采用记录类型的单个自变量的构造函数。</span><span class="sxs-lookup"><span data-stu-id="61258-132">A constructor taking a single argument of the record type.</span></span> <span data-ttu-id="61258-133">它被称为 "复制构造函数"。</span><span class="sxs-lookup"><span data-stu-id="61258-133">It is referred to as a "copy constructor".</span></span>
* <span data-ttu-id="61258-134">使用编译器保留名称的合成公共无参数虚拟实例 "clone" 方法</span><span class="sxs-lookup"><span data-stu-id="61258-134">A synthesized public parameterless virtual instance "clone" method with a compiler-reserved name</span></span>

<span data-ttu-id="61258-135">复制构造函数的目的是将状态从参数复制到正在创建的新实例。</span><span class="sxs-lookup"><span data-stu-id="61258-135">The purpose of the copy constructor is to copy the state from the parameter to the new instance being created.</span></span> <span data-ttu-id="61258-136">此构造函数不会运行记录声明中存在的任何实例字段/属性初始值设定项。</span><span class="sxs-lookup"><span data-stu-id="61258-136">This constructor doesn't run any instance field/property initializers present in the record declaration.</span></span> <span data-ttu-id="61258-137">如果未显式声明构造函数，则编译器将合成受保护的构造函数。</span><span class="sxs-lookup"><span data-stu-id="61258-137">If the constructor is not explicitly declared, a protected constructor will be synthesized by the compiler.</span></span>
<span data-ttu-id="61258-138">构造函数必须执行的第一件事是调用基的复制构造函数，如果记录继承自 object，则调用无参数的对象构造函数。</span><span class="sxs-lookup"><span data-stu-id="61258-138">The first thing the constructor must do, is to call a copy constructor of the base, or a parameter-less object constructor if the record inherits from object.</span></span> <span data-ttu-id="61258-139">如果用户定义的复制构造函数使用不满足此要求的隐式或显式构造函数初始值设定项，则会报告错误。</span><span class="sxs-lookup"><span data-stu-id="61258-139">An error is reported if a user-defined copy constructor uses an implicit or explicit constructor initializer that doesn't fulfill this requirement.</span></span>
<span data-ttu-id="61258-140">在调用基复制构造函数后，合成复制构造函数将在记录类型内隐式或显式声明的所有实例字段的值。</span><span class="sxs-lookup"><span data-stu-id="61258-140">After a base copy constructor is invoked, a synthesized copy constructor copies values for all instance fields implicitly or explicitly declared within the record type.</span></span>

<span data-ttu-id="61258-141">"Clone" 方法返回对构造函数的调用结果，该构造函数与复制构造函数具有相同的签名。</span><span class="sxs-lookup"><span data-stu-id="61258-141">The "clone" method returns the result of a call to a constructor with the same signature as the copy constructor.</span></span> <span data-ttu-id="61258-142">Clone 方法的返回类型为包含类型，除非基类中存在虚拟克隆方法。</span><span class="sxs-lookup"><span data-stu-id="61258-142">The return type of the clone method is the containing type, unless a virtual clone method is present in the base class.</span></span> <span data-ttu-id="61258-143">在这种情况下，如果支持 "协变返回" 功能和重写返回类型，则返回类型为当前的包含类型。</span><span class="sxs-lookup"><span data-stu-id="61258-143">In that case, the return type is the current containing type if the "covariant returns" feature is supported and the override return type otherwise.</span></span> <span data-ttu-id="61258-144">合成克隆方法是基类型克隆方法（如果存在）的重写。</span><span class="sxs-lookup"><span data-stu-id="61258-144">The synthesized clone method is an override of the base type clone method if one exists.</span></span> <span data-ttu-id="61258-145">如果基类型克隆方法是密封的，则会生成错误。</span><span class="sxs-lookup"><span data-stu-id="61258-145">An error is produced if the base type clone method is sealed.</span></span>

<span data-ttu-id="61258-146">如果包含的记录是抽象的，合成克隆方法也是抽象的。</span><span class="sxs-lookup"><span data-stu-id="61258-146">If the containing record is abstract, the synthesized clone method is also abstract.</span></span>

## <a name="positional-record-members"></a><span data-ttu-id="61258-147">位置记录成员</span><span class="sxs-lookup"><span data-stu-id="61258-147">Positional record members</span></span>

<span data-ttu-id="61258-148">除了以上成员以外，具有参数列表的记录（"位置记录"）合成其他成员与上述成员具有相同的条件。</span><span class="sxs-lookup"><span data-stu-id="61258-148">In addition to the above members, records with a parameter list ("positional records") synthesize additional members with the same conditions as the members above.</span></span>

### <a name="primary-constructor"></a><span data-ttu-id="61258-149">主构造函数</span><span class="sxs-lookup"><span data-stu-id="61258-149">Primary Constructor</span></span>

<span data-ttu-id="61258-150">记录类型具有公共构造函数，该构造函数的签名对应于类型声明的值参数。</span><span class="sxs-lookup"><span data-stu-id="61258-150">A record type has a public constructor whose signature corresponds to the value parameters of the type declaration.</span></span> <span data-ttu-id="61258-151">这称为类型的主构造函数，并导致禁止显示隐式声明的默认类构造函数（如果存在）。</span><span class="sxs-lookup"><span data-stu-id="61258-151">This is called the primary constructor for the type, and causes the implicitly declared default class constructor, if present, to be suppressed.</span></span> <span data-ttu-id="61258-152">类中已存在具有相同签名的主构造函数和构造函数是错误的。</span><span class="sxs-lookup"><span data-stu-id="61258-152">It is an error to have a primary constructor and a constructor with the same signature already present in the class.</span></span>

<span data-ttu-id="61258-153">在运行时，主构造函数</span><span class="sxs-lookup"><span data-stu-id="61258-153">At runtime the primary constructor</span></span>

1. <span data-ttu-id="61258-154">执行出现在类体中的实例初始值设定项</span><span class="sxs-lookup"><span data-stu-id="61258-154">executes the instance initializers appearing in the class-body</span></span>

1. <span data-ttu-id="61258-155">用子句中提供的参数 `record_base` （如果存在）调用基类构造函数</span><span class="sxs-lookup"><span data-stu-id="61258-155">invokes the base class constructor with the arguments provided in the `record_base` clause, if present</span></span>

<span data-ttu-id="61258-156">如果记录具有主构造函数，则任何用户定义的构造函数（"复制构造函数" 除外）都必须具有显式 `this` 构造函数初始值设定项。</span><span class="sxs-lookup"><span data-stu-id="61258-156">If a record has a primary constructor, any user-defined constructor, except "copy constructor" must have an explicit `this` constructor initializer.</span></span> 

<span data-ttu-id="61258-157">主构造函数的参数以及记录的成员位于 `argument_list` `record_base` 子句的和实例字段或属性的初始值设定项中的范围内。</span><span class="sxs-lookup"><span data-stu-id="61258-157">Parameters of the primary constructor as well as members of the record are in scope within the `argument_list` of the `record_base` clause and within initializers of instance fields or properties.</span></span> <span data-ttu-id="61258-158">实例成员将是这些位置中的错误（类似于当前在常规构造函数初始值设定项的范围内的方式，但使用的是错误），但主构造函数的参数在范围内并且可用，并将隐藏成员。</span><span class="sxs-lookup"><span data-stu-id="61258-158">Instance members would be an error in these locations (similar to how instance members are in scope in regular constructor initializers today, but an error to use), but the parameters of the primary constructor would be in scope and useable and would shadow members.</span></span> <span data-ttu-id="61258-159">静态成员还可以使用，类似于目前普通构造函数中的基调用和初始值设定项的工作方式。</span><span class="sxs-lookup"><span data-stu-id="61258-159">Static members would also be useable, similar to how base calls and initializers work in ordinary constructors today.</span></span> 

<span data-ttu-id="61258-160">在中声明的表达式变量 `argument_list` 在范围内 `argument_list` 。</span><span class="sxs-lookup"><span data-stu-id="61258-160">Expression variables declared in the `argument_list` are in scope within the `argument_list`.</span></span> <span data-ttu-id="61258-161">与规则构造函数初始值设定项的参数列表中相同的隐藏规则也适用。</span><span class="sxs-lookup"><span data-stu-id="61258-161">The same shadowing rules as within an argument list of a regular constructor initializer apply.</span></span>

### <a name="properties"></a><span data-ttu-id="61258-162">属性</span><span class="sxs-lookup"><span data-stu-id="61258-162">Properties</span></span>

<span data-ttu-id="61258-163">对于记录类型声明的每个记录参数，都有一个对应的公共属性成员，其名称和类型取自值参数声明。</span><span class="sxs-lookup"><span data-stu-id="61258-163">For each record parameter of a record type declaration there is a corresponding public property member whose name and type are taken from the value parameter declaration.</span></span>

<span data-ttu-id="61258-164">对于 a 记录：</span><span class="sxs-lookup"><span data-stu-id="61258-164">For a record:</span></span>

* <span data-ttu-id="61258-165">创建公共 `get` 和 `init` 自动属性（请参阅单独的 `init` 访问器规范）。</span><span class="sxs-lookup"><span data-stu-id="61258-165">A public `get` and `init` auto-property is created (see separate `init` accessor specification).</span></span>
  <span data-ttu-id="61258-166">将重写每个 "匹配" 继承抽象访问器。</span><span class="sxs-lookup"><span data-stu-id="61258-166">Each "matching" inherited abstract accessor is overridden.</span></span> <span data-ttu-id="61258-167">自动属性初始化为相应主构造函数参数的值。</span><span class="sxs-lookup"><span data-stu-id="61258-167">The auto-property is initialized to the value of the corresponding primary constructor parameter.</span></span>

### <a name="deconstruct"></a><span data-ttu-id="61258-168">析构</span><span class="sxs-lookup"><span data-stu-id="61258-168">Deconstruct</span></span>

<span data-ttu-id="61258-169">位置记录合成名为析构的公共 void 返回方法，其主构造函数声明的每个参数都有 out 参数声明。</span><span class="sxs-lookup"><span data-stu-id="61258-169">A positional record synthesizes a public void-returning method called Deconstruct with an out parameter declaration for each parameter of the primary constructor declaration.</span></span> <span data-ttu-id="61258-170">析构方法的每个参数都具有与主构造函数声明的相应参数相同的类型。</span><span class="sxs-lookup"><span data-stu-id="61258-170">Each parameter of the Deconstruct method has the same type as the corresponding parameter of the primary constructor declaration.</span></span> <span data-ttu-id="61258-171">方法的主体将析构方法的每个参数分配给一个实例成员访问同名的成员的值。</span><span class="sxs-lookup"><span data-stu-id="61258-171">The body of the method assigns each parameter of the Deconstruct method to the value from an instance member access to a member of the same name.</span></span>

## <a name="with-expression"></a><span data-ttu-id="61258-172">`with` 表达式</span><span class="sxs-lookup"><span data-stu-id="61258-172">`with` expression</span></span>

<span data-ttu-id="61258-173">`with`表达式是使用以下语法的新表达式。</span><span class="sxs-lookup"><span data-stu-id="61258-173">A `with` expression is a new expression using the following syntax.</span></span>

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

<span data-ttu-id="61258-174">`with`表达式允许 "非破坏性转变"，旨在生成接收方表达式的副本，并在中对赋值进行修改 `member_initializer_list` 。</span><span class="sxs-lookup"><span data-stu-id="61258-174">A `with` expression allows for "non-destructive mutation", designed to produce a copy of the receiver expression with modifications in assignments in the `member_initializer_list`.</span></span>

<span data-ttu-id="61258-175">有效的 `with` 表达式包含具有非 void 类型的接收方。</span><span class="sxs-lookup"><span data-stu-id="61258-175">A valid `with` expression has a receiver with a non-void type.</span></span> <span data-ttu-id="61258-176">接收器类型必须包含可访问的合成记录 "clone" 方法。</span><span class="sxs-lookup"><span data-stu-id="61258-176">The receiver type must contain an accessible synthesized record "clone" method.</span></span>

<span data-ttu-id="61258-177">表达式的右侧 `with` 是一个 `member_initializer_list` 带有*标识符*的赋值序列的，它必须是该方法的返回类型的可访问实例字段或属性 `Clone()` 。</span><span class="sxs-lookup"><span data-stu-id="61258-177">On the right hand side of the `with` expression is a `member_initializer_list` with a sequence of assignments to *identifier*, which must be an accessible instance field or property of the return type of the `Clone()` method.</span></span>

<span data-ttu-id="61258-178">每个的 `member_initializer` 处理方式与对记录克隆方法返回值的字段或属性访问的赋值相同。</span><span class="sxs-lookup"><span data-stu-id="61258-178">Each `member_initializer` is processed the same way as an assignment to a field or property access of the return value of the record clone method.</span></span> <span data-ttu-id="61258-179">Clone 方法只执行一次，并按词法顺序处理赋值。</span><span class="sxs-lookup"><span data-stu-id="61258-179">The clone method is executed only once and the assignments are processed in lexical order.</span></span>

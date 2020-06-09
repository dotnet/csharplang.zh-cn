---
ms.openlocfilehash: ffa34ad55752197bc2d8fb6cac7759602a2672c9
ms.sourcegitcommit: 5c9b8f27bd8299c70e2f4205a46079a10cffce76
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/08/2020
ms.locfileid: "84533341"
---

# <a name="records"></a><span data-ttu-id="39955-101">记录</span><span class="sxs-lookup"><span data-stu-id="39955-101">Records</span></span>

<span data-ttu-id="39955-102">本建议将跟踪 c # 9 记录功能的规范（由 c # 语言设计团队同意）。</span><span class="sxs-lookup"><span data-stu-id="39955-102">This proposal tracks the specification for the C# 9 records feature, as agreed to by the C# language design team.</span></span>

<span data-ttu-id="39955-103">记录的语法如下所示：</span><span class="sxs-lookup"><span data-stu-id="39955-103">The syntax for a record is as follows:</span></span>

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

<span data-ttu-id="39955-104">记录类型是引用类型，类似于类声明。</span><span class="sxs-lookup"><span data-stu-id="39955-104">Record types are reference types, similar to a class declaration.</span></span> <span data-ttu-id="39955-105">如果不包含，记录将提供一个错误 `record_base` `argument_list` `record_declaration` `parameter_list` 。</span><span class="sxs-lookup"><span data-stu-id="39955-105">It is an error for a record to provide a `record_base` `argument_list` if the `record_declaration` does not contain a `parameter_list`.</span></span>

## <a name="members-of-a-record-type"></a><span data-ttu-id="39955-106">记录类型的成员</span><span class="sxs-lookup"><span data-stu-id="39955-106">Members of a record type</span></span>

<span data-ttu-id="39955-107">除了记录体中声明的成员，记录类型还具有附加的合成成员。</span><span class="sxs-lookup"><span data-stu-id="39955-107">In addition to the members declared in the record body, a record type has additional synthesized members.</span></span>
<span data-ttu-id="39955-108">除非在记录正文中继承或声明了具有 "匹配" 签名的可访问的具体（非抽象）成员，否则将合成成员。</span><span class="sxs-lookup"><span data-stu-id="39955-108">Members are synthesized unless an accessible concrete (non-abstract) member with a "matching" signature is either inherited or declared in the record body.</span></span> <span data-ttu-id="39955-109">如果两个成员具有相同的签名，或在继承方案中被视为 "隐藏"，则会将其视为匹配项。</span><span class="sxs-lookup"><span data-stu-id="39955-109">Two members are considered matching if they have the same signature or would be considered "hiding" in an inheritance scenario.</span></span>

<span data-ttu-id="39955-110">合成成员如下所示：</span><span class="sxs-lookup"><span data-stu-id="39955-110">The synthesized members are as follows:</span></span>

### <a name="equality-members"></a><span data-ttu-id="39955-111">相等成员</span><span class="sxs-lookup"><span data-stu-id="39955-111">Equality members</span></span>

<span data-ttu-id="39955-112">记录类型为以下方法生成合成实现，其中 `T` 是包含类型：</span><span class="sxs-lookup"><span data-stu-id="39955-112">Record types produce synthesized implementations for the following methods, where `T` is the containing type:</span></span>

* <span data-ttu-id="39955-113">`object.GetHashCode()`忽略</span><span class="sxs-lookup"><span data-stu-id="39955-113">`object.GetHashCode()` override</span></span>
* <span data-ttu-id="39955-114">`object.Equals(object)`忽略</span><span class="sxs-lookup"><span data-stu-id="39955-114">`object.Equals(object)` override</span></span>
* <span data-ttu-id="39955-115">`T Equals(T)`方法，其中 `T` 是当前类型</span><span class="sxs-lookup"><span data-stu-id="39955-115">`T Equals(T)` method, where `T` is the current type</span></span>
* <span data-ttu-id="39955-116">`Type EqualityContract`获取属性</span><span class="sxs-lookup"><span data-stu-id="39955-116">`Type EqualityContract` get-only property</span></span>

<span data-ttu-id="39955-117">如果 `object.GetHashCode()` 或 `object.Equals(object)` 是密封的，则会生成错误。</span><span class="sxs-lookup"><span data-stu-id="39955-117">If either `object.GetHashCode()` or `object.Equals(object)` are sealed, an error is produced.</span></span>

<span data-ttu-id="39955-118">`EqualityContract`是返回的虚拟实例属性 `typeof(T)` 。</span><span class="sxs-lookup"><span data-stu-id="39955-118">`EqualityContract` is a virtual instance property which returns `typeof(T)`.</span></span> <span data-ttu-id="39955-119">如果基类型定义，则 `EqualityContract` 在派生的记录中重写它。</span><span class="sxs-lookup"><span data-stu-id="39955-119">If the base type defines an `EqualityContract` it is overridden in the derived record.</span></span> <span data-ttu-id="39955-120">如果基 `EqualityContract` 是密封或非虚的，则会生成错误。</span><span class="sxs-lookup"><span data-stu-id="39955-120">If the base `EqualityContract` is sealed or non-virtual, an error is produced.</span></span>

<span data-ttu-id="39955-121">`T Equals(T)`指定为执行值相等性，这样， `Equals` 当且仅当接收方中所有可访问的实例字段都等于参数的字段并等于时，此值才为 true `this.EqualityContract` `other.EqualityContract` 。</span><span class="sxs-lookup"><span data-stu-id="39955-121">`T Equals(T)` is specified to perform value equality such that `Equals` is true if and only if all accessible instance fields in the receiver are equal to the fields of the parameter and `this.EqualityContract` equals `other.EqualityContract`.</span></span>

<span data-ttu-id="39955-122">`object.Equals`执行等效于</span><span class="sxs-lookup"><span data-stu-id="39955-122">`object.Equals` performs the equivalent of</span></span>

```C#
override Equals(object o) => Equals(o as T);
```

### <a name="copy-and-clone-members"></a><span data-ttu-id="39955-123">复制和克隆成员</span><span class="sxs-lookup"><span data-stu-id="39955-123">Copy and Clone members</span></span>

<span data-ttu-id="39955-124">记录类型包含两个合成复制成员：</span><span class="sxs-lookup"><span data-stu-id="39955-124">A record type contains two synthesized copying members:</span></span>

* <span data-ttu-id="39955-125">一个受保护的构造函数，采用记录类型的单个自变量。</span><span class="sxs-lookup"><span data-stu-id="39955-125">A protected constructor taking a single argument of the record type.</span></span>
* <span data-ttu-id="39955-126">具有编译器保留名称的公共无参数虚拟实例 "clone" 方法</span><span class="sxs-lookup"><span data-stu-id="39955-126">A public parameterless virtual instance "clone" method with a compiler-reserved name</span></span>

<span data-ttu-id="39955-127">受保护的构造函数称为 "复制构造函数"，合成体将输入类型中所有可访问的实例字段的值复制到对应的字段 `this` 。</span><span class="sxs-lookup"><span data-stu-id="39955-127">The protected constructor is referred to as the "copy constructor" and the synthesized body copies the values of all accessible instance fields in the input type to the corresponding fields of `this`.</span></span>

<span data-ttu-id="39955-128">"Clone" 方法返回对构造函数的调用结果，该构造函数与复制构造函数具有相同的签名。</span><span class="sxs-lookup"><span data-stu-id="39955-128">The "clone" method returns the result of a call to a constructor with the same signature as the copy constructor.</span></span> <span data-ttu-id="39955-129">Clone 方法的返回类型为包含类型，除非基类中存在虚拟克隆方法。</span><span class="sxs-lookup"><span data-stu-id="39955-129">The return type of the clone method is the containing type, unless a virtual clone method is present in the base class.</span></span> <span data-ttu-id="39955-130">在这种情况下，如果支持 "协变返回" 功能和重写返回类型，则返回类型为当前的包含类型。</span><span class="sxs-lookup"><span data-stu-id="39955-130">In that case, the return type is the current containing type if the "covariant returns" feature is supported and the override return type otherwise.</span></span> <span data-ttu-id="39955-131">合成克隆方法是基类型克隆方法（如果存在）的重写。</span><span class="sxs-lookup"><span data-stu-id="39955-131">The synthesized clone method is an override of the base type clone method if one exists.</span></span> <span data-ttu-id="39955-132">如果基类型克隆方法是密封的，则会生成错误。</span><span class="sxs-lookup"><span data-stu-id="39955-132">An error is produced if the base type clone method is sealed.</span></span>

<span data-ttu-id="39955-133">如果包含的记录是抽象的，合成克隆方法也是抽象的。</span><span class="sxs-lookup"><span data-stu-id="39955-133">If the containing record is abstract, the synthesized clone method is also abstract.</span></span>

## <a name="positional-record-members"></a><span data-ttu-id="39955-134">位置记录成员</span><span class="sxs-lookup"><span data-stu-id="39955-134">Positional record members</span></span>

<span data-ttu-id="39955-135">除了以上成员以外，具有参数列表的记录（"位置记录"）合成其他成员与上述成员具有相同的条件。</span><span class="sxs-lookup"><span data-stu-id="39955-135">In addition to the above members, records with a parameter list ("positional records") synthesize additional members with the same conditions as the members above.</span></span>

### <a name="primary-constructor"></a><span data-ttu-id="39955-136">主构造函数</span><span class="sxs-lookup"><span data-stu-id="39955-136">Primary Constructor</span></span>

<span data-ttu-id="39955-137">记录类型具有公共构造函数，该构造函数的签名对应于类型声明的值参数。</span><span class="sxs-lookup"><span data-stu-id="39955-137">A record type has a public constructor whose signature corresponds to the value parameters of the type declaration.</span></span> <span data-ttu-id="39955-138">这称为类型的主构造函数，并导致禁止显示隐式声明的默认类构造函数（如果存在）。</span><span class="sxs-lookup"><span data-stu-id="39955-138">This is called the primary constructor for the type, and causes the implicitly declared default class constructor, if present, to be suppressed.</span></span> <span data-ttu-id="39955-139">类中已存在具有相同签名的主构造函数和构造函数是错误的。</span><span class="sxs-lookup"><span data-stu-id="39955-139">It is an error to have a primary constructor and a constructor with the same signature already present in the class.</span></span>

<span data-ttu-id="39955-140">在运行时，主构造函数</span><span class="sxs-lookup"><span data-stu-id="39955-140">At runtime the primary constructor</span></span>

1. <span data-ttu-id="39955-141">执行出现在类体中的实例初始值设定项</span><span class="sxs-lookup"><span data-stu-id="39955-141">executes the instance initializers appearing in the class-body</span></span>

1. <span data-ttu-id="39955-142">用子句中提供的参数 `record_base` （如果存在）调用基类构造函数</span><span class="sxs-lookup"><span data-stu-id="39955-142">invokes the base class constructor with the arguments provided in the `record_base` clause, if present</span></span>


### <a name="properties"></a><span data-ttu-id="39955-143">属性</span><span class="sxs-lookup"><span data-stu-id="39955-143">Properties</span></span>

<span data-ttu-id="39955-144">对于记录类型声明的每个记录参数，都有一个对应的公共属性成员，其名称和类型取自值参数声明。</span><span class="sxs-lookup"><span data-stu-id="39955-144">For each record parameter of a record type declaration there is a corresponding public property member whose name and type are taken from the value parameter declaration.</span></span>

<span data-ttu-id="39955-145">对于 a 记录：</span><span class="sxs-lookup"><span data-stu-id="39955-145">For a record:</span></span>

* <span data-ttu-id="39955-146">创建公共 `get` 和 `init` 自动属性（请参阅单独的 `init` 访问器规范）。</span><span class="sxs-lookup"><span data-stu-id="39955-146">A public `get` and `init` auto-property is created (see separate `init` accessor specification).</span></span>
  <span data-ttu-id="39955-147">将重写每个 "匹配" 继承抽象访问器。</span><span class="sxs-lookup"><span data-stu-id="39955-147">Each "matching" inherited abstract accessor is overridden.</span></span> <span data-ttu-id="39955-148">自动属性初始化为相应主构造函数参数的值。</span><span class="sxs-lookup"><span data-stu-id="39955-148">The auto-property is initialized to the value of the corresponding primary constructor parameter.</span></span>

### <a name="deconstruct"></a><span data-ttu-id="39955-149">析构</span><span class="sxs-lookup"><span data-stu-id="39955-149">Deconstruct</span></span>

<span data-ttu-id="39955-150">位置记录合成名为析构的公共 void 返回方法，其主构造函数声明的每个参数都有 out 参数声明。</span><span class="sxs-lookup"><span data-stu-id="39955-150">A positional record synthesizes a public void-returning method called Deconstruct with an out parameter declaration for each parameter of the primary constructor declaration.</span></span> <span data-ttu-id="39955-151">析构方法的每个参数都具有与主构造函数声明的相应参数相同的类型。</span><span class="sxs-lookup"><span data-stu-id="39955-151">Each parameter of the Deconstruct method has the same type as the corresponding parameter of the primary constructor declaration.</span></span> <span data-ttu-id="39955-152">方法的主体将析构方法的每个参数分配给一个实例成员访问同名的成员的值。</span><span class="sxs-lookup"><span data-stu-id="39955-152">The body of the method assigns each parameter of the Deconstruct method to the value from an instance member access to a member of the same name.</span></span>

## <a name="with-expression"></a><span data-ttu-id="39955-153">`with` 表达式</span><span class="sxs-lookup"><span data-stu-id="39955-153">`with` expression</span></span>

<span data-ttu-id="39955-154">`with`表达式是使用以下语法的新表达式。</span><span class="sxs-lookup"><span data-stu-id="39955-154">A `with` expression is a new expression using the following syntax.</span></span>

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

<span data-ttu-id="39955-155">`with`表达式允许 "非破坏性转变"，旨在生成接收方表达式的副本，并在中对赋值进行修改 `member_initializer_list` 。</span><span class="sxs-lookup"><span data-stu-id="39955-155">A `with` expression allows for "non-destructive mutation", designed to produce a copy of the receiver expression with modifications in assignments in the `member_initializer_list`.</span></span>

<span data-ttu-id="39955-156">有效的 `with` 表达式包含具有非 void 类型的接收方。</span><span class="sxs-lookup"><span data-stu-id="39955-156">A valid `with` expression has a receiver with a non-void type.</span></span> <span data-ttu-id="39955-157">接收器类型必须包含可访问的合成记录 "clone" 方法。</span><span class="sxs-lookup"><span data-stu-id="39955-157">The receiver type must contain an accessible synthesized record "clone" method.</span></span>

<span data-ttu-id="39955-158">表达式的右侧 `with` 是一个 `member_initializer_list` 带有*标识符*的赋值序列的，它必须是该方法的返回类型的可访问实例字段或属性 `Clone()` 。</span><span class="sxs-lookup"><span data-stu-id="39955-158">On the right hand side of the `with` expression is a `member_initializer_list` with a sequence of assignments to *identifier*, which must be an accessible instance field or property of the return type of the `Clone()` method.</span></span>

<span data-ttu-id="39955-159">每个的 `member_initializer` 处理方式与对记录克隆方法返回值的字段或属性访问的赋值相同。</span><span class="sxs-lookup"><span data-stu-id="39955-159">Each `member_initializer` is processed the same way as an assignment to a field or property access of the return value of the record clone method.</span></span> <span data-ttu-id="39955-160">Clone 方法只执行一次，并按词法顺序处理赋值。</span><span class="sxs-lookup"><span data-stu-id="39955-160">The clone method is executed only once and the assignments are processed in lexical order.</span></span>

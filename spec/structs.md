---
ms.openlocfilehash: 6b35df4e00a00a23221dedca6c0424604971a2f5
ms.sourcegitcommit: f921de697a44d3ae827f168a8371d7dc3ca66f2d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/02/2021
ms.locfileid: "101641981"
---
# <a name="structs"></a><span data-ttu-id="74ab2-101">结构</span><span class="sxs-lookup"><span data-stu-id="74ab2-101">Structs</span></span>

<span data-ttu-id="74ab2-102">结构与类相似，它们表示可以包含数据成员和函数成员的数据结构。</span><span class="sxs-lookup"><span data-stu-id="74ab2-102">Structs are similar to classes in that they represent data structures that can contain data members and function members.</span></span> <span data-ttu-id="74ab2-103">但是，与类不同的是，结构是值类型，不需要进行堆分配。</span><span class="sxs-lookup"><span data-stu-id="74ab2-103">However, unlike classes, structs are value types and do not require heap allocation.</span></span> <span data-ttu-id="74ab2-104">结构类型的变量直接包含结构的数据，而类类型的变量包含对数据的引用，后者称为对象。</span><span class="sxs-lookup"><span data-stu-id="74ab2-104">A variable of a struct type directly contains the data of the struct, whereas a variable of a class type contains a reference to the data, the latter known as an object.</span></span>

<span data-ttu-id="74ab2-105">结构对包含值语义的小型数据结构特别有用。</span><span class="sxs-lookup"><span data-stu-id="74ab2-105">Structs are particularly useful for small data structures that have value semantics.</span></span> <span data-ttu-id="74ab2-106">复数、坐标系中的点或字典中的键值对都是结构的典型示例。</span><span class="sxs-lookup"><span data-stu-id="74ab2-106">Complex numbers, points in a coordinate system, or key-value pairs in a dictionary are all good examples of structs.</span></span> <span data-ttu-id="74ab2-107">这些数据结构的关键在于它们有少量的数据成员，它们不需要使用继承或引用标识，并且可以使用赋值语义方便地实现这些数据结构，赋值将复制值而不是引用。</span><span class="sxs-lookup"><span data-stu-id="74ab2-107">Key to these data structures is that they have few data members, that they do not require use of inheritance or referential identity, and that they can be conveniently implemented using value semantics where assignment copies the value instead of the reference.</span></span>

<span data-ttu-id="74ab2-108">如 [简单类型](types.md#simple-types)中所述，c # 提供的简单类型（如 `int` 、 `double` 和 `bool` ）实际上都是所有结构类型。</span><span class="sxs-lookup"><span data-stu-id="74ab2-108">As described in [Simple types](types.md#simple-types), the simple types provided by C#, such as `int`, `double`, and `bool`, are in fact all struct types.</span></span> <span data-ttu-id="74ab2-109">正如这些预定义类型是结构一样，还可以使用结构和运算符重载来实现 c # 语言的新 "基元" 类型。</span><span class="sxs-lookup"><span data-stu-id="74ab2-109">Just as these predefined types are structs, it is also possible to use structs and operator overloading to implement new "primitive" types in the C# language.</span></span> <span data-ttu-id="74ab2-110">本章末尾提供了此类类型的两个示例 ([结构示例](structs.md#struct-examples)) 。</span><span class="sxs-lookup"><span data-stu-id="74ab2-110">Two examples of such types are given at the end of this chapter ([Struct examples](structs.md#struct-examples)).</span></span>

## <a name="struct-declarations"></a><span data-ttu-id="74ab2-111">结构声明</span><span class="sxs-lookup"><span data-stu-id="74ab2-111">Struct declarations</span></span>

<span data-ttu-id="74ab2-112">*Struct_declaration* 是声明新结构 ([类型声明](namespaces.md#type-declarations)) *type_declaration* ：</span><span class="sxs-lookup"><span data-stu-id="74ab2-112">A *struct_declaration* is a *type_declaration* ([Type declarations](namespaces.md#type-declarations)) that declares a new struct:</span></span>

```antlr
struct_declaration
    : attributes? struct_modifier* 'partial'? 'struct' identifier type_parameter_list?
      struct_interfaces? type_parameter_constraints_clause* struct_body ';'?
    ;
```

<span data-ttu-id="74ab2-113">*Struct_declaration* 包含一组可选的 *特性* ([特性](attributes.md)) ，后跟一组可选的 *struct_modifier* s ([结构修饰符](structs.md#struct-modifiers)) ，后跟一个可选 `partial` 修饰符，后面跟一个关键字 `struct` 和一个命名该结构的 *标识符*，后跟一个可选的 *type_parameter_list* 规范 ([类型参数](classes.md#type-parameters)") ，后跟一个可选的 *struct_interfaces* 规范 ([Partial 修饰符](structs.md#partial-modifier)) ，后跟一个可选的 ) type_parameter_constraints_clause [类型参数约束](classes.md#type-parameter-constraints) (，后跟一个可选的 *)*[结构体](structs.md#struct-body)struct_body，后面可以跟一个分号。</span><span class="sxs-lookup"><span data-stu-id="74ab2-113">A *struct_declaration* consists of an optional set of *attributes* ([Attributes](attributes.md)), followed by an optional set of *struct_modifier* s ([Struct modifiers](structs.md#struct-modifiers)), followed by an optional `partial` modifier, followed by the keyword `struct` and an *identifier* that names the struct, followed by an optional *type_parameter_list* specification ([Type parameters](classes.md#type-parameters)), followed by an optional *struct_interfaces* specification ([Partial modifier](structs.md#partial-modifier)) ), followed by an optional *type_parameter_constraints_clause* s specification ([Type parameter constraints](classes.md#type-parameter-constraints)), followed by a *struct_body* ([Struct body](structs.md#struct-body)), optionally followed by a semicolon.</span></span>

### <a name="struct-modifiers"></a><span data-ttu-id="74ab2-114">结构修饰符</span><span class="sxs-lookup"><span data-stu-id="74ab2-114">Struct modifiers</span></span>

<span data-ttu-id="74ab2-115">*Struct_declaration* 可以选择性地包含一系列结构修饰符：</span><span class="sxs-lookup"><span data-stu-id="74ab2-115">A *struct_declaration* may optionally include a sequence of struct modifiers:</span></span>

```antlr
struct_modifier
    : 'new'
    | 'public'
    | 'protected'
    | 'internal'
    | 'private'
    | struct_modifier_unsafe
    ;
```

<span data-ttu-id="74ab2-116">同一修饰符在结构声明中出现多次是编译时错误。</span><span class="sxs-lookup"><span data-stu-id="74ab2-116">It is a compile-time error for the same modifier to appear multiple times in a struct declaration.</span></span>

<span data-ttu-id="74ab2-117">结构声明的修饰符与类声明 ([类](classes.md#class-declarations) 声明的修饰符具有相同的含义，) 。</span><span class="sxs-lookup"><span data-stu-id="74ab2-117">The modifiers of a struct declaration have the same meaning as those of a class declaration ([Class declarations](classes.md#class-declarations)).</span></span>

### <a name="partial-modifier"></a><span data-ttu-id="74ab2-118">Partial 修饰符</span><span class="sxs-lookup"><span data-stu-id="74ab2-118">Partial modifier</span></span>

<span data-ttu-id="74ab2-119">`partial`修饰符指示此 *struct_declaration* 是分部类型声明。</span><span class="sxs-lookup"><span data-stu-id="74ab2-119">The `partial` modifier indicates that this *struct_declaration* is a partial type declaration.</span></span> <span data-ttu-id="74ab2-120">在封闭命名空间或类型声明中具有相同名称的多个分部结构声明组合在一起，并遵循在 [部分类型](classes.md#partial-types)中指定的规则组成一个结构声明。</span><span class="sxs-lookup"><span data-stu-id="74ab2-120">Multiple partial struct declarations with the same name within an enclosing namespace or type declaration combine to form one struct declaration, following the rules specified in [Partial types](classes.md#partial-types).</span></span>

### <a name="struct-interfaces"></a><span data-ttu-id="74ab2-121">结构接口</span><span class="sxs-lookup"><span data-stu-id="74ab2-121">Struct interfaces</span></span>

<span data-ttu-id="74ab2-122">结构声明可以包含 *struct_interfaces* 规范，在这种情况下，结构被称为直接实现给定的接口类型。</span><span class="sxs-lookup"><span data-stu-id="74ab2-122">A struct declaration may include a *struct_interfaces* specification, in which case the struct is said to directly implement the given interface types.</span></span>

```antlr
struct_interfaces
    : ':' interface_type_list
    ;
```

<span data-ttu-id="74ab2-123">[接口实现中进一步](interfaces.md#interface-implementations)讨论了接口实现。</span><span class="sxs-lookup"><span data-stu-id="74ab2-123">Interface implementations are discussed further in [Interface implementations](interfaces.md#interface-implementations).</span></span>

### <a name="struct-body"></a><span data-ttu-id="74ab2-124">结构体</span><span class="sxs-lookup"><span data-stu-id="74ab2-124">Struct body</span></span>

<span data-ttu-id="74ab2-125">结构的 *struct_body* 定义结构的成员。</span><span class="sxs-lookup"><span data-stu-id="74ab2-125">The *struct_body* of a struct defines the members of the struct.</span></span>

```antlr
struct_body
    : '{' struct_member_declaration* '}'
    ;
```

## <a name="struct-members"></a><span data-ttu-id="74ab2-126">结构成员</span><span class="sxs-lookup"><span data-stu-id="74ab2-126">Struct members</span></span>

<span data-ttu-id="74ab2-127">结构的成员由其 *struct_member_declaration* 引入的成员和从该类型继承的成员组成 `System.ValueType` 。</span><span class="sxs-lookup"><span data-stu-id="74ab2-127">The members of a struct consist of the members introduced by its *struct_member_declaration* s and the members inherited from the type `System.ValueType`.</span></span>

```antlr
struct_member_declaration
    : constant_declaration
    | field_declaration
    | method_declaration
    | property_declaration
    | event_declaration
    | indexer_declaration
    | operator_declaration
    | constructor_declaration
    | static_constructor_declaration
    | type_declaration
    | struct_member_declaration_unsafe
    ;
```

<span data-ttu-id="74ab2-128">除了[类和结构差异](structs.md#class-and-struct-differences)中所述的区别外，通过[异步函数](classes.md#async-functions)在[类成员](classes.md#class-members)中提供的类成员的说明也适用于结构成员。</span><span class="sxs-lookup"><span data-stu-id="74ab2-128">Except for the differences noted in [Class and struct differences](structs.md#class-and-struct-differences), the descriptions of class members provided in [Class members](classes.md#class-members) through [Async functions](classes.md#async-functions) apply to struct members as well.</span></span>

## <a name="class-and-struct-differences"></a><span data-ttu-id="74ab2-129">类和结构的区别</span><span class="sxs-lookup"><span data-stu-id="74ab2-129">Class and struct differences</span></span>

<span data-ttu-id="74ab2-130">结构在几个重要方面不同于类：</span><span class="sxs-lookup"><span data-stu-id="74ab2-130">Structs differ from classes in several important ways:</span></span>

*  <span data-ttu-id="74ab2-131">结构是值类型 () 的值 [语义](structs.md#value-semantics) 。</span><span class="sxs-lookup"><span data-stu-id="74ab2-131">Structs are value types ([Value semantics](structs.md#value-semantics)).</span></span>
*  <span data-ttu-id="74ab2-132">所有结构类型都隐式继承自类， `System.ValueType` ([继承](structs.md#inheritance)) 。</span><span class="sxs-lookup"><span data-stu-id="74ab2-132">All struct types implicitly inherit from the class `System.ValueType` ([Inheritance](structs.md#inheritance)).</span></span>
*  <span data-ttu-id="74ab2-133">对结构类型的变量赋值会创建 ([赋值](structs.md#assignment)) 分配的值的副本。</span><span class="sxs-lookup"><span data-stu-id="74ab2-133">Assignment to a variable of a struct type creates a copy of the value being assigned ([Assignment](structs.md#assignment)).</span></span>
*  <span data-ttu-id="74ab2-134">结构的默认值是通过将所有值类型字段设置为其默认值，并将所有引用类型字段设置为 `null` ([默认值](structs.md#default-values)) 生成的值。</span><span class="sxs-lookup"><span data-stu-id="74ab2-134">The default value of a struct is the value produced by setting all value type fields to their default value and all reference type fields to `null` ([Default values](structs.md#default-values)).</span></span>
*  <span data-ttu-id="74ab2-135">装箱和取消装箱操作用于在结构类型和 `object` ([装箱和取消装箱](structs.md#boxing-and-unboxing)) 之间进行转换。</span><span class="sxs-lookup"><span data-stu-id="74ab2-135">Boxing and unboxing operations are used to convert between a struct type and `object` ([Boxing and unboxing](structs.md#boxing-and-unboxing)).</span></span>
*  <span data-ttu-id="74ab2-136">`this`[此访问](expressions.md#this-access))  (结构的含义不同。</span><span class="sxs-lookup"><span data-stu-id="74ab2-136">The meaning of `this` is different for structs ([This access](expressions.md#this-access)).</span></span>
*  <span data-ttu-id="74ab2-137">结构的实例字段声明不允许包含变量初始值设定项 ([字段初始值设定](structs.md#field-initializers) 项) 。</span><span class="sxs-lookup"><span data-stu-id="74ab2-137">Instance field declarations for a struct are not permitted to include variable initializers ([Field initializers](structs.md#field-initializers)).</span></span>
*  <span data-ttu-id="74ab2-138">不允许结构)  ([构造函数](structs.md#constructors) 声明无参数的实例构造函数。</span><span class="sxs-lookup"><span data-stu-id="74ab2-138">A struct is not permitted to declare a parameterless instance constructor ([Constructors](structs.md#constructors)).</span></span>
*  <span data-ttu-id="74ab2-139">不允许结构将析构函数声明 ([析构函数](structs.md#destructors)) 。</span><span class="sxs-lookup"><span data-stu-id="74ab2-139">A struct is not permitted to declare a destructor ([Destructors](structs.md#destructors)).</span></span>

### <a name="value-semantics"></a><span data-ttu-id="74ab2-140">值语义</span><span class="sxs-lookup"><span data-stu-id="74ab2-140">Value semantics</span></span>

<span data-ttu-id="74ab2-141">结构是值 [类型 (值类型) ](types.md#value-types) 并且称为具有值语义。</span><span class="sxs-lookup"><span data-stu-id="74ab2-141">Structs are value types ([Value types](types.md#value-types)) and are said to have value semantics.</span></span> <span data-ttu-id="74ab2-142">另一方面，类是引用类型 ([引用](types.md#reference-types) 类型) 并被认为具有引用语义。</span><span class="sxs-lookup"><span data-stu-id="74ab2-142">Classes, on the other hand, are reference types ([Reference types](types.md#reference-types)) and are said to have reference semantics.</span></span>

<span data-ttu-id="74ab2-143">结构类型的变量直接包含结构的数据，而类类型的变量包含对数据的引用，后者称为对象。</span><span class="sxs-lookup"><span data-stu-id="74ab2-143">A variable of a struct type directly contains the data of the struct, whereas a variable of a class type contains a reference to the data, the latter known as an object.</span></span> <span data-ttu-id="74ab2-144">如果结构 `B` 包含类型为的实例字段 `A` 并且 `A` 是结构类型，则它是依赖于 `A` `B` 或从构造的类型的编译时错误 `B` 。</span><span class="sxs-lookup"><span data-stu-id="74ab2-144">When a struct `B` contains an instance field of type `A` and `A` is a struct type, it is a compile-time error for `A` to depend on `B` or a type constructed from `B`.</span></span> <span data-ttu-id="74ab2-145">`X`  \*  `Y` 如果 `X` 包含类型的实例字段，则结构直接依赖于 _ a 结构 `Y` 。</span><span class="sxs-lookup"><span data-stu-id="74ab2-145">A struct `X` \***directly depends on** _ a struct `Y` if `X` contains an instance field of type `Y`.</span></span> <span data-ttu-id="74ab2-146">根据此定义，结构所依赖的完整结构集是 _ *_直接依赖于_*\* 关系的传递闭包。</span><span class="sxs-lookup"><span data-stu-id="74ab2-146">Given this definition, the complete set of structs upon which a struct depends is the transitive closure of the _ *_directly depends on_*\* relationship.</span></span>  <span data-ttu-id="74ab2-147">例如</span><span class="sxs-lookup"><span data-stu-id="74ab2-147">For example</span></span>
```csharp
struct Node
{
    int data;
    Node next; // error, Node directly depends on itself
}
```
<span data-ttu-id="74ab2-148">是一个错误，因为 `Node` 包含自己的类型的实例字段。</span><span class="sxs-lookup"><span data-stu-id="74ab2-148">is an error because `Node` contains an instance field of its own type.</span></span>  <span data-ttu-id="74ab2-149">另一个示例</span><span class="sxs-lookup"><span data-stu-id="74ab2-149">Another example</span></span>
```csharp
struct A { B b; }

struct B { C c; }

struct C { A a; }
```
<span data-ttu-id="74ab2-150">是一个错误，因为每个类型 `A` 、 `B` 和相互 `C` 依赖。</span><span class="sxs-lookup"><span data-stu-id="74ab2-150">is an error because each of the types `A`, `B`, and `C` depend on each other.</span></span>

<span data-ttu-id="74ab2-151">对于类，两个变量可以引用同一对象，因此，对一个变量执行的运算可能会影响另一个变量所引用的对象。</span><span class="sxs-lookup"><span data-stu-id="74ab2-151">With classes, it is possible for two variables to reference the same object, and thus possible for operations on one variable to affect the object referenced by the other variable.</span></span> <span data-ttu-id="74ab2-152">使用结构，每个变量都有自己的数据副本 (除了 `ref` 和 `out` 参数变量) 以外，对一个变量执行的操作不可能影响另一个变量。</span><span class="sxs-lookup"><span data-stu-id="74ab2-152">With structs, the variables each have their own copy of the data (except in the case of `ref` and `out` parameter variables), and it is not possible for operations on one to affect the other.</span></span> <span data-ttu-id="74ab2-153">此外，由于结构不是引用类型，因此结构类型的值不能为 `null` 。</span><span class="sxs-lookup"><span data-stu-id="74ab2-153">Furthermore, because structs are not reference types, it is not possible for values of a struct type to be `null`.</span></span>

<span data-ttu-id="74ab2-154">给定声明</span><span class="sxs-lookup"><span data-stu-id="74ab2-154">Given the declaration</span></span>
```csharp
struct Point
{
    public int x, y;

    public Point(int x, int y) {
        this.x = x;
        this.y = y;
    }
}
```
<span data-ttu-id="74ab2-155">代码片段</span><span class="sxs-lookup"><span data-stu-id="74ab2-155">the code fragment</span></span>
```csharp
Point a = new Point(10, 10);
Point b = a;
a.x = 100;
System.Console.WriteLine(b.x);
```
<span data-ttu-id="74ab2-156">输出值 `10` 。</span><span class="sxs-lookup"><span data-stu-id="74ab2-156">outputs the value `10`.</span></span> <span data-ttu-id="74ab2-157">的赋值 `a` `b` 创建值的副本， `b` 因此不受赋值的影响 `a.x` 。</span><span class="sxs-lookup"><span data-stu-id="74ab2-157">The assignment of `a` to `b` creates a copy of the value, and `b` is thus unaffected by the assignment to `a.x`.</span></span> <span data-ttu-id="74ab2-158">`Point`改为将其声明为类， `100` 因为 `a` 和 `b` 将引用相同的对象。</span><span class="sxs-lookup"><span data-stu-id="74ab2-158">Had `Point` instead been declared as a class, the output would be `100` because `a` and `b` would reference the same object.</span></span>

### <a name="inheritance"></a><span data-ttu-id="74ab2-159">继承</span><span class="sxs-lookup"><span data-stu-id="74ab2-159">Inheritance</span></span>

<span data-ttu-id="74ab2-160">所有结构类型都隐式继承自类 `System.ValueType` ，后者又继承自类 `object` 。</span><span class="sxs-lookup"><span data-stu-id="74ab2-160">All struct types implicitly inherit from the class `System.ValueType`, which, in turn, inherits from class `object`.</span></span> <span data-ttu-id="74ab2-161">结构声明可以指定实现接口的列表，但结构声明不可能指定基类。</span><span class="sxs-lookup"><span data-stu-id="74ab2-161">A struct declaration may specify a list of implemented interfaces, but it is not possible for a struct declaration to specify a base class.</span></span>

<span data-ttu-id="74ab2-162">结构类型永远不是抽象类型，并且始终隐式密封。</span><span class="sxs-lookup"><span data-stu-id="74ab2-162">Struct types are never abstract and are always implicitly sealed.</span></span> <span data-ttu-id="74ab2-163">`abstract`因此， `sealed` 在结构声明中不允许使用和修饰符。</span><span class="sxs-lookup"><span data-stu-id="74ab2-163">The `abstract` and `sealed` modifiers are therefore not permitted in a struct declaration.</span></span>

<span data-ttu-id="74ab2-164">由于结构不支持继承，因此结构成员的声明的可访问性不能是 `protected` 或 `protected internal` 。</span><span class="sxs-lookup"><span data-stu-id="74ab2-164">Since inheritance isn't supported for structs, the declared accessibility of a struct member cannot be `protected` or `protected internal`.</span></span>

<span data-ttu-id="74ab2-165">结构中的函数成员不能是 `abstract` 或 `virtual` ，且 `override` 修饰符只允许重写从继承的方法 `System.ValueType` 。</span><span class="sxs-lookup"><span data-stu-id="74ab2-165">Function members in a struct cannot be `abstract` or `virtual`, and the `override` modifier is allowed only to override methods inherited from `System.ValueType`.</span></span>

### <a name="assignment"></a><span data-ttu-id="74ab2-166">分配</span><span class="sxs-lookup"><span data-stu-id="74ab2-166">Assignment</span></span>

<span data-ttu-id="74ab2-167">对结构类型的变量赋值会创建要分配的值的副本。</span><span class="sxs-lookup"><span data-stu-id="74ab2-167">Assignment to a variable of a struct type creates a copy of the value being assigned.</span></span> <span data-ttu-id="74ab2-168">这不同于对类类型的变量的赋值，后者复制引用，而不是由引用标识的对象。</span><span class="sxs-lookup"><span data-stu-id="74ab2-168">This differs from assignment to a variable of a class type, which copies the reference but not the object identified by the reference.</span></span>

<span data-ttu-id="74ab2-169">与赋值类似，当结构作为值参数传递或作为函数成员的结果返回时，将创建结构的副本。</span><span class="sxs-lookup"><span data-stu-id="74ab2-169">Similar to an assignment, when a struct is passed as a value parameter or returned as the result of a function member, a copy of the struct is created.</span></span> <span data-ttu-id="74ab2-170">可以通过使用或参数对函数成员的引用来传递结构 `ref` `out` 。</span><span class="sxs-lookup"><span data-stu-id="74ab2-170">A struct may be passed by reference to a function member using a `ref` or `out` parameter.</span></span>

<span data-ttu-id="74ab2-171">当结构的属性或索引器是赋值的目标时，与属性或索引器访问关联的实例表达式必须归类为变量。</span><span class="sxs-lookup"><span data-stu-id="74ab2-171">When a property or indexer of a struct is the target of an assignment, the instance expression associated with the property or indexer access must be classified as a variable.</span></span> <span data-ttu-id="74ab2-172">如果实例表达式归类为值，则会发生编译时错误。</span><span class="sxs-lookup"><span data-stu-id="74ab2-172">If the instance expression is classified as a value, a compile-time error occurs.</span></span> <span data-ttu-id="74ab2-173">这将在 [简单的赋值](expressions.md#simple-assignment)中更详细地介绍。</span><span class="sxs-lookup"><span data-stu-id="74ab2-173">This is described in further detail in [Simple assignment](expressions.md#simple-assignment).</span></span>

### <a name="default-values"></a><span data-ttu-id="74ab2-174">默认值</span><span class="sxs-lookup"><span data-stu-id="74ab2-174">Default values</span></span>

<span data-ttu-id="74ab2-175">如 " [默认值](variables.md#default-values)" 中所述，在创建多个变量时，它们会自动初始化为其默认值。</span><span class="sxs-lookup"><span data-stu-id="74ab2-175">As described in [Default values](variables.md#default-values), several kinds of variables are automatically initialized to their default value when they are created.</span></span> <span data-ttu-id="74ab2-176">对于类类型和其他引用类型的变量，此默认值为 `null` 。</span><span class="sxs-lookup"><span data-stu-id="74ab2-176">For variables of class types and other reference types, this default value is `null`.</span></span> <span data-ttu-id="74ab2-177">但是，由于结构是值类型，因此， `null` 结构的默认值是通过将所有值类型字段设置为其默认值和所有引用类型字段而产生的值 `null` 。</span><span class="sxs-lookup"><span data-stu-id="74ab2-177">However, since structs are value types that cannot be `null`, the default value of a struct is the value produced by setting all value type fields to their default value and all reference type fields to `null`.</span></span>

<span data-ttu-id="74ab2-178">引用 `Point` 上面声明的结构，示例</span><span class="sxs-lookup"><span data-stu-id="74ab2-178">Referring to the `Point` struct declared above, the example</span></span>
```csharp
Point[] a = new Point[100];
```
<span data-ttu-id="74ab2-179">将数组中的每个初始化 `Point` 为通过将 `x` 和字段设置为零而产生的值 `y` 。</span><span class="sxs-lookup"><span data-stu-id="74ab2-179">initializes each `Point` in the array to the value produced by setting the `x` and `y` fields to zero.</span></span>

<span data-ttu-id="74ab2-180">结构的默认值与 [结构 (默认构造函数](types.md#default-constructors)) 的默认构造函数返回的值相对应。</span><span class="sxs-lookup"><span data-stu-id="74ab2-180">The default value of a struct corresponds to the value returned by the default constructor of the struct ([Default constructors](types.md#default-constructors)).</span></span> <span data-ttu-id="74ab2-181">与类不同，结构不允许声明无参数的实例构造函数。</span><span class="sxs-lookup"><span data-stu-id="74ab2-181">Unlike a class, a struct is not permitted to declare a parameterless instance constructor.</span></span> <span data-ttu-id="74ab2-182">相反，每个结构都隐式包含一个无参数的实例构造函数，该构造函数始终返回通过将所有值类型字段设置为其默认值和所有引用类型字段而产生的值 `null` 。</span><span class="sxs-lookup"><span data-stu-id="74ab2-182">Instead, every struct implicitly has a parameterless instance constructor which always returns the value that results from setting all value type fields to their default value and all reference type fields to `null`.</span></span>

<span data-ttu-id="74ab2-183">结构应设计为将默认初始化状态视为有效状态。</span><span class="sxs-lookup"><span data-stu-id="74ab2-183">Structs should be designed to consider the default initialization state a valid state.</span></span> <span data-ttu-id="74ab2-184">示例中</span><span class="sxs-lookup"><span data-stu-id="74ab2-184">In the example</span></span>
```csharp
using System;

struct KeyValuePair
{
    string key;
    string value;

    public KeyValuePair(string key, string value) {
        if (key == null || value == null) throw new ArgumentException();
        this.key = key;
        this.value = value;
    }
}
```
<span data-ttu-id="74ab2-185">用户定义实例构造函数仅在显式调用它的位置保护 null 值。</span><span class="sxs-lookup"><span data-stu-id="74ab2-185">the user-defined instance constructor protects against null values only where it is explicitly called.</span></span> <span data-ttu-id="74ab2-186">如果 `KeyValuePair` 变量服从默认值初始化，则 `key` 和 `value` 字段将为 null，并且该结构必须准备好处理此状态。</span><span class="sxs-lookup"><span data-stu-id="74ab2-186">In cases where a `KeyValuePair` variable is subject to default value initialization, the `key` and `value` fields will be null, and the struct must be prepared to handle this state.</span></span>

### <a name="boxing-and-unboxing"></a><span data-ttu-id="74ab2-187">装箱和取消装箱</span><span class="sxs-lookup"><span data-stu-id="74ab2-187">Boxing and unboxing</span></span>

<span data-ttu-id="74ab2-188">类类型的值可以转换为类型 `object` 或由类实现的接口类型，只需在编译时将引用视为另一种类型。</span><span class="sxs-lookup"><span data-stu-id="74ab2-188">A value of a class type can be converted to type `object` or to an interface type that is implemented by the class simply by treating the reference as another type at compile-time.</span></span> <span data-ttu-id="74ab2-189">同样，可以将类型的值 `object` 或接口类型的值转换回类类型，而无需更改引用 (但在这种情况下，需要运行时类型检查) 。</span><span class="sxs-lookup"><span data-stu-id="74ab2-189">Likewise, a value of type `object` or a value of an interface type can be converted back to a class type without changing the reference (but of course a run-time type check is required in this case).</span></span>

<span data-ttu-id="74ab2-190">由于结构不是引用类型，因此对于结构类型，这些操作的实现方式有所不同。</span><span class="sxs-lookup"><span data-stu-id="74ab2-190">Since structs are not reference types, these operations are implemented differently for struct types.</span></span> <span data-ttu-id="74ab2-191">当结构类型的值转换为类型 `object` 或结构实现的接口类型时，将发生装箱操作。</span><span class="sxs-lookup"><span data-stu-id="74ab2-191">When a value of a struct type is converted to type `object` or to an interface type that is implemented by the struct, a boxing operation takes place.</span></span> <span data-ttu-id="74ab2-192">同样，如果类型的值 `object` 或接口类型的值转换回结构类型，则会发生取消装箱操作。</span><span class="sxs-lookup"><span data-stu-id="74ab2-192">Likewise, when a value of type `object` or a value of an interface type is converted back to a struct type, an unboxing operation takes place.</span></span> <span data-ttu-id="74ab2-193">对类类型的相同操作的主要区别是装箱和取消装箱操作会将结构值复制到或传出装箱的实例。</span><span class="sxs-lookup"><span data-stu-id="74ab2-193">A key difference from the same operations on class types is that boxing and unboxing copies the struct value either into or out of the boxed instance.</span></span> <span data-ttu-id="74ab2-194">因此，在装箱或取消装箱操作后，对取消装箱的结构所做的更改不会反映在已装箱的结构中。</span><span class="sxs-lookup"><span data-stu-id="74ab2-194">Thus, following a boxing or unboxing operation, changes made to the unboxed struct are not reflected in the boxed struct.</span></span>

<span data-ttu-id="74ab2-195">当结构类型重写从 `System.Object` (（如、或) ）继承的虚方法时 `Equals` `GetHashCode` `ToString` ，通过该结构类型的实例调用虚拟方法不会导致装箱发生。</span><span class="sxs-lookup"><span data-stu-id="74ab2-195">When a struct type overrides a virtual method inherited from `System.Object` (such as `Equals`, `GetHashCode`, or `ToString`), invocation of the virtual method through an instance of the struct type does not cause boxing to occur.</span></span> <span data-ttu-id="74ab2-196">即使将结构用作类型参数，并且通过类型参数类型的实例进行调用，也是如此。</span><span class="sxs-lookup"><span data-stu-id="74ab2-196">This is true even when the struct is used as a type parameter and the invocation occurs through an instance of the type parameter type.</span></span> <span data-ttu-id="74ab2-197">例如：</span><span class="sxs-lookup"><span data-stu-id="74ab2-197">For example:</span></span>
```csharp
using System;

struct Counter
{
    int value;

    public override string ToString() {
        value++;
        return value.ToString();
    }
}

class Program
{
    static void Test<T>() where T: new() {
        T x = new T();
        Console.WriteLine(x.ToString());
        Console.WriteLine(x.ToString());
        Console.WriteLine(x.ToString());
    }

    static void Main() {
        Test<Counter>();
    }
}
```

<span data-ttu-id="74ab2-198">该程序的输出为：</span><span class="sxs-lookup"><span data-stu-id="74ab2-198">The output of the program is:</span></span>
```console
1
2
3
```

<span data-ttu-id="74ab2-199">尽管对具有副作用的样式是不正确的 `ToString` ，但该示例演示了对的三个调用没有发生装箱 `x.ToString()` 。</span><span class="sxs-lookup"><span data-stu-id="74ab2-199">Although it is bad style for `ToString` to have side effects, the example demonstrates that no boxing occurred for the three invocations of `x.ToString()`.</span></span>

<span data-ttu-id="74ab2-200">同样，在访问受约束的类型参数上的成员时，不会隐式进行装箱。</span><span class="sxs-lookup"><span data-stu-id="74ab2-200">Similarly, boxing never implicitly occurs when accessing a member on a constrained type parameter.</span></span> <span data-ttu-id="74ab2-201">例如，假设某个接口 `ICounter` 包含一个 `Increment` 可用于修改值的方法。</span><span class="sxs-lookup"><span data-stu-id="74ab2-201">For example, suppose an interface `ICounter` contains a method `Increment` which can be used to modify a value.</span></span> <span data-ttu-id="74ab2-202">如果 `ICounter` 用作约束，则会 `Increment` 调用方法的实现，该方法具有对调用的变量的引用 `Increment` ，而不是装箱副本。</span><span class="sxs-lookup"><span data-stu-id="74ab2-202">If `ICounter` is used as a constraint, the implementation of the `Increment` method is called with a reference to the variable that `Increment` was called on, never a boxed copy.</span></span>

```csharp
using System;

interface ICounter
{
    void Increment();
}

struct Counter: ICounter
{
    int value;

    public override string ToString() {
        return value.ToString();
    }

    void ICounter.Increment() {
        value++;
    }
}

class Program
{
    static void Test<T>() where T: ICounter, new() {
        T x = new T();
        Console.WriteLine(x);
        x.Increment();                    // Modify x
        Console.WriteLine(x);
        ((ICounter)x).Increment();        // Modify boxed copy of x
        Console.WriteLine(x);
    }

    static void Main() {
        Test<Counter>();
    }
}
```

<span data-ttu-id="74ab2-203">用于 `Increment` 修改变量中的值的第一次调用 `x` 。</span><span class="sxs-lookup"><span data-stu-id="74ab2-203">The first call to `Increment` modifies the value in the variable `x`.</span></span> <span data-ttu-id="74ab2-204">这与对的第二次调用不等效 `Increment` ，后者修改的装箱副本中的值 `x` 。</span><span class="sxs-lookup"><span data-stu-id="74ab2-204">This is not equivalent to the second call to `Increment`, which modifies the value in a boxed copy of `x`.</span></span> <span data-ttu-id="74ab2-205">因此，该程序的输出为：</span><span class="sxs-lookup"><span data-stu-id="74ab2-205">Thus, the output of the program is:</span></span>
```console
0
1
1
```

<span data-ttu-id="74ab2-206">有关装箱和取消装箱的更多详细信息，请参阅 [装箱和取消装箱](types.md#boxing-and-unboxing)。</span><span class="sxs-lookup"><span data-stu-id="74ab2-206">For further details on boxing and unboxing, see [Boxing and unboxing](types.md#boxing-and-unboxing).</span></span>

### <a name="meaning-of-this"></a><span data-ttu-id="74ab2-207">含义</span><span class="sxs-lookup"><span data-stu-id="74ab2-207">Meaning of this</span></span>

<span data-ttu-id="74ab2-208">在类的实例构造函数或实例函数成员内， `this` 分类为值。</span><span class="sxs-lookup"><span data-stu-id="74ab2-208">Within an instance constructor or instance function member of a class, `this` is classified as a value.</span></span> <span data-ttu-id="74ab2-209">因此，虽然 `this` 可用于引用调用了函数成员的实例，但不能 `this` 在类的函数成员中对赋值。</span><span class="sxs-lookup"><span data-stu-id="74ab2-209">Thus, while `this` can be used to refer to the instance for which the function member was invoked, it is not possible to assign to `this` in a function member of a class.</span></span>

<span data-ttu-id="74ab2-210">在结构的实例构造函数中， `this` 对应于结构 `out` 类型的参数，并在结构的实例函数成员内与 `this` `ref` 结构类型的参数对应。</span><span class="sxs-lookup"><span data-stu-id="74ab2-210">Within an instance constructor of a struct, `this` corresponds to an `out` parameter of the struct type, and within an instance function member of a struct, `this` corresponds to a `ref` parameter of the struct type.</span></span> <span data-ttu-id="74ab2-211">在这两种情况下， `this` 都归类为变量，并且可以通过将 `this` 此函数作为或参数传递来修改调用了函数成员的整个结构 `ref` `out` 。</span><span class="sxs-lookup"><span data-stu-id="74ab2-211">In both cases, `this` is classified as a variable, and it is possible to modify the entire struct for which the function member was invoked by assigning to `this` or by passing this as a `ref` or `out` parameter.</span></span>

### <a name="field-initializers"></a><span data-ttu-id="74ab2-212">字段初始值设定项</span><span class="sxs-lookup"><span data-stu-id="74ab2-212">Field initializers</span></span>

<span data-ttu-id="74ab2-213">如 " [默认值](structs.md#default-values)" 中所述，结构的默认值由将所有值类型字段设置为其默认值并将所有引用类型字段设置为而产生的值 `null` 。</span><span class="sxs-lookup"><span data-stu-id="74ab2-213">As described in [Default values](structs.md#default-values), the default value of a struct consists of the value that results from setting all value type fields to their default value and all reference type fields to `null`.</span></span> <span data-ttu-id="74ab2-214">出于此原因，结构不允许实例字段声明包含变量初始值设定项。</span><span class="sxs-lookup"><span data-stu-id="74ab2-214">For this reason, a struct does not permit instance field declarations to include variable initializers.</span></span> <span data-ttu-id="74ab2-215">此限制仅适用于实例字段。</span><span class="sxs-lookup"><span data-stu-id="74ab2-215">This restriction applies only to instance fields.</span></span> <span data-ttu-id="74ab2-216">允许结构的静态字段包含变量初始值设定项。</span><span class="sxs-lookup"><span data-stu-id="74ab2-216">Static fields of a struct are permitted to include variable initializers.</span></span>

<span data-ttu-id="74ab2-217">示例</span><span class="sxs-lookup"><span data-stu-id="74ab2-217">The example</span></span>
```csharp
struct Point
{
    public int x = 1;  // Error, initializer not permitted
    public int y = 1;  // Error, initializer not permitted
}
```
<span data-ttu-id="74ab2-218">出现错误，因为实例字段声明包含变量初始值设定项。</span><span class="sxs-lookup"><span data-stu-id="74ab2-218">is in error because the instance field declarations include variable initializers.</span></span>

### <a name="constructors"></a><span data-ttu-id="74ab2-219">构造函数</span><span class="sxs-lookup"><span data-stu-id="74ab2-219">Constructors</span></span>

<span data-ttu-id="74ab2-220">与类不同，结构不允许声明无参数的实例构造函数。</span><span class="sxs-lookup"><span data-stu-id="74ab2-220">Unlike a class, a struct is not permitted to declare a parameterless instance constructor.</span></span> <span data-ttu-id="74ab2-221">相反，每个结构都隐式包含一个无参数的实例构造函数，该构造函数始终返回将所有值类型字段设置为其默认值并将所有引用类型字段设置为 null ([默认构造函数](types.md#default-constructors)) 时得出的值。</span><span class="sxs-lookup"><span data-stu-id="74ab2-221">Instead, every struct implicitly has a parameterless instance constructor which always returns the value that results from setting all value type fields to their default value and all reference type fields to null ([Default constructors](types.md#default-constructors)).</span></span> <span data-ttu-id="74ab2-222">结构可声明具有参数的实例构造函数。</span><span class="sxs-lookup"><span data-stu-id="74ab2-222">A struct can declare instance constructors having parameters.</span></span> <span data-ttu-id="74ab2-223">例如</span><span class="sxs-lookup"><span data-stu-id="74ab2-223">For example</span></span>
```csharp
struct Point
{
    int x, y;

    public Point(int x, int y) {
        this.x = x;
        this.y = y;
    }
}
```

<span data-ttu-id="74ab2-224">给定上述声明，语句</span><span class="sxs-lookup"><span data-stu-id="74ab2-224">Given the above declaration, the statements</span></span>
```csharp
Point p1 = new Point();
Point p2 = new Point(0, 0);
```
<span data-ttu-id="74ab2-225">两者均使用创建一个 `Point` `x` ，并 `y` 将其初始化为零。</span><span class="sxs-lookup"><span data-stu-id="74ab2-225">both create a `Point` with `x` and `y` initialized to zero.</span></span>

<span data-ttu-id="74ab2-226">不允许结构实例构造函数包含形式的构造函数初始值设定项 `base(...)` 。</span><span class="sxs-lookup"><span data-stu-id="74ab2-226">A struct instance constructor is not permitted to include a constructor initializer of the form `base(...)`.</span></span>

<span data-ttu-id="74ab2-227">如果结构实例构造函数未指定构造函数初始值设定项，则 `this` 变量与 `out` 结构类型的参数对应，与 `out` 参数类似，必须在 `this` 构造函数返回的每个位置 ([明确赋值](variables.md#definite-assignment)) 明确赋值。</span><span class="sxs-lookup"><span data-stu-id="74ab2-227">If the struct instance constructor doesn't specify a constructor initializer, the `this` variable corresponds to an `out` parameter of the struct type, and similar to an `out` parameter, `this` must be definitely assigned ([Definite assignment](variables.md#definite-assignment)) at every location where the constructor returns.</span></span> <span data-ttu-id="74ab2-228">如果结构实例构造函数指定构造函数初始值设定项，则 `this` 变量与结构类型的参数相对应，与 `ref` 参数类似 `ref` ， `this` 在进入构造函数主体时被视为已明确赋值。</span><span class="sxs-lookup"><span data-stu-id="74ab2-228">If the struct instance constructor specifies a constructor initializer, the `this` variable corresponds to a `ref` parameter of the struct type, and similar to a `ref` parameter, `this` is considered definitely assigned on entry to the constructor body.</span></span> <span data-ttu-id="74ab2-229">请考虑下面的实例构造函数实现：</span><span class="sxs-lookup"><span data-stu-id="74ab2-229">Consider the instance constructor implementation below:</span></span>
```csharp
struct Point
{
    int x, y;

    public int X {
        set { x = value; }
    }

    public int Y {
        set { y = value; }
    }

    public Point(int x, int y) {
        X = x;        // error, this is not yet definitely assigned
        Y = y;        // error, this is not yet definitely assigned
    }
}
```

<span data-ttu-id="74ab2-230">不 (包括属性和) 的 set 访问器的实例成员函数 `X` ， `Y` 直到构造的结构的所有字段都已明确赋值。</span><span class="sxs-lookup"><span data-stu-id="74ab2-230">No instance member function (including the set accessors for the properties `X` and `Y`) can be called until all fields of the struct being constructed have been definitely assigned.</span></span> <span data-ttu-id="74ab2-231">唯一的例外涉及自动实现属性， ([自动实现](classes.md#automatically-implemented-properties)) 的属性。</span><span class="sxs-lookup"><span data-stu-id="74ab2-231">The only exception involves automatically implemented properties ([Automatically implemented properties](classes.md#automatically-implemented-properties)).</span></span> <span data-ttu-id="74ab2-232">明确的赋值规则 ([简单赋值表达式](variables.md#simple-assignment-expressions) ，) 在该结构类型的实例构造函数中明确分配对结构类型的自动属性的赋值：此类赋值被视为自动属性的隐藏支持字段的明确分配。</span><span class="sxs-lookup"><span data-stu-id="74ab2-232">The definite assignment rules ([Simple assignment expressions](variables.md#simple-assignment-expressions)) specifically exempt assignment to an auto-property of a struct type within an instance constructor of that struct type: such an assignment is considered a definite assignment of the hidden backing field of the auto-property.</span></span> <span data-ttu-id="74ab2-233">因此，可以使用以下内容：</span><span class="sxs-lookup"><span data-stu-id="74ab2-233">Thus, the following is allowed:</span></span>

```csharp
struct Point
{
    public int X { get; set; }
    public int Y { get; set; }

    public Point(int x, int y) {
        X = x;      // allowed, definitely assigns backing field
        Y = y;      // allowed, definitely assigns backing field
    }
```

### <a name="destructors"></a><span data-ttu-id="74ab2-234">析构函数</span><span class="sxs-lookup"><span data-stu-id="74ab2-234">Destructors</span></span>

<span data-ttu-id="74ab2-235">不允许结构声明析构函数。</span><span class="sxs-lookup"><span data-stu-id="74ab2-235">A struct is not permitted to declare a destructor.</span></span>

### <a name="static-constructors"></a><span data-ttu-id="74ab2-236">静态构造函数</span><span class="sxs-lookup"><span data-stu-id="74ab2-236">Static constructors</span></span>

<span data-ttu-id="74ab2-237">结构的静态构造函数遵循与类相同的大多数规则。</span><span class="sxs-lookup"><span data-stu-id="74ab2-237">Static constructors for structs follow most of the same rules as for classes.</span></span> <span data-ttu-id="74ab2-238">在应用程序域中发生以下事件的第一个事件时，将触发结构类型的静态构造函数的执行：</span><span class="sxs-lookup"><span data-stu-id="74ab2-238">The execution of a static constructor for a struct type is triggered by the first of the following events to occur within an application domain:</span></span>

*  <span data-ttu-id="74ab2-239">引用结构类型的静态成员。</span><span class="sxs-lookup"><span data-stu-id="74ab2-239">A static member of the struct type is referenced.</span></span>
*  <span data-ttu-id="74ab2-240">将调用结构类型的显式声明的构造函数。</span><span class="sxs-lookup"><span data-stu-id="74ab2-240">An explicitly declared constructor of the struct type is called.</span></span>

<span data-ttu-id="74ab2-241"> (默认值) 结构类型的 [默认](structs.md#default-values) 值的创建不会触发静态构造函数。</span><span class="sxs-lookup"><span data-stu-id="74ab2-241">The creation of default values ([Default values](structs.md#default-values)) of struct types does not trigger the static constructor.</span></span> <span data-ttu-id="74ab2-242"> (的一个示例是数组中元素的初始值。 ) </span><span class="sxs-lookup"><span data-stu-id="74ab2-242">(An example of this is the initial value of elements in an array.)</span></span>

## <a name="struct-examples"></a><span data-ttu-id="74ab2-243">结构示例</span><span class="sxs-lookup"><span data-stu-id="74ab2-243">Struct examples</span></span>

<span data-ttu-id="74ab2-244">下面显示了两个使用 `struct` 类型创建类型的示例，这些类型可以与语言的预定义类型相似，但使用修改后的语义。</span><span class="sxs-lookup"><span data-stu-id="74ab2-244">The following shows two significant examples of using `struct` types to create types that can be used similarly to the predefined types of the language, but with modified semantics.</span></span>

### <a name="database-integer-type"></a><span data-ttu-id="74ab2-245">数据库整数类型</span><span class="sxs-lookup"><span data-stu-id="74ab2-245">Database integer type</span></span>

<span data-ttu-id="74ab2-246">`DBInt`下面的结构实现整数类型，该类型可以表示类型的完整值集 `int` ，另外还实现了指示未知值的附加状态。</span><span class="sxs-lookup"><span data-stu-id="74ab2-246">The `DBInt` struct below implements an integer type that can represent the complete set of values of the `int` type, plus an additional state that indicates an unknown value.</span></span> <span data-ttu-id="74ab2-247">具有这些特征的类型通常用于数据库。</span><span class="sxs-lookup"><span data-stu-id="74ab2-247">A type with these characteristics is commonly used in databases.</span></span>

```csharp
using System;

public struct DBInt
{
    // The Null member represents an unknown DBInt value.

    public static readonly DBInt Null = new DBInt();

    // When the defined field is true, this DBInt represents a known value
    // which is stored in the value field. When the defined field is false,
    // this DBInt represents an unknown value, and the value field is 0.

    int value;
    bool defined;

    // Private instance constructor. Creates a DBInt with a known value.

    DBInt(int value) {
        this.value = value;
        this.defined = true;
    }

    // The IsNull property is true if this DBInt represents an unknown value.

    public bool IsNull { get { return !defined; } }

    // The Value property is the known value of this DBInt, or 0 if this
    // DBInt represents an unknown value.

    public int Value { get { return value; } }

    // Implicit conversion from int to DBInt.

    public static implicit operator DBInt(int x) {
        return new DBInt(x);
    }

    // Explicit conversion from DBInt to int. Throws an exception if the
    // given DBInt represents an unknown value.

    public static explicit operator int(DBInt x) {
        if (!x.defined) throw new InvalidOperationException();
        return x.value;
    }

    public static DBInt operator +(DBInt x) {
        return x;
    }

    public static DBInt operator -(DBInt x) {
        return x.defined ? -x.value : Null;
    }

    public static DBInt operator +(DBInt x, DBInt y) {
        return x.defined && y.defined? x.value + y.value: Null;
    }

    public static DBInt operator -(DBInt x, DBInt y) {
        return x.defined && y.defined? x.value - y.value: Null;
    }

    public static DBInt operator *(DBInt x, DBInt y) {
        return x.defined && y.defined? x.value * y.value: Null;
    }

    public static DBInt operator /(DBInt x, DBInt y) {
        return x.defined && y.defined? x.value / y.value: Null;
    }

    public static DBInt operator %(DBInt x, DBInt y) {
        return x.defined && y.defined? x.value % y.value: Null;
    }

    public static DBBool operator ==(DBInt x, DBInt y) {
        return x.defined && y.defined? x.value == y.value: DBBool.Null;
    }

    public static DBBool operator !=(DBInt x, DBInt y) {
        return x.defined && y.defined? x.value != y.value: DBBool.Null;
    }

    public static DBBool operator >(DBInt x, DBInt y) {
        return x.defined && y.defined? x.value > y.value: DBBool.Null;
    }

    public static DBBool operator <(DBInt x, DBInt y) {
        return x.defined && y.defined? x.value < y.value: DBBool.Null;
    }

    public static DBBool operator >=(DBInt x, DBInt y) {
        return x.defined && y.defined? x.value >= y.value: DBBool.Null;
    }

    public static DBBool operator <=(DBInt x, DBInt y) {
        return x.defined && y.defined? x.value <= y.value: DBBool.Null;
    }

    public override bool Equals(object obj) {
        if (!(obj is DBInt)) return false;
        DBInt x = (DBInt)obj;
        return value == x.value && defined == x.defined;
    }

    public override int GetHashCode() {
        return value;
    }

    public override string ToString() {
        return defined? value.ToString(): "DBInt.Null";
    }
}
```

### <a name="database-boolean-type"></a><span data-ttu-id="74ab2-248">数据库布尔类型</span><span class="sxs-lookup"><span data-stu-id="74ab2-248">Database boolean type</span></span>

<span data-ttu-id="74ab2-249">`DBBool`下面的结构实现了三值逻辑类型。</span><span class="sxs-lookup"><span data-stu-id="74ab2-249">The `DBBool` struct below implements a three-valued logical type.</span></span> <span data-ttu-id="74ab2-250">此类型的可能值为 `DBBool.True` 、 `DBBool.False` 和 `DBBool.Null` ，其中 `Null` 成员指示未知值。</span><span class="sxs-lookup"><span data-stu-id="74ab2-250">The possible values of this type are `DBBool.True`, `DBBool.False`, and `DBBool.Null`, where the `Null` member indicates an unknown value.</span></span> <span data-ttu-id="74ab2-251">这三值逻辑类型通常用于数据库。</span><span class="sxs-lookup"><span data-stu-id="74ab2-251">Such three-valued logical types are commonly used in databases.</span></span>

```csharp
using System;

public struct DBBool
{
    // The three possible DBBool values.

    public static readonly DBBool Null = new DBBool(0);
    public static readonly DBBool False = new DBBool(-1);
    public static readonly DBBool True = new DBBool(1);

    // Private field that stores -1, 0, 1 for False, Null, True.

    sbyte value;

    // Private instance constructor. The value parameter must be -1, 0, or 1.

    DBBool(int value) {
        this.value = (sbyte)value;
    }

    // Properties to examine the value of a DBBool. Return true if this
    // DBBool has the given value, false otherwise.

    public bool IsNull { get { return value == 0; } }

    public bool IsFalse { get { return value < 0; } }

    public bool IsTrue { get { return value > 0; } }

    // Implicit conversion from bool to DBBool. Maps true to DBBool.True and
    // false to DBBool.False.

    public static implicit operator DBBool(bool x) {
        return x? True: False;
    }

    // Explicit conversion from DBBool to bool. Throws an exception if the
    // given DBBool is Null, otherwise returns true or false.

    public static explicit operator bool(DBBool x) {
        if (x.value == 0) throw new InvalidOperationException();
        return x.value > 0;
    }

    // Equality operator. Returns Null if either operand is Null, otherwise
    // returns True or False.

    public static DBBool operator ==(DBBool x, DBBool y) {
        if (x.value == 0 || y.value == 0) return Null;
        return x.value == y.value? True: False;
    }

    // Inequality operator. Returns Null if either operand is Null, otherwise
    // returns True or False.

    public static DBBool operator !=(DBBool x, DBBool y) {
        if (x.value == 0 || y.value == 0) return Null;
        return x.value != y.value? True: False;
    }

    // Logical negation operator. Returns True if the operand is False, Null
    // if the operand is Null, or False if the operand is True.

    public static DBBool operator !(DBBool x) {
        return new DBBool(-x.value);
    }

    // Logical AND operator. Returns False if either operand is False,
    // otherwise Null if either operand is Null, otherwise True.

    public static DBBool operator &(DBBool x, DBBool y) {
        return new DBBool(x.value < y.value? x.value: y.value);
    }

    // Logical OR operator. Returns True if either operand is True, otherwise
    // Null if either operand is Null, otherwise False.

    public static DBBool operator |(DBBool x, DBBool y) {
        return new DBBool(x.value > y.value? x.value: y.value);
    }

    // Definitely true operator. Returns true if the operand is True, false
    // otherwise.

    public static bool operator true(DBBool x) {
        return x.value > 0;
    }

    // Definitely false operator. Returns true if the operand is False, false
    // otherwise.

    public static bool operator false(DBBool x) {
        return x.value < 0;
    }

    public override bool Equals(object obj) {
        if (!(obj is DBBool)) return false;
        return value == ((DBBool)obj).value;
    }

    public override int GetHashCode() {
        return value;
    }

    public override string ToString() {
        if (value > 0) return "DBBool.True";
        if (value < 0) return "DBBool.False";
        return "DBBool.Null";
    }
}
```

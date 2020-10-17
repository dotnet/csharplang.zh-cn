---
ms.openlocfilehash: c4168bb99a60688c65db0c146e20ee62c009e08e
ms.sourcegitcommit: f6a48200419ba9f03f5efc8500bfae610cc45abc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/15/2020
ms.locfileid: "92097672"
---

# <a name="discriminated-unions--enum-class"></a><span data-ttu-id="d4894-101">可区分联合/ `enum class`</span><span class="sxs-lookup"><span data-stu-id="d4894-101">Discriminated unions / `enum class`</span></span>

<span data-ttu-id="d4894-102">`enum class`es 是类型声明的一种新类型，有时称为可区分联合，其中每个可能的实例都列出了该类型，并且每个实例都是非重叠的。</span><span class="sxs-lookup"><span data-stu-id="d4894-102">`enum class`es are a new kind of type declaration, sometimes referred to as discriminated unions, where each every possible instance the type is listed, and each instance is non-overlapping.</span></span>

<span data-ttu-id="d4894-103">`enum class`使用以下语法定义：</span><span class="sxs-lookup"><span data-stu-id="d4894-103">An `enum class` is defined using the following syntax:</span></span>

```antlr
enum_class
    : 'partial'? 'enum class' identifier type_parameter_list? type_parameter_constraints_clause* 
      '{' enum_class_body '}'
    ;

enum_class_body
    : enum_class_cases?
    | enum_class_cases ','
    ;

enum_class_cases
    : enum_class_case
    | enum_class_case ',' enum_class_cases
    ;

enum_class_case
    : enum_class
    | class_declaration
    | identifier type_parameter_list? '(' formal_parameter_list? ')'
    | identifier
    ;

```

<span data-ttu-id="d4894-104">示例语法：</span><span class="sxs-lookup"><span data-stu-id="d4894-104">Sample syntax:</span></span>

```C#
enum class Shape
{
    Rectangle(float Width, float Length),
    Circle(float Radius),
}
```

## <a name="semantics"></a><span data-ttu-id="d4894-105">语义</span><span class="sxs-lookup"><span data-stu-id="d4894-105">Semantics</span></span>

<span data-ttu-id="d4894-106">`enum class`定义定义根类型，该类型是与声明同名的抽象类 `enum class` 和一组成员，其中每个成员都有一个类型，该类型是根类型的子类型。</span><span class="sxs-lookup"><span data-stu-id="d4894-106">An `enum class` definition defines a root type, which is an abstract class of the same name as the `enum class` declaration, and a set of members, each of which has a type which is a subtype of the root type.</span></span> <span data-ttu-id="d4894-107">如果有多个 `partial enum class` 定义，则所有成员都将被视为 enum 类定义的成员。</span><span class="sxs-lookup"><span data-stu-id="d4894-107">If there are multiple `partial enum class` definitions, all members will be considered members of the enum class definition.</span></span> <span data-ttu-id="d4894-108">与用户定义的抽象类定义不同， `enum class` 根类型默认为 partial，并定义为具有默认的无*private*参数的构造函数。</span><span class="sxs-lookup"><span data-stu-id="d4894-108">Unlike a user-defined abstract class definition, the `enum class` root type is partial by default and defined to have a default *private* parameter-less constructor.</span></span>

<span data-ttu-id="d4894-109">请注意，由于根类型被定义为分部抽象类，因此还可以添加 *根类型* 的分部定义，其中允许使用类主体的标准语法形式。</span><span class="sxs-lookup"><span data-stu-id="d4894-109">Note that, since the root type is defined to be a partial abstract class, partial definitions of the *root type* may also be added, where standard syntax forms for a class body are allowed.</span></span>
<span data-ttu-id="d4894-110">但是，除了指定为成员的类型之外，任何类型都不能直接从根类型继承任何声明 `enum class` 。</span><span class="sxs-lookup"><span data-stu-id="d4894-110">However, no types may directly inherit from the root type in any declaration, aside from those specified as `enum class` members.</span></span> <span data-ttu-id="d4894-111">此外，根类型不允许使用用户定义的构造函数。</span><span class="sxs-lookup"><span data-stu-id="d4894-111">In addition, no user-defined constructors are permitted for the root type.</span></span>

<span data-ttu-id="d4894-112">有四种类型的 `enum class` 成员声明：</span><span class="sxs-lookup"><span data-stu-id="d4894-112">There are four kinds of `enum class` member declarations:</span></span>

* <span data-ttu-id="d4894-113">简单类成员</span><span class="sxs-lookup"><span data-stu-id="d4894-113">simple class members</span></span>

* <span data-ttu-id="d4894-114">复杂类成员</span><span class="sxs-lookup"><span data-stu-id="d4894-114">complex class members</span></span>

* <span data-ttu-id="d4894-115">`enum class` 成员</span><span class="sxs-lookup"><span data-stu-id="d4894-115">`enum class` members</span></span>

* <span data-ttu-id="d4894-116">值成员。</span><span class="sxs-lookup"><span data-stu-id="d4894-116">value members.</span></span>

### <a name="simple-class-members"></a><span data-ttu-id="d4894-117">简单类成员</span><span class="sxs-lookup"><span data-stu-id="d4894-117">Simple class members</span></span>

<span data-ttu-id="d4894-118">简单的类成员声明定义了一个新的嵌套 "record" 类， (特意在此文档中未定义同名) 。</span><span class="sxs-lookup"><span data-stu-id="d4894-118">A simple class member declaration defines a new nested "record" class (intentionally left undefined in this document) with the same name.</span></span> <span data-ttu-id="d4894-119">嵌套类从根类型继承。</span><span class="sxs-lookup"><span data-stu-id="d4894-119">The nested class inherits from the root type.</span></span>

<span data-ttu-id="d4894-120">给定上面的示例代码，</span><span class="sxs-lookup"><span data-stu-id="d4894-120">Given the sample code above,</span></span>

```C#
enum class Shape
{
    Rectangle(float Width, float Length),
    Circle(float Radius)
}
```

<span data-ttu-id="d4894-121">`enum class`声明的语义等效于以下声明</span><span class="sxs-lookup"><span data-stu-id="d4894-121">the `enum class` declaration has semantics equivalent to the following declaration</span></span>

```C#
 abstract partial class Shape
{
    public record Rectangle(float Width, float Length) : Shape;
    public record Circle(float Radius) : Shape;
}
```

### <a name="complex-class-members"></a><span data-ttu-id="d4894-122">复杂类成员</span><span class="sxs-lookup"><span data-stu-id="d4894-122">Complex class members</span></span>

<span data-ttu-id="d4894-123">你还可以将整个类声明嵌套在一个 `enum class` 声明下。</span><span class="sxs-lookup"><span data-stu-id="d4894-123">You can also nest an entire class declaration below an `enum class` declaration.</span></span> <span data-ttu-id="d4894-124">它将被视为根类型的嵌套类。</span><span class="sxs-lookup"><span data-stu-id="d4894-124">It will be treated as a nested class of the root type.</span></span> <span data-ttu-id="d4894-125">语法允许任何类声明，但复杂类成员需要从直接包含声明中继承 `enum class` 。</span><span class="sxs-lookup"><span data-stu-id="d4894-125">The syntax allows any class declaration, but it is required for the complex class member to inherit from the direct containing `enum class` declaration.</span></span> 

### <a name="enum-class-members"></a><span data-ttu-id="d4894-126">`enum class` 成员</span><span class="sxs-lookup"><span data-stu-id="d4894-126">`enum class` members</span></span>

<span data-ttu-id="d4894-127">`enum classes` 可以彼此嵌套，例如</span><span class="sxs-lookup"><span data-stu-id="d4894-127">`enum classes` can be nested under each other, e.g.</span></span>

```C#
enum class Expr
{
    enum class Binary
    {
        Addition(Expr left, Expr right),
        Multiplication(Expr left, Expr right)
    }
}
```

<span data-ttu-id="d4894-128">这与顶级的语义几乎完全相同 `enum class` ，不同之处在于：嵌套枚举类定义了嵌套根类型，嵌套的枚举类下面的所有内容都是嵌套根类型的子类型，而不是顶级根类型。</span><span class="sxs-lookup"><span data-stu-id="d4894-128">This is almost identical to the semantics of a top-level `enum class`, except that the nested enum class defines a nested root type, and everything below the nested enum class is a subtype of the nested root type, instead of the top-level root type.</span></span>

```C#
abstract partial class Expr
{
    abstract partial class Binary : Expr
    {
        public record Addition(Expr left, Expr right) : Binary;
        public record Multiplication(Expr left, Expr right) : Binary;
    }
}
```

### <a name="value-members"></a><span data-ttu-id="d4894-129">值成员</span><span class="sxs-lookup"><span data-stu-id="d4894-129">Value members</span></span>

<span data-ttu-id="d4894-130">`enum classes` 还可以包含值成员。</span><span class="sxs-lookup"><span data-stu-id="d4894-130">`enum classes` can also contain value members.</span></span> <span data-ttu-id="d4894-131">值成员在根类型上定义同时返回根类型的公共只读静态属性，例如</span><span class="sxs-lookup"><span data-stu-id="d4894-131">Value members define public get-only static properties on the root type that also return the root type, e.g.</span></span>

```C#
enum class Color
{
    Red,
    Green
}
```

<span data-ttu-id="d4894-132">的属性等效于</span><span class="sxs-lookup"><span data-stu-id="d4894-132">has properties equivalent to</span></span>

```C#
partial abstract class Color
{
    public static Color Red => ...;
    public static Color Green => ...;
}
```

<span data-ttu-id="d4894-133">完整的语义被视为实现详细信息，但保证每个属性都返回一个唯一实例，并在重复调用时返回同一个实例。</span><span class="sxs-lookup"><span data-stu-id="d4894-133">The complete semantics are considered an implementation detail, but it is guaranteed that one unique instance will be returned for each property, and the same instance will be returned on repeated invocations.</span></span>


### <a name="switch-expression-and-patterns"></a><span data-ttu-id="d4894-134">Switch 表达式和模式</span><span class="sxs-lookup"><span data-stu-id="d4894-134">Switch expression and patterns</span></span>

<span data-ttu-id="d4894-135">建议对模式匹配和要处理的 switch 表达式进行一些调整 `enum classes` 。</span><span class="sxs-lookup"><span data-stu-id="d4894-135">There are some proposed adjustments to pattern matching and the switch expression to handle `enum classes`.</span></span> <span data-ttu-id="d4894-136">Switch 表达式可能已通过变量模式匹配类型，但对于引用类型，当前不会将 switch 表达式中的任何一组开关臂视为已完成，除非对参数的静态类型或子类型进行匹配。</span><span class="sxs-lookup"><span data-stu-id="d4894-136">Switch expressions can already match types through the variable pattern, but for currently for reference types, no set of switch arms in the switch expression are considered complete, except for matching against the static type of the argument, or a subtype.</span></span>

<span data-ttu-id="d4894-137">Switch 表达式将进行更改，这种情况下，如果的根类型 `enum class` 是 switch 表达式的参数的静态类型，并且存在一组匹配枚举的所有成员的模式，则会将该开关视为详尽。</span><span class="sxs-lookup"><span data-stu-id="d4894-137">Switch expressions would be changed such that, if the root type of an `enum class` is the static type of the argument to the switch expression, and there is a set of patterns matching all members of the enum, then the switch will be considered exhaustive.</span></span>

<span data-ttu-id="d4894-138">由于值成员不是常量，并且不定义新的静态类型，因此它们当前无法通过模式进行匹配。</span><span class="sxs-lookup"><span data-stu-id="d4894-138">Since value members are not constants and do not define new static types, they currently cannot be matched by pattern.</span></span> <span data-ttu-id="d4894-139">为了实现这一点，将添加使用常量模式语法的新模式，以允许匹配 `enum class` 值成员。</span><span class="sxs-lookup"><span data-stu-id="d4894-139">To make this possible, a new pattern using the constant pattern syntax will be added to allow match against `enum class` value members.</span></span> <span data-ttu-id="d4894-140">当且仅当模式匹配的参数和值成员返回的值相等时，才会将 match 定义为 "成功" `enum class` ，不过，实现不需要执行此检查。</span><span class="sxs-lookup"><span data-stu-id="d4894-140">The match is defined to succeed if and only if the argument to the pattern match and the value returned by the `enum class` value member would be reference equal, although the implementation is not required to perform this check.</span></span>


## <a name="open-questions"></a><span data-ttu-id="d4894-141">打开问题</span><span class="sxs-lookup"><span data-stu-id="d4894-141">Open questions</span></span>

- <span data-ttu-id="d4894-142">[] 通用类型算法如何表达 `enum class` 成员呢？</span><span class="sxs-lookup"><span data-stu-id="d4894-142">[ ] What does the common type algorithm say about `enum class` members?</span></span> <span data-ttu-id="d4894-143">这是有效的代码吗？</span><span class="sxs-lookup"><span data-stu-id="d4894-143">Is this valid code?</span></span>
    * `var x = b ? new Shape.Rectangle(...) : new Shape.Circle(...)`

- <span data-ttu-id="d4894-144">[] 仅为值成员添加新模式似乎非常繁重。</span><span class="sxs-lookup"><span data-stu-id="d4894-144">[ ] Adding a new pattern just for value members seems heavy handed.</span></span> <span data-ttu-id="d4894-145">是否有更通用的版本构造要有意义？</span><span class="sxs-lookup"><span data-stu-id="d4894-145">Is there a more general version construction that makes sense?</span></span>
    - <span data-ttu-id="d4894-146">[] 值成员还不能很好地映射到并行嵌套类构造，因为这样</span><span class="sxs-lookup"><span data-stu-id="d4894-146">[ ] Value members also do not map well to a parallel nested class construction because of this</span></span>

- <span data-ttu-id="d4894-147">[] 正在针对 `enum class` 静态类型为保证为固定时间的自变量进行切换？</span><span class="sxs-lookup"><span data-stu-id="d4894-147">[ ] Is switching against an argument with an `enum class` static type guaranteed to be constant-time?</span></span>

- <span data-ttu-id="d4894-148">[] 在 switch 表达式中是否应将 `enum class` es 视为已完成？</span><span class="sxs-lookup"><span data-stu-id="d4894-148">[ ] Should there be a way to make `enum class`es not be considered complete in the switch expression?</span></span> <span data-ttu-id="d4894-149">带有前缀 `virtual` ？</span><span class="sxs-lookup"><span data-stu-id="d4894-149">Prefix with `virtual`?</span></span>

- <span data-ttu-id="d4894-150">[] 应允许哪些修饰符 `enum class` ？</span><span class="sxs-lookup"><span data-stu-id="d4894-150">[ ] What modifiers should be permitted on `enum class`?</span></span>

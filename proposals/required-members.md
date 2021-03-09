---
ms.openlocfilehash: ba36f1c95ee399c90885c351edc48b079c3bf860
ms.sourcegitcommit: 8a5955168e85fec63b404189cbbe8ca79c4fb34c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/08/2021
ms.locfileid: "102470100"
---
# <a name="required-members"></a><span data-ttu-id="cc353-101">必需的成员</span><span class="sxs-lookup"><span data-stu-id="cc353-101">Required Members</span></span>

## <a name="summary"></a><span data-ttu-id="cc353-102">总结</span><span class="sxs-lookup"><span data-stu-id="cc353-102">Summary</span></span>

<span data-ttu-id="cc353-103">此建议添加了一种方法，用于指定在对象初始化过程中需要设置属性或字段，从而强制实例创建者为创建站点上的对象初始值设定项中的成员提供初始值。</span><span class="sxs-lookup"><span data-stu-id="cc353-103">This proposal adds a way of specifying that a property or field is required to be set during object initialization, forcing the instance creator to provide an initial value for the member in an object initializer at the creation site.</span></span>

## <a name="motivation"></a><span data-ttu-id="cc353-104">动机</span><span class="sxs-lookup"><span data-stu-id="cc353-104">Motivation</span></span>

<span data-ttu-id="cc353-105">目前，对象层次结构需要大量的样板才能在层次结构的所有级别上传输数据。</span><span class="sxs-lookup"><span data-stu-id="cc353-105">Object hierarchies today require a lot of boilerplate to carry data across all levels of the hierarchy.</span></span> <span data-ttu-id="cc353-106">让我们看看一个简单的层次结构，该层次结构 `Person` 中可能定义了： c # 8：</span><span class="sxs-lookup"><span data-stu-id="cc353-106">Let's look at a simple hierarchy involving a `Person` as might be defined in C# 8:</span></span>

```cs
class Person
{
    public string FirstName { get; }
    public string MiddleName { get; }
    public string LastName { get; }

    public Person(string firstName, string lastName, string? middleName = null)
    {
        FirstName = firstName;
        LastName = lastName;
        MiddleName = middleName ?? string.Empty;
    }
}

class Student : Person
{
    public int ID { get; }
    public Person(int id, string firstName, string lastName, string? middleName = null)
        : base(firstName, lastName, middleName)
    {
        ID = id;
    }
}
```

<span data-ttu-id="cc353-107">这里有很多重复操作：</span><span class="sxs-lookup"><span data-stu-id="cc353-107">There's lots of repetition going on here:</span></span>

1. <span data-ttu-id="cc353-108">在层次结构的根中，每个属性的类型必须重复两次，且名称必须重复四次。</span><span class="sxs-lookup"><span data-stu-id="cc353-108">At the root of the hierarchy, the type of each property had to be repeated twice, and the name had to be repeated four times.</span></span>
2. <span data-ttu-id="cc353-109">在派生级别，每个继承属性的类型必须重复一次，并且名称必须重复两次。</span><span class="sxs-lookup"><span data-stu-id="cc353-109">At the derived level, the type of each inherited property had to be repeated once, and the name had to be repeated twice.</span></span>

<span data-ttu-id="cc353-110">这是一个简单的层次结构，具有3个属性和1个继承级别，但这种类型的层次结构的许多真实示例更深层个，将更大和更大的属性累积到一起。</span><span class="sxs-lookup"><span data-stu-id="cc353-110">This is a simple hierarchy with 3 properties and 1 level of inheritance, but many real-world examples of these types of hierarchies go many levels deeper, accumulating larger and larger numbers of properties to pass along as they do so.</span></span> <span data-ttu-id="cc353-111">Roslyn 是一个这样的基本代码，例如，在 CSTs 和 Ast 的各种树类型中。</span><span class="sxs-lookup"><span data-stu-id="cc353-111">Roslyn is one such codebase, for example, in the various tree types that make our CSTs and ASTs.</span></span> <span data-ttu-id="cc353-112">此嵌套很繁琐，因为我们有代码生成器来生成这些类型的构造函数和定义，而许多客户则采用类似的方法解决问题。</span><span class="sxs-lookup"><span data-stu-id="cc353-112">This nesting is tedious enough that we have code generators to generate the constructors and definitions of these types, and many customers take similar approaches to the problem.</span></span> <span data-ttu-id="cc353-113">C # 9 引入了记录，在某些情况下，这样做可以更好地实现此操作：</span><span class="sxs-lookup"><span data-stu-id="cc353-113">C# 9 introduces records, which for some scenarios can make this better:</span></span>

```cs
record Person(string FirstName, string LastName, string MiddleName = "");
record Student(int ID, string FirstName, string LastName, string MiddleName = "") : Person(FirstName, LastName, MiddleName);
```

<span data-ttu-id="cc353-114">`record`消除了第一个重复源，但第二个重复源保持不变：遗憾的是，这是在层次结构增长时增加的重复项的源，并且是在层次结构中做出更改后要修复的重复项的最困难部分，因为它需要跟踪层次结构中的所有位置（甚至可能跨项目，甚至可能会破坏使用者）。</span><span class="sxs-lookup"><span data-stu-id="cc353-114">`record`s eliminate the first source of duplication, but the second source of duplication remains unchanged: unfortunately, this is the source of duplication that grows as the hierarchy grows, and is the most painful part of the duplication to fix up after making a change in the hierarchy as it required chasing the hierarchy through all of its locations, possibly even across projects and potentially breaking consumers.</span></span>

<span data-ttu-id="cc353-115">作为避免此重复的一种解决方法，我们一直在使用对象初始值设定项作为避免编写构造函数的方法。</span><span class="sxs-lookup"><span data-stu-id="cc353-115">As a workaround to avoid this duplication, we have long seen consumers embracing object initializers as a way of avoiding writing constructors.</span></span> <span data-ttu-id="cc353-116">然而，在 c # 9 之前，这有2个主要缺点：</span><span class="sxs-lookup"><span data-stu-id="cc353-116">Prior to C# 9, however, this had 2 major downsides:</span></span>

1. <span data-ttu-id="cc353-117">对象层次结构必须是完全可变的，并具有 `set` 每个属性的访问器。</span><span class="sxs-lookup"><span data-stu-id="cc353-117">The object hierarchy has to be fully mutable, with `set` accessors on every property.</span></span>
2. <span data-ttu-id="cc353-118">没有办法确保图形中对象的每个实例化都设置每个成员。</span><span class="sxs-lookup"><span data-stu-id="cc353-118">There is no way to ensure that every instantation of an object from the graph sets every member.</span></span>

<span data-ttu-id="cc353-119">通过引入访问器，c # 9 再次解决了第一个问题 `init` ：使用访问器时，可以在创建/初始化对象时设置这些属性，但不能对其进行设置。</span><span class="sxs-lookup"><span data-stu-id="cc353-119">C# 9 again addressed the first issue here, by introducing the `init` accessor: with it, these properties can be set on object creation/initialization, but not subsequently.</span></span> <span data-ttu-id="cc353-120">但是，我们仍会遇到第二个问题： c # 中的属性是可选的，因为 c # 1.0。</span><span class="sxs-lookup"><span data-stu-id="cc353-120">However, we again still have the second issue: properties in C# have been optional since C# 1.0.</span></span> <span data-ttu-id="cc353-121">在 c # 8.0 中引入的可以为 null 的引用类型，解决了此问题的一部分：如果构造函数未初始化不可为 null 的引用类型属性，则会向用户发出警告。</span><span class="sxs-lookup"><span data-stu-id="cc353-121">Nullable reference types, introduced in C# 8.0, addressed part of this issue: if a constructor does not initialize a non-nullable reference-type property, then the user is warned about it.</span></span> <span data-ttu-id="cc353-122">但是，这并不能解决问题：用户想要在构造函数中重复使用其类型的大部分内容，他们需要将属性设置 _为其_ 使用者。</span><span class="sxs-lookup"><span data-stu-id="cc353-122">However, this doesn't solve the problem: the user here wants to not repeat large parts of their type in the constructor, they want to pass the _requirement_ to set properties on to their consumers.</span></span> <span data-ttu-id="cc353-123">它也不会提供有关 `ID` 来自的任何警告 `Student` ，因为这是一个值类型。</span><span class="sxs-lookup"><span data-stu-id="cc353-123">It also doesn't provide any warnings about `ID` from `Student`, as that is a value type.</span></span> <span data-ttu-id="cc353-124">这些方案在数据库模型 Orm （如 EF Core）中极为常见，后者需要具有一个公共的无参数构造函数，但随后会根据属性的为空性来驱动行的为空性。</span><span class="sxs-lookup"><span data-stu-id="cc353-124">These scenarios are extremely common in database model ORMs, such as EF Core, which need to have a public parameterless constructor but then drive nullability of the rows based on the nullability of the properties.</span></span>

<span data-ttu-id="cc353-125">此建议旨在通过引入 c # 的新功能来解决这些问题：所需成员。</span><span class="sxs-lookup"><span data-stu-id="cc353-125">This proposal seeks to address these concerns by introducing a new feature to C#: required members.</span></span> <span data-ttu-id="cc353-126">必需的成员将需要由使用者（而不是类型创作者）进行初始化，并具有各种自定义项，以允许多个构造函数和其他方案的灵活性。</span><span class="sxs-lookup"><span data-stu-id="cc353-126">Required members will be required to be initialized by consumers, rather than by the type author, with various customizations to allow flexibility for multiple constructors and other scenarios.</span></span>

## <a name="detailed-design"></a><span data-ttu-id="cc353-127">详细设计</span><span class="sxs-lookup"><span data-stu-id="cc353-127">Detailed Design</span></span>

<span data-ttu-id="cc353-128">`class`、 `struct` 和 `record` 类型可以声明 _所需的 \_ 成员列表 \__。</span><span class="sxs-lookup"><span data-stu-id="cc353-128">`class`, `struct`, and `record` types gain the ability to declare a _required\_member\_list_.</span></span> <span data-ttu-id="cc353-129">此列表是被视为 _必需_ 的类型的所有属性和字段的列表，并且必须在构造和初始化类型实例的过程中进行初始化。</span><span class="sxs-lookup"><span data-stu-id="cc353-129">This list is the list of all the properties and fields of a type that are considered _required_, and must be initialized during the construction and initialization of an instance of the type.</span></span> <span data-ttu-id="cc353-130">类型会自动从其基类型继承这些列表，并提供无缝的体验，删除样本和重复代码。</span><span class="sxs-lookup"><span data-stu-id="cc353-130">Types inherit these lists from their base types automatically, providing a seemless experience that removes boilerplate and repetitive code.</span></span>

### <a name="required-modifier"></a><span data-ttu-id="cc353-131">`required` 组合键</span><span class="sxs-lookup"><span data-stu-id="cc353-131">`required` modifier</span></span>

<span data-ttu-id="cc353-132">我们将添加 `'required'` 到 _字段 \_ 修饰符_ 和 _属性 \_ 修饰符_ 中的修饰符列表。</span><span class="sxs-lookup"><span data-stu-id="cc353-132">We add `'required'` to the list of modifiers in _field\_modifier_ and _property\_modifier_.</span></span> <span data-ttu-id="cc353-133">类型的 _必需 \_ 成员列表 \__ 由已 `required` 应用到这些成员的所有成员组成。</span><span class="sxs-lookup"><span data-stu-id="cc353-133">The _required\_member\_list_ of a type is composed of all the members that have had `required` applied to them.</span></span> <span data-ttu-id="cc353-134">因此， `Person` 以前的类型现在如下所示：</span><span class="sxs-lookup"><span data-stu-id="cc353-134">Thus, the `Person` type from earlier now looks like this:</span></span>

```cs
public class Person
{
    // The default constructor requires that FirstName and LastName be set at construction time
    public required string FirstName { get; init; }
    public string MiddleName { get; init; } = "";
    public required string LastName { get; init; }
}
```

<span data-ttu-id="cc353-135">具有 _所需 \_ 成员列表 \__ 的类型上的所有构造函数都将自动播发 _该类型_ 的使用者必须初始化列表中的所有属性。</span><span class="sxs-lookup"><span data-stu-id="cc353-135">All constructors on a type that has a _required\_member\_list_ automatically advertise a _contract_ that consumers of the type must initialize all of the properties in the list.</span></span> <span data-ttu-id="cc353-136">构造函数需要一个协定，该协定需要的成员不能至少与构造函数本身具有相同的可访问性。</span><span class="sxs-lookup"><span data-stu-id="cc353-136">It is an error for a constructor to advertise a contract that requires a member that is not at least as accessible as the constructor itself.</span></span> <span data-ttu-id="cc353-137">例如：</span><span class="sxs-lookup"><span data-stu-id="cc353-137">For example:</span></span>

```cs
public class C
{
    public required int Prop { get; protected init; }

    // Advertises that Prop is required. This is fine, because the constructor is just as accessible as the property initer.
    protected C() {}

    // Error: ctor C(object) is more accessible than required property Prop.init.
    public C(object otherArg) {}
}
```

<span data-ttu-id="cc353-138">`required` 仅在 `class` 、 `struct` 和类型中有效 `record` 。</span><span class="sxs-lookup"><span data-stu-id="cc353-138">`required` is only valid in `class`, `struct`, and `record` types.</span></span> <span data-ttu-id="cc353-139">它在类型中无效 `interface` 。</span><span class="sxs-lookup"><span data-stu-id="cc353-139">It is not valid in `interface` types.</span></span>

### <a name="init-clauses"></a><span data-ttu-id="cc353-140">`init` 分组</span><span class="sxs-lookup"><span data-stu-id="cc353-140">`init` Clauses</span></span>

<span data-ttu-id="cc353-141">构造函数可以通过添加 `init` 指定要删除的成员的名称的子句，从其协定中移除所需成员。</span><span class="sxs-lookup"><span data-stu-id="cc353-141">A constructor can remove a required member from its contract by adding an `init` clause that specifies the name of the member to remove.</span></span> <span data-ttu-id="cc353-142">例如：</span><span class="sxs-lookup"><span data-stu-id="cc353-142">For example:</span></span>

```cs
public class C
{
    public required int Prop1 { get; init; }
    public required int Prop2 { get; init; }

    // Advertises that just Prop1 is required.
    public C() : init(Prop2)
    {
        Prop2 = 2;
        Console.WriteLine($"Prop2 is {Prop2}")
    }
}
```

<span data-ttu-id="cc353-143">Init 子句还可以为内联属性提供初始化值。</span><span class="sxs-lookup"><span data-stu-id="cc353-143">An init clause can also provide the initialization value for the property inline.</span></span> <span data-ttu-id="cc353-144">这些分配将在执行构造函数的主体之前运行，在基调用之后（如果存在）：</span><span class="sxs-lookup"><span data-stu-id="cc353-144">These assignments are run before the body of the constructor is executed, after the base call if one exists:</span></span>

```cs
public class C
{
    public required int Prop1 { get; init; }
    public required int Prop2 { get; init; }

    // Sets Prop2 to 2 before the constructor body is run
    public C() : init(Prop2 = 2)
    {
        Console.WriteLine($"Prop2 is {Prop1}")
    }
}
```

<span data-ttu-id="cc353-145">Init 子句可以使用速记语句删除所有要求 `init required` ：</span><span class="sxs-lookup"><span data-stu-id="cc353-145">An init clause can remove all requirements by using the `init required` shorthand:</span></span>

```cs
public class C
{
    public required int Prop1 { get; init; }
    public required int Prop2 { get; init; }

    // Advertises that there are no requirements
    public C() : init required
    {
        Prop1 = 1;
        Prop2 = 2;
    }
}
```

<span data-ttu-id="cc353-146">_所需的 \_ 成员 \_ 列表_ 在类型层次结构中链接在一起。</span><span class="sxs-lookup"><span data-stu-id="cc353-146">_required\_member\_lists_ chain across a type hierarchy.</span></span> <span data-ttu-id="cc353-147">构造函数的 _协定_ 不仅包括当前类型中的必需成员，还包括基类型中的必需成员以及它所实现的任何接口。</span><span class="sxs-lookup"><span data-stu-id="cc353-147">A constructor's _contract_ not only includes the required members from the current type, but also the required members from the base type and any interfaces that it implements.</span></span> <span data-ttu-id="cc353-148">如果派生的构造函数调用了从列表中删除部分成员的基构造函数，则派生的构造函数还将从列表中删除这些成员。</span><span class="sxs-lookup"><span data-stu-id="cc353-148">If derived constructor calls a base constructor that removes some of those members from the list, the derived constructor also removes those members from the list.</span></span>

```cs
public class Base
{
    public required int Prop1 { get; init; }
    public required int Prop2 { get; init; }

    public Base() : init(Prop1 = 1) {}
}

public class Derived
{
    public required int Prop3 { get; set; }
    public required int Prop4 { get; set; }

    // Only advertises that Prop2 and Prop3 are required, because `base()` removed Prop1 from the list, and the init clause removes Prop4
    public Derived : base() init(Prop4 = 4) { }
}
```

### <a name="initialization-requirement"></a><span data-ttu-id="cc353-149">初始化要求</span><span class="sxs-lookup"><span data-stu-id="cc353-149">Initialization Requirement</span></span>

<span data-ttu-id="cc353-150">Init 子句中指定的成员必须在构造函数主体的末尾明确赋值。</span><span class="sxs-lookup"><span data-stu-id="cc353-150">Members specified in an init clause must be definitely assigned at the end of the constructor body.</span></span> <span data-ttu-id="cc353-151">如果不是，则会生成错误。</span><span class="sxs-lookup"><span data-stu-id="cc353-151">If they are not, an error is produced.</span></span> <span data-ttu-id="cc353-152">若要支持更复杂的初始化逻辑，可以使用运算符取消此错误 `!` ：</span><span class="sxs-lookup"><span data-stu-id="cc353-152">To support more complicated initialization logic, this error can be suppressed using the `!` operator:</span></span>

```cs
public class C
{
    public required int Prop { get; set; }

    // Error: Prop is not definitely assigned at the end of the constructor body
    public C(int param) : init(Prop)
    {
        Initialize(param);
    }

    // No error: Prop! suppresses it
    public C() : init(Prop!)
        => Initialize(1);

    public void Initialize(int param) => Prop = param;
}
```

<span data-ttu-id="cc353-153">`!`运算符也可以应用于 `init required` ，以禁止检查类型的所有必需属性。</span><span class="sxs-lookup"><span data-stu-id="cc353-153">The `!` operator can also be applied to `init required`, to suppress the checking for all required properties on a type.</span></span>

```cs
public class C
{
    public required int Prop1 { get; init; }
    public required int Prop2 { get; init; }

    // No errors: init required! suppresses
    public C() : init required! {}
}
```

#### <a name="grammar"></a><span data-ttu-id="cc353-154">语法</span><span class="sxs-lookup"><span data-stu-id="cc353-154">Grammar</span></span>

<span data-ttu-id="cc353-155">的语法如下所示 `constructor_initializer` 进行修改：</span><span class="sxs-lookup"><span data-stu-id="cc353-155">The grammar for a `constructor_initializer` is modified as follows:</span></span>

```antlr
constructor_initializer
    : ':' constructor_chain
    | ':' init_clause
    | ':' constructor_chain init_clause
    ;

constructor_chain
    : 'base' '(' argument_list? ')'
    | 'this' '(' argument_list? ')'
    ;

init_clause
    : 'init' '(' init_argument_list ')'
    | 'init' 'required' '!'?
    ;

init_argument_list
    : init_argument (',' init_argument)*
    ;

init_argument
    : identifier init_argument_initializer?
    | identifier '!'
    ;

init_argument_initializer
    : '=' expression
    ;
```

### <a name="new-constraint"></a><span data-ttu-id="cc353-156">`new()` 约束</span><span class="sxs-lookup"><span data-stu-id="cc353-156">`new()` constraint</span></span>

<span data-ttu-id="cc353-157">如果某个类型具有无参数的构造函数（该构造函数用于播发 _协定_ ），则不允许将其替换为约束为的类型形参 `new()` ，因为泛型实例化无法确保满足要求。</span><span class="sxs-lookup"><span data-stu-id="cc353-157">A type with a parameterless constructor that advertises a _contract_ is not allowed to be substituted for a type parameter constrained to `new()`, as there is no way for the generic instantiation to ensure that the requirements are satisfied.</span></span>

## <a name="open-questions"></a><span data-ttu-id="cc353-158">未解决的问题</span><span class="sxs-lookup"><span data-stu-id="cc353-158">Open Questions</span></span>

### <a name="syntax-questions"></a><span data-ttu-id="cc353-159">语法问题</span><span class="sxs-lookup"><span data-stu-id="cc353-159">Syntax questions</span></span>

* <span data-ttu-id="cc353-160">`init`正确的词是正确的吗？</span><span class="sxs-lookup"><span data-stu-id="cc353-160">Is `init` the right word?</span></span> <span data-ttu-id="cc353-161">`init` 作为构造函数的后缀修饰符，如果我们想要对工厂重用它，还可能会干扰，并 `init` 使用前缀修饰符启用方法。</span><span class="sxs-lookup"><span data-stu-id="cc353-161">`init` as a postfix modifier on the constructor might interfere if we ever want to reuse it for factories and also enable `init` methods with a prefix modifier.</span></span> <span data-ttu-id="cc353-162">其他可能性：</span><span class="sxs-lookup"><span data-stu-id="cc353-162">Other possibilities:</span></span>
    * `set`
* <span data-ttu-id="cc353-163">`required`用于指定是否初始化所有成员的正确修饰符？</span><span class="sxs-lookup"><span data-stu-id="cc353-163">Is `required` the right modifier for specifying that all members are initialized?</span></span> <span data-ttu-id="cc353-164">其他建议：</span><span class="sxs-lookup"><span data-stu-id="cc353-164">Others suggested:</span></span>
    * `default`
    * `all`
    * <span data-ttu-id="cc353-165">使用！</span><span class="sxs-lookup"><span data-stu-id="cc353-165">With a !</span></span> <span data-ttu-id="cc353-166">指示复杂逻辑</span><span class="sxs-lookup"><span data-stu-id="cc353-166">to indicate complex logic</span></span>
* <span data-ttu-id="cc353-167">如果需要分隔符 `base` / `this` 和 `init` ？</span><span class="sxs-lookup"><span data-stu-id="cc353-167">Should we require a separator between the `base`/`this` and the `init`?</span></span>
    * <span data-ttu-id="cc353-168">`:` 机</span><span class="sxs-lookup"><span data-stu-id="cc353-168">`:` separator</span></span>
    * <span data-ttu-id="cc353-169">"，" 分隔符</span><span class="sxs-lookup"><span data-stu-id="cc353-169">',' separator</span></span>
* <span data-ttu-id="cc353-170">`required`权限是否正确？</span><span class="sxs-lookup"><span data-stu-id="cc353-170">Is `required` the right modifier?</span></span> <span data-ttu-id="cc353-171">建议的其他备选方案：</span><span class="sxs-lookup"><span data-stu-id="cc353-171">Other alternatives that have been suggested:</span></span>
    * `req`
    * `require`
    * `mustinit`
    * `must`
    * `explicit`

### <a name="init-clause-restrictions"></a><span data-ttu-id="cc353-172">Init 子句限制</span><span class="sxs-lookup"><span data-stu-id="cc353-172">Init clause restrictions</span></span>

<span data-ttu-id="cc353-173">是否应允许 `this` 在 init 子句中访问？</span><span class="sxs-lookup"><span data-stu-id="cc353-173">Should we allow access to `this` in the init clause?</span></span> <span data-ttu-id="cc353-174">如果要在 `init` 构造函数本身中分配的是简写形式，就好像应该这样做。</span><span class="sxs-lookup"><span data-stu-id="cc353-174">If we want the assignment in `init` to be a shorthand for assigning the member in the constructor itself, it seems like we should.</span></span>

<span data-ttu-id="cc353-175">此外，它是否会创建新的作用域（例如 `base()` ），或它是否与方法主体共享同一范围？</span><span class="sxs-lookup"><span data-stu-id="cc353-175">Additionally, does it create a new scope, like `base()` does, or does it share the same scope as the method body?</span></span> <span data-ttu-id="cc353-176">这对于某些因素（例如，init 子句可能需要访问的本地函数）或名称映射（如果 init 表达式通过参数引入变量）尤其重要 `out` 。</span><span class="sxs-lookup"><span data-stu-id="cc353-176">This is particularly important for things like local functions, which the init clause may want to access, or for name shadowing, if an init expression introduces a variable via `out` parameter.</span></span>

### <a name="base-requirement-chaining-representation-in-metadata"></a><span data-ttu-id="cc353-177">元数据中的基本要求链接表示形式</span><span class="sxs-lookup"><span data-stu-id="cc353-177">Base requirement chaining representation in metadata</span></span>

<span data-ttu-id="cc353-178">元数据表示形式的一个理想实现就是让每个构造函数标记其在某种程度上调用的基构造函数，这可以确保如果基类型和派生类型在不同的程序集中，则基程序集中的版本更新将准确地反映在派生类型的使用中，而不需要升级派生的程序集。</span><span class="sxs-lookup"><span data-stu-id="cc353-178">An ideal implementation of the metadata representation would have each constructor mark the base constructor that they call in some fashion, which would ensure that, if the base and derived types are in different assemblies, a version update in the base assembly would be accurately reflected in usage of the derived type without the derived assembly having to upgrade.</span></span> <span data-ttu-id="cc353-179">但是，我们无法将方法令牌编码为签名，因此我们必须查找其他一些编码策略。</span><span class="sxs-lookup"><span data-stu-id="cc353-179">However, we don't have a way to encode a method token into a signature, so we'd have to find some other encoding strategy.</span></span> <span data-ttu-id="cc353-180">此策略在很多情况下都是脆弱的，因此，只需在派生的构造函数的已删除成员列表中重复任何已删除的成员。</span><span class="sxs-lookup"><span data-stu-id="cc353-180">This strategy will be inherently fragile to a number of potential scenarios, so it may be more pragmatic to simply repeat any removed members in the removed member list of the derived constructor.</span></span>

### <a name="warning-vs-error"></a><span data-ttu-id="cc353-181">警告与错误</span><span class="sxs-lookup"><span data-stu-id="cc353-181">Warning vs Error</span></span>

<span data-ttu-id="cc353-182">不应将所需成员设置为警告或错误吗？</span><span class="sxs-lookup"><span data-stu-id="cc353-182">Should not setting a required member be a warning or an error?</span></span> <span data-ttu-id="cc353-183">当然，可以通过或类似来欺骗系统， `Activator.CreateInstance(typeof(C))` 这意味着我们可能无法完全保证始终设置所有属性。</span><span class="sxs-lookup"><span data-stu-id="cc353-183">It is certainly possible to trick the system, via `Activator.CreateInstance(typeof(C))` or similar, which means we may not be able to fully guarantee all properties are always set.</span></span> <span data-ttu-id="cc353-184">我们还允许通过使用在构造函数站点禁止诊断 `!` ，这通常不允许错误。</span><span class="sxs-lookup"><span data-stu-id="cc353-184">We also allow suppression of the diagnostics at the constructor-site by using the `!`, which we generally do not allow for errors.</span></span> <span data-ttu-id="cc353-185">但是，此功能类似于 readonly 字段或 init 属性，因为如果用户尝试在初始化后设置此类成员，则很难出错，但反射可能会规避它们。</span><span class="sxs-lookup"><span data-stu-id="cc353-185">However, the feature is similar to readonly fields or init properties, in that we hard error if users attempt to set such a member after initialization, but they can be circumvented by reflection.</span></span>

### <a name="silly-diagnostics"></a><span data-ttu-id="cc353-186">"愚蠢的" 诊断</span><span class="sxs-lookup"><span data-stu-id="cc353-186">"Silly" diagnostics</span></span>

<span data-ttu-id="cc353-187">给定以下代码：</span><span class="sxs-lookup"><span data-stu-id="cc353-187">Given this code:</span></span>

```cs
class C
{
    public required object? O;
    public C() : init(O = null) {}
}
```

<span data-ttu-id="cc353-188">应该发出一些标记为 " `O` 必需" 但不需要在合同中使用的诊断？</span><span class="sxs-lookup"><span data-stu-id="cc353-188">Should issue some kind of diagnostic that `O` is marked required, but never required in a contract?</span></span> <span data-ttu-id="cc353-189">开发人员可能会发现根据需要将属性标记为 "安全网络"。</span><span class="sxs-lookup"><span data-stu-id="cc353-189">Developers might find marking properties as required to be useful as a safety net.</span></span>

## <a name="discussed-questions"></a><span data-ttu-id="cc353-190">讨论的问题</span><span class="sxs-lookup"><span data-stu-id="cc353-190">Discussed Questions</span></span>

### <a name="level-of-enforcement-for-init-clauses"></a><span data-ttu-id="cc353-191">子句的强制级别 `init`</span><span class="sxs-lookup"><span data-stu-id="cc353-191">Level of enforcement for `init` clauses</span></span>

<span data-ttu-id="cc353-192">我们是否严格强制实施子句中指定的成员， `init` 而没有初始值设定项必须初始化所有成员？</span><span class="sxs-lookup"><span data-stu-id="cc353-192">Do we strictly enforce that members specified in a `init` clause without an initializer must initialize all members?</span></span> <span data-ttu-id="cc353-193">很有可能会出现这种情况，否则我们将创建一个简单的故障维修。</span><span class="sxs-lookup"><span data-stu-id="cc353-193">It seems likely that we do, otherwise we create an easy pit-of-failure.</span></span> <span data-ttu-id="cc353-194">但是，我们还 `MemberNotNull` 在 c # 9 中重新引入了我们解决的相同问题的风险。</span><span class="sxs-lookup"><span data-stu-id="cc353-194">However, we also run the risk of reintroducing the same problems we solved with `MemberNotNull` in C# 9.</span></span> <span data-ttu-id="cc353-195">如果我们要严格实施此方法，则帮助器方法可能需要一种方法来指示它设置成员。</span><span class="sxs-lookup"><span data-stu-id="cc353-195">If we want to strictly enforce this, we will likely need a way for a helper method to indicate that it sets a member.</span></span> <span data-ttu-id="cc353-196">我们讨论过的一些可能的语法：</span><span class="sxs-lookup"><span data-stu-id="cc353-196">Some possible syntaxes we've discussed for this:</span></span>

* <span data-ttu-id="cc353-197">允许 `init` 方法。</span><span class="sxs-lookup"><span data-stu-id="cc353-197">Allow `init` methods.</span></span> <span data-ttu-id="cc353-198">仅允许从构造函数或其他方法调用这些方法 `init` ，并可以访问，就 `this` 像它在构造函数中 (ie、set `readonly` 和 `init` fields/properties) 。</span><span class="sxs-lookup"><span data-stu-id="cc353-198">These methods are only allowed to be called from a constructor or from another `init` method, and can access `this` as if it's in the constructor (ie, set `readonly` and `init` fields/properties).</span></span> <span data-ttu-id="cc353-199">此类方法可以与 `init` 子句组合在一起。</span><span class="sxs-lookup"><span data-stu-id="cc353-199">This can be combined with `init` clauses on such methods.</span></span> <span data-ttu-id="cc353-200">`init`如果子句中的成员在方法/构造函数的主体中明确赋值，则认为该子句已满足条件。</span><span class="sxs-lookup"><span data-stu-id="cc353-200">A `init` clause would be considered satisfied if the member in the clause is definitely assigned in the body of the method/constructor.</span></span> <span data-ttu-id="cc353-201">使用 `init` 包含成员计数的子句调用方法，将其分配给该成员。</span><span class="sxs-lookup"><span data-stu-id="cc353-201">Calling a method with a `init` clause that includes a member counts as assigning to that member.</span></span>
<span data-ttu-id="cc353-202">如果我们决定这是我们想要执行的路由（现在或将来），很可能不应将 `init` 用作构造函数上 init 子句的关键字，因为这会造成混淆。</span><span class="sxs-lookup"><span data-stu-id="cc353-202">If we do decided that this is a route we want to pursue, now or in the future, it seems likely that we should not use `init` as the keyword for the init clause on a constructor, as that would be confusing.</span></span>
* <span data-ttu-id="cc353-203">允许 `!` 操作员显式禁止显示警告/错误。</span><span class="sxs-lookup"><span data-stu-id="cc353-203">Allow the `!` operator to suppress the warning/error explicitly.</span></span> <span data-ttu-id="cc353-204">如果以复杂 (方式（例如在共享方法) 中）初始化成员，则用户可以将添加 `!` 到 init 子句以指示编译器不应检查初始化。</span><span class="sxs-lookup"><span data-stu-id="cc353-204">If initializing a member in a complicated way (such as in a shared method), the user can add a `!` to the init clause to indicate the compiler should not check for initialization.</span></span>

<span data-ttu-id="cc353-205">讨论后，我们喜欢运算符的概念 `!` 。</span><span class="sxs-lookup"><span data-stu-id="cc353-205">After discussion we like the idea of the `!` operator.</span></span> <span data-ttu-id="cc353-206">它允许用户特意了解更复杂的方案，同时还不会围绕 init 方法创建大型设计孔洞，并将每个方法注释为 X 或 Y 设置。 `!` 已选择，因为我们已将其用于禁止可为 null 的警告，并使用它来告知编译器 "我比你更聪明"。</span><span class="sxs-lookup"><span data-stu-id="cc353-206">It allows the user to be intentional about more complicated scenarios while also not creating a large design hole around init methods and annotating every method as setting members X or Y. `!` was chosen because we already use it for suppressing nullable warnings, and using it to tell the compiler "I'm smarter than you" in another place is a natural extension of the syntax form.</span></span>

### <a name="required-interface-members"></a><span data-ttu-id="cc353-207">必需的接口成员</span><span class="sxs-lookup"><span data-stu-id="cc353-207">Required interface members</span></span>

<span data-ttu-id="cc353-208">此建议不允许接口根据需要标记成员。</span><span class="sxs-lookup"><span data-stu-id="cc353-208">This proposal does not allow interfaces to mark members as required.</span></span> <span data-ttu-id="cc353-209">这样，我们就不必立即就泛型中的复杂方案进行判断 `new()` ，直接与工厂和一般构造相关。</span><span class="sxs-lookup"><span data-stu-id="cc353-209">This protects us from having to figure out complex scenarios around `new()` and interface constraints in generics right now, and is directly related to both factories and generic construction.</span></span> <span data-ttu-id="cc353-210">为了确保在此区域中具有设计空间，我们禁止 `required` 接口，并禁止使用 _所需 \_ 成员列表 \__ 替换约束为的类型参数 `new()` 。</span><span class="sxs-lookup"><span data-stu-id="cc353-210">In order to ensure that we have design space in this area, we forbid `required` in interfaces, and forbid types with _required\_member\_lists_ from being substituted for type parameters constrained to `new()`.</span></span> <span data-ttu-id="cc353-211">当我们想要深入了解工厂的通用构造方案时，我们可以重新访问此问题。</span><span class="sxs-lookup"><span data-stu-id="cc353-211">When we want to take a broader look at generic construction scenarios with factories, we can revisit this issue.</span></span>

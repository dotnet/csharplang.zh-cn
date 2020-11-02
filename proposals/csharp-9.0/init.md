---
ms.openlocfilehash: 79716f258ef080445c0f44af63fba4310fafdb4b
ms.sourcegitcommit: 22ab7021c3aa72a4c49bc8326deca389803422b5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/02/2020
ms.locfileid: "93189827"
---
<a name="init-only-setters"></a><span data-ttu-id="0a7c5-101">仅 Init 资源库</span><span class="sxs-lookup"><span data-stu-id="0a7c5-101">Init Only Setters</span></span>
=====

## <a name="summary"></a><span data-ttu-id="0a7c5-102">摘要</span><span class="sxs-lookup"><span data-stu-id="0a7c5-102">Summary</span></span>
<span data-ttu-id="0a7c5-103">此建议将仅 init 属性和索引器的概念添加到 c #。</span><span class="sxs-lookup"><span data-stu-id="0a7c5-103">This proposal adds the concept of init only properties and indexers to C#.</span></span> <span data-ttu-id="0a7c5-104">这些属性和索引器可以在创建对象时设置，但 `get` 仅在对象创建完成后才会有效。</span><span class="sxs-lookup"><span data-stu-id="0a7c5-104">These properties and indexers can be set at the point of object creation but become effectively `get` only once object creation has completed.</span></span>
<span data-ttu-id="0a7c5-105">这允许在 c # 中使用更灵活的不可变模型。</span><span class="sxs-lookup"><span data-stu-id="0a7c5-105">This allows for a much more flexible immutable model in C#.</span></span> 

## <a name="motivation"></a><span data-ttu-id="0a7c5-106">动机</span><span class="sxs-lookup"><span data-stu-id="0a7c5-106">Motivation</span></span>
<span data-ttu-id="0a7c5-107">自1.0 以来，用 c # 生成不可变数据的基础机制并未更改。</span><span class="sxs-lookup"><span data-stu-id="0a7c5-107">The underlying mechanisms for building immutable data in C# haven't changed since 1.0.</span></span> <span data-ttu-id="0a7c5-108">它们仍保留：</span><span class="sxs-lookup"><span data-stu-id="0a7c5-108">They remain:</span></span>

1. <span data-ttu-id="0a7c5-109">将字段声明为 `readonly` 。</span><span class="sxs-lookup"><span data-stu-id="0a7c5-109">Declaring fields as `readonly`.</span></span>
1. <span data-ttu-id="0a7c5-110">声明仅包含 `get` 访问器的属性。</span><span class="sxs-lookup"><span data-stu-id="0a7c5-110">Declaring properties that contain only a `get` accessor.</span></span>

<span data-ttu-id="0a7c5-111">这些机制在允许构造不可变的数据时有效，但它们通过向类型的样板代码添加成本并从对象和集合初始值设定项的功能中选择此类类型来实现此目的。</span><span class="sxs-lookup"><span data-stu-id="0a7c5-111">These mechanisms are effective at allowing the construction of immutable data but they do so by adding cost to the boilerplate code of types and opting such types out of features like object and collection initializers.</span></span> <span data-ttu-id="0a7c5-112">这意味着开发人员必须在易用性和不可变性之间进行选择。</span><span class="sxs-lookup"><span data-stu-id="0a7c5-112">This means developers must choose between ease of use and immutability.</span></span>

<span data-ttu-id="0a7c5-113">像声明类型一样，简单的不可变对象（例如 `Point` ）需要两个样板板代码，以支持构造。</span><span class="sxs-lookup"><span data-stu-id="0a7c5-113">A simple immutable object like `Point` requires twice as much boiler plate code to support construction as it does to declare the type.</span></span> <span data-ttu-id="0a7c5-114">类型越大，此样板板式的成本就越大：</span><span class="sxs-lookup"><span data-stu-id="0a7c5-114">The bigger the type the bigger the cost of this boiler plate:</span></span>

```cs
struct Point
{
    public int X { get; }
    public int Y { get; }

    public Point(int x, int y)
    {
        this.X = x;
        this.Y = y;
    }
}
```

<span data-ttu-id="0a7c5-115">`init`通过允许调用方在构造操作过程中改变成员，访问器使不可变对象更具灵活性。</span><span class="sxs-lookup"><span data-stu-id="0a7c5-115">The `init` accessor makes immutable objects more flexible by allowing the caller to mutate the members during the act of construction.</span></span> <span data-ttu-id="0a7c5-116">这意味着对象的不可变属性可以参与对象初始值设定项，因此不再需要类型中的所有构造函数样板。</span><span class="sxs-lookup"><span data-stu-id="0a7c5-116">That means the object's immutable properties can participate in object initializers and thus removes the need for all constructor boilerplate in the type.</span></span> <span data-ttu-id="0a7c5-117">`Point`类型现在只是：</span><span class="sxs-lookup"><span data-stu-id="0a7c5-117">The `Point` type is now simply:</span></span>

```cs
struct Point
{
    public int X { get; init; }
    public int Y { get; init; }
}
```

<span data-ttu-id="0a7c5-118">然后，使用者可以使用对象初始值设定项来创建对象。</span><span class="sxs-lookup"><span data-stu-id="0a7c5-118">The consumer can then use object initializers to create the object</span></span>

```cs
var p = new Point() { X = 42, Y = 13 };
```

## <a name="detailed-design"></a><span data-ttu-id="0a7c5-119">详细设计</span><span class="sxs-lookup"><span data-stu-id="0a7c5-119">Detailed Design</span></span>

### <a name="init-accessors"></a><span data-ttu-id="0a7c5-120">init 访问器</span><span class="sxs-lookup"><span data-stu-id="0a7c5-120">init accessors</span></span>
<span data-ttu-id="0a7c5-121">使用 `init` 访问器代替访问器来声明仅 init 属性 (或索引器) `set` ：</span><span class="sxs-lookup"><span data-stu-id="0a7c5-121">An init only property (or indexer) is declared by using the `init` accessor in place of the `set` accessor:</span></span>

```cs
class Student
{
    public string FirstName { get; init; }
    public string LastName { get; init; }
}
```

<span data-ttu-id="0a7c5-122">`init`在以下情况下，包含访问器的实例属性被视为可设置，但在本地函数或 lambda 中除外：</span><span class="sxs-lookup"><span data-stu-id="0a7c5-122">An instance property containing an `init` accessor is considered settable in the following circumstances, except when in a local function or lambda:</span></span>

- <span data-ttu-id="0a7c5-123">在对象初始值设定项期间</span><span class="sxs-lookup"><span data-stu-id="0a7c5-123">During an object initializer</span></span>
- <span data-ttu-id="0a7c5-124">`with`表达式初始值设定项期间</span><span class="sxs-lookup"><span data-stu-id="0a7c5-124">During a `with` expression initializer</span></span>
- <span data-ttu-id="0a7c5-125">在或上包含类型或派生类型的实例构造函数中 `this``base`</span><span class="sxs-lookup"><span data-stu-id="0a7c5-125">Inside an instance constructor of the containing or derived type, on `this` or `base`</span></span>
- <span data-ttu-id="0a7c5-126">`init`在任何属性的访问器中、在 `this` 或上`base`</span><span class="sxs-lookup"><span data-stu-id="0a7c5-126">Inside the `init` accessor of any property, on `this` or `base`</span></span>
- <span data-ttu-id="0a7c5-127">具有命名参数的属性用法</span><span class="sxs-lookup"><span data-stu-id="0a7c5-127">Inside attribute usages with named parameters</span></span>

<span data-ttu-id="0a7c5-128">在 `init` 此文档中，可设置访问器的时间统称为对象的构造阶段。</span><span class="sxs-lookup"><span data-stu-id="0a7c5-128">The times above in which the `init` accessors are settable are collectively referred to in this document as the construction phase of the object.</span></span>

<span data-ttu-id="0a7c5-129">这意味着， `Student` 可以通过以下方式使用类：</span><span class="sxs-lookup"><span data-stu-id="0a7c5-129">This means the `Student` class can be used in the following ways:</span></span>

```cs
var s = new Student()
{
    FirstName = "Jared",
    LastName = "Parosns",
};
s.LastName = "Parsons"; // Error: LastName is not settable
```

<span data-ttu-id="0a7c5-130">`init`访问器可设置的规则围绕类型层次结构扩展。</span><span class="sxs-lookup"><span data-stu-id="0a7c5-130">The rules around when `init` accessors are settable extend across type hierarchies.</span></span> <span data-ttu-id="0a7c5-131">如果该成员是可访问的，并且已知该对象处于构造阶段，则该成员是可设置的。</span><span class="sxs-lookup"><span data-stu-id="0a7c5-131">If the member is accessible and the object is known to be in the construction phase then the member is settable.</span></span> <span data-ttu-id="0a7c5-132">这就是：</span><span class="sxs-lookup"><span data-stu-id="0a7c5-132">That specifically allows for the following:</span></span>

```cs
class Base
{
    public bool Value { get; init; }
}

class Derived : Base
{
    Derived()
    {
        // Not allowed with get only properties but allowed with init
        Value = true;
    }
}

class Consumption
{
    void Example()
    {
        var d = new Derived() { Value = true; };
    }
}

```

<span data-ttu-id="0a7c5-133">在 `init` 调用访问器时，已知实例处于开放构造阶段。</span><span class="sxs-lookup"><span data-stu-id="0a7c5-133">At the point an `init` accessor is invoked, the instance is known to be in the open construction phase.</span></span> <span data-ttu-id="0a7c5-134">因此， `init` 除了一般 `set` 访问器可以执行的操作外，还允许访问器执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="0a7c5-134">Hence an `init` accessor is allowed to take the following actions in addition to what a normal `set` accessor can do:</span></span>

1. <span data-ttu-id="0a7c5-135">调用 `init` 通过或提供的其他访问器 `this``base`</span><span class="sxs-lookup"><span data-stu-id="0a7c5-135">Call other `init` accessors available through `this` or `base`</span></span>
1. <span data-ttu-id="0a7c5-136">`readonly`通过指定在同一类型中声明的字段`this`</span><span class="sxs-lookup"><span data-stu-id="0a7c5-136">Assign `readonly` fields declared on the same type through `this`</span></span>

```cs
class Complex
{
    readonly int Field1;
    int Field2;
    int Prop1 { get; init ; }
    int Prop2
    {
        get => 42;
        init
        {
            Field1 = 13; // okay
            Field2 = 13; // okay
            Prop1 = 13; // okay
        }
    }
}
```

<span data-ttu-id="0a7c5-137">将 `readonly` 字段从访问器分配的能力 `init` 限制为与访问器在同一类型上声明的那些字段。</span><span class="sxs-lookup"><span data-stu-id="0a7c5-137">The ability to assign `readonly` fields from an `init` accessor is limited to those fields declared on the same type as the accessor.</span></span> <span data-ttu-id="0a7c5-138">它不能用于分配 `readonly` 基类型中的字段。</span><span class="sxs-lookup"><span data-stu-id="0a7c5-138">It cannot be used to assign `readonly` fields in a base type.</span></span> <span data-ttu-id="0a7c5-139">此规则可确保类型作者保持对其类型的可变性行为的控制。</span><span class="sxs-lookup"><span data-stu-id="0a7c5-139">This rule ensures that type authors remain in control over the mutability behavior of their type.</span></span> <span data-ttu-id="0a7c5-140">不希望利用的开发人员 `init` 不会受到其他类型的影响，因为选择这样做：</span><span class="sxs-lookup"><span data-stu-id="0a7c5-140">Developers who do not wish to utilize `init` cannot be impacted from other types choosing to do so:</span></span>

```cs
class Base
{
    internal readonly int Field;
    internal int Property
    {
        get => Field;
        init => Field = value; // Okay
    }

    internal int OtherProperty { get; init; }
}

class Derived : Base
{
    internal readonly int DerivedField;
    internal int DerivedProperty
    {
        get => DerivedField;
        init
        {
            DerivedField = 42;  // Okay
            Property = 0;       // Okay
            Field = 13;         // Error Field is readonly
        }
    }

    public Derived()
    {
        Property = 42;  // Okay 
        Field = 13;     // Error Field is readonly
    }
}
```

<span data-ttu-id="0a7c5-141">当 `init` 在虚拟属性中使用时，所有重写也必须标记为 `init` 。</span><span class="sxs-lookup"><span data-stu-id="0a7c5-141">When `init` is used in a virtual property then all the overrides must also be marked as `init`.</span></span> <span data-ttu-id="0a7c5-142">同样，不能使用重写的简单 `set` `init` 。</span><span class="sxs-lookup"><span data-stu-id="0a7c5-142">Likewise it is not possible to override a simple `set` with `init`.</span></span>

```cs
class Base
{
    public virtual int Property { get; init; }
}

class C1 : Base
{
    public override int Property { get; init; }
}

class C2 : Base
{
    // Error: Property must have init to override Base.Property
    public override int Property { get; set; }
}
```

<span data-ttu-id="0a7c5-143">`interface`声明还可以 `init` 通过以下模式参与样式初始化：</span><span class="sxs-lookup"><span data-stu-id="0a7c5-143">An `interface` declaration can also participate in `init` style initialization via the following pattern:</span></span>

```cs
interface IPerson
{
    string Name { get; init; }
}

class Init
{
    void M<T>() where T : IPerson, new()
    {
        var local = new T()
        {
            Name = "Jared"
        };
        local.Name = "Jraed"; // Error
    }
}
```

<span data-ttu-id="0a7c5-144">此功能的限制：</span><span class="sxs-lookup"><span data-stu-id="0a7c5-144">Restrictions of this feature:</span></span>
- <span data-ttu-id="0a7c5-145">`init`访问器只能用于实例属性</span><span class="sxs-lookup"><span data-stu-id="0a7c5-145">The `init` accessor can only be used on instance properties</span></span>
- <span data-ttu-id="0a7c5-146">属性不能同时包含 `init` 和 `set` 访问器</span><span class="sxs-lookup"><span data-stu-id="0a7c5-146">A property cannot contain both an `init` and `set` accessor</span></span>
- <span data-ttu-id="0a7c5-147">如果基具有，则属性的所有重写都必须具有 `init` `init` 。</span><span class="sxs-lookup"><span data-stu-id="0a7c5-147">All overrides of a property must have `init` if the base had `init`.</span></span> <span data-ttu-id="0a7c5-148">此规则也适用于接口实现。</span><span class="sxs-lookup"><span data-stu-id="0a7c5-148">This rule also applies to interface implementation.</span></span>

### <a name="readonly-structs"></a><span data-ttu-id="0a7c5-149">只读结构</span><span class="sxs-lookup"><span data-stu-id="0a7c5-149">Readonly structs</span></span>

<span data-ttu-id="0a7c5-150">`init` 访问器 (自动实现的访问器和手动实现的访问器) 在的属性以及属性上都是允许的 `readonly struct` `readonly` 。</span><span class="sxs-lookup"><span data-stu-id="0a7c5-150">`init` accessors (both auto-implemented accessors and manually-implemented accessors) are permitted on properties of `readonly struct`s, as well as `readonly` properties.</span></span> <span data-ttu-id="0a7c5-151">`init` 不允许 `readonly` 在 `readonly` 和非类型中将访问器标记为自身 `readonly` `struct` 。</span><span class="sxs-lookup"><span data-stu-id="0a7c5-151">`init` accessors are not permitted to be marked `readonly` themselves, in both `readonly` and non-`readonly` `struct` types.</span></span>

```cs
readonly struct ReadonlyStruct1
{
    public int Prop1 { get; init; } // Allowed
}

struct ReadonlyStruct2
{
    public readonly int Prop2 { get; init; } // Allowed

    public int Prop3 { get; readonly init; } // Error
}
```

### <a name="metadata-encoding"></a><span data-ttu-id="0a7c5-152">元数据编码</span><span class="sxs-lookup"><span data-stu-id="0a7c5-152">Metadata encoding</span></span> 
<span data-ttu-id="0a7c5-153">属性 `init` 访问器将作为标准 `set` 访问器发出，并使用标记有 modreq 的返回类型 `IsExternalInit` 。</span><span class="sxs-lookup"><span data-stu-id="0a7c5-153">Property `init` accessors will be emitted as a standard `set` accessor with the return type marked with a modreq of `IsExternalInit`.</span></span> <span data-ttu-id="0a7c5-154">这是一种新类型，它具有以下定义：</span><span class="sxs-lookup"><span data-stu-id="0a7c5-154">This is a new type which will have the following definition:</span></span>

```cs
namespace System.Runtime.CompilerServices
{
    public sealed class IsExternalInit
    {
    }
}
```

<span data-ttu-id="0a7c5-155">编译器将按全名匹配类型。</span><span class="sxs-lookup"><span data-stu-id="0a7c5-155">The compiler will match the type by full name.</span></span> <span data-ttu-id="0a7c5-156">它不会在核心库中显示。</span><span class="sxs-lookup"><span data-stu-id="0a7c5-156">There is no requirement that it appear in the core library.</span></span> <span data-ttu-id="0a7c5-157">如果有多个使用此名称的类型，则编译器将按以下顺序关联中断：</span><span class="sxs-lookup"><span data-stu-id="0a7c5-157">If there are multiple types by this name then the compiler will tie break in the following order:</span></span>

1. <span data-ttu-id="0a7c5-158">正在编译的项目中定义的项</span><span class="sxs-lookup"><span data-stu-id="0a7c5-158">The one defined in the project being compiled</span></span>
1. <span data-ttu-id="0a7c5-159">System.private.corelib 中定义的</span><span class="sxs-lookup"><span data-stu-id="0a7c5-159">The one defined in corelib</span></span>

<span data-ttu-id="0a7c5-160">如果这些都不存在，则将发出类型多义性错误。</span><span class="sxs-lookup"><span data-stu-id="0a7c5-160">If neither of these exist then a type ambiguity error will be issued.</span></span>

<span data-ttu-id="0a7c5-161">`IsExternalInit`[此问题](https://github.com/dotnet/runtime/issues/34978)中进一步介绍了的设计</span><span class="sxs-lookup"><span data-stu-id="0a7c5-161">The design for `IsExternalInit` is futher covered in [this issue](https://github.com/dotnet/runtime/issues/34978)</span></span>

## <a name="questions"></a><span data-ttu-id="0a7c5-162">问题</span><span class="sxs-lookup"><span data-stu-id="0a7c5-162">Questions</span></span>

### <a name="breaking-changes"></a><span data-ttu-id="0a7c5-163">中断性变更</span><span class="sxs-lookup"><span data-stu-id="0a7c5-163">Breaking changes</span></span>
<span data-ttu-id="0a7c5-164">编码此功能的方式的主要透视点之一将会导致以下问题：</span><span class="sxs-lookup"><span data-stu-id="0a7c5-164">One of the main pivot points in how this feature is encoded will come down to the following question:</span></span> 

> <span data-ttu-id="0a7c5-165">它是否是要替换的二进制重大 `init` 更改 `set` ？</span><span class="sxs-lookup"><span data-stu-id="0a7c5-165">Is it a binary breaking change to replace `init` with `set`?</span></span>

<span data-ttu-id="0a7c5-166">将替换 `init` 为 `set` ，并使属性完全可写，而不会对非虚拟属性进行源重大更改。</span><span class="sxs-lookup"><span data-stu-id="0a7c5-166">Replacing `init` with `set` and thus making a property fully writable is never a source breaking change on a non-virtual property.</span></span> <span data-ttu-id="0a7c5-167">它只是扩展可写入属性的方案集。</span><span class="sxs-lookup"><span data-stu-id="0a7c5-167">It simply expands the set of scenarios where the property can be written.</span></span> <span data-ttu-id="0a7c5-168">相关的唯一行为是，这是否仍是二进制的重大更改。</span><span class="sxs-lookup"><span data-stu-id="0a7c5-168">The only behavior in question is whether or not this remains a binary breaking change.</span></span>

<span data-ttu-id="0a7c5-169">如果我们想要将更改 `init` 为 `set` 源和二进制兼容的更改，则会强制我们在下面的 modreq 与属性决策上做出选择，因为它会将 modreqs 排除为 soulution。</span><span class="sxs-lookup"><span data-stu-id="0a7c5-169">If we want to make the change of `init` to `set` a source and binary compatible change then it will force our hand on the modreq vs. attributes decision below because it will rule out modreqs as a soulution.</span></span> <span data-ttu-id="0a7c5-170">另一方面，这会被视为一种不感兴趣的做法，这将使 modreq 与属性决策更少有影响力。</span><span class="sxs-lookup"><span data-stu-id="0a7c5-170">If on the other hand this is seen as a non-interesting then this will make the modreq vs. attribute decision less impactful.</span></span>

<span data-ttu-id="0a7c5-171">**解决方法** LDM 不会出现这种情况。</span><span class="sxs-lookup"><span data-stu-id="0a7c5-171">**Resolution** This scenario is not seen as compelling by LDM.</span></span>

### <a name="modreqs-vs-attributes"></a><span data-ttu-id="0a7c5-172">Modreqs 与属性</span><span class="sxs-lookup"><span data-stu-id="0a7c5-172">Modreqs vs. attributes</span></span>
<span data-ttu-id="0a7c5-173">在 `init` 元数据过程中发出时，属性访问器的发出策略必须在使用属性或 modreqs 之间进行选择。</span><span class="sxs-lookup"><span data-stu-id="0a7c5-173">The emit strategy for `init` property accessors must choose between using attributes or modreqs when emitting during metadata.</span></span> <span data-ttu-id="0a7c5-174">它们具有不同的权衡要求。</span><span class="sxs-lookup"><span data-stu-id="0a7c5-174">These have different trade offs that need to be considered.</span></span>

<span data-ttu-id="0a7c5-175">使用 modreq 声明为属性集访问器批注意味着 CLI 兼容编译器将忽略访问器，除非它了解 modreq。</span><span class="sxs-lookup"><span data-stu-id="0a7c5-175">Annotating a property set accessor with a modreq declaration means CLI compliant compilers will ignore the accessor unless it understands the modreq.</span></span> <span data-ttu-id="0a7c5-176">这意味着仅识别的编译器 `init` 将读取成员。</span><span class="sxs-lookup"><span data-stu-id="0a7c5-176">That means only compilers aware of `init` will read the member.</span></span> <span data-ttu-id="0a7c5-177">不识别的编译器 `init` 会忽略 `set` 访问器，因此不会意外地将属性视为读/写。</span><span class="sxs-lookup"><span data-stu-id="0a7c5-177">Compilers unaware of `init` will ignore the `set` accessor and hence will not accidentally treat the property as read / write.</span></span> 

<span data-ttu-id="0a7c5-178">Modreq 的缺点 `init` 成为访问器的二进制签名的一部分 `set` 。</span><span class="sxs-lookup"><span data-stu-id="0a7c5-178">The downside of modreq is `init` becomes a part of the binary signature of the `set` accessor.</span></span> <span data-ttu-id="0a7c5-179">添加或删除 `init` 会破坏应用程序的二进制 compatbility。</span><span class="sxs-lookup"><span data-stu-id="0a7c5-179">Adding or removing `init` will break binary compatbility of the application.</span></span>

<span data-ttu-id="0a7c5-180">使用特性来批注 `set` 访问器意味着只有了解该属性的编译器知道它会限制对它的访问。</span><span class="sxs-lookup"><span data-stu-id="0a7c5-180">Using attributes to annotate the `set` accessor means that only compilers which understand the attribute will know to limit access to it.</span></span> <span data-ttu-id="0a7c5-181">不知道的编译器 `init` 会将其视为简单的读/写属性，并允许访问。</span><span class="sxs-lookup"><span data-stu-id="0a7c5-181">A compiler unaware of `init` will see it as a simple read / write property and allow access.</span></span>

<span data-ttu-id="0a7c5-182">这似乎表明，这一决定是在额外安全方面做出的选择，但代价是在二进制兼容性的代价。</span><span class="sxs-lookup"><span data-stu-id="0a7c5-182">This would seemingly mean this decision is a choice between extra safety at the expense of binary compatibility.</span></span> <span data-ttu-id="0a7c5-183">深入，额外的安全性并不是它的真正含义。</span><span class="sxs-lookup"><span data-stu-id="0a7c5-183">Digging in a bit the extra safety is not exactly what it seems.</span></span> <span data-ttu-id="0a7c5-184">它不会在以下情况下受到保护：</span><span class="sxs-lookup"><span data-stu-id="0a7c5-184">It will not protect against the following circumstances:</span></span>

1. <span data-ttu-id="0a7c5-185">成员反射 `public`</span><span class="sxs-lookup"><span data-stu-id="0a7c5-185">Reflection over `public` members</span></span>
1. <span data-ttu-id="0a7c5-186">使用 `dynamic`</span><span class="sxs-lookup"><span data-stu-id="0a7c5-186">The use of `dynamic`</span></span> 
1. <span data-ttu-id="0a7c5-187">不识别 modreqs 的编译器</span><span class="sxs-lookup"><span data-stu-id="0a7c5-187">Compilers that don't recognize modreqs</span></span>

<span data-ttu-id="0a7c5-188">还应考虑到，当我们完成 .NET 5 的 IL 验证规则时，它 `init` 将是这些规则之一。</span><span class="sxs-lookup"><span data-stu-id="0a7c5-188">It should also be considered that, when we complete the IL verification rules for .NET 5, `init` will be one of those rules.</span></span> <span data-ttu-id="0a7c5-189">这意味着只需验证发出可验证 IL 的编译器即可获得额外的强制。</span><span class="sxs-lookup"><span data-stu-id="0a7c5-189">That means extra enforcement will be gained from simply verifying compilers emitting verifiable IL.</span></span>

<span data-ttu-id="0a7c5-190">.NET (c #、F # 和 VB) 的主要语言将进行更新以识别这些 `init` 访问器。</span><span class="sxs-lookup"><span data-stu-id="0a7c5-190">The primary languages for .NET (C#, F# and VB) will all be updated to recognize these `init` accessors.</span></span> <span data-ttu-id="0a7c5-191">因此，这种情况下唯一现实的方案是： c # 9 编译器发出 `init` 属性，这些属性由较旧的工具集（如 c # 8、VB 15 等）发现。C # 8。</span><span class="sxs-lookup"><span data-stu-id="0a7c5-191">Hence the only realistic scenario here is when a C# 9 compiler emits `init` properties and they are seen by an older toolset such as C# 8, VB 15, etc ... C# 8.</span></span> <span data-ttu-id="0a7c5-192">这就是要考虑和权衡二进制兼容性的权衡。</span><span class="sxs-lookup"><span data-stu-id="0a7c5-192">That is the trade off to consider and weigh against binary compatibility.</span></span>

<span data-ttu-id="0a7c5-193">**注意** 本讨论主要适用于成员，不适用于字段。</span><span class="sxs-lookup"><span data-stu-id="0a7c5-193">**Note** This discussion primarily applies to members only, not to fields.</span></span> <span data-ttu-id="0a7c5-194">尽管 `init` 字段被 LDM 拒绝，但对于 modreq 与属性讨论仍有兴趣。</span><span class="sxs-lookup"><span data-stu-id="0a7c5-194">While `init` fields were rejected by LDM they are still interesting to consider for the modreq vs. attribute discussion.</span></span> <span data-ttu-id="0a7c5-195">`init`字段的功能是现有限制的 relaxation `readonly` 。</span><span class="sxs-lookup"><span data-stu-id="0a7c5-195">The `init` feature for fields is a relaxation of the existing restriction of `readonly`.</span></span> <span data-ttu-id="0a7c5-196">这意味着，如果将这些字段作为 `readonly` + a 属性发出，则较早的编译器不会出现错误，因为它们已被识别 `readonly` 。</span><span class="sxs-lookup"><span data-stu-id="0a7c5-196">That means if we emit the fields as `readonly` + an attribute there is no risk of older compilers mis-using the field because they would already recognize `readonly`.</span></span> <span data-ttu-id="0a7c5-197">因此，在这里使用 modreq 不会添加任何额外的保护。</span><span class="sxs-lookup"><span data-stu-id="0a7c5-197">Hence using a modreq here doesn't add any extra protection.</span></span>

<span data-ttu-id="0a7c5-198">**解决方法** 此功能将使用 modreq 对属性 setter 进行编码 `init` 。</span><span class="sxs-lookup"><span data-stu-id="0a7c5-198">**Resolution** The feature will use a modreq to encode the property `init` setter.</span></span> <span data-ttu-id="0a7c5-199">有说服力的因素是 (的，无特定顺序) ：</span><span class="sxs-lookup"><span data-stu-id="0a7c5-199">The compelling factors were (in no particular order):</span></span>

* <span data-ttu-id="0a7c5-200">希望防止旧的编译器违反 `init` 语义</span><span class="sxs-lookup"><span data-stu-id="0a7c5-200">Desire to discourage older compilers from violating `init` semantics</span></span>
* <span data-ttu-id="0a7c5-201">希望 `init` 在声明中添加或删除 `virtual` ，或者同时执行 `interface` 源和二进制的重大更改。</span><span class="sxs-lookup"><span data-stu-id="0a7c5-201">Desire to make adding or removing `init` in a `virtual` declaration or `interface` both a source and binary breaking change.</span></span>

<span data-ttu-id="0a7c5-202">如果对删除是二进制兼容更改，也不会有重要支持， `init` 则可以选择直接使用 modreq。</span><span class="sxs-lookup"><span data-stu-id="0a7c5-202">Given there was also no significant support for removing `init` to be a binary compatible change it made the choice of using modreq straight forward.</span></span>

### <a name="init-vs-initonly"></a><span data-ttu-id="0a7c5-203">init 与 initonly</span><span class="sxs-lookup"><span data-stu-id="0a7c5-203">init vs. initonly</span></span>
<span data-ttu-id="0a7c5-204">有三种语法形式，可在我们的 LDM 会议中获得重要的注意事项：</span><span class="sxs-lookup"><span data-stu-id="0a7c5-204">There were three syntax forms which got significant consideration during our LDM meeting:</span></span>

```cs
// 1. Use init 
int Option1 { get; init; }
// 2. Use init set
int Option2 { get; init set; }
// 3. Use initonly
int Option3 { get; initonly; }
```

<span data-ttu-id="0a7c5-205">**解决方法** LDM 中没有充分的语法。</span><span class="sxs-lookup"><span data-stu-id="0a7c5-205">**Resolution** There was no syntax which was overwhelmingly favored in LDM.</span></span>

<span data-ttu-id="0a7c5-206">需要注意的一点是，对语法的选择如何影响我们 `init` 在将来作为一般功能执行成员的能力。</span><span class="sxs-lookup"><span data-stu-id="0a7c5-206">One point which got significant attention was how the choice of syntax would impact our ability to do `init` members as a general feature in the future.</span></span>
<span data-ttu-id="0a7c5-207">如果选择选项1，则意味着在将来定义具有样式方法的属性是很困难的 `init` `get` 。</span><span class="sxs-lookup"><span data-stu-id="0a7c5-207">Choosing option 1 would mean that it would be difficult to define a property which had an `init` style `get` method in the future.</span></span> <span data-ttu-id="0a7c5-208">最终，如果我们决定以后使用常规成员继续操作，则 `init` 可以 `init` 在属性访问器列表中提供修饰符，并为提供一个简短的 `init set` 。</span><span class="sxs-lookup"><span data-stu-id="0a7c5-208">Eventually it was decided that if we decided to go forward with general `init` members in future, we could allow `init` to be a modifier in the property accessor list as well as a short hand for `init set`.</span></span> <span data-ttu-id="0a7c5-209">实质上，下面两个声明是相同的。</span><span class="sxs-lookup"><span data-stu-id="0a7c5-209">Essentially the following two declarations would be identical.</span></span>

```cs
int Property1 { get; init; }
int Property1 { get; init set; }
```

<span data-ttu-id="0a7c5-210">在 `init` 属性访问器列表中，决定以独立访问器的形式继续。</span><span class="sxs-lookup"><span data-stu-id="0a7c5-210">The decision was made to move forward with `init` as a standalone accessor in the property accessor list.</span></span>

### <a name="warn-on-failed-init"></a><span data-ttu-id="0a7c5-211">初始化失败时发出警告</span><span class="sxs-lookup"><span data-stu-id="0a7c5-211">Warn on failed init</span></span>
<span data-ttu-id="0a7c5-212">请考虑以下场景。</span><span class="sxs-lookup"><span data-stu-id="0a7c5-212">Consider the following scenario.</span></span> <span data-ttu-id="0a7c5-213">类型声明的 `init` 唯一成员未在构造函数中设置。</span><span class="sxs-lookup"><span data-stu-id="0a7c5-213">A type declares an `init` only member which is not set in the constructor.</span></span> <span data-ttu-id="0a7c5-214">如果构造对象的代码未能初始化值，是否应收到警告？</span><span class="sxs-lookup"><span data-stu-id="0a7c5-214">Should the code which constructs the object get a warning if they failed to initialize the value?</span></span>

<span data-ttu-id="0a7c5-215">此时，该字段将永远不会被设置，因此，可能会有许多相似之处和有关无法初始化数据的警告 `private` 。</span><span class="sxs-lookup"><span data-stu-id="0a7c5-215">At that point it is clear the field will never be set and hence has a lot of similarities with the warning around failing to initialize `private` data.</span></span> <span data-ttu-id="0a7c5-216">那么，在这种情况下面会出现一个警告，</span><span class="sxs-lookup"><span data-stu-id="0a7c5-216">Hence a warning would seemingly have some value here?</span></span>

<span data-ttu-id="0a7c5-217">不过，此警告有重大缺点：</span><span class="sxs-lookup"><span data-stu-id="0a7c5-217">There are significant downsides to this warning though:</span></span>
1. <span data-ttu-id="0a7c5-218">它会使更改到的兼容性故事复杂化 `readonly` `init` 。</span><span class="sxs-lookup"><span data-stu-id="0a7c5-218">It complicates the compatibility story of changing `readonly` to `init`.</span></span> 
1. <span data-ttu-id="0a7c5-219">它需要额外的元数据来表示需要由调用方初始化的成员。</span><span class="sxs-lookup"><span data-stu-id="0a7c5-219">It requires carrying additional metadata around to denote the members which are required to be initialized by the caller.</span></span>

<span data-ttu-id="0a7c5-220">此外，如果我们认为在这种情况下，我们认为在这种情况下，强制对象创建者收到警告/错误应对特定字段存在价值，这可能是一项常见功能。</span><span class="sxs-lookup"><span data-stu-id="0a7c5-220">Further if we believe there is value here in the overall scenario of forcing object creators to be warned / error'd about specific fields then this likely makes sense as a general feature.</span></span> <span data-ttu-id="0a7c5-221">没有理由只应限制为 `init` 成员。</span><span class="sxs-lookup"><span data-stu-id="0a7c5-221">There is no reason it should be limited to just `init` members.</span></span>

<span data-ttu-id="0a7c5-222">**解决方法** 字段和属性的使用不会出现警告 `init` 。</span><span class="sxs-lookup"><span data-stu-id="0a7c5-222">**Resolution** There will be no warning on consumption of `init` fields and properties.</span></span>

<span data-ttu-id="0a7c5-223">LDM 希望更广泛地讨论必填字段和属性的概念。</span><span class="sxs-lookup"><span data-stu-id="0a7c5-223">LDM wants to have a broader discussion on the idea of required fields and properties.</span></span> <span data-ttu-id="0a7c5-224">这可能会导致我们返回并重新考虑我们的 `init` 成员和验证的位置。</span><span class="sxs-lookup"><span data-stu-id="0a7c5-224">That may cause us to come back and reconsider our position on `init` members and validation.</span></span>

## <a name="allow-init-as-a-field-modifier"></a><span data-ttu-id="0a7c5-225">允许 init 作为字段修饰符</span><span class="sxs-lookup"><span data-stu-id="0a7c5-225">Allow init as a field modifier</span></span>
<span data-ttu-id="0a7c5-226">同样， `init` 也可以作为属性访问器，它也可以作为字段的指定，将其作为 `init` 属性提供。</span><span class="sxs-lookup"><span data-stu-id="0a7c5-226">In the same way `init` can serve as a property accessor it could also serve as a designation on fields to give them similar behaviors as `init` properties.</span></span>
<span data-ttu-id="0a7c5-227">这将允许在类型、派生类型或对象初始值设定项完成构造之前，对字段进行赋值。</span><span class="sxs-lookup"><span data-stu-id="0a7c5-227">That would allow for the field to be assigned before construction was complete by the type, derived types, or object initializers.</span></span>

```cs
class Student
{
    public init string FirstName;
    public init string LastName;
}

var s = new Student()
{
    FirstName = "Jarde",
    LastName = "Parsons",
}

s.FirstName = "Jared"; // Error FirstName is readonly
```

<span data-ttu-id="0a7c5-228">在元数据中，这些字段将以与字段相同的方式进行标记， `readonly` 但使用附加的属性或 modreq 指示它们是 `init` 样式字段。</span><span class="sxs-lookup"><span data-stu-id="0a7c5-228">In metadata these fields would be marked in the same way as `readonly` fields but with an additional attribute or modreq to indicate they are `init` style fields.</span></span> 

<span data-ttu-id="0a7c5-229">**解决方法** LDM 同意此建议是听起来的，但是总体情况下，这种情况并不完全相同。</span><span class="sxs-lookup"><span data-stu-id="0a7c5-229">**Resolution** LDM agrees this proposal is sound but overall the scenario felt disjoint from properties.</span></span> <span data-ttu-id="0a7c5-230">这里的决策是只继续进行 `init` 属性。</span><span class="sxs-lookup"><span data-stu-id="0a7c5-230">The decision was to proceed only with `init` properties for now.</span></span> <span data-ttu-id="0a7c5-231">由于 `init` 属性可以 `readonly` 在属性的声明类型上改变字段，因此它具有适当的灵活性级别。</span><span class="sxs-lookup"><span data-stu-id="0a7c5-231">This has a suitable level of flexibility as an `init` property can mutate a `readonly` field on the declaring type of the property.</span></span> <span data-ttu-id="0a7c5-232">如果有大量客户反馈来表明方案，则会很少见。</span><span class="sxs-lookup"><span data-stu-id="0a7c5-232">This will be reconsidered if there is significant customer feedback that justifies the scenario.</span></span>

### <a name="allow-init-as-a-type-modifier"></a><span data-ttu-id="0a7c5-233">允许 init 作为类型修饰符</span><span class="sxs-lookup"><span data-stu-id="0a7c5-233">Allow init as a type modifier</span></span>
<span data-ttu-id="0a7c5-234">在中 `readonly` ，可以将修饰符应用于 `struct` 来自动将所有字段声明为 `readonly` ， `init` 也可以在上声明唯一修饰符，并将 `struct` `class` 所有字段自动标记为 `init` 。</span><span class="sxs-lookup"><span data-stu-id="0a7c5-234">In the same way the `readonly` modifier can be applied to a `struct` to automatically declare all fields as `readonly`, the `init` only modifier can be declared on a `struct` or `class` to automatically mark all fields as `init`.</span></span>
<span data-ttu-id="0a7c5-235">这意味着以下两个类型声明是等效的：</span><span class="sxs-lookup"><span data-stu-id="0a7c5-235">This means the following two type declarations are equivalent:</span></span>

```cs
struct Point
{
    public init int X;
    public init int Y;
}

// vs. 

init struct Point
{
    public int X;
    public int Y;
}
```

<span data-ttu-id="0a7c5-236">**解决方法** 此功能在此处过于 *刻意* ，并与 `readonly struct` 它所基于的功能冲突。</span><span class="sxs-lookup"><span data-stu-id="0a7c5-236">**Resolution** This feature is too *cute* here and conflicts with the `readonly struct` feature on which it is based.</span></span> <span data-ttu-id="0a7c5-237">此 `readonly struct` 功能很简单，因为它适用于 `readonly` 所有成员：字段、方法等。此 `init struct` 功能仅适用于属性。</span><span class="sxs-lookup"><span data-stu-id="0a7c5-237">The `readonly struct` feature is simple in that it applies `readonly` to all members: fields, methods, etc ... The `init struct` feature would only apply to properties.</span></span> <span data-ttu-id="0a7c5-238">这实际上会使用户感到困惑。</span><span class="sxs-lookup"><span data-stu-id="0a7c5-238">This actually ends up making it confusing for users.</span></span> 

<span data-ttu-id="0a7c5-239">假设 `init` 只对某个类型的某些方面有效，我们拒绝将其作为类型修饰符。</span><span class="sxs-lookup"><span data-stu-id="0a7c5-239">Given that `init` is only valid on certain aspects of a type, we rejected the idea of having it as a type modifier.</span></span>

## <a name="considerations"></a><span data-ttu-id="0a7c5-240">注意事项</span><span class="sxs-lookup"><span data-stu-id="0a7c5-240">Considerations</span></span>

### <a name="compatibility"></a><span data-ttu-id="0a7c5-241">兼容性</span><span class="sxs-lookup"><span data-stu-id="0a7c5-241">Compatibility</span></span>
<span data-ttu-id="0a7c5-242">此 `init` 功能旨在与现有的 `get` 属性兼容。</span><span class="sxs-lookup"><span data-stu-id="0a7c5-242">The `init` feature is designed to be compatible with existing `get` only properties.</span></span> <span data-ttu-id="0a7c5-243">具体而言，它只是一个属性的完全累加性更改，该属性 `get` 仅在今天，但需要更多的 flexbile 对象创建语义。</span><span class="sxs-lookup"><span data-stu-id="0a7c5-243">Specifically it is meant to be a completely additive change for a property which is `get` only today but desires more flexbile object creation semantics.</span></span>

<span data-ttu-id="0a7c5-244">例如，考虑以下类型：</span><span class="sxs-lookup"><span data-stu-id="0a7c5-244">For example consider the following type:</span></span>

```cs
class Name
{
    public string First { get; }
    public string Last { get; }

    public Name(string first, string last)
    {
        First = first;
        Last = last;
    }
}
```

<span data-ttu-id="0a7c5-245">添加 `init` 到这些属性中是非重大更改：</span><span class="sxs-lookup"><span data-stu-id="0a7c5-245">Adding `init` to these properties is a non-breaking change:</span></span>

```cs
class Name
{
    public string First { get; init; }
    public string Last { get; init; }

    public Name(string first, string last)
    {
        First = first;
        Last = last;
    }
}
```

### <a name="il-verification"></a><span data-ttu-id="0a7c5-246">IL 验证</span><span class="sxs-lookup"><span data-stu-id="0a7c5-246">IL verification</span></span>
<span data-ttu-id="0a7c5-247">当 .NET Core 决定重新实现 IL 验证时，需要对规则进行调整，以便为成员提供帐户 `init` 。</span><span class="sxs-lookup"><span data-stu-id="0a7c5-247">When .NET Core decides to re-implement IL verification, the rules will need to be adjusted to account for `init` members.</span></span> <span data-ttu-id="0a7c5-248">这将需要包含在规则更改中，以便对数据进行非转变性的更改 `readonly` 。</span><span class="sxs-lookup"><span data-stu-id="0a7c5-248">This will need to be included in the rule changes for non-mutating acess to `readonly` data.</span></span>

<span data-ttu-id="0a7c5-249">IL 验证规则需要分为两部分：</span><span class="sxs-lookup"><span data-stu-id="0a7c5-249">The IL verification rules will need to be broken into two parts:</span></span> 

1. <span data-ttu-id="0a7c5-250">允许 `init` 成员设置 `readonly` 字段。</span><span class="sxs-lookup"><span data-stu-id="0a7c5-250">Allowing `init` members to set a `readonly` field.</span></span>
1. <span data-ttu-id="0a7c5-251">确定何时 `init` 可以合法地调用成员。</span><span class="sxs-lookup"><span data-stu-id="0a7c5-251">Determining when an `init` member can be legally called.</span></span>

<span data-ttu-id="0a7c5-252">第一种是对现有规则的简单调整。</span><span class="sxs-lookup"><span data-stu-id="0a7c5-252">The first is a simple adjustment to the existing rules.</span></span> <span data-ttu-id="0a7c5-253">可以学会使用 IL 验证程序来识别 `init` 成员，而只需考虑在 `readonly` 此类成员上可设置的字段 `this` 。</span><span class="sxs-lookup"><span data-stu-id="0a7c5-253">The IL verifier can be taught to recognize `init` members and from there it just needs to consider a `readonly` field to be settable on `this` in such a member.</span></span>

<span data-ttu-id="0a7c5-254">第二个规则更复杂。</span><span class="sxs-lookup"><span data-stu-id="0a7c5-254">The second rule is more complicated.</span></span> <span data-ttu-id="0a7c5-255">在对象初始值设定项的简单情况下，该规则是直接的。</span><span class="sxs-lookup"><span data-stu-id="0a7c5-255">In the simple case of object initializers the rule is straight forward.</span></span> <span data-ttu-id="0a7c5-256">`init`当表达式的结果 `new` 仍在堆栈上时，调用成员应合法。</span><span class="sxs-lookup"><span data-stu-id="0a7c5-256">It should be legal to call `init` members when the result of a `new` expression is still on the stack.</span></span> <span data-ttu-id="0a7c5-257">也就是说，在将值存储在本地数组元素或字段中，或将其作为参数传递给另一种方法时，它仍合法调用 `init` 成员。</span><span class="sxs-lookup"><span data-stu-id="0a7c5-257">That is until the value has been stored in a local, array element or field or passed as an argument to another method it will still be legal to call `init` members.</span></span> <span data-ttu-id="0a7c5-258">这可确保一旦表达式的结果 `new` 被发布到命名标识符 (而不是) ，就 `this` 不会再合法调用 `init` 成员。</span><span class="sxs-lookup"><span data-stu-id="0a7c5-258">This ensures that once the result of the `new` expression is published to a named identifier (other than `this`) then it will no longer be legal to call `init` members.</span></span> 

<span data-ttu-id="0a7c5-259">尽管我们混合了 `init` 成员、对象初始值设定项和，但这种情况更复杂 `await` 。</span><span class="sxs-lookup"><span data-stu-id="0a7c5-259">The more complicated case though is when we mix `init` members, object initializers and `await`.</span></span> <span data-ttu-id="0a7c5-260">这可能会导致新创建的对象暂时提升到状态机中，因而放入字段中。</span><span class="sxs-lookup"><span data-stu-id="0a7c5-260">That can cause the newly created object to be temporarily hoisted into a state machine and hence put into a field.</span></span>

```cs
var student = new Student() 
{
    Name = await SomeMethod()
};
```

<span data-ttu-id="0a7c5-261">在此之前，的结果 `new Student()` 将作为字段 hoised 到状态机中 `Name` 。</span><span class="sxs-lookup"><span data-stu-id="0a7c5-261">Here the result of `new Student()` will be hoised into a state machine as a field before the set of `Name` occurs.</span></span> <span data-ttu-id="0a7c5-262">编译器需要将此类已提升的字段标记为 IL 验证程序理解它们不是用户可访问的方式，因此不违反的预期语义 `init` 。</span><span class="sxs-lookup"><span data-stu-id="0a7c5-262">The compiler will need to mark such hoisted fields in a way that the IL verifier understands they're not user accessible and hence doesn't violate the intended semantics of `init`.</span></span>

### <a name="init-members"></a><span data-ttu-id="0a7c5-263">init 成员</span><span class="sxs-lookup"><span data-stu-id="0a7c5-263">init members</span></span>
<span data-ttu-id="0a7c5-264">`init`修饰符可以扩展以应用于所有实例成员。</span><span class="sxs-lookup"><span data-stu-id="0a7c5-264">The `init` modifier could be extended to apply to all instance members.</span></span> <span data-ttu-id="0a7c5-265">这会在 `init` 对象构造过程中通用化概念，并允许类型声明可在构造过程中 partipate 以初始化字段和属性的帮助器方法 `init` 。</span><span class="sxs-lookup"><span data-stu-id="0a7c5-265">This would generalize the concept of `init` during object construction and allow types to declare helper methods that could partipate in the construction process to initialize `init` fields and properties.</span></span>

<span data-ttu-id="0a7c5-266">此类成员将具有 `init` 访问器在此设计中执行的所有 restricions。</span><span class="sxs-lookup"><span data-stu-id="0a7c5-266">Such members would have all the restricions that an `init` accessor does in this design.</span></span> <span data-ttu-id="0a7c5-267">不过，这种需要是值得怀疑的，而这可以通过一种兼容的方式安全地添加在语言的未来版本中。</span><span class="sxs-lookup"><span data-stu-id="0a7c5-267">The need is questionable though and this can be safely added in a future version of the language in a compatible manner.</span></span>

### <a name="generate-three-accessors"></a><span data-ttu-id="0a7c5-268">生成三个访问器</span><span class="sxs-lookup"><span data-stu-id="0a7c5-268">Generate three accessors</span></span>
<span data-ttu-id="0a7c5-269">属性的一个可能实现 `init` 是 `init` 完全独立于 `set` 。</span><span class="sxs-lookup"><span data-stu-id="0a7c5-269">One potential implementation of `init` properties is to make `init` completely separate from `set`.</span></span> <span data-ttu-id="0a7c5-270">这意味着，一个属性可能具有三个不同的访问器： `get` 、 `set` 和 `init` 。</span><span class="sxs-lookup"><span data-stu-id="0a7c5-270">That means that a property can potentially have three different accessors: `get`, `set`, and `init`.</span></span>

<span data-ttu-id="0a7c5-271">这可能会带来的优势是，允许使用 modreq 来强制实现正确性，同时保持二进制兼容性。</span><span class="sxs-lookup"><span data-stu-id="0a7c5-271">This has the potential advantage of allowing the use of modreq to enforce correctness while maintaining binary compatibility.</span></span> <span data-ttu-id="0a7c5-272">实现大致如下所示：</span><span class="sxs-lookup"><span data-stu-id="0a7c5-272">The implementation would roughly be the following:</span></span>

1. <span data-ttu-id="0a7c5-273">`init`如果有，则始终发出访问器 `set` 。</span><span class="sxs-lookup"><span data-stu-id="0a7c5-273">An `init` accessor is always emitted if there is a `set`.</span></span> <span data-ttu-id="0a7c5-274">当开发人员未定义时，它只是对的引用 `set` 。</span><span class="sxs-lookup"><span data-stu-id="0a7c5-274">When not defined by the developer it is simply a reference to `set`.</span></span> 
1. <span data-ttu-id="0a7c5-275">对象初始值设定项中的属性集将始终使用（ `init` 如果存在），但如果缺少，则返回 `set` 。</span><span class="sxs-lookup"><span data-stu-id="0a7c5-275">The set of a property in an object initializer will always use `init` if present but fall back to `set` if it's missing.</span></span>

<span data-ttu-id="0a7c5-276">这意味着开发人员始终可以 `init` 从属性中安全地删除。</span><span class="sxs-lookup"><span data-stu-id="0a7c5-276">This means that a developer can always safely delete `init` from a property.</span></span> 

<span data-ttu-id="0a7c5-277">这种设计的缺点是，仅 `init` 当在出现时 **始终** 发出时才有用 `set` 。</span><span class="sxs-lookup"><span data-stu-id="0a7c5-277">The downside of this design is that is only useful if `init` is **always** emitted when there is a `set`.</span></span> <span data-ttu-id="0a7c5-278">如果以前删除了该语言 `init` ，则它必须假定它是，因此 `init` 必须始终发出。</span><span class="sxs-lookup"><span data-stu-id="0a7c5-278">The language can't know if `init` was deleted in the past, it has to assume it was and hence the `init` must always be emitted.</span></span> <span data-ttu-id="0a7c5-279">这会导致一种重要的元数据扩展，而不是在此处提供兼容性的代价。</span><span class="sxs-lookup"><span data-stu-id="0a7c5-279">That would cause a significant metadata expansion and is simply not worth the cost of the compatibility here.</span></span>

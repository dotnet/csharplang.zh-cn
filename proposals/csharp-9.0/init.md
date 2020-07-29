---
ms.openlocfilehash: 0642f28e921ea224a157394226d43df2f3f81a10
ms.sourcegitcommit: 0c25406d8a99064bb85d934bb32ffcf547753acc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/28/2020
ms.locfileid: "87297272"
---
<a name="init-only-setters"></a>仅 Init 资源库
=====

## <a name="summary"></a>摘要
此建议将仅 init 属性和索引器的概念添加到 c #。 这些属性和索引器可以在创建对象时设置，但 `get` 仅在对象创建完成后才会有效。
这允许在 c # 中使用更灵活的不可变模型。 

## <a name="motivation"></a>动机
自1.0 以来，用 c # 生成不可变数据的基础机制并未更改。 它们仍保留：

1. 将字段声明为 `readonly` 。
1. 声明仅包含 `get` 访问器的属性。

这些机制在允许构造不可变的数据时有效，但它们通过向类型的样板代码添加成本并从对象和集合初始值设定项的功能中选择此类类型来实现此目的。 这意味着开发人员必须在易用性和不可变性之间进行选择。

像声明类型一样，简单的不可变对象（例如 `Point` ）需要两个样板板代码，以支持构造。 类型越大，此样板板式的成本就越大：

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

`init`通过允许调用方在构造操作过程中改变成员，访问器使不可变对象更具灵活性。 这意味着对象的不可变属性可以参与对象初始值设定项，因此不再需要类型中的所有构造函数样板。 `Point`类型现在只是：

```cs
struct Point
{
    public int X { get; init; }
    public int Y { get; init; }
}
```

然后，使用者可以使用对象初始值设定项来创建对象。

```cs
var p = new Point() { X = 42, Y = 13 };
```

## <a name="detailed-design"></a>详细设计

### <a name="init-accessors"></a>init 访问器
Init only 属性（或索引器）是使用 `init` 访问器替代 `set` 访问器声明的：

```cs
class Student
{
    public string FirstName { get; init; }
    public string LastName { get; init; }
}
```

`init`在以下情况下，包含访问器的实例属性被视为可设置，但在本地函数或 lambda 中除外：

- 在对象初始值设定项期间
- `with`表达式初始值设定项期间
- 在或上包含类型或派生类型的实例构造函数中 `this``base`
- `init`在任何属性的访问器中、在 `this` 或上`base`
- 具有命名参数的属性用法

在 `init` 此文档中，可设置访问器的时间统称为对象的构造阶段。

这意味着， `Student` 可以通过以下方式使用类：

```cs
var s = new Student()
{
    FirstName = "Jared",
    LastName = "Parosns",
};
s.LastName = "Parsons"; // Error: LastName is not settable
```

`init`访问器可设置的规则围绕类型层次结构扩展。 如果该成员是可访问的，并且已知该对象处于构造阶段，则该成员是可设置的。 这就是：

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

在 `init` 调用访问器时，实例已知处于开放构造阶段。 因此， `init` 除了一般 `set` 访问器可以执行的操作外，还允许访问器执行以下操作：

1. 调用 `init` 通过或提供的其他访问器 `this``base`
1. `readonly`通过指定在同一类型中声明的字段`this`

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

将 `readonly` 字段从访问器分配的能力 `init` 限制为与访问器在同一类型上声明的那些字段。 它不能用于分配 `readonly` 基类型中的字段。 此规则可确保类型作者保持对其类型的可变性行为的控制。 不希望利用的开发人员 `init` 不会受到其他类型的影响，因为选择这样做：

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

当 `init` 在虚拟属性中使用时，所有重写也必须标记为 `init` 。 同样，不能使用重写的简单 `set` `init` 。

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

`interface`声明还可以 `init` 通过以下模式参与样式初始化：

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

此功能的限制：
- `init`访问器只能用于实例属性
- 属性不能同时包含 `init` 和 `set` 访问器
- 如果基具有，则属性的所有重写都必须具有 `init` `init` 。 此规则也适用于接口实现。

### <a name="metadata-encoding"></a>元数据编码 
属性 `init` 访问器将作为标准 `set` 访问器发出，并使用标记有 modreq 的返回类型 `IsExternalInit` 。 这是一种新类型，它具有以下定义：

```cs
namespace System.Runtime.CompilerServices
{
    public sealed class IsExternalInit
    {
    }
}
```

编译器将按全名匹配类型。 它不会在核心库中显示。 如果有多个使用此名称的类型，则编译器将按以下顺序关联中断：

1. 正在编译的项目中定义的项
1. System.private.corelib 中定义的

如果这些都不存在，则将发出类型多义性错误。

`IsExternalInit`[此问题](https://github.com/dotnet/runtime/issues/34978)中进一步介绍了的设计

## <a name="questions"></a>问题

### <a name="breaking-changes"></a>中断性变更
编码此功能的方式的主要透视点之一将会导致以下问题： 

> 它是否是要替换的二进制重大 `init` 更改 `set` ？

将替换 `init` 为 `set` ，并使属性完全可写，而不会对非虚拟属性进行源重大更改。 它只是扩展可写入属性的方案集。 相关的唯一行为是，这是否仍是二进制的重大更改。

如果我们想要将更改 `init` 为 `set` 源和二进制兼容的更改，则会强制我们在下面的 modreq 与属性决策上做出选择，因为它会将 modreqs 排除为 soulution。 另一方面，这会被视为一种不感兴趣的做法，这将使 modreq 与属性决策更少有影响力。

**解决方法**LDM 不会出现这种情况。

### <a name="modreqs-vs-attributes"></a>Modreqs 与属性
在 `init` 元数据过程中发出时，属性访问器的发出策略必须在使用属性或 modreqs 之间进行选择。 它们具有不同的权衡要求。

使用 modreq 声明为属性集访问器批注意味着 CLI 兼容编译器将忽略访问器，除非它了解 modreq。 这意味着仅识别的编译器 `init` 将读取成员。 不识别的编译器 `init` 会忽略 `set` 访问器，因此不会意外地将属性视为读/写。 

Modreq 的缺点 `init` 成为访问器的二进制签名的一部分 `set` 。 添加或删除 `init` 会破坏应用程序的二进制 compatbility。

使用特性来批注 `set` 访问器意味着只有了解该属性的编译器知道它会限制对它的访问。 不知道的编译器 `init` 会将其视为简单的读/写属性，并允许访问。

这似乎表明，这一决定是在额外安全方面做出的选择，但代价是在二进制兼容性的代价。 深入，额外的安全性并不是它的真正含义。 它不会在以下情况下受到保护：

1. 成员反射 `public`
1. 使用`dynamic` 
1. 不识别 modreqs 的编译器

还应考虑到，当我们完成 .NET 5 的 IL 验证规则时，它 `init` 将是这些规则之一。 这意味着只需验证发出可验证 IL 的编译器即可获得额外的强制。

.NET 的主要语言（c #、F # 和 VB）都将更新，以识别这些 `init` 访问器。 因此，这种情况下唯一现实的方案是： c # 9 编译器发出 `init` 属性，这些属性由较旧的工具集（如 c # 8、VB 15 等）发现。C # 8。 这就是要考虑和权衡二进制兼容性的权衡。

**注意**本讨论主要适用于成员，不适用于字段。 尽管 `init` 字段被 LDM 拒绝，但对于 modreq 与属性讨论仍有兴趣。 `init`字段的功能是现有限制的 relaxation `readonly` 。 这意味着，如果将这些字段作为 `readonly` + a 属性发出，则较早的编译器不会出现错误，因为它们已被识别 `readonly` 。 因此，在这里使用 modreq 不会添加任何额外的保护。

**解决方法**此功能将使用 modreq 对属性 setter 进行编码 `init` 。 有说服力的因素是（无特定顺序）：

* 希望防止旧的编译器违反 `init` 语义
* 希望 `init` 在声明中添加或删除 `virtual` ，或者同时执行 `interface` 源和二进制的重大更改。

如果对删除是二进制兼容更改，也不会有重要支持， `init` 则可以选择直接使用 modreq。

### <a name="init-vs-initonly"></a>init 与 initonly
有三种语法形式，可在我们的 LDM 会议中获得重要的注意事项：

```cs
// 1. Use init 
int Option1 { get; init; }
// 2. Use init set
int Option2 { get; init set; }
// 3. Use initonly
int Option3 { get; initonly; }
```

**解决方法**LDM 中没有充分的语法。

需要注意的一点是，对语法的选择如何影响我们 `init` 在将来作为一般功能执行成员的能力。
如果选择选项1，则意味着在将来定义具有样式方法的属性是很困难的 `init` `get` 。 最终，如果我们决定以后继续使用常规成员，我们就 `init` 可以允许在 `init` 属性访问器列表中提供修饰符，为提供一个简短的修饰符 `init set` 。 实质上，下面两个声明是相同的。

```cs
int Property1 { get; init; }
int Property1 { get; init set; }
```

在 `init` 属性访问器列表中，决定以独立访问器的形式继续。

### <a name="warn-on-failed-init"></a>初始化失败时发出警告
请考虑以下情形。 类型声明的 `init` 唯一成员未在构造函数中设置。 如果构造对象的代码未能初始化值，是否应收到警告？

此时，该字段将永远不会被设置，因此，可能会有许多相似之处和有关无法初始化数据的警告 `private` 。 那么，在这种情况下面会出现一个警告，

不过，此警告有重大缺点：
1. 它会使更改到的兼容性故事复杂化 `readonly` `init` 。 
1. 它需要额外的元数据来表示需要由调用方初始化的成员。

此外，如果我们认为在这种情况下，我们认为在这种情况下，强制对象创建者收到警告/错误应对特定字段存在价值，这可能是一项常见功能。 没有理由只应限制为 `init` 成员。

**解决方法**字段和属性的使用不会出现警告 `init` 。

LDM 希望更广泛地讨论必填字段和属性的概念。 这可能会导致我们返回并重新考虑我们的 `init` 成员和验证的位置。

## <a name="allow-init-as-a-field-modifier"></a>允许 init 作为字段修饰符
同样， `init` 也可以作为属性访问器，它也可以作为字段的指定，将其作为 `init` 属性提供。
这将允许在类型、派生类型或对象初始值设定项完成构造之前，对字段进行赋值。

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

在元数据中，这些字段将以与字段相同的方式进行标记， `readonly` 但使用附加的属性或 modreq 指示它们是 `init` 样式字段。 

**解决方法**LDM 同意此建议是听起来的，但是总体情况下，这种情况并不完全相同。 这里的决策是只继续进行 `init` 属性。 由于 `init` 属性可以 `readonly` 在属性的声明类型上改变字段，因此它具有适当的灵活性级别。 如果有大量客户反馈来表明方案，则会很少见。

### <a name="allow-init-as-a-type-modifier"></a>允许 init 作为类型修饰符
在中 `readonly` ，可以将修饰符应用于 `struct` 来自动将所有字段声明为 `readonly` ， `init` 也可以在上声明唯一修饰符，并将 `struct` `class` 所有字段自动标记为 `init` 。
这意味着以下两个类型声明是等效的：

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

**解决方法**此功能在此处过于*刻意*，并与 `readonly struct` 它所基于的功能冲突。 此 `readonly struct` 功能很简单，因为它适用于 `readonly` 所有成员：字段、方法等。此 `init struct` 功能仅适用于属性。 这实际上会使用户感到困惑。 

假设 `init` 只对某个类型的某些方面有效，我们拒绝将其作为类型修饰符。

## <a name="considerations"></a>注意事项

### <a name="compatibility"></a>兼容性
此 `init` 功能旨在与现有的 `get` 属性兼容。 具体而言，它只是一个属性的完全累加性更改，该属性 `get` 仅在今天，但需要更多的 flexbile 对象创建语义。

例如，考虑以下类型：

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

添加 `init` 到这些属性中是非重大更改：

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

### <a name="il-verification"></a>IL 验证
当 .NET Core 决定重新实现 IL 验证时，需要对规则进行调整，以便为成员提供帐户 `init` 。 这将需要包含在规则更改中，以便对数据进行非转变性的更改 `readonly` 。

IL 验证规则需要分为两部分： 

1. 允许 `init` 成员设置 `readonly` 字段。
1. 确定何时 `init` 可以合法地调用成员。

第一种是对现有规则的简单调整。 可以学会使用 IL 验证程序来识别 `init` 成员，而只需考虑在 `readonly` 此类成员上可设置的字段 `this` 。

第二个规则更复杂。 在对象初始值设定项的简单情况下，该规则是直接的。 `init`当表达式的结果 `new` 仍在堆栈上时，调用成员应合法。 也就是说，在将值存储在本地数组元素或字段中，或将其作为参数传递给另一种方法时，它仍合法调用 `init` 成员。 这可确保将表达式的结果 `new` 发布到命名标识符（而不是）后， `this` 它将不再合法调用 `init` 成员。 

尽管我们混合了 `init` 成员、对象初始值设定项和，但这种情况更复杂 `await` 。 这可能会导致新创建的对象暂时提升到状态机中，因而放入字段中。

```cs
var student = new Student() 
{
    Name = await SomeMethod()
};
```

在此之前，的结果 `new Student()` 将作为字段 hoised 到状态机中 `Name` 。 编译器需要将此类已提升的字段标记为 IL 验证程序理解它们不是用户可访问的方式，因此不违反的预期语义 `init` 。

### <a name="init-members"></a>init 成员
`init`修饰符可以扩展以应用于所有实例成员。 这会在 `init` 对象构造过程中通用化概念，并允许类型声明可在构造过程中 partipate 以初始化字段和属性的帮助器方法 `init` 。

此类成员将具有 `init` 访问器在此设计中执行的所有 restricions。 不过，这种需要是值得怀疑的，而这可以通过一种兼容的方式安全地添加在语言的未来版本中。

### <a name="generate-three-accessors"></a>生成三个访问器
属性的一个可能实现 `init` 是 `init` 完全独立于 `set` 。 这意味着，一个属性可能具有三个不同的访问器： `get` 、 `set` 和 `init` 。

这可能会带来的优势是，允许使用 modreq 来强制实现正确性，同时保持二进制兼容性。 实现大致如下所示：

1. `init`如果有，则始终发出访问器 `set` 。 当开发人员未定义时，它只是对的引用 `set` 。 
1. 对象初始值设定项中的属性集将始终使用（ `init` 如果存在），但如果缺少，则返回 `set` 。

这意味着开发人员始终可以 `init` 从属性中安全地删除。 

这种设计的缺点是，仅 `init` 当在出现时**始终**发出时才有用 `set` 。 如果以前删除了该语言 `init` ，则它必须假定它是，因此 `init` 必须始终发出。 这会导致一种重要的元数据扩展，而不是在此处提供兼容性的代价。

---
ms.openlocfilehash: ba36f1c95ee399c90885c351edc48b079c3bf860
ms.sourcegitcommit: 8a5955168e85fec63b404189cbbe8ca79c4fb34c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/08/2021
ms.locfileid: "102470100"
---
# <a name="required-members"></a>必需的成员

## <a name="summary"></a>总结

此建议添加了一种方法，用于指定在对象初始化过程中需要设置属性或字段，从而强制实例创建者为创建站点上的对象初始值设定项中的成员提供初始值。

## <a name="motivation"></a>动机

目前，对象层次结构需要大量的样板才能在层次结构的所有级别上传输数据。 让我们看看一个简单的层次结构，该层次结构 `Person` 中可能定义了： c # 8：

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

这里有很多重复操作：

1. 在层次结构的根中，每个属性的类型必须重复两次，且名称必须重复四次。
2. 在派生级别，每个继承属性的类型必须重复一次，并且名称必须重复两次。

这是一个简单的层次结构，具有3个属性和1个继承级别，但这种类型的层次结构的许多真实示例更深层个，将更大和更大的属性累积到一起。 Roslyn 是一个这样的基本代码，例如，在 CSTs 和 Ast 的各种树类型中。 此嵌套很繁琐，因为我们有代码生成器来生成这些类型的构造函数和定义，而许多客户则采用类似的方法解决问题。 C # 9 引入了记录，在某些情况下，这样做可以更好地实现此操作：

```cs
record Person(string FirstName, string LastName, string MiddleName = "");
record Student(int ID, string FirstName, string LastName, string MiddleName = "") : Person(FirstName, LastName, MiddleName);
```

`record`消除了第一个重复源，但第二个重复源保持不变：遗憾的是，这是在层次结构增长时增加的重复项的源，并且是在层次结构中做出更改后要修复的重复项的最困难部分，因为它需要跟踪层次结构中的所有位置（甚至可能跨项目，甚至可能会破坏使用者）。

作为避免此重复的一种解决方法，我们一直在使用对象初始值设定项作为避免编写构造函数的方法。 然而，在 c # 9 之前，这有2个主要缺点：

1. 对象层次结构必须是完全可变的，并具有 `set` 每个属性的访问器。
2. 没有办法确保图形中对象的每个实例化都设置每个成员。

通过引入访问器，c # 9 再次解决了第一个问题 `init` ：使用访问器时，可以在创建/初始化对象时设置这些属性，但不能对其进行设置。 但是，我们仍会遇到第二个问题： c # 中的属性是可选的，因为 c # 1.0。 在 c # 8.0 中引入的可以为 null 的引用类型，解决了此问题的一部分：如果构造函数未初始化不可为 null 的引用类型属性，则会向用户发出警告。 但是，这并不能解决问题：用户想要在构造函数中重复使用其类型的大部分内容，他们需要将属性设置 _为其_ 使用者。 它也不会提供有关 `ID` 来自的任何警告 `Student` ，因为这是一个值类型。 这些方案在数据库模型 Orm （如 EF Core）中极为常见，后者需要具有一个公共的无参数构造函数，但随后会根据属性的为空性来驱动行的为空性。

此建议旨在通过引入 c # 的新功能来解决这些问题：所需成员。 必需的成员将需要由使用者（而不是类型创作者）进行初始化，并具有各种自定义项，以允许多个构造函数和其他方案的灵活性。

## <a name="detailed-design"></a>详细设计

`class`、 `struct` 和 `record` 类型可以声明 _所需的 \_ 成员列表 \__。 此列表是被视为 _必需_ 的类型的所有属性和字段的列表，并且必须在构造和初始化类型实例的过程中进行初始化。 类型会自动从其基类型继承这些列表，并提供无缝的体验，删除样本和重复代码。

### <a name="required-modifier"></a>`required` 组合键

我们将添加 `'required'` 到 _字段 \_ 修饰符_ 和 _属性 \_ 修饰符_ 中的修饰符列表。 类型的 _必需 \_ 成员列表 \__ 由已 `required` 应用到这些成员的所有成员组成。 因此， `Person` 以前的类型现在如下所示：

```cs
public class Person
{
    // The default constructor requires that FirstName and LastName be set at construction time
    public required string FirstName { get; init; }
    public string MiddleName { get; init; } = "";
    public required string LastName { get; init; }
}
```

具有 _所需 \_ 成员列表 \__ 的类型上的所有构造函数都将自动播发 _该类型_ 的使用者必须初始化列表中的所有属性。 构造函数需要一个协定，该协定需要的成员不能至少与构造函数本身具有相同的可访问性。 例如：

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

`required` 仅在 `class` 、 `struct` 和类型中有效 `record` 。 它在类型中无效 `interface` 。

### <a name="init-clauses"></a>`init` 分组

构造函数可以通过添加 `init` 指定要删除的成员的名称的子句，从其协定中移除所需成员。 例如：

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

Init 子句还可以为内联属性提供初始化值。 这些分配将在执行构造函数的主体之前运行，在基调用之后（如果存在）：

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

Init 子句可以使用速记语句删除所有要求 `init required` ：

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

_所需的 \_ 成员 \_ 列表_ 在类型层次结构中链接在一起。 构造函数的 _协定_ 不仅包括当前类型中的必需成员，还包括基类型中的必需成员以及它所实现的任何接口。 如果派生的构造函数调用了从列表中删除部分成员的基构造函数，则派生的构造函数还将从列表中删除这些成员。

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

### <a name="initialization-requirement"></a>初始化要求

Init 子句中指定的成员必须在构造函数主体的末尾明确赋值。 如果不是，则会生成错误。 若要支持更复杂的初始化逻辑，可以使用运算符取消此错误 `!` ：

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

`!`运算符也可以应用于 `init required` ，以禁止检查类型的所有必需属性。

```cs
public class C
{
    public required int Prop1 { get; init; }
    public required int Prop2 { get; init; }

    // No errors: init required! suppresses
    public C() : init required! {}
}
```

#### <a name="grammar"></a>语法

的语法如下所示 `constructor_initializer` 进行修改：

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

### <a name="new-constraint"></a>`new()` 约束

如果某个类型具有无参数的构造函数（该构造函数用于播发 _协定_ ），则不允许将其替换为约束为的类型形参 `new()` ，因为泛型实例化无法确保满足要求。

## <a name="open-questions"></a>未解决的问题

### <a name="syntax-questions"></a>语法问题

* `init`正确的词是正确的吗？ `init` 作为构造函数的后缀修饰符，如果我们想要对工厂重用它，还可能会干扰，并 `init` 使用前缀修饰符启用方法。 其他可能性：
    * `set`
* `required`用于指定是否初始化所有成员的正确修饰符？ 其他建议：
    * `default`
    * `all`
    * 使用！ 指示复杂逻辑
* 如果需要分隔符 `base` / `this` 和 `init` ？
    * `:` 机
    * "，" 分隔符
* `required`权限是否正确？ 建议的其他备选方案：
    * `req`
    * `require`
    * `mustinit`
    * `must`
    * `explicit`

### <a name="init-clause-restrictions"></a>Init 子句限制

是否应允许 `this` 在 init 子句中访问？ 如果要在 `init` 构造函数本身中分配的是简写形式，就好像应该这样做。

此外，它是否会创建新的作用域（例如 `base()` ），或它是否与方法主体共享同一范围？ 这对于某些因素（例如，init 子句可能需要访问的本地函数）或名称映射（如果 init 表达式通过参数引入变量）尤其重要 `out` 。

### <a name="base-requirement-chaining-representation-in-metadata"></a>元数据中的基本要求链接表示形式

元数据表示形式的一个理想实现就是让每个构造函数标记其在某种程度上调用的基构造函数，这可以确保如果基类型和派生类型在不同的程序集中，则基程序集中的版本更新将准确地反映在派生类型的使用中，而不需要升级派生的程序集。 但是，我们无法将方法令牌编码为签名，因此我们必须查找其他一些编码策略。 此策略在很多情况下都是脆弱的，因此，只需在派生的构造函数的已删除成员列表中重复任何已删除的成员。

### <a name="warning-vs-error"></a>警告与错误

不应将所需成员设置为警告或错误吗？ 当然，可以通过或类似来欺骗系统， `Activator.CreateInstance(typeof(C))` 这意味着我们可能无法完全保证始终设置所有属性。 我们还允许通过使用在构造函数站点禁止诊断 `!` ，这通常不允许错误。 但是，此功能类似于 readonly 字段或 init 属性，因为如果用户尝试在初始化后设置此类成员，则很难出错，但反射可能会规避它们。

### <a name="silly-diagnostics"></a>"愚蠢的" 诊断

给定以下代码：

```cs
class C
{
    public required object? O;
    public C() : init(O = null) {}
}
```

应该发出一些标记为 " `O` 必需" 但不需要在合同中使用的诊断？ 开发人员可能会发现根据需要将属性标记为 "安全网络"。

## <a name="discussed-questions"></a>讨论的问题

### <a name="level-of-enforcement-for-init-clauses"></a>子句的强制级别 `init`

我们是否严格强制实施子句中指定的成员， `init` 而没有初始值设定项必须初始化所有成员？ 很有可能会出现这种情况，否则我们将创建一个简单的故障维修。 但是，我们还 `MemberNotNull` 在 c # 9 中重新引入了我们解决的相同问题的风险。 如果我们要严格实施此方法，则帮助器方法可能需要一种方法来指示它设置成员。 我们讨论过的一些可能的语法：

* 允许 `init` 方法。 仅允许从构造函数或其他方法调用这些方法 `init` ，并可以访问，就 `this` 像它在构造函数中 (ie、set `readonly` 和 `init` fields/properties) 。 此类方法可以与 `init` 子句组合在一起。 `init`如果子句中的成员在方法/构造函数的主体中明确赋值，则认为该子句已满足条件。 使用 `init` 包含成员计数的子句调用方法，将其分配给该成员。
如果我们决定这是我们想要执行的路由（现在或将来），很可能不应将 `init` 用作构造函数上 init 子句的关键字，因为这会造成混淆。
* 允许 `!` 操作员显式禁止显示警告/错误。 如果以复杂 (方式（例如在共享方法) 中）初始化成员，则用户可以将添加 `!` 到 init 子句以指示编译器不应检查初始化。

讨论后，我们喜欢运算符的概念 `!` 。 它允许用户特意了解更复杂的方案，同时还不会围绕 init 方法创建大型设计孔洞，并将每个方法注释为 X 或 Y 设置。 `!` 已选择，因为我们已将其用于禁止可为 null 的警告，并使用它来告知编译器 "我比你更聪明"。

### <a name="required-interface-members"></a>必需的接口成员

此建议不允许接口根据需要标记成员。 这样，我们就不必立即就泛型中的复杂方案进行判断 `new()` ，直接与工厂和一般构造相关。 为了确保在此区域中具有设计空间，我们禁止 `required` 接口，并禁止使用 _所需 \_ 成员列表 \__ 替换约束为的类型参数 `new()` 。 当我们想要深入了解工厂的通用构造方案时，我们可以重新访问此问题。

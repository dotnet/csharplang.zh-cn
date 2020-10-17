---
ms.openlocfilehash: c4168bb99a60688c65db0c146e20ee62c009e08e
ms.sourcegitcommit: f6a48200419ba9f03f5efc8500bfae610cc45abc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/15/2020
ms.locfileid: "92097672"
---

# <a name="discriminated-unions--enum-class"></a>可区分联合/ `enum class`

`enum class`es 是类型声明的一种新类型，有时称为可区分联合，其中每个可能的实例都列出了该类型，并且每个实例都是非重叠的。

`enum class`使用以下语法定义：

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

示例语法：

```C#
enum class Shape
{
    Rectangle(float Width, float Length),
    Circle(float Radius),
}
```

## <a name="semantics"></a>语义

`enum class`定义定义根类型，该类型是与声明同名的抽象类 `enum class` 和一组成员，其中每个成员都有一个类型，该类型是根类型的子类型。 如果有多个 `partial enum class` 定义，则所有成员都将被视为 enum 类定义的成员。 与用户定义的抽象类定义不同， `enum class` 根类型默认为 partial，并定义为具有默认的无*private*参数的构造函数。

请注意，由于根类型被定义为分部抽象类，因此还可以添加 *根类型* 的分部定义，其中允许使用类主体的标准语法形式。
但是，除了指定为成员的类型之外，任何类型都不能直接从根类型继承任何声明 `enum class` 。 此外，根类型不允许使用用户定义的构造函数。

有四种类型的 `enum class` 成员声明：

* 简单类成员

* 复杂类成员

* `enum class` 成员

* 值成员。

### <a name="simple-class-members"></a>简单类成员

简单的类成员声明定义了一个新的嵌套 "record" 类， (特意在此文档中未定义同名) 。 嵌套类从根类型继承。

给定上面的示例代码，

```C#
enum class Shape
{
    Rectangle(float Width, float Length),
    Circle(float Radius)
}
```

`enum class`声明的语义等效于以下声明

```C#
 abstract partial class Shape
{
    public record Rectangle(float Width, float Length) : Shape;
    public record Circle(float Radius) : Shape;
}
```

### <a name="complex-class-members"></a>复杂类成员

你还可以将整个类声明嵌套在一个 `enum class` 声明下。 它将被视为根类型的嵌套类。 语法允许任何类声明，但复杂类成员需要从直接包含声明中继承 `enum class` 。 

### <a name="enum-class-members"></a>`enum class` 成员

`enum classes` 可以彼此嵌套，例如

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

这与顶级的语义几乎完全相同 `enum class` ，不同之处在于：嵌套枚举类定义了嵌套根类型，嵌套的枚举类下面的所有内容都是嵌套根类型的子类型，而不是顶级根类型。

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

### <a name="value-members"></a>值成员

`enum classes` 还可以包含值成员。 值成员在根类型上定义同时返回根类型的公共只读静态属性，例如

```C#
enum class Color
{
    Red,
    Green
}
```

的属性等效于

```C#
partial abstract class Color
{
    public static Color Red => ...;
    public static Color Green => ...;
}
```

完整的语义被视为实现详细信息，但保证每个属性都返回一个唯一实例，并在重复调用时返回同一个实例。


### <a name="switch-expression-and-patterns"></a>Switch 表达式和模式

建议对模式匹配和要处理的 switch 表达式进行一些调整 `enum classes` 。 Switch 表达式可能已通过变量模式匹配类型，但对于引用类型，当前不会将 switch 表达式中的任何一组开关臂视为已完成，除非对参数的静态类型或子类型进行匹配。

Switch 表达式将进行更改，这种情况下，如果的根类型 `enum class` 是 switch 表达式的参数的静态类型，并且存在一组匹配枚举的所有成员的模式，则会将该开关视为详尽。

由于值成员不是常量，并且不定义新的静态类型，因此它们当前无法通过模式进行匹配。 为了实现这一点，将添加使用常量模式语法的新模式，以允许匹配 `enum class` 值成员。 当且仅当模式匹配的参数和值成员返回的值相等时，才会将 match 定义为 "成功" `enum class` ，不过，实现不需要执行此检查。


## <a name="open-questions"></a>打开问题

- [] 通用类型算法如何表达 `enum class` 成员呢？ 这是有效的代码吗？
    * `var x = b ? new Shape.Rectangle(...) : new Shape.Circle(...)`

- [] 仅为值成员添加新模式似乎非常繁重。 是否有更通用的版本构造要有意义？
    - [] 值成员还不能很好地映射到并行嵌套类构造，因为这样

- [] 正在针对 `enum class` 静态类型为保证为固定时间的自变量进行切换？

- [] 在 switch 表达式中是否应将 `enum class` es 视为已完成？ 带有前缀 `virtual` ？

- [] 应允许哪些修饰符 `enum class` ？

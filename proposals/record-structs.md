---
ms.openlocfilehash: a62f2efa5b0a38a408fc4aaba2860be22f5d161b
ms.sourcegitcommit: a9b70c6ee1117df36eb66cf5b8e45c47e6c4f12e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/17/2021
ms.locfileid: "98536254"
---
# <a name="record-structs"></a>记录结构

记录结构的语法如下所示：

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

记录结构类型是值类型，如其他结构类型。 它们隐式继承自类 `System.ValueType` 。
记录结构的修饰符和成员受到的限制与结构 (可访问性的限制相同，成员、无参数实例构造函数、 `base(...)` 实例构造函数初始值设定项、构造函数中的明确赋值、 `this` 析构函数 ) 。

请参见https://github.com/dotnet/csharplang/blob/master/spec/structs.md

但如果存在主构造函数，则允许记录结构的实例字段声明包含变量初始值设定项。

记录结构不能使用 `ref` 修饰符。

部分记录结构最多只能有一个分部类型声明可以提供 `parameter_list` 。
`parameter_list`不能为空。

记录结构参数不能 `ref` 使用 `out` 或 `this` 修饰符 (但 `in` `params` 允许) 。

## <a name="members-of-a-record-struct"></a>Record 结构的成员

除了在 record 结构体中声明的成员外，记录结构类型还具有附加的合成成员。
除非在 record 结构主体中声明了具有 "匹配" 签名的成员，或者继承了具有 "匹配" 签名的可访问的具体非虚拟成员，否则将合成成员。
如果两个成员具有相同的签名，或在继承方案中被视为 "隐藏"，则会将其视为匹配项。
请参见https://github.com/dotnet/csharplang/blob/master/spec/basic-concepts.md#signatures-and-overloading

记录结构的成员命名为 "Clone" 是错误的。

记录结构的实例字段具有不安全类型是错误的。

不允许记录结构声明析构函数。

合成成员如下所示：

### <a name="equality-members"></a>相等成员

合成相等性成员类似于此类型 (的记录类 `Equals` 、 `Equals` `object` 类型 `==` 和 `!=` 此类型的运算符) ，\
除缺少以外 `EqualityContract` ，空检查或继承。

Record 结构实现 `System.IEquatable<R>` 并包含合成的强类型重载， `Equals(R other)` 其中 `R` 是记录结构。
方法为 `public` 。
可以显式声明方法。 如果显式声明与预期的签名或辅助功能不匹配，则是错误的。

如果 `Equals(R other)` 是用户定义的 (未合成) 但 `GetHashCode` 不是，则会生成警告。

```C#
public bool Equals(R other);
```

`Equals(R)` `true` 当且仅当记录结构中的每个实例字段的值为时，才会返回， `fieldN` `System.Collections.Generic.EqualityComparer<TN>.Default.Equals(fieldN, other.fieldN)` 其中， `TN` 字段类型为 `true` 。

记录结构包括合成 `==` 运算符和与 `!=` 运算符等效的运算符，如下所示：
```C#
public static bool operator==(R r1, R r2)
    => r1.Equals(r2);
public static bool operator!=(R r1, R r2)
    => !(r1 == r2);
```
`Equals`运算符调用的方法 `==` 是 `Equals(R other)` 上面指定的方法。 `!=`运算符委托给 `==` 运算符。 如果显式声明了运算符，则是错误的。

Record 结构包括合成重写等效于声明为的方法：
```C#
public override bool Equals(object? obj);
```
如果显式声明了重写，则是错误的。 合成重写返回， `other is R temp && Equals(temp)` 其中 `R` 是记录结构。

Record 结构包括合成重写等效于声明为的方法：
```C#
public override int GetHashCode();
```
可以显式声明方法。

如果 `Equals(R)` 和中 `GetHashCode()` 的一个是显式声明的，而另一个方法不是显式的，则会报告警告。

的合成重写 `GetHashCode()` 返回 `int` 一个确定性函数的结果，该函数将 `System.Collections.Generic.EqualityComparer<TN>.Default.GetHashCode(fieldN)` 每个实例字段的值与的类型组合在 `fieldN` 一起 `TN` `fieldN` 。

例如，请考虑以下 record 结构：
```C#
record struct R1(T1 P1, T2 P2);
```

对于此记录结构，合成相等性成员将如下所示：
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

### <a name="printing-members-printmembers-and-tostring-methods"></a>打印成员： PrintMembers 和 ToString 方法

Record 结构包含与声明方法等效的合成方法：
```C#
private bool PrintMembers(System.Text.StringBuilder builder);
```

该方法将执行以下操作：
1. 对于每个记录结构的可打印成员 (非静态公共字段和可读属性成员) ，追加该成员的名称后跟 "="，后跟成员的值（用 "，" 分隔），
2. 如果 record 结构具有可打印成员，则返回 true。

对于具有值类型的成员，我们将使用可用于目标平台的最有效方法将其值转换为字符串表示形式。 目前意味着在 `ToString` 传递到之前调用 `StringBuilder.Append` 。

`PrintMembers`可以显式声明方法。
如果显式声明与预期的签名或辅助功能不匹配，则是错误的。

Record 结构包含与声明方法等效的合成方法：
```C#
public override string ToString();
```

可以显式声明方法。 如果显式声明与预期的签名或辅助功能不匹配，则是错误的。

合成方法：
1. 创建一个 `StringBuilder` 实例，
2. 将记录结构名称追加到生成器，后跟 "{"，
3. 调用记录结构的 `PrintMembers` 方法，为其提供生成器，后跟 "" （如果它返回 true），
4. 追加 "}"，
5. 返回生成器的内容 `builder.ToString()` 。

例如，请考虑以下 record 结构：

``` csharp
record struct R1(T1 P1, T2 P2);
```

对于此记录结构，合成打印成员应类似于：

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

## <a name="positional-record-struct-members"></a>位置记录结构成员

除了以上成员以外，使用参数列表记录结构 ( "位置记录" ) 合成其他成员与上述成员具有相同的条件。

### <a name="primary-constructor"></a>主构造函数

记录结构有一个公共构造函数，该构造函数的签名对应于类型声明的值参数。 这称为类型的主构造函数。 如果结构中已存在具有相同签名的主构造函数和构造函数，则是错误的。
不允许记录结构声明无参数的主构造函数。

如果存在主构造函数，则允许记录结构的实例字段声明包含变量初始值设定项。 在运行时，主构造函数会执行在记录结构体中显示的实例初始值设定项。

如果 record 结构具有主构造函数，则任何用户定义的构造函数（"复制构造函数" 除外）都必须具有显式 `this` 构造函数初始值设定项。

主构造函数的参数以及 record 结构的成员位于实例字段或属性的初始值设定项的范围内。 实例成员将是这些位置中的错误 (类似于当前在常规构造函数初始值设定项的作用域中的方式，但使用) 的错误，但主构造函数的参数将在范围内并且可用，并将隐藏成员。 静态成员也是可用的。

如果未读取主构造函数的参数，则会生成一个警告。

结构实例构造函数的明确分配规则适用于记录结构的主构造函数。 例如，以下是一个错误：

```csharp
record struct Pos(int X) // definite assignment error in primary constructor
{
    private int x;
    public int X { get { return x; } set { x = value; } } = X;
}
```

### <a name="properties"></a>属性

对于 record 结构声明的每个记录 struct 参数，都有一个对应的公共属性成员，其名称和类型取自值参数声明。

对于 record 结构：

* `get` `init` 如果 record 结构具有修饰符，则创建公共和自动属性 `readonly` ，否则创建 `get` `set` 。
  两种类型的 set 访问器都 (`set` 和 `init`) 视为 "匹配"。 因此，用户可以声明仅限初始属性来代替合成可变的属性。
  `abstract`具有匹配类型的继承属性被重写。
  如果继承的属性没有 `public` `get` 和 `set` / `init` 访问器，则是错误的。
  自动属性初始化为相应主构造函数参数的值。
  特性可应用于合成自动属性及其支持字段，方法是使用 `property:` 或 `field:` 在语法上应用于相应记录结构参数的属性的目标。  

### <a name="deconstruct"></a>析构

一个位置记录结构，其中至少有一个参数合成一个公共 void 返回实例方法， `Deconstruct` 该方法由主构造函数声明的每个参数的 out 参数声明调用。 析构方法的每个参数都具有与主构造函数声明的相应参数相同的类型。 方法的主体将析构方法的每个参数分配给一个实例成员访问同名的成员的值。
可以显式声明方法。 如果显式声明与预期的签名或辅助功能不匹配，或者是静态的，则是错误的。

# <a name="allow-with-expression-on-structs"></a>允许 `with` 在结构上的表达式

现在，表达式中的接收方可以 `with` 使用结构类型。

表达式的右侧 `with` 是一个 `member_initializer_list` 带有 *标识符* 的赋值序列的，它必须是接收方类型的可访问实例字段或属性。

对于结构类型为的接收方，接收方首先复制，然后 `member_initializer` 处理的方法与对转换结果的字段或属性访问的赋值相同。 按词法顺序处理赋值。

# <a name="improvements-on-records"></a>记录改进

## <a name="allow-record-class"></a>禁止 `record class`

记录类型的现有语法允许 `record class` 具有与相同的含义 `record` ：

```antlr
record_declaration
    : attributes? class_modifier* 'partial'? 'record' 'class'? identifier type_parameter_list?
      parameter_list? record_base? type_parameter_constraints_clause* record_body
    ;
```

## <a name="allow-user-defined-positional-members-to-be-fields"></a>允许用户定义的位置成员作为字段

请参见https://github.com/dotnet/csharplang/blob/master/meetings/2020/LDM-2020-10-05.md#changing-the-member-type-of-a-primary-constructor-parameter

存在一种后端兼容性问题，因此，我们可能会删除此功能，或者我们需要尽快实现它 (作为 bug 修复) 。

```csharp
public record Base
{
    public int Field;
}
public record Derived(int Field);
```

# <a name="open-questions"></a>打开问题

- 是否应禁止使用复制构造函数签名的用户定义的构造函数？
- 确认是否要将 PrintMembers 设计保留 (单独方法返回 `bool`) 
- 确认是否要禁止名为 "Clone" 的成员。
- 为什么在记录的实例字段中不允许安全类型？ 我假设我们还希望在记录结构中禁止。
- `with` 在泛型上？  (可能会影响记录结构的设计) 
- 确认不会允许 `record ref struct` (`IEquatable<RefStruct>` 和引用字段的问题) 
- 确认相等性成员的实现。 替代方法是合成 `bool Equals(R other)` `bool Equals(object? other)` 和运算符，只委托给 `ValueType.Equals` 。
- 确认是否要在存在主构造函数时允许字段初始值设定项。 我们还想要在) 时，允许无参数结构构造函数 (的激活器问题已明确固定？
- 我们想要对方法说多少 `Combine` ？


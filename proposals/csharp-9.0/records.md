---
ms.openlocfilehash: 3605661e9c17143c8df5b9b994a1f67800db006f
ms.sourcegitcommit: 7125a8428a23fadaf4c93b2fc1ec88019876eaa7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/31/2020
ms.locfileid: "93142153"
---

# <a name="records"></a>记录

本建议将跟踪 c # 9 记录功能的规范（由 c # 语言设计团队同意）。

记录的语法如下所示：

```antlr
record_declaration
    : attributes? class_modifier* 'partial'? 'record' identifier type_parameter_list?
      parameter_list? record_base? type_parameter_constraints_clause* record_body
    ;

record_base
    : ':' class_type argument_list?
    | ':' interface_type_list
    | ':' class_type argument_list? ',' interface_type_list
    ;

record_body
    : '{' class_member_declaration* '}' ';'?
    | ';'
    ;
```

记录类型是引用类型，类似于类声明。 如果不包含，记录将提供一个错误 `record_base` `argument_list` `record_declaration` `parameter_list` 。
部分记录最多只能有一个分部类型声明提供 `parameter_list` 。

记录参数不能 `ref` 使用 `out` 或 `this` 修饰符 (但 `in` `params` 允许) 。

## <a name="inheritance"></a>继承

除非类为 `object` ，且类不能从记录继承，否则记录不能从类继承。 记录可以继承自其他记录。

## <a name="members-of-a-record-type"></a>记录类型的成员

除了记录体中声明的成员，记录类型还具有附加的合成成员。
如果在记录正文中声明了具有 "匹配" 签名的成员，或者继承了具有 "匹配" 签名的可访问的具体非虚拟成员，则将合成成员。
如果两个成员具有相同的签名，或在继承方案中被视为 "隐藏"，则会将其视为匹配项。
将记录的成员命名为 "Clone" 是错误的。
如果记录的实例字段具有不安全类型，则是错误的。

合成成员如下所示：

### <a name="equality-members"></a>相等成员

如果记录是从派生的 `object` ，则记录类型包括合成的 readonly 属性，该属性等效于声明的属性，如下所示：
```C#
Type EqualityContract { get; };
```
`private`如果记录类型为，则属性为 `sealed` 。 否则，属性为 `virtual` 和 `protected` 。
可以显式声明属性。 如果显式声明与预期的签名或辅助功能不匹配，或者如果显式声明不允许在派生类型中重写它并且记录类型不是，则是错误的 `sealed` 。

如果记录类型是从基本记录类型派生的 `Base` ，则记录类型包括合成 readonly 属性，该属性等效于声明为的属性，如下所示：
```C#
protected override Type EqualityContract { get; };
```

可以显式声明属性。 如果显式声明与预期的签名或辅助功能不匹配，或者如果显式声明不允许在派生类型中重写它并且记录类型不是，则是错误的 `sealed` 。 如果合成的或显式声明的属性未在记录类型中重写具有此签名的属性，则该属性是错误的， `Base` 例如，如果在 `Base` 、密封或不是 ) 虚拟等中缺少该属性，则该属性 (。
合成属性返回 `typeof(R)` ，其中 `R` 是记录类型。

记录类型实现 `System.IEquatable<R>` 并包含合成型强类型重载， `Equals(R? other)` 其中 `R` 是记录类型。
方法为 `public` ，并且方法为， `virtual` 除非记录类型为 `sealed` 。
可以显式声明方法。 如果显式声明与预期的签名或辅助功能不匹配，或显式声明不允许在派生类型中重写，并且记录类型不是，则是错误的 `sealed` 。

如果 `Equals(R? other)` 是用户定义的 (未合成) 但 `GetHashCode` 不是，则会生成警告。

```C#
public virtual bool Equals(R? other);
```
`Equals(R?)` `true` 当且仅当以下各项都为时，合成返回 `true` ：
- `other` 不是 `null` ，并且
- 对于 `fieldN` 记录类型中不是继承的每个实例字段，其中的值 `System.Collections.Generic.EqualityComparer<TN>.Default.Equals(fieldN, other.fieldN)` `TN` 为字段类型，而
- 如果有基本记录类型，则的值 `base.Equals(other)` (对) 的非虚拟调用 `public virtual bool Equals(Base? other)` ; 否则为的值 `EqualityContract == other.EqualityContract` 。

记录类型包括合成 `==` 运算符和与 `!=` 运算符等效的运算符，如下所示：
```C#
public static bool operator==(R? r1, R? r2)
    => (object)r1 == r2 || (r1?.Equals(r2) ?? false);
public static bool operator!=(R? r1, R? r2)
    => !(r1 == r2);
```
`Equals`运算符调用的方法 `==` 是 `Equals(R? other)` 上面指定的方法。 `!=`运算符委托给 `==` 运算符。 如果显式声明了运算符，则是错误的。
    
如果记录类型是从基本记录类型派生的 `Base` ，则记录类型包括合成重写等效于如下所示的方法：
```C#
public sealed override bool Equals(Base? other);
```
如果显式声明了重写，则是错误的。 如果方法在记录类型中未重写具有相同签名的方法，则是错误的 `Base` (例如，如果方法在中缺失， `Base` 或者密封或不是虚拟的，等等 ) 。
合成重写返回 `Equals((object?)other)` 。

记录类型包括合成重写等效于如下所示的方法：
```C#
public override bool Equals(object? obj);
```
如果显式声明了重写，则是错误的。 如果该方法不重写 `object.Equals(object? obj)` (例如，由于中间基类型中的隐藏，等等 ) ，则是错误的。
合成重写返回， `Equals(other as R)` 其中 `R` 是记录类型。

记录类型包括合成重写等效于如下所示的方法：
```C#
public override int GetHashCode();
```
可以显式声明方法。
如果显式声明不允许在派生类型中重写它并且记录类型不是，则是错误的 `sealed` 。 如果合成的或显式声明的方法不会重写 `object.GetHashCode()` (例如，由于中间基类型中的隐藏等 ) ，则是错误的。
 
如果 `Equals(R?)` 和中 `GetHashCode()` 的一个是显式声明的，而另一个方法不是显式的，则会报告警告。

的合成重写 `GetHashCode()` 返回将 `int` 以下值组合在一起的确定性函数的结果：
- 对于 `fieldN` 记录类型中不是继承的每个实例字段，其中的值 `System.Collections.Generic.EqualityComparer<TN>.Default.GetHashCode(fieldN)` `TN` 为字段类型，而
- 如果有基本记录类型，则的值 `base.GetHashCode()` 为; 否则为的值 `System.Collections.Generic.EqualityComparer<System.Type>.Default.GetHashCode(EqualityContract)` 。

例如，请考虑以下记录类型：
```C#
record R1(T1 P1);
record R2(T1 P1, T2 P2) : R1(P1);
record R3(T1 P1, T2 P2, T3 P3) : R2(P1, P2);
```

对于这些记录类型，合成相等性成员将如下所示：
```C#
class R1 : IEquatable<R1>
{
    public T1 P1 { get; init; }
    protected virtual Type EqualityContract => typeof(R1);
    public override bool Equals(object? obj) => Equals(obj as R1);
    public virtual bool Equals(R1? other)
    {
        return !(other is null) &&
            EqualityContract == other.EqualityContract &&
            EqualityComparer<T1>.Default.Equals(P1, other.P1);
    }
    public static bool operator==(R1? r1, R1? r2)
        => (object)r1 == r2 || (r1?.Equals(r2) ?? false);
    public static bool operator!=(R1? r1, R1? r2)
        => !(r1 == r2);    
    public override int GetHashCode()
    {
        return Combine(EqualityComparer<Type>.Default.GetHashCode(EqualityContract),
            EqualityComparer<T1>.Default.GetHashCode(P1));
    }
}

class R2 : R1, IEquatable<R2>
{
    public T2 P2 { get; init; }
    protected override Type EqualityContract => typeof(R2);
    public override bool Equals(object? obj) => Equals(obj as R2);
    public sealed override bool Equals(R1? other) => Equals((object?)other);
    public virtual bool Equals(R2? other)
    {
        return base.Equals((R1?)other) &&
            EqualityComparer<T2>.Default.Equals(P2, other.P2);
    }
    public static bool operator==(R2? r1, R2? r2)
        => (object)r1 == r2 || (r1?.Equals(r2) ?? false);
    public static bool operator!=(R2? r1, R2? r2)
        => !(r1 == r2);    
    public override int GetHashCode()
    {
        return Combine(base.GetHashCode(),
            EqualityComparer<T2>.Default.GetHashCode(P2));
    }
}

class R3 : R2, IEquatable<R3>
{
    public T3 P3 { get; init; }
    protected override Type EqualityContract => typeof(R3);
    public override bool Equals(object? obj) => Equals(obj as R3);
    public sealed override bool Equals(R2? other) => Equals((object?)other);
    public virtual bool Equals(R3? other)
    {
        return base.Equals((R2?)other) &&
            EqualityComparer<T3>.Default.Equals(P3, other.P3);
    }
    public static bool operator==(R3? r1, R3? r2)
        => (object)r1 == r2 || (r1?.Equals(r2) ?? false);
    public static bool operator!=(R3? r1, R3? r2)
        => !(r1 == r2);    
    public override int GetHashCode()
    {
        return Combine(base.GetHashCode(),
            EqualityComparer<T3>.Default.GetHashCode(P3));
    }
}
```

### <a name="copy-and-clone-members"></a>复制和克隆成员

记录类型包含两个复制成员：

* 采用记录类型的单个自变量的构造函数。 它被称为 "复制构造函数"。
* 使用编译器保留名称的合成公共无参数实例 "clone" 方法

复制构造函数的目的是将状态从参数复制到正在创建的新实例。 此构造函数不会运行记录声明中存在的任何实例字段/属性初始值设定项。 如果未显式声明构造函数，则编译器将合成构造函数。 如果记录是密封的，则构造函数将为私有，否则将受到保护。
显式声明的复制构造函数必须是公共的或受保护的，除非记录是密封的。
构造函数必须执行的第一件事是调用基的复制构造函数，如果记录继承自 object，则调用无参数的对象构造函数。 如果用户定义的复制构造函数使用不满足此要求的隐式或显式构造函数初始值设定项，则会报告错误。
在调用基复制构造函数后，合成复制构造函数将在记录类型内隐式或显式声明的所有实例字段的值。 无论是显式还是隐式，复制构造函数的唯一存在都不会阻止自动添加默认实例构造函数。

如果基本记录中存在虚拟 "clone" 方法，合成的 "clone" 方法将重写它，并且如果支持 "协变返回" 功能和重写返回类型，则该方法的返回类型为当前的包含类型。 如果基本记录克隆方法是密封的，则会生成错误。
如果基本记录中不存在虚拟 "clone" 方法，则克隆方法的返回类型为包含类型，并且方法为虚拟，除非该记录是密封或抽象的。
如果包含的记录是抽象的，合成克隆方法也是抽象的。
如果 "clone" 方法不是抽象的，则会返回对复制构造函数的调用结果。 


### <a name="printing-members-printmembers-and-tostring-methods"></a>打印成员： PrintMembers 和 ToString 方法

如果记录是从派生的 `object` ，则记录包含一个合成方法，该方法等效于声明为的方法：
```C#
bool PrintMembers(System.Text.StringBuilder builder);
```
`private`如果记录类型为，则方法为 `sealed` 。 否则，方法为 `virtual` 和 `protected` 。

方法如下：
1. 对于每个记录的可打印成员 (非静态公共字段和可读属性成员) ，追加该成员的名称后跟 "="，后跟成员的值： `this.member` ，用 "，" 分隔，
2. 如果记录具有可打印成员，则返回 true。

如果记录类型是从基本记录派生的 `Base` ，则记录包括合成重写等效于声明为的方法：
```C#
protected override bool PrintMembers(StringBuilder builder);
```

如果记录没有可打印成员，则方法将调用 `PrintMembers` 具有一个参数的基方法， (其 `builder` 参数) 并返回结果。

否则，方法为：
1. 调用基 `PrintMembers` 方法，其中一个参数 (其 `builder` 参数) 。
2. 如果该 `PrintMembers` 方法返回 true，则将 "，" 追加到生成器中，
3. 对于每个记录的可打印成员，将追加后跟 "=" 后跟成员值的成员的名称： `this.member` (或 `this.member.ToString()` 值类型) ，用 "，" 分隔）。
4. 返回 true。

`PrintMembers`可以显式声明方法。
如果显式声明与预期的签名或辅助功能不匹配，或者如果显式声明不允许在派生类型中重写它并且记录类型不是，则是错误的 `sealed` 。

记录包含与声明方法等效的合成方法，如下所示：
```C#
public override string ToString();
```

可以显式声明方法。 如果显式声明与预期的签名或辅助功能不匹配，或者如果显式声明不允许在派生类型中重写它并且记录类型不是，则是错误的 `sealed` 。 如果合成的或显式声明的方法不会重写 `object.ToString()` (例如，由于中间基类型中的隐藏等 ) ，则是错误的。

合成方法：
1. 创建一个 `StringBuilder` 实例，
2. 将记录名称追加到生成器，后面跟有 "{"、
3. 调用记录的 `PrintMembers` 方法，为其提供生成器，后跟 "" （如果它返回 true），
4. 追加 "}"，
3. 返回生成器的内容 `builder.ToString()` 。

例如，请考虑以下记录类型：

``` csharp
record R1(T1 P1);
record R2(T1 P1, T2 P2, T3 P3) : R1(P1);
```

对于这些记录类型，合成打印成员应类似于：

```C#
class R1 : IEquatable<R1>
{
    public T1 P1 { get; init; }
    
    protected virtual bool PrintMembers(StringBuilder builder)
    {
        builder.Append(nameof(P1));
        builder.Append(" = ");
        builder.Append(this.P1); // or builder.Append(this.P1); if P1 has a value type
        
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

class R2 : R1, IEquatable<R2>
{
    public T2 P2 { get; init; }
    public T3 P3 { get; init; }
    
    protected override void PrintMembers(StringBuilder builder)
    {
        if (base.PrintMembers(builder))
            builder.Append(", ");
            
        builder.Append(nameof(P2));
        builder.Append(" = ");
        builder.Append(this.P2); // or builder.Append(this.P2); if P2 has a value type
        
        builder.Append(", ");
        
        builder.Append(nameof(P3));
        builder.Append(" = ");
        builder.Append(this.P3); // or builder.Append(this.P3); if P3 has a value type
        
        return true;
    }
    
    public override string ToString()
    {
        var builder = new StringBuilder();
        builder.Append(nameof(R2));
        builder.Append(" { ");

        if (PrintMembers(builder))
            builder.Append(" ");

        builder.Append("}");
        return builder.ToString();
    }
}
```

## <a name="positional-record-members"></a>位置记录成员

除了以上成员以外，具有参数列表 ( "位置记录" 的记录 ) 合成其他成员与上述成员具有相同的条件。

### <a name="primary-constructor"></a>主构造函数

记录类型具有公共构造函数，该构造函数的签名对应于类型声明的值参数。 这称为类型的主构造函数，并导致禁止显示隐式声明的默认类构造函数（如果存在）。 类中已存在具有相同签名的主构造函数和构造函数是错误的。

在运行时，主构造函数

1. 执行出现在类体中的实例初始值设定项

1. 用子句中提供的参数 `record_base` （如果存在）调用基类构造函数

如果记录具有主构造函数，则任何用户定义的构造函数（"复制构造函数" 除外）都必须具有显式 `this` 构造函数初始值设定项。 

主构造函数的参数以及记录的成员位于 `argument_list` `record_base` 子句的和实例字段或属性的初始值设定项中的范围内。 实例成员将是这些位置中的错误 (类似于当前在常规构造函数初始值设定项的作用域中的方式，但使用) 的错误，但主构造函数的参数将在范围内并且可用，并将隐藏成员。 静态成员还可以使用，类似于目前普通构造函数中的基调用和初始值设定项的工作方式。

如果未读取主构造函数的参数，则会生成一个警告。

在中声明的表达式变量 `argument_list` 在范围内 `argument_list` 。 与规则构造函数初始值设定项的参数列表中相同的隐藏规则也适用。

### <a name="properties"></a>属性

对于记录类型声明的每个记录参数，都有一个对应的公共属性成员，其名称和类型取自值参数声明。

对于 a 记录：

* 创建公共 `get` 和 `init` 自动属性 (参阅单独的 `init` 取值函数说明) 。
  `abstract`具有匹配类型的继承属性被重写。
  如果继承的属性没有可 `public` 重写的 `get` 和 `init` 访问器，则是错误的。
  自动属性初始化为相应主构造函数参数的值。
  特性可应用于合成自动属性及其支持字段，方法是使用 `property:` 或 `field:` 语法应用于相应记录参数的属性的目标。  

### <a name="deconstruct"></a>析构

具有至少一个参数的位置记录合成名为析构的公共 void 返回实例方法，其中包含主构造函数声明的每个参数的 out 参数声明。 析构方法的每个参数都具有与主构造函数声明的相应参数相同的类型。 方法的主体将析构方法的每个参数分配给一个实例成员访问同名的成员的值。
可以显式声明方法。 如果显式声明与预期的签名或辅助功能不匹配，或者是静态的，则是错误的。

## <a name="with-expression"></a>`with` 表达式

`with`表达式是使用以下语法的新表达式。

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
`with`表达式不允许作为语句。

`with`表达式允许 "非破坏性转变"，旨在生成接收方表达式的副本，并在中对赋值进行修改 `member_initializer_list` 。

有效的 `with` 表达式包含具有非 void 类型的接收方。 接收方类型必须是一条记录。

表达式的右侧 `with` 是一个 `member_initializer_list` 带有 *标识符* 的赋值序列的，它必须是接收方类型的可访问实例字段或属性。

首先，调用接收方的 "clone" 方法 (调用) ，并将其结果转换为接收方的类型。 然后，每个 `member_initializer` 处理方式与对转换结果的字段或属性访问的赋值相同。 按词法顺序处理赋值。

---
ms.openlocfilehash: 35f6836e20776450ce5f776e7fdb66ca634d04a0
ms.sourcegitcommit: 0f56445e250ddf82b88848b94c59870f13ab8ffc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/10/2020
ms.locfileid: "84663262"
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
    | ':' class_type argument_list? interface_type_list
    ;

record_body
    : '{' class_member_declaration* '}'
    | ';'
    ;
```

记录类型是引用类型，类似于类声明。 如果不包含，记录将提供一个错误 `record_base` `argument_list` `record_declaration` `parameter_list` 。

## <a name="members-of-a-record-type"></a>记录类型的成员

除了记录体中声明的成员，记录类型还具有附加的合成成员。
如果在记录正文中声明了具有 "匹配" 签名的成员，或者继承了具有 "匹配" 签名的可访问的具体非虚拟成员，则将合成成员。
如果两个成员具有相同的签名，或在继承方案中被视为 "隐藏"，则会将其视为匹配项。

合成成员如下所示：

### <a name="equality-members"></a>相等成员

记录类型生成以下方法的合成实现，其中 `T` 是包含类型：
```C#
public override int GetHashCode();
public override bool Equals(object other);
public virtual bool Equals(T other);
```
`GetHashCode()`和 `Equals(object other)` 是中的虚方法的重写 `System.Object` 。
重写时，将忽略中间基类上隐藏这些方法的任何方法。

派生记录类型还会重写 `Equals(TBase other)` 每个基本记录类型中的方法。

记录类型合成 `System.IEquatable<T>` 由隐式实现的实现， `Equals(T other)` 其中 `T` 是包含类型。
记录类型不会合成 `System.IEquatable<TBase>` 任何基类型的实现 `TBase` ，即使这些接口是由基本记录类型实现的。

基本记录类合成 `EqualityContract` 属性。 属性在派生的记录类中被重写。 合成实现返回 `typeof(T)` ，其中 `T` 包含类型。
```C#
protected virtual Type EqualityContract { get; }
```

如果任何重写的成员的基实现是密封的或非虚拟的，或者不符合预期的签名和可访问性，则是错误的。

`Equals(T other)`当且仅当以下每个条件都为 true 时，返回 true：
- `other`不是 `null` ，并且
- 对于在记录类型中声明的每个字段，的 `System.Collections.Generic.EqualityComparer<TN>.Default.Equals(fieldN, other.fieldN)` 值 `TN` 为，其中为字段类型，
- 如果有基本记录类型，则的值 `base.Equals(other)` 为; 否则为的值 `EqualityContract.Equals(other.EqualityContract)` 。

`Equals(T other)`基本方法（包括）的的重写 `object.Equals(object other)` 执行的等效操作：
```C#
public override bool Equals(object other) => Equals(other as T);
```

`GetHashCode()`返回 `int` 采用以下值的确定性函数的结果：
- 对于在记录类型中声明的每个字段，的 `System.Collections.Generic.EqualityComparer<TN>.Default.GetHashCode(fieldN)` 值 `TN` 为，其中为字段类型，
- 如果有基本记录类型，则的值 `base.GetHashCode()` 为; 否则为的值 `System.Collections.Generic.EqualityComparer<System.Type>.Default.GetHashCode(EqualityContract)` 。

### <a name="copy-and-clone-members"></a>复制和克隆成员

记录类型包含两个复制成员：

* 采用记录类型的单个自变量的构造函数。 它被称为 "复制构造函数"。
* 使用编译器保留名称的合成公共无参数虚拟实例 "clone" 方法

复制构造函数的目的是将状态从参数复制到正在创建的新实例。 此构造函数不会运行记录声明中存在的任何实例字段/属性初始值设定项。 如果未显式声明构造函数，则编译器将合成受保护的构造函数。
构造函数必须执行的第一件事是调用基的复制构造函数，如果记录继承自 object，则调用无参数的对象构造函数。 如果用户定义的复制构造函数使用不满足此要求的隐式或显式构造函数初始值设定项，则会报告错误。
在调用基复制构造函数后，合成复制构造函数将在记录类型内隐式或显式声明的所有实例字段的值。

"Clone" 方法返回对构造函数的调用结果，该构造函数与复制构造函数具有相同的签名。 Clone 方法的返回类型为包含类型，除非基类中存在虚拟克隆方法。 在这种情况下，如果支持 "协变返回" 功能和重写返回类型，则返回类型为当前的包含类型。 合成克隆方法是基类型克隆方法（如果存在）的重写。 如果基类型克隆方法是密封的，则会生成错误。

如果包含的记录是抽象的，合成克隆方法也是抽象的。

## <a name="positional-record-members"></a>位置记录成员

除了以上成员以外，具有参数列表的记录（"位置记录"）合成其他成员与上述成员具有相同的条件。

### <a name="primary-constructor"></a>主构造函数

记录类型具有公共构造函数，该构造函数的签名对应于类型声明的值参数。 这称为类型的主构造函数，并导致禁止显示隐式声明的默认类构造函数（如果存在）。 类中已存在具有相同签名的主构造函数和构造函数是错误的。

在运行时，主构造函数

1. 执行出现在类体中的实例初始值设定项

1. 用子句中提供的参数 `record_base` （如果存在）调用基类构造函数

如果记录具有主构造函数，则任何用户定义的构造函数（"复制构造函数" 除外）都必须具有显式 `this` 构造函数初始值设定项。 

主构造函数的参数以及记录的成员位于 `argument_list` `record_base` 子句的和实例字段或属性的初始值设定项中的范围内。 实例成员将是这些位置中的错误（类似于当前在常规构造函数初始值设定项的范围内的方式，但使用的是错误），但主构造函数的参数在范围内并且可用，并将隐藏成员。 静态成员还可以使用，类似于目前普通构造函数中的基调用和初始值设定项的工作方式。 

在中声明的表达式变量 `argument_list` 在范围内 `argument_list` 。 与规则构造函数初始值设定项的参数列表中相同的隐藏规则也适用。

### <a name="properties"></a>属性

对于记录类型声明的每个记录参数，都有一个对应的公共属性成员，其名称和类型取自值参数声明。

对于 a 记录：

* 创建公共 `get` 和 `init` 自动属性（请参阅单独的 `init` 访问器规范）。
  将重写每个 "匹配" 继承抽象访问器。 自动属性初始化为相应主构造函数参数的值。

### <a name="deconstruct"></a>析构

位置记录合成名为析构的公共 void 返回方法，其主构造函数声明的每个参数都有 out 参数声明。 析构方法的每个参数都具有与主构造函数声明的相应参数相同的类型。 方法的主体将析构方法的每个参数分配给一个实例成员访问同名的成员的值。

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

`with`表达式允许 "非破坏性转变"，旨在生成接收方表达式的副本，并在中对赋值进行修改 `member_initializer_list` 。

有效的 `with` 表达式包含具有非 void 类型的接收方。 接收器类型必须包含可访问的合成记录 "clone" 方法。

表达式的右侧 `with` 是一个 `member_initializer_list` 带有*标识符*的赋值序列的，它必须是该方法的返回类型的可访问实例字段或属性 `Clone()` 。

每个的 `member_initializer` 处理方式与对记录克隆方法返回值的字段或属性访问的赋值相同。 Clone 方法只执行一次，并按词法顺序处理赋值。

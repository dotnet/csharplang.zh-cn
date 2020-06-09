---
ms.openlocfilehash: ffa34ad55752197bc2d8fb6cac7759602a2672c9
ms.sourcegitcommit: 5c9b8f27bd8299c70e2f4205a46079a10cffce76
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/08/2020
ms.locfileid: "84533341"
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
除非在记录正文中继承或声明了具有 "匹配" 签名的可访问的具体（非抽象）成员，否则将合成成员。 如果两个成员具有相同的签名，或在继承方案中被视为 "隐藏"，则会将其视为匹配项。

合成成员如下所示：

### <a name="equality-members"></a>相等成员

记录类型为以下方法生成合成实现，其中 `T` 是包含类型：

* `object.GetHashCode()`忽略
* `object.Equals(object)`忽略
* `T Equals(T)`方法，其中 `T` 是当前类型
* `Type EqualityContract`获取属性

如果 `object.GetHashCode()` 或 `object.Equals(object)` 是密封的，则会生成错误。

`EqualityContract`是返回的虚拟实例属性 `typeof(T)` 。 如果基类型定义，则 `EqualityContract` 在派生的记录中重写它。 如果基 `EqualityContract` 是密封或非虚的，则会生成错误。

`T Equals(T)`指定为执行值相等性，这样， `Equals` 当且仅当接收方中所有可访问的实例字段都等于参数的字段并等于时，此值才为 true `this.EqualityContract` `other.EqualityContract` 。

`object.Equals`执行等效于

```C#
override Equals(object o) => Equals(o as T);
```

### <a name="copy-and-clone-members"></a>复制和克隆成员

记录类型包含两个合成复制成员：

* 一个受保护的构造函数，采用记录类型的单个自变量。
* 具有编译器保留名称的公共无参数虚拟实例 "clone" 方法

受保护的构造函数称为 "复制构造函数"，合成体将输入类型中所有可访问的实例字段的值复制到对应的字段 `this` 。

"Clone" 方法返回对构造函数的调用结果，该构造函数与复制构造函数具有相同的签名。 Clone 方法的返回类型为包含类型，除非基类中存在虚拟克隆方法。 在这种情况下，如果支持 "协变返回" 功能和重写返回类型，则返回类型为当前的包含类型。 合成克隆方法是基类型克隆方法（如果存在）的重写。 如果基类型克隆方法是密封的，则会生成错误。

如果包含的记录是抽象的，合成克隆方法也是抽象的。

## <a name="positional-record-members"></a>位置记录成员

除了以上成员以外，具有参数列表的记录（"位置记录"）合成其他成员与上述成员具有相同的条件。

### <a name="primary-constructor"></a>主构造函数

记录类型具有公共构造函数，该构造函数的签名对应于类型声明的值参数。 这称为类型的主构造函数，并导致禁止显示隐式声明的默认类构造函数（如果存在）。 类中已存在具有相同签名的主构造函数和构造函数是错误的。

在运行时，主构造函数

1. 执行出现在类体中的实例初始值设定项

1. 用子句中提供的参数 `record_base` （如果存在）调用基类构造函数


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

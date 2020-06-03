---
ms.openlocfilehash: 1bf904965c2eacfdeb28aace93e1361881f1b0bd
ms.sourcegitcommit: 6c404867b9f1ab70c4c4960ddf821b52579ed1fa
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/02/2020
ms.locfileid: "84305473"
---

# <a name="records-work-in-progress"></a>记录正在进行的工作

不同于其他记录建议，这不是一项提议，而是一项工作，用于记录有关记录功能的共识设计决策。 为了解决问题，将根据需要添加规范详细信息。

建议添加记录的语法，如下所示：

```antlr
class_declaration
    : attributes? class_modifiers? 'partial'? 'class' identifier type_parameter_list?
      parameter_list? type_parameter_constraints_clauses? class_body
    ;

struct_declaration
    : attributes? struct_modifiers? 'partial'? 'struct' identifier type_parameter_list?
      parameter_list? struct_interfaces? type_parameter_constraints_clauses? struct_body
    ;

class_body
    : '{' class_member_declarations? '}'
    | ';'
    ;

struct_body
    : '{' struct_members_declarations? '}'
    | ';'
    ;
```

`attributes`非终端还将允许新的上下文属性 `data` 。

用参数列表或修饰符声明的类（结构） `data` 称为记录类（record struct），其中的任何一个是记录类型。

如果不使用修饰符声明记录类型，则是错误的 `data` 。

## <a name="members-of-a-record-type"></a>记录类型的成员

除了在类或结构体中声明的成员以外，记录类型还具有下列附加成员：

### <a name="equality-members"></a>相等成员

记录类型为以下方法生成合成实现：

* `object.GetHashCode()`重写（除非它是密封的或用户提供的）
* `object.Equals(object)`重写（除非它是密封的或用户提供的）
* `T Equals(T)`方法，其中 `T` 是当前类型

`T Equals(T)`指定为执行值相等性，这样， `Equals` 当且仅当接收方类型中声明的所有实例字段都等于其他类型的字段时，才为 true。

`object.Equals`执行等效于

```C#
override Equals(object o) => Equals(o as T);
```

### <a name="copy-and-clone-members"></a>复制和克隆成员

如果记录类型中尚未声明具有相同签名的方法，则记录类型包含两个合成复制成员：

* 一个受保护的构造函数，采用记录类型的单个自变量。
* 一个调用的公共无参数实例方法 `Clone` ，该方法返回记录类型。

受保护的构造函数称为 "复制构造函数"，合成体将输入类型中声明的所有实例字段的值复制到的相应字段 `this` 。

`Clone`方法返回对构造函数的调用结果，该结果与复制构造函数具有相同的签名。

## <a name="positional-record-members"></a>位置记录成员

除了上述成员以外，具有参数列表的记录（"位置记录"）还具有以下成员：

### <a name="primary-constructor"></a>主构造函数

记录类型具有公共构造函数，该构造函数的签名对应于类型声明的值参数。 这称为类型的主构造函数，并导致禁止显示隐式声明的默认类构造函数（如果存在）。 类中已存在具有相同签名的主构造函数和构造函数是错误的。

在运行时，主构造函数

1. 执行出现在类体中的实例字段初始值设定项;然后调用不带参数的基类构造函数。

1. 为对应于值参数的属性初始化编译器生成的支持字段（如果这些属性是编译器提供的）

### <a name="properties"></a>属性

对于记录类型声明的每个记录参数，都有一个对应的公共属性成员，其名称和类型取自值参数声明。 如果没有显式声明或继承具有 get 访问器和此名称和类型的具体（即非抽象）属性，则编译器将生成，如下所示：

对于记录结构或记录类：

* 创建公共 `get` 和 `init` 自动属性（请参阅单独的 `init` 访问器规范）。 在构造过程中，它的值将与相应的主构造函数参数的值一起初始化。 将重写每个 "匹配" 继承抽象属性的 get 访问器。


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

有效的 `with` 表达式包含具有非 void 类型的接收方。 接收器类型必须包含一个名为的可访问参数实例方法，该方法 `Clone` 的返回类型必须是接收方类型或其基类型。

表达式的右侧 `with` 是一个 `member_initializer_list` 带有*标识符*的赋值序列的，它必须是该方法的返回类型的可访问实例字段或属性 `Clone()` 。

每个的 `member_initializer` 处理方式与方法的返回值上的字段或属性目标的赋值方式相同 `Clone()` 。 `Clone()`方法只执行一次，并按词法顺序处理赋值。

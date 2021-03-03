---
ms.openlocfilehash: 76ca46dbeb912924de85d11da9c89e3865671106
ms.sourcegitcommit: f921de697a44d3ae827f168a8371d7dc3ca66f2d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/02/2021
ms.locfileid: "101641994"
---
# <a name="conversions"></a>转换

***转换** _ 允许将表达式视为特定类型。 转换可能会导致将给定类型的表达式视为具有不同的类型，也可能导致不具有类型的表达式获得类型。 转换可以是 _*_隐式_*_ 或 _ *_explicit_* *，这确定是否需要显式强制转换。 例如，从类型 `int` 到类型的转换 `long` 是隐式的，因此类型的表达式 `int` 可以隐式对待为类型 `long` 。 相反，从类型 `long` 到类型的转换 `int` 是显式的，因此需要显式强制转换。

```csharp
int a = 123;
long b = a;         // implicit conversion from int to long
int c = (int) b;    // explicit conversion from long to int
```

某些转换是由语言定义的。 程序还可以定义自己的转换 ([用户定义的转换](conversions.md#user-defined-conversions)) 。

## <a name="implicit-conversions"></a>隐式转换

以下转换归类为隐式转换：

*  标识转换
*  隐式数值转换
*  隐式枚举转换
*  隐式内插字符串转换
*  隐式可为空转换
*  Null 文本转换
*  隐式引用转换
*  装箱转换
*  隐式动态转换
*  隐式常量表达式转换
*  用户定义的隐式转换
*  匿名函数转换
*  方法组转换

隐式转换可能会在多种情况下发生，包括函数成员调用 ([编译时检查动态重载解析](expressions.md#compile-time-checking-of-dynamic-overload-resolution)) 、强制转换表达式 ([强制转换表达式](expressions.md#cast-expressions)) ，并 ([赋值运算符) 赋值运算符](expressions.md#assignment-operators) 。

预定义的隐式转换始终会成功，并且永远不会引发异常。 正确设计的用户定义隐式转换也应显示这些特征。

出于转换目的，类型 `object` 和被 `dynamic` 视为等效的。

但是，动态转换 ([隐式动态转换](conversions.md#implicit-dynamic-conversions) 和 [显式动态转换](conversions.md#explicit-dynamic-conversions)) 仅应用于 `dynamic` ([动态类型](types.md#the-dynamic-type)) 的类型的表达式。

### <a name="identity-conversion"></a>标识转换

标识转换从任何类型转换为同一类型。 此转换存在，因此，已具有所需类型的实体可被视为可转换为该类型。

*  因为和被视为等效的，所以在 `object` `dynamic` 和之间存在一个标识转换， `object` `dynamic` 在将的所有匹配项替换为相同的构造类型之间 `dynamic` `object` 。

### <a name="implicit-numeric-conversions"></a>隐式数值转换

隐式数值转换为：

*  从 `sbyte` 到、、、、 `short` `int` `long` `float` `double` 或 `decimal` 。
*  从到、、、、、、、 `byte` `short` `ushort` `int` `uint` `long` `ulong` `float` `double` 或 `decimal` 。
*  从 `short` 到 `int` 、、、 `long` `float` `double` 或 `decimal` 。
*  从到、、、、、 `ushort` `int` `uint` `long` `ulong` `float` `double` 或 `decimal` 。
*  从 `int` 到 `long` 、 `float` 、 `double` 或 `decimal` 。
*  从 `uint` 到 `long` 、、、 `ulong` `float` `double` 或 `decimal` 。
*  从 `long` 到 `float` 、 `double` 或 `decimal` 。
*  从 `ulong` 到 `float` 、 `double` 或 `decimal` 。
*  从到、、、、、、 `char` `ushort` `int` `uint` `long` `ulong` `float` `double` 或 `decimal` 。
*  从 `float` 到 `double`。

从 `int` 、、 `uint` `long` 或到之间的转换 `ulong` `float` `long` `ulong` `double` 可能会导致精度损失，但永远不会导致数量级损失。 其他隐式数值转换不会丢失任何信息。

不存在对类型的隐式转换 `char` ，因此其他整型类型的值不会自动转换为 `char` 类型。

### <a name="implicit-enumeration-conversions"></a>隐式枚举转换

隐式枚举转换允许 *decimal_integer_literal* `0` 转换为任何 *enum_type* ，以及基础类型为 *enum_type* 的任何 *nullable_type* 。 在后一种情况下，转换将通过转换为基础 *enum_type* 进行计算，并将结果包装 ([可以为 null 的类型](types.md#nullable-types)) 。

### <a name="implicit-interpolated-string-conversions"></a>隐式内插字符串转换

隐式内插字符串转换允许 *interpolated_string_expression* (内 [插字符串](expressions.md#interpolated-strings)) 转换为 `System.IFormattable` `System.FormattableString` 实现) 的或 (`System.IFormattable` 。

应用此转换时，字符串值不是由内插字符串组成的。 相反 `System.FormattableString` ，将创建的实例，如内 [插字符串](expressions.md#interpolated-strings)中所述。

### <a name="implicit-nullable-conversions"></a>隐式可为空转换

对不可以为 null 的值类型进行操作的预定义隐式转换也可用于这些类型的可以为 null 的形式。 对于从不可为 null 的值类型转换为不可为 null 的值类型的每个预定义隐式标识和数值转换 `S` `T` ，存在以下隐式可为 null 的转换：

*  从到的隐式转换 `S?` `T?` 。
*  从到的隐式转换 `S` `T?` 。

根据中的基础转换，计算隐式可为 null 的转换， `S` `T` 如下所示：

*  如果可以为 null 的转换从 `S?` 到 `T?` ：
    * 如果源值为 null (`HasValue` 属性为 false) ，则结果为类型的 null 值 `T?` 。
    * 否则，转换将作为从到的解包进行计算 `S?` `S` ，后跟从到的基础转换 `S` `T` ，然后) 从到的包装 ([可以为 null 的类型](types.md#nullable-types) `T` `T?` 。

*  如果可以为 null 的转换从 `S` 到 `T?` ，则会将转换计算为从到的基础转换， `S` 并将从到 `T` 的换行 `T` `T?` 。

### <a name="null-literal-conversions"></a>Null 文本转换

存在从 `null` 文本到任何可以为 null 的类型的隐式转换。 这种转换会生成 null 值， (给定可以为 null 的类型) [可](types.md#nullable-types) 为 null 的类型。

### <a name="implicit-reference-conversions"></a>隐式引用转换

隐式引用转换为：

*  从任何 *reference_type* 到 `object` 和 `dynamic` 。
*  从任何 *class_type* `S` 到任何 *class_type* `T` ， `S` 都是从派生的 `T` 。
*  从任何 *class_type* `S` 到任何 *interface_type* `T` ，提供的 `S` 实现 `T` 。
*  从任何 *interface_type* `S` 到任何 *interface_type* `T` ， `S` 都是从派生的 `T` 。
*  在 array_type 具有元素类型的 *array_type* 中 `S` ，如果 `SE`  `T` `TE` 满足以下所有条件：
    * `S` 和 `T` 仅因元素类型而异。 换言之， `S` 和 `T` 具有相同的维数。
    * `SE`和 `TE` 都是 *reference_type*。
    * 存在从到的隐式引用转换 `SE` `TE` 。
*  从任何 *array_type* `System.Array` 和它实现的接口。
*  从一维数组类型 `S[]` 到 `System.Collections.Generic.IList<T>` 及其基接口，前提是存在隐式标识或从到的引用转换 `S` `T` 。
*  从任何 *delegate_type* `System.Delegate` 和它实现的接口。
*  从 null 文本到任何 *reference_type*。
*  从任何 *reference_type* 到 *reference_type* ， `T` 如果它具有隐式标识或到 *reference_type* 的引用转换 `T0` ，并且 `T0` 具有到的标识转换 `T` 。
*  从任何 *reference_type* 到接口或委托类型（ `T` 如果它具有隐式标识或到接口或委托类型的引用转换 `T0` ），并且) 到之间存在可转换的 `T0` ([方差转换](interfaces.md#variance-conversion) `T` 。
*  涉及称为引用类型的类型参数的隐式转换。 有关涉及类型参数的隐式转换的更多详细信息，请参阅 [涉及类型参数的隐式转换](conversions.md#implicit-conversions-involving-type-parameters) 。

隐式引用转换是 *reference_type* 之间的转换，这些转换可证明始终成功，因此不需要在运行时进行检查。

引用转换、隐式或显式转换决不会更改正在转换的对象的引用标识。 换言之，虽然引用转换可以更改引用的类型，但它不会更改引用的对象的类型或值。

### <a name="boxing-conversions"></a>装箱转换

装箱转换允许 *value_type* 隐式转换为引用类型。 对 `object` `dynamic` `System.ValueType` *non_nullable_value_type* 实现的任何 *interface_type* ，都存在从到、、以及的任何 non_nullable_value_type 的装箱转换。 此外，可以将 *enum_type* 转换为类型 `System.Enum` 。

如果且仅当存在从基础 *non_nullable_value_type* 到引用类型的装箱转换，则从 *nullable_type* 到引用类型的装箱转换。

如果值类型 `I` 具有到接口类型的装箱转换 `I0` ，并且其 `I0` 标识转换为，则该值类型具有到接口类型的装箱转换 `I` 。

如果值类型 `I` 具有到接口类型的装箱转换或委托类型的装箱转换 `I0` 并且 `I0` ([方差转换](interfaces.md#variance-conversion)) 为变体，则该值类型具有到的装箱转换 `I` 。

将 *non_nullable_value_type* 的值装箱包括分配对象实例并将 *value_type* 值复制到该实例中。 结构可以装箱到类型 `System.ValueType` ，因为这是所有结构的基类 ([继承](structs.md#inheritance)) 。

将 *nullable_type* 的值装箱将继续执行以下操作：

*  如果源值为 null (`HasValue` 属性为 false) ，则结果将为目标类型的空引用。
*  否则，结果将是对 `T` 源值解包和装箱而生成的装箱的引用。

[装箱转换中进一步](types.md#boxing-conversions)介绍了装箱转换。

### <a name="implicit-dynamic-conversions"></a>隐式动态转换

存在从类型为的表达式到任何类型的隐式动态转换 `dynamic` `T` 。 转换是动态绑定 ([动态绑定](expressions.md#dynamic-binding)) ，这意味着将在运行时从表达式的运行时类型中查找隐式转换 `T` 。 如果未找到任何转换，则会引发运行时异常。

请注意，此隐式转换似乎违反了隐式转换开始时的 [建议，隐](conversions.md#implicit-conversions) 式转换应永远不会引发异常。 但它不是转换本身，而是 *查找* 导致异常的转换。 运行时异常的风险在使用动态绑定时是固有的。 如果不需要转换的动态绑定，则可以先将该表达式转换为 `object` ，然后再转换为所需的类型。

下面的示例阐释了隐式动态转换：

```csharp
object o  = "object"
dynamic d = "dynamic";

string s1 = o; // Fails at compile-time -- no conversion exists
string s2 = d; // Compiles and succeeds at run-time
int i     = d; // Compiles but fails at run-time -- no conversion exists
```

和中的 `s2` 分配 `i` 都采用隐式动态转换，在这种情况下，将在运行时暂停操作的绑定。 在运行时，隐式转换从的运行时类型中查找 `d`  --  `string` 到目标类型。 找到到， `string` 但不能转换为 `int` 。

### <a name="implicit-constant-expression-conversions"></a>隐式常量表达式转换

隐式常数表达式转换允许以下转换：

*   [](expressions.md#constant-expressions) `int` `sbyte` `byte` `short` `ushort` `uint` `ulong` 如果 *constant_expression* 的值在目标类型的范围内，则可以将类型的 constant_expression (常数表达式) 转换为类型、、、、或。
*   `long` `ulong` 如果 *constant_expression* 的值不为负数，则可以将类型的 constant_expression 转换为类型。

### <a name="implicit-conversions-involving-type-parameters"></a>涉及类型参数的隐式转换

给定的类型参数存在以下隐式转换 `T` ：

*  从到 `T` 其有效的基类 `C` ，从到的任何基类，从到所 `T` `C` `T` 实现的任何接口 `C` 。 在运行时，如果 `T` 是值类型，则转换将作为装箱转换执行。 否则，转换将作为隐式引用转换或标识转换执行。
*  从 `T` `I` `T` 设置为有效接口中的接口类型，并从设置 `T` 为的任何基接口 `I` 。 在运行时，如果 `T` 是值类型，则转换将作为装箱转换执行。 否则，转换将作为隐式引用转换或标识转换执行。
*  从 `T` 到类型参数 `U` ， `T` 具体取决于 `U`)  ([类型参数约束](classes.md#type-parameter-constraints) 。 在运行时，如果 `U` 是值类型，则和的 `T` `U` 类型必须相同，并且不执行任何转换。 否则，如果 `T` 是值类型，则转换将作为装箱转换执行。 否则，转换将作为隐式引用转换或标识转换执行。
*  从 null 文本到 `T` ，已知为 `T` 引用类型。
*  `T`向引用类型（ `I` 如果它具有到引用类型的隐式转换 `S0` 和到的 `S0` 标识转换） `S` 。 在运行时，转换的执行方式与转换到的方式相同 `S0` 。
*  从 `T` 到接口类型 `I` （如果它具有到接口或委托类型的隐式转换 `I0` ）并且可以转换为 `I0` `I` ([方差转换](interfaces.md#variance-conversion)) 。 在运行时，如果 `T` 是值类型，则转换将作为装箱转换执行。 否则，转换将作为隐式引用转换或标识转换执行。

如果 `T` 已知 ([类型参数约束](classes.md#type-parameter-constraints)) 为引用类型，则上述转换全都归类为隐式引用转换 ([隐式引用转换](conversions.md#implicit-reference-conversions)) 。 如果 `T` 不知道是引用类型，上面的转换归类为装箱转换 ([装箱转换](conversions.md#boxing-conversions)) 。

### <a name="user-defined-implicit-conversions"></a>用户定义的隐式转换

用户定义的隐式转换包括一个可选的标准隐式转换，然后执行用户定义的隐式转换运算符，然后执行另一个可选的标准隐式转换。 用于评估用户定义的隐式转换的确切规则在 [处理用户定义的隐式转换](conversions.md#processing-of-user-defined-implicit-conversions)中进行了介绍。

### <a name="anonymous-function-conversions-and-method-group-conversions"></a>匿名函数转换和方法组转换

匿名函数和方法组本身没有类型，但可能会隐式转换为委托类型或表达式树类型。 [匿名函数转换和](conversions.md#anonymous-function-conversions)[方法组转换](conversions.md#method-group-conversions)中的方法组转换更详细地介绍了匿名函数转换。

## <a name="explicit-conversions"></a>显式转换

以下转换归类为显式转换：

*  所有隐式转换。
*  显式数值转换。
*  显式枚举转换。
*  可以为 null 的显式转换。
*  显式引用转换。
*  显式接口转换。
*  取消装箱转换。
*  显式动态转换
*  用户定义的显式转换。

显式转换可在转换表达式 () [转换表达式](expressions.md#cast-expressions) 中发生。

显式转换集包括所有隐式转换。 这意味着允许冗余强制转换表达式。

不是隐式转换的显式转换是转换，这种转换不能始终成功、已知的转换可能会丢失信息，并且跨类型的域转换的类型充分不同，以确保显式表示法。

### <a name="explicit-numeric-conversions"></a>显式数值转换

显式数值转换是指从 *numeric_type* 到另一个 *numeric_type* 的转换， ([隐式](conversions.md#implicit-numeric-conversions) 数值转换) 尚不存在：

*  从 `sbyte` 到 `byte` 、、、 `ushort` `uint` `ulong` 或 `char` 。
*  从 `byte` 到 `sbyte` 和 `char` 。
*  从 `short` 到、、、、 `sbyte` `byte` `ushort` `uint` `ulong` 或 `char` 。
*  从 `ushort` 到 `sbyte` 、 `byte` 、 `short` 或 `char` 。
*  从到、、、、、 `int` `sbyte` `byte` `short` `ushort` `uint` `ulong` 或 `char` 。
*  从 `uint` 到、、、、 `sbyte` `byte` `short` `ushort` `int` 或 `char` 。
*  从到、、、、、、 `long` `sbyte` `byte` `short` `ushort` `int` `uint` `ulong` 或 `char` 。
*  从到、、、、、、 `ulong` `sbyte` `byte` `short` `ushort` `int` `uint` `long` 或 `char` 。
*  从 `char` 到 `sbyte` 、 `byte` 或 `short` 。
*  从到、、、、、、、、 `float` `sbyte` `byte` `short` `ushort` `int` `uint` `long` `ulong` `char` 或 `decimal` 。
*  从到、、、、、、、、、 `double` `sbyte` `byte` `short` `ushort` `int` `uint` `long` `ulong` `char` `float` 或 `decimal` 。
*  从到、、、、、、、、、 `decimal` `sbyte` `byte` `short` `ushort` `int` `uint` `long` `ulong` `char` `float` 或 `double` 。

由于显式转换包括所有隐式和显式的数值转换，因此始终可以使用强制转换表达式 (强制转换) [表达式](expressions.md#cast-expressions)将任何 *numeric_type* 转换为任何其他 *numeric_type* 。

显式数值转换可能会丢失信息，或可能导致引发异常。 显式数值转换的处理方式如下：

*  对于从整型转换为另一整型类型的转换，该处理依赖于溢出检查上下文 ([已检查和未检查的运算符](expressions.md#the-checked-and-unchecked-operators)) 发生转换的：
    * 在 `checked` 上下文中，如果源操作数的值在目标类型的范围内，则转换成功; 但 `System.OverflowException` 如果源操作数的值超出了目标类型的范围，则会引发。
    * 在 `unchecked` 上下文中，转换始终成功，并按如下所示继续。
        * 如果源类型大于目标类型，则通过放弃其“额外”最高有效位来截断源值。 结果会被视为目标类型的值。
        * 如果源类型小于目标类型，则源值是符号扩展或零扩展，以使其与目标类型的大小相同。 如果源类型带符号，则是符号扩展；如果源类型是无符号的，则是零扩展。 结果会被视为目标类型的值。
        * 如果源类型与目标类型的大小相同，则源值将被视为目标类型的值。
*  对于从 `decimal` 到整数类型的转换，源值向零舍入到最接近的整数值，并且此整数值将成为转换的结果。 如果生成的整数值超出了目标类型的范围， `System.OverflowException` 则会引发。
*  对于从 `float` 或 `double` 到整型类型的转换，处理操作依赖于溢出检查上下文 ([已检查和未检查的运算符](expressions.md#the-checked-and-unchecked-operators)) 发生转换的：
    * 在 `checked` 上下文中，转换过程如下所示：
        * 如果操作数的值为 NaN 或无穷大，则 `System.OverflowException` 会引发。
        * 否则，源操作数向零舍入到最接近的整数值。 如果此整数值在目标类型的范围内，则此值为转换的结果。
        * 否则，将会引发 `System.OverflowException`。
    * 在 `unchecked` 上下文中，转换始终成功，并按如下所示继续。
        * 如果操作数的值为 NaN 或无穷大，则转换的结果是目标类型的未指定值。
        * 否则，源操作数向零舍入到最接近的整数值。 如果此整数值在目标类型的范围内，则此值为转换的结果。
        * 否则，转换的结果是目标类型的未指定值。
*  对于从到的 `double` 转换 `float` ，此 `double` 值舍入为最接近的 `float` 值。 如果 `double` 值太小而无法表示为 `float` ，则结果将变为正零或负零。 如果 `double` 值太大而无法表示为 `float` ，则结果将变为正无穷或负无穷。 如果 `double` 该值为 nan，则结果也为 nan。
*  对于从 `float` 或到的 `double` 转换 `decimal` ，将源值转换为 `decimal` 表示形式，并在第28位小数后舍入为最接近的数（如果需要 ([decimal 类型](types.md#the-decimal-type)) 。 如果源值太小而无法表示为 `decimal` ，则结果将变为零。 如果源值为 NaN、无限大或太大而无法表示为 `decimal` ， `System.OverflowException` 则会引发。
*  对于从 `decimal` 到或的 `float` 转换 `double` ，将 `decimal` 值舍入为最接近 `double` 的 `float` 值或值。 虽然这种转换可能会丢失精度，但它永远不会引发异常。

### <a name="explicit-enumeration-conversions"></a>显式枚举转换

显式枚举转换为：

*  从 `sbyte` 、 `byte` 、、、、、、、、、 `short` `ushort` `int` `uint` `long` `ulong` `char` `float` `double` 或 `decimal` 到任何 *enum_type*。
*  从任何 *enum_type* 到、、、、、、、、、、 `sbyte` `byte` `short` `ushort` `int` `uint` `long` `ulong` `char` `float` `double` 或 `decimal` 。
*  从任何 *enum_type* 到任何其他 *enum_type*。

处理两种类型之间的显式枚举转换的方式是将任何参与的 *enum_type* 视为 *enum_type* 的基础类型，然后在生成的类型之间执行隐式或显式数字转换。 例如，假设有一个 `E` 具有和基础类型的 enum_type，则从到的显式数字转换 `int` 会将从到的转换 `E` `byte` 作为显式数字转换处理 (从到的) [显式](conversions.md#explicit-numeric-conversions)数字转换 `int` `byte` ，并将从) 到的 `byte` `E` [隐式](conversions.md#implicit-numeric-conversions)数值转换作为 (隐 `byte` `int` 式数值转换处理。

### <a name="explicit-nullable-conversions"></a>显式可为空转换

***显式可以为 null 的转换*** 允许对不可以为 null 的值类型进行操作的预定义显式转换也可用于这些类型的可以为 null 的形式。 对于从不可为 null 的值类型转换为不可为 null 的值类型的每个预定义的显式转换 `S` `T` ([标识转换](conversions.md#identity-conversion)、 [隐式数值转换](conversions.md#implicit-numeric-conversions)、 [隐式枚举转换](conversions.md#implicit-enumeration-conversions)、 [显式数字转换](conversions.md#explicit-numeric-conversions)和 [显式枚举转换](conversions.md#explicit-enumeration-conversions)) ，可以为 null 的转换存在：

*  从到的显式 `S?` 转换 `T?` 。
*  从到的显式 `S` 转换 `T?` 。
*  从到的显式 `S?` 转换 `T` 。

基于从到的基础转换计算可以为 null 的转换的 `S` `T` 过程如下所示：

*  如果可以为 null 的转换从 `S?` 到 `T?` ：
    * 如果源值为 null (`HasValue` 属性为 false) ，则结果为类型的 null 值 `T?` 。
    * 否则，转换将作为从到的解包进行计算 `S?` `S` ，后跟从到的 `S` 转换 `T` ，后跟从到的换行 `T` `T?` 。
*  如果可以为 null 的转换从 `S` 到 `T?` ，则会将转换计算为从到的基础转换， `S` 并将从到 `T` 的换行 `T` `T?` 。
*  如果从到的可为 null 的转换为 `S?` `T` ，则会将转换计算为从到的解包， `S?` `S` 后跟从到的基础转换 `S` `T` 。

请注意，如果值为，则对可以为 null 的值进行解包的尝试将引发异常 `null` 。

### <a name="explicit-reference-conversions"></a>显式引用转换

显式引用转换为：

*  从 `object` 和 `dynamic` 到任何其他 *reference_type*。
*  从任何 *class_type* `S` 到任何 *class_type* `T` ，提供的 `S` 是的基类 `T` 。
*  从任何 *class_type* `S` 到任何 *interface_type* `T` ，提供 `S` 的不是密封的，并且不 `S` 会实现 `T` 。
*  从任何 *interface_type* `S` 到任何 *class_type* `T` `T` 均未密封或未提供 `T` 实现 `S` 。
*  从任何 *interface_type* `S` 到任何 *interface_type* `T` ，提供 `S` 的不是从派生的 `T` 。
*  在 array_type 具有元素类型的 *array_type* 中 `S` ，如果 `SE`  `T` `TE` 满足以下所有条件：
    * `S` 和 `T` 仅因元素类型而异。 换言之， `S` 和 `T` 具有相同的维数。
    * `SE`和 `TE` 都是 *reference_type*。
    * 存在从到的显式引用 `SE` 转换 `TE` 。
*  从 `System.Array` 及其实现的接口到任何 *array_type*。
*  从一维数组类型 `S[]` 到 `System.Collections.Generic.IList<T>` 及其基接口，前提是存在从到的显式引用转换 `S` `T` 。
*  从 `System.Collections.Generic.IList<S>` 及其基接口到一维数组类型 `T[]` ，前提是存在从到的显式标识或引用转换 `S` `T` 。
*  从 `System.Delegate` 及其实现的接口到任何 *delegate_type*。
*  如果引用类型 `T` 具有到引用类型的显式引用转换 `T0` 并且 `T0` 具有标识转换，则从引用类型转换为引用类型 `T` 。
*  如果引用类型 `T` 具有到接口或委托类型的显式引用转换，则从引用类型转换为接口或委托类型 `T0` ，或者可以转换为 (方差 `T0` `T` `T` `T0` [转换](interfaces.md#variance-conversion)) 的变体。
*  从 `D<S1...Sn>` 到， `D<T1...Tn>` 其中 `D<X1...Xn>` 是泛型委托类型，与 `D<S1...Sn>` `D<T1...Tn>` 以下类型的每个类型参数均不兼容或不相同 `Xi` `D` ：
    * 如果 `Xi` 是固定的，则与 `Si` 相同 `Ti` 。
    * 如果 `Xi` 是协变的，则存在隐式或显式标识或从到的引用转换 `Si` `Ti` 。
    * 如果 `Xi` 为逆变，则 `Si` 和 `Ti` 都是相同或同时为这两个引用类型。
*  涉及称为引用类型的类型参数的显式转换。 有关涉及类型参数的显式转换的详细信息，请参阅 [涉及类型参数的显式转换](conversions.md#explicit-conversions-involving-type-parameters)。

显式引用转换是需要运行时检查以确保它们正确的引用类型之间的转换。

若要在运行时成功进行显式引用转换，源操作数的值必须为 `null` ，或者源操作数引用的对象的实际类型必须是可通过隐式引用转换转换为目标类型的类型， ([隐式引用](conversions.md#implicit-reference-conversions) 转换) 或装箱转换 ([装箱](conversions.md#boxing-conversions) 转换) 。 如果显式引用转换失败， `System.InvalidCastException` 将引发。

引用转换、隐式或显式转换决不会更改正在转换的对象的引用标识。 换言之，虽然引用转换可以更改引用的类型，但它不会更改引用的对象的类型或值。

### <a name="unboxing-conversions"></a>取消装箱转换

取消装箱转换允许将引用类型显式转换为 *value_type*。 从类型 `object` `dynamic` 和 `System.ValueType` 任何 *non_nullable_value_type*，以及从任何 *interface_type* 到实现 *interface_type* 的任何 *non_nullable_value_type* 的取消装箱转换。 此外 `System.Enum` ，可以将类型取消装箱为任意 *enum_type*。

如果从引用类型到 *nullable_type* 的基础 *non_nullable_value_type* 的取消装箱转换存在，则取消装箱转换将从引用类型转换为 *nullable_type* 。

如果值类型 `S` `I` 具有从接口类型的取消装箱转换 `I0` ，并且其 `I0` 标识转换为，则值类型会从接口类型进行取消装箱转换 `I` 。

如果某个值类型 `S` `I` 具有从接口或委托类型的取消装箱转换，并且该类型的变体转换为 `I0` `I0` `I` `I` `I0` ([方差转换](interfaces.md#variance-conversion)) ，则它会从接口类型进行取消装箱转换。

取消装箱操作包括：首先检查对象实例是否是给定 *value_type* 的装箱值，然后将值复制到该实例之外。 取消装箱对 *nullable_type* 的空引用将生成 *nullable_type* 的 null 值。 结构可以从类型取消装箱 `System.ValueType` ，因为这是所有结构的基类 ([继承](structs.md#inheritance)) 。

取消装箱转换中进一步介绍 [了取消装箱](types.md#unboxing-conversions)转换。

### <a name="explicit-dynamic-conversions"></a>显式动态转换

存在从类型为的表达式 `dynamic` 到任何类型的显式动态转换 `T` 。 转换是动态绑定 ([动态绑定](expressions.md#dynamic-binding)) ，这意味着将在运行时从表达式的运行时类型中查找显式转换 `T` 。 如果未找到任何转换，则会引发运行时异常。

如果不需要转换的动态绑定，则可以先将该表达式转换为 `object` ，然后再转换为所需的类型。

假定定义了下面的类：
```csharp
class C
{
    int i;

    public C(int i) { this.i = i; }

    public static explicit operator C(string s) 
    {
        return new C(int.Parse(s));
    }
}
```

下面的示例阐释了显式动态转换：
```csharp
object o  = "1";
dynamic d = "2";

var c1 = (C)o; // Compiles, but explicit reference conversion fails
var c2 = (C)d; // Compiles and user defined conversion succeeds
```

在编译时，将转换为的最佳转换为 `o` `C` 显式引用转换。 这会在运行时失败，因为 `"1"` 事实上并不是 `C` 。 但是，将转换为，以 `d` `C` 显式动态转换为运行时，将在其中找到用户定义的运行时类型到的转换 `d`  --  `string` `C` ，并成功。

### <a name="explicit-conversions-involving-type-parameters"></a>涉及类型参数的显式转换

给定类型参数存在以下显式转换 `T` ：

*  从的有效基类 `C` `T` ，到的 `T` 任何基类 `C` `T` 。 在运行时，如果 `T` 是值类型，则转换将作为取消装箱转换执行。 否则，转换将作为显式引用转换或标识转换执行。
*  从任何接口类型到 `T` 。 在运行时，如果 `T` 是值类型，则转换将作为取消装箱转换执行。 否则，转换将作为显式引用转换或标识转换执行。
*  `T`  `I` 如果尚未从到的隐式转换，则从到任何 `T` interface_type `I` 。 在运行时，如果 `T` 是值类型，则转换将作为装箱转换执行，后跟显式引用转换。 否则，转换将作为显式引用转换或标识转换执行。
*  从类型参数 `U` 到 `T` ，前提条件 `T` 取决于 `U`)  ([类型参数约束](classes.md#type-parameter-constraints) 。 在运行时，如果 `U` 是值类型，则和的 `T` `U` 类型必须相同，并且不执行任何转换。 否则，如果 `T` 是值类型，则转换将作为取消装箱转换执行。 否则，转换将作为显式引用转换或标识转换执行。

如果 `T` 已知为引用类型，则上述转换均分类为显式引用转换 () 的 [显式引用转换](conversions.md#explicit-reference-conversions) 。 如果 `T` 未知类型为引用类型，则上述转换归类为取消装箱转换 ([取消装箱转换](conversions.md#unboxing-conversions)) 。

以上规则不允许从不受约束的类型形参直接转换为非接口类型，这可能会令人吃惊。 此规则的原因是为了防止混淆并使此类转换的语义清晰。 例如，请考虑以下声明：
```csharp
class X<T>
{
    public static long F(T t) {
        return (long)t;                // Error 
    }
}
```

如果允许将的直接显式转换 `t` 为 `int` ，则很可能会出现这样的情况 `X<int>.F(7)`  `7L` 。 但是，它不会这样，因为仅当已知类型在绑定时是数字时才考虑标准数字转换。 为了使语义清晰明了，必须改为编写以上示例：
```csharp
class X<T>
{
    public static long F(T t) {
        return (long)(object)t;        // Ok, but will only work when T is long
    }
}
```

此代码现在将进行编译，但执行 `X<int>.F(7)` 会在运行时引发异常，因为装箱 `int` 无法直接转换为 `long` 。

### <a name="user-defined-explicit-conversions"></a>用户定义的显式转换

用户定义的显式转换包含一个可选的标准显式转换，然后执行用户定义的隐式或显式转换运算符，然后执行另一个可选的标准显式转换。 用于评估用户定义的显式转换的确切规则在 [处理用户定义的显式转换](conversions.md#processing-of-user-defined-explicit-conversions)中进行了介绍。

## <a name="standard-conversions"></a>标准转换

标准转换是那些预定义的转换，这些转换可作为用户定义的转换的一部分出现。

### <a name="standard-implicit-conversions"></a>标准隐式转换

以下隐式转换归类为标准隐式转换：

*  标识转换 ([标识转换](conversions.md#identity-conversion)) 
*  隐式数值转换 ([隐式数值转换](conversions.md#implicit-numeric-conversions)) 
*  隐式可为 null 转换 ([隐式可为 null 转换](conversions.md#implicit-nullable-conversions)) 
*  隐 [式引用转换 (隐](conversions.md#implicit-reference-conversions) 式引用转换) 
*  [装箱转换 (装箱](conversions.md#boxing-conversions)转换) 
*  隐式常量表达式转换 ([隐式常量表达式转换](conversions.md#implicit-constant-expression-conversions)) 
*  涉及类型参数的隐式转换 ([涉及类型参数的隐式转换](conversions.md#implicit-conversions-involving-type-parameters)) 

标准隐式转换专门排除用户定义的隐式转换。

### <a name="standard-explicit-conversions"></a>标准显式转换

标准显式转换均为标准隐式转换，以及与之相反的标准隐式转换的显式转换的子集。 换言之，如果存在从类型到类型的标准隐式转换 `A` `B` ，则从类型 `A` 到类型 `B` 以及从类型到类型的标准显式转换 `B` `A` 。

## <a name="user-defined-conversions"></a>用户定义的转换

C # 允许使用 ***用户定义的转换*** 来扩充预定义的隐式和显式转换。 用户定义的转换是通过在类和结构类型中 ([转换运算符](classes.md#conversion-operators)) 声明转换运算符引入的。

### <a name="permitted-user-defined-conversions"></a>允许的用户定义的转换

C # 只允许声明某些用户定义的转换。 特别是，不能重新定义已存在的隐式或显式转换。

对于给定的源类型 `S` 和目标类型 `T` ，如果 `S` 或 `T` 是可以为 null 的类型，则让 `S0` 和 `T0` 引用它们的基础类型，否则， `S0` 并 `T0` `S` `T` 分别为和。 `S` `T` 仅当满足以下所有条件时，才允许类或结构声明从源类型到目标类型的转换：

*  `S0` 和 `T0` 是不同的类型。
*  `S0`或 `T0` 是发生运算符声明的类或结构类型。
*  `S0`和 `T0` 都不是 *interface_type*。
*  如果不包括用户定义的转换，则从到的转换不存在 `S` `T` `T` `S` 。

适用于用户定义的转换的限制将在 [转换运算符](classes.md#conversion-operators)中进一步讨论。

### <a name="lifted-conversion-operators"></a>提升的转换运算符

给定用户定义的转换运算符，该运算符将不可以为 null 的值类型转换 `S` 为不可为 null 的值类型 `T` ，并且存在将从转换为的 ***提升转换运算符*** `S?` `T?` 。 此提升转换运算符执行从到的解包，然后执行从到的 `S?` `S` 用户定义的转换 `S` `T` ，后跟从到的换行 `T` `T?` ，只不过空值 `S?` 直接转换为 null 值 `T?` 。

提升的转换运算符与其基础用户定义转换运算符具有相同的隐式或显式分类。 "用户定义的转换" 一词适用于用户定义的转换运算符和提升的转换运算符。

### <a name="evaluation-of-user-defined-conversions"></a>计算用户定义的转换

用户定义的转换将其类型（称为 ***源类型** _）的值转换为另一种类型，称为 _*_目标类型_*_。 计算用户定义的转换中心，查找特定源和目标类型的 _ *_最具体_* 的 * 用户定义转换运算符。 此确定分为几个步骤：

*  查找要将用户定义的转换运算符视为其中的一组类和结构。 此集由源类型及其基类和目标类型及其基类 (，这些隐式假设只有类和结构可以声明用户定义的运算符，并且非类类型没有基类) 。 对于此步骤，如果源类型或目标类型为 *nullable_type*，则改为使用它们的基础类型。
*  从该类型集中确定哪些用户定义的转换运算符适用。 要使转换运算符适用，必须可以执行标准转换 ([标准](conversions.md#standard-conversions) 转换) 从源类型转换为运算符的操作数类型，并且必须能够执行从运算符的结果类型到目标类型的标准转换操作。
*  从一组适用的用户定义的运算符，确定哪个运算符明确是最具体的。 一般来说，最特定的运算符是操作数类型与源类型 "最接近" 的运算符，其结果类型与目标类型 "最近"。 用户定义的转换运算符优先于提升转换运算符。 以下部分定义了用于建立最具体的用户定义转换运算符的确切规则。

确定了最特定的用户定义转换运算符后，用户定义的转换的实际执行操作包括多达三个步骤：

*  首先，如果需要，执行从源类型到用户定义或提升的转换运算符的操作数类型的标准转换。
*  接下来，调用用户定义或提升的转换运算符来执行转换。
*  最后，如果需要，执行从用户定义转换运算符的结果类型到目标类型的标准转换。

用户定义的转换的计算从不涉及多个用户定义的转换运算符或提升的转换运算符。 换句话说，从类型到类型的转换 `S` `T` 从不首先执行从到的用户定义的转换 `S` `X` ，然后执行从到的用户定义的转换 `X` `T` 。

以下各节提供了用户定义的隐式或显式转换的确切计算定义。 定义使用以下术语：

*  如果标准隐式转换 (从类型到类型的标准 [隐式](conversions.md#standard-implicit-conversions)转换) `A` `B` ，并且和都不 `A` `B` *interface_type*，则称为 `A` ***包含** _ `B` ，并 `B` 被称为 _ *_包含_* *  `A` 。
*  一组类型中包含的 ***最大的类型*** 是一种包含集内所有其他类型的类型。 如果没有一种类型包含所有其他类型，则该集没有最大的包含类型。 更直观地说，最包含的类型是集中的 "最大" 类型，每个其他类型都可以隐式转换为一种类型。
*  类型集中 ***最常被包含的类型*** 是一种类型，它由集中的所有其他类型所包含。 如果所有其他类型都不包含任何一种类型，则该集不包含大多数被包含的类型。 更直观地说，最包含的类型是集中的 "最小" 类型，这种类型可以隐式转换为其他类型。

### <a name="processing-of-user-defined-implicit-conversions"></a>处理用户定义的隐式转换

用户定义的从类型到类型的隐式转换的 `S` `T` 处理方式如下：

*  确定类型 `S0` 和 `T0` 。 如果 `S` 或 `T` 是可以为 null 的类型， `S0` 并且 `T0` 是其基础类型，则为; 否则 `S0` `T0` 分别为和 `S` `T` 。
*  查找类型集， `D` 用户定义的转换运算符将从其考虑。 `S0`如果是类或结构) ，则此集由 (组成 `S0` ， (的基类（ `S0` 如果 `S0` 是类) ）和 `T0` (（如果 `T0` 是类或结构) ）。
*  查找适用的用户定义转换运算符和提升转换运算符集 `U` 。 此集由中的类或结构所声明的用户定义的和提升的隐式转换运算符组成，这些类或结构从包含的类型包含在中 `D` `S` `T` 。 如果 `U` 为空，则不定义转换，并发生编译时错误。
*  查找中运算符的最特定源类型 `SX` `U` ：
    * 如果中的任何运算符 `U` `S` 都为，则 `SX` 为 `S` 。
    * 否则， `SX` 是中运算符的组合源类型集内包含程度最高的类型 `U` 。 如果找不到完全包含的类型，则转换是不明确的，并发生编译时错误。
*  查找中运算符的最特定目标类型 `TX` `U` ：
    * 如果中的任何运算符 `U` 转换为 `T` ，则 `TX` 为 `T` 。
    * 否则， `TX` 是中运算符的组合目标类型集中的最包含的类型 `U` 。 如果找不到完全包含的类型，则转换是不明确的，并发生编译时错误。
*  查找最具体的转换运算符：
    * 如果 `U` 只包含一个从转换为的用户定义的转换 `SX` 运算符 `TX` ，则这是最具体的转换运算符。
    * 否则，如果 `U` 只包含一个从转换为的提升转换 `SX` 运算符 `TX` ，则这是最具体的转换运算符。
    * 否则，转换是不明确的，并发生编译时错误。
*  最后，应用转换：
    * 如果不 `S` 为 `SX` ，则执行从到的标准隐式转换 `S` `SX` 。
    * 调用最特定的转换运算符，以将转换 `SX` 为 `TX` 。
    * 如果不 `TX` 为 `T` ，则执行从到的标准隐式转换 `TX` `T` 。

### <a name="processing-of-user-defined-explicit-conversions"></a>处理用户定义的显式转换

用户定义的从类型到类型的显式转换的 `S` `T` 处理方式如下：

*  确定类型 `S0` 和 `T0` 。 如果 `S` 或 `T` 是可以为 null 的类型， `S0` 并且 `T0` 是其基础类型，则为; 否则 `S0` `T0` 分别为和 `S` `T` 。
*  查找类型集， `D` 用户定义的转换运算符将从其考虑。 `S0`如果是类或结构) ，则此集由 (，如果是类 `S0` 或结构、 (的基类（ `S0` 如果是类 `S0`) ）、 `T0` (if `T0` 是类或结构) ，以及 (的基类（ `T0` 如果 `T0` 是类) ）。
*  查找适用的用户定义转换运算符和提升转换运算符集 `U` 。 此集由中由的类或结构声明的用户定义的隐式或显式转换运算符，由中的 `D` 类型转换 `S` 为包含或包含的类型 `T` 。 如果 `U` 为空，则不定义转换，并发生编译时错误。
*  查找中运算符的最特定源类型 `SX` `U` ：
    * 如果中的任何运算符 `U` `S` 都为，则 `SX` 为 `S` 。
    * 否则，如果中的任何运算符 `U` 从包含的类型转换 `S` ，则 `SX` 是这些运算符的组合源类型集中包含程度最高的类型。 如果找不到最能包含的类型，则转换是不明确的，并发生编译时错误。
    * 否则， `SX` 是中运算符的组合源类型集中的最包含的类型 `U` 。 如果找不到完全包含的类型，则转换是不明确的，并发生编译时错误。
*  查找中运算符的最特定目标类型 `TX` `U` ：
    * 如果中的任何运算符 `U` 转换为 `T` ，则 `TX` 为 `T` 。
    * 否则，如果中的任何运算符 `U` 转换为所包含的类型，则 `T` `TX` 是这些运算符的组合目标类型集中最包含的类型。 如果找不到完全包含的类型，则转换是不明确的，并发生编译时错误。
    * 否则， `TX` 是中运算符的组合目标类型集中的包含程度最高的类型 `U` 。 如果找不到最能包含的类型，则转换是不明确的，并发生编译时错误。
*  查找最具体的转换运算符：
    * 如果 `U` 只包含一个从转换为的用户定义的转换 `SX` 运算符 `TX` ，则这是最具体的转换运算符。
    * 否则，如果 `U` 只包含一个从转换为的提升转换 `SX` 运算符 `TX` ，则这是最具体的转换运算符。
    * 否则，转换是不明确的，并发生编译时错误。
*  最后，应用转换：
    * 如果不 `S` 为 `SX` ，则执行从到的标准显式转换 `S` `SX` 。
    * 调用最特定的用户定义转换运算符，以将从转换 `SX` 为 `TX` 。
    * 如果不 `TX` 为 `T` ，则执行从到的标准显式转换 `TX` `T` 。

## <a name="anonymous-function-conversions"></a>匿名函数转换

*Anonymous_method_expression* 或 *lambda_expression* 归类为匿名函数 ([匿名函数表达式](expressions.md#anonymous-function-expressions)) 。 表达式没有类型，但可以隐式转换为兼容的委托类型或表达式树类型。 具体而言，匿名函数 `F` 与所提供的委托类型兼容 `D` ：

*  如果 `F` 包含一个 *anonymous_function_signature*，则 `D` 和 `F` 具有相同数量的参数。
*  如果不 `F` 包含 *anonymous_function_signature*，则 `D` 只要没有的参数 `D` 具有参数修饰符，就可以具有零个或多个任意类型的参数 `out` 。
*  如果 `F` 具有显式类型化参数列表，则中的每个参数 `D` 都具有与中相应参数相同的类型和修饰符 `F` 。
*  如果 `F` 具有隐式类型的参数列表，则 `D` 不包含 `ref` 或 `out` 参数。
*  如果的主体 `F` 是一个表达式，并且 `D` 具有 `void` 返回类型或 `F` 为 async 并且 `D` 具有返回类型 `Task` ，则当的每个参数都为 `F` 指定中的相应参数的类型时，的 `D` 主体 `F` 为 (wrt [表达式](expressions.md)) 的有效表达式，该表达式将允许作为) 的 *statement_expression* ([表达式语句](statements.md#expression-statements) 。
*  如果的主体 `F` 是一个语句块，并且 `D` 具有 `void` 返回类型或 `F` 为 async 并且 `D` 具有返回类型 `Task` ，则当的每个参数都为 `F` 指定中的相应参数的类型时，的 `D` 正文 `F` 为有效语句块 (wrt [块](statements.md#blocks)) ，其中没有 `return` 语句指定表达式。
*  如果的主体 `F` 是一个表达式，并且 `F` 为非 async 并且 `D` 具有非 void 返回类型 `T` ，*或者* `F` 是异步的，并且 `D` 具有返回类型，则 `Task<T>` 当的每个参数 `F` 都为指定中的相应参数的类型时，的 `D` 主体 `F` 将是有效的表达式， (可隐 [](expressions.md)式转换为) 的 wrt 表达式 `T` 。
*  如果的主体 `F` 是语句块，和 *均* `F` 为非 async 并且 `D` 具有非 void 返回类型 `T` ， *或者* `F` 是异步的，并且 `D` 具有返回类型，则 `Task<T>` 当的每个参数的类型为 `F` 中的相应参数时 `D` ，的主体 `F` 为有效语句块 (wrt 块 [，](statements.md#blocks) 其中每个语句都) `return` 指定一个可隐式转换为的表达式 `T` 。

为了简洁起见，本部分对任务类型 `Task` 和 `Task<T>` ([异步函数](classes.md#async-functions)) 使用缩写形式。

`F` `Expression<D>` 如果与 `F` 委托类型兼容，则 lambda 表达式与表达式树类型兼容 `D` 。 请注意，这不适用于匿名方法，只适用于 lambda 表达式。

某些 lambda 表达式不能转换为表达式树类型：即使转换 *存在*，它也会在编译时失败。 如果 lambda 表达式为，则为这种情况：

*  具有 *块* 体
*  包含简单赋值运算符或复合赋值运算符
*  包含动态绑定的表达式
*  为异步

下面的示例使用泛型委托类型， `Func<A,R>` 该类型表示一个函数，该函数采用类型的参数 `A` 并返回类型的值 `R` ：
```csharp
delegate R Func<A,R>(A arg);
```

在分配中
```csharp
Func<int,int> f1 = x => x + 1;                 // Ok

Func<int,double> f2 = x => x + 1;              // Ok

Func<double,int> f3 = x => x + 1;              // Error

Func<int, Task<int>> f4 = async x => x + 1;    // Ok
```
每个匿名函数的参数和返回类型都是从匿名函数所分配到的变量类型确定的。

第一次分配成功将匿名函数转换为委托类型 `Func<int,int>` ，因为当 `x` 给定类型时 `int` ， `x+1` 是可隐式转换为类型的有效表达式 `int` 。

同样，第二个赋值成功将匿名函数转换为委托类型， `Func<int,double>` 因为) 类型的 (的结果可 `x+1` `int` 隐式转换为 `double` 类型。

但是，第三个赋值是编译时错误，因为当 `x` 给定类型时， `double` `x+1` 类型)  (的结果 `double` 无法隐式转换为类型 `int` 。

第四个赋值成功将匿名 async 函数转换为委托类型， `Func<int, Task<int>>` 因为) 类型的 (的结果可 `x+1` `int` 隐式转换为该 `int` 任务类型的结果类型 `Task<int>` 。

匿名函数可能会影响重载决策，并参与类型推理。 有关更多详细信息，请参阅 [函数成员](expressions.md#function-members) 。

### <a name="evaluation-of-anonymous-function-conversions-to-delegate-types"></a>匿名函数转换到委托类型的计算

将匿名函数转换为委托类型会生成一个委托实例，该实例引用匿名函数，并且 (可能为空，这是在计算时处于活动状态的已捕获外部变量的) 集。 调用委托时，将执行匿名函数的主体。 使用委托引用的捕获外部变量集来执行正文中的代码。

从匿名函数生成的委托的调用列表包含单个项。 委托的确切目标对象和目标方法未指定。 具体而言，它是未指定的，即委托的目标对象是 `null` 、 `this` 封闭函数成员的值还是其他某个对象。

允许 (但不要求) 返回同一个委托实例，但不允许将语义相同的匿名函数（具有相同的)  (）转换为同一委托类型。 此处使用的术语在语义上是相同的，这意味着在所有情况下，匿名函数的执行将在给定相同参数的情况下生成相同的效果。 此规则允许对如下代码进行优化。

```csharp
delegate double Function(double x);

class Test
{
    static double[] Apply(double[] a, Function f) {
        double[] result = new double[a.Length];
        for (int i = 0; i < a.Length; i++) result[i] = f(a[i]);
        return result;
    }

    static void F(double[] a, double[] b) {
        a = Apply(a, (double x) => Math.Sin(x));
        b = Apply(b, (double y) => Math.Sin(y));
        ...
    }
}
```

由于两个匿名函数委托具有相同的 (空) 一组捕获的外部变量，并且由于匿名函数在语义上是相同的，因此允许编译器使委托引用相同的目标方法。 的确，允许编译器从两个匿名函数表达式返回完全相同的委托实例。

### <a name="evaluation-of-anonymous-function-conversions-to-expression-tree-types"></a>对表达式树类型的匿名函数转换的计算

将匿名函数转换为表达式树类型会生成表达式树， ([表达式树类型](types.md#expression-tree-types)) 。 更准确地说，对匿名函数转换的评估导致了表示匿名函数本身的结构的对象结构。 表达式树的准确结构以及用于创建它的确切过程是定义的实现。

### <a name="implementation-example"></a>实现示例

本部分介绍了使用其他 c # 构造实现的匿名函数转换的可能实现。 此处所述的实现基于 Microsoft c # 编译器所使用的相同原则，但它并不是强制性实现，也不是唯一可行的方法。 它只是简单地提到了转换为表达式树，因为其确切语义超出了本规范的范围。

本部分的其余部分提供了一些代码示例，其中包含具有不同特征的匿名函数。 对于每个示例，提供了对仅使用其他 c # 构造的代码的相应转换。 在这些示例中， `D` 假定使用标识符表示以下委托类型：
```csharp
public delegate void D();
```

匿名函数的最简单形式是捕获无外部变量：
```csharp
class Test
{
    static void F() {
        D d = () => { Console.WriteLine("test"); };
    }
}
```

这可以转换为引用编译器生成的静态方法（在其中放置匿名函数的代码）的委托实例化：
```csharp
class Test
{
    static void F() {
        D d = new D(__Method1);
    }

    static void __Method1() {
        Console.WriteLine("test");
    }
}
```

在下面的示例中，匿名函数引用的实例成员 `this` ：
```csharp
class Test
{
    int x;

    void F() {
        D d = () => { Console.WriteLine(x); };
    }
}
```

这可以转换为包含匿名函数代码的编译器生成的实例方法：
```csharp
class Test
{
    int x;

    void F() {
        D d = new D(__Method1);
    }

    void __Method1() {
        Console.WriteLine(x);
    }
}
```

在此示例中，匿名函数捕获本地变量：
```csharp
class Test
{
    void F() {
        int y = 123;
        D d = () => { Console.WriteLine(y); };
    }
}
```

局部变量的生存期现在必须至少扩展到匿名函数委托的生存期。 这可以通过将本地变量 "提升" 到编译器生成的类的字段来实现。 实例化局部变量 (实例 [化](expressions.md#instantiation-of-local-variables) 局部变量) 然后与创建编译器生成的类的实例，并且访问本地变量对应于访问编译器生成的类的实例中的字段。 此外，匿名函数会成为编译器生成的类的实例方法：
```csharp
class Test
{
    void F() {
        __Locals1 __locals1 = new __Locals1();
        __locals1.y = 123;
        D d = new D(__locals1.__Method1);
    }

    class __Locals1
    {
        public int y;

        public void __Method1() {
            Console.WriteLine(y);
        }
    }
}
```

最后，下面的匿名函数捕获和 `this` 两个具有不同生存期的局部变量：
```csharp
class Test
{
    int x;

    void F() {
        int y = 123;
        for (int i = 0; i < 10; i++) {
            int z = i * 2;
            D d = () => { Console.WriteLine(x + y + z); };
        }
    }
}
```

在这里，将为每个在其中捕获局部变量的语句块创建一个编译器生成的类，以便不同块中的局部变量可以具有独立生存期。 的实例 `__Locals2` （内部语句块的编译器生成的类）包含局部变量 `z` 和引用实例的字段 `__Locals1` 。  的一个实例 `__Locals1` ，它是外部语句块的编译器生成的类，其中包含局部变量 `y` 和引用 `this` 封闭函数成员的字段。 利用这些数据结构，可以通过的实例访问所有捕获的外部变量 `__Local2` ，匿名函数的代码就可以作为该类的实例方法实现。

```csharp
class Test
{
    void F() {
        __Locals1 __locals1 = new __Locals1();
        __locals1.__this = this;
        __locals1.y = 123;
        for (int i = 0; i < 10; i++) {
            __Locals2 __locals2 = new __Locals2();
            __locals2.__locals1 = __locals1;
            __locals2.z = i * 2;
            D d = new D(__locals2.__Method1);
        }
    }

    class __Locals1
    {
        public Test __this;
        public int y;
    }

    class __Locals2
    {
        public __Locals1 __locals1;
        public int z;

        public void __Method1() {
            Console.WriteLine(__locals1.__this.x + __locals1.y + z);
        }
    }
}
```

在此处用于捕获局部变量的相同技术也可以在将匿名函数转换为表达式树时使用：对编译器生成的对象的引用可以存储在表达式树中，对局部变量的访问可表示为这些对象上的字段访问。 此方法的优点是允许在委托和表达式树之间共享 "提升" 的局部变量。

## <a name="method-group-conversions"></a>方法组转换

隐式转换 () 存在从方法组 ([表达式分类](expressions.md#expression-classifications)) 到兼容的委托类型的隐[式](conversions.md#implicit-conversions)转换。 给定委托类型 `D` 和 `E` 归类为方法组的表达式时，存在从到的隐式转换 `E` `D` `E` 。如果包含至少一个适用于其正常窗体的方法 (适用的 [函数成员](expressions.md#applicable-function-member)) 到通过使用的参数类型和修饰符构造的参数列表 `D` ，如下所述。

以下中描述了从方法组转换为委托类型的编译时应用程序 `E` `D` 。 请注意，是否存在从到的隐式转换， `E` `D` 并不保证转换的编译时应用程序不会出错。

*  `M`选择与方法调用对应的单个方法 ([方法](expressions.md#method-invocations)调用) 的方法调用 `E(A)` ，并进行以下修改：
    * 参数列表 `A` 是表达式的列表，每个表达式都归类为变量，并且具有类型和修饰符 (`ref` 或的 `out` *formal_parameter_list* 中的相应参数) `D` 。
    * 考虑的候选方法只是那些适用于其正常形式 ([适用的函数成员](expressions.md#applicable-function-member)) ，而不是仅在其展开形式中适用的方法。
*  如果 [方法调用](expressions.md#method-invocations) 的算法产生错误，则会发生编译时错误。 否则，该算法将生成单个最佳方法， `M` 该方法的参数数目与相同 `D` ，转换被视为存在。
*  所选方法 `M` 必须兼容 (委托类型) [委托兼容性](delegates.md#delegate-compatibility) `D` ，否则将发生编译时错误。
*  如果所选方法 `M` 是实例方法，则与关联的实例表达式 `E` 确定委托的目标对象。
*  如果所选方法 M 是通过实例表达式上的成员访问方式表示的扩展方法，则该实例表达式将确定委托的目标对象。
*  转换结果为类型的值  `D` ，即引用所选方法和目标对象的新创建的委托。
*  请注意，如果 [方法调用](expressions.md#method-invocations) 的算法找不到实例方法，但在将调用 `E(A)` 作为扩展方法调用处理 ([扩展](expressions.md#extension-method-invocations) 方法调用) ，则此过程可能会导致创建扩展方法的委托。 因此，创建的委托将捕获扩展方法以及其第一个参数。

下面的示例演示方法组转换：
```csharp
delegate string D1(object o);

delegate object D2(string s);

delegate object D3();

delegate string D4(object o, params object[] a);

delegate string D5(int i);

class Test
{
    static string F(object o) {...}

    static void G() {
        D1 d1 = F;            // Ok
        D2 d2 = F;            // Ok
        D3 d3 = F;            // Error -- not applicable
        D4 d4 = F;            // Error -- not applicable in normal form
        D5 d5 = F;            // Error -- applicable but not compatible

    }
}
```

赋值 `d1` 隐式将方法组转换 `F` 为类型的值 `D1` 。

的赋值 `d2` 演示了如何创建一个委托，该委托指向 (逆变) 参数类型和派生程度更高的 (协变) 返回类型的派生方法。

`d3`如果该方法不适用，则分配为显示不存在转换的情况。

用于 `d4` 显示此方法必须以其正常形式适用的方式的赋值。

用于 `d5` 显示如何允许委托和方法的参数和返回类型不同于引用类型的赋值。

与所有其他隐式和显式转换一样，转换运算符可用于显式执行方法组转换。 因此，示例
```csharp
object obj = new EventHandler(myDialog.OkClick);
```
可以改为写入
```csharp
object obj = (EventHandler)myDialog.OkClick;
```

方法组可能会影响重载决策，并参与类型推理。 有关更多详细信息，请参阅 [函数成员](expressions.md#function-members) 。

方法组转换的运行时计算如下所示：

*  如果在编译时选择的方法是实例方法，或者它是作为实例方法访问的扩展方法，则将从与关联的实例表达式确定委托的目标对象 `E` ：
    * 计算实例表达式。 如果此计算导致异常，则不执行其他步骤。
    * 如果实例表达式为 *reference_type*，则由实例表达式计算的值将成为目标对象。 如果所选的方法是实例方法，并且目标对象为 `null` ， `System.NullReferenceException` 则会引发，而不执行进一步的步骤。
    * 如果实例表达式为 *value_type*，则会执行装箱运算 ([装箱转换](types.md#boxing-conversions) ，) 将值转换为对象，此对象将成为目标对象。
*  否则，所选方法为静态方法调用的一部分，并且委托的目标对象是 `null` 。
*  分配委托类型的新实例 `D` 。 如果没有足够的内存可用于分配新的实例， `System.OutOfMemoryException` 则会引发，而不执行进一步的步骤。
*  使用对在编译时确定的方法以及对上面计算的目标对象的引用来初始化新的委托实例。

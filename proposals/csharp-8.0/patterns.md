---
ms.openlocfilehash: 7ee72a2e3171940e93a2dc7048136a7ddc6b7698
ms.sourcegitcommit: 75ea1d741f92d6fda117caa189db17370f1b9f1c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/27/2020
ms.locfileid: "92684067"
---
# <a name="recursive-pattern-matching"></a>递归模式匹配

## <a name="summary"></a>摘要
[summary]: #summary

C # 的模式匹配扩展可实现与功能语言的代数数据类型和模式匹配的许多优势，但这种方式与基础语言的外观顺畅集成。 此方法的元素通过编程语言 [F #](http://www.msr-waypoint.net/pubs/79947/p29-syme.pdf "通过轻型语言进行的可扩展模式匹配") 和 [Scala](https://link.springer.com/content/pdf/10.1007%2F978-3-540-73589-2.pdf "与模式匹配的对象，页273")中的相关功能来实现。

## <a name="detailed-design"></a>详细设计
[design]: #detailed-design

### <a name="is-expression"></a>为 Expression

`is`扩展运算符以针对 *模式* 测试表达式。

```antlr
relational_expression
    : is_pattern_expression
    ;
is_pattern_expression
    : relational_expression 'is' pattern
    ;
```

这种形式的 *relational_expression* 除了 c # 规范中的现有窗体外。 如果标记左侧的 *relational_expression* `is` 未指定值或没有类型，则会发生编译时错误。

该模式的每个 *标识符* 都引入一个新的局部变量，该局部变量在运算符 (后是 *明确* 赋值的， `is` `true` 即 *在) true 时明确赋值* 。

> 注意：在技术上，和 constant_pattern 中的 *类型* 之间存在二义性 `is-expression` ，其中可能是 *constant_pattern* 限定标识符的有效分析。 我们尝试将其绑定为类型以与以前版本的语言兼容;只有当这种情况失败时，我们才会解决此问题，因为我们在其他上下文中执行了表达式，这 (必须是常量或) 类型。 这种多义性仅出现在表达式的右侧 `is` 。

### <a name="patterns"></a>模式

在 *is_pattern* 运算符、 *switch_statement* 和 *switch_expression* 中使用模式来表示数据的形状，而在这种情况下，将对其调用输入值) 的传入 (数据进行比较。 模式可以是递归的，以便可以将部分数据与子模式进行匹配。

```antlr
pattern
    : declaration_pattern
    | constant_pattern
    | var_pattern
    | positional_pattern
    | property_pattern
    | discard_pattern
    ;
declaration_pattern
    : type simple_designation
    ;
constant_pattern
    : constant_expression
    ;
var_pattern
    : 'var' designation
    ;
positional_pattern
    : type? '(' subpatterns? ')' property_subpattern? simple_designation?
    ;
subpatterns
    : subpattern
    | subpattern ',' subpatterns
    ;
subpattern
    : pattern
    | identifier ':' pattern
    ;
property_subpattern
    : '{' '}'
    | '{' subpatterns ','? '}'
    ;
property_pattern
    : type? property_subpattern simple_designation?
    ;
simple_designation
    : single_variable_designation
    | discard_designation
    ;
discard_pattern
    : '_'
    ;
```

#### <a name="declaration-pattern"></a>声明模式

```antlr
declaration_pattern
    : type simple_designation
    ;
```

如果测试成功，则 *declaration_pattern* 两个测试表达式是否为给定的类型并将其转换为该类型。 如果指定的是 *single_variable_designation* ，则这可能会引入给定标识符命名的给定类型的局部变量。 在模式匹配操作的结果为时，该局部变量是 *明确赋值* 的 `true` 。

此表达式的运行时语义是根据模式中的 *类型* 测试左侧 *relational_expression* 操作数的运行时类型。  如果它属于此运行时类型 (或某些子类型) 而不是，则的 `null` 结果 `is operator` 为 `true` 。

左侧和给定类型的静态类型的某些组合被视为不兼容并导致编译时错误。 `E` *pattern-compatible* `T` 如果存在标识转换、隐式引用转换、装箱转换、显式引用转换或从到的取消装箱转换， `E` `T` 或者其中一个类型是开放类型，则将静态类型的值称为模式与类型兼容。 如果类型的输入 `E` 不与它所匹配的类型模式中的 *类型**模式兼容* ，则会发生编译时错误。

类型模式对于执行引用类型的运行时类型测试非常有用，并且替换了方法

```csharp
var v = expr as Type;
if (v != null) { // code using v
```

稍微简单一些

```csharp
if (expr is Type v) { // code using v
```

如果 *类型* 是可以为 null 的值类型，则是错误的。

Type 模式可用于测试可为 null 的类型的值： `Nullable<T>` `T` `T2 id` 如果值为非 null，并且的类型 `T2` 为 `T` 或的某个基类型或接口， `T` 则类型为 (或装箱) 类型的值匹配。 例如，在代码片段中

```csharp
int? x = 3;
if (x is int v) { // code using v
```

语句的条件 `if` 是 `true` 在运行时，变量在 `v` `3` 块内保存类型的值 `int` 。 块后，变量 `v` 处于范围内但未明确赋值。

#### <a name="constant-pattern"></a>常量模式

```antlr
constant_pattern
    : constant_expression
    ;
```

常数模式根据常数值测试表达式的值。 常数可以是任何常数表达式，如文本、声明的变量的名称 `const` 或枚举常量。 如果输入值不是开放类型，则常量表达式将隐式转换为匹配的表达式的类型;如果输入值的类型与常量表达式的类型不 *兼容* ，则模式匹配操作为错误。

如果返回，模式 *c* 被视为与转换后的输入值 *e* 匹配 `object.Equals(c, e)` `true` 。

`e is null`在新编写的代码中，我们希望看到最常见的测试方法 `null` ，因为它无法调用用户定义的代码 `operator==` 。

#### <a name="var-pattern"></a>Var 模式

```antlr
var_pattern
    : 'var' designation
    ;
designation
    : simple_designation
    | tuple_designation
    ;
simple_designation
    : single_variable_designation
    | discard_designation
    ;
single_variable_designation
    : identifier
    ;
discard_designation
    : _
    ;
tuple_designation
    : '(' designations? ')'
    ;
designations
    : designation
    | designations ',' designation
    ;
```

如果 *指定* 为 *simple_designation* ，则表达式 *e* 与模式匹配。 换言之，与 *var 模式* 的匹配始终会成功，并提供 *simple_designation* 。 如果 *simple_designation* 为 *single_variable_designation* ，则 *e* 的值将绑定到新引入的局部变量。 局部变量的类型为 *e* 的静态类型。

如果 *指定* 的是 *tuple_designation* ，则该模式等效于 "格式指定 ..." 的 *positional_pattern* `(var` *designation* ， `)` 其中， *指定* 是在 *tuple_designation* 中找到的。  例如，模式与 `var (x, (y, z))` 等效 `(var x, (var y, var z))` 。

如果名称绑定到类型，则是错误的 `var` 。

#### <a name="discard-pattern"></a>丢弃模式

```antlr
discard_pattern
    : '_'
    ;
```

表达式 *e* 始终匹配模式 `_` 。 换言之，每个表达式都与丢弃模式匹配。

丢弃模式不能用作 *is_pattern_expression* 模式。

#### <a name="positional-pattern"></a>位置模式

位置模式检查输入值是否不是 `null` ，调用适当的 `Deconstruct` 方法，并对生成的值执行进一步的模式匹配。  如果输入值的类型与包含的类型相同，或者输入值的类型是元组类型，或者输入值的类型为 `Deconstruct` `object` 或 `ITuple` 且表达式的运行时类型为或，则它还支持类似元组的模式语法)  (`ITuple` 。

```antlr
positional_pattern
    : type? '(' subpatterns? ')' property_subpattern? simple_designation?
    ;
subpatterns
    : subpattern
    | subpattern ',' subpatterns
    ;
subpattern
    : pattern
    | identifier ':' pattern
    ;
```

如果省略该 *类型* ，则会将其作为输入值的静态类型。

给定模式 *类型* subpattern_list 的输入值匹配 `(` *subpattern_list* `)` 时，将通过在 *类型* 中搜索 `Deconstruct` ，并使用与析构声明相同的规则在其中选择一个方法，从而选择一个方法。

如果 *positional_pattern* 省略了类型，具有单个无 *标识符* 的子 *模式* ，则没有任何 *property_subpattern* ，并且没有 *simple_designation* 。 此消除是带圆括号和 *positional_pattern* 的 *constant_pattern* 之间的。

为了提取要与列表中的模式匹配的值，
- 如果省略了 *type* 并且输入值的类型是元组类型，则 subpatterns 的数目需要与该元组的基数相同。 每个元组元素与相应的子 *模式* 相匹配，如果所有这些元组元素都成功，则匹配成功。 如果任何子 *模式* 包含 *标识符* ，则必须在元组类型中的相应位置命名元组元素。
- 否则，如果适当的 `Deconstruct` 作为 *类型* 的成员存在，则在输入值的类型与 *类型* 不 *兼容* 的情况下，会发生编译时错误。 在运行时，将根据 *类型* 测试输入值。 如果此操作失败，则位置模式匹配失败。 如果成功，则会将输入值转换为此类型，并 `Deconstruct` 通过全新的编译器生成的变量调用来接收 `out` 参数。 收到的每个值都与相应的子 *模式* 相匹配，如果所有这些值都成功，则匹配成功。 如果任何子 *模式* 包含 *标识符* ，则必须在相应的位置为参数命名 `Deconstruct` 。
- 否则，如果省略了 *type* ，并且输入值的类型为 `object` 或， `ITuple` 或者是可通过隐式引用转换转换为的某种类型， `ITuple` 则在 subpatterns 中不会显示任何 *标识符* ，因此，我们将使用进行匹配 `ITuple` 。
- 否则，模式为编译时错误。

Subpatterns 在运行时匹配的顺序未指定，并且失败的匹配可能不会尝试匹配所有 subpatterns。

##### <a name="example"></a>示例

此示例使用此规范中所述的许多功能

``` c#
    var newState = (GetState(), action, hasKey) switch {
        (DoorState.Closed, Action.Open, _) => DoorState.Opened,
        (DoorState.Opened, Action.Close, _) => DoorState.Closed,
        (DoorState.Closed, Action.Lock, true) => DoorState.Locked,
        (DoorState.Locked, Action.Unlock, true) => DoorState.Closed,
        (var state, _, _) => state };
```

#### <a name="property-pattern"></a>属性模式

属性模式检查输入的值是否不是 `null` ，并以递归方式匹配通过使用可访问的属性或字段提取的值。

```antlr
property_pattern
    : type? property_subpattern simple_designation?
    ;
property_subpattern
    : '{' '}'
    | '{' subpatterns ','? '}'
    ;
```

如果 _property_pattern_ 的任何子 _模式_ 不包含 _标识符_ (它必须为第二种形式，而该窗体具有 _标识符_ ) ，则是错误的。  最后一个子模式后的尾随逗号是可选的。

请注意，空检查模式不太常用。 若要检查字符串 `s` 是否为非 null，可以编写以下任何形式

```csharp
if (s is object o) ... // o is of type object
if (s is string x) ... // x is of type string
if (s is {} x) ... // x is of type string
if (s is {}) ...
```

假设 *表达式 e 与* *type* `{` *property_pattern_list* `}` *类型* T 指定的类型 *T* 的 *模式不兼容* ，则在给定模式类型 property_pattern_list 的情况 *e* 下，它是编译时错误。 如果该类型不存在，则会将其作为 *e* 的静态类型。 如果 *标识符* 存在，则 *声明类型为* 的模式变量。 显示在其 *property_pattern_list* 左侧的每个标识符都必须指定可访问的可读属性或 *T* 的字段。如果 *property_pattern* 的 *simple_designation* 存在，它将定义类型为 *T* 的模式变量。

在运行时，将根据 *T* 对表达式进行测试。如果此操作失败，则属性模式匹配失败，结果为 `false` 。 如果成功，则将读取每个 *property_subpattern* 字段或属性，并将其值与其对应的模式相匹配。 整个匹配的结果仅适用于 `false` 其中任何一个的结果 `false` 。 未指定 subpatterns 匹配的顺序，并且失败的匹配可能与运行时的所有 subpatterns 都不匹配。 如果匹配成功并且 *property_pattern* 的 *simple_designation* 为 *single_variable_designation* ，则它会定义一个类型为 *T* 的变量，该变量被分配了匹配的值。

> 注意：属性模式可用于与匿名类型匹配。

##### <a name="example"></a>示例

```csharp
if (o is string { Length: 5 } s)
```

### <a name="switch-expression"></a>Switch 表达式

将 *switch_expression* 添加到 `switch` 表达式上下文的支持类似语义。

C # 语言语法增加了以下语法：

```antlr
multiplicative_expression
    : switch_expression
    | multiplicative_expression '*' switch_expression
    | multiplicative_expression '/' switch_expression
    | multiplicative_expression '%' switch_expression
    ;
switch_expression
    : range_expression 'switch' '{' '}'
    | range_expression 'switch' '{' switch_expression_arms ','? '}'
    ;
switch_expression_arms
    : switch_expression_arm
    | switch_expression_arms ',' switch_expression_arm
    ;
switch_expression_arm
    : pattern case_guard? '=>' expression
    ;
case_guard
    : 'when' null_coalescing_expression
    ;
```

*Switch_expression* 不允许作为 *expression_statement* 。

> 在未来的版本中，我们正在考虑这种情况。

*Switch_expression* 的类型是出现在 switch_expression_arm s 的标记右侧的表达式的 [*最佳通用类型*](https://github.com/dotnet/csharplang/blob/master/spec/expressions.md#finding-the-best-common-type-of-a-set-of-expressions) `=>` （如果存在此类 *switch_expression_arm* 类型），并且 switch 表达式的每个 arm 中的表达式都可以隐式转换为该类型。  此外，我们还添加了一个新的 *switch expression 转换* ，该转换是一种预定义的隐式转换，它是从 switch 表达式到每个类型的预定义隐式转换 `T` `T` 。

如果某些 *switch_expression_arm* 模式不会影响结果，则是错误的，因为某些以前的模式和临界将始终匹配。

如果 switch 表达式的某些 arm 处理其输入的每个值 *，则可以* 使用 switch 表达式。  如果 switch 表达式并非 *详尽* ，则编译器将生成警告。

在运行时， *switch_expression* 的结果是第一个 *switch_expression_arm* 的 *表达式* 的值， *switch_expression* 的左侧的表达式与 *switch_expression_arm* 的模式匹配，并且 *case_guard* 的 switch_expression_arm （如果存在 *）的计算* 结果为 `true` 。 如果没有这样的 *switch_expression_arm* ，则 *switch_expression* 会引发异常的实例 `System.Runtime.CompilerServices.SwitchExpressionException` 。

### <a name="optional-parens-when-switching-on-a-tuple-literal"></a>切换元组文本时的可选括号

若要使用 *switch_statement* 在元组文本上切换，你必须编写看似冗余的括号

```csharp
switch ((a, b))
{
```

允许

```csharp
switch (a, b)
{
```

当要打开的表达式是元组文本时，switch 语句的括号是可选的。

### <a name="order-of-evaluation-in-pattern-matching"></a>模式匹配中的求值顺序

为了更灵活地对模式匹配过程中执行的操作进行重新排序，可允许使用灵活性来提高模式匹配的效率。  (不强制执行的) 要求是在模式中访问的属性和析构方法，必须是 "纯" (副作用、幂等) 。 这并不意味着我们会将纯度作为语言概念添加，只是我们允许编译器灵活地进行重新排序操作。

**解决方法 2018-04-04 LDM** ：已确认：允许编译器对中的方法的调用重新排序 `Deconstruct` 、属性访问和调用， `ITuple` 并且可能会假设返回的值与多个调用的值相同。 编译器不应调用无法影响结果的函数，在以后对编译器生成的计算顺序进行任何更改之前，我们将非常小心。

### <a name="some-possible-optimizations"></a>一些可能的优化

模式匹配的编译可以利用模式的常见部分。 例如，如果 *switch_statement* 中两个连续模式的顶级类型测试为同一类型，则生成的代码可以跳过第二个模式的类型测试。

当某些模式为整数或字符串时，编译器可以为该语言的早期版本中的 switch 语句生成相同类型的代码。

有关这些类型的优化的详细信息，请参阅 [[Scott 和 Ramsey (2000) ]](https://www.cs.tufts.edu/~nr/cs257/archive/norman-ramsey/match.pdf "Match-Compilation 试探是否重要？")。

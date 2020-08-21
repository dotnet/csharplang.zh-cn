---
ms.openlocfilehash: a252f62178cc527c091b35780d750d1e61528c9f
ms.sourcegitcommit: 06ee75e6e3f1e0d9b4859ffed66024364d3d8f26
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/21/2020
ms.locfileid: "88720459"
---
# <a name="pattern-matching-for-c-7"></a>C # 7 的模式匹配

C # 的模式匹配扩展可实现与功能语言的代数数据类型和模式匹配的许多优势，但这种方式与基础语言的外观顺畅集成。 基本功能包括： [记录类型](../csharp-9.0/records.md)，这些类型的语义含义由数据形状描述;和模式匹配，这是一个新的表达式窗体，用于启用这些数据类型的极简洁的多级分解。 此方法的元素通过编程语言 [F #](https://www.microsoft.com/en-us/research/wp-content/uploads/2016/02/p29-syme.pdf "通过轻型语言进行的可扩展模式匹配") 和 [Scala](https://infoscience.epfl.ch/record/98468/files/MatchingObjectsWithPatterns-TR.pdf "与模式匹配的对象")中的相关功能来实现。

## <a name="is-expression"></a>Is 表达式

`is`扩展运算符以针对*模式*测试表达式。

```antlr
relational_expression
    : relational_expression 'is' pattern
    ;
```

这种形式的 *relational_expression* 除了 c # 规范中的现有窗体外。 如果标记左侧的 *relational_expression* `is` 未指定值或没有类型，则会发生编译时错误。

该模式的每个 *标识符* 都引入一个新的局部变量，该局部变量在运算符 (后是 *明确* 赋值的， `is` `true` 即 *在) true 时明确赋值* 。

> 注意：在技术上，和 constant_pattern 中的*类型*之间存在二义性 `is-expression` ，其中可能是*constant_pattern*限定标识符的有效分析。 我们尝试将其绑定为类型以与以前版本的语言兼容;仅当此操作失败时，我们会按我们在其他上下文中的方式解决该问题，第一件事情 (它必须是常量或) 类型。 这种多义性仅出现在表达式的右侧 `is` 。

## <a name="patterns"></a>模式

在 `is` 运算符和 *switch_statement* 中使用模式来表示要比较传入数据的数据的形状。 模式可以是递归的，以便可以将部分数据与子模式进行匹配。

```antlr
pattern
    : declaration_pattern
    | constant_pattern
    | var_pattern
    ;

declaration_pattern
    : type simple_designation
    ;

constant_pattern
    : shift_expression
    ;

var_pattern
    : 'var' simple_designation
    ;
```

> 注意：在技术上，和 constant_pattern 中的*类型*之间存在二义性 `is-expression` ，其中可能是*constant_pattern*限定标识符的有效分析。 我们尝试将其绑定为类型以与以前版本的语言兼容;仅当此操作失败时，我们会按我们在其他上下文中的方式解决该问题，第一件事情 (它必须是常量或) 类型。 这种多义性仅出现在表达式的右侧 `is` 。

### <a name="declaration-pattern"></a>声明模式

如果测试成功，则 *declaration_pattern* 两个测试表达式是否为给定的类型并将其转换为该类型。 如果 *simple_designation* 是一个标识符，则会引入给定标识符的给定类型的局部变量。 当模式匹配操作的结果为 true 时，将 *明确分配* 该局部变量。

```antlr
declaration_pattern
    : type simple_designation
    ;
```

此表达式的运行时语义是根据模式中的*类型*测试左侧*relational_expression*操作数的运行时类型。 如果它属于此运行时类型 (或某些子类型) ，则的结果 `is operator` 为 `true` 。 它声明一个由 *标识符* 命名的新局部变量，在结果为时，将为其分配左操作数的值 `true` 。

左侧和给定类型的静态类型的某些组合被视为不兼容并导致编译时错误。 `E` *pattern compatible* `T` 如果存在标识转换、隐式引用转换、装箱转换、显式引用转换或从到的取消装箱转换，则将静态类型的值称为模式与类型兼容 `E` `T` 。 如果类型为的表达式 `E` 与类型模式中的类型不兼容，则这是编译时错误。

> 注意： [在 c # 7.1 中，我们扩展此项](../csharp-7.1/generics-pattern-match.md) ，以便在输入类型或类型 `T` 为开放类型时允许模式匹配操作。 此段落替换为以下内容：
> 
> 左侧和给定类型的静态类型的某些组合被视为不兼容并导致编译时错误。 `E` *pattern compatible* `T` 如果存在标识转换、隐式引用转换、装箱转换、显式引用转换或从到的取消装箱转换， `E` `T` **或者 `E` 或 `T` 是开放类型**，则将静态类型的值称为模式与类型兼容。 如果类型为的表达式 `E` 与类型模式中的类型不兼容，则这是编译时错误。

声明模式对于执行引用类型的运行时类型测试非常有用，并且替换了方法

```csharp
var v = expr as Type;
if (v != null) { // code using v }
```

稍微简单一些

```csharp
if (expr is Type v) { // code using v }
```

如果 *类型* 是可以为 null 的值类型，则是错误的。

声明模式可用于测试可为 null 的类型的值： `Nullable<T>` `T` `T2 id` 如果值为非 null，并且的类型 `T2` 为 `T` 或的某个基类型或接口， `T` 则类型 (或装箱) 类型的值匹配。 例如，在代码片段中

```csharp
int? x = 3;
if (x is int v) { // code using v }
```

语句的条件 `if` 是 `true` 在运行时，变量在 `v` `3` 块内保存类型的值 `int` 。

### <a name="constant-pattern"></a>常量模式

```antlr
constant_pattern
    : shift_expression
    ;
```

常数模式根据常数值测试表达式的值。 常数可以是任何常数表达式，如文本、声明的变量的名称 `const` 、枚举常量或 `typeof` 表达式。

如果 *e* 和 *c* 均为整型类型，则当表达式的结果为时，该模式将被视为匹配 `e == c` `true` 。

否则，如果返回，则认为模式是匹配的 `object.Equals(e, c)` `true` 。 在这种情况下，如果 *e* 的静态类型与常量的类型不 *兼容* ，则会发生编译时错误。

### <a name="var-pattern"></a>Var 模式

```antlr
var_pattern
    : 'var' simple_designation
    ;
```

表达式 *e* 与 *var_pattern* 总是匹配。 换言之，与 *var 模式* 匹配始终会成功。 如果 *simple_designation* 是标识符，则在运行时， *e* 的值将绑定到新引入的局部变量。 局部变量的类型为 *e*的静态类型。

如果名称绑定到类型，则是错误的 `var` 。

## <a name="switch-statement"></a>Switch 语句

`switch`扩展了语句，以选择执行第一个具有与*switch 表达式*相匹配的模式的块。

```antlr
switch_label
    : 'case' complex_pattern case_guard? ':'
    | 'case' constant_expression case_guard? ':'
    | 'default' ':'
    ;

case_guard
    : 'when' expression
    ;
```

未定义模式匹配的顺序。 允许编译器按顺序匹配模式，并重复使用已匹配的模式的结果来计算其他模式的匹配结果。

如果存在 *case 保护* ，则其表达式的类型为 `bool` 。 它作为附加条件进行评估，此条件必须满足，才能认为需要满足此条件。

如果 *switch_label* 在运行时不起作用，则是错误的，因为在以前的情况下，其模式归入。 [TODO：我们应该更精确地了解编译器要求使用哪种方法才能达到这一判断。]

当且仅当该事例块精确包含一个*switch_label*时，在*switch_label*中声明的模式变量将在其 case 块中明确赋值。

[TODO：应指定何时可以访问 *switch 块* 。]

### <a name="scope-of-pattern-variables"></a>模式变量的范围

在模式中声明的变量的作用域如下：

- 如果模式为 case 标签，则该变量的作用域为 *事例块*。

否则，将在 *is_pattern* 表达式中声明变量，并且其作用域基于直接包含 *is_pattern* 表达式的表达式的构造，如下所示：

- 如果表达式位于 expression-bodied lambda 表达式中，则其作用域为 lambda 的主体。
- 如果表达式在 expression-bodied 方法或属性中，则它的作用域是方法或属性的主体。
- 如果表达式位于 `when` 子句的子句中，则 `catch` 该表达式的作用域是该 `catch` 子句。
- 如果表达式位于 *iteration_statement*中，则它的作用域只是该语句。
- 否则，如果表达式是在其他语句形式中，则它的作用域是包含语句的作用域。

为了确定作用域， *embedded_statement* 被视为位于其自身的作用域中。 例如， *if_statement* 的语法为

``` antlr
if_statement
    : 'if' '(' boolean_expression ')' embedded_statement
    | 'if' '(' boolean_expression ')' embedded_statement 'else' embedded_statement
    ;
```

因此，如果 *if_statement* 的受控语句声明一个模式变量，则其作用域限制为该 *embedded_statement*：

```csharp
if (x) M(y is var z);
```

在此示例中，的作用域 `z` 为嵌入语句 `M(y is var z);` 。

其他情况下，出于其他 (原因（例如，在参数的默认值或属性中）错误，这两者都是错误，因为这些上下文需要常量表达式) 。

> [在 c # 7.3 中，我们添加了以下上下文](../csharp-7.3/expression-variables-in-initializers.md) ，可在其中声明模式变量：
> - 如果表达式在 *构造函数初始值设定项*中，则其作用域为 *构造函数初始值设定项* 和构造函数的主体。
> - 如果表达式在字段初始值设定项中，其作用域就是它出现的 *equals_value_clause* 。
> - 如果表达式所在的查询子句所指定的值将转换为 lambda 体，则该表达式的作用域只是该表达式。

## <a name="changes-to-syntactic-disambiguation"></a>对句法歧义的更改

在某些情况下，c # 语法不明确，语言规范说明了如何解决这些歧义：

> #### <a name="7652-grammar-ambiguities"></a>7.6.5.2 语法多义性
> *简单名称* (§ 7.6.3) 和*成员访问* (第 7.6.5) 的生产可以在表达式语法中带来歧义。 例如，语句：
> ```csharp
> F(G<A,B>(7));
> ```
> 可以解释为对 `F` 具有两个参数和的的 `G < A` 调用 `B > (7)` 。 另外，它也可以解释为 `F` 使用一个自变量进行调用，该参数是对 `G` 具有两个类型参数和一个正则参数的泛型方法的调用。

> 如果可以将标记序列 (在上下文中) 为 *简单名称* (§ 7.6.3) ， *成员访问* (§ 7.6.5) 或 *指针成员访问* (§ 18.5.2) 第 * () §* 4.4.1 中，将检查紧跟结束标记的标记 `>` 。 如果是
> ```none
> (  )  ]  }  :  ;  ,  .  ?  ==  !=  |  ^
> ```
> 然后，将 *类型参数列表* 保留为 *简单名称*、 *成员访问* 或 *指针成员访问* 的一部分，并且会丢弃标记序列的任何其他可能分析。 否则， *类型参数列表* 不会被视为 *简单名称*、 *成员访问* 或 > *指针成员访问*的一部分，即使没有其他可能的标记序列分析也是如此。 请注意，在) *命名空间或类型名称* (§3.8 中分析*类型参数列表*时，不应用这些规则。 语句
> ```csharp
> F(G<A,B>(7));
> ```
> 根据此规则，将解释为 `F` 使用一个参数调用，该参数是对 `G` 具有两个类型参数和一个正则参数的泛型方法的调用。 语句
> ```csharp
> F(G < A, B > 7);
> F(G < A, B >> 7);
> ```
> 每个都将解释为对 `F` 具有两个参数的的调用。 语句
> ```csharp
> x = F < A > +y;
> ```
> 将解释为小于运算符、大于运算符和一元正运算符，就像编写了语句 `x = (F < A) > (+y)` ，而不是使用后跟二元加运算符的*类型参数列表*的*简单名称*。 在语句中
> ```csharp
> x = y is C<T> + z;
> ```
> 标记 `C<T>` 被解释为带有*类型参数列表*的*命名空间或类型名称*。

C # 7 中引入了许多更改，使这些歧义消除规则不再足以处理语言的复杂性。

### <a name="out-variable-declarations"></a>Out 变量声明

现在可以在 out 参数中声明变量：

```csharp
M(out Type name);
```

但类型可以是泛型： 

```csharp
M(out A<B> name);
```

由于参数的语言语法使用 *expression*，因此此上下文服从消除规则。 在这种情况下，关闭 `>` 后跟 *标识符*，该标识符不是允许它被视为 *类型参数列表*的标记之一。 因此，我建议 **将 *标识符* 添加到用于触发 *类型参数列表*的消除歧义的令牌集。**

### <a name="tuples-and-deconstruction-declarations"></a>元组和析构声明

元组文本运行完全相同的问题。 考虑元组表达式

```csharp
(A < B, C > D, E < F, G > H)
```

在旧的 c # 6 用于分析参数列表的规则下，此方法将分析为具有四个元素的元组，以 `A < B` 第一个元素开头。 但是，如果在析构的左侧出现这种情况，我们需要由 *标识符* 标记触发的歧义消除，如上所述：

```csharp
(A<B,C> D, E<F,G> H) = e;
```

这是声明两个变量的析构声明，其中第一个变量的类型为 `A<B,C>` ，名称为 `D` 。 换言之，元组文本包含两个表达式，其中每个表达式都是一个声明表达式。

为了简化规范和编译器，我建议将此元组文本作为两元素元组进行分析，无论其出现在赋值) 的左侧 (。 这是上一节中所述歧义消除的自然结果。

### <a name="pattern-matching"></a>模式匹配

模式匹配会引入一个新的上下文，在此上下文中会出现表达式类型的多义性。 运算符的右侧 `is` 是一个类型。 现在，它可以是类型或表达式，如果它是类型，则可以后跟标识符。 从技术上讲，可以更改现有代码的含义：

```csharp
var x = e is T < A > B;
```

可在 c # 6 规则中将其分析为

```csharp
var x = ((e is T) < A) > B;
```

但在 "c # 7 规则" 下的 "不符合) 建议的歧义" (将被分析为

```csharp
var x = e is T<A> B;
```

，它声明 `B` 类型的变量 `T<A>` 。 幸运的是，本机编译器和 Roslyn 编译器都有 bug，因此它们给出了 c # 6 代码的语法错误。 因此，不需要考虑这种特殊的重大更改。

模式匹配引入了其他标记，这些标记应促进选择类型的多义性解析。 下面的现有有效 c # 6 代码的示例将中断，无需额外消除规则：

```csharp
var x = e is A<B> && f;            // &&
var x = e is A<B> || f;            // ||
var x = e is A<B> & f;             // &
var x = e is A<B>[];               // [
```

### <a name="proposed-change-to-the-disambiguation-rule"></a>消除歧义规则的建议更改

我建议修订规范，以更改歧义令牌的列表

>
```none
(  )  ]  }  :  ;  ,  .  ?  ==  !=  |  ^
```

设置为

>
```none
(  )  ]  }  :  ;  ,  .  ?  ==  !=  |  ^  &&  ||  &  [
```

在某些上下文中，我们将 *标识符* 视为歧义标记。 这些上下文包括：要消除的标记的序列紧靠在 `is` `case` `out` 分析元组文本的第一个元素时，或在分析元组文本的第一个元素时出现 (，这种情况下，令牌前面是 `(` 或 `:` ，标识符后跟 `,`) 或元组文本的后续元素。

### <a name="modified-disambiguation-rule"></a>修改后消除规则

修改后的消除歧义规则如下所示

> 如果可以将标记序列 (在上下文中) 为 *简单名称* (§ 7.6.3) ， *成员访问* (§ 7.6.5) 或 *指针成员访问* (§ 18.5.2) 第 * () §* 4.4.1 中，将检查紧跟在结束标记后面的标记 `>` ，查看其是否为
> - 之一 `(  )  ]  }  :  ;  ,  .  ?  ==  !=  |  ^  &&  ||  &  [` ; 或
> - 关系运算符之一 `<  >  <=  >=  is as` ; 或
> - 上下文查询关键字显示在查询表达式中;或
> - 在某些上下文中，我们将 *标识符* 视为歧义标记。 这些上下文包括：要消除的标记的序列紧靠在 `is` `case` `out` 分析元组文本的第一个元素时，或在分析元组文本的第一个元素时出现 (，这种情况下，令牌前面是 `(` 或 `:` ，标识符后跟 `,`) 或元组文本的后续元素。
> 
> 如果以下标记在此列表中，或在此类上下文中为标识符，则会将 *类型参数列表* 保留为 *简单名称*、 *成员访问* 或  *指针成员访问* 的一部分，并且会丢弃标记序列的任何其他可能分析。  否则， *类型参数列表* 不被视为 *简单名称*、 *成员访问* 或 *指针访问权限*的一部分，即使没有其他可能的标记序列分析也是如此。 请注意，在) *命名空间或类型名称* (§3.8 中分析*类型参数列表*时，不应用这些规则。

### <a name="breaking-changes-due-to-this-proposal"></a>由于此建议造成的重大更改

由于此建议的消除歧义规则，不能识别重大更改。

### <a name="interesting-examples"></a>有趣的示例

下面是这些歧义消除规则的一些有趣的结果：

表达式 `(A < B, C > D)` 是具有两个元素的元组，每个元素都是比较。

表达式 `(A<B,C> D, E)` 是具有两个元素的元组，其中第一个元素是声明表达式。

调用 `M(A < B, C > D, E)` 具有三个参数。

调用 `M(out A<B,C> D, E)` 具有两个参数，第一个参数是 `out` 声明。

表达式 `e is A<B> C` 使用声明表达式。

Case 标签 `case A<B> C:` 使用声明表达式。

## <a name="some-examples-of-pattern-matching"></a>模式匹配的一些示例

### <a name="is-as"></a>为-As

我们可以替换用法

```csharp
var v = expr as Type;   
if (v != null) {
    // code using v
}
```

稍微简单、直接

```csharp
if (expr is Type v) {
    // code using v
}
```

### <a name="testing-nullable"></a>测试可为 null

我们可以替换用法

```csharp
Type? v = x?.y?.z;
if (v.HasValue) {
    var value = v.GetValueOrDefault();
    // code using value
}
```

稍微简单、直接

```csharp
if (x?.y?.z is Type value) {
    // code using value
}
```

### <a name="arithmetic-simplification"></a>算术简化

假设我们定义一组递归类型，以表示每个单独的提议)  (的表达式：

```csharp
abstract class Expr;
class X() : Expr;
class Const(double Value) : Expr;
class Add(Expr Left, Expr Right) : Expr;
class Mult(Expr Left, Expr Right) : Expr;
class Neg(Expr Value) : Expr;
```

现在，我们可以定义一个函数来计算表达式的 (unreduced) 导数：

```csharp
Expr Deriv(Expr e)
{
  switch (e) {
    case X(): return Const(1);
    case Const(*): return Const(0);
    case Add(var Left, var Right):
      return Add(Deriv(Left), Deriv(Right));
    case Mult(var Left, var Right):
      return Add(Mult(Deriv(Left), Right), Mult(Left, Deriv(Right)));
    case Neg(var Value):
      return Neg(Deriv(Value));
  }
}
```

表达式 simplifier 演示位置模式：

```csharp
Expr Simplify(Expr e)
{
  switch (e) {
    case Mult(Const(0), *): return Const(0);
    case Mult(*, Const(0)): return Const(0);
    case Mult(Const(1), var x): return Simplify(x);
    case Mult(var x, Const(1)): return Simplify(x);
    case Mult(Const(var l), Const(var r)): return Const(l*r);
    case Add(Const(0), var x): return Simplify(x);
    case Add(var x, Const(0)): return Simplify(x);
    case Add(Const(var l), Const(var r)): return Const(l+r);
    case Neg(Const(var k)): return Const(-k);
    default: return e;
  }
}
```

---
ms.openlocfilehash: e3027c3fe9b05d3e5379535c010e148e4fbbc761
ms.sourcegitcommit: 2661a4b3950d2ec5c557b025af13e52ba200fd05
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/16/2020
ms.locfileid: "90687170"
---
# <a name="pattern-matching-changes-for-c-90"></a>C # 9.0 的模式匹配更改

我们正在考虑对 c # 9.0 的模式匹配的一小部分增强功能，这些增强功能具有自然协作，并且能够正常处理许多常见的编程问题：
- https://github.com/dotnet/csharplang/issues/2925 类型模式
- https://github.com/dotnet/csharplang/issues/1350 用圆括号模式强制或强调新组合器的优先级
- https://github.com/dotnet/csharplang/issues/1350 需要两个 `and` 不同模式来匹配的联合模式;
- https://github.com/dotnet/csharplang/issues/1350`or`需要匹配两种不同模式的析取范式模式;
- https://github.com/dotnet/csharplang/issues/1350`not`需要指定模式*不*匹配的求反模式; 和
- https://github.com/dotnet/csharplang/issues/812 需要输入值小于、小于或等于的关系模式，等等。

## <a name="parenthesized-patterns"></a>圆括号模式

圆括号模式允许编程人员在任何模式两边加上括号。  这对于 c # 8.0 中的现有模式不起作用，但新模式组合器引入了程序员可能要重写的优先级。

```antlr
primary_pattern
    : parenthesized_pattern
    | // all of the existing forms
    ;
parenthesized_pattern
    : '(' pattern ')'
    ;
```

## <a name="type-patterns"></a>类型模式

我们允许一种类型作为模式：

``` antlr
primary_pattern
    : type-pattern
    | // all of the existing forms
    ;
type_pattern
    : type
    ;
```

这 retcons 将现有的 *类型表达式* 作为模式 *表达式* ，其中模式为 *类型模式*，但我们不会更改编译器生成的语法树。

一个微妙的实现问题是，此语法是不明确的。  例如，可以在 `a.b` 类型上下文中将字符串分析为限定 (名，) 或在表达式上下文) 中 (点表达式。  编译器已经能够处理与点式表达式相同的限定名，以便处理类似的内容 `e is Color.Red` 。  编译器的语义分析将进一步扩展，以便能够绑定 (语法) 常量模式 (例如，点表达式) 为类型，以便将其视为绑定类型模式，以便支持此构造。

完成此更改后，你将能够编写
```csharp
void M(object o1, object o2)
{
    var t = (o1, o2);
    if (t is (int, string)) {} // test if o1 is an int and o2 is a string
    switch (o1) {
        case int: break; // test if o1 is an int
        case System.String: break; // test if o1 is a string
    }
}
```

## <a name="relational-patterns"></a>关系模式

与常数值相比，关系模式允许程序员表达输入值必须满足关系约束：

``` C#
    public static LifeStage LifeStageAtAge(int age) => age switch
    {
        < 0 =>  LifeStage.Prenatal,
        < 2 =>  LifeStage.Infant,
        < 4 =>  LifeStage.Toddler,
        < 6 =>  LifeStage.EarlyChild,
        < 12 => LifeStage.MiddleChild,
        < 20 => LifeStage.Adolescent,
        < 40 => LifeStage.EarlyAdult,
        < 65 => LifeStage.MiddleAdult,
        _ =>    LifeStage.LateAdult,
    };
```

关系模式支持 `<` 所有内置类型上的关系运算符、、 `<=` 和， `>` `>=` 它们支持在表达式中使用两个具有相同类型的操作数的内置类型。 具体而言，我们支持、、、 `sbyte` `byte` `short` `ushort` 、 `int` 、 `uint` 、 `long` `ulong` `char` `float` `double` `decimal` `nint` `nuint` 、、、、、、、、、、、、、和的所有关系模式。

```antlr
primary_pattern
    : relational_pattern
    ;
relational_pattern
    : '<' relational_expression
    | '<=' relational_expression
    | '>' relational_expression
    | '>=' relational_expression
    ;
```

表达式需要计算为常量值。  如果该常数值为或，则是错误的 `double.NaN` `float.NaN` 。  如果表达式为 null 常量，则是错误的。

如果输入的类型为，并且定义了适当的内置二元关系运算符，并且该运算符适用于输入作为其左操作数，而给定常量作为其右操作数，则该运算符的计算将被视为关系模式的意义。  否则，使用显式的可为 null 或取消装箱转换将输入转换为表达式的类型。  如果不存在这样的转换，则会发生编译时错误。  如果转换失败，该模式将被视为不匹配。  如果转换成功，则模式匹配操作的结果是计算表达式的结果 `e OP v` ，其中为转换后的 `e` 输入， `OP` 是关系运算符， `v` 是常数表达式。

## <a name="pattern-combinators"></a>模式组合器

模式 *组合器* 允许使用 (匹配两个不同模式 `and` ，这可以通过重复使用) 来扩展到任意数量的模式，方法是 `and` 使用 `or` (ditto) ，或者使用的是模式的 *求反* `not` 。

连结符的常见用途是使用

``` c#
if (e is not null) ...
```

比当前用法更具可读性 `e is object` ，此模式清楚地表明它正在检查非 null 值。

`and`和 `or` 组合器对于测试值范围很有用

``` c#
bool IsLetter(char c) => c is >= 'a' and <= 'z' or >= 'A' and <= 'Z';
```

此示例演示 `and` 将具有更高的分析优先级 (例如，绑定的) 将更严格 `or` 。  程序员可以使用 *带括号的模式* 来使优先显式：

``` c#
bool IsLetter(char c) => c is (>= 'a' and <= 'z') or (>= 'A' and <= 'Z');
```

与所有模式一样，这些组合器可用于需要模式的任何上下文中，其中包括嵌套模式、 *is 模式表达式*、 *switch 表达式*和 switch 语句的 case 标签模式。

```antlr
pattern
    : disjunctive_pattern
    ;
disjunctive_pattern
    : disjunctive_pattern 'or' conjunctive_pattern
    | conjunctive_pattern
    ;
conjunctive_pattern
    : conjunctive_pattern 'and' negated_pattern
    | negated_pattern
    ;
negated_pattern
    : 'not' negated_pattern
    | primary_pattern
    ;
primary_pattern
    : // all of the patterns forms previously defined
    ;
```

## <a name="change-to-7542-grammar-ambiguities"></a>更改为7.5.4.2 语法多义性

由于引入了 *type 模式*，因此泛型类型可能出现在标记之前 `=>` 。  因此，我们将添加 `=>` 到 *7.5.4.2 语法多义性* 中列出的一组标记，以允许消除 `<` 开始类型参数列表的的歧义。  另请参阅 https://github.com/dotnet/roslyn/issues/47614。

## <a name="open-issues-with-proposed-changes"></a>打开建议更改的问题

### <a name="syntax-for-relational-operators"></a>关系运算符的语法

是否为 `and` 、 `or` 和 `not` 某种上下文关键字？  如果是这样，则是否存在重大更改 (例如，将其用作 *声明模式*) 中的指示符。

### <a name="semantics-eg-type-for-relational-operators"></a>语义 (例如，类型) 关系运算符

我们希望支持使用关系运算符在表达式中进行比较的所有基元类型。  简单情况下的含义是清晰的

``` c#
bool IsValidPercentage(int x) => x is >= 0 and <= 100;
```

但如果输入不是基元类型，我们会尝试将其转换为哪种类型？

``` c#
bool IsValidPercentage(object x) => x is >= 0 and <= 100;
```

我们建议，当输入类型已经是比较的类型时，这是比较的类型。 但是，当输入不是可比较的基元时，我们将关系视为包含对关系右侧的常量类型的隐式类型测试。  如果程序员打算支持多个输入类型，则必须显式完成：

``` c#
bool IsValidPercentage(object x) => x is
    >= 0 and <= 100 or    // integer tests
    >= 0F and <= 100F or  // float tests
    >= 0D and <= 100D;    // double tests
```

### <a name="flowing-type-information-from-the-left-to-the-right-of-and"></a>从左侧的左侧流向类型信息 `and`

建议在编写 `and` 连结符时，在左侧了解的有关顶级类型的信息可以流向右侧。  例如：

```csharp
bool isSmallByte(object o) => o is byte and < 100;
```

此处，第二个模式的 *输入类型* 将被缩小的 *类型的收缩* 要求范围内 `and` 。  我们将为所有模式定义类型收缩语义，如下所示。  模式的 *缩小类型* `P` 定义如下：
1. 如果 `P` 为类型模式，则 *缩小的类型* 为类型模式类型的类型。
2. 如果 `P` 是声明模式，则缩小后的 *类型* 为声明模式类型的类型。
3. 如果 `P` 是提供显式类型的递归模式，则缩小后的 *类型* 为该类型。
4. 如果 `P` [通过的规则匹配](https://github.com/dotnet/csharplang/blob/master/proposals/csharp-8.0/patterns.md#positional-pattern) `ITuple` ，则缩小的 *类型* 为类型 `System.Runtime.CompilerServices.ITuple` 。
5. 如果 `P` 是常量模式，其中的常量不是 null 常量，并且表达式没有到*输入类型*的*常量表达式转换*，则*缩小的类型*是常量的类型。
6. 如果 `P` 是一个关系模式，其中的常量表达式没有到*输入类型*的*常量表达式转换*，则*缩小*后的类型是常量的类型。
7. 如果 `P` 是 `or` 模式，则如果存在此类通用类型，则 *缩小的类型* 是 subpatterns 的 *缩小类型* 的通用类型。 出于此目的，通用类型算法只考虑标识、装箱和隐式引用转换，并将一系列模式的所有 subpatterns 都视为 `or` (忽略带圆括号的模式) 。
8. 如果 `P` 是 `and` 模式，则缩小后的 *类型* 为适当模式的 *缩小类型* 。 而且，左侧模式的 *缩小类型* 是正确模式的 *输入类型* 。
9. 否则，的 *缩小类型* `P` 为 `P` 的输入类型。

### <a name="variable-definitions-and-definite-assignment"></a>变量定义和明确赋值

添加 `or` 和模式会在 `not` 模式变量和明确赋值方面产生一些有趣的新问题。  由于变量通常可以最多声明一次，因此，在模式的一侧声明的任何模式变量都 `or` 不会在模式匹配时明确赋值。  同样，在模式匹配时，不应将在模式中声明的变量 `not` 明确赋值。  解决这种情况的最简单方法是禁止在这些上下文中声明模式变量。  但是，这可能过于严格。  还有其他方法可以考虑。

值得考虑的一种情况是，

``` csharp
if (e is not int i) return;
M(i); // is i definitely assigned here?
```

目前这不起作用，因为对于 *is 模式表达式*，只会将模式变量视为 *明确分配* ，其中模式 *变量为 true* ( ") 时明确赋值。

从程序员的角度来看，支持这一 (更简单，) 同时也添加了对求反条件语句的支持 `if` 。  即使我们添加了此类支持，程序员也可能想知道上述代码片段不起作用。  另一方面，在中，这种情况并不 `switch` 太合理，因为 *当 false* 会有意义时，程序中没有相应的点可明确赋值。  是否允许在模式表达式中使用它 *，* 但在允许模式的其他上下文中不允许这样做？  这似乎是不规则的。

与此相关的是在 *析取范式模式*下明确赋值的问题。

```csharp
if (e is 0 or int i)
{
    M(i); // is i definitely assigned here?
}
```

仅 `i` 当输入不为零时，才应明确赋值。  但由于我们不知道输入值是否为零或不在块中，因此 `i` 未明确赋值。  但是，如果我们允许 `i` 在不同的互相排斥模式下声明怎么办？

```csharp
if ((e1, e2) is (0, int i) or (int i, 0))
{
    M(i);
}
```

此处，变量在 `i` 块中是明确赋值的，当找到零个元素时，将从该元组的另一个元素中获取该变量的值。

还建议您允许变量 (在 case 块的每个事例中定义) ：

```csharp
    case (0, int x):
    case (int x, 0):
        Console.WriteLine(x);
```

若要进行任何此类工作，必须仔细定义允许此类多个定义的位置，并在哪些条件下将变量视为明确赋值。

如果以后 (建议) ，我们会选择推迟此类工作
- 在 `not` 或下 `or` ，不能声明模式变量。

接下来，我们有时间开发一些经验，这些体验可让您更好地了解稍后的可能值。

### <a name="diagnostics-subsumption-and-exhaustiveness"></a>诊断、subsumption 和 exhaustiveness

这些新模式窗体为 diagnosable 程序员错误引入了许多新机会。  我们需要确定将诊断哪些类型的错误，以及如何执行此操作。  下面是一些示例：

``` csharp
case >= 0 and <= 100D:
```

这种情况永远不会与 (匹配，因为输入不能同时为 `int` 和 `double`) 。  我们检测到不能匹配的情况时，我们已遇到错误，但其措辞 ( "switch case 已被上一事例处理"，并且 "模式已经由 switch 表达式的前一 arm 处理" ) 可能会在新方案中产生误导。  我们可能必须修改措辞，只是说该模式将永远不会与输入匹配。

``` csharp
case 1 and 2:
```

同样，这会是一个错误，因为值不能同时为 `1` 和 `2` 。

``` csharp
case 1 or 2 or 3 or 1:
```

这种情况可以匹配，但 `or 1` 在结束时，不会对模式添加任何意义。  我建议，每当复合模式的某些对或 disjunct 不定义模式变量或影响匹配值集时，将产生错误。

``` csharp
case < 2: break;
case 0 or 1 or 2 or 3 or 4 or 5: break;
```

此处，不向 `0 or 1 or` 第二种情况添加任何内容，因为这些值已被第一种情况处理。  这也是一个错误。

``` csharp
byte b = ...;
int x = b switch { <100 => 0, 100 => 1, 101 => 2, >101 => 3 };
```

诸如这样的 switch 表达式应被视为 *详尽* 的 (它处理) 的所有可能输入值。

在 c # 8.0 中，如果具有类型的输入的 switch 表达式 `byte` 包含的最终 arm 的模式与 *丢弃模式* 或 *var 模式*)  (，则该表达式将被视为穷举。  即使是在 c # 8 中，对每个非重复值具有 arm 的 switch 表达式也 `byte` 不会被视为详尽。  为了正确地处理关系模式的 exhaustiveness，我们还需要处理这种情况。  从技术上讲，这会是一项重大更改，但用户不可能注意到。

---
ms.openlocfilehash: 138d66c669e5db527ffe24d2bbb004b5fd2f6db9
ms.sourcegitcommit: 00b0f04deb7ad7b7a1f16ed8e9ec40620d87e774
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/06/2021
ms.locfileid: "102250388"
---
# <a name="lambda-improvements"></a>Lambda 改进

## <a name="summary"></a>总结
建议的更改：
1. 允许具有属性的 lambda
2. 允许具有显式返回类型的 lambda
3. 推断 lambda 和方法组的自然委托类型

## <a name="motivation"></a>动机
对 lambda 的属性的支持将提供方法和本地函数的奇偶校验。

对显式返回类型的支持将提供与 lambda 参数的对称，可在其中指定显式类型。
如果允许显式返回类型，则还会在嵌套的 lambda 中提供对编译器性能的控制，在这种情况下，重载决策必须绑定 lambda 体来确定该签名。

Lambda 表达式和方法组的自然类型将允许在没有显式委托类型（包括作为声明中的初始值设定项）的情况下，可以使用 lambda 和方法组。 `var`

对于 lambda 和方法组，需要显式委托类型对于客户来说是一个摩擦点，并且已成为 ASP.NET 中的最新[工作的障碍。](https://github.com/dotnet/aspnetcore/pull/29878)

[ASP.NET MapAction](https://github.com/dotnet/aspnetcore/pull/29878) 没有建议的更改 (`MapAction()` 采用 `System.Delegate` 参数) ：
```csharp
[HttpGet("/")] Todo GetTodo() => new(Id: 0, Name: "Name");
app.MapAction((Func<Todo>)GetTodo);

[HttpPost("/")] Todo PostTodo([FromBody] Todo todo) => todo);
app.MapAction((Func<Todo, Todo>)PostTodo);
```

用于方法组的自然类型的[ASP.NET MapAction](https://github.com/dotnet/aspnetcore/pull/29878) ：
```csharp
[HttpGet("/")] Todo GetTodo() => new(Id: 0, Name: "Name");
app.MapAction(GetTodo);

[HttpPost("/")] Todo PostTodo([FromBody] Todo todo) => todo);
app.MapAction(PostTodo);
```

Lambda 表达式的属性和自然类型[ASP.NET MapAction](https://github.com/dotnet/aspnetcore/pull/29878) ：
```csharp
app.MapAction([HttpGet("/")] () => new Todo(Id: 0, Name: "Name"));
app.MapAction([HttpPost("/")] ([FromBody] Todo todo) => todo);
```

## <a name="attributes"></a>属性
特性可以添加到 lambda 表达式。
```csharp
f = [MyAttribute] x => x;          // [MyAttribute]lambda
f = [MyAttribute] (int x) => x;    // [MyAttribute]lambda
f = [MyAttribute] static x => x;   // [MyAttribute]lambda
f = [return: MyAttribute] () => 1; // [return: MyAttribute]lambda
```

_如果将属性添加到整个表达式，则参数列表应为括号？应 `[MyAttribute] x => x` 禁用 (吗？如果是这样，该怎么办 `[MyAttribute] static x => x` ？ )_

特性可以添加到用显式类型声明的 lambda 参数。
```csharp
f = ([MyAttribute] x) => x;      // syntax error
f = ([MyAttribute] int x) => x;  // [MyAttribute]x
```

用语法声明的匿名方法不支持特性 `delegate { }` 。
```csharp
f = [MyAttribute] delegate { return 1; };         // syntax error
f = delegate ([MyAttribute] int x) { return x; }; // syntax error
```

Lambda 表达式或 lambda 参数上的特性将发送到映射到 lambda 的方法的元数据中。

通常，客户不应依赖于 lambda 表达式和本地函数从源映射到元数据的方式。 如何发出 lambda 和本地函数可以在编译器版本之间进行更改。

此处建议的更改以驱动的方案为目标 `Delegate` 。
检查 `MethodInfo` 与实例关联的以 `Delegate` 确定 lambda 表达式或局部函数的签名（包括任何显式属性和编译器发出的其他元数据，如默认参数），这应该是有效的。
这样，ASP.NET 的团队就可以将 lambda 和本地函数的行为与普通方法相同。

## <a name="explicit-return-type"></a>显式返回类型
可以在参数列表之后指定显式返回类型。
```csharp
f = () : T => default;              // () : T
f = x : short => 1;                 // <unknown> : short
f = (ref int x) : ref int => ref x; // ref int : ref int
f = static _ : void => { };         // <unknown> : void
```

用语法声明的匿名方法不支持显式返回类型 `delegate { }` 。
```csharp
f = delegate : int { return 1; };         // syntax error
f = delegate (int x) : int { return x; }; // syntax error
```

## <a name="natural-delegate-type"></a>自然委托类型
如果参数类型为显式，并且返回类型为显式，或者正文中所有表达式的自然类型都有通用类型，则 lambda 表达式具有自然类型 `return` 。

自然类型是委托类型，其中参数类型为显式 lambda 参数类型，返回类型 `R` 为：
- 如果 lambda 返回类型为显式，则使用该类型;
- 如果 lambda 没有返回表达式，返回类型为 `void` 或 `System.Threading.Tasks.Task` if `async` ;
- 如果正文中所有表达式的自然类型的通用类型 `return` 为类型 `R0` ，则返回类型为 `R0` 或 `System.Threading.Tasks.Task<R0>` `async` 。

如果方法组包含单个方法，则该方法组具有自然类型。

方法组可以引用扩展方法。 通常，方法组解析会以延迟方式搜索扩展方法，只循环访问连续的命名空间范围，直到找到与目标类型匹配的扩展方法。 但若要确定自然类型需要搜索所有命名空间范围， _若要最大程度地减少不必要的绑定，可能只在没有目标类型的情况下（即，只在需要时才计算自然类型）计算自然类型。_

Lambda 或方法组的委托类型以及参数类型 `P1, ..., Pn` 和返回类型 `R` 为：
- 如果任何参数或返回值不是通过值，或者有16个以上的参数，或者任何参数类型或返回值不是有效的类型参数 (则 `(int* p) => { }`) ，则该委托是一个具有与 `internal` lambda 或方法组相匹配的签名的合成匿名委托类型，并且具有参数名 `arg1, ..., argn` 或 `arg` if 一个参数;
- 如果 `R` 为 `void` ，则委托类型为 `System.Action<P1, ..., Pn>` ;
- 否则，委托类型为 `System.Func<P1, ..., Pn, R>` 。

`modopt()` 在 `modreq()` 对应的委托类型中忽略方法组签名中的或。

如果同一编译中的两个 lambda 表达式或方法组需要使用相同的参数类型和修饰符以及相同的返回类型和修饰符的合成委托类型，则编译器将使用同一合成委托类型。

具有自然类型的 lambda 或方法组可用作声明中的初始值设定项 `var` 。

```csharp
var f1 = () => default;        // error: no natural type
var f2 = x => { };             // error: no natural type
var f3 = x => x;               // error: no natural type
var f4 = () => 1;              // System.Func<int>
var f5 = () : string => null;  // System.Func<string>
```

```csharp
static void F1() { }
static void F1<T>(this T t) { }
static void F2(this string s) { }

var f6 = F1;    // error: multiple methods
var f7 = "".F1; // System.Action
var f8 = F2;    // System.Action<string> 
```

合成委托类型是隐式的协变和逆变。
```csharp
var fA = (IEnumerable<string> e, ref int i) => { }; // void DA$(IEnumerable<string>, ref int);
fA = (IEnumerable<object> e, ref int i) => { };     // ok

var fB = (IEnumerable<object> e, ref int i) => { }; // void DB$(IEnumerable<object>, ref int);
fB = (IEnumerable<string> e, ref int i) => { };     // error: parameter type mismatch
```

### <a name="implicit-conversion-to-systemdelegate"></a>隐式转换到 `System.Delegate`
推断自然类型的结果是： lambda 表达式和具有自然类型的方法组可隐式转换为 `System.Delegate` 。
```csharp
static void Invoke(Func<string> f) { }
static void Invoke(Delegate d) { }

static string GetString() => "";
static int GetInt() => 0;

Invoke(() => "");  // Invoke(Func<string>)
Invoke(() => 0);   // Invoke(Delegate) [new]

Invoke(GetString); // Invoke(Func<string>)
Invoke(GetInt);    // Invoke(Delegate) [new]
```

如果无法推断自然类型，则不会将隐式转换为 `System.Delegate` 。
```csharp
static void Invoke(Delegate d) { }

Invoke(Console.WriteLine); // error: cannot to 'Delegate'; multiple candidate methods
Invoke(x => x);            // error: cannot to 'Delegate'; no natural type for 'x'
```

为了避免重大更改，会更新重载决策，以优先使用强类型委托和表达式 `System.Delegate` 。
_下面的示例演示了 lambda 的打破规则。方法组是否有等效的示例？_
```csharp
static void Execute(Expression<Func<string>> e) { }
static void Execute(Delegate d) { }

static string GetString() => "";
static int GetInt() => 0;

Execute(() => "");  // Execute(Expression<Func<string>>) [tie-breaker]
Execute(() => 0);   // Execute(Delegate) [new]

Execute(GetString); // Execute(Delegate) [new]
Execute(GetInt);    // Execute(Delegate) [new]
```

## <a name="syntax"></a>语法

```antlr
lambda_expression
  : attribute_list* modifier* lambda_parameters (':' type)? '=>' (block | body)
  ;

lambda_parameters
  : lambda_parameter
  | '(' (lambda_parameter (',' lambda_parameter)*)? ')'
  ;

lambda_parameter
  : identifier
  | (attribute_list* modifier* type)? identifier equals_value_clause?
  ;
```

_`: type`返回类型语法是否引入了 `?:` 不能轻易解析的多义性？_

_如果允许参数的属性没有显式类型（如？）， `([MyAttribute] x) => { }` (我们不允许对没有显式类型的参数的修饰符，如 `(ref x) => { }` 。 )_

## <a name="design-meetings"></a>设计会议

- https://github.com/dotnet/csharplang/blob/master/meetings/2021/LDM-2021-03-03.md

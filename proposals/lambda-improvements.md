---
ms.openlocfilehash: 138d66c669e5db527ffe24d2bbb004b5fd2f6db9
ms.sourcegitcommit: 00b0f04deb7ad7b7a1f16ed8e9ec40620d87e774
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/06/2021
ms.locfileid: "102250388"
---
# <a name="lambda-improvements"></a><span data-ttu-id="4ec7f-101">Lambda 改进</span><span class="sxs-lookup"><span data-stu-id="4ec7f-101">Lambda improvements</span></span>

## <a name="summary"></a><span data-ttu-id="4ec7f-102">总结</span><span class="sxs-lookup"><span data-stu-id="4ec7f-102">Summary</span></span>
<span data-ttu-id="4ec7f-103">建议的更改：</span><span class="sxs-lookup"><span data-stu-id="4ec7f-103">Proposed changes:</span></span>
1. <span data-ttu-id="4ec7f-104">允许具有属性的 lambda</span><span class="sxs-lookup"><span data-stu-id="4ec7f-104">Allow lambdas with attributes</span></span>
2. <span data-ttu-id="4ec7f-105">允许具有显式返回类型的 lambda</span><span class="sxs-lookup"><span data-stu-id="4ec7f-105">Allow lambdas with explicit return type</span></span>
3. <span data-ttu-id="4ec7f-106">推断 lambda 和方法组的自然委托类型</span><span class="sxs-lookup"><span data-stu-id="4ec7f-106">Infer a natural delegate type for lambdas and method groups</span></span>

## <a name="motivation"></a><span data-ttu-id="4ec7f-107">动机</span><span class="sxs-lookup"><span data-stu-id="4ec7f-107">Motivation</span></span>
<span data-ttu-id="4ec7f-108">对 lambda 的属性的支持将提供方法和本地函数的奇偶校验。</span><span class="sxs-lookup"><span data-stu-id="4ec7f-108">Support for attributes on lambdas would provide parity with methods and local functions.</span></span>

<span data-ttu-id="4ec7f-109">对显式返回类型的支持将提供与 lambda 参数的对称，可在其中指定显式类型。</span><span class="sxs-lookup"><span data-stu-id="4ec7f-109">Support for explicit return types would provide symmetry with lambda parameters where explicit types can be specified.</span></span>
<span data-ttu-id="4ec7f-110">如果允许显式返回类型，则还会在嵌套的 lambda 中提供对编译器性能的控制，在这种情况下，重载决策必须绑定 lambda 体来确定该签名。</span><span class="sxs-lookup"><span data-stu-id="4ec7f-110">Allowing explicit return types would also provide control over compiler performance in nested lambdas where overload resolution must bind the lambda body currently to determine the signature.</span></span>

<span data-ttu-id="4ec7f-111">Lambda 表达式和方法组的自然类型将允许在没有显式委托类型（包括作为声明中的初始值设定项）的情况下，可以使用 lambda 和方法组。 `var`</span><span class="sxs-lookup"><span data-stu-id="4ec7f-111">A natural type for lambda expressions and method groups will allow more scenarios where lambdas and method groups may be used without an explicit delegate type, including as initializers in `var` declarations.</span></span>

<span data-ttu-id="4ec7f-112">对于 lambda 和方法组，需要显式委托类型对于客户来说是一个摩擦点，并且已成为 ASP.NET 中的最新[工作的障碍。](https://github.com/dotnet/aspnetcore/pull/29878)</span><span class="sxs-lookup"><span data-stu-id="4ec7f-112">Requiring explicit delegate types for lambdas and method groups has been a friction point for customers, and has become an impediment to progress in ASP.NET with recent work on [MapAction](https://github.com/dotnet/aspnetcore/pull/29878).</span></span>

<span data-ttu-id="4ec7f-113">[ASP.NET MapAction](https://github.com/dotnet/aspnetcore/pull/29878) 没有建议的更改 (`MapAction()` 采用 `System.Delegate` 参数) ：</span><span class="sxs-lookup"><span data-stu-id="4ec7f-113">[ASP.NET MapAction](https://github.com/dotnet/aspnetcore/pull/29878) without proposed changes (`MapAction()` takes a `System.Delegate` argument):</span></span>
```csharp
[HttpGet("/")] Todo GetTodo() => new(Id: 0, Name: "Name");
app.MapAction((Func<Todo>)GetTodo);

[HttpPost("/")] Todo PostTodo([FromBody] Todo todo) => todo);
app.MapAction((Func<Todo, Todo>)PostTodo);
```

<span data-ttu-id="4ec7f-114">用于方法组的自然类型的[ASP.NET MapAction](https://github.com/dotnet/aspnetcore/pull/29878) ：</span><span class="sxs-lookup"><span data-stu-id="4ec7f-114">[ASP.NET MapAction](https://github.com/dotnet/aspnetcore/pull/29878) with natural types for method groups:</span></span>
```csharp
[HttpGet("/")] Todo GetTodo() => new(Id: 0, Name: "Name");
app.MapAction(GetTodo);

[HttpPost("/")] Todo PostTodo([FromBody] Todo todo) => todo);
app.MapAction(PostTodo);
```

<span data-ttu-id="4ec7f-115">Lambda 表达式的属性和自然类型[ASP.NET MapAction](https://github.com/dotnet/aspnetcore/pull/29878) ：</span><span class="sxs-lookup"><span data-stu-id="4ec7f-115">[ASP.NET MapAction](https://github.com/dotnet/aspnetcore/pull/29878) with attributes and natural types for lambda expressions:</span></span>
```csharp
app.MapAction([HttpGet("/")] () => new Todo(Id: 0, Name: "Name"));
app.MapAction([HttpPost("/")] ([FromBody] Todo todo) => todo);
```

## <a name="attributes"></a><span data-ttu-id="4ec7f-116">属性</span><span class="sxs-lookup"><span data-stu-id="4ec7f-116">Attributes</span></span>
<span data-ttu-id="4ec7f-117">特性可以添加到 lambda 表达式。</span><span class="sxs-lookup"><span data-stu-id="4ec7f-117">Attributes may be added to lambda expressions.</span></span>
```csharp
f = [MyAttribute] x => x;          // [MyAttribute]lambda
f = [MyAttribute] (int x) => x;    // [MyAttribute]lambda
f = [MyAttribute] static x => x;   // [MyAttribute]lambda
f = [return: MyAttribute] () => 1; // [return: MyAttribute]lambda
```

<span data-ttu-id="4ec7f-118">_如果将属性添加到整个表达式，则参数列表应为括号？应 `[MyAttribute] x => x` 禁用 (吗？如果是这样，该怎么办 `[MyAttribute] static x => x` ？ )_</span><span class="sxs-lookup"><span data-stu-id="4ec7f-118">_Should parentheses be required for the parameter list if attributes are added to the entire expression? (Should `[MyAttribute] x => x` be disallowed? If so, what about `[MyAttribute] static x => x`?)_</span></span>

<span data-ttu-id="4ec7f-119">特性可以添加到用显式类型声明的 lambda 参数。</span><span class="sxs-lookup"><span data-stu-id="4ec7f-119">Attributes may be added to lambda parameters that are declared with explicit types.</span></span>
```csharp
f = ([MyAttribute] x) => x;      // syntax error
f = ([MyAttribute] int x) => x;  // [MyAttribute]x
```

<span data-ttu-id="4ec7f-120">用语法声明的匿名方法不支持特性 `delegate { }` 。</span><span class="sxs-lookup"><span data-stu-id="4ec7f-120">Attributes are not supported for anonymous methods declared with `delegate { }` syntax.</span></span>
```csharp
f = [MyAttribute] delegate { return 1; };         // syntax error
f = delegate ([MyAttribute] int x) { return x; }; // syntax error
```

<span data-ttu-id="4ec7f-121">Lambda 表达式或 lambda 参数上的特性将发送到映射到 lambda 的方法的元数据中。</span><span class="sxs-lookup"><span data-stu-id="4ec7f-121">Attributes on the lambda expression or lambda parameters will be emitted to metadata on the method that maps to the lambda.</span></span>

<span data-ttu-id="4ec7f-122">通常，客户不应依赖于 lambda 表达式和本地函数从源映射到元数据的方式。</span><span class="sxs-lookup"><span data-stu-id="4ec7f-122">In general, customers should not depend on how lambda expressions and local functions map from source to metadata.</span></span> <span data-ttu-id="4ec7f-123">如何发出 lambda 和本地函数可以在编译器版本之间进行更改。</span><span class="sxs-lookup"><span data-stu-id="4ec7f-123">How lambdas and local functions are emitted can, and has, changed between compiler versions.</span></span>

<span data-ttu-id="4ec7f-124">此处建议的更改以驱动的方案为目标 `Delegate` 。</span><span class="sxs-lookup"><span data-stu-id="4ec7f-124">The changes proposed here are targeted at the `Delegate` driven scenario.</span></span>
<span data-ttu-id="4ec7f-125">检查 `MethodInfo` 与实例关联的以 `Delegate` 确定 lambda 表达式或局部函数的签名（包括任何显式属性和编译器发出的其他元数据，如默认参数），这应该是有效的。</span><span class="sxs-lookup"><span data-stu-id="4ec7f-125">It should be valid to inspect the `MethodInfo` associated with a `Delegate` instance to determine the signature of the lambda expression or local function including any explicit attributes and additional metadata emitted by the compiler such as default parameters.</span></span>
<span data-ttu-id="4ec7f-126">这样，ASP.NET 的团队就可以将 lambda 和本地函数的行为与普通方法相同。</span><span class="sxs-lookup"><span data-stu-id="4ec7f-126">This allows teams such as ASP.NET to make available the same behaviors for lambdas and local functions as ordinary methods.</span></span>

## <a name="explicit-return-type"></a><span data-ttu-id="4ec7f-127">显式返回类型</span><span class="sxs-lookup"><span data-stu-id="4ec7f-127">Explicit return type</span></span>
<span data-ttu-id="4ec7f-128">可以在参数列表之后指定显式返回类型。</span><span class="sxs-lookup"><span data-stu-id="4ec7f-128">An explicit return type may be specified after the parameter list.</span></span>
```csharp
f = () : T => default;              // () : T
f = x : short => 1;                 // <unknown> : short
f = (ref int x) : ref int => ref x; // ref int : ref int
f = static _ : void => { };         // <unknown> : void
```

<span data-ttu-id="4ec7f-129">用语法声明的匿名方法不支持显式返回类型 `delegate { }` 。</span><span class="sxs-lookup"><span data-stu-id="4ec7f-129">Explicit return types are not supported for anonymous methods declared with `delegate { }` syntax.</span></span>
```csharp
f = delegate : int { return 1; };         // syntax error
f = delegate (int x) : int { return x; }; // syntax error
```

## <a name="natural-delegate-type"></a><span data-ttu-id="4ec7f-130">自然委托类型</span><span class="sxs-lookup"><span data-stu-id="4ec7f-130">Natural delegate type</span></span>
<span data-ttu-id="4ec7f-131">如果参数类型为显式，并且返回类型为显式，或者正文中所有表达式的自然类型都有通用类型，则 lambda 表达式具有自然类型 `return` 。</span><span class="sxs-lookup"><span data-stu-id="4ec7f-131">A lambda expression has a natural type if the parameters types are explicit and either the return type is explicit or there is a common type from the natural types of all `return` expressions in the body.</span></span>

<span data-ttu-id="4ec7f-132">自然类型是委托类型，其中参数类型为显式 lambda 参数类型，返回类型 `R` 为：</span><span class="sxs-lookup"><span data-stu-id="4ec7f-132">The natural type is a delegate type where the parameter types are the explicit lambda parameter types and the return type `R` is:</span></span>
- <span data-ttu-id="4ec7f-133">如果 lambda 返回类型为显式，则使用该类型;</span><span class="sxs-lookup"><span data-stu-id="4ec7f-133">if the lambda return type is explicit, that type is used;</span></span>
- <span data-ttu-id="4ec7f-134">如果 lambda 没有返回表达式，返回类型为 `void` 或 `System.Threading.Tasks.Task` if `async` ;</span><span class="sxs-lookup"><span data-stu-id="4ec7f-134">if the lambda has no return expressions, the return type is `void` or `System.Threading.Tasks.Task` if `async`;</span></span>
- <span data-ttu-id="4ec7f-135">如果正文中所有表达式的自然类型的通用类型 `return` 为类型 `R0` ，则返回类型为 `R0` 或 `System.Threading.Tasks.Task<R0>` `async` 。</span><span class="sxs-lookup"><span data-stu-id="4ec7f-135">if the common type from the natural type of all `return` expressions in the body is the type `R0`, the return type is `R0` or `System.Threading.Tasks.Task<R0>` if `async`.</span></span>

<span data-ttu-id="4ec7f-136">如果方法组包含单个方法，则该方法组具有自然类型。</span><span class="sxs-lookup"><span data-stu-id="4ec7f-136">A method group has a natural type if the method group contains a single method.</span></span>

<span data-ttu-id="4ec7f-137">方法组可以引用扩展方法。</span><span class="sxs-lookup"><span data-stu-id="4ec7f-137">A method group might refer to extension methods.</span></span> <span data-ttu-id="4ec7f-138">通常，方法组解析会以延迟方式搜索扩展方法，只循环访问连续的命名空间范围，直到找到与目标类型匹配的扩展方法。</span><span class="sxs-lookup"><span data-stu-id="4ec7f-138">Normally method group resolution searches for extension methods lazily, only iterating through successive namespace scopes until extension methods are found that match the target type.</span></span> <span data-ttu-id="4ec7f-139">但若要确定自然类型需要搜索所有命名空间范围，</span><span class="sxs-lookup"><span data-stu-id="4ec7f-139">But to determine the natural type will require searching all namespace scopes.</span></span> <span data-ttu-id="4ec7f-140">_若要最大程度地减少不必要的绑定，可能只在没有目标类型的情况下（即，只在需要时才计算自然类型）计算自然类型。_</span><span class="sxs-lookup"><span data-stu-id="4ec7f-140">_To minimize unnecessary binding, perhaps natural type should be calculated only in cases where there is no target type - that is, only calculate the natural type in cases where it is needed._</span></span>

<span data-ttu-id="4ec7f-141">Lambda 或方法组的委托类型以及参数类型 `P1, ..., Pn` 和返回类型 `R` 为：</span><span class="sxs-lookup"><span data-stu-id="4ec7f-141">The delegate type for the lambda or method group and parameter types `P1, ..., Pn` and return type `R` is:</span></span>
- <span data-ttu-id="4ec7f-142">如果任何参数或返回值不是通过值，或者有16个以上的参数，或者任何参数类型或返回值不是有效的类型参数 (则 `(int* p) => { }`) ，则该委托是一个具有与 `internal` lambda 或方法组相匹配的签名的合成匿名委托类型，并且具有参数名 `arg1, ..., argn` 或 `arg` if 一个参数;</span><span class="sxs-lookup"><span data-stu-id="4ec7f-142">if any parameter or return value is not by value, or there are more than 16 parameters, or any of the parameter types or return are not valid type arguments (say, `(int* p) => { }`), then the delegate is a synthesized `internal` anonymous delegate type with signature that matches the lambda or method group, and with parameter names `arg1, ..., argn` or `arg` if a single parameter;</span></span>
- <span data-ttu-id="4ec7f-143">如果 `R` 为 `void` ，则委托类型为 `System.Action<P1, ..., Pn>` ;</span><span class="sxs-lookup"><span data-stu-id="4ec7f-143">if `R` is `void`, then the delegate type is `System.Action<P1, ..., Pn>`;</span></span>
- <span data-ttu-id="4ec7f-144">否则，委托类型为 `System.Func<P1, ..., Pn, R>` 。</span><span class="sxs-lookup"><span data-stu-id="4ec7f-144">otherwise the delegate type is `System.Func<P1, ..., Pn, R>`.</span></span>

<span data-ttu-id="4ec7f-145">`modopt()` 在 `modreq()` 对应的委托类型中忽略方法组签名中的或。</span><span class="sxs-lookup"><span data-stu-id="4ec7f-145">`modopt()` or `modreq()` in the method group signature are ignored in the corresponding delegate type.</span></span>

<span data-ttu-id="4ec7f-146">如果同一编译中的两个 lambda 表达式或方法组需要使用相同的参数类型和修饰符以及相同的返回类型和修饰符的合成委托类型，则编译器将使用同一合成委托类型。</span><span class="sxs-lookup"><span data-stu-id="4ec7f-146">If two lambda expressions or method groups in the same compilation require synthesized delegate types with the same parameter types and modifiers and the same return type and modifiers, the compiler will use the same synthesized delegate type.</span></span>

<span data-ttu-id="4ec7f-147">具有自然类型的 lambda 或方法组可用作声明中的初始值设定项 `var` 。</span><span class="sxs-lookup"><span data-stu-id="4ec7f-147">Lambdas or method groups with natural types can be used as initializers in `var` declarations.</span></span>

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

<span data-ttu-id="4ec7f-148">合成委托类型是隐式的协变和逆变。</span><span class="sxs-lookup"><span data-stu-id="4ec7f-148">The synthesized delegate types are implicitly co- and contra-variant.</span></span>
```csharp
var fA = (IEnumerable<string> e, ref int i) => { }; // void DA$(IEnumerable<string>, ref int);
fA = (IEnumerable<object> e, ref int i) => { };     // ok

var fB = (IEnumerable<object> e, ref int i) => { }; // void DB$(IEnumerable<object>, ref int);
fB = (IEnumerable<string> e, ref int i) => { };     // error: parameter type mismatch
```

### <a name="implicit-conversion-to-systemdelegate"></a><span data-ttu-id="4ec7f-149">隐式转换到 `System.Delegate`</span><span class="sxs-lookup"><span data-stu-id="4ec7f-149">Implicit conversion to `System.Delegate`</span></span>
<span data-ttu-id="4ec7f-150">推断自然类型的结果是： lambda 表达式和具有自然类型的方法组可隐式转换为 `System.Delegate` 。</span><span class="sxs-lookup"><span data-stu-id="4ec7f-150">A consequence of inferring a natural type is that lambda expressions and method groups with natural type are implicitly convertible to `System.Delegate`.</span></span>
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

<span data-ttu-id="4ec7f-151">如果无法推断自然类型，则不会将隐式转换为 `System.Delegate` 。</span><span class="sxs-lookup"><span data-stu-id="4ec7f-151">If a natural type cannot be inferred, there is no implicit conversion to `System.Delegate`.</span></span>
```csharp
static void Invoke(Delegate d) { }

Invoke(Console.WriteLine); // error: cannot to 'Delegate'; multiple candidate methods
Invoke(x => x);            // error: cannot to 'Delegate'; no natural type for 'x'
```

<span data-ttu-id="4ec7f-152">为了避免重大更改，会更新重载决策，以优先使用强类型委托和表达式 `System.Delegate` 。</span><span class="sxs-lookup"><span data-stu-id="4ec7f-152">To avoid a breaking change, overload resolution will be updated to prefer strongly-typed delegates and expressions over `System.Delegate`.</span></span>
<span data-ttu-id="4ec7f-153">_下面的示例演示了 lambda 的打破规则。方法组是否有等效的示例？_</span><span class="sxs-lookup"><span data-stu-id="4ec7f-153">_The example below demonstrates the tie-breaking rule for lambdas. Is there an equivalent example for method groups?_</span></span>
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

## <a name="syntax"></a><span data-ttu-id="4ec7f-154">语法</span><span class="sxs-lookup"><span data-stu-id="4ec7f-154">Syntax</span></span>

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

<span data-ttu-id="4ec7f-155">_`: type`返回类型语法是否引入了 `?:` 不能轻易解析的多义性？_</span><span class="sxs-lookup"><span data-stu-id="4ec7f-155">_Does the `: type` return type syntax introduce ambiguities with `?:` that cannot be resolved easily?_</span></span>

<span data-ttu-id="4ec7f-156">_如果允许参数的属性没有显式类型（如？）， `([MyAttribute] x) => { }` (我们不允许对没有显式类型的参数的修饰符，如 `(ref x) => { }` 。 )_</span><span class="sxs-lookup"><span data-stu-id="4ec7f-156">_Should we allow attributes on parameters without explicit types, such as `([MyAttribute] x) => { }`? (We don't allow modifiers on parameters without explicit types, such as `(ref x) => { }`.)_</span></span>

## <a name="design-meetings"></a><span data-ttu-id="4ec7f-157">设计会议</span><span class="sxs-lookup"><span data-stu-id="4ec7f-157">Design meetings</span></span>

- https://github.com/dotnet/csharplang/blob/master/meetings/2021/LDM-2021-03-03.md

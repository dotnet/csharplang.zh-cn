---
ms.openlocfilehash: e0e18660cb3dbb6bfea77dd860ef7aa1388a3ca3
ms.sourcegitcommit: f22293533088b7527fa88d8916711b1d6a9ce5fd
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/05/2020
ms.locfileid: "87802414"
---
# <a name="top-level-statements"></a><span data-ttu-id="b4baf-101">顶级语句</span><span class="sxs-lookup"><span data-stu-id="b4baf-101">Top-level statements</span></span>

* <span data-ttu-id="b4baf-102">[x] 建议</span><span class="sxs-lookup"><span data-stu-id="b4baf-102">[x] Proposed</span></span>
* <span data-ttu-id="b4baf-103">[x] 原型：已启动</span><span class="sxs-lookup"><span data-stu-id="b4baf-103">[x] Prototype: Started</span></span>
* <span data-ttu-id="b4baf-104">[x] 实现：已启动</span><span class="sxs-lookup"><span data-stu-id="b4baf-104">[x] Implementation: Started</span></span>
* <span data-ttu-id="b4baf-105">[] 规范：未启动</span><span class="sxs-lookup"><span data-stu-id="b4baf-105">[ ] Specification: Not Started</span></span>

## <a name="summary"></a><span data-ttu-id="b4baf-106">总结</span><span class="sxs-lookup"><span data-stu-id="b4baf-106">Summary</span></span>
[summary]: #summary

<span data-ttu-id="b4baf-107">允许一系列*语句*直接出现在*compilation_unit* (的*namespace_member_declaration*（即源文件) ）之前。</span><span class="sxs-lookup"><span data-stu-id="b4baf-107">Allow a sequence of *statements* to occur right before the *namespace_member_declaration*s of a *compilation_unit* (i.e. source file).</span></span>

<span data-ttu-id="b4baf-108">语义是，如果存在这样一系列*语句*，则将发出下面的类型声明，取模实际类型名称和方法名称：</span><span class="sxs-lookup"><span data-stu-id="b4baf-108">The semantics are that if such a sequence of *statements* is present, the following type declaration, modulo the actual type name and the method name, would be emitted:</span></span>

``` c#
static class Program
{
    static async Task Main(string[] args)
    {
        // statements
    }
}
```

<span data-ttu-id="b4baf-109">另请参阅 https://github.com/dotnet/csharplang/issues/3117。</span><span class="sxs-lookup"><span data-stu-id="b4baf-109">See also https://github.com/dotnet/csharplang/issues/3117.</span></span>

## <a name="motivation"></a><span data-ttu-id="b4baf-110">动机</span><span class="sxs-lookup"><span data-stu-id="b4baf-110">Motivation</span></span>
[motivation]: #motivation

<span data-ttu-id="b4baf-111">由于需要使用显式方法，因此，除了最简单的程序，还有一定数量的样板 `Main` 。</span><span class="sxs-lookup"><span data-stu-id="b4baf-111">There's a certain amount of boilerplate surrounding even the simplest of programs, because of the need for an explicit `Main` method.</span></span> <span data-ttu-id="b4baf-112">这似乎会使语言学习和编程清晰。</span><span class="sxs-lookup"><span data-stu-id="b4baf-112">This seems to get in the way of language learning and program clarity.</span></span> <span data-ttu-id="b4baf-113">因此，此功能的主要目的是允许 c # 程序在不必要的情况下不必要进行解释，以方便学习和编写代码。</span><span class="sxs-lookup"><span data-stu-id="b4baf-113">The primary goal of the feature therefore is to allow C# programs without unnecessary boilerplate around them, for the sake of learners and the clarity of code.</span></span>

## <a name="detailed-design"></a><span data-ttu-id="b4baf-114">详细设计</span><span class="sxs-lookup"><span data-stu-id="b4baf-114">Detailed design</span></span>
[design]: #detailed-design

### <a name="syntax"></a><span data-ttu-id="b4baf-115">语法</span><span class="sxs-lookup"><span data-stu-id="b4baf-115">Syntax</span></span>

<span data-ttu-id="b4baf-116">唯一的附加语法允许编译单元中的一系列*语句*，刚好在*namespace_member_declaration*之前：</span><span class="sxs-lookup"><span data-stu-id="b4baf-116">The only additional syntax is allowing a sequence of *statement*s in a compilation unit, just before the *namespace_member_declaration*s:</span></span>

``` antlr
compilation_unit
    : extern_alias_directive* using_directive* global_attributes? statement* namespace_member_declaration*
    ;
```

<span data-ttu-id="b4baf-117">只允许一个*compilation_unit*具有*语句*。</span><span class="sxs-lookup"><span data-stu-id="b4baf-117">Only one *compilation_unit* is allowed to have *statement*s.</span></span> 

<span data-ttu-id="b4baf-118">示例：</span><span class="sxs-lookup"><span data-stu-id="b4baf-118">Example:</span></span>

``` c#
if (args.Length == 0
    || !int.TryParse(args[0], out int n)
    || n < 0) return;
Console.WriteLine(Fib(n).curr);

(int curr, int prev) Fib(int i)
{
    if (i == 0) return (1, 0);
    var (curr, prev) = Fib(i - 1);
    return (curr + prev, curr);
}
```

### <a name="semantics"></a><span data-ttu-id="b4baf-119">语义</span><span class="sxs-lookup"><span data-stu-id="b4baf-119">Semantics</span></span>

<span data-ttu-id="b4baf-120">如果任何顶级语句出现在程序的任何编译单元中，就像它们合并到 `Main` `Program` 全局命名空间中类的方法的块体中一样，如下所示：</span><span class="sxs-lookup"><span data-stu-id="b4baf-120">If any top-level statements are present in any compilation unit of the program, the meaning is as if they were combined in the block body of a `Main` method of a `Program` class in the global namespace, as follows:</span></span>

``` c#
static class Program
{
    static async Task Main(string[] args)
    {
        // statements
    }
}
```

<span data-ttu-id="b4baf-121">请注意，名称 "Program" 和 "Main" 仅用于说明，编译器使用的实际名称是依赖实现的，并且类型和方法都不能从源代码中按名称引用。</span><span class="sxs-lookup"><span data-stu-id="b4baf-121">Note that the names "Program" and "Main" are used only for illustrations purposes, actual names used by compiler are implementation dependent and neither the type, nor the method can be referenced by name from source code.</span></span>

<span data-ttu-id="b4baf-122">方法被指定为程序的入口点。</span><span class="sxs-lookup"><span data-stu-id="b4baf-122">The method is designated as the entry point of the program.</span></span> <span data-ttu-id="b4baf-123">按照约定可将显式声明的方法视为忽略入口点候选项。</span><span class="sxs-lookup"><span data-stu-id="b4baf-123">Explicitly declared methods that by convention could be considered as an entry point candidates are ignored.</span></span> <span data-ttu-id="b4baf-124">出现这种情况时，会报告警告。</span><span class="sxs-lookup"><span data-stu-id="b4baf-124">A warning is reported when that happens.</span></span> <span data-ttu-id="b4baf-125">`-main:<type>`如果存在顶级语句，则指定编译器开关是错误的。</span><span class="sxs-lookup"><span data-stu-id="b4baf-125">It is an error to specify `-main:<type>` compiler switch when there are top-level statements.</span></span>

<span data-ttu-id="b4baf-126">入口点方法始终有一个形参 ```string[] args``` 。</span><span class="sxs-lookup"><span data-stu-id="b4baf-126">The entry point method always has one formal parameter, ```string[] args```.</span></span> <span data-ttu-id="b4baf-127">执行环境创建并传递一个参数，该 ```string[]``` 参数包含在启动应用程序时指定的命令行参数。</span><span class="sxs-lookup"><span data-stu-id="b4baf-127">The execution environment creates and passes a ```string[]``` argument containing the command-line arguments that were specified when the application was started.</span></span> <span data-ttu-id="b4baf-128">```string[]```参数决不会为 null，但如果未指定命令行参数，则其长度可能为零。</span><span class="sxs-lookup"><span data-stu-id="b4baf-128">The ```string[]``` argument is never null, but it may have a length of zero if no command-line arguments were specified.</span></span> <span data-ttu-id="b4baf-129">"Args" 参数在顶级语句的范围内，不在它们的作用域内。</span><span class="sxs-lookup"><span data-stu-id="b4baf-129">The ‘args’ parameter is in scope  within top-level statements and is not in scope outside of them.</span></span> <span data-ttu-id="b4baf-130">常规名称冲突/隐藏规则适用。</span><span class="sxs-lookup"><span data-stu-id="b4baf-130">Regular name conflict/shadowing rules apply.</span></span>

<span data-ttu-id="b4baf-131">在顶级语句中允许使用异步操作，使其能够在常规异步入口点方法中的语句中使用。</span><span class="sxs-lookup"><span data-stu-id="b4baf-131">Async operations are allowed in top-level statements to the degree they are allowed in statements within a regular async entry point method.</span></span> <span data-ttu-id="b4baf-132">但是，它们不是必需的，如果 `await` 省略了表达式和其他异步操作，则不会生成任何警告。</span><span class="sxs-lookup"><span data-stu-id="b4baf-132">However, they are not required, if `await` expressions and other async operations are omitted, no warning is produced.</span></span>

<span data-ttu-id="b4baf-133">生成的入口点方法的签名根据顶级语句使用的操作来确定，如下所示：</span><span class="sxs-lookup"><span data-stu-id="b4baf-133">The signature of the generated entry point method is determined based on operations used by the top level statements as follows:</span></span>
| <span data-ttu-id="b4baf-134">**Async-operations\Return-with-expression**</span><span class="sxs-lookup"><span data-stu-id="b4baf-134">**Async-operations\Return-with-expression**</span></span> | <span data-ttu-id="b4baf-135">**现值**</span><span class="sxs-lookup"><span data-stu-id="b4baf-135">**Present**</span></span> | <span data-ttu-id="b4baf-136">**不存在**</span><span class="sxs-lookup"><span data-stu-id="b4baf-136">**Absent**</span></span> |
|----------------------------------------|-------------|-------------|
| <span data-ttu-id="b4baf-137">**现值**</span><span class="sxs-lookup"><span data-stu-id="b4baf-137">**Present**</span></span> | ```static Task<int> Main(string[] args)```| ```static Task Main(string[] args)``` |
| <span data-ttu-id="b4baf-138">**不存在**</span><span class="sxs-lookup"><span data-stu-id="b4baf-138">**Absent**</span></span>  | ```static int Main(string[] args)``` | ```static void Main(string[] args)``` |

<span data-ttu-id="b4baf-139">以上示例将生成以下 `$Main` 方法声明：</span><span class="sxs-lookup"><span data-stu-id="b4baf-139">The example above would yield the following `$Main` method declaration:</span></span>

``` c#
static class $Program
{
    static void $Main(string[] args)
    {
        if (args.Length == 0
            || !int.TryParse(args[0], out int n)
            || n < 0) return;
        Console.WriteLine(Fib(n).curr);
        
        (int curr, int prev) Fib(int i)
        {
            if (i == 0) return (1, 0);
            var (curr, prev) = Fib(i - 1);
            return (curr + prev, curr);
        }
    }
}
```

<span data-ttu-id="b4baf-140">同时，示例如下所示：</span><span class="sxs-lookup"><span data-stu-id="b4baf-140">At the same time an example like this:</span></span>
``` c#
await System.Threading.Tasks.Task.Delay(1000);
System.Console.WriteLine("Hi!");
```

<span data-ttu-id="b4baf-141">将产生：</span><span class="sxs-lookup"><span data-stu-id="b4baf-141">would  yield:</span></span>
``` c#
static class $Program
{
    static async Task $Main(string[] args)
    {
        await System.Threading.Tasks.Task.Delay(1000);
        System.Console.WriteLine("Hi!");
    }
}
```

<span data-ttu-id="b4baf-142">例如：</span><span class="sxs-lookup"><span data-stu-id="b4baf-142">An example like this:</span></span>
``` c#
await System.Threading.Tasks.Task.Delay(1000);
System.Console.WriteLine("Hi!");
return 0;
```

<span data-ttu-id="b4baf-143">将产生：</span><span class="sxs-lookup"><span data-stu-id="b4baf-143">would  yield:</span></span>
``` c#
static class $Program
{
    static async Task<int> $Main(string[] args)
    {
        await System.Threading.Tasks.Task.Delay(1000);
        System.Console.WriteLine("Hi!");
        return 0;
    }
}
```

<span data-ttu-id="b4baf-144">下面是一个示例：</span><span class="sxs-lookup"><span data-stu-id="b4baf-144">And an example like this:</span></span>
``` c#
System.Console.WriteLine("Hi!");
return 2;
```

<span data-ttu-id="b4baf-145">将产生：</span><span class="sxs-lookup"><span data-stu-id="b4baf-145">would  yield:</span></span>
``` c#
static class $Program
{
    static int $Main(string[] args)
    {
        System.Console.WriteLine("Hi!");
        return 2;
    }
}
```

### <a name="scope-of-top-level-local-variables-and-local-functions"></a><span data-ttu-id="b4baf-146">顶层本地变量和本地函数的作用域</span><span class="sxs-lookup"><span data-stu-id="b4baf-146">Scope of top-level local variables and local functions</span></span>

<span data-ttu-id="b4baf-147">即使顶级局部变量和函数被 "包装" 到生成的入口点方法中，它们仍应在每个编译单元中的整个程序范围内。</span><span class="sxs-lookup"><span data-stu-id="b4baf-147">Even though top-level local variables and functions are "wrapped" into the generated entry point method, they should still be in scope throughout the program in every compilation unit.</span></span>
<span data-ttu-id="b4baf-148">出于简单名称计算的目的，一旦达到全局命名空间即可：</span><span class="sxs-lookup"><span data-stu-id="b4baf-148">For the purpose of simple-name evaluation, once the global namespace is reached:</span></span>
- <span data-ttu-id="b4baf-149">首先，尝试在生成的入口点方法中计算名称，并且仅在此尝试失败时进行计算</span><span class="sxs-lookup"><span data-stu-id="b4baf-149">First, an attempt is made to evaluate the name within the generated entry point method and only if this attempt fails</span></span> 
- <span data-ttu-id="b4baf-150">全局命名空间声明中的 "常规" 计算是执行的。</span><span class="sxs-lookup"><span data-stu-id="b4baf-150">The "regular" evaluation within the global namespace declaration is performed.</span></span> 

<span data-ttu-id="b4baf-151">这可能导致在全局命名空间中声明的命名空间和类型进行名称隐藏，以及对导入的名称进行隐藏。</span><span class="sxs-lookup"><span data-stu-id="b4baf-151">This could lead to name shadowing of namespaces and types declared within the global namespace as well as to shadowing of imported names.</span></span>

<span data-ttu-id="b4baf-152">如果简单名称计算在顶级语句之外进行，并且计算产生顶级局部变量或函数，则会导致错误。</span><span class="sxs-lookup"><span data-stu-id="b4baf-152">If the simple name evaluation occurs outside of the top-level statements and the evaluation yields a top-level local variable or function, that should lead to an error.</span></span>

<span data-ttu-id="b4baf-153">通过这种方式，我们可以保护我们的未来功能，从而更好地解决中的 "顶级功能" (情况 2 https://github.com/dotnet/csharplang/issues/3117) ，并能够向错误地认为受支持的用户提供有用的诊断。</span><span class="sxs-lookup"><span data-stu-id="b4baf-153">In this way we protect our future ability to better address "Top-level functions" (scenario 2 in https://github.com/dotnet/csharplang/issues/3117), and are able to give useful diagnostics to users who mistakenly believe them to be supported.</span></span>


---
ms.openlocfilehash: f4304760253d26cff7adec14ed27a8ec7ca91757
ms.sourcegitcommit: cc02cda55fca4ebd201e242c9f04498abcb7fb10
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/19/2020
ms.locfileid: "88602648"
---
# <a name="top-level-statements"></a>顶级语句

* [x] 建议
* [x] 原型：已启动
* [x] 实现：已启动
* [] 规范：未启动

## <a name="summary"></a>总结
[summary]: #summary

允许一系列*语句*直接出现在*compilation_unit* (的*namespace_member_declaration*（即源文件) ）之前。

语义是，如果存在这样一系列 *语句* ，则将发出下面的类型声明，取模实际类型名称和方法名称：

``` c#
static class Program
{
    static async Task Main(string[] args)
    {
        // statements
    }
}
```

另请参阅 https://github.com/dotnet/csharplang/issues/3117。

## <a name="motivation"></a>动机
[motivation]: #motivation

由于需要使用显式方法，因此，除了最简单的程序，还有一定数量的样板 `Main` 。 这似乎会使语言学习和编程清晰。 因此，此功能的主要目的是允许 c # 程序在不必要的情况下不必要进行解释，以方便学习和编写代码。

## <a name="detailed-design"></a>详细设计
[design]: #detailed-design

### <a name="syntax"></a>语法

唯一的附加语法允许编译单元中的一系列 *语句*，刚好在 *namespace_member_declaration*之前：

``` antlr
compilation_unit
    : extern_alias_directive* using_directive* global_attributes? statement* namespace_member_declaration*
    ;
```

只允许一个 *compilation_unit* 具有 *语句*。 

示例：

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

### <a name="semantics"></a>语义

如果任何顶级语句出现在程序的任何编译单元中，就像它们合并到 `Main` `Program` 全局命名空间中类的方法的块体中一样，如下所示：

``` c#
static class Program
{
    static async Task Main(string[] args)
    {
        // statements
    }
}
```

请注意，名称 "Program" 和 "Main" 仅用于说明，编译器使用的实际名称是依赖实现的，并且类型和方法都不能从源代码中按名称引用。

方法被指定为程序的入口点。 按照约定可将显式声明的方法视为忽略入口点候选项。 出现这种情况时，会报告警告。 `-main:<type>`如果存在顶级语句，则指定编译器开关是错误的。

入口点方法始终有一个形参 ```string[] args``` 。 执行环境创建并传递一个参数，该 ```string[]``` 参数包含在启动应用程序时指定的命令行参数。 ```string[]```参数决不会为 null，但如果未指定命令行参数，则其长度可能为零。 "Args" 参数在顶级语句的范围内，不在它们的作用域内。 常规名称冲突/隐藏规则适用。

在顶级语句中允许使用异步操作，使其能够在常规异步入口点方法中的语句中使用。 但是，它们不是必需的，如果 `await` 省略了表达式和其他异步操作，则不会生成任何警告。

生成的入口点方法的签名根据顶级语句使用的操作来确定，如下所示：

| **Async-operations\Return-with-expression** | **现值** | **不存在** |
|----------------------------------------|-------------|-------------|
| **现值** | ```static Task<int> Main(string[] args)```| ```static Task Main(string[] args)``` |
| **不存在**  | ```static int Main(string[] args)``` | ```static void Main(string[] args)``` |

以上示例将生成以下 `$Main` 方法声明：

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

同时，示例如下所示：
``` c#
await System.Threading.Tasks.Task.Delay(1000);
System.Console.WriteLine("Hi!");
```

将产生：
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

例如：
``` c#
await System.Threading.Tasks.Task.Delay(1000);
System.Console.WriteLine("Hi!");
return 0;
```

将产生：
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

下面是一个示例：
``` c#
System.Console.WriteLine("Hi!");
return 2;
```

将产生：
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

### <a name="scope-of-top-level-local-variables-and-local-functions"></a>顶层本地变量和本地函数的作用域

即使顶级局部变量和函数被 "包装" 到生成的入口点方法中，它们仍应在每个编译单元中的整个程序范围内。
出于简单名称计算的目的，一旦达到全局命名空间即可：
- 首先，尝试在生成的入口点方法中计算名称，并且仅在此尝试失败时进行计算 
- 全局命名空间声明中的 "常规" 计算是执行的。 

这可能导致在全局命名空间中声明的命名空间和类型进行名称隐藏，以及对导入的名称进行隐藏。

如果简单名称计算在顶级语句之外进行，并且计算产生顶级局部变量或函数，则会导致错误。

通过这种方式，我们可以保护我们的未来功能，从而更好地解决中的 "顶级功能" (情况 2 https://github.com/dotnet/csharplang/issues/3117) ，并能够向错误地认为受支持的用户提供有用的诊断。


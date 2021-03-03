---
ms.openlocfilehash: fe9955c66ec231a85bc2058f044e8921fc2fbbc0
ms.sourcegitcommit: 3f8f57e294e2952af19a5231e6b9c7ff85d0ed56
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/02/2021
ms.locfileid: "101641742"
---
# <a name="introduction"></a>简介

C#（读作“See Sharp”）是一种简单易用的新式编程语言，不仅面向对象，还类型安全。 C # 在 C 语言系列中具有其根，并将对 C、c + + 和 Java 程序员立即熟悉。 C # 由 ECMA 国际标准化为 ***ecma-334** _ 标准，并通过 ISO/iec 作为 _ *_iso/iec 23270_** standard 进行标准化。 Microsoft 针对 .NET Framework 的 c # 编译器是这两个标准的一致性实现。

C# 是一种面向对象的语言。不仅如此，C# 还进一步支持 ***面向组件的*** 编程。 当代软件设计越来越依赖采用自描述的独立功能包形式的软件组件。 此类组件的关键特征包括：为编程模型提供属性、方法和事件；包含提供组件声明性信息的特性；包含自己的文档。 C # 提供了语言构造，以直接支持这些概念，使 c # 成为一种非常自然的语言，用于创建和使用软件组件。

多个 c # 功能有助于构建强大的持久应用程序： ***垃圾回收** _ 会自动回收未使用的对象占用的内存; _*_异常处理_*_ 提供了一种结构化的可扩展方法用于错误检测和恢复;以及语言的 _ *_类型安全_** 设计使无法从未初始化的变量中进行读取，将数组索引到其边界之外，或执行未检查的类型转换。

C# 采用 ***统一的类型系统***。 所有 C# 类型（包括 `int` 和 `double` 等基元类型）均继承自一个根 `object` 类型。 因此，所有类型共用一组通用运算，任何类型的值都可以一致地进行存储、传输和处理。 此外，C# 还支持用户定义的引用类型和值类型，从而支持对象动态分配以及轻量级结构的内嵌式存储。

为了确保 C# 程序和库能够持续兼容，C# 设计非常注重 ***版本控制***。 许多编程语言很少关注这个问题，因此，当引入新版依赖库时，用这些语言编写的程序会出现更多不必要的中断现象。 C# 设计中受版本控制加强直接影响的方面包括：单独的 `virtual` 和 `override` 修饰符，关于方法重载决策的规则，以及对显式接口成员声明的支持。

本章的其余部分介绍了 c # 语言的重要功能。 尽管后面的章节介绍了以详细的方式（有时也是数学方式）的规则和例外，但本章采用的是以完整性为代价来提高清晰度和简洁性。 目的是为读者提供一篇有助于编写早期程序和阅读后续章节的语言。

## <a name="hello-world"></a>Hello world

“Hello, World”程序历来都用于介绍编程语言。 下面展示了此程序的 C# 代码：

```csharp
using System;

class Hello
{
    static void Main() {
        Console.WriteLine("Hello, World");
    }
}
```

C# 源文件的文件扩展名通常为 `.cs`。 假设 "Hello，World" 程序存储在文件中 `hello.cs` ，则可以使用命令行通过 Microsoft c # 编译器编译该程序
```console
csc hello.cs
```
这会生成一个名为的可执行程序集 `hello.exe` 。 此应用程序在运行时生成的输出为
```console
Hello, World
```

“Hello, World”程序始于引用 `System` 命名空间的 `using` 指令。 命名空间提供了一种用于组织 C# 程序和库的分层方法。 命名空间包含类型和其他命名空间。例如，`System` 命名空间包含许多类型（如程序中引用的 `Console` 类）和其他许多命名空间（如 `IO` 和 `Collections`）。 借助引用给定命名空间的 `using` 指令，可以非限定的方式使用作为相应命名空间成员的类型。 由于使用 `using` 指令，因此程序可以使用 `Console.WriteLine` 作为 `System.Console.WriteLine` 的简写。

“Hello, World”程序声明的 `Hello` 类只有一个成员，即 `Main` 方法。 `Main` 方法使用 `static` 修饰符进行声明。 实例方法可以使用关键字 `this` 引用特定的封闭对象实例，而静态方法则可以在不引用特定对象的情况下运行。 按照约定，`Main` 静态方法是程序的入口点。

程序的输出是由 `System` 命名空间中 `Console` 类的 `WriteLine` 方法生成。 此类由 .NET Framework 类库提供，默认情况下，由 Microsoft c # 编译器自动引用。 请注意，c # 本身没有单独的运行时库。 相反，.NET Framework 是 c # 的运行时库。

## <a name="program-structure"></a>程序结构

C # 中的关键组织概念是 *** 程序** _、 _*_命名空间_*_、 _*_类型_*_、 _*_成员_*_ 和 _*_程序集_*_。 C# 程序由一个或多个源文件组成。 程序声明类型，而类型则包含成员，并被整理到命名空间中。 类型示例包括类和接口。 成员示例包括字段、方法、属性和事件。 编译完的 C# 程序实际上会打包到程序集中。 程序集通常具有文件扩展名 `.exe` 或 `.dll` ，具体取决于它们是否实现 _*_应用程序_*_ 或 _ *_库_* *。

示例

```csharp
using System;

namespace Acme.Collections
{
    public class Stack
    {
        Entry top;

        public void Push(object data) {
            top = new Entry(top, data);
        }

        public object Pop() {
            if (top == null) throw new InvalidOperationException();
            object result = top.data;
            top = top.next;
            return result;
        }

        class Entry
        {
            public Entry next;
            public object data;
    
            public Entry(Entry next, object data) {
                this.next = next;
                this.data = data;
            }
        }
    }
}
```
在名为的 `Stack` 命名空间中声明一个名为的类 `Acme.Collections` 。 此类的完全限定的名称为 `Acme.Collections.Stack`。 此类包含多个成员：一个 `top` 字段、两个方法（`Push` 和 `Pop`）和一个 `Entry` 嵌套类。 `Entry` 类还包含三个成员：一个 `next` 字段、一个 `data` 字段和一个构造函数。 假定示例的源代码存储在 `acme.cs` 文件中，以下命令行

```console
csc /t:library acme.cs
```
将示例编译成库（不含 `Main` 入口点的代码），并生成 `acme.dll` 程序集。

程序集包含以 ***中间语言** _ 为形式的可执行代码 (IL) 指令和格式为 _ *_metadata_* * 的符号信息。 执行前，程序集中的 IL 代码会被 .NET 公共语言运行时的实时 (JIT) 编译器自动转换成处理器专属代码。

由于程序集是包含代码和元数据的自描述功能单元，因此无需在 C# 中使用 `#include` 指令和头文件。 只需在编译程序时引用特定的程序集，即可在 C# 程序中使用此程序集中包含的公共类型和成员。 例如，此程序使用 `acme.dll` 程序集中的 `Acme.Collections.Stack` 类：

```csharp
using System;
using Acme.Collections;

class Test
{
    static void Main() {
        Stack s = new Stack();
        s.Push(1);
        s.Push(10);
        s.Push(100);
        Console.WriteLine(s.Pop());
        Console.WriteLine(s.Pop());
        Console.WriteLine(s.Pop());
    }
}
```
如果程序存储在文件中 `test.cs` ， `test.cs` 则编译时， `acme.dll` 可以使用编译器的选项来引用程序集 `/r` ：

```console
csc /r:acme.dll test.cs
```
这会创建 `test.exe` 可执行程序集，它将在运行时输出以下内容：

```console
100
10
1
```
使用 C#，可以将程序的源文本存储在多个源文件中。 编译多文件 C# 程序时，可以将所有源文件一起处理，并且源文件可以随意相互引用。从概念上讲，就像是所有源文件在处理前被集中到一个大文件中一样。 在 C# 中，永远都不需要使用前向声明，因为声明顺序无关紧要（除了极少数的例外情况）。 C# 并不限制源文件只能声明一种公共类型，也不要求源文件的文件名必须与其中声明的类型相匹配。

## <a name="types-and-variables"></a>类型和变量

C # 中有两种类型： ***值类型** _ 和 _ *_引用类型_* *。 值类型的变量直接包含数据，而引用类型的变量则存储对数据（称为“对象”）的引用。 对于引用类型，两个变量可以引用同一对象；因此，对一个变量执行的运算可能会影响另一个变量引用的对象。 借助值类型，每个变量都有自己的数据副本；因此，对一个变量执行的运算不会影响另一个变量（`ref` 和 `out` 参数变量除外）。

C # 的值类型进一步划分为 ***简单类型** _、 _*_枚举类型_*_、 _*_结构类型_*_ 和 _*_可以为 null 的类型_*_，并且 c # 的引用类型进一步分为 _*_类类型_*_、 _*_接口类型_*_、 _*_数组类型_*_ 和 _ *_委托类型_* *。

下表概述了 c # 的类型系统。

| __类别__    |                 | __说明__ |
|-----------------|-----------------|-----------------|
| 值类型     | 简单类型    | 有符号的整型：`sbyte`、`short`、`int`、`long` |
|                 |                 | 无符号的整型：`byte`、`ushort`、`uint`、`ulong` |
|                 |                 | Unicode 字符：`char` |
|                 |                 | IEEE 浮点：`float`、`double` |
|                 |                 | 高精度小数：`decimal` |
|                 |                 | 布尔：`bool` |
|                 | 枚举类型      | 格式为 `enum E {...}` 的用户定义类型 |
|                 | 结构类型    | 格式为 `struct S {...}` 的用户定义类型 |
|                 | 可为 null 的类型  | 值为 `null` 的其他所有值类型的扩展 |
| 引用类型 | 课程类型     | 其他所有类型的最终基类：`object` |
|                 |                 | Unicode 字符串：`string` |
|                 |                 | 格式为 `class C {...}` 的用户定义类型 |
|                 | 接口类型 | 格式为 `interface I {...}` 的用户定义类型 |
|                 | 数组类型     | 一维和多维，例如 `int[]` 和 `int[,]` |
|                 | 委托类型  | 格式为的用户定义的类型，例如 `delegate int  D(...)` |

八个整型类型支持带符号或不带符号格式的 8 位、16 位、32 位和 64 位值。

这两个浮点类型（ `float` 和 `double` ）使用32位单精度和64位双精度 IEEE 754 格式表示。

`decimal` 类型是适用于财务和货币计算的 128 位数据类型。

C # 的 `bool` 类型用于表示布尔值（或的值） `true` `false` 。

C# 使用 Unicode 编码处理字符和字符串。 `char` 类型表示 UTF-16 代码单元，`string` 类型表示一系列 UTF-16 代码单元。

下表总结了 c # 的数值类型。


| __类别__      | __Bits__ | 类型  | __范围/精度__ |
|-------------------|----------|-----------|---------------------|
| 有符号整型   | 8        | `sbyte`   | -128 ... 127 |
|                   | 16       | `short`   | -32768 ... 32、767 |
|                   | 32       | `int`     | -2147483648 ... 2，147，483，647 |
|                   | 64       | `long`    | -9223372036854775808 ... 9，223，372，036，854，775，807 |
| 无符号的整型 | 8        | `byte`    | 0 ... 255 |
|                   | 16       | `ushort`  | 0 ... 65，535 |
|                   | 32       | `uint`    | 0 ... 4、294次、967、 |
|                   | 64       | `ulong`   | 0 ... 18、446、744、073、为709、551、615 |
| 浮点    | 32       | `float`   | 1.5 × 10 ^ −45到3.4 × 10 ^ 38，7位精度 |
|                   | 64       | `double`  | 5.0 × 10 ^ −324到1.7 × 10 ^ 308，15位精度 |
| 小数           | 128      | `decimal` | 1.0 × 10 ^ −28到7.9 × 10 ^ 28，28位精度 |

C# 程序使用 ***类型声明*** 创建新类型。 类型声明指定新类型的名称和成员。 用户可定义以下五种 C# 类型：类类型、结构类型、接口类型、枚举类型和委托类型。

类类型定义包含数据成员的数据结构， (字段) 和函数成员 (方法、属性和其他) 。 类类型支持单一继承和多形性，即派生类可以扩展和专门针对基类的机制。

结构类型类似于类类型，因为它表示包含数据成员和函数成员的结构。 但是，与类不同的是，结构是值类型，不需要进行堆分配。 结构类型不支持用户指定的继承，并且所有结构类型均隐式继承自类型 `object`。

接口类型将协定定义为公共函数成员的命名集。 实现接口的类或结构必须提供接口的函数成员的实现。 接口可以从多个基接口继承，类或结构可以实现多个接口。

委托类型表示对具有特定参数列表和返回类型的方法的引用。 通过委托，可以将方法视为可分配给变量并可作为参数传递的实体。 委托类似于其他一些语言中的函数指针概念，但与函数指针不同的是，委托不仅面向对象，还类型安全。

类、结构、接口和委托类型都支持泛型，因此可以用其他类型参数化。

枚举类型是具有已命名常数的不同类型。 每个枚举类型都有一个基础类型，该类型必须是八个整型类型之一。 枚举类型的值集与基础类型的值集相同。

C# 支持任意类型的一维和多维数组。 与上述类型不同，数组类型无需先声明即可使用。 相反，数组类型是通过在类型名称后面添加方括号构造而成。 例如，是的一维数组，是的二维数组，是的一维数组， `int[]` `int` `int[,]` `int` `int[][]` 它是 `int` 的一维数组。

可以为 null 的类型也无需声明即可使用。 对于每个不可以为 null 的值类型 `T` ，都有一个对应的可以为 null 的类型 `T?` ，该类型可以保存附加值 `null` 。 例如， `int?` 是一个可以容纳任何32位整数或值的类型 `null` 。

C # 的类型系统是统一的，因此任何类型的值都可以被视为对象。 每种 C# 类型都直接或间接地派生自 `object` 类类型，而 `object` 是所有类型的最终基类。 只需将值视为类型 `object`，即可将引用类型的值视为对象。 值类型的值通过执行 ***装箱** _ 和 _ *_取消装箱_** 操作被视为对象。 在以下示例中，`int` 值被转换成 `object`，然后又恢复成 `int`。

```csharp
using System;

class Test
{
    static void Main() {
        int i = 123;
        object o = i;          // Boxing
        int j = (int)o;        // Unboxing
    }
}
```
当值类型的值转换为类型时 `object` ，将分配一个对象实例（也称为 "box"）来保存值，并将该值复制到该框中。 相反，将 `object` 引用强制转换为值类型时，会进行检查以确定所引用的对象是否为正确的值类型的框，如果检查成功，则会将框中的值复制出来。

C # 的统一类型系统实际上意味着值类型可以 "按需" 变为对象。 鉴于这种统一性，使用类型 `object` 的常规用途库可以与引用类型和值类型结合使用。

C# 有多种 ***变量***，其中包括字段、数组元素、局部变量和参数。 变量表示存储位置，每个变量都有一种类型，用于确定可以在变量中存储的值，如下表所示。


| __变量类型__    | __可能的内容__ |
|-------------------------|-----------------------|
| 不可以为 null 的值类型 | 具有精确类型的值 |
| 可以为 null 的值类型     | 空值或该精确类型的值 |
| `object`                | 空引用、对任何引用类型的对象的引用或对任何值类型的装箱值的引用 |
| 类类型              | 空引用、对该类类型的实例的引用，或对派生自该类类型的类的实例的引用 |
| 接口类型          | 空引用、对实现接口类型的类类型的实例的引用，或对实现接口类型的值类型的装箱值的引用 |
| 数组类型              | 空引用、对该数组类型的实例的引用，或对兼容的数组类型实例的引用 |
| 委托类型           | 空引用或对该委托类型的实例的引用 |

## <a name="expressions"></a>表达式

***表达式** _ 是从 _*_操作数_*_ 和 _*_运算符_*_ 构造的。 表达式的运算符指明了向操作数应用的运算。 运算符的示例包括 `+`、`-`、`_`、`/` 和 `new`。 操作数的示例包括文本、字段、局部变量和表达式。

如果表达式包含多个运算符，则运算符的 ***优先级** _ 控制各个运算符的计算顺序。 例如，表达式 `x + y _ z` 相当于计算 `x + (y * z)`，因为 `*` 运算符的优先级高于 `+` 运算符。

大部分运算符可 ***重载***。 借助运算符重载，可以为一个或两个操作数为用户定义类或结构类型的运算指定用户定义运算符实现代码。

下表总结了 c # 的运算符，并按优先级从高到低的顺序列出了运算符类别。 同一类别的运算符具有相等的优先级。


| __类别__                     | __表达式__    | __说明__ |
|----------------------------------|-------------------|-----------------|
| 主要                          | `x.m`             | 成员访问 |
|                                  | `x(...)`          | 方法和委托调用 |
|                                  | `x[...]`          | 数组和索引器访问 |
|                                  | `x++`             | 后递增 |
|                                  | `x--`             | 后递减 |
|                                  | `new T(...)`      | 对象和委托创建 |
|                                  | `new T(...){...}` | 使用初始值设定项创建对象 |
|                                  | `new {...}`       | 匿名对象初始值设定项 |
|                                  | `new T[...]`      | 数组创建 |
|                                  | `typeof(T)`       | 获取 `T` 的 `System.Type` 对象 |
|                                  | `checked(x)`      | 在已检查的上下文中计算表达式 |
|                                  | `unchecked(x)`    | 在未检查的上下文中计算表达式 |
|                                  | `default(T)`      | 获取类型为 `T` 的默认值 |
|                                  | `delegate {...}`  | 匿名函数（匿名方法） |
| 一元                            | `+x`              | 标识 |
|                                  | `-x`              | 否定 |
|                                  | `!x`              | 逻辑非 |
|                                  | `~x`              | 按位求反 |
|                                  | `++x`             | 前递增 |
|                                  | `--x`             | 前递减 |
|                                  | `(T)x`            | 将 `x` 显式转换为类型 `T` |
|                                  | `await x`         | 异步等待 `x` 完成 |
| 乘法性的                   | `x * y`           | 乘法 |
|                                  | `x / y`           | 除法 |
|                                  | `x % y`           | 余数 |
| 累加性                         | `x + y`           | 相加、字符串串联、委托组合 |
|                                  | `x - y`           | 相减、委托移除 |
| 移位                            | `x << y`          | 左移 |
|                                  | `x >> y`          | 右移 |
| 关系和类型测试      | `x < y`           | 小于 |
|                                  | `x > y`           | 大于 |
|                                  | `x <= y`          | 小于或等于 |
|                                  | `x >= y`          | 大于或等于 |
|                                  | `x is T`          | 如果 `x` 是 `T`，则返回 `true`；否则，返回 `false` |
|                                  | `x as T`          | 返回类型为 `T` 的 `x`；如果 `x` 的类型不是 `T`，则返回 `null` |
| 等式                         | `x == y`          | 等于      |
|                                  | `x != y`          | 不等于 |
| 逻辑与                      | `x & y`           | 整型按位 AND，布尔型逻辑 AND |
| 逻辑 XOR                      | `x ^ y`           | 整型按位 XOR，布尔型逻辑 XOR |
| 逻辑或                       | <code>x &#124; y</code> | 整型按位“或”，布尔型逻辑“或” |
| 条件“与”                  | `x && y`          | `y`仅当 `x` 为时才计算`true` |
| 条件“或”                   | <code>x &#124;&#124; y</code> | `y`仅当 `x` 为时才计算`false` |
| null 合并                  | `x ?? y`          | 如果为，则计算结果为 `y` `x` `null` ， `x` 否则为 |
| 条件逻辑                      | `x ? y : z`       | 如果 `x` 为 `true`，则计算 `y`；如果 `x` 为 `false`，则计算 `z` |
| 赋值或匿名函数 | `x = y`           | 分配 |
|                                  | `x op= y`         | 复合赋值;支持的运算符 `*=` `/=` `%=` `+=` `-=` 为 `<<=` `>>=` `&=` `^=`<code>&#124;=</code> |
|                                  | `(T x) => y`      | 匿名函数（lambda 表达式） |

## <a name="statements"></a>语句

程序操作使用 ***语句*** 进行表示。 C# 支持几种不同的语句，其中许多语句是从嵌入语句的角度来定义的。

使用 ***代码块***，可以在允许编写一个语句的上下文中编写多个语句。 代码块是由一系列在分隔符 `{` 和 `}` 内编写的语句组成。

***声明语句*** 用于声明局部变量和常量。

***表达式语句*** 用于计算表达式。 可用作语句的表达式包括：方法调用、使用运算符的对象分配 `new` 、使用 `=` 和复合赋值运算符的赋值、使用 `++` and `--` 运算符和 await 表达式递增和递减运算。

***选择语句*** 用于根据一些表达式的值从多个可能的语句中选择一个以供执行。 这一类语句包括 `if` 和 `switch` 语句。

***迭代语句*** 用于重复执行嵌入语句。 这一类语句包括 `while`、`do`、`for` 和 `foreach` 语句。

***跳转语句*** 用于转移控制权。 这一类语句包括 `break`、`continue`、`goto`、`throw`、`return` 和 `yield` 语句。

`try`...`catch` 语句用于捕获在代码块执行期间发生的异常，`try`...`finally` 语句用于指定始终执行的最终代码，无论异常发生与否。

`checked`和 `unchecked` 语句用于控制整型算术运算和转换的溢出检查上下文。

`lock` 语句用于获取给定对象的相互排斥锁定，执行语句，然后解除锁定。

`using` 语句用于获取资源，执行语句，然后释放资源。

下面是每种语句的示例

__局部变量声明__

```csharp
static void Main() {
   int a;
   int b = 2, c = 3;
   a = 1;
   Console.WriteLine(a + b + c);
}
```


__局部常量声明__

```csharp
static void Main() {
    const float pi = 3.1415927f;
    const int r = 25;
    Console.WriteLine(pi * r * r);
}
```


__Expression 语句__

```csharp
static void Main() {
    int i;
    i = 123;                // Expression statement
    Console.WriteLine(i);   // Expression statement
    i++;                    // Expression statement
    Console.WriteLine(i);   // Expression statement
}
```

__`if` 语句__

```csharp
static void Main(string[] args) {
    if (args.Length == 0) {
        Console.WriteLine("No arguments");
    }
    else {
        Console.WriteLine("One or more arguments");
    }
}
```


__`switch` 语句__

```csharp
static void Main(string[] args) {
    int n = args.Length;
    switch (n) {
        case 0:
            Console.WriteLine("No arguments");
            break;
        case 1:
            Console.WriteLine("One argument");
            break;
        default:
            Console.WriteLine("{0} arguments", n);
            break;
    }
}
```

__`while` 语句__

```csharp
static void Main(string[] args) {
    int i = 0;
    while (i < args.Length) {
        Console.WriteLine(args[i]);
        i++;
    }
}
```


__`do` 语句__

```csharp
static void Main() {
    string s;
    do {
        s = Console.ReadLine();
        if (s != null) Console.WriteLine(s);
    } while (s != null);
}
```

__`for` 语句__

```csharp
static void Main(string[] args) {
    for (int i = 0; i < args.Length; i++) {
        Console.WriteLine(args[i]);
    }
}
```

__`foreach` 语句__

```csharp
static void Main(string[] args) {
    foreach (string s in args) {
        Console.WriteLine(s);
    }
}
```

__`break` 语句__

```csharp
static void Main() {
    while (true) {
        string s = Console.ReadLine();
        if (s == null) break;
        Console.WriteLine(s);
    }
}
```

__`continue` 语句__

```csharp
static void Main(string[] args) {
    for (int i = 0; i < args.Length; i++) {
        if (args[i].StartsWith("/")) continue;
        Console.WriteLine(args[i]);
    }
}
```

__`goto` 语句__

```csharp
static void Main(string[] args) {
    int i = 0;
    goto check;
    loop:
    Console.WriteLine(args[i++]);
    check:
    if (i < args.Length) goto loop;
}
```

__`return` 语句__

```csharp
static int Add(int a, int b) {
    return a + b;
}

static void Main() {
    Console.WriteLine(Add(1, 2));
    return;
}
```

__`yield` 语句__

```csharp
static IEnumerable<int> Range(int from, int to) {
    for (int i = from; i < to; i++) {
        yield return i;
    }
    yield break;
}

static void Main() {
    foreach (int x in Range(-10,10)) {
        Console.WriteLine(x);
    }
}
```

__`throw` 和 `try` 语句__

```csharp
static double Divide(double x, double y) {
    if (y == 0) throw new DivideByZeroException();
    return x / y;
}

static void Main(string[] args) {
    try {
        if (args.Length != 2) {
            throw new Exception("Two numbers required");
        }
        double x = double.Parse(args[0]);
        double y = double.Parse(args[1]);
        Console.WriteLine(Divide(x, y));
    }
    catch (Exception e) {
        Console.WriteLine(e.Message);
    }
    finally {
        Console.WriteLine("Good bye!");
    }
}
```

__`checked` 和 `unchecked` 语句__

```csharp
static void Main() {
    int i = int.MaxValue;
    checked {
        Console.WriteLine(i + 1);        // Exception
    }
    unchecked {
        Console.WriteLine(i + 1);        // Overflow
    }
}
```

__`lock` 语句__

```csharp
class Account
{
    decimal balance;
    public void Withdraw(decimal amount) {
        lock (this) {
            if (amount > balance) {
                throw new Exception("Insufficient funds");
            }
            balance -= amount;
        }
    }
}
```

__`using` 语句__

```csharp
static void Main() {
    using (TextWriter w = File.CreateText("test.txt")) {
        w.WriteLine("Line one");
        w.WriteLine("Line two");
        w.WriteLine("Line three");
    }
}
```

## <a name="classes-and-objects"></a>类和对象

***类** _ 是 c # 的最基本类型。 类是一种数据结构，可在一个单元中就将状态（字段）和操作（方法和其他函数成员）结合起来。 类为动态创建的类（也称为 _*_对象_*_）_*_实例_*_ 提供定义。 类支持 _*_继承_*_ 和 _*_多态性_*_，即 _*_派生类_*_ 可以扩展和专用化 _ *_基类_* * 的机制。

新类使用类声明进行创建。 类声明的开头是标头，指定了类的特性和修饰符、类名、基类（若指定）以及类实现的接口。 标头后面是类主体，由在分隔符 `{` 和 `}` 内编写的成员声明列表组成。

以下是简单类 `Point` 的声明：

```csharp
public class Point
{
    public int x, y;

    public Point(int x, int y) {
        this.x = x;
        this.y = y;
    }
}
```
类实例是使用 `new` 运算符进行创建，此运算符为新实例分配内存，调用构造函数来初始化实例，并返回对实例的引用。 以下语句创建两个 `Point` 对象，并将对这些对象的引用存储在两个变量中：

```csharp
Point p1 = new Point(0, 0);
Point p2 = new Point(10, 20);
```
当不再使用对象时，将自动回收对象占用的内存。 既没必要，也无法在 C# 中显式解除分配对象。

### <a name="members"></a>成员

类的成员为 ***静态成员** _ 或 _ *_实例成员_* *。 静态成员属于类，而实例成员则属于对象（类实例）。

下表提供了类可以包含的成员种类的概述。


| __成员__   | __说明__ |
|------------  |-----------------|
| 常量    | 与类相关联的常量值 |
| 字段       | 类的常量 |
| 方法      | 类可以执行的计算和操作 |
| 属性   | 与读取和写入类的已命名属性相关联的操作 |
| 索引器     | 与将类实例编入索引（像处理数组一样）相关联的操作 |
| 事件       | 类可以生成的通知 |
| 运算符    | 类支持的转换和表达式运算符 |
| 构造函数 | 初始化类实例或类本身所需的操作 |
| 析构函数  | 永久放弃类实例前要执行的操作 |
| 类型        | 类声明的嵌套类型 |

### <a name="accessibility"></a>辅助功能

每个类成员都有关联的可访问性，用于控制能够访问成员的程序文本区域。 可访问性有五种可能的形式。 下表汇总了这些更新。


| __辅助功能__    | __含义__ |
|----------------------|-----------------|
| `public`             | 访问不受限 |
| `protected`          | 只能访问此类或派生自此类的类 |
| `internal`           | 只能访问此程序 |
| `protected internal` | 只能访问此程序或派生自此类的类 |
| `private`            | 只能访问此类 |

### <a name="type-parameters"></a>类型参数

类定义可能会按如下方式指定一组类型参数：在类名后面用尖括号括住类型参数名称列表。 然后，可以在类声明的主体中使用类型参数来定义类成员。 在以下示例中，`Pair` 的类型参数是 `TFirst` 和 `TSecond`：

```csharp
public class Pair<TFirst,TSecond>
{
    public TFirst First;
    public TSecond Second;
}
```
声明为采用类型参数的类类型称为泛型类类型。 结构、接口和委托类型也可以是泛型。

使用泛型类时，必须为每个类型参数提供类型自变量：

```csharp
Pair<int,string> pair = new Pair<int,string> { First = 1, Second = "two" };
int i = pair.First;     // TFirst is int
string s = pair.Second; // TSecond is string
```
提供类型参数的泛型类型（如上文所示 `Pair<int,string>` ）称为构造类型。

### <a name="base-classes"></a>基类

类声明可能会按如下方式指定基类：在类名和类型参数后面编写冒号和基类名。 省略基类规范与从 `object` 类型派生相同。 在以下示例中，`Point3D` 的基类是 `Point`，`Point` 的基类是 `object`：

```csharp
public class Point
{
    public int x, y;

    public Point(int x, int y) {
        this.x = x;
        this.y = y;
    }
}

public class Point3D: Point
{
    public int z;

    public Point3D(int x, int y, int z): base(x, y) {
        this.z = z;
    }
}
```
类继承其基类的成员。 继承意味着类隐式包含其基类的所有成员（实例和静态构造函数除外）和基类的析构函数。 派生类可以其继承的类添加新成员，但无法删除继承成员的定义。 在上面的示例中，`Point3D` 从 `Point` 继承了 `x` 和 `y` 字段，每个 `Point3D` 实例均包含三个字段（`x`、`y` 和 `z`）。

可以将类类型隐式转换成其任意基类类型。 因此，类类型的变量可以引用相应类的实例或任意派生类的实例。 例如，类声明如上，`Point` 类型的变量可以引用 `Point` 或 `Point3D`：

```csharp
Point a = new Point(10, 20);
Point b = new Point3D(10, 20, 30);
```

### <a name="fields"></a>字段

字段是与类或类实例相关联的变量。

使用修饰符声明的字段 `static` 定义 ***静态字段***。 静态字段只指明一个存储位置。 无论创建多少个类实例，永远只有一个静态字段副本。

未使用修饰符声明的字段 `static` 定义 ***实例字段***。 每个类实例均包含相应类的所有实例字段的单独副本。

在以下示例中，每个 `Color` 类实例均包含 `r`、`g` 和 `b` 实例字段的单独副本，但分别只包含 `Black`、`White`、`Red`、`Green` 和 `Blue` 静态字段的一个副本：

```csharp
public class Color
{
    public static readonly Color Black = new Color(0, 0, 0);
    public static readonly Color White = new Color(255, 255, 255);
    public static readonly Color Red = new Color(255, 0, 0);
    public static readonly Color Green = new Color(0, 255, 0);
    public static readonly Color Blue = new Color(0, 0, 255);
    private byte r, g, b;

    public Color(byte r, byte g, byte b) {
        this.r = r;
        this.g = g;
        this.b = b;
    }
}
```
如上面的示例所示，可以使用 `readonly` 修饰符声明 ***只读字段***。 对字段的赋值 `readonly` 只能作为字段声明的一部分出现，或者出现在同一类的构造函数中。

### <a name="methods"></a>方法

***方法** _ 是实现可由对象或类执行的计算或操作的成员。 通过类访问 _*_静态方法_*_。 _ *_实例方法_** 可通过类的实例进行访问。

方法具有 (可能为空的 ***parameters**) 列表（表示传递给方法的值或变量引用）和一个 _ *_返回类型_* * （指定方法计算并返回的值的类型）。 如果不返回值，则为方法的返回类型 `void` 。

方法可能也包含一组类型参数，必须在调用方法时指定类型自变量，这一点与类型一样。 与类型不同的是，通常可以根据方法调用的自变量推断出类型自变量，无需显式指定。

在声明方法的类中，方法的 ***签名*** 必须是唯一的。 方法签名包含方法名称、类型参数数量及其参数的数量、修饰符和类型。 方法签名不包含返回类型。

#### <a name="parameters"></a>参数

参数用于将值或变量引用传递给方法。 方法参数从调用方法时指定的 ***自变量*** 中获取其实际值。 有四类参数：值参数、引用参数、输出参数和参数数组。

***值参数*** 用于传递输入参数。 值参数对应于局部变量，从为其传递的自变量中获取初始值。 修改值参数不会影响为其传递的自变量。

可以指定默认值，从而省略相应的自变量，这样值参数就是可选的。

***引用参数*** 用于传递输入和输出参数。 为引用参数传递的自变量必须是变量，并且在方法执行期间，引用参数指明的存储位置与自变量相同。 引用参数使用 `ref` 修饰符进行声明。 下面的示例展示了如何使用 `ref` 参数。

```csharp
using System;

class Test
{
    static void Swap(ref int x, ref int y) {
        int temp = x;
        x = y;
        y = temp;
    }

    static void Main() {
        int i = 1, j = 2;
        Swap(ref i, ref j);
        Console.WriteLine("{0} {1}", i, j);            // Outputs "2 1"
    }
}
```
***输出参数*** 用于传递输出参数。 输出参数与引用参数类似，不同之处在于，调用方提供的自变量的初始值并不重要。 输出参数使用 `out` 修饰符进行声明。 下面的示例展示了如何使用 `out` 参数。

```csharp
using System;

class Test
{
    static void Divide(int x, int y, out int result, out int remainder) {
        result = x / y;
        remainder = x % y;
    }

    static void Main() {
        int res, rem;
        Divide(10, 3, out res, out rem);
        Console.WriteLine("{0} {1}", res, rem);    // Outputs "3 1"
    }
}
```
***参数数组*** 允许向方法传递数量不定的自变量。 参数数组使用 `params` 修饰符进行声明。 参数数组只能是方法的最后一个参数，且参数数组的类型必须是一维数组类型。 `System.Console` 类的 `Write` 和 `WriteLine` 方法是参数数组用法的典型示例。 它们的声明方式如下。

```csharp
public class Console
{
    public static void Write(string fmt, params object[] args) {...}
    public static void WriteLine(string fmt, params object[] args) {...}
    ...
}
```
在使用参数数组的方法中，参数数组的行为与数组类型的常规参数完全相同。 不过，在调用包含参数数组的方法时，要么可以传递参数数组类型的一个自变量，要么可以传递参数数组的元素类型的任意数量自变量。 在后一种情况中，数组实例会自动创建，并初始化为包含给定的自变量。 以下示例：

```csharp
Console.WriteLine("x={0} y={1} z={2}", x, y, z);
```
等同于编写以下代码：

```csharp
string s = "x={0} y={1} z={2}";
object[] args = new object[3];
args[0] = x;
args[1] = y;
args[2] = z;
Console.WriteLine(s, args);
```

#### <a name="method-body-and-local-variables"></a>方法主体和局部变量

方法的主体指定在调用方法时要执行的语句。

方法主体可以声明特定于方法调用的变量。 此类变量称为 ***局部变量***。 局部变量声明指定了类型名称、变量名称以及可能的初始值。 下面的示例声明了初始值为零的局部变量 `i` 和无初始值的局部变量 `j`。

```csharp
using System;

class Squares
{
    static void Main() {
        int i = 0;
        int j;
        while (i < 10) {
            j = i * i;
            Console.WriteLine("{0} x {0} = {1}", i, j);
            i = i + 1;
        }
    }
}
```
C# 要求必须先 ***明确赋值*** 局部变量，然后才能获取其值。 例如，如果上面的 `i` 声明未包含初始值，那么编译器会在后面使用 `i` 时报告错误，因为在后面使用时 `i` 不会在程序中进行明确赋值。

方法可以使用 `return` 语句将控制权返回给调用方。 在返回 `void` 的方法中，`return` 语句无法指定表达式。 在返回非的方法中 `void` ， `return` 语句必须包含用于计算返回值的表达式。

#### <a name="static-and-instance-methods"></a>静态和实例方法

使用 `static` 修饰符声明的方法是静态方法。 静态方法不对特定的实例起作用，只能直接访问静态成员。

未使用 `static` 修饰符声明的方法是实例方法。 实例方法对特定的实例起作用，并能够访问静态和实例成员。 其中调用实例方法的实例可以作为 `this` 显式访问。 在静态方法中引用 `this` 会生成错误。

以下 `Entity` 类包含静态和实例成员。

```csharp
class Entity
{
    static int nextSerialNo;
    int serialNo;

    public Entity() {
        serialNo = nextSerialNo++;
    }

    public int GetSerialNo() {
        return serialNo;
    }

    public static int GetNextSerialNo() {
        return nextSerialNo;
    }

    public static void SetNextSerialNo(int value) {
        nextSerialNo = value;
    }
}
```
每个 `Entity` 实例均有一个序列号（很可能包含此处未显示的其他一些信息）。 `Entity` 构造函数（类似于实例方法）将新实例初始化为包含下一个可用的序列号。 由于构造函数是实例成员，因此可以访问 `serialNo` 实例字段和 `nextSerialNo` 静态字段。

`GetNextSerialNo` 和 `SetNextSerialNo` 静态方法可以访问 `nextSerialNo` 静态字段，但如果直接访问 `serialNo` 实例字段，则会生成错误。

下例显示了 `Entity` 类的用法。

```csharp
using System;

class Test
{
    static void Main() {
        Entity.SetNextSerialNo(1000);
        Entity e1 = new Entity();
        Entity e2 = new Entity();
        Console.WriteLine(e1.GetSerialNo());           // Outputs "1000"
        Console.WriteLine(e2.GetSerialNo());           // Outputs "1001"
        Console.WriteLine(Entity.GetNextSerialNo());   // Outputs "1002"
    }
}
```
请注意，`SetNextSerialNo` 和 `GetNextSerialNo` 静态方法是在类中调用，而 `GetSerialNo` 实例方法则是在类实例中调用。

#### <a name="virtual-override-and-abstract-methods"></a>虚方法、重写方法和抽象方法

当实例方法声明包含修饰符时 `virtual` ，该方法被称为 ***虚拟方法** _。 当不 `virtual` 存在修饰符时，此方法被称为 _ *_非虚方法_* *。

调用虚方法时，调用发生的实例的 ***运行时类型** _ 将确定要调用的实际方法实现。 在非虚拟方法调用中，实例的 _ *_编译时类型_** 是确定因素。

可以在派生类中 ***重写*** 虚方法。 当实例方法声明包含修饰符时 `override` ，此方法将重写具有相同签名的继承的虚方法。 但如果虚方法声明中引入新方法，重写方法声明通过提供相应方法的新实现代码，专门针对现有的继承虚方法。

***抽象*** 方法是无实现的虚方法。 抽象方法使用修饰符进行声明 `abstract` ，只允许在也声明的类中使用 `abstract` 。 必须在所有非抽象派生类中重写抽象方法。

下面的示例声明了一个抽象类 `Expression`，用于表示表达式树节点；还声明了三个派生类（`Constant`、`VariableReference` 和 `Operation`），用于实现常量、变量引用和算术运算的表达式树节点。  (这类似于，但不与 [表达式树类型](types.md#expression-tree-types) 中引入的表达式树类型混淆) 。

```csharp
using System;
using System.Collections;

public abstract class Expression
{
    public abstract double Evaluate(Hashtable vars);
}

public class Constant: Expression
{
    double value;

    public Constant(double value) {
        this.value = value;
    }

    public override double Evaluate(Hashtable vars) {
        return value;
    }
}

public class VariableReference: Expression
{
    string name;

    public VariableReference(string name) {
        this.name = name;
    }

    public override double Evaluate(Hashtable vars) {
        object value = vars[name];
        if (value == null) {
            throw new Exception("Unknown variable: " + name);
        }
        return Convert.ToDouble(value);
    }
}

public class Operation: Expression
{
    Expression left;
    char op;
    Expression right;

    public Operation(Expression left, char op, Expression right) {
        this.left = left;
        this.op = op;
        this.right = right;
    }

    public override double Evaluate(Hashtable vars) {
        double x = left.Evaluate(vars);
        double y = right.Evaluate(vars);
        switch (op) {
            case '+': return x + y;
            case '-': return x - y;
            case '*': return x * y;
            case '/': return x / y;
        }
        throw new Exception("Unknown operator");
    }
}
```
上面的四个类可用于进行算术表达式建模。 例如，使用这些类的实例，可以按如下方式表示表达式 `x + 3`。

```csharp
Expression e = new Operation(
    new VariableReference("x"),
    '+',
    new Constant(3));
```
调用 `Expression` 实例的 `Evaluate` 方法可以计算给定的表达式并生成 `double` 值。 此方法采用作为参数的 `Hashtable` ，其中包含变量名称 (作为项的键) 和值 (为项) 的值。 `Evaluate`方法是一个虚拟抽象方法，这意味着非抽象派生类必须重写它以提供实际实现。

`Constant` 的 `Evaluate` 实现代码只返回存储的常量。 的 `VariableReference` 实现查找哈希表中的变量名并返回结果值。 `Operation` 实现代码先计算左右操作数（以递归方式调用其 `Evaluate` 方法），然后执行给定的算术运算。

以下程序使用 `Expression` 类根据不同的 `x` 和 `y` 值计算表达式 `x * (y + 2)`。

```csharp
using System;
using System.Collections;

class Test
{
    static void Main() {
        Expression e = new Operation(
            new VariableReference("x"),
            '*',
            new Operation(
                new VariableReference("y"),
                '+',
                new Constant(2)
            )
        );
        Hashtable vars = new Hashtable();
        vars["x"] = 3;
        vars["y"] = 5;
        Console.WriteLine(e.Evaluate(vars));        // Outputs "21"
        vars["x"] = 1.5;
        vars["y"] = 9;
        Console.WriteLine(e.Evaluate(vars));        // Outputs "16.5"
    }
}
```

#### <a name="method-overloading"></a>方法重载

方法 ***重载** _ 允许同一类中的多个方法具有相同的名称，只要它们具有唯一的签名。 在编译重载方法的调用时，编译器使用 _ *_重载决策_** 来确定要调用的特定方法。 重载决策查找与自变量最匹配的方法；如果找不到最佳匹配项，则会报告错误。 下面的示例展示了重载决策的实际工作方式。 `Main` 方法中每个调用的注释指明了实际调用的方法。

```csharp
class Test
{
    static void F() {
        Console.WriteLine("F()");
    }

    static void F(object x) {
        Console.WriteLine("F(object)");
    }

    static void F(int x) {
        Console.WriteLine("F(int)");
    }

    static void F(double x) {
        Console.WriteLine("F(double)");
    }

    static void F<T>(T x) {
        Console.WriteLine("F<T>(T)");
    }

    static void F(double x, double y) {
        Console.WriteLine("F(double, double)");
    }

    static void Main() {
        F();                 // Invokes F()
        F(1);                // Invokes F(int)
        F(1.0);              // Invokes F(double)
        F("abc");            // Invokes F(object)
        F((double)1);        // Invokes F(double)
        F((object)1);        // Invokes F(object)
        F<int>(1);           // Invokes F<T>(T)
        F(1, 1);             // Invokes F(double, double)
    }
}
```
如示例所示，可以随时将自变量显式转换成确切的参数类型，并/或显式提供类型自变量，从而选择特定的方法。

### <a name="other-function-members"></a>其他函数成员

包含可执行代码的成员统称为类的 ***函数成员***。 上一部分介绍了作为主要函数成员类型的方法。 本节介绍 c # 支持的其他类型的函数成员：构造函数、属性、索引器、事件、运算符和析构函数。

下面的代码演示了一个名为 `List<T>` 的泛型类，该类实现对象的可扩充列表。 此类包含最常见类型函数成员的多个示例。


```csharp
public class List<T> {
    // Constant...
    const int defaultCapacity = 4;

    // Fields...
    T[] items;
    int count;

    // Constructors...
    public List(int capacity = defaultCapacity) {
        items = new T[capacity];
    }

    // Properties...
    public int Count {
        get { return count; }
    }
    public int Capacity {
        get {
            return items.Length;
        }
        set {
            if (value < count) value = count;
            if (value != items.Length) {
                T[] newItems = new T[value];
                Array.Copy(items, 0, newItems, 0, count);
                items = newItems;
            }
        }
    }

    // Indexer...
    public T this[int index] {
        get {
            return items[index];
        }
        set {
            items[index] = value;
            OnChanged();
        }
    }

    // Methods...
    public void Add(T item) {
        if (count == Capacity) Capacity = count * 2;
        items[count] = item;
        count++;
        OnChanged();
    }
    protected virtual void OnChanged() {
        if (Changed != null) Changed(this, EventArgs.Empty);
    }
    public override bool Equals(object other) {
        return Equals(this, other as List<T>);
    }
    static bool Equals(List<T> a, List<T> b) {
        if (a == null) return b == null;
        if (b == null || a.count != b.count) return false;
        for (int i = 0; i < a.count; i++) {
            if (!object.Equals(a.items[i], b.items[i])) {
                return false;
            }
        }
        return true;
    }

    // Event...
    public event EventHandler Changed;

    // Operators...
    public static bool operator ==(List<T> a, List<T> b) {
        return Equals(a, b);
    }
    public static bool operator !=(List<T> a, List<T> b) {
        return !Equals(a, b);
    }
}
```

#### <a name="constructors"></a>构造函数

C# 支持实例和静态构造函数。 ***实例构造函数** _ 是一个成员，用于实现初始化类的实例所需的操作。 _ *_静态构造函数_** 是一个成员，用于实现第一次加载时初始化类本身所需的操作。

构造函数的声明方式与方法一样，都没有返回类型，且与所含类同名。 如果构造函数声明包含 `static` 修饰符，则声明的是静态构造函数。 否则，声明的是实例构造函数。

可以重载实例构造函数。 例如，`List<T>` 类声明两个实例构造函数：一个没有参数，另一个需要使用 `int` 参数。 实例构造函数使用 `new` 运算符进行调用。 以下语句 `List<string>` 使用类的每个构造函数分配两个实例 `List` 。

```csharp
List<string> list1 = new List<string>();
List<string> list2 = new List<string>(10);
```
与其他成员不同，实例构造函数不能被继承，且类中只能包含实际已声明的实例构造函数。 如果没有为类提供实例构造函数，则会自动提供不含参数的空实例构造函数。

#### <a name="properties"></a>“属性”

***Properties** _ 是字段的自然扩展。 两者都是包含关联类型的已命名成员，用于访问字段和属性的语法也是一样的。 不过，与字段不同的是，属性不指明存储位置。 相反，属性具有 _ *_访问器_**，用于指定读取或写入它们的值时要执行的语句。

属性的声明方式与字段类似，不同之处在于声明以 `get` 取值函数和/或分隔符结尾， `set` 而不是 `{` 以 `}` 分号结束。 同时具有 `get` 访问器和访问器的属性 `set` 是一个 ***读写属性** _，只有一个 `get` 访问器的属性是 _*_只读属性_*_，并且只有一个 `set` 访问器的属性是一个 _ *_只写属性_* *。

`get`访问器与具有属性类型返回值的无参数方法相对应。 除了作为赋值的目标，当在表达式中引用属性时，将 `get` 调用属性的访问器来计算属性的值。

`set`访问器对应于方法，该方法具有一个名为的参数 `value` ，没有返回类型。 当属性作为赋值的目标或作为或的操作数进行引用时 `++` `--` ，将 `set` 使用提供新值的自变量调用访问器。

`List<T>` 类声明以下两个属性：`Count` 和 `Capacity`（分别为只读和读写）。 下面的示例展示了如何使用这些属性。

```csharp
List<string> names = new List<string>();
names.Capacity = 100;            // Invokes set accessor
int i = names.Count;             // Invokes get accessor
int j = names.Capacity;          // Invokes get accessor
```
类似于字段和方法，C# 支持实例属性和静态属性。 静态属性是用修饰符声明的 `static` ，实例属性是在没有它的情况下声明的。

属性的访问器可以是虚的。 如果属性声明包含 `virtual`、`abstract` 或 `override` 修饰符，则适用于属性的访问器。

#### <a name="indexers"></a>索引器

借助 ***索引器*** 成员，可以将对象编入索引（像处理数组一样）。 索引器的声明方式与属性类似，不同之处在于，索引器成员名称格式为 `this` 后跟在分隔符 `[` 和 `]` 内写入的参数列表。 这些参数在索引器的访问器中可用。 类似于属性，索引器分为读写、只读和只写索引器，且索引器的访问器可以是虚的。

`List` 类声明一个需要使用 `int` 参数的读写索引器。 借助索引器，可以使用 `int` 值将 `List` 实例编入索引。 例如

```csharp
List<string> names = new List<string>();
names.Add("Liz");
names.Add("Martha");
names.Add("Beth");
for (int i = 0; i < names.Count; i++) {
    string s = names[i];
    names[i] = s.ToUpper();
}
```
索引器可以进行重载。也就是说，类可以声明多个索引器，只要其参数的数量或类型不同即可。

#### <a name="events"></a>事件

借助 ***事件*** 成员，类或对象可以提供通知。 事件的声明方式与字段类似，区别是事件声明包括 `event` 关键字，且类型必须是委托类型。

在声明事件成员的类中，事件的行为与委托类型的字段完全相同（前提是事件不是抽象的，且不声明访问器）。 字段存储对委托的引用，委托表示已添加到事件的事件处理程序。 如果不存在任何事件句柄，则字段为 `null` 。

`List<T>` 类声明一个 `Changed` 事件成员，指明已向列表添加了新项。 此 `Changed` 事件由 `OnChanged` 虚拟方法引发，该方法首先检查事件是否 `null` (表示) 没有处理程序。 引发事件的概念恰恰等同于调用由事件表示的委托，因此，没有用于引发事件的特殊语言构造。

客户端通过 ***事件处理程序*** 响应事件。 使用 `+=` 和 `-=` 运算符分别可以附加和删除事件处理程序。 下面的示例展示了如何向 `List<string>` 的 `Changed` 事件附加事件处理程序。

```csharp
using System;

class Test
{
    static int changeCount;

    static void ListChanged(object sender, EventArgs e) {
        changeCount++;
    }

    static void Main() {
        List<string> names = new List<string>();
        names.Changed += new EventHandler(ListChanged);
        names.Add("Liz");
        names.Add("Martha");
        names.Add("Beth");
        Console.WriteLine(changeCount);        // Outputs "3"
    }
}
```
对于需要控制事件的基础存储的高级方案，事件声明可以显式提供 `add` 和 `remove` 访问器，这在某种程度上与属性的 `set` 访问器类似。

#### <a name="operators"></a>运算符

***运算符*** 是定义向类实例应用特定表达式运算符的含义的成员。 可以定义三种类型的运算符：一元运算符、二元运算符和转换运算符。 所有运算符都必须声明为 `public` 和 `static`。

`List<T>` 类声明两个运算符（`operator==` 和 `operator!=`），因此定义了向 `List` 实例应用这些运算符的表达式的新含义。 具体而言，这些运算符定义的是两个 `List<T>` 实例的相等性（使用其 `Equals` 方法比较所包含的每个对象）。 下面的示例展示了如何使用 `==` 运算符比较两个 `List<int>` 实例。

```csharp
using System;

class Test
{
    static void Main() {
        List<int> a = new List<int>();
        a.Add(1);
        a.Add(2);
        List<int> b = new List<int>();
        b.Add(1);
        b.Add(2);
        Console.WriteLine(a == b);        // Outputs "True"
        b.Add(3);
        Console.WriteLine(a == b);        // Outputs "False"
    }
}
```

第一个 `Console.WriteLine` 输出 `True`，因为两个列表包含的对象不仅数量相同，而且值和顺序也相同。 如果 `List<T>` 未定义 `operator==`，那么第一个 `Console.WriteLine` 会输出 `False`，因为 `a` 和 `b` 引用不同的 `List<int>` 实例。

#### <a name="destructors"></a>析构函数

***析构函数*** 是实现析构类的实例所需的操作的成员。 析构函数不能有参数，它们不能具有可访问性修饰符，也不能被显式调用。 在垃圾回收过程中会自动调用实例的析构函数。

垃圾回收器允许使用广泛的纬度来确定何时收集对象和运行析构函数。 具体而言，析构函数调用的时间是不确定的，析构函数可以在任何线程上执行。 出于这些原因和其他原因，类应仅在没有其他任何解决方案可行时才实现析构函数。

处理对象析构的更好方法是使用 `using` 语句。

## <a name="structs"></a>结构

***结构*** 是可以包含数据成员和函数成员的数据结构，这一点与类一样；与类不同的是，结构是值类型，无需进行堆分配。 结构类型的变量直接存储结构数据，而类类型的变量存储对动态分配的对象的引用。 结构类型不支持用户指定的继承，并且所有结构类型均隐式继承自类型 `object`。

结构对包含值语义的小型数据结构特别有用。 复数、坐标系中的点或字典中的键值对都是结构的典型示例。 对小型数据结构使用结构（而不是类）在应用程序执行的内存分配次数上存在巨大差异。 例如，以下程序创建并初始化包含 100 个点的数组。 通过将 `Point` 实现为类，可单独实例化 101 个对象，一个对象用于数组，其他所有对象分别用于 100 个元素。

```csharp
class Point
{
    public int x, y;

    public Point(int x, int y) {
        this.x = x;
        this.y = y;
    }
}

class Test
{
    static void Main() {
        Point[] points = new Point[100];
        for (int i = 0; i < 100; i++) points[i] = new Point(i, i);
    }
}
```
另一种方法是创建 `Point` 结构。

```csharp
struct Point
{
    public int x, y;

    public Point(int x, int y) {
        this.x = x;
        this.y = y;
    }
}
```
现在，仅实例化一个对象（即用于数组的对象），`Point` 实例存储内嵌在数组中。

结构构造函数使用 `new` 运算符进行调用，但这不并表示要分配内存。 结构构造函数只返回结构值本身（通常在堆栈的临时位置中），并在必要时复制此值，而非动态分配对象并返回对此对象的引用。

借助类，两个变量可以引用同一对象；因此，对一个变量执行的运算可能会影响另一个变量引用的对象。 借助结构，每个变量都有自己的数据副本；因此，对一个变量执行的运算不会影响另一个变量。 例如，下面的代码段生成的输出取决于 `Point` 是类还是结构。

```csharp
Point a = new Point(10, 10);
Point b = a;
a.x = 20;
Console.WriteLine(b.x);
```
如果 `Point` 是一个类，则输出为 `20` ， `a` 并且 `b` 引用相同的对象。 如果 `Point` 是一个结构，则输出为， `10` 因为的赋值 `a` `b` 创建值的副本，而此副本不受对的后续赋值的影响 `a.x` 。

以上示例突出显示了结构的两个限制。 首先，复制整个结构通常比复制对象引用效率更低，因此通过结构进行的赋值和值参数传递可能比通过引用类型成本更高。 其次，除 `ref` 和 `out` 参数以外，无法创建对结构的引用，这就表示在很多应用场景中都不能使用结构。

## <a name="arrays"></a>数组

***Array** _ 是一种数据结构，其中包含可通过计算索引访问的多个变量。 数组中包含的变量（也称为数组的 _*_元素_*_ ）都属于同一类型，并且此类型称为数组的 _ *_元素类型_**。

数组类型是引用类型，声明数组变量只是为引用数组实例预留空间。 实际的数组实例是在运行时使用运算符动态创建的 `new` 。 `new` 运算指定了新数组实例的长度，然后在此实例的生存期内固定使用这个长度。 数组元素的索引介于 `0` 到 `Length - 1` 之间。 `new` 运算符自动将数组元素初始化为其默认值（例如，所有数值类型的默认值为 0，所有引用类型的默认值为 `null`）。

以下示例创建 `int` 元素数组，初始化此数组，然后打印输出此数组的内容。

```csharp
using System;

class Test
{
    static void Main() {
        int[] a = new int[10];
        for (int i = 0; i < a.Length; i++) {
            a[i] = i * i;
        }
        for (int i = 0; i < a.Length; i++) {
            Console.WriteLine("a[{0}] = {1}", i, a[i]);
        }
    }
}
```
此示例将创建一个 * 一 **维数组** _ 并对其进行操作。 C# 还支持多维数组。 数组类型的维度的数量（也称为数组类型的 _ *_秩_**）是一个加在数组类型方括号之间的逗号的数目。 下面的示例分配一个一维、二维和三维数组。

```csharp
int[] a1 = new int[10];
int[,] a2 = new int[10, 5];
int[,,] a3 = new int[10, 5, 2];
```
`a1` 数组包含 10 个元素，`a2` 数组包含 50 个元素 (10 × 5)，`a3` 数组包含 100 个元素 (10 × 5 × 2)。

数组的元素类型可以是任意类型（包括数组类型）。 包含数组类型元素的数组有时称为 ***交错数组***，因为元素数组的长度不必全都一样。 以下示例分配由 `int` 数组构成的数组：

```csharp
int[][] a = new int[3][];
a[0] = new int[10];
a[1] = new int[5];
a[2] = new int[20];
```
第一行创建包含三个元素的数组，每个元素都是 `int[]` 类型，并且初始值均为 `null`。 后面的代码行将这三个元素初始化为引用长度不同的各个数组实例。

通过 `new` 运算符，可以使用“数组初始值设定项”（在分隔符 `{` 和 `}` 内编写的表达式列表）指定数组元素的初始值。 以下示例分配 `int[]`，并将其初始化为包含三个元素。

```csharp
int[] a = new int[] {1, 2, 3};
```
请注意，数组的长度是从和之间的表达式数推断出来 `{` 的 `}` 。 局部变量和字段声明可以进一步缩短，这样就不用重新声明数组类型了。

```csharp
int[] a = {1, 2, 3};
```
以上两个示例等同于以下示例：

```csharp
int[] t = new int[3];
t[0] = 1;
t[1] = 2;
t[2] = 3;
int[] a = t;
```
## <a name="interfaces"></a>接口

***接口*** 定义了可由类和结构实现的协定。 接口可以包含方法、属性、事件和索引器。 接口不提供所定义的成员的实现代码，仅指定必须由实现接口的类或结构提供的成员。

接口可以采用 ***多重继承***。 在以下示例中，接口 `IComboBox` 同时继承自 `ITextBox` 和 `IListBox`。

```csharp
interface IControl
{
    void Paint();
}

interface ITextBox: IControl
{
    void SetText(string text);
}

interface IListBox: IControl
{
    void SetItems(string[] items);
}

interface IComboBox: ITextBox, IListBox {}
```
类和结构可以实现多个接口。 在以下示例中，类 `EditBox` 同时实现 `IControl` 和 `IDataBound`。

```csharp
interface IDataBound
{
    void Bind(Binder b);
}

public class EditBox: IControl, IDataBound
{
    public void Paint() {...}
    public void Bind(Binder b) {...}
}
```
当类或结构实现特定接口时，此类或结构的实例可以隐式转换成相应的接口类型。 例如

```csharp
EditBox editBox = new EditBox();
IControl control = editBox;
IDataBound dataBound = editBox;
```
如果已知实例不是静态地实现特定接口，可以使用动态类型显式转换功能。 例如，下面的语句使用动态类型强制转换来获取对象的 `IControl` 和 `IDataBound` 接口实现。 由于对象的实际类型为，因此 `EditBox` 强制转换成功。

```csharp
object obj = new EditBox();
IControl control = (IControl)obj;
IDataBound dataBound = (IDataBound)obj;
```
在以前的 `EditBox` 类中， `Paint` 接口中的方法 `IControl` 和接口中的 `Bind` 方法 `IDataBound` 是使用成员实现的 `public` 。 C # 还支持 ***显式接口成员实现***，使用该类或结构可以避免成为成员 `public` 。 显式接口成员实现代码是使用完全限定的接口成员名称进行编写。 例如，`EditBox` 类可以使用显式接口成员实现代码来实现 `IControl.Paint` 和 `IDataBound.Bind` 方法，如下所示。

```csharp
public class EditBox: IControl, IDataBound
{
    void IControl.Paint() {...}
    void IDataBound.Bind(Binder b) {...}
}
```
显式接口成员只能通过接口类型进行访问。 例如， `IControl.Paint` `EditBox` 只有先将 `EditBox` 引用转换为接口类型，才能调用上一个类提供的实现 `IControl` 。

```csharp
EditBox editBox = new EditBox();
editBox.Paint();                        // Error, no such method
IControl control = editBox;
control.Paint();                        // Ok
```

## <a name="enums"></a>枚举

***枚举类型*** 是包含一组已命名常量的独特值类型。 下面的示例声明并使用名为 `Color` 三个常数值、、和的枚举类型 `Red` `Green` `Blue` 。

```csharp
using System;

enum Color
{
    Red,
    Green,
    Blue
}

class Test
{
    static void PrintColor(Color color) {
        switch (color) {
            case Color.Red:
                Console.WriteLine("Red");
                break;
            case Color.Green:
                Console.WriteLine("Green");
                break;
            case Color.Blue:
                Console.WriteLine("Blue");
                break;
            default:
                Console.WriteLine("Unknown color");
                break;
        }
    }

    static void Main() {
        Color c = Color.Red;
        PrintColor(c);
        PrintColor(Color.Blue);
    }
}
```
每个枚举类型都有一个对应的整型类型，称为枚举类型的 ***基础类型*** 。 不显式声明基础类型的枚举类型具有基础类型 `int` 。 枚举类型的存储格式和可能值的范围由其基础类型决定。 枚举类型可以采用的值集不受其枚举成员限制。 特别是，枚举的基础类型的任何值都可以转换为枚举类型，并且是该枚举类型的一个不同的有效值。

下面的示例声明一个名为 `Alignment` 且基础类型为的枚举类型 `sbyte` 。

```csharp
enum Alignment: sbyte
{
    Left = -1,
    Center = 0,
    Right = 1
}
```
如前面的示例所示，枚举成员声明可以包含指定成员的值的常量表达式。 每个枚举成员的常数值必须在该枚举的基础类型的范围内。 当枚举成员声明未显式指定一个值时，如果该成员是枚举) 类型中的第一个成员，则将其值指定为零 (值为零。

可以使用类型强制转换将枚举值转换为整数值，反之亦然。 例如

```csharp
int i = (int)Color.Blue;        // int i = 2;
Color c = (Color)2;             // Color c = Color.Blue;
```
任何枚举类型的默认值都是整数值零，转换为枚举类型。 如果变量自动初始化为默认值，则这是给定给枚举类型的变量的值。 为了使枚举类型的默认值易于使用，文本 `0` 隐式转换为任何枚举类型。 因此，可以运行以下命令。

```csharp
Color c = 0;
```

## <a name="delegates"></a>委托

***委托类型*** 表示对具有特定参数列表和返回类型的方法的引用。 通过委托，可以将方法视为可分配给变量并可作为参数传递的实体。 委托类似于其他一些语言中的函数指针概念，但与函数指针不同的是，委托不仅面向对象，还类型安全。

下面的示例声明并使用 `Function` 委托类型。

```csharp
using System;

delegate double Function(double x);

class Multiplier
{
    double factor;

    public Multiplier(double factor) {
        this.factor = factor;
    }

    public double Multiply(double x) {
        return x * factor;
    }
}

class Test
{
    static double Square(double x) {
        return x * x;
    }

    static double[] Apply(double[] a, Function f) {
        double[] result = new double[a.Length];
        for (int i = 0; i < a.Length; i++) result[i] = f(a[i]);
        return result;
    }

    static void Main() {
        double[] a = {0.0, 0.5, 1.0};
        double[] squares = Apply(a, Square);
        double[] sines = Apply(a, Math.Sin);
        Multiplier m = new Multiplier(2.0);
        double[] doubles =  Apply(a, m.Multiply);
    }
}
```
`Function` 委托类型实例可以引用需要使用 `double` 自变量并返回 `double` 值的方法。 `Apply` 方法将给定的 `Function` 应用于 `double[]` 的元素，从而返回包含结果的 `double[]`。 在 `Main` 方法中，`Apply` 用于向 `double[]` 应用三个不同的函数。

委托可以引用静态方法（如上面示例中的 `Square` 或 `Math.Sin`）或实例方法（如上面示例中的 `m.Multiply`）。 引用实例方法的委托还会引用特定对象，通过委托调用实例方法时，该对象会变成调用中的 `this`。

还可以使用匿名函数创建委托，这些函数是便捷创建的“内联方法”。 匿名函数可以查看周围方法的局部变量。 因此，可以更轻松地编写上面的乘数示例，而无需使用 `Multiplier` 类：

```csharp
double[] doubles =  Apply(a, (double x) => x * 2.0);
```
委托的一个有趣且有用的属性是，它不知道也不关心所引用的方法的类；只关心引用的方法是否具有与委托相同的参数和返回类型。

## <a name="attributes"></a>属性

C# 程序中的类型、成员和其他实体支持使用修饰符来控制其行为的某些方面。 例如，方法的可访问性是由 `public`、`protected`、`internal` 和 `private` 修饰符控制。 C# 整合了这种能力，以便可以将用户定义类型的声明性信息附加到程序实体，并在运行时检索此类信息。 程序通过定义和使用 ***特性*** 来指定此附加声明性信息。

以下示例声明了 `HelpAttribute` 特性，可将其附加到程序实体，以提供指向关联文档的链接。

```csharp
using System;

public class HelpAttribute: Attribute
{
    string url;
    string topic;

    public HelpAttribute(string url) {
        this.url = url;
    }

    public string Url {
        get { return url; }
    }

    public string Topic {
        get { return topic; }
        set { topic = value; }
    }
}
```
所有特性类均派生自 `System.Attribute` .NET Framework 提供的基类。 特性的应用方式为，在相关声明前的方括号内指定特性的名称以及任意自变量。 如果特性的名称以结尾 `Attribute` ，则引用该属性时可以省略此部分名称。 例如，可按如下方法使用 `HelpAttribute` 特性。

```csharp
[Help("http://msdn.microsoft.com/.../MyClass.htm")]
public class Widget
{
    [Help("http://msdn.microsoft.com/.../MyClass.htm", Topic = "Display")]
    public void Display(string text) {}
}
```
此示例将附加 `HelpAttribute` 到 `Widget` 类，并将另一类附加 `HelpAttribute` 到 `Display` 类中的方法。 特性类的公共构造函数控制了将特性附加到程序实体时必须提供的信息。 可以通过引用特性类的公共读写属性（如上面示例对 `Topic` 属性的引用），提供其他信息。

下面的示例演示如何使用反射在运行时检索给定程序实体的属性信息。

```csharp
using System;
using System.Reflection;

class Test
{
    static void ShowHelp(MemberInfo member) {
        HelpAttribute a = Attribute.GetCustomAttribute(member,
            typeof(HelpAttribute)) as HelpAttribute;
        if (a == null) {
            Console.WriteLine("No help for {0}", member);
        }
        else {
            Console.WriteLine("Help for {0}:", member);
            Console.WriteLine("  Url={0}, Topic={1}", a.Url, a.Topic);
        }
    }

    static void Main() {
        ShowHelp(typeof(Widget));
        ShowHelp(typeof(Widget).GetMethod("Display"));
    }
}
```
通过反射请求获得特定特性时，将调用特性类的构造函数（由程序源提供信息），并返回生成的特性实例。 如果是通过属性提供其他信息，那么在特性实例返回前，这些属性会设置为给定值。

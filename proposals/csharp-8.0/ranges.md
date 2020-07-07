---
ms.openlocfilehash: bee562d64c402e645879b7811cc82c971d8b61cc
ms.sourcegitcommit: 9b736b5b73321f18a04e02d26596388f7ceb42fc
ms.contentlocale: zh-CN
ms.lasthandoff: 07/07/2020
ms.locfileid: "86052202"
---
# <a name="ranges"></a>范围

## <a name="summary"></a>总结

此功能与提供两个新的运算符，它们允许构造 `System.Index` 和 `System.Range` 对象，并使用它们在运行时索引/切片集合。

## <a name="overview"></a>概述

### <a name="well-known-types-and-members"></a>已知类型和成员

若要将新的句法形式用于 `System.Index` 和 `System.Range` ，可能需要新的已知类型和成员，具体取决于所使用的语法格式。

若要使用 "hat" 运算符（ `^` ），则需要以下项

```csharp
namespace System
{
    public readonly struct Index
    {
        public Index(int value, bool fromEnd);
    }
}
```

若要 `System.Index` 在数组元素访问中使用类型作为参数，需要以下成员：

```csharp
int System.Index.GetOffset(int length);
```

的 `..` 语法 `System.Range` 需要该 `System.Range` 类型，以及以下一个或多个成员：

```csharp
namespace System
{
    public readonly struct Range
    {
        public Range(System.Index start, System.Index end);
        public static Range StartAt(System.Index start);
        public static Range EndAt(System.Index end);
        public static Range All { get; }
    }
}
```

`..`语法允许不存在它的任何一个、两个或不存在任何参数。 无论参数的数量如何， `Range` 构造函数始终都足以使用 `Range` 语法。 但是，如果存在任何其他成员并且缺少一个或多个 `..` 参数，则可以替换相应的成员。

最后，对于 `System.Range` 要在数组元素访问表达式中使用的类型值，必须存在以下成员：

```csharp
namespace System.Runtime.CompilerServices
{
    public static class RuntimeHelpers
    {
        public static T[] GetSubArray<T>(T[] array, System.Range range);
    }
}
```

### <a name="systemindex"></a>System.object

C # 无法从末尾开始为集合编制索引，而是大多数索引器使用 "从开始" 概念，或执行 "长度-i" 表达式。 我们引入了一个表示 "从末尾" 的新索引表达式。 此功能将引入一个新的一元前缀 "hat" 运算符。 其单个操作数必须可以转换为 `System.Int32` 。 它将降低为适当的 `System.Index` 工厂方法调用。

我们通过以下附加语法形式补充*unary_expression*的语法：

```antlr
unary_expression
    : '^' unary_expression
    ;
```

我们*从 end*运算符将此索引称为索引。 最终运算符*中*的预定义索引如下所示：

```csharp
System.Index operator ^(int fromEnd);
```

仅为大于或等于零的输入值定义此运算符的行为。

示例：

```csharp
var array = new int[] { 1, 2, 3, 4, 5 };
var thirdItem = array[2];    // array[2]
var lastItem = array[^1];    // array[new Index(1, fromEnd: true)]
```

#### <a name="systemrange"></a>System.object

C # 没有语法方法来访问集合的 "范围" 或 "切片"。 通常，用户被迫实现复杂的结构来筛选/操作内存的切片，或利用 LINQ 方法（如） `list.Skip(5).Take(2)` 。 如果添加了 `System.Span<T>` 和其他类似类型，则更重要的是在语言/运行时中的更深级别支持此类操作，并使接口具有统一的。

语言会引入新的范围运算符 `x..y` 。 它是一个接受两个表达式的二元中缀运算符。 可以省略任一操作数（下面的示例），并且它们必须可转换为 `System.Index` 。 它将降低到适当的 `System.Range` 工厂方法调用。

我们将*multiplicative_expression*的 c # 语法规则替换为以下内容（以便引入新的优先级别）：

```antlr
range_expression
    : unary_expression
    | range_expression? '..' range_expression?
    ;

multiplicative_expression
    : range_expression
    | multiplicative_expression '*' range_expression
    | multiplicative_expression '/' range_expression
    | multiplicative_expression '%' range_expression
    ;
```

所有形式的*range 运算符*都具有相同的优先级。 此新优先级组低于*一元运算符*，大于*乘法算术运算符*。

我们 `..` 将运算符称为*范围运算符*。 可以大致理解内置范围运算符，使其与此窗体的内置运算符的调用相对应：

```csharp
System.Range operator ..(Index start = 0, Index end = ^0);
```

示例：

```csharp
var array = new int[] { 1, 2, 3, 4, 5 };
var slice1 = array[2..^3];    // array[new Range(2, new Index(3, fromEnd: true))]
var slice2 = array[..^3];     // array[Range.EndAt(new Index(3, fromEnd: true))]
var slice3 = array[2..];      // array[Range.StartAt(2)]
var slice4 = array[..];       // array[Range.All]
```

此外， `System.Index` 应从进行隐式转换 `System.Int32` ，以避免对多维签名重载混合整数和索引的需求。

## <a name="adding-index-and-range-support-to-existing-library-types"></a>向现有库类型添加索引和范围支持

### <a name="implicit-index-support"></a>隐式索引支持

该语言将为满足以下条件的类型提供具有单个类型参数的实例索引器成员 `Index` ：

- 类型为可数。
- 该类型具有可访问的实例索引器，该索引器采用单个 `int` 作为参数。
- 该类型没有可访问的实例索引器，该索引器采用 `Index` 作为第一个参数。 `Index`必须是唯一的参数，否则剩余的参数必须是可选的。

如果某个类型***Countable***具有一个名为的属性 `Length` 或一个 `Count` 具有可访问 getter 的属性和一个返回类型，则该类型为可数 `int` 。 语言可以利用此属性将类型的表达式转换为 `Index` `int` 表达式点的，而无需全部使用该类型 `Index` 。 如果同时 `Length` 存在和 `Count` ， `Length` 则优先。 为方便起见，该建议将使用名称 `Length` 来表示 `Count` 或 `Length` 。

对于此类类型，语言将充当格式的索引器成员， `T this[Index index]` 其中， `T` 是 `int` 基于索引器的返回类型（包括任何 `ref` 样式批注）。 新成员将具有 `get` `set` 与索引器匹配的可访问性的和成员 `int` 。

新的索引器将通过将类型的参数转换 `Index` 为 `int` 并发出对基于索引器的调用来实现 `int` 。 出于讨论目的，我们使用的示例 `receiver[expr]` 。 将转换为，如下所示 `expr` `int` ：

- 如果参数的格式为 `^expr2` ，并且的类型 `expr2` 为，则 `int` 它将转换为 `receiver.Length - expr2` 。
- 否则，它将转换为 `expr.GetOffset(receiver.Length)` 。

这允许开发人员 `Index` 在现有类型上使用该功能，而无需修改。 例如：

``` csharp
List<char> list = ...;
var value = list[^1];

// Gets translated to
var value = list[list.Count - 1];
```

`receiver`和 `Length` 表达式会被适当地溅入，以确保任何副作用仅执行一次。 例如：

``` csharp
class Collection {
    private int[] _array = new[] { 1, 2, 3 };

    int Length {
        get {
            Console.Write("Length ");
            return _array.Length;
        }
    }

    int this[int index] => _array[index];
}

class SideEffect {
    Collection Get() {
        Console.Write("Get ");
        return new Collection();
    }

    void Use() {
        int i = Get()[^1];
        Console.WriteLine(i);
    }
}
```

此代码将打印 "获取长度 3"。

### <a name="implicit-range-support"></a>隐式范围支持

该语言将为满足以下条件的类型提供具有单个类型参数的实例索引器成员 `Range` ：

- 类型为可数。
- 该类型具有一个名为的可访问成员 `Slice` ，它具有两个类型为的参数 `int` 。
- 该类型没有将单个 `Range` 作为第一个参数的实例索引器。 `Range`必须是唯一的参数，否则剩余的参数必须是可选的。

对于这种类型的情况，语言将绑定为，因为该窗体的索引器成员 `T this[Range range]` `T` 是 `Slice` 包含任何样式批注的方法的返回类型 `ref` 。 新成员还将具有匹配的可访问性 `Slice` 。

在 `Range` 名为的表达式上绑定基于的索引器时 `receiver` ，将表达式转换为 `Range` 两个值，然后将该表达式转换为方法，这会降低 `Slice` 。 出于讨论目的，我们使用的示例 `receiver[expr]` 。

`Slice`将通过以下方式转换范围类型化表达式来获取的第一个参数：

- 当 `expr` 的形式为 `expr1..expr2` （ `expr2` 可以省略的位置）并且 `expr1` 具有类型时 `int` ，它将作为发出 `expr1` 。
- 当 `expr` 的形式为 `^expr1..expr2` （ `expr2` 可以省略的位置）时，它将作为发出 `receiver.Length - expr1` 。
- 当 `expr` 的形式为 `..expr2` （ `expr2` 可以省略的位置）时，它将作为发出 `0` 。
- 否则，它将作为发出 `expr.Start.GetOffset(receiver.Length)` 。

此值将在第二个参数的计算中重复使用 `Slice` 。 执行此操作时，它将被称为 `start` 。 `Slice`将通过以下方式转换范围类型化表达式来获取的第二个参数：

- 当 `expr` 的形式为 `expr1..expr2` （ `expr1` 可以省略的位置）并且 `expr2` 具有类型时 `int` ，它将作为发出 `expr2 - start` 。
- 当 `expr` 的形式为 `expr1..^expr2` （ `expr1` 可以省略的位置）时，它将作为发出 `(receiver.Length - expr2) - start` 。
- 当 `expr` 的形式为 `expr1..` （ `expr1` 可以省略的位置）时，它将作为发出 `receiver.Length - start` 。
- 否则，它将作为发出 `expr.End.GetOffset(receiver.Length) - start` 。

将根据需要将 `receiver` 、 `Length` 和 `expr` 表达式溢出，以确保只执行一次副作用。 例如：

``` csharp
class Collection {
    private int[] _array = new[] { 1, 2, 3 };

    int Length {
        get {
            Console.Write("Length ");
            return _array.Length;
        }
    }

    int[] Slice(int start, int length) {
        var slice = new int[length];
        Array.Copy(_array, start, slice, 0, length);
        return slice;
    }
}

class SideEffect {
    Collection Get() {
        Console.Write("Get ");
        return new Collection();
    }

    void Use() {
        var array = Get()[0..2];
        Console.WriteLine(array.length);
    }
}
```

此代码将打印 "获取长度 2"。

此语言将用以下已知类型作为特例：

- `string`： `Substring` 将使用（而不是）方法 `Slice` 。
- `array`： `System.Reflection.CompilerServices.GetSubArray` 将使用（而不是）方法 `Slice` 。

## <a name="alternatives"></a>备选方法

新运算符（ `^` 和 `..` ）是句法糖。 此功能可以通过对和工厂方法的显式调用来实现 `System.Index` `System.Range` ，但它会导致更多样板代码，并将 unintuitive。

## <a name="il-representation"></a>IL 表示形式

这两个运算符将降低为常规索引器/方法调用，后续编译器层不会更改。

## <a name="runtime-behavior"></a>运行时行为

- 编译器可以优化内置类型（如数组和字符串）的索引器，并将索引的索引减小到适当的现有方法。
- `System.Index`如果用负值构造，将引发。
- `^0`不会引发，但会转换为其提供的集合/可枚举的长度。
- `Range.All`在语义上等效于 `0..^0` ，可以析构这些索引。

## <a name="considerations"></a>注意事项

### <a name="detect-indexable-based-on-icollection"></a>基于 ICollection 检测可索引

此行为的灵感是集合初始值设定项。 使用类型的结构来表明它已选择加入功能。 如果集合初始值设定项类型可通过实现接口 `IEnumerable` （非泛型）来选择功能。

这一建议最初要求类型实现为 `ICollection` 可编制索引。 但这需要一些特殊情况：

- `ref struct`：这些类无法实现接口，如的类型 `Span<T>` 是索引/范围支持的理想选择。
- `string`：不实现 `ICollection` 并添加 `interface` 具有较大开销的。

这意味着支持已需要的密钥类型特殊大小写。 的特殊大小写不 `string` 太重要，因为该语言在其他区域（ `foreach` 降低、常量等）中执行此功能。的特殊大小写 `ref struct` 更多，因为它是整个类型类的特殊大小写。 如果它们只具有一个名为且返回类型为的属性，则它们将被标记为可索引 `Count` `int` 。

考虑到该设计已规范化为，表示具有 `Count`  /  `Length` 返回类型为的属性的任何类型都可以 `int` 编制索引。 这将删除所有特殊大小写，即使对于和数组也是如此 `string` 。

### <a name="detect-just-count"></a>仅检测计数

检测属性名称或对 `Count` `Length` 设计有点复杂。 只需选择一项进行标准化即可实现标准化，因为它最终会排除大量类型：

- 使用 `Length` ：排除 system.web 和子命名空间中的每个集合。 它们往往派生自 `ICollection` ，因此更喜欢 `Count` 超出长度。
- 使用 `Count` ：不包括 `string` 、数组以及 `Span<T>` 基于大多数的 `ref struct` 类型

在其他方面，对可编制索引的类型的初始检测的额外问题在有点简化。

### <a name="choice-of-slice-as-a-name"></a>将切片选为名称

`Slice`已选择名称，因为它是 .net 中切片样式操作的实际标准名称。 从 netcoreapp 2.1 开始，所有范围样式类型使用 `Slice` 切片操作的名称。 在 netcoreapp 2.1 之前，没有任何关于示例的切片的示例。 类似于的类型 `List<T>` `ArraySegment<T>` `SortedList<T>` 非常适合用于切片，但在添加类型时该概念不存在。

因此， `Slice` 作为一个唯一的示例，它被选为名称。

### <a name="index-target-type-conversion"></a>索引目标类型转换

`Index`在索引器表达式中查看转换的另一种方法是作为目标类型转换。 此语言不是像窗体的成员那样绑定，而是将 `return_type this[Index]` 目标类型化转换分配到 `int` 。

此概念可以通用化到对可数类型的所有成员访问。 只要使用类型的表达式 `Index` 作为实例成员调用的参数并且接收方可数，该表达式就会将目标类型转换为 `int` 。 适用于此转换的成员调用包括方法、索引器、属性、扩展方法等。仅排除构造函数，因为它们没有接收方。

对于具有类型的任何表达式，将按如下所示实现目标类型转换 `Index` 。 出于讨论目的，可使用以下示例 `receiver[expr]` ：

- 如果 `expr` 为形式， `^expr2` 并且的类型 `expr2` 为 `int` ，则它将转换为 `receiver.Length - expr2` 。
- 否则，它将转换为 `expr.GetOffset(receiver.Length)` 。

`receiver`和 `Length` 表达式会被适当地溅入，以确保任何副作用仅执行一次。 例如：

``` csharp
class Collection {
    private int[] _array = new[] { 1, 2, 3 };

    int Length {
        get {
            Console.Write("Length ");
            return _array.Length;
        }
    }

    int GetAt(int index) => _array[index];
}

class SideEffect {
    Collection Get() {
        Console.Write("Get ");
        return new Collection();
    }

    void Use() {
        int i = Get().GetAt(^1);
        Console.WriteLine(i);
    }
}
```

此代码将打印 "获取长度 3"。

对于具有表示索引的参数的任何成员，此功能将非常有用。 例如，`List<T>.InsertAt`。 这也可能会造成混淆，因为该语言不能提供任何有关表达式是否用于索引的指导。 在 `Index` `int` 调用可数类型的成员时，它可以将任何表达式转换为。

限制：

- 仅当类型为的表达式直接是成员的参数时，此转换才适用 `Index` 。 它不适用于任何嵌套表达式。

## <a name="decisions-made-during-implementation"></a>在实现过程中做出的决策

- 模式中的所有成员都必须是实例成员
- 如果找到长度方法但其返回类型错误，则继续查找计数
- 用于索引模式的索引器必须正好有一个 int 参数
- 用于范围模式的切片方法必须正好有两个 int 参数
- 查找模式成员时，查找原始定义，而不是构造成员

## <a name="design-meetings"></a>设计会议

- [2018年1月10日](https://github.com/dotnet/csharplang/blob/master/meetings/2018/LDM-2018-01-10.md)
- [1月18日，2018](https://github.com/dotnet/csharplang/blob/master/meetings/2018/LDM-2018-01-18.md)
- [2018年1月22日](https://github.com/dotnet/csharplang/blob/master/meetings/2018/LDM-2018-01-22.md)
- [12月3，2018](https://github.com/dotnet/csharplang/blob/master/meetings/2018/LDM-2018-12-03.md)
- [3月25日，2019](https://github.com/dotnet/csharplang/blob/master/meetings/2019/LDM-2019-03-25.md#pattern-based-indexing-with-index-and-range)
- [4月1日，2019](https://github.com/dotnet/csharplang/blob/master/meetings/2019/LDM-2019-04-01.md)
- [2019 年 4 月 15 日](https://github.com/dotnet/csharplang/blob/master/meetings/2019/LDM-2019-04-15.md#follow-up-decisions-for-pattern-based-indexrange)

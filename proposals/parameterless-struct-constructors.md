---
ms.openlocfilehash: d5eeb2501d4e99136911cb1fa2153ff7057432b3
ms.sourcegitcommit: 547b1d0991d7557cd658937d1d730a1c91ff1571
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/05/2021
ms.locfileid: "102236358"
---
# <a name="parameterless-struct-constructors"></a>无参数结构构造函数

## <a name="summary"></a>总结

支持结构类型的无参数构造函数和实例字段初始值设定项。

## <a name="proposal"></a>建议

### <a name="instance-field-initializers"></a>实例字段初始值设定项
结构的实例字段声明可以包含初始值设定项。

与 [类字段初始值设定项](https://github.com/dotnet/csharplang/blob/master/spec/classes.md#instance-field-initialization)一样：
> 实例字段的变量初始值设定项不能引用正在创建的实例。 

### <a name="constructors"></a>构造函数
结构可声明无参数的实例构造函数。
无参数的实例构造函数对所有结构类型有效 `struct` ，包括、 `readonly struct` 、 `ref struct` 和 `record struct` 。

如果该结构未声明无参数的实例构造函数，并且该结构没有包含变量初始值设定项的字段，则结构 (参见 [结构构造函数](https://github.com/dotnet/csharplang/blob/master/spec/structs.md#constructors)) .。。
> 隐式具有一个无参数的实例构造函数，该构造函数始终返回通过将所有值类型字段设置为其默认值，并将所有引用类型字段设置为 null 而产生的值。

如果结构未声明无参数的实例构造函数，并且该结构具有字段初始值设定项， `public` 则会合成无参数的实例构造函数。
无参数构造函数会合成，即使所有初始值设定项的值都为零。

### <a name="modifiers"></a>修饰符
无参数的实例构造函数的可访问性可能低于包含结构。
这对稍后详细介绍的某些情况有影响。

同一组修饰符可用于作为其他构造函数的无参数构造函数： `static` 、 `extern` 和 `unsafe` 。

### <a name="executing-field-initializers"></a>执行字段初始值设定项
结构实例字段初始值设定项的执行与 [类字段初始值设定](https://github.com/dotnet/csharplang/blob/master/spec/classes.md#instance-variable-initializers)项的执行匹配：
> 当实例构造函数没有构造函数初始值设定项时 .。。该构造函数隐式执行实例字段的 _variable_initializers_ 指定的初始化 ...。 这对应于在进入构造函数之后、直接调用直接基类构造函数之前立即执行的一系列赋值。 变量初始值设定项的执行顺序与它们在 .。。声明.

### <a name="definite-assignment"></a>明确赋值
实例字段必须在没有初始值设定项的结构实例构造函数中明确赋值 `this()` (参见 [结构构造函数](https://github.com/dotnet/csharplang/blob/master/spec/structs.md#constructors)) 。

在显式无参数的构造函数中，还需要显式分配实例字段。
合成无参数的构造函数中 _不需要_ 实例字段的明确分配。

_是否应一致地应用明确的分配规则？也就是说，如果需要显式分配结构的所有实例字段（即使未合成无参数的构造函数），或者是否应该删除在结构构造函数中显式分配了所有字段的现有要求，_
```csharp
struct S0
{
    object x = null;
    object y;
    // ok
}

struct S1
{
    object x = null;
    object y;
    S() { } // error: field 'y' must be assigned
}

struct S2
{
    object x = null;
    object y;
    S() : this(null) { }        // ok
    S(object y) { this.y = y; } // ok
}
```

_合成无参数的构造函数可能需要 `ldarg.0 initobj S` 在任何字段初始值设定项之前发出。描述具体内容。如何在 Visual Basic 中处理此问题？_

### <a name="no-base-initializer"></a>没有 `base()` 初始值设定项
`base()`结构构造函数中不允许使用初始值设定项。

编译器不会 `System.ValueType` 从包含显式无参数构造函数的任何结构实例构造函数中发出对基构造函数的调用。

### <a name="constructor-use"></a>构造函数使用

无参数的构造函数比包含值类型的可访问性低。
```csharp
public struct NoConstructor { }
public struct PublicConstructor { public PublicConstructor() { } }
public struct InternalConstructor { internal InternalConstructor() { } }
public struct PrivateConstructor { private PrivateConstructor() { } }
```

`default` 忽略无参数构造函数并生成一个零的实例。
```csharp
_ = default(NoConstructor);      // ok
_ = default(PublicConstructor);  // ok: constructor ignored
_ = default(PrivateConstructor); // ok: constructor ignored
```

对象创建表达式要求可访问的无参数构造函数（如果已定义）。
无参数构造函数是显式调用的。
_如果带参数构造函数的结构类型来自于现有程序集，则这是一项重大更改。_
```csharp
_ = new NoConstructor();       // ok: initobj NoConstructor
_ = new InternalConstructor(); // ok: call InternalConstructor::.ctor()
_ = new PrivateConstructor();  // error: 'PrivateConstructor..ctor()' is inaccessible
```

未显式初始化的结构类型的局部或字段归零。
对于未初始化的非空结构，编译器将报告明确赋值错误。 
```csharp
NoConstructor s1;
PublicConstructor s2;
s1.ToString(); // error: use of unassigned local (unless type is empty)
s2.ToString(); // error: use of unassigned local (unless type is empty)
```

数组分配将忽略任何无参数的构造函数并生成零个元素。
_编译器是否应警告无参数构造函数被忽略？如何避免此类警告？_
```csharp
_ = new NoConstructor[1];      // ok
_ = new PublicConstructor[1];  // ok: constructor ignored
_ = new PrivateConstructor[1]; // ok: constructor ignored
```

无参数构造函数不能用作参数的默认值。
_如果带参数构造函数的结构类型来自于现有程序集，则这是一项重大更改。_
```csharp
static void F1(NoConstructor s1 = new()) { }     // ok
static void F2(PublicConstructor s1 = new()) { } // error: default value must be constant
```

### <a name="constraints"></a>约束
`new()`如果定义了类型参数，则该约束要求参数构造函数 `public` (参阅) [满足约束](https://github.com/dotnet/csharplang/blob/master/spec/types.md#satisfying-constraints)。
```csharp
static T CreateNew<T>() where T : new() => new T();

_ = CreateNew<NoConstructor>();       // ok
_ = CreateNew<PublicConstructor>();   // ok
_ = CreateNew<InternalConstructor>(); // error: 'InternalConstructor..ctor()' is not public
_ = CreateNew<PrivateConstructor>();  // error: 'PrivateConstructor..ctor()' is not public
```

`new T()` 作为对的调用发出 `System.Activator.CreateInstance<T>()` ，并且编译器假定的实现将 `CreateInstance<T>()` 调用 `public` 无参数的构造函数（如果已定义）。

类型参数约束检查存在间隔，因为 `new()` 具有约束的类型参数满足约束 `struct` (请参阅 [满足约束](https://github.com/dotnet/csharplang/blob/master/spec/types.md#satisfying-constraints)) 。

因此，编译器将允许以下项，但 `Activator.CreateInstance<InternalConstructor>()` 在运行时将失败。
此问题不是由本提案引入的-如果在元数据中具有不可访问的无参数构造函数的结构类型，则 c # 9 中会出现此问题。
```csharp
static T CreateNew<T>() where T : new() => new T();
static T CreateStruct<T>() where T : struct => CreateNew<T>();

_ = CreateStruct<InternalConstructor>(); // compiles; 'MissingMethodException' at runtime
```

### <a name="metadata"></a>元数据
显式和合成无参数结构实例构造函数将发送到元数据。

无参数结构实例构造函数将从元数据导入。
_这是对具有无参数构造函数的结构的现有程序集的使用者的重大更改。_

无参数结构实例构造函数将发出到 ref 程序集，而不考虑允许使用者区分无参数构造函数的构造函数。

## <a name="see-also"></a>请参阅

- https://github.com/dotnet/roslyn/issues/1029

## <a name="design-meetings"></a>设计会议

- https://github.com/dotnet/csharplang/blob/master/meetings/2021/LDM-2021-01-27.md#field-initializers

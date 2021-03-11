---
ms.openlocfilehash: aaf11b188c4622d50e86f757c24eb5cd934f8909
ms.sourcegitcommit: 5b6f535beed62156b5239035e1829b001886b0b4
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/10/2021
ms.locfileid: "102622480"
---
# <a name="parameterless-struct-constructors"></a>无参数结构构造函数

## <a name="summary"></a>总结
支持结构类型的无参数构造函数和实例字段初始值设定项。

## <a name="motivation"></a>动机
显式无参数构造函数可以更好地控制结构类型的最小构造实例。
实例字段初始值设定项允许跨多个构造函数的简化初始化。
它们共同会关闭和声明之间的明显差距 `struct` `class` 。

对字段初始值设定项的支持还允许在声明中初始化字段， `record struct` 而无需显式实现主构造函数。
```csharp
record struct Person(string Name)
{
    public object Id { get; init; } = GetNextId();
}
```

如果结构字段初始值设定项支持具有参数的构造函数，则将其扩展到无参数构造函数似乎很自然。
```csharp
record struct Person()
{
    public string Name { get; init; }
    public object Id { get; init; } = GetNextId();
}
```

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
```csharp
public struct NoConstructor { }
public struct PublicConstructor { public PublicConstructor() { } }
public struct InternalConstructor { internal InternalConstructor() { } }
public struct PrivateConstructor { private PrivateConstructor() { } }
```

同一组修饰符可用于作为其他实例构造函数的无参数构造函数： `extern` 、和 `unsafe` 。

构造函数不能为 `partial` 。

### <a name="executing-field-initializers"></a>执行字段初始值设定项
结构实例字段初始值设定项的执行与 [类字段初始值设定](https://github.com/dotnet/csharplang/blob/master/spec/classes.md#instance-variable-initializers)项的执行匹配：
> 当实例构造函数没有构造函数初始值设定项时 .。。该构造函数隐式执行实例字段的 _variable_initializers_ 指定的初始化 ...。 这对应于在进入构造函数之后立即执行的一系列赋值。 变量初始值设定项的执行顺序与它们在 .。。声明.

### <a name="definite-assignment"></a>明确赋值
实例字段必须在没有初始值设定项的结构实例构造函数中明确赋值 `this()` (参见 [结构构造函数](https://github.com/dotnet/csharplang/blob/master/spec/structs.md#constructors)) 。

在显式无参数的构造函数中，还需要显式分配实例字段。
```csharp
struct S1
{
    int x = 1;
    object y;
    S() { } // error: field 'y' must be assigned
}

struct S2
{
    int x = 2;
    object y;
    S() : this(null) { }        // ok
    S(object y) { this.y = y; } // ok
}
```

_是否应在合成无参数构造函数中明确分配结构实例字段？_ 
_如果是这样，则在任何实例字段都有初始值设定项时，所有实例字段必须有初始值设定项。_
```csharp
struct S0
{
    int x = 0;
    object y;
    // ok?
}
```

如果未显式初始化字段，则在执行任何字段初始值设定项之前，构造函数将需要为该实例的零。
```
.class S0 extends System.ValueType
{
    .field int32 x
    .field object y
    .method public instance void .ctor()
    {
        ldarg.0
        initobj S0
        ldarg.0
        ldc.i4.0
        stfld int32 S0::x
        ret
    }
}
```

### <a name="no-base-initializer"></a>没有 `base()` 初始值设定项
`base()`结构构造函数中不允许使用初始值设定项。

编译器不会 `System.ValueType` 从包含显式无参数构造函数的任何结构实例构造函数中发出对基构造函数的调用。

### <a name="fields"></a>字段
合成无参数构造函数将为零个字段，而不是为字段类型调用任何无参数的构造函数。

_当忽略字段的构造函数时，编译器是否应报告警告？_
```csharp
struct S0
{
    public S0() { }
}

struct S1
{
    S0 F; // S0::.ctor() ignored
}

struct S<T> where T : struct
{
    T F; // constructor ignored
}
```

### <a name="default-expression"></a>`default` 表达式
`default` 忽略无参数构造函数并生成一个零的实例。

_当忽略构造函数时，编译器是否应报告警告？_
```csharp
_ = default(NoConstructor);      // ok
_ = default(PublicConstructor);  // ok: constructor ignored
_ = default(PrivateConstructor); // ok: constructor ignored
```

### <a name="object-creation"></a>对象创建
对象创建表达式要求可访问的无参数构造函数（如果已定义）。
无参数构造函数是显式调用的。

_如果带参数构造函数的结构类型来自于现有程序集，则这是一项重大更改。_ 
_`new()` 如果构造函数不可访问 `initobj` ，则编译器是否应报告警告，而不是为兼容性发出错误？_
```csharp
_ = new NoConstructor();      // ok: initobj NoConstructor
_ = new PublicConstructor();  // ok: call PublicConstructor::.ctor()
_ = new PrivateConstructor(); // error: 'PrivateConstructor..ctor()' is inaccessible
```

### <a name="uninitialized-values"></a>未初始化值
未显式初始化的结构类型的局部或字段归零。
对于未初始化的非空结构，编译器将报告明确赋值错误。 
```csharp
NoConstructor s1;
PublicConstructor s2;
s1.ToString(); // error: use of unassigned local (unless type is empty)
s2.ToString(); // error: use of unassigned local (unless type is empty)
```

### <a name="array-allocation"></a>数组分配
数组分配将忽略任何无参数的构造函数并生成零个元素。

_编译器是否应警告无参数构造函数被忽略？如何避免此类警告？_
```csharp
_ = new NoConstructor[1];      // ok
_ = new PublicConstructor[1];  // ok: constructor ignored
_ = new PrivateConstructor[1]; // ok: constructor ignored
```

### <a name="parameter-default-values"></a>参数默认值
无参数构造函数不能用作参数的默认值。

_如果带参数构造函数的结构类型来自于现有程序集，则这是一项重大更改。_ 
_`new()` 如果构造函数不可访问 `default` ，则编译器是否应报告警告，而不是为兼容性发出错误？_
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
```
_如果将具有非公共构造函数的结构替换为具有约束的类型参数，编译器将报告警告而不是错误 `new()` ，以确定是否实际实例化了类型？_

`new T()` 作为对的调用发出 `System.Activator.CreateInstance<T>()` ，并且编译器假定的实现将 `CreateInstance<T>()` 调用 `public` 无参数的构造函数（如果已定义）。

_如果有 .NET Framework，则 `Activator.CreateInstance<T>()` 调用无参数的构造函数，如果约束为，则将调用无 `where T : new()` 参数构造函数 `where T : struct` 。_

类型参数约束检查存在间隔，因为 `new()` 具有约束的类型参数满足约束 `struct` (请参阅 [满足约束](https://github.com/dotnet/csharplang/blob/master/spec/types.md#satisfying-constraints)) 。

因此，编译器将允许以下项，但 `Activator.CreateInstance<InternalConstructor>()` 在运行时将失败。
此问题不是由本提案引入的-如果在元数据中具有不可访问的无参数构造函数的结构类型，则 c # 9 中会出现此问题。
```csharp
static T CreateNew<T>() where T : new() => new T();
static T CreateStruct<T>() where T : struct => CreateNew<T>();

_ = CreateStruct<InternalConstructor>(); // compiles; 'MissingMethodException' at runtime
```

### <a name="optional-parameters"></a>可选参数
具有可选参数的构造函数不被视为无参数的构造函数。 此行为与早期版本的编译器版本保持不变。
```csharp
struct S1 { public S1(string s = "") { } }
struct S2 { public S2(params object[] args) { } }

_ = new S1(); // ok: ignores constructor
_ = new S2(); // ok: ignores constructor
```

### <a name="metadata"></a>元数据
显式和合成无参数结构实例构造函数将发送到元数据。

无参数结构实例构造函数将从元数据中导入，而不考虑可访问性。
_如果报告了其他错误或警告，则这可能是使用具有私有无参数构造函数的结构的现有程序集的使用者的重大更改。_

无参数结构实例构造函数将发出到 ref 程序集，而不考虑允许使用者区分无参数构造函数的构造函数。

## <a name="see-also"></a>另请参阅

- https://github.com/dotnet/roslyn/issues/1029

## <a name="design-meetings"></a>设计会议

- https://github.com/dotnet/csharplang/blob/master/meetings/2021/LDM-2021-01-27.md#field-initializers

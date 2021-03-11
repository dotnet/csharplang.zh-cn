---
ms.openlocfilehash: aaf11b188c4622d50e86f757c24eb5cd934f8909
ms.sourcegitcommit: 5b6f535beed62156b5239035e1829b001886b0b4
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/10/2021
ms.locfileid: "102622480"
---
# <a name="parameterless-struct-constructors"></a><span data-ttu-id="40564-101">无参数结构构造函数</span><span class="sxs-lookup"><span data-stu-id="40564-101">Parameterless struct constructors</span></span>

## <a name="summary"></a><span data-ttu-id="40564-102">总结</span><span class="sxs-lookup"><span data-stu-id="40564-102">Summary</span></span>
<span data-ttu-id="40564-103">支持结构类型的无参数构造函数和实例字段初始值设定项。</span><span class="sxs-lookup"><span data-stu-id="40564-103">Support parameterless constructors and instance field initializers for struct types.</span></span>

## <a name="motivation"></a><span data-ttu-id="40564-104">动机</span><span class="sxs-lookup"><span data-stu-id="40564-104">Motivation</span></span>
<span data-ttu-id="40564-105">显式无参数构造函数可以更好地控制结构类型的最小构造实例。</span><span class="sxs-lookup"><span data-stu-id="40564-105">Explicit parameterless constructors would give more control over minimally constructed instances of the struct type.</span></span>
<span data-ttu-id="40564-106">实例字段初始值设定项允许跨多个构造函数的简化初始化。</span><span class="sxs-lookup"><span data-stu-id="40564-106">Instance field initializers would allow simplified initialization across multiple constructors.</span></span>
<span data-ttu-id="40564-107">它们共同会关闭和声明之间的明显差距 `struct` `class` 。</span><span class="sxs-lookup"><span data-stu-id="40564-107">Together these would close an obvious gap between `struct` and `class` declarations.</span></span>

<span data-ttu-id="40564-108">对字段初始值设定项的支持还允许在声明中初始化字段， `record struct` 而无需显式实现主构造函数。</span><span class="sxs-lookup"><span data-stu-id="40564-108">Support for field initializers would also allow initialization of fields in `record struct` declarations without explicitly implementing the primary constructor.</span></span>
```csharp
record struct Person(string Name)
{
    public object Id { get; init; } = GetNextId();
}
```

<span data-ttu-id="40564-109">如果结构字段初始值设定项支持具有参数的构造函数，则将其扩展到无参数构造函数似乎很自然。</span><span class="sxs-lookup"><span data-stu-id="40564-109">If struct field initializers are supported for constructors with parameters, it seems natural to extend that to parameterless constructors as well.</span></span>
```csharp
record struct Person()
{
    public string Name { get; init; }
    public object Id { get; init; } = GetNextId();
}
```

## <a name="proposal"></a><span data-ttu-id="40564-110">建议</span><span class="sxs-lookup"><span data-stu-id="40564-110">Proposal</span></span>

### <a name="instance-field-initializers"></a><span data-ttu-id="40564-111">实例字段初始值设定项</span><span class="sxs-lookup"><span data-stu-id="40564-111">Instance field initializers</span></span>
<span data-ttu-id="40564-112">结构的实例字段声明可以包含初始值设定项。</span><span class="sxs-lookup"><span data-stu-id="40564-112">Instance field declarations for a struct may include initializers.</span></span>

<span data-ttu-id="40564-113">与 [类字段初始值设定项](https://github.com/dotnet/csharplang/blob/master/spec/classes.md#instance-field-initialization)一样：</span><span class="sxs-lookup"><span data-stu-id="40564-113">As with [class field initializers](https://github.com/dotnet/csharplang/blob/master/spec/classes.md#instance-field-initialization):</span></span>
> <span data-ttu-id="40564-114">实例字段的变量初始值设定项不能引用正在创建的实例。</span><span class="sxs-lookup"><span data-stu-id="40564-114">A variable initializer for an instance field cannot reference the instance being created.</span></span> 

### <a name="constructors"></a><span data-ttu-id="40564-115">构造函数</span><span class="sxs-lookup"><span data-stu-id="40564-115">Constructors</span></span>
<span data-ttu-id="40564-116">结构可声明无参数的实例构造函数。</span><span class="sxs-lookup"><span data-stu-id="40564-116">A struct may declare a parameterless instance constructor.</span></span>

<span data-ttu-id="40564-117">无参数的实例构造函数对所有结构类型有效 `struct` ，包括、 `readonly struct` 、 `ref struct` 和 `record struct` 。</span><span class="sxs-lookup"><span data-stu-id="40564-117">A parameterless instance constructor is valid for all struct kinds including `struct`, `readonly struct`, `ref struct`, and `record struct`.</span></span>

<span data-ttu-id="40564-118">如果该结构未声明无参数的实例构造函数，并且该结构没有包含变量初始值设定项的字段，则结构 (参见 [结构构造函数](https://github.com/dotnet/csharplang/blob/master/spec/structs.md#constructors)) .。。</span><span class="sxs-lookup"><span data-stu-id="40564-118">If the struct does not declare a parameterless instance constructor, and the struct has no fields with variable initializers, the struct (see [struct constructors](https://github.com/dotnet/csharplang/blob/master/spec/structs.md#constructors)) ...</span></span>
> <span data-ttu-id="40564-119">隐式具有一个无参数的实例构造函数，该构造函数始终返回通过将所有值类型字段设置为其默认值，并将所有引用类型字段设置为 null 而产生的值。</span><span class="sxs-lookup"><span data-stu-id="40564-119">implicitly has a parameterless instance constructor which always returns the value that results from setting all value type fields to their default value and all reference type fields to null.</span></span>

<span data-ttu-id="40564-120">如果结构未声明无参数的实例构造函数，并且该结构具有字段初始值设定项， `public` 则会合成无参数的实例构造函数。</span><span class="sxs-lookup"><span data-stu-id="40564-120">If the struct does not declare a parameterless instance constructor, and the struct has field initializers, a `public` parameterless instance constructor is synthesized.</span></span>
<span data-ttu-id="40564-121">无参数构造函数会合成，即使所有初始值设定项的值都为零。</span><span class="sxs-lookup"><span data-stu-id="40564-121">The parameterless constructor is synthesized even if all initializer values are zeros.</span></span>

### <a name="modifiers"></a><span data-ttu-id="40564-122">修饰符</span><span class="sxs-lookup"><span data-stu-id="40564-122">Modifiers</span></span>
<span data-ttu-id="40564-123">无参数的实例构造函数的可访问性可能低于包含结构。</span><span class="sxs-lookup"><span data-stu-id="40564-123">A parameterless instance constructor may be less accessible than the containing struct.</span></span>
```csharp
public struct NoConstructor { }
public struct PublicConstructor { public PublicConstructor() { } }
public struct InternalConstructor { internal InternalConstructor() { } }
public struct PrivateConstructor { private PrivateConstructor() { } }
```

<span data-ttu-id="40564-124">同一组修饰符可用于作为其他实例构造函数的无参数构造函数： `extern` 、和 `unsafe` 。</span><span class="sxs-lookup"><span data-stu-id="40564-124">The same set of modifiers can be used for parameterless constructors as other instance constructors: `extern`, and `unsafe`.</span></span>

<span data-ttu-id="40564-125">构造函数不能为 `partial` 。</span><span class="sxs-lookup"><span data-stu-id="40564-125">Constructors cannot be `partial`.</span></span>

### <a name="executing-field-initializers"></a><span data-ttu-id="40564-126">执行字段初始值设定项</span><span class="sxs-lookup"><span data-stu-id="40564-126">Executing field initializers</span></span>
<span data-ttu-id="40564-127">结构实例字段初始值设定项的执行与 [类字段初始值设定](https://github.com/dotnet/csharplang/blob/master/spec/classes.md#instance-variable-initializers)项的执行匹配：</span><span class="sxs-lookup"><span data-stu-id="40564-127">Execution of struct instance field initializers matches execution of [class field initializers](https://github.com/dotnet/csharplang/blob/master/spec/classes.md#instance-variable-initializers):</span></span>
> <span data-ttu-id="40564-128">当实例构造函数没有构造函数初始值设定项时 .。。该构造函数隐式执行实例字段的 _variable_initializers_ 指定的初始化 ...。</span><span class="sxs-lookup"><span data-stu-id="40564-128">When an instance constructor has no constructor initializer, ... that constructor implicitly performs the initializations specified by the _variable_initializers_ of the instance fields ... .</span></span> <span data-ttu-id="40564-129">这对应于在进入构造函数之后立即执行的一系列赋值。</span><span class="sxs-lookup"><span data-stu-id="40564-129">This corresponds to a sequence of assignments that are executed immediately upon entry to the constructor ... .</span></span> <span data-ttu-id="40564-130">变量初始值设定项的执行顺序与它们在 .。。声明.</span><span class="sxs-lookup"><span data-stu-id="40564-130">The variable initializers are executed in the textual order in which they appear in the ... declaration.</span></span>

### <a name="definite-assignment"></a><span data-ttu-id="40564-131">明确赋值</span><span class="sxs-lookup"><span data-stu-id="40564-131">Definite assignment</span></span>
<span data-ttu-id="40564-132">实例字段必须在没有初始值设定项的结构实例构造函数中明确赋值 `this()` (参见 [结构构造函数](https://github.com/dotnet/csharplang/blob/master/spec/structs.md#constructors)) 。</span><span class="sxs-lookup"><span data-stu-id="40564-132">Instance fields must be definitely assigned in struct instance constructors that do not have a `this()` initializer (see [struct constructors](https://github.com/dotnet/csharplang/blob/master/spec/structs.md#constructors)).</span></span>

<span data-ttu-id="40564-133">在显式无参数的构造函数中，还需要显式分配实例字段。</span><span class="sxs-lookup"><span data-stu-id="40564-133">Definite assignment of instance fields is required within explicit parameterless constructors as well.</span></span>
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

<span data-ttu-id="40564-134">_是否应在合成无参数构造函数中明确分配结构实例字段？_ 
_如果是这样，则在任何实例字段都有初始值设定项时，所有实例字段必须有初始值设定项。_</span><span class="sxs-lookup"><span data-stu-id="40564-134">_Should definite assignment of struct instance fields be required within synthesized parameterless constructors?_
_If so, then if any instance fields have initializers, all instance fields must have initializers._</span></span>
```csharp
struct S0
{
    int x = 0;
    object y;
    // ok?
}
```

<span data-ttu-id="40564-135">如果未显式初始化字段，则在执行任何字段初始值设定项之前，构造函数将需要为该实例的零。</span><span class="sxs-lookup"><span data-stu-id="40564-135">If fields are not explicitly initialized, the constructor will need to zero the instance before executing any field initializers.</span></span>
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

### <a name="no-base-initializer"></a><span data-ttu-id="40564-136">没有 `base()` 初始值设定项</span><span class="sxs-lookup"><span data-stu-id="40564-136">No `base()` initializer</span></span>
<span data-ttu-id="40564-137">`base()`结构构造函数中不允许使用初始值设定项。</span><span class="sxs-lookup"><span data-stu-id="40564-137">A `base()` initializer is disallowed in struct constructors.</span></span>

<span data-ttu-id="40564-138">编译器不会 `System.ValueType` 从包含显式无参数构造函数的任何结构实例构造函数中发出对基构造函数的调用。</span><span class="sxs-lookup"><span data-stu-id="40564-138">The compiler will not emit a call to the base `System.ValueType` constructor from any struct instance constructors including explicit and synthesized parameterless constructors.</span></span>

### <a name="fields"></a><span data-ttu-id="40564-139">字段</span><span class="sxs-lookup"><span data-stu-id="40564-139">Fields</span></span>
<span data-ttu-id="40564-140">合成无参数构造函数将为零个字段，而不是为字段类型调用任何无参数的构造函数。</span><span class="sxs-lookup"><span data-stu-id="40564-140">The synthesized parameterless constructor will zero fields rather than calling any parameterless constructors for the field types.</span></span>

<span data-ttu-id="40564-141">_当忽略字段的构造函数时，编译器是否应报告警告？_</span><span class="sxs-lookup"><span data-stu-id="40564-141">_Should the compiler report a warning when constructors for fields are ignored?_</span></span>
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

### <a name="default-expression"></a><span data-ttu-id="40564-142">`default` 表达式</span><span class="sxs-lookup"><span data-stu-id="40564-142">`default` expression</span></span>
<span data-ttu-id="40564-143">`default` 忽略无参数构造函数并生成一个零的实例。</span><span class="sxs-lookup"><span data-stu-id="40564-143">`default` ignores the parameterless constructor and generates a zeroed instance.</span></span>

<span data-ttu-id="40564-144">_当忽略构造函数时，编译器是否应报告警告？_</span><span class="sxs-lookup"><span data-stu-id="40564-144">_Should the compiler report a warning when a constructor is ignored?_</span></span>
```csharp
_ = default(NoConstructor);      // ok
_ = default(PublicConstructor);  // ok: constructor ignored
_ = default(PrivateConstructor); // ok: constructor ignored
```

### <a name="object-creation"></a><span data-ttu-id="40564-145">对象创建</span><span class="sxs-lookup"><span data-stu-id="40564-145">Object creation</span></span>
<span data-ttu-id="40564-146">对象创建表达式要求可访问的无参数构造函数（如果已定义）。</span><span class="sxs-lookup"><span data-stu-id="40564-146">Object creation expressions require the parameterless constructor to be accessible if defined.</span></span>
<span data-ttu-id="40564-147">无参数构造函数是显式调用的。</span><span class="sxs-lookup"><span data-stu-id="40564-147">The parameterless constructor is invoked explicitly.</span></span>

<span data-ttu-id="40564-148">_如果带参数构造函数的结构类型来自于现有程序集，则这是一项重大更改。_ 
_`new()` 如果构造函数不可访问 `initobj` ，则编译器是否应报告警告，而不是为兼容性发出错误？_</span><span class="sxs-lookup"><span data-stu-id="40564-148">_This is a breaking change if the struct type with parameterless constructor is from an existing assembly._
_Should the compiler report a warning rather than an error for `new()` if the constructor is inaccessible, and emit `initobj`, for compatability?_</span></span>
```csharp
_ = new NoConstructor();      // ok: initobj NoConstructor
_ = new PublicConstructor();  // ok: call PublicConstructor::.ctor()
_ = new PrivateConstructor(); // error: 'PrivateConstructor..ctor()' is inaccessible
```

### <a name="uninitialized-values"></a><span data-ttu-id="40564-149">未初始化值</span><span class="sxs-lookup"><span data-stu-id="40564-149">Uninitialized values</span></span>
<span data-ttu-id="40564-150">未显式初始化的结构类型的局部或字段归零。</span><span class="sxs-lookup"><span data-stu-id="40564-150">A local or field of a struct type that is not explicitly initialized is zeroed.</span></span>
<span data-ttu-id="40564-151">对于未初始化的非空结构，编译器将报告明确赋值错误。</span><span class="sxs-lookup"><span data-stu-id="40564-151">The compiler reports a definite assignment error for an uninitialized struct that is not empty.</span></span> 
```csharp
NoConstructor s1;
PublicConstructor s2;
s1.ToString(); // error: use of unassigned local (unless type is empty)
s2.ToString(); // error: use of unassigned local (unless type is empty)
```

### <a name="array-allocation"></a><span data-ttu-id="40564-152">数组分配</span><span class="sxs-lookup"><span data-stu-id="40564-152">Array allocation</span></span>
<span data-ttu-id="40564-153">数组分配将忽略任何无参数的构造函数并生成零个元素。</span><span class="sxs-lookup"><span data-stu-id="40564-153">Array allocation ignores any parameterless constructor and generates zeroed elements.</span></span>

<span data-ttu-id="40564-154">_编译器是否应警告无参数构造函数被忽略？如何避免此类警告？_</span><span class="sxs-lookup"><span data-stu-id="40564-154">_Should the compiler warn that the parameterless constructor is ignored? How would such a warning be avoided?_</span></span>
```csharp
_ = new NoConstructor[1];      // ok
_ = new PublicConstructor[1];  // ok: constructor ignored
_ = new PrivateConstructor[1]; // ok: constructor ignored
```

### <a name="parameter-default-values"></a><span data-ttu-id="40564-155">参数默认值</span><span class="sxs-lookup"><span data-stu-id="40564-155">Parameter default values</span></span>
<span data-ttu-id="40564-156">无参数构造函数不能用作参数的默认值。</span><span class="sxs-lookup"><span data-stu-id="40564-156">Parameterless constructors cannot be used as parameter default values.</span></span>

<span data-ttu-id="40564-157">_如果带参数构造函数的结构类型来自于现有程序集，则这是一项重大更改。_ 
_`new()` 如果构造函数不可访问 `default` ，则编译器是否应报告警告，而不是为兼容性发出错误？_</span><span class="sxs-lookup"><span data-stu-id="40564-157">_This is a breaking change if the struct type with parameterless constructor is from an existing assembly._
_Should the compiler report a warning rather than an error for `new()` if the constructor is inaccessible, and emit `default`, for compatability?_</span></span>
```csharp
static void F1(NoConstructor s1 = new()) { }     // ok
static void F2(PublicConstructor s1 = new()) { } // error: default value must be constant
```

### <a name="constraints"></a><span data-ttu-id="40564-158">约束</span><span class="sxs-lookup"><span data-stu-id="40564-158">Constraints</span></span>
<span data-ttu-id="40564-159">`new()`如果定义了类型参数，则该约束要求参数构造函数 `public` (参阅) [满足约束](https://github.com/dotnet/csharplang/blob/master/spec/types.md#satisfying-constraints)。</span><span class="sxs-lookup"><span data-stu-id="40564-159">The `new()` type parameter constraint requires the parameterless constructor to be `public` if defined (see [satisfying constraints](https://github.com/dotnet/csharplang/blob/master/spec/types.md#satisfying-constraints)).</span></span>
```csharp
static T CreateNew<T>() where T : new() => new T();

_ = CreateNew<NoConstructor>();       // ok
_ = CreateNew<PublicConstructor>();   // ok
_ = CreateNew<InternalConstructor>(); // error: 'InternalConstructor..ctor()' is not public
```
<span data-ttu-id="40564-160">_如果将具有非公共构造函数的结构替换为具有约束的类型参数，编译器将报告警告而不是错误 `new()` ，以确定是否实际实例化了类型？_</span><span class="sxs-lookup"><span data-stu-id="40564-160">_Should the compiler report a warning rather than an error when substituting a struct with a non-public constructor for a type parameter with a `new()` constraint, for compatability and to avoid assuming the type is actually instantiated?_</span></span>

<span data-ttu-id="40564-161">`new T()` 作为对的调用发出 `System.Activator.CreateInstance<T>()` ，并且编译器假定的实现将 `CreateInstance<T>()` 调用 `public` 无参数的构造函数（如果已定义）。</span><span class="sxs-lookup"><span data-stu-id="40564-161">`new T()` is emitted as a call to `System.Activator.CreateInstance<T>()`, and the compiler assumes the implementation of `CreateInstance<T>()` invokes the `public` parameterless constructor if defined.</span></span>

<span data-ttu-id="40564-162">_如果有 .NET Framework，则 `Activator.CreateInstance<T>()` 调用无参数的构造函数，如果约束为，则将调用无 `where T : new()` 参数构造函数 `where T : struct` 。_</span><span class="sxs-lookup"><span data-stu-id="40564-162">_With .NET Framework, `Activator.CreateInstance<T>()` invokes the parameterless constructor if the constraint is `where T : new()` but appears to ignore the parameterless constructor if the constraint is `where T : struct`._</span></span>

<span data-ttu-id="40564-163">类型参数约束检查存在间隔，因为 `new()` 具有约束的类型参数满足约束 `struct` (请参阅 [满足约束](https://github.com/dotnet/csharplang/blob/master/spec/types.md#satisfying-constraints)) 。</span><span class="sxs-lookup"><span data-stu-id="40564-163">There is a gap in type parameter constraint checking because the `new()` constraint is satisfied by a type parameter with a `struct` constraint (see [satisfying constraints](https://github.com/dotnet/csharplang/blob/master/spec/types.md#satisfying-constraints)).</span></span>

<span data-ttu-id="40564-164">因此，编译器将允许以下项，但 `Activator.CreateInstance<InternalConstructor>()` 在运行时将失败。</span><span class="sxs-lookup"><span data-stu-id="40564-164">As a result, the following will be allowed by the compiler but `Activator.CreateInstance<InternalConstructor>()` will fail at runtime.</span></span>
<span data-ttu-id="40564-165">此问题不是由本提案引入的-如果在元数据中具有不可访问的无参数构造函数的结构类型，则 c # 9 中会出现此问题。</span><span class="sxs-lookup"><span data-stu-id="40564-165">The issue is not introduced by this proposal though - the issue exists with C# 9 if the struct type with inaccessible parameterless constructor is from metadata.</span></span>
```csharp
static T CreateNew<T>() where T : new() => new T();
static T CreateStruct<T>() where T : struct => CreateNew<T>();

_ = CreateStruct<InternalConstructor>(); // compiles; 'MissingMethodException' at runtime
```

### <a name="optional-parameters"></a><span data-ttu-id="40564-166">可选参数</span><span class="sxs-lookup"><span data-stu-id="40564-166">Optional parameters</span></span>
<span data-ttu-id="40564-167">具有可选参数的构造函数不被视为无参数的构造函数。</span><span class="sxs-lookup"><span data-stu-id="40564-167">Constructors with optional parameters are not considered parameterless constructors.</span></span> <span data-ttu-id="40564-168">此行为与早期版本的编译器版本保持不变。</span><span class="sxs-lookup"><span data-stu-id="40564-168">This behavior is unchanged from earlier compiler versions.</span></span>
```csharp
struct S1 { public S1(string s = "") { } }
struct S2 { public S2(params object[] args) { } }

_ = new S1(); // ok: ignores constructor
_ = new S2(); // ok: ignores constructor
```

### <a name="metadata"></a><span data-ttu-id="40564-169">元数据</span><span class="sxs-lookup"><span data-stu-id="40564-169">Metadata</span></span>
<span data-ttu-id="40564-170">显式和合成无参数结构实例构造函数将发送到元数据。</span><span class="sxs-lookup"><span data-stu-id="40564-170">Explicit and synthesized parameterless struct instance constructors will be emitted to metadata.</span></span>

<span data-ttu-id="40564-171">无参数结构实例构造函数将从元数据中导入，而不考虑可访问性。</span><span class="sxs-lookup"><span data-stu-id="40564-171">Parameterless struct instance constructors will be imported from metadata regardless of accessibility.</span></span>
<span data-ttu-id="40564-172">_如果报告了其他错误或警告，则这可能是使用具有私有无参数构造函数的结构的现有程序集的使用者的重大更改。_</span><span class="sxs-lookup"><span data-stu-id="40564-172">_This might be a breaking change for consumers of existing assemblies with structs with private parameterless constructors if additional errors or warnings are reported._</span></span>

<span data-ttu-id="40564-173">无参数结构实例构造函数将发出到 ref 程序集，而不考虑允许使用者区分无参数构造函数的构造函数。</span><span class="sxs-lookup"><span data-stu-id="40564-173">Parameterless struct instance constructors will be emitted to ref assemblies regardless of accessibility to allow consumers to differentiate between no parameterless constructor an inaccessible constructor.</span></span>

## <a name="see-also"></a><span data-ttu-id="40564-174">另请参阅</span><span class="sxs-lookup"><span data-stu-id="40564-174">See also</span></span>

- https://github.com/dotnet/roslyn/issues/1029

## <a name="design-meetings"></a><span data-ttu-id="40564-175">设计会议</span><span class="sxs-lookup"><span data-stu-id="40564-175">Design meetings</span></span>

- https://github.com/dotnet/csharplang/blob/master/meetings/2021/LDM-2021-01-27.md#field-initializers

---
ms.openlocfilehash: d5eeb2501d4e99136911cb1fa2153ff7057432b3
ms.sourcegitcommit: 14c7c290e926fcaff7cb8609b282821a473589af
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/02/2021
ms.locfileid: "101751639"
---
# <a name="parameterless-struct-constructors"></a><span data-ttu-id="342b8-101">无参数结构构造函数</span><span class="sxs-lookup"><span data-stu-id="342b8-101">Parameterless struct constructors</span></span>

## <a name="summary"></a><span data-ttu-id="342b8-102">总结</span><span class="sxs-lookup"><span data-stu-id="342b8-102">Summary</span></span>

<span data-ttu-id="342b8-103">支持结构类型的无参数构造函数和实例字段初始值设定项。</span><span class="sxs-lookup"><span data-stu-id="342b8-103">Support parameterless constructors and instance field initializers for struct types.</span></span>

## <a name="proposal"></a><span data-ttu-id="342b8-104">建议</span><span class="sxs-lookup"><span data-stu-id="342b8-104">Proposal</span></span>

### <a name="instance-field-initializers"></a><span data-ttu-id="342b8-105">实例字段初始值设定项</span><span class="sxs-lookup"><span data-stu-id="342b8-105">Instance field initializers</span></span>
<span data-ttu-id="342b8-106">结构的实例字段声明可以包含初始值设定项。</span><span class="sxs-lookup"><span data-stu-id="342b8-106">Instance field declarations for a struct may include initializers.</span></span>

<span data-ttu-id="342b8-107">与 [类字段初始值设定项](https://github.com/dotnet/csharplang/blob/master/spec/classes.md#instance-field-initialization)一样：</span><span class="sxs-lookup"><span data-stu-id="342b8-107">As with [class field initializers](https://github.com/dotnet/csharplang/blob/master/spec/classes.md#instance-field-initialization):</span></span>
> <span data-ttu-id="342b8-108">实例字段的变量初始值设定项不能引用正在创建的实例。</span><span class="sxs-lookup"><span data-stu-id="342b8-108">A variable initializer for an instance field cannot reference the instance being created.</span></span> 

### <a name="constructors"></a><span data-ttu-id="342b8-109">构造函数</span><span class="sxs-lookup"><span data-stu-id="342b8-109">Constructors</span></span>
<span data-ttu-id="342b8-110">结构可声明无参数的实例构造函数。</span><span class="sxs-lookup"><span data-stu-id="342b8-110">A struct may declare a parameterless instance constructor.</span></span>
<span data-ttu-id="342b8-111">无参数的实例构造函数对所有结构类型有效 `struct` ，包括、 `readonly struct` 、 `ref struct` 和 `record struct` 。</span><span class="sxs-lookup"><span data-stu-id="342b8-111">A parameterless instance constructor is valid for all struct kinds including `struct`, `readonly struct`, `ref struct`, and `record struct`.</span></span>

<span data-ttu-id="342b8-112">如果该结构未声明无参数的实例构造函数，并且该结构没有包含变量初始值设定项的字段，则结构 (参见 [结构构造函数](https://github.com/dotnet/csharplang/blob/master/spec/structs.md#constructors)) .。。</span><span class="sxs-lookup"><span data-stu-id="342b8-112">If the struct does not declare a parameterless instance constructor, and the struct has no fields with variable initializers, the struct (see [struct constructors](https://github.com/dotnet/csharplang/blob/master/spec/structs.md#constructors)) ...</span></span>
> <span data-ttu-id="342b8-113">隐式具有一个无参数的实例构造函数，该构造函数始终返回通过将所有值类型字段设置为其默认值，并将所有引用类型字段设置为 null 而产生的值。</span><span class="sxs-lookup"><span data-stu-id="342b8-113">implicitly has a parameterless instance constructor which always returns the value that results from setting all value type fields to their default value and all reference type fields to null.</span></span>

<span data-ttu-id="342b8-114">如果结构未声明无参数的实例构造函数，并且该结构具有字段初始值设定项， `public` 则会合成无参数的实例构造函数。</span><span class="sxs-lookup"><span data-stu-id="342b8-114">If the struct does not declare a parameterless instance constructor, and the struct has field initializers, a `public` parameterless instance constructor is synthesized.</span></span>
<span data-ttu-id="342b8-115">无参数构造函数会合成，即使所有初始值设定项的值都为零。</span><span class="sxs-lookup"><span data-stu-id="342b8-115">The parameterless constructor is synthesized even if all initializer values are zeros.</span></span>

### <a name="modifiers"></a><span data-ttu-id="342b8-116">修饰符</span><span class="sxs-lookup"><span data-stu-id="342b8-116">Modifiers</span></span>
<span data-ttu-id="342b8-117">无参数的实例构造函数的可访问性可能低于包含结构。</span><span class="sxs-lookup"><span data-stu-id="342b8-117">A parameterless instance constructor may be less accessible than the containing struct.</span></span>
<span data-ttu-id="342b8-118">这对稍后详细介绍的某些情况有影响。</span><span class="sxs-lookup"><span data-stu-id="342b8-118">This has consequences for certain scenarios detailed later.</span></span>

<span data-ttu-id="342b8-119">同一组修饰符可用于作为其他构造函数的无参数构造函数： `static` 、 `extern` 和 `unsafe` 。</span><span class="sxs-lookup"><span data-stu-id="342b8-119">The same set of modifiers can be used for parameterless constructors as other constructors: `static`, `extern`, and `unsafe`.</span></span>

### <a name="executing-field-initializers"></a><span data-ttu-id="342b8-120">执行字段初始值设定项</span><span class="sxs-lookup"><span data-stu-id="342b8-120">Executing field initializers</span></span>
<span data-ttu-id="342b8-121">结构实例字段初始值设定项的执行与 [类字段初始值设定](https://github.com/dotnet/csharplang/blob/master/spec/classes.md#instance-variable-initializers)项的执行匹配：</span><span class="sxs-lookup"><span data-stu-id="342b8-121">Execution of struct instance field initializers matches execution of [class field initializers](https://github.com/dotnet/csharplang/blob/master/spec/classes.md#instance-variable-initializers):</span></span>
> <span data-ttu-id="342b8-122">当实例构造函数没有构造函数初始值设定项时 .。。该构造函数隐式执行实例字段的 _variable_initializers_ 指定的初始化 ...。</span><span class="sxs-lookup"><span data-stu-id="342b8-122">When an instance constructor has no constructor initializer, ... that constructor implicitly performs the initializations specified by the _variable_initializers_ of the instance fields ... .</span></span> <span data-ttu-id="342b8-123">这对应于在进入构造函数之后、直接调用直接基类构造函数之前立即执行的一系列赋值。</span><span class="sxs-lookup"><span data-stu-id="342b8-123">This corresponds to a sequence of assignments that are executed immediately upon entry to the constructor and before the implicit invocation of the direct base class constructor.</span></span> <span data-ttu-id="342b8-124">变量初始值设定项的执行顺序与它们在 .。。声明.</span><span class="sxs-lookup"><span data-stu-id="342b8-124">The variable initializers are executed in the textual order in which they appear in the ... declaration.</span></span>

### <a name="definite-assignment"></a><span data-ttu-id="342b8-125">明确赋值</span><span class="sxs-lookup"><span data-stu-id="342b8-125">Definite assignment</span></span>
<span data-ttu-id="342b8-126">实例字段必须在没有初始值设定项的结构实例构造函数中明确赋值 `this()` (参见 [结构构造函数](https://github.com/dotnet/csharplang/blob/master/spec/structs.md#constructors)) 。</span><span class="sxs-lookup"><span data-stu-id="342b8-126">Instance fields must be definitely assigned in struct instance constructors that do not have a `this()` initializer (see [struct constructors](https://github.com/dotnet/csharplang/blob/master/spec/structs.md#constructors)).</span></span>

<span data-ttu-id="342b8-127">在显式无参数的构造函数中，还需要显式分配实例字段。</span><span class="sxs-lookup"><span data-stu-id="342b8-127">Definite assignment of instance fields is required within explicit parameterless constructors as well.</span></span>
<span data-ttu-id="342b8-128">合成无参数的构造函数中 _不需要_ 实例字段的明确分配。</span><span class="sxs-lookup"><span data-stu-id="342b8-128">Definite assignment of instance fields _is not required_ within synthesized parameterless constructors.</span></span>

<span data-ttu-id="342b8-129">_是否应一致地应用明确的分配规则？也就是说，如果需要显式分配结构的所有实例字段（即使未合成无参数的构造函数），或者是否应该删除在结构构造函数中显式分配了所有字段的现有要求，_</span><span class="sxs-lookup"><span data-stu-id="342b8-129">_Should the definite assignment rule be applied consistently? That is, should we require that all instance fields of a struct are explicitly assigned even when the parameterless constructor is synthesized, or should we drop the existing requirement that all fields are explicitly assigned in struct constructors?_</span></span>
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

<span data-ttu-id="342b8-130">_合成无参数的构造函数可能需要 `ldarg.0 initobj S` 在任何字段初始值设定项之前发出。描述具体内容。如何在 Visual Basic 中处理此问题？_</span><span class="sxs-lookup"><span data-stu-id="342b8-130">_The synthesized parameterless constructor may need to emit `ldarg.0 initobj S` before any field initializers. Describe the specifics. How is this handled in Visual Basic?_</span></span>

### <a name="no-base-initializer"></a><span data-ttu-id="342b8-131">没有 `base()` 初始值设定项</span><span class="sxs-lookup"><span data-stu-id="342b8-131">No `base()` initializer</span></span>
<span data-ttu-id="342b8-132">`base()`结构构造函数中不允许使用初始值设定项。</span><span class="sxs-lookup"><span data-stu-id="342b8-132">A `base()` initializer is disallowed in struct constructors.</span></span>

<span data-ttu-id="342b8-133">编译器不会 `System.ValueType` 从包含显式无参数构造函数的任何结构实例构造函数中发出对基构造函数的调用。</span><span class="sxs-lookup"><span data-stu-id="342b8-133">The compiler will not emit a call to the base `System.ValueType` constructor from any struct instance constructors including explicit and synthesized parameterless constructors.</span></span>

### <a name="constructor-use"></a><span data-ttu-id="342b8-134">构造函数使用</span><span class="sxs-lookup"><span data-stu-id="342b8-134">Constructor use</span></span>

<span data-ttu-id="342b8-135">无参数的构造函数比包含值类型的可访问性低。</span><span class="sxs-lookup"><span data-stu-id="342b8-135">The parameterless constructor may be less accessible than the containing value type.</span></span>
```csharp
public struct NoConstructor { }
public struct PublicConstructor { public PublicConstructor() { } }
public struct InternalConstructor { internal InternalConstructor() { } }
public struct PrivateConstructor { private PrivateConstructor() { } }
```

<span data-ttu-id="342b8-136">`default` 忽略无参数构造函数并生成一个零的实例。</span><span class="sxs-lookup"><span data-stu-id="342b8-136">`default` ignores the parameterless constructor and generates a zeroed instance.</span></span>
```csharp
_ = default(NoConstructor);      // ok
_ = default(PublicConstructor);  // ok: constructor ignored
_ = default(PrivateConstructor); // ok: constructor ignored
```

<span data-ttu-id="342b8-137">对象创建表达式要求可访问的无参数构造函数（如果已定义）。</span><span class="sxs-lookup"><span data-stu-id="342b8-137">Object creation expressions require the parameterless constructor to be accessible if defined.</span></span>
<span data-ttu-id="342b8-138">无参数构造函数是显式调用的。</span><span class="sxs-lookup"><span data-stu-id="342b8-138">The parameterless constructor is invoked explicitly.</span></span>
<span data-ttu-id="342b8-139">_如果带参数构造函数的结构类型来自于现有程序集，则这是一项重大更改。_</span><span class="sxs-lookup"><span data-stu-id="342b8-139">_This is a breaking change if the struct type with parameterless constructor is from an existing assembly._</span></span>
```csharp
_ = new NoConstructor();       // ok: initobj NoConstructor
_ = new InternalConstructor(); // ok: call InternalConstructor::.ctor()
_ = new PrivateConstructor();  // error: 'PrivateConstructor..ctor()' is inaccessible
```

<span data-ttu-id="342b8-140">未显式初始化的结构类型的局部或字段归零。</span><span class="sxs-lookup"><span data-stu-id="342b8-140">A local or field of a struct type that is not explicitly initialized is zeroed.</span></span>
<span data-ttu-id="342b8-141">对于未初始化的非空结构，编译器将报告明确赋值错误。</span><span class="sxs-lookup"><span data-stu-id="342b8-141">The compiler reports a definite assignment error for an uninitialized struct that is not empty.</span></span> 
```csharp
NoConstructor s1;
PublicConstructor s2;
s1.ToString(); // error: use of unassigned local (unless type is empty)
s2.ToString(); // error: use of unassigned local (unless type is empty)
```

<span data-ttu-id="342b8-142">数组分配将忽略任何无参数的构造函数并生成零个元素。</span><span class="sxs-lookup"><span data-stu-id="342b8-142">Array allocation ignores any parameterless constructor and generates zeroed elements.</span></span>
<span data-ttu-id="342b8-143">_编译器是否应警告无参数构造函数被忽略？如何避免此类警告？_</span><span class="sxs-lookup"><span data-stu-id="342b8-143">_Should the compiler warn that the parameterless constructor is ignored? How would such a warning be avoided?_</span></span>
```csharp
_ = new NoConstructor[1];      // ok
_ = new PublicConstructor[1];  // ok: constructor ignored
_ = new PrivateConstructor[1]; // ok: constructor ignored
```

<span data-ttu-id="342b8-144">无参数构造函数不能用作参数的默认值。</span><span class="sxs-lookup"><span data-stu-id="342b8-144">Parameterless constructors cannot be used as parameter default values.</span></span>
<span data-ttu-id="342b8-145">_如果带参数构造函数的结构类型来自于现有程序集，则这是一项重大更改。_</span><span class="sxs-lookup"><span data-stu-id="342b8-145">_This is a breaking change if the struct type with parameterless constructor is from an existing assembly._</span></span>
```csharp
static void F1(NoConstructor s1 = new()) { }     // ok
static void F2(PublicConstructor s1 = new()) { } // error: default value must be constant
```

### <a name="constraints"></a><span data-ttu-id="342b8-146">约束</span><span class="sxs-lookup"><span data-stu-id="342b8-146">Constraints</span></span>
<span data-ttu-id="342b8-147">`new()`如果定义了类型参数，则该约束要求参数构造函数 `public` (参阅) [满足约束](https://github.com/dotnet/csharplang/blob/master/spec/types.md#satisfying-constraints)。</span><span class="sxs-lookup"><span data-stu-id="342b8-147">The `new()` type parameter constraint requires the parameterless constructor to be `public` if defined (see [satisfying constraints](https://github.com/dotnet/csharplang/blob/master/spec/types.md#satisfying-constraints)).</span></span>
```csharp
static T CreateNew<T>() where T : new() => new T();

_ = CreateNew<NoConstructor>();       // ok
_ = CreateNew<PublicConstructor>();   // ok
_ = CreateNew<InternalConstructor>(); // error: 'InternalConstructor..ctor()' is not public
_ = CreateNew<PrivateConstructor>();  // error: 'PrivateConstructor..ctor()' is not public
```

<span data-ttu-id="342b8-148">`new T()` 作为对的调用发出 `System.Activator.CreateInstance<T>()` ，并且编译器假定的实现将 `CreateInstance<T>()` 调用 `public` 无参数的构造函数（如果已定义）。</span><span class="sxs-lookup"><span data-stu-id="342b8-148">`new T()` is emitted as a call to `System.Activator.CreateInstance<T>()`, and the compiler assumes the implementation of `CreateInstance<T>()` invokes the `public` parameterless constructor if defined.</span></span>

<span data-ttu-id="342b8-149">类型参数约束检查存在间隔，因为 `new()` 具有约束的类型参数满足约束 `struct` (请参阅 [满足约束](https://github.com/dotnet/csharplang/blob/master/spec/types.md#satisfying-constraints)) 。</span><span class="sxs-lookup"><span data-stu-id="342b8-149">There is a gap in type parameter constraint checking because the `new()` constraint is satisfied by a type parameter with a `struct` constraint (see [satisfying constraints](https://github.com/dotnet/csharplang/blob/master/spec/types.md#satisfying-constraints)).</span></span>

<span data-ttu-id="342b8-150">因此，编译器将允许以下项，但 `Activator.CreateInstance<InternalConstructor>()` 在运行时将失败。</span><span class="sxs-lookup"><span data-stu-id="342b8-150">As a result, the following will be allowed by the compiler but `Activator.CreateInstance<InternalConstructor>()` will fail at runtime.</span></span>
<span data-ttu-id="342b8-151">此问题不是由本提案引入的-如果在元数据中具有不可访问的无参数构造函数的结构类型，则 c # 9 中会出现此问题。</span><span class="sxs-lookup"><span data-stu-id="342b8-151">The issue is not introduced by this proposal though - the issue exists with C# 9 if the struct type with inaccessible parameterless constructor is from metadata.</span></span>
```csharp
static T CreateNew<T>() where T : new() => new T();
static T CreateStruct<T>() where T : struct => CreateNew<T>();

_ = CreateStruct<InternalConstructor>(); // compiles; 'MissingMethodException' at runtime
```

### <a name="metadata"></a><span data-ttu-id="342b8-152">Metadata</span><span class="sxs-lookup"><span data-stu-id="342b8-152">Metadata</span></span>
<span data-ttu-id="342b8-153">显式和合成无参数结构实例构造函数将发送到元数据。</span><span class="sxs-lookup"><span data-stu-id="342b8-153">Explicit and synthesized parameterless struct instance constructors will be emitted to metadata.</span></span>

<span data-ttu-id="342b8-154">无参数结构实例构造函数将从元数据导入。</span><span class="sxs-lookup"><span data-stu-id="342b8-154">Parameterless struct instance constructors will be imported from metadata.</span></span>
<span data-ttu-id="342b8-155">_这是对具有无参数构造函数的结构的现有程序集的使用者的重大更改。_</span><span class="sxs-lookup"><span data-stu-id="342b8-155">_This is a breaking change for consumers of existing assemblies with structs with parameterless constructors._</span></span>

<span data-ttu-id="342b8-156">无参数结构实例构造函数将发出到 ref 程序集，而不考虑允许使用者区分无参数构造函数的构造函数。</span><span class="sxs-lookup"><span data-stu-id="342b8-156">Parameterless struct instance constructors will be emitted to ref assemblies regardless of accessibility to allow consumers to differentiate between no parameterless constructor an inaccessible constructor.</span></span>

## <a name="see-also"></a><span data-ttu-id="342b8-157">另请参阅</span><span class="sxs-lookup"><span data-stu-id="342b8-157">See also</span></span>

- https://github.com/dotnet/roslyn/issues/1029

## <a name="design-meetings"></a><span data-ttu-id="342b8-158">设计会议</span><span class="sxs-lookup"><span data-stu-id="342b8-158">Design meetings</span></span>

- https://github.com/dotnet/csharplang/blob/master/meetings/2021/LDM-2021-01-27.md#field-initializers

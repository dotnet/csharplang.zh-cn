---
ms.openlocfilehash: a082598353ede157e6d5b3d219a04a85cbb292bf
ms.sourcegitcommit: 8cf85e8021b081f3bb2c71dede5e2aee9923bfa4
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/04/2020
ms.locfileid: "96596364"
---
<a name="low-level-struct-improvements"></a><span data-ttu-id="9263d-101">低级别结构改进</span><span class="sxs-lookup"><span data-stu-id="9263d-101">Low Level Struct Improvements</span></span>
=====

## <a name="summary"></a><span data-ttu-id="9263d-102">总结</span><span class="sxs-lookup"><span data-stu-id="9263d-102">Summary</span></span>
<span data-ttu-id="9263d-103">此建议是用于改进性能的几个不同提议的聚合 `struct` 。</span><span class="sxs-lookup"><span data-stu-id="9263d-103">This proposal is an aggregation of several different proposals for `struct` performance improvements.</span></span> <span data-ttu-id="9263d-104">目标是一种设计，该设计将考虑各种方案来创建一个集中的功能集来 `struct` 改进。</span><span class="sxs-lookup"><span data-stu-id="9263d-104">The goal being a design which takes into account the various proposals to create a single overarching feature set for `struct` improvements.</span></span>

## <a name="motivation"></a><span data-ttu-id="9263d-105">动机</span><span class="sxs-lookup"><span data-stu-id="9263d-105">Motivation</span></span>
<span data-ttu-id="9263d-106">在过去的几个版本中，c # 为该语言添加了许多低级别性能功能： `ref` 返回、 `ref struct` 、函数指针等。使 .NET 开发人员能够创建编写高性能的代码，同时继续利用 c # 语言规则提供类型和内存安全。</span><span class="sxs-lookup"><span data-stu-id="9263d-106">Over the last few releases C# has added a number of low level performance features to the language: `ref` returns, `ref struct`, function pointers, etc. ... These enabled .NET developers to create write highly performant code while continuing to leverage the C# language rules for type and memory safety.</span></span>
<span data-ttu-id="9263d-107">它还允许在等 .NET 库中创建基本性能类型 `Span<T>` 。</span><span class="sxs-lookup"><span data-stu-id="9263d-107">It also allowed the creation of fundamental performance types in the .NET libraries like `Span<T>`.</span></span>

<span data-ttu-id="9263d-108">由于这些功能在内部和外部的 .NET 生态系统开发人员中获得了吸引力，因此我们提供了有关生态系统中剩余的摩擦要点的信息。</span><span class="sxs-lookup"><span data-stu-id="9263d-108">As these features have gained traction in the .NET ecosystem developers, both internal and external, have been providing us with information on remaining friction points in the ecosystem.</span></span> <span data-ttu-id="9263d-109">，他们仍需要删除 `unsafe` 代码才能获取其工作，或者要求运行时使用特殊的 case 类型（如） `Span<T>` 。</span><span class="sxs-lookup"><span data-stu-id="9263d-109">Places where they still need to drop to `unsafe` code to get their work, or require the runtime to special case types like `Span<T>`.</span></span> 

<span data-ttu-id="9263d-110">此建议旨在通过构建现有的低级别功能来解决其中的许多问题。</span><span class="sxs-lookup"><span data-stu-id="9263d-110">This proposal aims to address many of these concerns by building on top of our existing low level features.</span></span> <span data-ttu-id="9263d-111">具体来说，它旨在：</span><span class="sxs-lookup"><span data-stu-id="9263d-111">Specifically it aims to:</span></span>

- <span data-ttu-id="9263d-112">允许 `ref struct` 类型声明 `ref` 字段。</span><span class="sxs-lookup"><span data-stu-id="9263d-112">Allow `ref struct` types to declare `ref` fields.</span></span>
- <span data-ttu-id="9263d-113">允许运行时 `Span<T>` 使用 c # 类型系统完全定义，并删除特殊的 case 类型（如） `ByReference<T>`</span><span class="sxs-lookup"><span data-stu-id="9263d-113">Allow the runtime to fully define `Span<T>` using the C# type system and remove special case type like `ByReference<T>`</span></span>
- <span data-ttu-id="9263d-114">允许 `struct` 类型返回 `ref` 到其字段。</span><span class="sxs-lookup"><span data-stu-id="9263d-114">Allow `struct` types to return `ref` to their fields.</span></span>
- <span data-ttu-id="9263d-115">允许声明 `fixed` 中的托管和非托管类型的安全缓冲区 `struct`</span><span class="sxs-lookup"><span data-stu-id="9263d-115">Allow the declaration of safe `fixed` buffers for managed and unmanaged types in `struct`</span></span>

## <a name="detailed-design"></a><span data-ttu-id="9263d-116">详细设计</span><span class="sxs-lookup"><span data-stu-id="9263d-116">Detailed Design</span></span> 
<span data-ttu-id="9263d-117">`ref struct`安全规则在[范围安全文档](https://github.com/dotnet/csharplang/blob/master/proposals/csharp-7.2/span-safety.md)中定义。</span><span class="sxs-lookup"><span data-stu-id="9263d-117">The rules for `ref struct` safety are defined in the [span-safety document](https://github.com/dotnet/csharplang/blob/master/proposals/csharp-7.2/span-safety.md).</span></span> <span data-ttu-id="9263d-118">本文档将介绍此建议所需的更改。</span><span class="sxs-lookup"><span data-stu-id="9263d-118">This document will describe the required changes to this document as a result of this proposal.</span></span> <span data-ttu-id="9263d-119">一旦接受作为已批准的功能，这些更改将合并到该文档中。</span><span class="sxs-lookup"><span data-stu-id="9263d-119">Once accepted as an approved feature these changes will be incorporated into that document.</span></span>

### <a name="provide-ref-fields"></a><span data-ttu-id="9263d-120">提供引用字段</span><span class="sxs-lookup"><span data-stu-id="9263d-120">Provide ref fields</span></span>
<span data-ttu-id="9263d-121">此语言将允许开发人员声明 `ref` 中的字段 `ref struct` 。</span><span class="sxs-lookup"><span data-stu-id="9263d-121">The language will allow developers to declare `ref` fields inside of a `ref struct`.</span></span> <span data-ttu-id="9263d-122">例如，封装大型可变 `struct` 实例或 `Span<T>` 在运行时以外的库中定义高性能类型时，这可能很有用。</span><span class="sxs-lookup"><span data-stu-id="9263d-122">This can be useful for example when encapsulating large mutable `struct` instances or defining high performance types like `Span<T>` in libraries besides the runtime.</span></span>

<span data-ttu-id="9263d-123">现在， `ref` 通过使用 `ByReference<T>` 运行时将其视为字段的类型，在运行时中完成的字段 `ref` 。</span><span class="sxs-lookup"><span data-stu-id="9263d-123">Today `ref` fields accomplished in the runtime by using the `ByReference<T>` type which the runtime treats effective as a `ref` field.</span></span> <span data-ttu-id="9263d-124">这意味着，只有运行时存储库才能充分利用 `ref` 字段（如行为），并且它的所有用途都需要手动验证安全。</span><span class="sxs-lookup"><span data-stu-id="9263d-124">This means though that only the runtime repository can take full advantage of `ref` field like behavior and all uses of it require manual verification of safety.</span></span> <span data-ttu-id="9263d-125">[此工作的动机](https://github.com/dotnet/runtime/issues/32060)部分是 `ByReference<T>` `ref` 在所有基本代码中删除并使用适当的字段。</span><span class="sxs-lookup"><span data-stu-id="9263d-125">Part of the [motivation for this work](https://github.com/dotnet/runtime/issues/32060) is to remove `ByReference<T>` and use proper `ref` fields in all code bases.</span></span> <span data-ttu-id="9263d-126">虽然允许字段声明的挑战部分 `ref` 涉及定义规则，以便 `Span<T>` 可以使用字段定义这些规则， `ref` 而不会中断与现有代码的兼容性。</span><span class="sxs-lookup"><span data-stu-id="9263d-126">The challenging part about allowing `ref` fields declarations though comes in defining rules such that `Span<T>` can be defined using `ref` fields without breaking compatibility with existing code.</span></span> 

<span data-ttu-id="9263d-127">在探讨这里的问题之前，请注意， `ref` 字段只需要对现有 span 安全规则进行少量的目标更改。</span><span class="sxs-lookup"><span data-stu-id="9263d-127">Before diving into the problems here it should be noted that `ref` fields only require a small number of targeted changes to our existing span safety rules.</span></span> <span data-ttu-id="9263d-128">在某些情况下，不支持新功能，而是合理化现有 `Span<T>` 数据的使用 `ref` 。</span><span class="sxs-lookup"><span data-stu-id="9263d-128">In some cases it's not even to support new features but to rationalize our existing `Span<T>` usage of `ref` data.</span></span> <span data-ttu-id="9263d-129">尽管我觉得很重要的是，应尽可能详细地传达这些更改的 "原因" 和提供支持示例，这部分是很重要的。</span><span class="sxs-lookup"><span data-stu-id="9263d-129">This section of the proposal is quite involved though because I feel it's important to communicate the "why" of these changes in as much detail as possible and providing supporting samples.</span></span> <span data-ttu-id="9263d-130">这是为了确保更改的声音，并使未来开发人员更好地了解此处所做的选择。</span><span class="sxs-lookup"><span data-stu-id="9263d-130">This is to both ensure the changes are sound as well as giving future developers a better understanding of the choices made here.</span></span>

<span data-ttu-id="9263d-131">要了解这一难题，我们首先考虑一下 `Span<T>` `ref` 支持字段的方式。</span><span class="sxs-lookup"><span data-stu-id="9263d-131">To understand the challenges here let's first consider how `Span<T>` will look once `ref` fields are supported.</span></span>

```cs
// This is the eventual definition of Span<T> once we add ref fields into the
// language
readonly ref struct Span<T>
{
    ref readonly T _field;
    int _length;

    // This constructor does not exist today however will be added as a
    // part of changing Span<T> to have ref fields. It is a convenient, and
    // safe, way to create a length one span over a stack value that today 
    // requires unsafe code.
    public Span(ref T value)
    {
        ref _field = ref value;
        _length = 1;
    }
}
```

<span data-ttu-id="9263d-132">此处定义的构造函数会出现问题，因为对于许多输入，其返回值必须具有受限的生存期。</span><span class="sxs-lookup"><span data-stu-id="9263d-132">The constructor defined here presents a problem because its return values must necessarily have restricted lifetimes for many inputs.</span></span> <span data-ttu-id="9263d-133">如果将本地参数传递 `ref` 到此构造函数中，并且返回的 `Span<T>` 必须具有本地声明范围的 *安全对转义* 范围，请注意。</span><span class="sxs-lookup"><span data-stu-id="9263d-133">Consider that if a local parameter is passed by `ref` into this constructor that the returned `Span<T>` must have a *safe-to-escape* scope of the local's declaration scope.</span></span>

```cs
Span<int> CreatingAndReturningSpan()
{
    int i = 42;

    // This must be an error in the new design because it stores stack 
    // state in the Span. 
    return new Span<int>(ref i);

    // This must be legal in the new design because it is legal today (it 
    // cannot store stack state)
    return new Span<int>(new int[] { });
}
```

<span data-ttu-id="9263d-134">同时，合法的方法是使用一个 `ref` 参数并返回一个 `Span<T>` 。</span><span class="sxs-lookup"><span data-stu-id="9263d-134">At the same time it is legal to have methods today which take a `ref` parameter and return a `Span<T>`.</span></span>  <span data-ttu-id="9263d-135">这些方法对新添加的构造函数具有很多相似性 `Span<T>` ： take `ref` ，返回 `Span<T>` 。</span><span class="sxs-lookup"><span data-stu-id="9263d-135">These methods bear a lot of similarity to the newly added `Span<T>` constructor: take a `ref`, return a `Span<T>`.</span></span> <span data-ttu-id="9263d-136">但这些方法的返回值的生存期永远不会受输入限制。</span><span class="sxs-lookup"><span data-stu-id="9263d-136">However the lifetime of the return value of these methods is never restricted by the inputs.</span></span>
<span data-ttu-id="9263d-137">现有的 span 安全规则将此类值视为有效地总是在封闭方法之外 *进行安全的转义* 。</span><span class="sxs-lookup"><span data-stu-id="9263d-137">The existing span safety rules consider such values as effectively always *safe-to-escape* outside the enclosing method.</span></span>

```cs
class ExistingScenarios
{
    Span<T> CreateSpan<T>(ref T p)
    {
        // The implementation of this method is irrelevant. From the point of
        // the consumer the returned value is always safe to return.
        ... 
    }

    Span<T> Examples<T>(ref T p, T[] array)
    {
        // Legal today
        return CreateSpan(ref p); 

        // Legal today, must remain legal
        T local = default;
        return CreateSpan(ref local);

        // Legal for any possible value that could be used as an argument
        return CreateSpan(...);
    }
}
```

<span data-ttu-id="9263d-138">上述所有示例均合法的原因是，在现有设计中，没有办法使返回 `Span<T>` 存储对方法调用的输入状态的引用。</span><span class="sxs-lookup"><span data-stu-id="9263d-138">The reason that all of the above samples are legal is because in the existing design there is no way for the return `Span<T>` to store a reference to the input state of the method call.</span></span> <span data-ttu-id="9263d-139">这是因为范围安全规则明确依赖于 `Span<T>` 没有使用参数的构造函数 `ref` ，并将其存储为字段。</span><span class="sxs-lookup"><span data-stu-id="9263d-139">This is because the span safety rules explicitly depend on `Span<T>` not having a constructor which takes a `ref` parameter and stores it as a field.</span></span> 

```cs
class ExistingAssumptions
{
    Span<T> CreateSpan<T>(ref T p)
    {
        // !!! Cannot happen today !!!
        // The existing span safety rules specifically call out that this method
        // cannot exist hence they can assume all returns from CreateSpan are
        // safe to return.
        return new Span<T>(ref p);
    }
}
```

<span data-ttu-id="9263d-140">我们为字段定义的规则 `ref` 必须确保 `Span<T>` 构造函数在捕获状态的情况下正确地限制构造对象的 *安全取消转义* 范围 `ref` 。</span><span class="sxs-lookup"><span data-stu-id="9263d-140">The rules we define for `ref` fields must ensure the `Span<T>` constructor properly restricts the *safe-to-escape* scope of constructed objects in the cases it captures `ref` state.</span></span> <span data-ttu-id="9263d-141">同时，还必须确保不会中断方法（如）的现有使用规则 `CreateSpan<T>` 。</span><span class="sxs-lookup"><span data-stu-id="9263d-141">At the same time it must ensure that we don't break the existing consumption rules for methods like `CreateSpan<T>`.</span></span> 

```cs
class GoalOfRefFields
{
    Span<T> CreateSpan<T>(ref T p)
    {
        // ERROR: the existing consumption rules for CreateSpan believe this 
        // can never happen hence we must continue to enforce that it cannot
        return new Span<T>(ref p);

        // Okay: this is legal today
        return new Span<int>(new int[] { });
    }

    Span<int> ConsumptionCompatibility()
    {
        // Okay: this is legal today and must remain legal.
        int local = 42;
        return CreateSpan(ref local);

        // Okay: the arguments don't actually matter here. Literally any value 
        // could be passed to this method and the return of it would still be 
        // *safe-to-escape* outside the enclosing method. 
        return CreateSpan(...);
    }
}
```

<span data-ttu-id="9263d-142">允许构造函数（如和）之间的这一张力 `Span<T>(ref T field)` 可确保与返回方法（如）的设计的兼容性 `ref struct` `CreateSpan<T>` 是字段设计中的关键点 `ref` 。</span><span class="sxs-lookup"><span data-stu-id="9263d-142">This tension between allowing constructors such as `Span<T>(ref T field)` and ensuring compatibility with `ref struct` returning methods like `CreateSpan<T>` is a key pivot point in the design of `ref` fields.</span></span>

<span data-ttu-id="9263d-143">为此，我们将更改构造函数调用的转义规则，该调用现在与方法调用相同，后者 `ref struct` **直接** 包含字段，如下所示 `ref` ：</span><span class="sxs-lookup"><span data-stu-id="9263d-143">To do this we will change the escape rules for a constructor invocation, which today are the same as method invocation, on a `ref struct` that **directly** contains a `ref` field as follows:</span></span>
- <span data-ttu-id="9263d-144">如果构造函数包含任何 `ref` `out` 或 `in` 参数，并且这些参数不是全部引用堆，则返回的安全取消 *转义* 将为当前作用域</span><span class="sxs-lookup"><span data-stu-id="9263d-144">If the constructor contains any `ref`, `out` or `in` parameters, and the arguments do not all refer to the heap, then the *safe-to-escape* of the return will be the current scope</span></span>
- <span data-ttu-id="9263d-145">否则，如果该构造函数包含任何参数，则返回的的 `ref struct` *安全对转义* 将为当前作用域</span><span class="sxs-lookup"><span data-stu-id="9263d-145">Else if the constructor contains any `ref struct` parameters then the *safe-to-escape* of the return will be the current scope</span></span>
- <span data-ttu-id="9263d-146">否则， *安全对转义* 将是方法范围外的</span><span class="sxs-lookup"><span data-stu-id="9263d-146">Else the *safe-to-escape* will be the outside the method scope</span></span>

<span data-ttu-id="9263d-147">让我们在示例上下文中查看这些规则，以更好地了解它们的影响。</span><span class="sxs-lookup"><span data-stu-id="9263d-147">Let's examine these rules in the context of samples to better understand their impact.</span></span>

```cs
ref struct RS
{
    ref int _field;

    public RS(int[] array, int index)
    {
        ref _field = ref array[index];
    }

    public RS(ref int i)
    {
        ref _field = ref i;
    }

    static RS CreateRS(ref int i)
    {
        // The implementation of this method is irrelevant to the safety rule
        // examples below. The returned value is always *safe-to-escape* outside
        // the enclosing method scope
    }

    static RS RuleExamples(ref int i, int[] array)
    {
        var rs1 = new RS(ref i);

        // ERROR by bullet 1: the safe-to-escape scope of 'rs1' is the current
        // scope.
        return rs1; 

        var rs2 = new RS(array, 0);

        // Okay by bullet 2: the safe-to-escape scope of 'rs2' is outside the 
        // method scope.
        return rs2; 

        int local = 42;

        // ERROR by bullet 1: the safe-to-escape scope is the current scope
        return new RS(ref local);
        return new RS(ref i);

        // Okay because rules for method calls have not changed. This is legal
        // today hence it must be legal in the presence of ref fields.
        return CreateRS(ref local);
        return CreateRS(ref i);
    }
}
```

<span data-ttu-id="9263d-148">需要注意的是，在通过使用构造函数链接的任何 `this` 情况下，都可以将构造函数调用视为构造函数调用。</span><span class="sxs-lookup"><span data-stu-id="9263d-148">It is important to note that for the purposes of the rule above any use of constructor chaining via `this` is considered a constructor invocation.</span></span> <span data-ttu-id="9263d-149">链式构造函数调用的结果被视为返回到原始构造函数，因此可以 *进行安全的转义* 规则。</span><span class="sxs-lookup"><span data-stu-id="9263d-149">The result of the chained constructor call is considered to be returning to the original constructor hence *safe-to-escape* rules come into play.</span></span> <span data-ttu-id="9263d-150">这对于避免如下的不安全示例十分重要：</span><span class="sxs-lookup"><span data-stu-id="9263d-150">That is important in avoiding unsafe examples like the following:</span></span>

```cs
ref struct RS1
{
    ref int _field;
    public RS1(ref int p)
    {
        ref _field = ref p;
    }
}

ref struct RS2
{
    RS1 _field;
    public RS2(RS1 p)
    {
        // Okay
        _field = p;
    }

    public RS2(ref int i)
    {
        // ERROR: The *safe-to-escape* scope of the constructor here is the 
        // current method scope while the *safe-to-escape* scope of `this` is
        // outside the current method scope hence this assignment is illegal
        _field = new RS1(ref i);
    }

    public RS2(ref int i)  
        // ERROR: the *safe-to-escape* return of :this the current method scope
        // but the 'this' parameter has a *safe-to-escape* outside the current
        // method scope
        : this(new RS1(ref i))
    {

    }
}
```

<span data-ttu-id="9263d-151">仅直接包含字段的构造函数规则限制 `ref struct` `ref` 是另一个重要的兼容性问题。</span><span class="sxs-lookup"><span data-stu-id="9263d-151">The limiting of the constructor rules to just `ref struct` that directly contain `ref` field is another important compatibility concern.</span></span> <span data-ttu-id="9263d-152">考虑到今天定义的大多数都 `ref struct` 间接包含 `Span<T>` 引用。</span><span class="sxs-lookup"><span data-stu-id="9263d-152">Consider that the majority of `ref struct` defined today indirectly contain `Span<T>` references.</span></span> <span data-ttu-id="9263d-153">这意味着通过扩展，它们会在 `ref` 采用字段后间接包含字段 `Span<T>` `ref` 。</span><span class="sxs-lookup"><span data-stu-id="9263d-153">That mean by extension they will indirectly contain `ref` fields once `Span<T>` adopts `ref` fields.</span></span> <span data-ttu-id="9263d-154">因此，请务必确保这些类型的构造函数的 *安全返回* 规则不会更改。</span><span class="sxs-lookup"><span data-stu-id="9263d-154">Hence it's important to ensure the *safe-to-return* rules of constructors on these types do not change.</span></span> <span data-ttu-id="9263d-155">这就是为什么限制仅适用于直接包含字段的类型的原因 `ref` 。</span><span class="sxs-lookup"><span data-stu-id="9263d-155">That is why the restrictions must only apply to types that directly contain a `ref` field.</span></span>

<span data-ttu-id="9263d-156">这种情况的示例。</span><span class="sxs-lookup"><span data-stu-id="9263d-156">Example of where this comes into play.</span></span>

```cs
ref struct Container
{
    LargeStruct _largeStruct;
    Span<int> _span;

    public Container(in LargeStruct largeStruct, Span<int> span)
    {
        _largeStruct = largeStruct;
        _span = span;
    }
}
```

<span data-ttu-id="9263d-157">与在 `CreateSpan<T>` 构造函数的 *安全取消转义* 之前的示例非常类似， `Container` 不受参数影响 `largeStruct` 。</span><span class="sxs-lookup"><span data-stu-id="9263d-157">Much like the `CreateSpan<T>` example before the *safe-to-escape* return of the `Container` constructor is not impacted by the `largeStruct` parameter.</span></span> <span data-ttu-id="9263d-158">如果将新的构造函数规则应用于此类型，则会中断与现有代码的兼容性。</span><span class="sxs-lookup"><span data-stu-id="9263d-158">If the new constructor rules were applied to this type then it would break compatibility with existing code.</span></span> <span data-ttu-id="9263d-159">现有规则还足以满足现有的构造函数的要求，因为这些规则会将 `ref` 字段存储到字段中来模拟字段 `Span<T>` 。</span><span class="sxs-lookup"><span data-stu-id="9263d-159">The existing rules are also sufficient for existing constructors to prevent them from simulating `ref` fields by storing them into `Span<T>` fields.</span></span>

```cs
ref struct RS4
{
    Span<int> _span;

    public RS4(Span<int> span)
    {
        // Legal today and the rules for this constructor invocation 
        // remain unchanged
        _span = span;
    }

    public RS4(ref int i)
    {
        // ERROR. Bullet 1 of the new constructor rules gives this newly created
        // Span<T> a *safe-to-escape* of the current scope. The 'this' parameter
        // though has a *safe-to-escape* outside the current method. Hence this
        // is illegal by assignment rules because it's assigning a smaller scope
        // to a larger one.
        _span = new Span(ref i);
    }

    // Legal today, must remain legal for compat. If the new constructor rules 
    // applied to 'RS4' though this would be illegal. This is why the new 
    // constructor rules have a restriction to directly defining a ref field
    // 
    // Only ref struct which explicitly opt into ref fields would see a breaking
    // change here.
    static RS4 CreateContainer(ref int i) => new RS4(ref i);
}
```

<span data-ttu-id="9263d-160">此设计还要求对字段生存期的规则进行扩展，因为目前的规则不会对其进行任何考虑。</span><span class="sxs-lookup"><span data-stu-id="9263d-160">This design also requires that the rules for field lifetimes be expanded as the rules today simply don't account for them.</span></span> <span data-ttu-id="9263d-161">需要特别注意的是，我们在此处展开规则不会定义新的行为，而是考虑长时间存在的行为。</span><span class="sxs-lookup"><span data-stu-id="9263d-161">It's important to note that our expansion of the rules here is not defining new behavior but rather accounting for behavior that has long existed.</span></span> <span data-ttu-id="9263d-162">`ref struct`有关使用完全确认和帐户的安全规则将 `ref struct` 包含 `ref` 状态，并且该 `ref` 状态将向使用者公开。</span><span class="sxs-lookup"><span data-stu-id="9263d-162">The safety rules around using `ref struct` fully acknowledge and account for the possibility that `ref struct` will contain `ref` state and that `ref` state will be exposed to consumers.</span></span> <span data-ttu-id="9263d-163">最明显的示例是中的索引器 `Span<T>` ：</span><span class="sxs-lookup"><span data-stu-id="9263d-163">The most prominent example of this is the indexer on `Span<T>`:</span></span>

``` cs
readonly ref struct Span<T>
{
    public ref T this[int index] => ...; 
}
```

<span data-ttu-id="9263d-164">这会直接公开 `ref` 此的内部状态 `Span<T>` 和 span 安全规则帐户。</span><span class="sxs-lookup"><span data-stu-id="9263d-164">This directly exposes the `ref` state inside `Span<T>` and the span safety rules account for this.</span></span> <span data-ttu-id="9263d-165">是实现为， `ByReference<T>` 还是 `ref` 字段重要到这些规则。</span><span class="sxs-lookup"><span data-stu-id="9263d-165">Whether that was implemented as `ByReference<T>` or `ref` fields is immaterial to those rules.</span></span> <span data-ttu-id="9263d-166">`ref`尽管允许字段，但我们必须定义其规则，以使它们适合的现有使用规则 `ref struct` 。</span><span class="sxs-lookup"><span data-stu-id="9263d-166">As a part of allowing `ref` fields though we must define their rules such that they fit into the existing consumption rules for `ref struct`.</span></span> <span data-ttu-id="9263d-167">特别要注意的是，它 *现在* 是合法的，以便将 `ref struct` 其状态返回 `ref` `ref` 给使用者。</span><span class="sxs-lookup"><span data-stu-id="9263d-167">Specifically this must account for the fact that it's legal *today* for a `ref struct` to return its `ref` state as `ref` to the consumer.</span></span> 

<span data-ttu-id="9263d-168">若要了解建议的更改，最好先查看有关方法调用的现有规则，并将其用于 *引用安全到转义* ，以及它们如何共同考虑 `ref struct` `ref` 当前公开状态：</span><span class="sxs-lookup"><span data-stu-id="9263d-168">To understand the proposed changes it's helpful to first review the existing rules for method invocation around *ref-safe-to-escape* and how they account for a `ref struct` exposing `ref` state today:</span></span>

> <span data-ttu-id="9263d-169">由引用返回的方法调用 e1 产生的左值。M (e2，... ) 是 *引用安全* 的最小作用域：</span><span class="sxs-lookup"><span data-stu-id="9263d-169">An lvalue resulting from a ref-returning method invocation e1.M(e2, ...) is *ref-safe-to-escape* the smallest of the following scopes:</span></span>
> 1. <span data-ttu-id="9263d-170">整个封闭方法</span><span class="sxs-lookup"><span data-stu-id="9263d-170">The entire enclosing method</span></span>
> 2. <span data-ttu-id="9263d-171">除接收方外 (所有 ref 和 out 自变量表达式的 *ref 安全对转义*) </span><span class="sxs-lookup"><span data-stu-id="9263d-171">The *ref-safe-to-escape* of all ref and out argument expressions (excluding the receiver)</span></span>
> 3. <span data-ttu-id="9263d-172">对于方法的每个 in 参数，如果有一个对应的表达式是左值，则为它的 *ref 安全到转义*，否则为最接近的封闭范围</span><span class="sxs-lookup"><span data-stu-id="9263d-172">For each in parameter of the method, if there is a corresponding expression that is an lvalue, its *ref-safe-to-escape*, otherwise the nearest enclosing scope</span></span>
> 4. <span data-ttu-id="9263d-173">所有参数表达式的 *安全对转义* (包括接收方) </span><span class="sxs-lookup"><span data-stu-id="9263d-173">the *safe-to-escape* of all argument expressions (including the receiver)</span></span>

<span data-ttu-id="9263d-174">第四项在 `ref struct` 向调用方公开状态的周围提供关键安全点 `ref` 。</span><span class="sxs-lookup"><span data-stu-id="9263d-174">The fourth item provides the critical safety point around a `ref struct` exposing `ref` state to callers.</span></span> <span data-ttu-id="9263d-175">当 `ref` 中存储的状态 `ref struct` 引用堆栈时，的 *安全对转义* 范围 `ref struct` 最多为定义所引用的状态的范围。</span><span class="sxs-lookup"><span data-stu-id="9263d-175">When the `ref` state stored in a `ref struct` refers to the stack then the *safe-to-escape* scope for that `ref struct` will be at most the scope which defines the state being referred to.</span></span> <span data-ttu-id="9263d-176">因此，将对的调用的 *ref 安全到转义* 范围限制在 `ref struct` 接收方的 *安全* 取消转义范围内，确保生存期是正确的。</span><span class="sxs-lookup"><span data-stu-id="9263d-176">Hence limiting the *ref-safe-to-escape* of invocations of a `ref struct` to the *safe-to-escape* scope of the receiver ensures the lifetimes are correct.</span></span>

<span data-ttu-id="9263d-177">请考虑作为索引器的示例 `Span<T>` `ref` `ref` 。</span><span class="sxs-lookup"><span data-stu-id="9263d-177">Consider as an example the indexer on `Span<T>` which is returning `ref` fields by `ref` today.</span></span> <span data-ttu-id="9263d-178">此处的第四项是在此处提供安全性的内容：</span><span class="sxs-lookup"><span data-stu-id="9263d-178">The fourth item here is what provides the safety here:</span></span>

```cs
ref int Examples()
{
    Span<int> s1 = stackalloc int[5];
    // ERROR: illegal because the *safe-to-escape* scope of `s1` is the current
    // method scope hence that limits the *ref-safe-to-escape" to the current
    // method scope as well.
    return ref s1[0];

    // SUCCESS: legal because the *safe-to-escape* scope of `s2` is outside
    // the current method scope hence the *ref-safe-to-escape* is as well
    Span<int> s2 = default;
    return ref s2[0];
}
```

<span data-ttu-id="9263d-179">若要对 `ref` 字段进行考虑，字段的 *ref 安全到转义* 规则将调整为以下内容：</span><span class="sxs-lookup"><span data-stu-id="9263d-179">To account for `ref` fields the *ref-safe-to-escape* rules for fields will be adjusted to the following:</span></span>

> <span data-ttu-id="9263d-180">引用字段的引用的左值 (按引用 *安全地转义*) ，如下所示：</span><span class="sxs-lookup"><span data-stu-id="9263d-180">An lvalue designating a reference to a field, e.F, is *ref-safe-to-escape* (by reference) as follows:</span></span>
> - <span data-ttu-id="9263d-181">如果 `F` 是一个 `ref` 字段，并且 `e` 为 `this` ，则从封闭方法对其进行 *引用安全的转义* 。</span><span class="sxs-lookup"><span data-stu-id="9263d-181">If `F` is a `ref` field and `e` is `this`, it is *ref-safe-to-escape* from the enclosing method.</span></span>
> - <span data-ttu-id="9263d-182">否则，如果 `F` 是 `ref` 字段，它的 *ref 安全到转义* 范围是的 *安全对转义* 范围 `e` 。</span><span class="sxs-lookup"><span data-stu-id="9263d-182">Else if `F` is a `ref` field its *ref-safe-to-escape* scope is the *safe-to-escape* scope of `e`.</span></span>
> - <span data-ttu-id="9263d-183">否则 `e` ，如果为引用类型，则它是来自封闭方法的 *引用安全的转义* 。</span><span class="sxs-lookup"><span data-stu-id="9263d-183">Else if `e` is of a reference type, it is *ref-safe-to-escape* from the enclosing method.</span></span>
> - <span data-ttu-id="9263d-184">否则，将从的 *ref 安全到转义* 获取其 *引用安全的转义* `e` 。</span><span class="sxs-lookup"><span data-stu-id="9263d-184">Else its *ref-safe-to-escape* is taken from the *ref-safe-to-escape* of `e`.</span></span>

<span data-ttu-id="9263d-185">这会显式地允许 `ref` `ref` 从（而非普通字段）返回字段， `ref struct` (稍后将介绍) 。</span><span class="sxs-lookup"><span data-stu-id="9263d-185">This explicitly allows for `ref` fields being returned as `ref` from a `ref struct` but not normal fields (that will be covered later).</span></span> 

```cs
ref struct RS
{
    ref int _refField;
    int _field;

    // Okay: this falls into bullet one above. 
    public ref int Prop1 => ref _refField;

    // ERROR: This is bullet four above and the *ref-safe-to-escape* of `this`
    // in a `struct` is the current method scope.
    public ref int Prop2 => ref _field;

    public RS(int[] array)
    {
        ref _refField = ref array[0];
    }

    public RS(ref int i)
    {
        ref _refField = ref i;
    }

    public RS CreateRS() => ...;

    public ref int M1(RS rs)
    {
        ref int local1 = ref rs.Prop1;

        // Okay: this falls into bullet two above and the *safe-to-escape* of
        // `rs` is outside the current method scope. Hence the *ref-safe-to-escape*
        // of `local1` is outside the current method scope.
        return ref local;

        // Okay: this falls into bullet two above and the *safe-to-escape* of
        // `rs` is outside the current method scope. Hence the *ref-safe-to-escape*
        // of `local1` is outside the current method scope.
        //
        // In fact in this scenario you can guarantee that the value returned
        // from Prop1 must exist on the heap. 
        RS local2 = CreateRS();
        return ref local2.Prop1;

        // ERROR: the *safe-to-escape* of `local4` here is the current method 
        // scope by the revised constructor rules. This falls into bullet two 
        // above and hence based on that allowed scope.
        int local3 = 42;
        var local4 = new RS(ref local3);
        return ref local4.Prop1;

    }
}
```

<span data-ttu-id="9263d-186">还需要对分配的规则进行调整，以考虑 `ref` 字段。</span><span class="sxs-lookup"><span data-stu-id="9263d-186">The rules for assignment also need to be adjusted to account for `ref` fields.</span></span>
<span data-ttu-id="9263d-187">此设计仅允许 `ref` `ref` 在对象构造过程中或在已知值引用堆时分配字段。</span><span class="sxs-lookup"><span data-stu-id="9263d-187">This design only allows for `ref` assignment of a `ref` field during object construction or when the value is known to refer to the heap.</span></span> <span data-ttu-id="9263d-188">对象构造包括在声明类型的构造函数中、 `init` 访问器内部和对象初始值设定项表达式内。</span><span class="sxs-lookup"><span data-stu-id="9263d-188">Object construction includes in the constructor of the declaring type, inside `init` accessors and inside object initializer expressions.</span></span> <span data-ttu-id="9263d-189">`ref`在这种情况下，要分配给字段的更多 `ref` 内容必须具有与字段接收方相同的 *ref 安全到转义*：</span><span class="sxs-lookup"><span data-stu-id="9263d-189">Further the `ref` being assigned to the `ref` field in this case must have *ref-safe-to-escape* greater than the receiver of the field:</span></span> 

- <span data-ttu-id="9263d-190">构造函数：值必须在构造函数之外进行 *引用安全的转义*</span><span class="sxs-lookup"><span data-stu-id="9263d-190">Constructors: The value must be *ref-safe-to-escape* outside the constructor</span></span>
- <span data-ttu-id="9263d-191">`init` 取值函数：值限制为已知用于引用堆的值，因为访问器不能有 `ref` 参数</span><span class="sxs-lookup"><span data-stu-id="9263d-191">`init` accessors:  The value limited to values that are known to refer to the heap as accessors can't have `ref` parameters</span></span>
- <span data-ttu-id="9263d-192">对象初始值设定项：值可具有任何 *ref 安全到转义* 值，因为这会将源纳入现有规则来对构造对象 *进行安全转义* 。</span><span class="sxs-lookup"><span data-stu-id="9263d-192">object initializers: The value can have any *ref-safe-to-escape* value as this will feed into the calculation of the *safe-to-escape* of the constructed object by existing rules.</span></span>

<span data-ttu-id="9263d-193">`ref`当已知值引用堆时，只能在构造函数外部分配字段。</span><span class="sxs-lookup"><span data-stu-id="9263d-193">A `ref` field can only be assigned outside a constructor when the value is known to refer to the heap.</span></span> <span data-ttu-id="9263d-194">这是允许的，因为它在分配位置是安全的 (满足字段分配规则，以确保分配的值的生存期至少与接收方) 的生存期相同，并且不需要对现有方法调用规则进行任何更新。</span><span class="sxs-lookup"><span data-stu-id="9263d-194">That is allowed because it is both safe at the assignment location (meets the field assignment rules for ensuring the value being assigned has a lifetime at least as large as the receiver) as well as requires no updates to the existing method invocation rules.</span></span> 

<span data-ttu-id="9263d-195">此设计不允许 `ref` 在对象构造之外进行常规字段赋值，因为存在对生存期的现有限制。</span><span class="sxs-lookup"><span data-stu-id="9263d-195">This design does not allow for general `ref` field assignment outside object construction due to existing limitations on lifetimes.</span></span> <span data-ttu-id="9263d-196">具体来说，它对如下方案提出挑战：</span><span class="sxs-lookup"><span data-stu-id="9263d-196">Specifically it poses challenges for scenarios like the following:</span></span>

```cs
ref struct SmallSpan
{
    public ref int _field;

    // Notice once again we're back at the same problem as the original 
    // CreateSpan method: a method returning a ref struct and taking a ref
    // parameter
    SmallSpan TrickyRefAssignment(ref int i)
    {
        // *safe-to-escape* is outside the current method by current rules.
        SmallSpan s = default;

        // The *ref-safe-to-escape* of 'i' is the same as the *safe-to-escape*
        // of 's' hence most assignment rules would allow it.
        ref s._field = ref i;

        // ERROR: this must be disallowed for the exact same reasons we can't 
        // return a Span<T> wrapping the parameter: the consumption rules
        // believe such state smuggling cannot exist
        return s;
    }

    SmallSpan SafeRefAssignment()
    {
        int[] array = new int[] { 42, 13 };
        SmallSpan s = default;

        // Okay: the value being assigned here is known to refer to the heap 
        // hence it is allowed by our rules above because it requires no changes
        // to existing method invocation rules (hence preserves compat)
        ref s._field = ref array[i];

        return s;
    }

    SmallSpan BadUsage()
    {
        // Legal today and must remain legal (and safe)
        int i = 0;
        return TrickyRefAssignment(ref i);
    }
}
```

<span data-ttu-id="9263d-197">有一些设计选项可让你更灵活地 `ref` 重新分配字段。</span><span class="sxs-lookup"><span data-stu-id="9263d-197">There are designs choices we could make to allow more flexible `ref` re-assignment of fields.</span></span> <span data-ttu-id="9263d-198">例如，如果我们知道接收方的 *安全取消转义* 范围不在当前方法范围内，则可以使用此方法。</span><span class="sxs-lookup"><span data-stu-id="9263d-198">For example it could be allowed in cases where we knew the receiver had a *safe-to-escape* scope that was not outside the current method scope.</span></span> <span data-ttu-id="9263d-199">此外，我们还可以提供语法，使此类向下的值更易于声明：本质上是具有 *安全到转义* 范围的值限制为当前方法。</span><span class="sxs-lookup"><span data-stu-id="9263d-199">Further we could provide syntax for making such downward facing values easier to declare: essentially values that have *safe-to-escape* scopes restricted to the current method.</span></span> <span data-ttu-id="9263d-200">[此处](https://github.com/dotnet/csharplang/discussions/1130)) 讨论此类设计。</span><span class="sxs-lookup"><span data-stu-id="9263d-200">Such a design is discussed [here](https://github.com/dotnet/csharplang/discussions/1130)).</span></span>
<span data-ttu-id="9263d-201">但是，这种规则的额外复杂性似乎并不值得这种情况。</span><span class="sxs-lookup"><span data-stu-id="9263d-201">However extra complexity of such rules do not seem to be worth the limited cases this enables.</span></span> <span data-ttu-id="9263d-202">如果有引人注目的示例，我们可以重新访问此决定。</span><span class="sxs-lookup"><span data-stu-id="9263d-202">Should compelling samples come up we can revisit this decision.</span></span>

<span data-ttu-id="9263d-203">这意味着这些 `ref` 字段在很大程度上是实际的 `ref readonly` 。</span><span class="sxs-lookup"><span data-stu-id="9263d-203">This means though that `ref` fields are largely in practice `ref readonly`.</span></span> <span data-ttu-id="9263d-204">对象初始值设定项和已知为引用堆的值时的主要异常。</span><span class="sxs-lookup"><span data-stu-id="9263d-204">The main exceptions being object initializers and when the value is known to refer to the heap.</span></span>

<span data-ttu-id="9263d-205">`ref`将使用签名将字段发送到元数据中 `ELEMENT_TYPE_BYREF` 。</span><span class="sxs-lookup"><span data-stu-id="9263d-205">A `ref` field will be emitted into metadata using the `ELEMENT_TYPE_BYREF` signature.</span></span> <span data-ttu-id="9263d-206">这与发出局部变量或参数的方式没有什么不同 `ref` `ref` 。</span><span class="sxs-lookup"><span data-stu-id="9263d-206">This is no different than how we emit `ref` locals or `ref` arguments.</span></span> <span data-ttu-id="9263d-207">例如， `ref int _field` 将作为发出 `ELEMENT_TYPE_BYREF ELEMENT_TYPE_I4` 。</span><span class="sxs-lookup"><span data-stu-id="9263d-207">For example `ref int _field` will be emitted as `ELEMENT_TYPE_BYREF ELEMENT_TYPE_I4`.</span></span> <span data-ttu-id="9263d-208">这将要求我们更新 ECMA335 以允许此项，但这应该是一种直接的转发。</span><span class="sxs-lookup"><span data-stu-id="9263d-208">This will require us to update ECMA335 to allow this entry but this should be rather straight forward.</span></span>

<span data-ttu-id="9263d-209">开发人员可以 `ref struct` `ref` 使用表达式继续使用字段初始化， `default` 在这种情况下，所有声明 `ref` 字段都将具有值 `null` 。</span><span class="sxs-lookup"><span data-stu-id="9263d-209">Developers can continue to initialize a `ref struct` with a `ref` field using the `default` expression in which case all declared `ref` fields will have the value `null`.</span></span> <span data-ttu-id="9263d-210">尝试使用此类字段将导致 `NullReferenceException` 引发。</span><span class="sxs-lookup"><span data-stu-id="9263d-210">Any attempt to use such fields will result in a `NullReferenceException` being thrown.</span></span>

```cs
struct S1 
{
    public ref int Value;
}

S1 local = default;
local.Value.ToString(); // throws NullReferenceException
```

<span data-ttu-id="9263d-211">尽管 c # 语言在 `ref` `null` 运行时级别上非常合理，但它具有定义完善的语义。</span><span class="sxs-lookup"><span data-stu-id="9263d-211">While the C# language pretends that a `ref` cannot be `null` this is legal at the runtime level and has well defined semantics.</span></span> <span data-ttu-id="9263d-212">`ref`向其类型引入字段的开发人员需要了解这种可能性，因此 **强烈** 建议不要将此详细信息泄漏到使用代码。</span><span class="sxs-lookup"><span data-stu-id="9263d-212">Developers who introduce `ref` fields into their types need to be aware of this possibility and should be **strongly** discouraged from leaking this detail into consuming code.</span></span> <span data-ttu-id="9263d-213">相反， `ref` 应使用 [运行时帮助](https://github.com/dotnet/runtime/pull/40008) 器将字段验证为非 null，并在 `struct` 不正确使用未初始化的时引发。</span><span class="sxs-lookup"><span data-stu-id="9263d-213">Instead `ref` fields should be validated as non-null using the [runtime helpers](https://github.com/dotnet/runtime/pull/40008) and throwing when an uninitialized `struct` is used incorrectly.</span></span>

```cs
struct S1 
{
    private ref int Value;

    public int GetValue()
    {
        if (System.Runtime.CompilerServices.Unsafe.IsNullRef(ref Value))
        {
            throw new InvalidOperationException(...);
        }

        return Value;
    }
}
```

<span data-ttu-id="9263d-214">杂项说明：</span><span class="sxs-lookup"><span data-stu-id="9263d-214">Misc Notes:</span></span>
- <span data-ttu-id="9263d-215">`ref`字段只能在中声明`ref struct`</span><span class="sxs-lookup"><span data-stu-id="9263d-215">A `ref` field can only be declared inside of a `ref struct`</span></span> 
- <span data-ttu-id="9263d-216">`ref`不能声明字段`static`</span><span class="sxs-lookup"><span data-stu-id="9263d-216">A `ref` field cannot be declared `static`</span></span>
- <span data-ttu-id="9263d-217">`ref`只能 `ref` 在声明类型的构造函数中分配字段。</span><span class="sxs-lookup"><span data-stu-id="9263d-217">A `ref` field can only be `ref` assigned in the constructor of the declaring type.</span></span>
- <span data-ttu-id="9263d-218">引用程序集生成过程必须保留中是否存在 `ref` 字段 `ref struct`</span><span class="sxs-lookup"><span data-stu-id="9263d-218">The reference assembly generation process must preserve the presence of a `ref` field inside a `ref struct`</span></span> 
- <span data-ttu-id="9263d-219">`ref readonly struct`必须将其 `ref` 字段声明为`ref readonly`</span><span class="sxs-lookup"><span data-stu-id="9263d-219">A `ref readonly struct` must declare its `ref` fields as `ref readonly`</span></span>
- <span data-ttu-id="9263d-220">必须按照本文档中所述更新构造函数、字段和分配的 span 安全规则。</span><span class="sxs-lookup"><span data-stu-id="9263d-220">The span safety rules for constructors, fields and assignment must be updated as outlined in this document.</span></span>
- <span data-ttu-id="9263d-221">Span 安全规则需要包括 `ref` "引用堆" 值的定义。</span><span class="sxs-lookup"><span data-stu-id="9263d-221">The span safety rules need to include the definition of `ref` values that "refer to the heap".</span></span> 

### <a name="provide-struct-this-escape-annotation"></a><span data-ttu-id="9263d-222">提供此转义批注的结构</span><span class="sxs-lookup"><span data-stu-id="9263d-222">Provide struct this escape annotation</span></span>
<span data-ttu-id="9263d-223">中范围的规则限制了 `this` `struct` 当前方法的 *ref 安全到转义* 范围。</span><span class="sxs-lookup"><span data-stu-id="9263d-223">The rules for the scope of `this` in a `struct` limit the *ref-safe-to-escape* scope to the current method.</span></span> <span data-ttu-id="9263d-224">这意味着 `this` ，也不能通过引用调用方返回任何字段。</span><span class="sxs-lookup"><span data-stu-id="9263d-224">That means neither `this`, nor any of its fields can return by reference to the caller.</span></span>

```cs
struct S
{
    int _field;
    // Error: this, and hence _field, can't return by ref
    public ref int Prop => ref _field;
}
```

<span data-ttu-id="9263d-225">使用转义的转义本质上没有任何错误 `struct` `this` 。</span><span class="sxs-lookup"><span data-stu-id="9263d-225">There is nothing inherently wrong with a `struct` escaping `this` by reference.</span></span>
<span data-ttu-id="9263d-226">相反，此规则的理由是，它在和的可用性之间达成了 `struct` 平衡 `interfaces` 。</span><span class="sxs-lookup"><span data-stu-id="9263d-226">Instead the justification for this rule is that it strikes a balance between the usability of `struct` and `interfaces`.</span></span> <span data-ttu-id="9263d-227">如果 `struct` 可以通过引用进行转义，则会 `this` 显著减少 `ref` 接口中的返回使用。</span><span class="sxs-lookup"><span data-stu-id="9263d-227">If a `struct` could escape `this` by reference then it would significantly reduce the use of `ref` returns in interfaces.</span></span>

```cs
interface I1
{
    ref int Prop { get; }
}

struct S1 : I1
{
    int _field;
    public ref int Prop => _ref field;

    // When T is a struct type, like S1 this would end up returning a reference
    // to the parameter
    static ref int M<T>(T p) where T : I1 => ref p.Prop;
}
```

<span data-ttu-id="9263d-228">这里的理由是合理的，但它也在 `struct` 不参与接口调用的成员上引入了不必要的摩擦。</span><span class="sxs-lookup"><span data-stu-id="9263d-228">The justification here is reasonable but it also introduces unnecessary friction on `struct` members that don't participate in interface invocations.</span></span> 

<span data-ttu-id="9263d-229">在此处进行更改时，必须记住一个关键的兼容性方案：</span><span class="sxs-lookup"><span data-stu-id="9263d-229">One key compatibility scenario that we have to keep in mind when approaching changes here is the following:</span></span>

```cs
struct S1
{
    ref int GetValue() => ...
}

class Example
{
    ref int M()
    {
        // Okay: this is always allowed no matter how `local` is initialized
        S1 local = default;
        return local.GetValue();
    }
}
```

<span data-ttu-id="9263d-230">这样做的原因是，今天返回的安全规则 `ref` 不考虑 (的生存期， `this` 因为它不能) 的 `ref` 内部状态返回。</span><span class="sxs-lookup"><span data-stu-id="9263d-230">This works because the safety rules for `ref` return today do not take into account the lifetime of `this` (because it can't return a `ref` to internal state).</span></span> <span data-ttu-id="9263d-231">这意味着， `ref` 从中返回的返回 `struct` 可在封闭方法范围外返回，但有一些 `ref` 参数或 `ref struct` 不是封闭方法范围外的不 *安全转义* 的情况除外。</span><span class="sxs-lookup"><span data-stu-id="9263d-231">This means that `ref` returns from a `struct` can return outside the enclosing method scope except in cases where there are `ref` parameters or a `ref struct` which is not *safe-to-escape* outside the enclosing method scope.</span></span> <span data-ttu-id="9263d-232">因此，此处的解决方案并不像允许 `ref` 在非接口方法中返回字段那样简单。</span><span class="sxs-lookup"><span data-stu-id="9263d-232">Hence the solution here is not as easy as allowing `ref` return of fields in non-interface methods.</span></span>

<span data-ttu-id="9263d-233">为了消除这种摩擦，语言将提供属性 `[ThisRefEscapes]` 。</span><span class="sxs-lookup"><span data-stu-id="9263d-233">To remove this friction the language will provide the attribute `[ThisRefEscapes]`.</span></span>
<span data-ttu-id="9263d-234">如果将此特性应用于实例方法，则或参数的实例属性或实例访问器 `struct` `ref struct` 将被视为在 `this` 封闭方法之外的 *引用安全到转义* 。</span><span class="sxs-lookup"><span data-stu-id="9263d-234">When this attribute is applied to an instance method, instance property or instance accessor of a `struct` or `ref struct` then the `this` parameter will be considered *ref-safe-to-escape* outside the enclosing method.</span></span>

<span data-ttu-id="9263d-235">这样就可以在定义中提供更大的灵活性， `struct` 因为它们可以开始返回 `ref` 到其字段。</span><span class="sxs-lookup"><span data-stu-id="9263d-235">This allows for greater flexibility in `struct` definitions as they can begin returning `ref` to their fields.</span></span> <span data-ttu-id="9263d-236">这允许类型类似于 `FrugalList<T>` ：</span><span class="sxs-lookup"><span data-stu-id="9263d-236">That allows for types like `FrugalList<T>`:</span></span>

```cs
struct FrugalList<T>
{
    private T _item0;
    private T _item1;
    private T _item2;

    public int Count = 3;

    public ref T this[int index]
    {
        [ThisRefEscapes]
        get
        {
            switch (index)
            {
                case 0: return ref _item1;
                case 1: return ref _item2;
                case 2: return ref _item3;
                default: throw null;
            }
        }
    }
}
```

<span data-ttu-id="9263d-237">这自然是由 span 安全规范中的现有规则允许除直接字段外返回可传递字段。</span><span class="sxs-lookup"><span data-stu-id="9263d-237">This will naturally, by the existing rules in the span safety spec, allow for returning transitive fields in addition to direct fields.</span></span>

```cs
struct ListWithDefault<T>
{
    private FrugalList<T> _list;
    private T _default;

    public ref T this[int index]
    {
        [ThisRefEscapes]
        get
        {
            if (index >= _list.Count)
            {
                return ref _default;
            }

            return ref _list[index];
        }
    }
}
```

<span data-ttu-id="9263d-238">包含特性的成员 `[ThisRefEscapes]` 不能用于实现接口成员。</span><span class="sxs-lookup"><span data-stu-id="9263d-238">Members which contain the `[ThisRefEscapes]` attribute cannot be used to implement interface members.</span></span> <span data-ttu-id="9263d-239">这会在调用站点隐藏成员的生存期 `interface` ，并导致不正确的生存期计算。</span><span class="sxs-lookup"><span data-stu-id="9263d-239">This would hide the lifetime nature of the member at the `interface` call site and would lead to incorrect lifetime calculations.</span></span>

<span data-ttu-id="9263d-240">为了对此进行更改，将更新 span 安全文档的 "Parameters" 部分，包括以下内容：</span><span class="sxs-lookup"><span data-stu-id="9263d-240">To account for this change the "Parameters" section of the span safety document will be updated to include the following:</span></span>

- <span data-ttu-id="9263d-241">如果该参数是 `this` 某个类型的参数 `struct` ，则它是对封闭方法的顶级范围的 *引用安全对的转义* ，除非该方法使用进行批注， `[ThisRefEscapes]` 在这种情况下，该方法在封闭方法之外是 *引用安全的转义* 。</span><span class="sxs-lookup"><span data-stu-id="9263d-241">If the parameter is the `this` parameter of a `struct` type, it is *ref-safe-to-escape* to the top scope of the enclosing method unless the method is annotated with `[ThisRefEscapes]` in which case it is *ref-safe-to-escape* outside the enclosing method.</span></span>

<span data-ttu-id="9263d-242">杂项说明：</span><span class="sxs-lookup"><span data-stu-id="9263d-242">Misc Notes:</span></span>
- <span data-ttu-id="9263d-243">标记为的成员无法 `[ThisRefEscapes]` 实现 `interface` 方法或 `overrides`</span><span class="sxs-lookup"><span data-stu-id="9263d-243">A member marked as `[ThisRefEscapes]` can not implement an `interface` method or be `overrides`</span></span>
- <span data-ttu-id="9263d-244">标记为的成员 `[ThisRefEscapes]` 将随 `modreq` 该属性发出。</span><span class="sxs-lookup"><span data-stu-id="9263d-244">A member marked as `[ThisRefEscapes]` will be emitted with a `modreq` on that attribute.</span></span>
- <span data-ttu-id="9263d-245">`RefEscapesAttribute`将在命名空间中定义 `System.Runtime.CompilerServices` 。</span><span class="sxs-lookup"><span data-stu-id="9263d-245">The `RefEscapesAttribute` will be defined in the `System.Runtime.CompilerServices` namespace.</span></span>

### <a name="safe-fixed-size-buffers"></a><span data-ttu-id="9263d-246">安全固定大小缓冲区</span><span class="sxs-lookup"><span data-stu-id="9263d-246">Safe fixed size buffers</span></span>
<span data-ttu-id="9263d-247">此语言将放宽对固定大小的数组的限制，使它们可以在安全代码中声明，而元素类型可以是托管或非托管。</span><span class="sxs-lookup"><span data-stu-id="9263d-247">The language will relax the restrictions on fixed sized arrays such that they can be declared in safe code and the element type can be managed or unmanaged.</span></span> <span data-ttu-id="9263d-248">这会使类型类似于以下法律：</span><span class="sxs-lookup"><span data-stu-id="9263d-248">This will make types like the following legal:</span></span>

```cs
internal struct CharBuffer
{
    internal fixed char Data[128];
}
```

<span data-ttu-id="9263d-249">这些声明非常类似于 `unsafe` 计数器部分，将定义 `N` 包含类型中的元素序列。</span><span class="sxs-lookup"><span data-stu-id="9263d-249">These declarations, much like their `unsafe` counter parts, will define a sequence of `N` elements in the containing type.</span></span> <span data-ttu-id="9263d-250">这些成员可以使用索引器进行访问，也可以转换为 `Span<T>` 和 `ReadOnlySpan<T>` 实例。</span><span class="sxs-lookup"><span data-stu-id="9263d-250">These members can be accessed with an indexer and can also be converted to `Span<T>` and `ReadOnlySpan<T>` instances.</span></span>

<span data-ttu-id="9263d-251">在对类型的 `fixed` 缓冲区进行索引时， `T` 必须考虑容器的 `readonly` 状态。</span><span class="sxs-lookup"><span data-stu-id="9263d-251">When indexing into a `fixed` buffer of type `T` the `readonly` state of the container must be taken into account.</span></span>  <span data-ttu-id="9263d-252">如果容器为，则 `readonly` 索引器将返回， `ref readonly T` 否则返回 `ref T` 。</span><span class="sxs-lookup"><span data-stu-id="9263d-252">If the container is `readonly` then the indexer returns `ref readonly T` else it returns `ref T`.</span></span> 

<span data-ttu-id="9263d-253">`fixed`不使用索引器访问缓冲区没有自然类型，但它可转换为 `Span<T>` 类型。</span><span class="sxs-lookup"><span data-stu-id="9263d-253">Accessing a `fixed` buffer without an indexer has no natural type however it is convertible to `Span<T>` types.</span></span> <span data-ttu-id="9263d-254">如果容器为 `readonly` 缓冲区隐式转换为 `ReadOnlySpan<T>` ，则它可以隐式转换为 `Span<T>` 或 `ReadOnlySpan<T>` (`Span<T>` 转换被视为 *更好*) 。</span><span class="sxs-lookup"><span data-stu-id="9263d-254">In the case the container is `readonly` the buffer is implicitly convertible to `ReadOnlySpan<T>`, else it can implicitly convert to `Span<T>` or `ReadOnlySpan<T>` (the `Span<T>` conversion is considered *better*).</span></span> 

<span data-ttu-id="9263d-255">生成的 `Span<T>` 实例的长度将等于缓冲区上声明的大小 `fixed` 。</span><span class="sxs-lookup"><span data-stu-id="9263d-255">The resulting `Span<T>` instance will have a length equal to the size declared on the `fixed` buffer.</span></span> <span data-ttu-id="9263d-256">返回的值的 *安全取消转义* 范围将等于容器的 *安全对转义* 范围。</span><span class="sxs-lookup"><span data-stu-id="9263d-256">The *safe-to-escape* scope of the returned value will be equal to the *safe-to-escape* scope of the container.</span></span>

<span data-ttu-id="9263d-257">对于 `fixed` 类型中元素类型为的每个声明，该 `T` 语言将生成 `get` 返回类型为的唯一索引器方法 `ref T` 。</span><span class="sxs-lookup"><span data-stu-id="9263d-257">For each `fixed` declaration in a type where the element type is `T` the language will generate a corresponding `get` only indexer method whose return type is `ref T`.</span></span> <span data-ttu-id="9263d-258">索引器将用属性进行批注 `[ThisRefEscapes]` ，因为该实现将返回声明类型的字段。</span><span class="sxs-lookup"><span data-stu-id="9263d-258">The indexer will be annotated with the `[ThisRefEscapes]` attribute as the implementation will be returning fields of the declaring type.</span></span> <span data-ttu-id="9263d-259">成员的可访问性将与字段的可访问性匹配 `fixed` 。</span><span class="sxs-lookup"><span data-stu-id="9263d-259">The accessibility of the member will match the accessibility on the `fixed` field.</span></span>

<span data-ttu-id="9263d-260">例如，索引器的签名 `CharBuffer.Data` 将如下所示：</span><span class="sxs-lookup"><span data-stu-id="9263d-260">For example, the signature of the indexer for `CharBuffer.Data` will be the following:</span></span>

```cs
[ThisRefEscapes]
internal ref char <>DataIndexer(int index) => ...;
```

<span data-ttu-id="9263d-261">如果提供的索引位于数组的已声明边界之外， `fixed` 则 `IndexOutOfRangeException` 将引发。</span><span class="sxs-lookup"><span data-stu-id="9263d-261">If the provided index is outside the declared bounds of the `fixed` array then an `IndexOutOfRangeException` will be thrown.</span></span> <span data-ttu-id="9263d-262">如果提供了常数值，则会将其替换为对相应元素的直接引用。</span><span class="sxs-lookup"><span data-stu-id="9263d-262">In the case a constant value is provided then it will be replaced with a direct reference to the appropriate element.</span></span> <span data-ttu-id="9263d-263">除非常量不在声明的边界内，否则将发生编译时错误。</span><span class="sxs-lookup"><span data-stu-id="9263d-263">Unless the constant is outside the declared bounds in which case a compile time error would occur.</span></span>

<span data-ttu-id="9263d-264">还将为每个 `fixed` 通过值和操作提供的缓冲区生成命名访问 `get` 器 `set` 。</span><span class="sxs-lookup"><span data-stu-id="9263d-264">There will also be a named accessor generated for each `fixed` buffer that provides by value `get` and `set` operations.</span></span> <span data-ttu-id="9263d-265">这意味着， `fixed` 通过具有 `ref` 访问器以及 byval `get` 和操作，缓冲区将更接近于现有数组语义 `set` 。</span><span class="sxs-lookup"><span data-stu-id="9263d-265">Having this means that `fixed` buffers will more closely resemble existing array semantics by having a `ref` accessor as well as byval `get` and `set` operations.</span></span> <span data-ttu-id="9263d-266">这意味着当发出代码使用缓冲区时，编译器将具有相同的灵活性，就 `fixed` 像使用数组时一样。</span><span class="sxs-lookup"><span data-stu-id="9263d-266">This means compilers will have the same flexibility when emitting code consuming `fixed` buffers as they do when consuming arrays.</span></span> <span data-ttu-id="9263d-267">这应该是类似于 `await` `fixed` 缓冲区的操作，更容易发出。</span><span class="sxs-lookup"><span data-stu-id="9263d-267">This should be operations like `await` over `fixed` buffers easier to emit.</span></span> 

<span data-ttu-id="9263d-268">这还带来了额外的好处，使 `fixed` 缓冲区更易于从其他语言使用。</span><span class="sxs-lookup"><span data-stu-id="9263d-268">This also has the added benefit that it will make `fixed` buffers easier to consume from other languages.</span></span> <span data-ttu-id="9263d-269">命名索引器是自 .NET 1.0 版本以来存在的一项功能。</span><span class="sxs-lookup"><span data-stu-id="9263d-269">Named indexers is a feature that has existed since the 1.0 release of .NET.</span></span> <span data-ttu-id="9263d-270">甚至不能直接发出命名索引器的语言通常会使用它们 (c # 实际上是此) 的一个很好的例子。</span><span class="sxs-lookup"><span data-stu-id="9263d-270">Even languages which cannot directly emit a named indexer can generally consume them (C# is actually a good example of this).</span></span>

<span data-ttu-id="9263d-271">此外，还将为 `get` `set` 每个</span><span class="sxs-lookup"><span data-stu-id="9263d-271">There will also be a by value `get` and `set` accessor generated for every</span></span> 

<span data-ttu-id="9263d-272">将使用属性生成缓冲区的后备存储 `[InlineArray]` 。</span><span class="sxs-lookup"><span data-stu-id="9263d-272">The backing storage for the buffer will be generated using the `[InlineArray]` attribute.</span></span> <span data-ttu-id="9263d-273">这是 [isuse 12320](https://github.com/dotnet/runtime/issues/12320) 中所述的一种机制，该机制可以专门用于有效声明同一类型字段的序列。</span><span class="sxs-lookup"><span data-stu-id="9263d-273">This is a mechanism discussed in [isuse 12320](https://github.com/dotnet/runtime/issues/12320) which allows specifically for the case of efficiently declaring sequence of fields of the same type.</span></span>

<span data-ttu-id="9263d-274">此特定问题仍处于活动状态，但预期是此功能的实现将遵循此项讨论。</span><span class="sxs-lookup"><span data-stu-id="9263d-274">This particular issue is still under active discussion and the expectation is that the implementation of this feature will follow however that discussion goes.</span></span>

### <a name="provide-parameter-does-not-escape-annotations"></a><span data-ttu-id="9263d-275">提供参数不转义批注</span><span class="sxs-lookup"><span data-stu-id="9263d-275">Provide parameter does not escape annotations</span></span>
<span data-ttu-id="9263d-276">低级别代码中重复出现的一种源是在封闭方法体之外 *安全地转义* 的参数的默认转义范围。</span><span class="sxs-lookup"><span data-stu-id="9263d-276">One source of repeated friction in low level code is the default escape scope for parameters being *safe-to-escape* outside the enclosing method body.</span></span> <span data-ttu-id="9263d-277">这是一个合理的默认值，因为它以 .NET 的编码模式为整体。</span><span class="sxs-lookup"><span data-stu-id="9263d-277">This is a sensible default because it lines up with the coding patterns of .NET as a whole.</span></span> <span data-ttu-id="9263d-278">在低级别代码中，的使用较大 `ref struct` ，并且此默认作用域可能会使范围安全规则中的其他部分产生摩擦。</span><span class="sxs-lookup"><span data-stu-id="9263d-278">In low level code there is a larger usage of `ref struct` and this default scope can cause friction with other parts of our span safety rules.</span></span>

<span data-ttu-id="9263d-279">之所以出现主要摩擦点，是因为方法调用有以下约束：</span><span class="sxs-lookup"><span data-stu-id="9263d-279">The main friction point occurs because of the following constraint around method invocations:</span></span>

> <span data-ttu-id="9263d-280">对于方法调用，如果 ref 结构类型有 ref 或 out 参数 (包括接收方) ，其中包含安全到转义的 E1，则不 (包含) 接收方的参数的安全对转义比 E1 更窄</span><span class="sxs-lookup"><span data-stu-id="9263d-280">For a method invocation if there is a ref or out argument of a ref struct type (including the receiver), with safe-to-escape E1, then no argument (including the receiver) may have a narrower safe-to-escape than E1</span></span>

<span data-ttu-id="9263d-281">此规则最常见的方法是使用实例方法， `ref struct` 其中至少有一个参数也是 `ref struct` 。</span><span class="sxs-lookup"><span data-stu-id="9263d-281">This rule most commonly comes into play with instance methods on `ref struct` where at least one parameter is also a `ref struct`.</span></span> <span data-ttu-id="9263d-282">这是低级别代码中的一种常见模式，其中 `ref struct` 类型常利用 `Span<T>` 其方法中的参数。</span><span class="sxs-lookup"><span data-stu-id="9263d-282">This is a common pattern in low level code where `ref struct` types commonly leverage `Span<T>` parameters in their methods.</span></span> <span data-ttu-id="9263d-283">考虑使用传递缓冲区的任何生成器或编写器样式对象 `Span<T>` 。</span><span class="sxs-lookup"><span data-stu-id="9263d-283">Consider any builder or writer style object that uses `Span<T>` to pass around buffers.</span></span>

<span data-ttu-id="9263d-284">此规则的存在是为了防止出现如下方案：</span><span class="sxs-lookup"><span data-stu-id="9263d-284">This rule exists to prevent scenarios like the following:</span></span>

```cs
ref struct RS
{
    Span<int> _field;
    void Set(Span<int> p)
    {
        _field = p;
    }

    static void DangerousCode(ref RS p)
    {
        Span<int> span = stackalloc int[] { 42 };

        // Error: if allowed this would let the method return a reference to 
        // the stack
        p.Set(span);
    }
}
```

<span data-ttu-id="9263d-285">实质上存在此规则，因为语言必须假定方法的所有输入都被转义为其最大允许范围。</span><span class="sxs-lookup"><span data-stu-id="9263d-285">Essentially this rule exists because the language must assume that all inputs to a method escape to their maximum allowed scope.</span></span> <span data-ttu-id="9263d-286">在上面的示例中，语言必须假定参数换入接收方的字段。</span><span class="sxs-lookup"><span data-stu-id="9263d-286">In the above case the language must assume that parameters escape into fields of the receiver.</span></span>

<span data-ttu-id="9263d-287">在实践中，这种方法决不会对参数进行转义。</span><span class="sxs-lookup"><span data-stu-id="9263d-287">In practice though there are many such methods which never escape the parameter.</span></span>
<span data-ttu-id="9263d-288">它只是在实现中使用的值。</span><span class="sxs-lookup"><span data-stu-id="9263d-288">It is just a value that is used within the implementation.</span></span> 

```cs
ref struct JsonReader
{
    Span<char> _buffer;
    int _position;

    internal bool TextEquals(ReadOnySpan<char> text)
    {
        var current = _buffer.Slice(_position, text.Length);
        return current == text;
    }
}

class C
{
    static void M(ref JsonReader reader)
    {
        Span<char> span = stackalloc char[4];
        span[0] = 'd';
        span[1] = 'o';
        span[2] = 'g';

        // Error: The *safe-to-escape* of `span` is the current method scope 
        // while `reader` is outside the current method scope hence this fails
        // by the above rule.
        if (reader.TextEquals(span)
        {
            ...
        })
    }
}
```

<span data-ttu-id="9263d-289">若要解决这种低级别的代码，请在 `unsafe` 编译器的生存期内采取技巧 `ref struct` 。</span><span class="sxs-lookup"><span data-stu-id="9263d-289">In order to work around this low level code will resort to `unsafe` tricks to lie to the compiler about the lifetime of their `ref struct`.</span></span> <span data-ttu-id="9263d-290">这可以极大地降低的价值主张， `ref struct` 因为它们应该是在 `unsafe` 继续编写高性能代码时避免的一种方法。</span><span class="sxs-lookup"><span data-stu-id="9263d-290">This significantly reduces the value proposition of `ref struct` as they are meant to be a means to avoid `unsafe` while continuing to write high performance code.</span></span>

<span data-ttu-id="9263d-291">参数默认转义范围导致摩擦的另一个位置是在方法主体中重新分配它们时。</span><span class="sxs-lookup"><span data-stu-id="9263d-291">The other place where parameter default escape scope causes friction is when they are re-assigned within a method body.</span></span> <span data-ttu-id="9263d-292">例如，如果方法体决定使用堆栈分配的值有条件地将转义应用于输入。</span><span class="sxs-lookup"><span data-stu-id="9263d-292">For instance if a method body decides to conditionally apply escaping to input by using stack allocated values.</span></span> <span data-ttu-id="9263d-293">再次强调，这会出现一些不足之处。</span><span class="sxs-lookup"><span data-stu-id="9263d-293">Once again this runs into some friction.</span></span>

```cs
void WriteData(ReadOnlySpan<char> data)
{
    if (data.Contains(':'))
    {
        Span<char> buffer = stackalloc char[256];
        Escape(data, buffer, out var length);

        // Error: Cannot assign `buffer` to `data` here as the *safe-to-escape*
        // scope of `buffer` is to the current method scope while `buffer` is
        // outside the current method scope
        data = buffer.Slice(0, length);
    }

    WriteDataCore(data);
}
```

<span data-ttu-id="9263d-294">此模式在 .NET 代码中比较常见，当不涉及时，此模式将正常运行 `ref struct` 。</span><span class="sxs-lookup"><span data-stu-id="9263d-294">This pattern is fairly common across .NET code and it works just fine when a `ref struct` is not involved.</span></span> <span data-ttu-id="9263d-295">一旦用户采用 `ref struct` 这种方式，就会强制用户在此处更改其模式，并且通常只需采取措施来 `unsafe` 解决此处的限制。</span><span class="sxs-lookup"><span data-stu-id="9263d-295">Once users adopt `ref struct` though it forces them to change their patterns here and often they just resort to `unsafe` to work around the limitations here.</span></span>

<span data-ttu-id="9263d-296">为了消除这种摩擦，语言将提供属性 `[DoesNotEscape]` 。</span><span class="sxs-lookup"><span data-stu-id="9263d-296">To remove this friction the language will provide the attribute `[DoesNotEscape]`.</span></span> <span data-ttu-id="9263d-297">这可应用于在上定义的任何类型或实例成员的参数 `ref struct` 。</span><span class="sxs-lookup"><span data-stu-id="9263d-297">This can be applied to parameters of any type or instance members defined on `ref struct`.</span></span> <span data-ttu-id="9263d-298">当应用于参数时， *安全 to escape* 和 *ref safe 到 escape* 范围将为当前方法作用域。</span><span class="sxs-lookup"><span data-stu-id="9263d-298">When applied to parameters the *safe-to-escape* and *ref-safe-to-escape* scope will be the current method scope.</span></span> <span data-ttu-id="9263d-299">当应用于具有相同限制的实例成员时， `ref struct` 将应用于该 `this` 参数。</span><span class="sxs-lookup"><span data-stu-id="9263d-299">When applied to instance members of `ref struct` the same limitation will apply to the `this` parameter.</span></span>

```cs
class C
{
    static Span<int> M1(Span<int> p1, [DoesNotEscape] Span<int> p2)
    {
        // Okay: the *safe-to-escape* here is still outside the enclosing scope
        // of the current method.
        return p1; 

        // ERROR: the [DoesNotEscape] attribute changes the *safe-to-escape* 
        // to be limited to the current method scope. Hence it cannot be 
        // returned
        return p2; 

        // ERROR: `local` has the same *safe-to-escape* as `p2` hence it cannot
        // be returned.
        Span<int> local = p2;
        return p2; 
    }
}
```

<span data-ttu-id="9263d-300">为了对此进行更改，将更新 span 安全文档的 "Parameters" 部分，使其包含以下项目符号：</span><span class="sxs-lookup"><span data-stu-id="9263d-300">To account for this change the "Parameters" section of the span safety document will be updated to include the following bullet:</span></span>

- <span data-ttu-id="9263d-301">如果用标记参数，则该参数 `[DoesNotEscape]` 是 *安全的转义* ，并对包含方法的作用域进行 *引用安全的转义* 。</span><span class="sxs-lookup"><span data-stu-id="9263d-301">If the parameter is marked with `[DoesNotEscape]`it is *safe-to-escape* and *ref-safe-to-escape* to the scope of the containing method.</span></span> 

<span data-ttu-id="9263d-302">需要注意的是，这自然会阻止此类参数通过存储为字段进行转义。</span><span class="sxs-lookup"><span data-stu-id="9263d-302">It's important to note that this will naturally block the ability for such parameters to escape by being stored as fields.</span></span> <span data-ttu-id="9263d-303">通过或传递的接收方在 `ref` `this` `ref struct` 当前方法之外具有 *安全的转义* 范围。</span><span class="sxs-lookup"><span data-stu-id="9263d-303">Receivers that are passed by `ref`, or `this` on `ref struct`, have a *safe-to-escape* scope outside the current method.</span></span> <span data-ttu-id="9263d-304">因此 `[DoesNotEscape]` ，从参数到此类值的字段的赋值将失败，因为现有字段赋值规则：接收方的作用域大于所赋的值。</span><span class="sxs-lookup"><span data-stu-id="9263d-304">Hence assignment from a `[DoesNotEscape]` parameter to a field on such a value fails by existing field assignment rules: the scope of the receiver is greater than the value being assigned.</span></span>

```cs
ref struct S
{
    Span<int> _field;

    void M1(Span<int> p1, [DoesNotEscape] Span<int> p2)
    {
        // Okay: the *safe-to-escape* here is still outside the enclosing scope
        // of the current method and hence the same as the receiver.
        _field = p1;

        // ERROR: the [DoesNotEscape] attribute changes the *safe-to-escape* 
        // to be limited to the current method scope. Hence it cannot be 
        // assigned to a receiver than has a *safe-to-escape* scope outside the 
        // current method.
        _field = p2;
    }
}
```

<span data-ttu-id="9263d-305">如果以这种方式限制了参数，则我们还将更新 "方法调用" 部分以放宽其规则。</span><span class="sxs-lookup"><span data-stu-id="9263d-305">Given that parameters are restricted in this way we will also update the "Method Invocation" section to relax its rules.</span></span> <span data-ttu-id="9263d-306">在任何情况下，如果它考虑 *引用安全到转义* 或 *安全到转义* 的参数生存期，则该规范将更改为忽略与标记为的参数对齐的参数 `[DoesNotEscape]` 。</span><span class="sxs-lookup"><span data-stu-id="9263d-306">In all cases where it is considering the *ref-safe-to-escape* or *safe-to-escape* lifetimes of arguments the spec will change to ignore those arguments which line up to parameters which are marked as `[DoesNotEscape]`.</span></span> <span data-ttu-id="9263d-307">因为这些参数不能转义它们的生存期，所以在考虑返回值的生存期时无需考虑这些参数。</span><span class="sxs-lookup"><span data-stu-id="9263d-307">Because these arguments cannot escape their lifetime does not need to be considered when considering the lifetime of returned values.</span></span>

<span data-ttu-id="9263d-308">例如，计算的返回的 *安全 to 转义* 的最后一行将更改为</span><span class="sxs-lookup"><span data-stu-id="9263d-308">For example the last line of calculating *safe-to-escape* of returns will change to</span></span> 

> <span data-ttu-id="9263d-309">包含接收方的所有参数表达式的安全对转义。</span><span class="sxs-lookup"><span data-stu-id="9263d-309">the safe-to-escape of all argument expressions including the receiver.</span></span> <span data-ttu-id="9263d-310">**这将排除用标记为 [DoesNotEscape] 的参数对齐的所有参数**</span><span class="sxs-lookup"><span data-stu-id="9263d-310">**This will exclude all arguments that line up with parameters marked as [DoesNotEscape]**</span></span>

<span data-ttu-id="9263d-311">杂项说明：</span><span class="sxs-lookup"><span data-stu-id="9263d-311">Misc Notes:</span></span>
- <span data-ttu-id="9263d-312">`DoesNotEscapeAttribute`将在命名空间中定义 `System.Runtime.CompilerServices` 。</span><span class="sxs-lookup"><span data-stu-id="9263d-312">The  `DoesNotEscapeAttribute` will be defined in the `System.Runtime.CompilerServices` namespace.</span></span>
- <span data-ttu-id="9263d-313">`DoesNotEscapeAttribute`不能与特性结合使用 `[ThisRefEscapes]` ，这样做会导致错误。</span><span class="sxs-lookup"><span data-stu-id="9263d-313">The `DoesNotEscapeAttribute` cannot be combined with the `[ThisRefEscapes]` attribute, doing so results in an error.</span></span>
- <span data-ttu-id="9263d-314">`DoesNotEscapeAttribute`将作为`modreq`</span><span class="sxs-lookup"><span data-stu-id="9263d-314">The `DoesNotEscapeAttribute` will be emitted as a `modreq`</span></span>

## <a name="considerations"></a><span data-ttu-id="9263d-315">注意事项</span><span class="sxs-lookup"><span data-stu-id="9263d-315">Considerations</span></span>

### <a name="keywords-vs-attributes"></a><span data-ttu-id="9263d-316">关键字与属性</span><span class="sxs-lookup"><span data-stu-id="9263d-316">Keywords vs. attributes</span></span>
<span data-ttu-id="9263d-317">此设计调用使用特性来批注成员的新生存期规则 `struct` 。</span><span class="sxs-lookup"><span data-stu-id="9263d-317">This design calls for using attributes to annotate the new lifetime rules for `struct` members.</span></span> <span data-ttu-id="9263d-318">同样，也可以通过上下文关键字轻松完成此操作。</span><span class="sxs-lookup"><span data-stu-id="9263d-318">This also could've been done just as easily with contextual keywords.</span></span> <span data-ttu-id="9263d-319">例如： `scoped` 和 `escapes` 可能已被使用，而不是 `DoesNotEscape` 和 `ThisRefEscapes` 。</span><span class="sxs-lookup"><span data-stu-id="9263d-319">For instance: `scoped` and `escapes` could have been used instead of `DoesNotEscape` and `ThisRefEscapes`.</span></span>

<span data-ttu-id="9263d-320">关键字（甚至是上下文的）的权重比特性要大得多。</span><span class="sxs-lookup"><span data-stu-id="9263d-320">Keywords, even the contextual ones, have a much heavier weight in the language than attributes.</span></span> <span data-ttu-id="9263d-321">这些功能的使用案例虽然非常有价值，但会影响少量开发人员。</span><span class="sxs-lookup"><span data-stu-id="9263d-321">The use cases these features solve, while very valuable, impact a small number of developers.</span></span> <span data-ttu-id="9263d-322">请注意，只有几个高端开发人员定义了 `ref struct` 实例，然后考虑到只有一小部分开发人员将使用这些新的生存期功能。</span><span class="sxs-lookup"><span data-stu-id="9263d-322">Consider that only a fraction of high end developers are defining `ref struct` instances and then consider that only a fraction of those developers will be using these new lifetime features.</span></span>
<span data-ttu-id="9263d-323">这似乎不是将新的上下文关键字添加到语言的理由。</span><span class="sxs-lookup"><span data-stu-id="9263d-323">That doesn't seem to justify adding a new contextual keyword to the language.</span></span>

<span data-ttu-id="9263d-324">但这意味着，将根据属性定义程序的正确性。</span><span class="sxs-lookup"><span data-stu-id="9263d-324">This does mean that program correctness will be defined in terms of attributes though.</span></span> <span data-ttu-id="9263d-325">这是一种灰色区域，适用于功能的语言一方，但运行时的模式已建立。</span><span class="sxs-lookup"><span data-stu-id="9263d-325">That is a bit of a gray area for the language side of things but an established pattern for the runtime.</span></span> 

## <a name="open-issues"></a><span data-ttu-id="9263d-326">未结的问题</span><span class="sxs-lookup"><span data-stu-id="9263d-326">Open Issues</span></span>

### <a name="allow-fixed-buffer-locals"></a><span data-ttu-id="9263d-327">允许固定缓冲区局部变量</span><span class="sxs-lookup"><span data-stu-id="9263d-327">Allow fixed buffer locals</span></span>
<span data-ttu-id="9263d-328">此设计允许 `fixed` 可支持任何类型的安全缓冲区。</span><span class="sxs-lookup"><span data-stu-id="9263d-328">This design allows for safe `fixed` buffers that can support any type.</span></span> <span data-ttu-id="9263d-329">此处的一个可能的扩展允许 `fixed` 将此类缓冲区声明为局部变量。</span><span class="sxs-lookup"><span data-stu-id="9263d-329">One possible extension here is allowing such `fixed` buffers to be declared as local variables.</span></span> <span data-ttu-id="9263d-330">这将允许多个现有 `stackalloc` 操作替换为一个 `fixed` 缓冲区。</span><span class="sxs-lookup"><span data-stu-id="9263d-330">This would allow a number of existing `stackalloc` operations to be replaced with a `fixed` buffer.</span></span> <span data-ttu-id="9263d-331">它还会扩展一组方案，在缓冲区不是的情况下，我们可以将堆栈样式分配 `stackalloc` 限制为非托管元素类型 `fixed` 。</span><span class="sxs-lookup"><span data-stu-id="9263d-331">It would also expand the set of scenarios we could have stack style allocations as `stackalloc` is limited to unmanaged element types while `fixed` buffers are not.</span></span> 

```cs
class FixedBufferLocals
{
    void Example()
    {
        Span<int> span = stakalloc int[42];
        int buffer[42];
    }
}
```

<span data-ttu-id="9263d-332">这会结合在一起，但需要我们为局部变量扩展语法。</span><span class="sxs-lookup"><span data-stu-id="9263d-332">This holds together but does require us to extend the syntax for locals a bit.</span></span> <span data-ttu-id="9263d-333">如果这是或不值得额外的复杂性，则不明确。</span><span class="sxs-lookup"><span data-stu-id="9263d-333">Unclear if this is or isn't worth the extra complexity.</span></span> <span data-ttu-id="9263d-334">我们可能会立即决定，如果有足够的需求，请稍后再试。</span><span class="sxs-lookup"><span data-stu-id="9263d-334">Possible we could decide no for now and bring back later if sufficient need is demonstrated.</span></span>

<span data-ttu-id="9263d-335">这会带来好处的示例： https://github.com/dotnet/runtime/pull/34149</span><span class="sxs-lookup"><span data-stu-id="9263d-335">Example of where this would be beneficial: https://github.com/dotnet/runtime/pull/34149</span></span>

### <a name="allow-multi-dimensional-fixed-buffers"></a><span data-ttu-id="9263d-336">允许多维固定缓冲区</span><span class="sxs-lookup"><span data-stu-id="9263d-336">Allow multi-dimensional fixed buffers</span></span>
<span data-ttu-id="9263d-337">缓冲区设计是否应 `fixed` 扩展以包含多维样式数组？</span><span class="sxs-lookup"><span data-stu-id="9263d-337">Should the design for `fixed` buffers be extended to include multi-dimensional style arrays?</span></span> <span data-ttu-id="9263d-338">基本上允许如下声明：</span><span class="sxs-lookup"><span data-stu-id="9263d-338">Essentially allowing for declarations like the following:</span></span>

```cs
struct Dimensions
{
    int array[42, 13];
}
```

## <a name="future-considerations"></a><span data-ttu-id="9263d-339">未来的注意事项</span><span class="sxs-lookup"><span data-stu-id="9263d-339">Future Considerations</span></span>

### <a name="allowing-attributes-on-locals"></a><span data-ttu-id="9263d-340">允许本地属性</span><span class="sxs-lookup"><span data-stu-id="9263d-340">Allowing attributes on locals</span></span>
<span data-ttu-id="9263d-341">对于使用的开发人员而言，另一个与之不同的 `ref struct` 是，本地变量可能会受到与参数的相同问题的影响。</span><span class="sxs-lookup"><span data-stu-id="9263d-341">Another friction point for developers using `ref struct` is local variables can suffer from the same issues as parameters with respect to their lifetimes being decided at declaration.</span></span> <span data-ttu-id="9263d-342">比使用在 `ref struct` 多个路径中分配的非常困难，其中至少有一个路径是受限的 *安全到转义* 作用域。</span><span class="sxs-lookup"><span data-stu-id="9263d-342">Than can make it difficult to work with `ref struct` that are assigned on multiple paths where at least one of the paths is a limited *safe-to-escape* scope.</span></span> 

```cs
int length = ...;
Span<byte> span;
if (length > StackAllocLimit)
{
    span = new Span(new byte[length]);
}
else
{
    // Error: The *safe-to-escape* of `span` was decided to be outside the 
    // current method scope hence it can't be the target of a stackalloc
    span = stackalloc byte[length];
}
```

<span data-ttu-id="9263d-343">`Span<T>`具体来说，开发人员可以通过使用大小为零的本地初始化来解决此情况 `stackalloc` 。</span><span class="sxs-lookup"><span data-stu-id="9263d-343">For `Span<T>` specifically developers can work around this by initializing the local with a `stackalloc` of size zero.</span></span> <span data-ttu-id="9263d-344">这会将 *安全 to escape* 范围更改为当前方法，并被编译器优化掉。</span><span class="sxs-lookup"><span data-stu-id="9263d-344">This changes the *safe-to-escape* scope to be the current method and is optimized away by the compiler.</span></span> <span data-ttu-id="9263d-345">它实际上是用于进行本地的语法 `[DoesNotEscape]` 。</span><span class="sxs-lookup"><span data-stu-id="9263d-345">It's effectively a syntax for making a `[DoesNotEscape]` local.</span></span>

```cs
int length = ...;
Span<byte> span = stackalloc byte[0];
if (length > StackAllocLimit)
{
    span = new Span(new byte[length]);
}
else
{
    // Okay
    span = stackalloc byte[length];
}
```

<span data-ttu-id="9263d-346">这仅适用于 `Span<T>` ，但没有适用于值的通用机制 `ref struct` 。</span><span class="sxs-lookup"><span data-stu-id="9263d-346">This only works for `Span<T>` though, there is no general purpose mechanism for `ref struct` values.</span></span> <span data-ttu-id="9263d-347">但是， `[DoesNotEscape]` 属性提供了此处所需的确切语义。</span><span class="sxs-lookup"><span data-stu-id="9263d-347">However the `[DoesNotEscape]` attribute provides exactly the semantics that are desired here.</span></span> <span data-ttu-id="9263d-348">如果我们决定将来允许将特性应用于本地变量，则会为此方案提供立即止裂槽。</span><span class="sxs-lookup"><span data-stu-id="9263d-348">If we decide in the future to allow attributes to apply to local variables it would provide immediate relief to this scenario.</span></span>

## <a name="related-information"></a><span data-ttu-id="9263d-349">相关信息</span><span class="sxs-lookup"><span data-stu-id="9263d-349">Related Information</span></span>

### <a name="issues"></a><span data-ttu-id="9263d-350">问题</span><span class="sxs-lookup"><span data-stu-id="9263d-350">Issues</span></span>
<span data-ttu-id="9263d-351">以下问题都与此建议相关：</span><span class="sxs-lookup"><span data-stu-id="9263d-351">The following issues are all related to this proposal:</span></span>

- https://github.com/dotnet/csharplang/issues/1130
- https://github.com/dotnet/csharplang/issues/1147
- https://github.com/dotnet/csharplang/issues/992
- https://github.com/dotnet/csharplang/issues/1314
- https://github.com/dotnet/csharplang/issues/2208
- https://github.com/dotnet/runtime/issues/32060

### <a name="proposals"></a><span data-ttu-id="9263d-352">建议</span><span class="sxs-lookup"><span data-stu-id="9263d-352">Proposals</span></span>
<span data-ttu-id="9263d-353">以下建议与此建议相关：</span><span class="sxs-lookup"><span data-stu-id="9263d-353">The following proposals are related to this proposal:</span></span>

- https://github.com/dotnet/csharplang/blob/725763343ad44a9251b03814e6897d87fe553769/proposals/fixed-sized-buffers.md

### <a name="existing-samples"></a><span data-ttu-id="9263d-354">现有示例</span><span class="sxs-lookup"><span data-stu-id="9263d-354">Existing samples</span></span>

[<span data-ttu-id="9263d-355">Utf8JsonReader</span><span class="sxs-lookup"><span data-stu-id="9263d-355">Utf8JsonReader</span></span>](https://github.com/dotnet/runtime/blob/f1a7cb3fdd7ffc4ce7d996b7ac6867ffe2c953b9/src/libraries/System.Text.Json/src/System/Text/Json/Reader/Utf8JsonReader.cs#L523-L528)

<span data-ttu-id="9263d-356">此特定代码片段要求不安全，因为它在传递时遇到问题， `Span<T>` 可以将其堆栈分配给上的实例方法 `ref struct` 。</span><span class="sxs-lookup"><span data-stu-id="9263d-356">This particular snippet requires unsafe because it runs into issues with passing around a `Span<T>` which can be stack allocated to an instance method on a `ref struct`.</span></span> <span data-ttu-id="9263d-357">即使未捕获此参数，语言也必须假定它是这样的，这样就不会造成不必要的问题。</span><span class="sxs-lookup"><span data-stu-id="9263d-357">Even though this parameter is not captured the language must assume it is and hence needlessly causes friction here.</span></span>

[<span data-ttu-id="9263d-358">Utf8JsonWriter</span><span class="sxs-lookup"><span data-stu-id="9263d-358">Utf8JsonWriter</span></span>](https://github.com/dotnet/runtime/blob/f1a7cb3fdd7ffc4ce7d996b7ac6867ffe2c953b9/src/libraries/System.Text.Json/src/System/Text/Json/Writer/Utf8JsonWriter.WriteProperties.String.cs#L122-L127)

<span data-ttu-id="9263d-359">此代码段要通过对数据的元素进行转义来改变参数。</span><span class="sxs-lookup"><span data-stu-id="9263d-359">This snippet wants to mutate a parameter by escaping elements of the data.</span></span> <span data-ttu-id="9263d-360">为了提高效率，可以对转义的数据进行堆栈分配。</span><span class="sxs-lookup"><span data-stu-id="9263d-360">The escaped data can be stack allocated for efficiency.</span></span> <span data-ttu-id="9263d-361">即使未对参数进行转义，编译器也会将其分配给封闭方法之外的 *安全取消转义* 范围，因为它是一个参数。</span><span class="sxs-lookup"><span data-stu-id="9263d-361">Even though the parameter is not escaped the compiler assigns it a *safe-to-escape* scope of outside the enclosing method because it is a parameter.</span></span> <span data-ttu-id="9263d-362">这意味着，若要使用堆栈分配，实现必须使用堆栈分配，以便 `unsafe` 在转义数据后将返回到参数。</span><span class="sxs-lookup"><span data-stu-id="9263d-362">This means in order to use stack allocation the implementation must use `unsafe` in order to assign back to the parameter after escaping the data.</span></span>

### <a name="fun-samples"></a><span data-ttu-id="9263d-363">趣味示例</span><span class="sxs-lookup"><span data-stu-id="9263d-363">Fun Samples</span></span>

```cs
ref struct StackLinkedListNode<T>
{
    T _value;
    ref StackLinkedListNode<T> _next;

    public T Value => _value;

    public bool HasNext => !Unsafe.IsNullRef(ref _next);

    public ref StackLinkedListNode<T> Next 
    {
        get
        {
            if (!HasNext)
            {
                throw new InvalidOperationException("No next node");
            }

            return ref _next;
        }
    }

    public StackLinkedListNode(T value)
    {
        this = default;
        _value = value;
    }

    public StackLinkedListNode(T value, ref StackLinkedListNode<T> next)
    {
        _value = value;
        ref _next = ref next;
    }
}
```

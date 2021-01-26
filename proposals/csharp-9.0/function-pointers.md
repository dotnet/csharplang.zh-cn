---
ms.openlocfilehash: 05de92313261e2d0c74de13df5a3c0eb96b49e1e
ms.sourcegitcommit: 6ab8409a32f1249f9aef618054426acb7bc7b4c1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/25/2021
ms.locfileid: "98763660"
---
# <a name="function-pointers"></a><span data-ttu-id="9e7e7-101">函数指针</span><span class="sxs-lookup"><span data-stu-id="9e7e7-101">Function Pointers</span></span>

## <a name="summary"></a><span data-ttu-id="9e7e7-102">总结</span><span class="sxs-lookup"><span data-stu-id="9e7e7-102">Summary</span></span>

<span data-ttu-id="9e7e7-103">此建议提供的语言构造提供当前无法有效访问或根本不能在 c # 中访问的 IL 操作码： `ldftn` 和 `calli` 。</span><span class="sxs-lookup"><span data-stu-id="9e7e7-103">This proposal provides language constructs that expose IL opcodes that cannot currently be accessed efficiently, or at all, in C# today: `ldftn` and `calli`.</span></span> <span data-ttu-id="9e7e7-104">这些 IL 操作码在高性能代码中非常重要，开发人员需要一种高效的访问方式。</span><span class="sxs-lookup"><span data-stu-id="9e7e7-104">These IL opcodes can be important in high performance code and developers need an efficient way to access them.</span></span>

## <a name="motivation"></a><span data-ttu-id="9e7e7-105">动机</span><span class="sxs-lookup"><span data-stu-id="9e7e7-105">Motivation</span></span>

<span data-ttu-id="9e7e7-106">下面的问题中描述了此功能的动机和背景， (方式是功能) 的可能实现：</span><span class="sxs-lookup"><span data-stu-id="9e7e7-106">The motivations and background for this feature are described in the following issue (as is a potential implementation of the feature):</span></span>

https://github.com/dotnet/csharplang/issues/191

<span data-ttu-id="9e7e7-107">这是[编译器内部函数](https://github.com/dotnet/csharplang/blob/master/proposals/intrinsics.md)的替代设计建议</span><span class="sxs-lookup"><span data-stu-id="9e7e7-107">This is an alternate design proposal to [compiler intrinsics](https://github.com/dotnet/csharplang/blob/master/proposals/intrinsics.md)</span></span>

## <a name="detailed-design"></a><span data-ttu-id="9e7e7-108">详细设计</span><span class="sxs-lookup"><span data-stu-id="9e7e7-108">Detailed Design</span></span>

### <a name="function-pointers"></a><span data-ttu-id="9e7e7-109">函数指针</span><span class="sxs-lookup"><span data-stu-id="9e7e7-109">Function pointers</span></span>

<span data-ttu-id="9e7e7-110">该语言将允许使用语法声明函数指针 `delegate*` 。</span><span class="sxs-lookup"><span data-stu-id="9e7e7-110">The language will allow for the declaration of function pointers using the `delegate*` syntax.</span></span> <span data-ttu-id="9e7e7-111">在下一节中详细介绍了完整的语法，但它应类似于 `Func` 和类型声明所使用的语法 `Action` 。</span><span class="sxs-lookup"><span data-stu-id="9e7e7-111">The full syntax is described in detail in the next section but it is meant to resemble the syntax used by `Func` and `Action` type declarations.</span></span>

``` csharp
unsafe class Example {
    void Example(Action<int> a, delegate*<int, void> f) {
        a(42);
        f(42);
    }
}
```

<span data-ttu-id="9e7e7-112">这些类型使用 ECMA-335 中所述的函数指针类型来表示。</span><span class="sxs-lookup"><span data-stu-id="9e7e7-112">These types are represented using the function pointer type as outlined in ECMA-335.</span></span> <span data-ttu-id="9e7e7-113">这意味着对的调用将 `delegate*` 使用对 `calli` `delegate` 方法使用的调用 `callvirt` `Invoke` 。</span><span class="sxs-lookup"><span data-stu-id="9e7e7-113">This means invocation of a `delegate*` will use `calli` where invocation of a `delegate` will use `callvirt` on the `Invoke` method.</span></span>
<span data-ttu-id="9e7e7-114">当然，对于这两种构造，调用是相同的。</span><span class="sxs-lookup"><span data-stu-id="9e7e7-114">Syntactically though invocation is identical for both constructs.</span></span>

<span data-ttu-id="9e7e7-115">方法指针的 ECMA-335 定义包含调用约定，作为类型签名的一部分 (节 7.1) 。</span><span class="sxs-lookup"><span data-stu-id="9e7e7-115">The ECMA-335 definition of method pointers includes the calling convention as part of the type signature (section 7.1).</span></span>
<span data-ttu-id="9e7e7-116">默认调用约定将为 `managed` 。</span><span class="sxs-lookup"><span data-stu-id="9e7e7-116">The default calling convention will be `managed`.</span></span> <span data-ttu-id="9e7e7-117">通过 `unmanaged` 将关键字叫到 `delegate*` 将使用运行时平台默认的语法，可以指定非托管调用约定。</span><span class="sxs-lookup"><span data-stu-id="9e7e7-117">Unmanaged calling conventions can by specified by putting an `unmanaged` keyword afer the `delegate*` syntax, which will use the runtime platform default.</span></span> <span data-ttu-id="9e7e7-118">然后 `unmanaged` ，可以通过在命名空间中指定以命名空间开头的任何类型，在括号中指定特定的非托管约定 `CallConv` `System.Runtime.CompilerServices` ，而不是 `CallConv` 前缀。</span><span class="sxs-lookup"><span data-stu-id="9e7e7-118">Specific unmanaged conventions can then be specified in brackets to the `unmanaged` keyword by specifying any type starting with `CallConv` in the `System.Runtime.CompilerServices` namespace, leaving off the `CallConv` prefix.</span></span> <span data-ttu-id="9e7e7-119">这些类型必须来自程序的核心库，并且一组有效的组合依赖于平台。</span><span class="sxs-lookup"><span data-stu-id="9e7e7-119">These types must come from the program's core library, and the set of valid combinations is platform-dependent.</span></span>

``` csharp
//This method has a managed calling convention. This is the same as leaving the managed keyword off.
delegate* managed<int, int>;

// This method will be invoked using whatever the default unmanaged calling convention on the runtime
// platform is. This is platform and architecture dependent and is determined by the CLR at runtime.
delegate* unmanaged<int, int>;

// This method will be invoked using the cdecl calling convention
// Cdecl maps to System.Runtime.CompilerServices.CallConvCdecl
delegate* unmanaged[Cdecl] <int, int>;

// This method will be invoked using the stdcall calling convention, and suppresses GC transition
// Stdcall maps to System.Runtime.CompilerServices.CallConvStdcall
// SuppressGCTransition maps to System.Runtime.CompilerServices.CallConvSuppressGCTransition
delegate* unmanaged[Stdcall, SuppressGCTransition] <int, int>;
```

<span data-ttu-id="9e7e7-120">类型之间的转换 `delegate*` 是根据其签名（包括调用约定）完成的。</span><span class="sxs-lookup"><span data-stu-id="9e7e7-120">Conversions between `delegate*` types is done based on their signature including the calling convention.</span></span>

``` csharp
unsafe class Example {
    void Conversions() {
        delegate*<int, int, int> p1 = ...;
        delegate* managed<int, int, int> p2 = ...;
        delegate* unmanaged<int, int, int> p3 = ...;

        p1 = p2; // okay p1 and p2 have compatible signatures
        Console.WriteLine(p2 == p1); // True
        p2 = p3; // error: calling conventions are incompatible
    }
}
```

<span data-ttu-id="9e7e7-121">`delegate*`类型是指针类型，这意味着它具有标准指针类型的所有功能和限制：</span><span class="sxs-lookup"><span data-stu-id="9e7e7-121">A `delegate*` type is a pointer type which means it has all of the capabilities and restrictions of a standard pointer type:</span></span>

- <span data-ttu-id="9e7e7-122">仅在上下文中有效 `unsafe` 。</span><span class="sxs-lookup"><span data-stu-id="9e7e7-122">Only valid in an `unsafe` context.</span></span>
- <span data-ttu-id="9e7e7-123">`delegate*`只能从上下文调用包含参数或返回类型的方法 `unsafe` 。</span><span class="sxs-lookup"><span data-stu-id="9e7e7-123">Methods which contain a `delegate*` parameter or return type can only be called from an `unsafe` context.</span></span>
- <span data-ttu-id="9e7e7-124">不能转换为 `object` 。</span><span class="sxs-lookup"><span data-stu-id="9e7e7-124">Cannot be converted to `object`.</span></span>
- <span data-ttu-id="9e7e7-125">不能用作泛型参数。</span><span class="sxs-lookup"><span data-stu-id="9e7e7-125">Cannot be used as a generic argument.</span></span>
- <span data-ttu-id="9e7e7-126">可隐式转换 `delegate*` 为 `void*` 。</span><span class="sxs-lookup"><span data-stu-id="9e7e7-126">Can implicitly convert `delegate*` to `void*`.</span></span>
- <span data-ttu-id="9e7e7-127">可以从显式转换 `void*` 为 `delegate*` 。</span><span class="sxs-lookup"><span data-stu-id="9e7e7-127">Can explicitly convert from `void*` to `delegate*`.</span></span>

<span data-ttu-id="9e7e7-128">限制：</span><span class="sxs-lookup"><span data-stu-id="9e7e7-128">Restrictions:</span></span>

- <span data-ttu-id="9e7e7-129">自定义特性不能应用于 `delegate*` 或它的任何元素。</span><span class="sxs-lookup"><span data-stu-id="9e7e7-129">Custom attributes cannot be applied to a `delegate*` or any of its elements.</span></span>
- <span data-ttu-id="9e7e7-130">`delegate*`不能将参数标记为`params`</span><span class="sxs-lookup"><span data-stu-id="9e7e7-130">A `delegate*` parameter cannot be marked as `params`</span></span>
- <span data-ttu-id="9e7e7-131">`delegate*`类型具有正常指针类型的所有限制。</span><span class="sxs-lookup"><span data-stu-id="9e7e7-131">A `delegate*` type has all of the restrictions of a normal pointer type.</span></span>
- <span data-ttu-id="9e7e7-132">指针算法不能直接对函数指针类型执行。</span><span class="sxs-lookup"><span data-stu-id="9e7e7-132">Pointer arithmetic cannot be performed directly on function pointer types.</span></span>

### <a name="function-pointer-syntax"></a><span data-ttu-id="9e7e7-133">函数指针语法</span><span class="sxs-lookup"><span data-stu-id="9e7e7-133">Function pointer syntax</span></span>

<span data-ttu-id="9e7e7-134">完整的函数指针语法由以下语法表示：</span><span class="sxs-lookup"><span data-stu-id="9e7e7-134">The full function pointer syntax is represented by the following grammar:</span></span>

```antlr
pointer_type
    : ...
    | funcptr_type
    ;

funcptr_type
    : 'delegate' '*' calling_convention_specifier? '<' funcptr_parameter_list funcptr_return_type '>'
    ;

calling_convention_specifier
    : 'managed'
    | 'unmanaged' ('[' unmanaged_calling_convention ']')?
    ;

unmanaged_calling_convention
    : 'Cdecl'
    | 'Stdcall'
    | 'Thiscall'
    | 'Fastcall'
    | identifier (',' identifier)*
    ;

funptr_parameter_list
    : (funcptr_parameter ',')*
    ;

funcptr_parameter
    : funcptr_parameter_modifier? type
    ;

funcptr_return_type
    : funcptr_return_modifier? return_type
    ;

funcptr_parameter_modifier
    : 'ref'
    | 'out'
    | 'in'
    ;

funcptr_return_modifier
    : 'ref'
    | 'ref readonly'
    ;
```

<span data-ttu-id="9e7e7-135">如果未 `calling_convention_specifier` 提供，则默认值为 `managed` 。</span><span class="sxs-lookup"><span data-stu-id="9e7e7-135">If no `calling_convention_specifier` is provided, the default is `managed`.</span></span> <span data-ttu-id="9e7e7-136">在 `calling_convention_specifier` `identifier` `unmanaged_calling_convention` [调用约定的元数据表示形式](#metadata-representation-of-calling-conventions)中介绍了的精确元数据编码以及在中有效的。</span><span class="sxs-lookup"><span data-stu-id="9e7e7-136">The precise metadata encoding of the `calling_convention_specifier` and what `identifier`s are valid in the `unmanaged_calling_convention` is covered in [Metadata Representation of Calling Conventions](#metadata-representation-of-calling-conventions).</span></span>

``` csharp
delegate int Func1(string s);
delegate Func1 Func2(Func1 f);

// Function pointer equivalent without calling convention
delegate*<string, int>;
delegate*<delegate*<string, int>, delegate*<string, int>>;

// Function pointer equivalent with calling convention
delegate* managed<string, int>;
delegate*<delegate* managed<string, int>, delegate*<string, int>>;
```

### <a name="function-pointer-conversions"></a><span data-ttu-id="9e7e7-137">函数指针转换</span><span class="sxs-lookup"><span data-stu-id="9e7e7-137">Function pointer conversions</span></span>

<span data-ttu-id="9e7e7-138">在不安全的上下文中，将扩展 (隐式转换) 的可用隐式转换集，使其包含以下隐式指针转换：</span><span class="sxs-lookup"><span data-stu-id="9e7e7-138">In an unsafe context, the set of available implicit conversions (Implicit conversions) is extended to include the following implicit pointer conversions:</span></span>
- [<span data-ttu-id="9e7e7-139">_现有转换_</span><span class="sxs-lookup"><span data-stu-id="9e7e7-139">_Existing conversions_</span></span>](https://github.com/dotnet/csharplang/blob/master/spec/unsafe-code.md#pointer-conversions)
- <span data-ttu-id="9e7e7-140">如果满足以下所有条件，则从 _funcptr \_ 类型_ `F0` 到另一 _funcptr \_ 类型_ `F1` ：</span><span class="sxs-lookup"><span data-stu-id="9e7e7-140">From _funcptr\_type_ `F0` to another _funcptr\_type_ `F1`, provided all of the following are true:</span></span>
    - <span data-ttu-id="9e7e7-141">`F0` 和 `F1` 具有相同数量的参数，中的每个参数与 `D0n` `F0` `ref` `out` `in` 中的相应参数具有相同的、或修饰符 `D1n` `F1` 。</span><span class="sxs-lookup"><span data-stu-id="9e7e7-141">`F0` and `F1` have the same number of parameters, and each parameter `D0n` in `F0` has the same `ref`, `out`, or `in` modifiers as the corresponding parameter `D1n` in `F1`.</span></span>
    - <span data-ttu-id="9e7e7-142">对于每个 value 参数 (没有 `ref` 、 `out` 或 `in` 修饰符) 的参数，存在从中的参数类型 `F0` 到中的相应参数类型的标识转换、隐式引用转换或隐式指针转换 `F1` 。</span><span class="sxs-lookup"><span data-stu-id="9e7e7-142">For each value parameter (a parameter with no `ref`, `out`, or `in` modifier), an identity conversion, implicit reference conversion, or implicit pointer conversion exists from the parameter type in `F0` to the corresponding parameter type in `F1`.</span></span>
    - <span data-ttu-id="9e7e7-143">对于每个 `ref` 、 `out` 或 `in` 参数，中的参数类型与 `F0` 中相应的参数类型相同 `F1` 。</span><span class="sxs-lookup"><span data-stu-id="9e7e7-143">For each `ref`, `out`, or `in` parameter, the parameter type in `F0` is the same as the corresponding parameter type in `F1`.</span></span>
    - <span data-ttu-id="9e7e7-144">如果返回类型是通过值 (no `ref` 或 `ref readonly`) 、标识、隐式引用或隐式指针转换从的返回类型 `F1` 到的返回类型 `F0` 。</span><span class="sxs-lookup"><span data-stu-id="9e7e7-144">If the return type is by value (no `ref` or `ref readonly`), an identity, implicit reference, or implicit pointer conversion exists from the return type of `F1` to the return type of `F0`.</span></span>
    - <span data-ttu-id="9e7e7-145">如果返回类型是按引用 (`ref` 或 `ref readonly`) ，则的返回类型和修饰符与的 `ref` `F1` 返回类型和 `ref` 修饰符相同 `F0` 。</span><span class="sxs-lookup"><span data-stu-id="9e7e7-145">If the return type is by reference (`ref` or `ref readonly`), the return type and `ref` modifiers of `F1` are the same as the return type and `ref` modifiers of `F0`.</span></span>
    - <span data-ttu-id="9e7e7-146">的调用约定与的 `F0` 调用约定相同 `F1` 。</span><span class="sxs-lookup"><span data-stu-id="9e7e7-146">The calling convention of `F0` is the same as the calling convention of `F1`.</span></span>

### <a name="allow-address-of-to-target-methods"></a><span data-ttu-id="9e7e7-147">允许目标方法的地址</span><span class="sxs-lookup"><span data-stu-id="9e7e7-147">Allow address-of to target methods</span></span>

<span data-ttu-id="9e7e7-148">现在将允许方法组作为表达式的参数。</span><span class="sxs-lookup"><span data-stu-id="9e7e7-148">Method groups will now be allowed as arguments to an address-of expression.</span></span> <span data-ttu-id="9e7e7-149">此类表达式的类型将为， `delegate*` 它具有目标方法的等效签名和托管调用约定：</span><span class="sxs-lookup"><span data-stu-id="9e7e7-149">The type of such an expression will be a `delegate*` which has the equivalent signature of the target method and a managed calling convention:</span></span>

``` csharp
unsafe class Util {
    public static void Log() { }

    void Use() {
        delegate*<void> ptr1 = &Util.Log;

        // Error: type "delegate*<void>" not compatible with "delegate*<int>";
        delegate*<int> ptr2 = &Util.Log;
   }
}
```

<span data-ttu-id="9e7e7-150">在不安全的上下文中， `M` `F` 如果满足以下所有条件，则方法与函数指针类型兼容：</span><span class="sxs-lookup"><span data-stu-id="9e7e7-150">In an unsafe context, a method `M` is compatible with a function pointer type `F` if all of the following are true:</span></span>
- <span data-ttu-id="9e7e7-151">`M` 和 `F` 具有相同数量的参数，中的每个参数与 `M` `ref` `out` `in` 中的相应参数具有相同的、或修饰符 `F` 。</span><span class="sxs-lookup"><span data-stu-id="9e7e7-151">`M` and `F` have the same number of parameters, and each parameter in `M` has the same `ref`, `out`, or `in` modifiers as the corresponding parameter in `F`.</span></span>
- <span data-ttu-id="9e7e7-152">对于每个 value 参数 (没有 `ref` 、 `out` 或 `in` 修饰符) 的参数，存在从中的参数类型 `M` 到中的相应参数类型的标识转换、隐式引用转换或隐式指针转换 `F` 。</span><span class="sxs-lookup"><span data-stu-id="9e7e7-152">For each value parameter (a parameter with no `ref`, `out`, or `in` modifier), an identity conversion, implicit reference conversion, or implicit pointer conversion exists from the parameter type in `M` to the corresponding parameter type in `F`.</span></span>
- <span data-ttu-id="9e7e7-153">对于每个 `ref` 、 `out` 或 `in` 参数，中的参数类型与 `M` 中相应的参数类型相同 `F` 。</span><span class="sxs-lookup"><span data-stu-id="9e7e7-153">For each `ref`, `out`, or `in` parameter, the parameter type in `M` is the same as the corresponding parameter type in `F`.</span></span>
- <span data-ttu-id="9e7e7-154">如果返回类型是通过值 (no `ref` 或 `ref readonly`) 、标识、隐式引用或隐式指针转换从的返回类型 `F` 到的返回类型 `M` 。</span><span class="sxs-lookup"><span data-stu-id="9e7e7-154">If the return type is by value (no `ref` or `ref readonly`), an identity, implicit reference, or implicit pointer conversion exists from the return type of `F` to the return type of `M`.</span></span>
- <span data-ttu-id="9e7e7-155">如果返回类型是按引用 (`ref` 或 `ref readonly`) ，则的返回类型和修饰符与的 `ref` `F` 返回类型和 `ref` 修饰符相同 `M` 。</span><span class="sxs-lookup"><span data-stu-id="9e7e7-155">If the return type is by reference (`ref` or `ref readonly`), the return type and `ref` modifiers of `F` are the same as the return type and `ref` modifiers of `M`.</span></span>
- <span data-ttu-id="9e7e7-156">的调用约定与的 `M` 调用约定相同 `F` 。</span><span class="sxs-lookup"><span data-stu-id="9e7e7-156">The calling convention of `M` is the same as the calling convention of `F`.</span></span> <span data-ttu-id="9e7e7-157">这包括调用约定位，以及非托管标识符中指定的任何调用约定标志。</span><span class="sxs-lookup"><span data-stu-id="9e7e7-157">This includes both the calling convention bit, as well as any calling convention flags specified in the unmanaged identifier.</span></span>
- <span data-ttu-id="9e7e7-158">`M` 是静态方法。</span><span class="sxs-lookup"><span data-stu-id="9e7e7-158">`M` is a static method.</span></span>

<span data-ttu-id="9e7e7-159">在不安全的上下文中，如果包含的表达式的目标为方法组，而该表达式的目标为方法组，则该表达式的目标为方法组， `E` `F` 前提是至少有 `E` 一个方法适用于通过使用的参数类型和修饰符构造的参数列表 `F` ，如下所述。</span><span class="sxs-lookup"><span data-stu-id="9e7e7-159">In an unsafe context, an implicit conversion exists from an address-of expression whose target is a method group `E` to a compatible function pointer type `F` if `E` contains at least one method that is applicable in its normal form to an argument list constructed by use of the parameter types and modifiers of `F`, as described in the following.</span></span>
- <span data-ttu-id="9e7e7-160">选择一个方法，该方法 `M` 对应于窗体的方法调用 `E(A)` ，其中包含以下修改：</span><span class="sxs-lookup"><span data-stu-id="9e7e7-160">A single method `M` is selected corresponding to a method invocation of the form `E(A)` with the following modifications:</span></span>
   - <span data-ttu-id="9e7e7-161">参数列表 `A` 是一个表达式列表，每个表达式都分类为一个变量，并使用类型和修饰符 (`ref` 、或的 `out` `in` 相应 _funcptr \_ 参数 \_ 列表_ 的) `F` 。</span><span class="sxs-lookup"><span data-stu-id="9e7e7-161">The arguments list `A` is a list of expressions, each classified as a variable and with the type and modifier (`ref`, `out`, or `in`) of the corresponding _funcptr\_parameter\_list_ of `F`.</span></span>
   - <span data-ttu-id="9e7e7-162">候选方法只是那些适用于其普通窗体的方法，而不是以其展开形式适用的方法。</span><span class="sxs-lookup"><span data-stu-id="9e7e7-162">The candidate methods are only those methods that are applicable in their normal form, not those applicable in their expanded form.</span></span>
   - <span data-ttu-id="9e7e7-163">候选方法仅为静态方法。</span><span class="sxs-lookup"><span data-stu-id="9e7e7-163">The candidate methods are only those methods that are static.</span></span>
- <span data-ttu-id="9e7e7-164">如果重载决策的算法产生错误，则会发生编译时错误。</span><span class="sxs-lookup"><span data-stu-id="9e7e7-164">If the algorithm of overload resolution produces an error, then a compile-time error occurs.</span></span> <span data-ttu-id="9e7e7-165">否则，该算法将生成单个最佳方法， `M` 该方法的参数数目与 `F` 相同，转换被视为存在。</span><span class="sxs-lookup"><span data-stu-id="9e7e7-165">Otherwise, the algorithm produces a single best method `M` having the same number of parameters as `F` and the conversion is considered to exist.</span></span>
- <span data-ttu-id="9e7e7-166">所选的方法 `M` 必须与) 与函数指针类型一起定义 (兼容 `F` 。</span><span class="sxs-lookup"><span data-stu-id="9e7e7-166">The selected method `M` must be compatible (as defined above) with the function pointer type `F`.</span></span> <span data-ttu-id="9e7e7-167">否则，将发生编译时错误。</span><span class="sxs-lookup"><span data-stu-id="9e7e7-167">Otherwise, a compile-time error occurs.</span></span>
- <span data-ttu-id="9e7e7-168">转换的结果是类型的函数指针 `F` 。</span><span class="sxs-lookup"><span data-stu-id="9e7e7-168">The result of the conversion is a function pointer of type `F`.</span></span>

<span data-ttu-id="9e7e7-169">这意味着开发人员可以依赖重载决策规则与地址运算符结合使用：</span><span class="sxs-lookup"><span data-stu-id="9e7e7-169">This means developers can depend on overload resolution rules to work in conjunction with the address-of operator:</span></span>

``` csharp
unsafe class Util {
    public static void Log() { }
    public static void Log(string p1) { }
    public static void Log(int i) { };

    void Use() {
        delegate*<void> a1 = &Log; // Log()
        delegate*<int, void> a2 = &Log; // Log(int i)

        // Error: ambiguous conversion from method group Log to "void*"
        void* v = &Log;
    }
```

<span data-ttu-id="9e7e7-170">将使用指令实现的地址为的运算符 `ldftn` 。</span><span class="sxs-lookup"><span data-stu-id="9e7e7-170">The address-of operator will be implemented using the `ldftn` instruction.</span></span>

<span data-ttu-id="9e7e7-171">此功能的限制：</span><span class="sxs-lookup"><span data-stu-id="9e7e7-171">Restrictions of this feature:</span></span>

- <span data-ttu-id="9e7e7-172">仅适用于标记为的方法 `static` 。</span><span class="sxs-lookup"><span data-stu-id="9e7e7-172">Only applies to methods marked as `static`.</span></span>
- <span data-ttu-id="9e7e7-173">非 `static` 本地函数不能在中使用 `&` 。</span><span class="sxs-lookup"><span data-stu-id="9e7e7-173">Non-`static` local functions cannot be used in `&`.</span></span> <span data-ttu-id="9e7e7-174">这些方法的实现细节特意不由语言指定。</span><span class="sxs-lookup"><span data-stu-id="9e7e7-174">The implementation details of these methods are deliberately not specified by the language.</span></span> <span data-ttu-id="9e7e7-175">这包括它们是静态的还是实例的，或者是否与它们一起发出的签名完全相同。</span><span class="sxs-lookup"><span data-stu-id="9e7e7-175">This includes whether they are static vs. instance or exactly what signature they are emitted with.</span></span>


### <a name="operators-on-function-pointer-types"></a><span data-ttu-id="9e7e7-176">函数指针类型上的运算符</span><span class="sxs-lookup"><span data-stu-id="9e7e7-176">Operators on Function Pointer Types</span></span>

<span data-ttu-id="9e7e7-177">将按如下所示修改不安全代码中的部分：</span><span class="sxs-lookup"><span data-stu-id="9e7e7-177">The section in unsafe code on operators is modified as such:</span></span>

> <span data-ttu-id="9e7e7-178">在不安全的上下文中，有多个构造可用于在 \_ 不 _funcptr type_s 的所有 _pointer type_s 上运行 \_ ：</span><span class="sxs-lookup"><span data-stu-id="9e7e7-178">In an unsafe context, several constructs are available for operating on all _pointer\_type_s that are not _funcptr\_type_s:</span></span>
>
> *  <span data-ttu-id="9e7e7-179">`*`运算符可用于 ([指针间接](../../spec/unsafe-code.md#pointer-indirection)寻址) 执行指针间接寻址。</span><span class="sxs-lookup"><span data-stu-id="9e7e7-179">The `*` operator may be used to perform pointer indirection ([Pointer indirection](../../spec/unsafe-code.md#pointer-indirection)).</span></span>
> *  <span data-ttu-id="9e7e7-180">`->`运算符可用于通过指针访问结构的成员 ([指针成员访问](../../spec/unsafe-code.md#pointer-member-access)) 。</span><span class="sxs-lookup"><span data-stu-id="9e7e7-180">The `->` operator may be used to access a member of a struct through a pointer ([Pointer member access](../../spec/unsafe-code.md#pointer-member-access)).</span></span>
> *  <span data-ttu-id="9e7e7-181">`[]`运算符可用于索引指针 ([指针元素访问](../../spec/unsafe-code.md#pointer-element-access)) 。</span><span class="sxs-lookup"><span data-stu-id="9e7e7-181">The `[]` operator may be used to index a pointer ([Pointer element access](../../spec/unsafe-code.md#pointer-element-access)).</span></span>
> *  <span data-ttu-id="9e7e7-182">`&`运算符可用于获取 () [地址的](../../spec/unsafe-code.md#the-address-of-operator)变量的地址。</span><span class="sxs-lookup"><span data-stu-id="9e7e7-182">The `&` operator may be used to obtain the address of a variable ([The address-of operator](../../spec/unsafe-code.md#the-address-of-operator)).</span></span>
> *  <span data-ttu-id="9e7e7-183">`++`和 `--` 运算符可用于递增和递减指针 ([指针增量和减量](../../spec/unsafe-code.md#pointer-increment-and-decrement)) 。</span><span class="sxs-lookup"><span data-stu-id="9e7e7-183">The `++` and `--` operators may be used to increment and decrement pointers ([Pointer increment and decrement](../../spec/unsafe-code.md#pointer-increment-and-decrement)).</span></span>
> *  <span data-ttu-id="9e7e7-184">`+`和 `-` 运算符可用于 ([指针算法](../../spec/unsafe-code.md#pointer-arithmetic)) 执行指针算法。</span><span class="sxs-lookup"><span data-stu-id="9e7e7-184">The `+` and `-` operators may be used to perform pointer arithmetic ([Pointer arithmetic](../../spec/unsafe-code.md#pointer-arithmetic)).</span></span>
> *  <span data-ttu-id="9e7e7-185">`==` `!=` 可以使用、、、、 `<` `>` `<=` 和 `=>` 运算符来比较指针)  ([指针比较](../../spec/unsafe-code.md#pointer-comparison)。</span><span class="sxs-lookup"><span data-stu-id="9e7e7-185">The `==`, `!=`, `<`, `>`, `<=`, and `=>` operators may be used to compare pointers ([Pointer comparison](../../spec/unsafe-code.md#pointer-comparison)).</span></span>
> *  <span data-ttu-id="9e7e7-186">`stackalloc`运算符可用于从调用堆栈 ([固定大小缓冲区](../../spec/unsafe-code.md#fixed-size-buffers)) 分配内存。</span><span class="sxs-lookup"><span data-stu-id="9e7e7-186">The `stackalloc` operator may be used to allocate memory from the call stack ([Fixed size buffers](../../spec/unsafe-code.md#fixed-size-buffers)).</span></span>
> *  <span data-ttu-id="9e7e7-187">`fixed`语句可用于临时修复变量，因此可以 ([fixed 语句](../../spec/unsafe-code.md#the-fixed-statement)) 获取其地址。</span><span class="sxs-lookup"><span data-stu-id="9e7e7-187">The `fixed` statement may be used to temporarily fix a variable so its address can be obtained ([The fixed statement](../../spec/unsafe-code.md#the-fixed-statement)).</span></span>
> 
> <span data-ttu-id="9e7e7-188">在不安全的上下文中，有多个构造可用于在所有 _funcptr type_s 上操作 \_ ：</span><span class="sxs-lookup"><span data-stu-id="9e7e7-188">In an unsafe context, several constructs are available for operating on all _funcptr\_type_s:</span></span>
> *  <span data-ttu-id="9e7e7-189">`&`运算符可用于获取静态方法的地址 ([允许地址的目标方法](#allow-address-of-to-target-methods)) </span><span class="sxs-lookup"><span data-stu-id="9e7e7-189">The `&` operator may be used to obtain the address of static methods ([Allow address-of to target methods](#allow-address-of-to-target-methods))</span></span>
> *  <span data-ttu-id="9e7e7-190">`==` `!=` 可以使用、、、、 `<` `>` `<=` 和 `=>` 运算符来比较指针)  ([指针比较](../../spec/unsafe-code.md#pointer-comparison)。</span><span class="sxs-lookup"><span data-stu-id="9e7e7-190">The `==`, `!=`, `<`, `>`, `<=`, and `=>` operators may be used to compare pointers ([Pointer comparison](../../spec/unsafe-code.md#pointer-comparison)).</span></span>

<span data-ttu-id="9e7e7-191">此外，我们修改中的所有部分 `Pointers in expressions` 以禁止函数指针类型（和除外） `Pointer comparison` `The sizeof operator` 。</span><span class="sxs-lookup"><span data-stu-id="9e7e7-191">Additionally, we modify all the sections in `Pointers in expressions` to forbid function pointer types, except `Pointer comparison` and `The sizeof operator`.</span></span>

### <a name="better-function-member"></a><span data-ttu-id="9e7e7-192">更好的函数成员</span><span class="sxs-lookup"><span data-stu-id="9e7e7-192">Better function member</span></span>

<span data-ttu-id="9e7e7-193">更好的函数成员规范将更改为包含以下行：</span><span class="sxs-lookup"><span data-stu-id="9e7e7-193">The better function member specification will be changed to include the following line:</span></span>

> <span data-ttu-id="9e7e7-194">`delegate*`比`void*`</span><span class="sxs-lookup"><span data-stu-id="9e7e7-194">A `delegate*` is more specific than `void*`</span></span>

<span data-ttu-id="9e7e7-195">这意味着，可以在和上重载， `void*` `delegate*` 但仍揭示使用地址运算符。</span><span class="sxs-lookup"><span data-stu-id="9e7e7-195">This means that it is possible to overload on `void*` and a `delegate*` and still sensibly use the address-of operator.</span></span>

### <a name="type-inference"></a><span data-ttu-id="9e7e7-196">类型推断</span><span class="sxs-lookup"><span data-stu-id="9e7e7-196">Type Inference</span></span>

<span data-ttu-id="9e7e7-197">在不安全代码中，对类型推理算法进行了以下更改：</span><span class="sxs-lookup"><span data-stu-id="9e7e7-197">In unsafe code, the following changes are made to the type inference algorithms:</span></span>

#### <a name="input-types"></a><span data-ttu-id="9e7e7-198">输入类型</span><span class="sxs-lookup"><span data-stu-id="9e7e7-198">Input types</span></span>

https://github.com/dotnet/csharplang/blob/master/spec/expressions.md#input-types

<span data-ttu-id="9e7e7-199">添加了以下内容：</span><span class="sxs-lookup"><span data-stu-id="9e7e7-199">The following is added:</span></span>

> <span data-ttu-id="9e7e7-200">如果 `E` 是方法组的地址并且 `T` 是函数指针类型，则的所有参数类型 `T` 均为类型为的输入类型 `E` `T` 。</span><span class="sxs-lookup"><span data-stu-id="9e7e7-200">If `E` is an address-of method group and `T` is a function pointer type then all the parameter types of `T` are input types of `E` with type `T`.</span></span>

#### <a name="output-types"></a><span data-ttu-id="9e7e7-201">输出类型</span><span class="sxs-lookup"><span data-stu-id="9e7e7-201">Output types</span></span>

https://github.com/dotnet/csharplang/blob/master/spec/expressions.md#output-types

<span data-ttu-id="9e7e7-202">添加了以下内容：</span><span class="sxs-lookup"><span data-stu-id="9e7e7-202">The following is added:</span></span>

> <span data-ttu-id="9e7e7-203">如果 `E` 是方法组地址并且 `T` 是函数指针类型，则的返回类型 `T` 是类型为的输出类型 `E` `T` 。</span><span class="sxs-lookup"><span data-stu-id="9e7e7-203">If `E` is an address-of method group and `T` is a function pointer type then the return type of `T` is an output type of `E` with type `T`.</span></span>

#### <a name="output-type-inferences"></a><span data-ttu-id="9e7e7-204">输出类型推断</span><span class="sxs-lookup"><span data-stu-id="9e7e7-204">Output type inferences</span></span>

https://github.com/dotnet/csharplang/blob/master/spec/expressions.md#output-type-inferences

<span data-ttu-id="9e7e7-205">在项目符号2和3之间添加以下项目符号：</span><span class="sxs-lookup"><span data-stu-id="9e7e7-205">The following bullet is added between bullets 2 and 3:</span></span>

> * <span data-ttu-id="9e7e7-206">如果 `E` 是方法组的地址，并且 `T` 是具有参数类型和返回类型的函数指针类型 `T1...Tk` `Tb` ，且类型为的重载解析 `E` 产生了 `T1..Tk` 返回类型的单个方法 `U` ，则将从到进行 _下限推理_ `U` `Tb` 。</span><span class="sxs-lookup"><span data-stu-id="9e7e7-206">If `E` is an address-of method group and `T` is a function pointer type with parameter types `T1...Tk` and return type `Tb`, and overload resolution of `E` with the types `T1..Tk` yields a single method with return type `U`, then a _lower-bound inference_ is made from `U` to `Tb`.</span></span>

#### <a name="exact-inferences"></a><span data-ttu-id="9e7e7-207">精确推断</span><span class="sxs-lookup"><span data-stu-id="9e7e7-207">Exact inferences</span></span>

https://github.com/dotnet/csharplang/blob/master/spec/expressions.md#exact-inferences

<span data-ttu-id="9e7e7-208">以下子项目符号将作为方案添加到项目符号2：</span><span class="sxs-lookup"><span data-stu-id="9e7e7-208">The following sub-bullet is added as a case to bullet 2:</span></span>

> * <span data-ttu-id="9e7e7-209">`V` 是一个函数指针类型 `delegate*<V2..Vk, V1>` `U` ，并且是一个函数指针类型 `delegate*<U2..Uk, U1>` ，的调用约定与 `V` 相同 `U` ，并且的 refness 与 `Vi` 相同 `Ui` 。</span><span class="sxs-lookup"><span data-stu-id="9e7e7-209">`V` is a function pointer type `delegate*<V2..Vk, V1>` and `U` is a function pointer type `delegate*<U2..Uk, U1>`, and the calling convention of `V` is identical to `U`, and the refness of `Vi` is identical to `Ui`.</span></span>

#### <a name="lower-bound-inferences"></a><span data-ttu-id="9e7e7-210">下限推理</span><span class="sxs-lookup"><span data-stu-id="9e7e7-210">Lower-bound inferences</span></span>

https://github.com/dotnet/csharplang/blob/master/spec/expressions.md#lower-bound-inferences

<span data-ttu-id="9e7e7-211">以下事例添加到了项目符号3：</span><span class="sxs-lookup"><span data-stu-id="9e7e7-211">The following case is added to bullet 3:</span></span>

> * <span data-ttu-id="9e7e7-212">`V` 是一个函数指针类型， `delegate*<V2..Vk, V1>` 并且存在与相同的函数指针类型 `delegate*<U2..Uk, U1>` ，的 `U` `delegate*<U2..Uk, U1>` 调用约定与相同，并且的 `V` refness 与 `U` `Vi` 相同 `Ui` 。</span><span class="sxs-lookup"><span data-stu-id="9e7e7-212">`V` is a function pointer type `delegate*<V2..Vk, V1>` and there is a function pointer type `delegate*<U2..Uk, U1>` such that `U` is identical to `delegate*<U2..Uk, U1>`, and the calling convention of `V` is identical to `U`, and the refness of `Vi` is identical to `Ui`.</span></span>

<span data-ttu-id="9e7e7-213">将从到的推理的第一个项目符号 `Ui` `Vi` 修改为：</span><span class="sxs-lookup"><span data-stu-id="9e7e7-213">The first bullet of inference from `Ui` to `Vi` is modified to:</span></span>

> * <span data-ttu-id="9e7e7-214">如果不是 `U` 函数指针类型并且 `Ui` 未知是引用类型，或者是 `U` 函数指针类型并且未知的是函数 `Ui` 指针类型或引用类型，则进行 _精确推理_</span><span class="sxs-lookup"><span data-stu-id="9e7e7-214">If `U` is not a function pointer type and `Ui` is not known to be a reference type, or if `U` is a function pointer type and `Ui` is not known to be a function pointer type or a reference type, then an _exact inference_ is made</span></span>

<span data-ttu-id="9e7e7-215">然后，将从到的推理的第三个项目符号后面添加 `Ui` 到 `Vi` ：</span><span class="sxs-lookup"><span data-stu-id="9e7e7-215">Then, added after the 3rd bullet of inference from `Ui` to `Vi`:</span></span>

> * <span data-ttu-id="9e7e7-216">否则，如果 `V` 为， `delegate*<V2..Vk, V1>` 则推理依赖于的第 i 个参数 `delegate*<V2..Vk, V1>` ：</span><span class="sxs-lookup"><span data-stu-id="9e7e7-216">Otherwise, if `V` is `delegate*<V2..Vk, V1>` then inference depends on the i-th parameter of `delegate*<V2..Vk, V1>`:</span></span>
>    * <span data-ttu-id="9e7e7-217">如果 V1：</span><span class="sxs-lookup"><span data-stu-id="9e7e7-217">If V1:</span></span>
>        * <span data-ttu-id="9e7e7-218">如果返回值为，则进行 _下限推理_ 。</span><span class="sxs-lookup"><span data-stu-id="9e7e7-218">If the return is by value, then a _lower-bound inference_ is made.</span></span>
>        * <span data-ttu-id="9e7e7-219">如果按引用返回，则进行 _精确推理_ 。</span><span class="sxs-lookup"><span data-stu-id="9e7e7-219">If the return is by reference, then an _exact inference_ is made.</span></span>
>    * <span data-ttu-id="9e7e7-220">如果 V2 .。。Vk</span><span class="sxs-lookup"><span data-stu-id="9e7e7-220">If V2..Vk:</span></span>
>        * <span data-ttu-id="9e7e7-221">如果参数的值为，则进行 _上限推理_ 。</span><span class="sxs-lookup"><span data-stu-id="9e7e7-221">If the parameter is by value, then an _upper-bound inference_ is made.</span></span>
>        * <span data-ttu-id="9e7e7-222">如果参数是按引用进行的，则进行 _精确推理_ 。</span><span class="sxs-lookup"><span data-stu-id="9e7e7-222">If the parameter is by reference, then an _exact inference_ is made.</span></span>

#### <a name="upper-bound-inferences"></a><span data-ttu-id="9e7e7-223">上限推理</span><span class="sxs-lookup"><span data-stu-id="9e7e7-223">Upper-bound inferences</span></span>

https://github.com/dotnet/csharplang/blob/master/spec/expressions.md#upper-bound-inferences

<span data-ttu-id="9e7e7-224">将以下事例添加到项目符号2：</span><span class="sxs-lookup"><span data-stu-id="9e7e7-224">The following case is added to bullet 2:</span></span>

> * <span data-ttu-id="9e7e7-225">`U` 是一个函数指针类型， `delegate*<U2..Uk, U1>` 并且 `V` 是一个与相同的函数指针类型 `delegate*<V2..Vk, V1>` ，的调用约定与 `U` 相同 `V` ，并且的 refness 与 `Ui` 相同 `Vi` 。</span><span class="sxs-lookup"><span data-stu-id="9e7e7-225">`U` is a function pointer type `delegate*<U2..Uk, U1>` and `V` is a function pointer type which is identical to `delegate*<V2..Vk, V1>`, and the calling convention of `U` is identical to `V`, and the refness of `Ui` is identical to `Vi`.</span></span>

<span data-ttu-id="9e7e7-226">将从到的推理的第一个项目符号 `Ui` `Vi` 修改为：</span><span class="sxs-lookup"><span data-stu-id="9e7e7-226">The first bullet of inference from `Ui` to `Vi` is modified to:</span></span>

> * <span data-ttu-id="9e7e7-227">如果不是 `U` 函数指针类型并且 `Ui` 未知是引用类型，或者是 `U` 函数指针类型并且未知的是函数 `Ui` 指针类型或引用类型，则进行 _精确推理_</span><span class="sxs-lookup"><span data-stu-id="9e7e7-227">If `U` is not a function pointer type and `Ui` is not known to be a reference type, or if `U` is a function pointer type and `Ui` is not known to be a function pointer type or a reference type, then an _exact inference_ is made</span></span>

<span data-ttu-id="9e7e7-228">然后，将从到的推理的第三个项目符号后面添加 `Ui` 到 `Vi` ：</span><span class="sxs-lookup"><span data-stu-id="9e7e7-228">Then added after the 3rd bullet of inference from `Ui` to `Vi`:</span></span>

> * <span data-ttu-id="9e7e7-229">否则，如果 `U` 为， `delegate*<U2..Uk, U1>` 则推理依赖于的第 i 个参数 `delegate*<U2..Uk, U1>` ：</span><span class="sxs-lookup"><span data-stu-id="9e7e7-229">Otherwise, if `U` is `delegate*<U2..Uk, U1>` then inference depends on the i-th parameter of `delegate*<U2..Uk, U1>`:</span></span>
>    * <span data-ttu-id="9e7e7-230">如果 U1：</span><span class="sxs-lookup"><span data-stu-id="9e7e7-230">If U1:</span></span>
>        * <span data-ttu-id="9e7e7-231">如果返回值为，则进行 _上限推理_ 。</span><span class="sxs-lookup"><span data-stu-id="9e7e7-231">If the return is by value, then an _upper-bound inference_ is made.</span></span>
>        * <span data-ttu-id="9e7e7-232">如果按引用返回，则进行 _精确推理_ 。</span><span class="sxs-lookup"><span data-stu-id="9e7e7-232">If the return is by reference, then an _exact inference_ is made.</span></span>
>    * <span data-ttu-id="9e7e7-233">如果英式</span><span class="sxs-lookup"><span data-stu-id="9e7e7-233">If U2..Uk:</span></span>
>        * <span data-ttu-id="9e7e7-234">如果参数的值为，则进行 _下限推理_ 。</span><span class="sxs-lookup"><span data-stu-id="9e7e7-234">If the parameter is by value, then a _lower-bound inference_ is made.</span></span>
>        * <span data-ttu-id="9e7e7-235">如果参数是按引用进行的，则进行 _精确推理_ 。</span><span class="sxs-lookup"><span data-stu-id="9e7e7-235">If the parameter is by reference, then an _exact inference_ is made.</span></span>

## <a name="metadata-representation-of-in-out-and-ref-readonly-parameters-and-return-types"></a><span data-ttu-id="9e7e7-236">`in`、 `out` 、 `ref readonly` 参数和返回类型的元数据表示形式</span><span class="sxs-lookup"><span data-stu-id="9e7e7-236">Metadata representation of `in`, `out`, and `ref readonly` parameters and return types</span></span>

<span data-ttu-id="9e7e7-237">函数指针签名没有参数标志位置，因此，我们必须使用 modreqs 对参数和返回类型是 `in` 、 `out` 还是进行编码 `ref readonly` 。</span><span class="sxs-lookup"><span data-stu-id="9e7e7-237">Function pointer signatures have no parameter flags location, so we must encode whether parameters and the return type are `in`, `out`, or `ref readonly` by using modreqs.</span></span>

### `in`

<span data-ttu-id="9e7e7-238">将 `System.Runtime.InteropServices.InAttribute` 作为 `modreq` 参数或返回类型上的 ref 说明符的重复使用，以表示以下内容：</span><span class="sxs-lookup"><span data-stu-id="9e7e7-238">We reuse `System.Runtime.InteropServices.InAttribute`, applied as a `modreq` to the ref specifier on a parameter or return type, to mean the following:</span></span>
* <span data-ttu-id="9e7e7-239">如果应用于参数 ref 说明符，此参数将被视为 `in` 。</span><span class="sxs-lookup"><span data-stu-id="9e7e7-239">If applied to a parameter ref specifier, this parameter is treated as `in`.</span></span>
* <span data-ttu-id="9e7e7-240">如果应用于返回类型 ref 说明符，返回类型将被视为 `ref readonly` 。</span><span class="sxs-lookup"><span data-stu-id="9e7e7-240">If applied to the return type ref specifier, the return type is treated as `ref readonly`.</span></span>

### `out`

<span data-ttu-id="9e7e7-241">我们使用将 `System.Runtime.InteropServices.OutAttribute` 作为 `modreq` 参数类型上的 ref 说明符的，以表示该参数是一个 `out` 参数。</span><span class="sxs-lookup"><span data-stu-id="9e7e7-241">We use `System.Runtime.InteropServices.OutAttribute`, applied as a `modreq` to the ref specifier on a parameter type, to mean that the parameter is an `out` parameter.</span></span>

### <a name="errors"></a><span data-ttu-id="9e7e7-242">错误</span><span class="sxs-lookup"><span data-stu-id="9e7e7-242">Errors</span></span>

* <span data-ttu-id="9e7e7-243">将作为 modreq 应用于返回类型是错误的 `OutAttribute` 。</span><span class="sxs-lookup"><span data-stu-id="9e7e7-243">It is an error to apply `OutAttribute` as a modreq to a return type.</span></span>
* <span data-ttu-id="9e7e7-244">将 `InAttribute` 和 `OutAttribute` 作为 modreq 应用到参数类型是错误的。</span><span class="sxs-lookup"><span data-stu-id="9e7e7-244">It is an error to apply both `InAttribute` and `OutAttribute` as a modreq to a parameter type.</span></span>
* <span data-ttu-id="9e7e7-245">如果通过 modopt 指定任一方法，则它们将被忽略。</span><span class="sxs-lookup"><span data-stu-id="9e7e7-245">If either are specified via modopt, they are ignored.</span></span>

### <a name="metadata-representation-of-calling-conventions"></a><span data-ttu-id="9e7e7-246">调用约定的元数据表示形式</span><span class="sxs-lookup"><span data-stu-id="9e7e7-246">Metadata Representation of Calling Conventions</span></span>

<span data-ttu-id="9e7e7-247">调用约定在元数据的方法签名中通过签名中标志的组合进行编码 `CallKind` ，而在签名的开头有零个或多个 `modopt` 。</span><span class="sxs-lookup"><span data-stu-id="9e7e7-247">Calling conventions are encoded in a method signature in metadata by a combination of the `CallKind` flag in the signature and zero or more `modopt`s at the start of the signature.</span></span> <span data-ttu-id="9e7e7-248">ECMA-335 当前声明标志中的以下元素 `CallKind` ：</span><span class="sxs-lookup"><span data-stu-id="9e7e7-248">ECMA-335 currently declares the following elements in the `CallKind` flag:</span></span>

```antlr
CallKind
   : default
   | unmanaged cdecl
   | unmanaged fastcall
   | unmanaged thiscall
   | unmanaged stdcall
   | varargs
   ;
```

<span data-ttu-id="9e7e7-249">其中，c # 中的函数指针将支持除之外的所有函数 `varargs` 。</span><span class="sxs-lookup"><span data-stu-id="9e7e7-249">Of these, function pointers in C# will support all but `varargs`.</span></span>

<span data-ttu-id="9e7e7-250">此外，运行时 (和最终 335) 将更新为 `CallKind` 在新平台上包含新的。</span><span class="sxs-lookup"><span data-stu-id="9e7e7-250">In addition, the runtime (and eventually 335) will be updated to include a new `CallKind` on new platforms.</span></span> <span data-ttu-id="9e7e7-251">目前没有正式的名称，但本文档将用作 `unmanaged ext` 新的可扩展调用约定格式的占位符作为替代。</span><span class="sxs-lookup"><span data-stu-id="9e7e7-251">This does not have a formal name currently, but this document will use `unmanaged ext` as a placeholder to stand for the new extensible calling convention format.</span></span> <span data-ttu-id="9e7e7-252">不包含 `modopt` ， `unmanaged ext` 是平台默认调用约定， `unmanaged` 不带方括号。</span><span class="sxs-lookup"><span data-stu-id="9e7e7-252">With no `modopt`s, `unmanaged ext` is the platform default calling convention, `unmanaged` without the square brackets.</span></span>

#### <a name="mapping-the-calling_convention_specifier-to-a-callkind"></a><span data-ttu-id="9e7e7-253">将映射 `calling_convention_specifier` 到 `CallKind`</span><span class="sxs-lookup"><span data-stu-id="9e7e7-253">Mapping the `calling_convention_specifier` to a `CallKind`</span></span>

<span data-ttu-id="9e7e7-254">`calling_convention_specifier`省略或指定为的将 `managed` 映射到 `default` `CallKind` 。</span><span class="sxs-lookup"><span data-stu-id="9e7e7-254">A `calling_convention_specifier` that is omitted, or specified as `managed`, maps to the `default` `CallKind`.</span></span> <span data-ttu-id="9e7e7-255">这是 `CallKind` 未特性化的任何方法的默认值 `UnmanagedCallersOnly` 。</span><span class="sxs-lookup"><span data-stu-id="9e7e7-255">This is default `CallKind` of any method not attributed with `UnmanagedCallersOnly`.</span></span>

<span data-ttu-id="9e7e7-256">C # 识别从 ECMA 335 映射到特定现有非托管的4个特殊标识符 `CallKind` 。</span><span class="sxs-lookup"><span data-stu-id="9e7e7-256">C# recognizes 4 special identifiers that map to specific existing unmanaged `CallKind`s from ECMA 335.</span></span> <span data-ttu-id="9e7e7-257">若要进行此映射，必须自行指定这些标识符，无需任何其他标识符，并且此要求将编码到中的规范中 `unmanaged_calling_convention` 。</span><span class="sxs-lookup"><span data-stu-id="9e7e7-257">In order for this mapping to occur, these identifiers must be specified on their own, with no other identifiers, and this requirement is encoded into the spec for `unmanaged_calling_convention`s.</span></span> <span data-ttu-id="9e7e7-258">这些标识符 `Cdecl` `Thiscall` `Stdcall` `Fastcall` `unmanaged cdecl` `unmanaged thiscall` `unmanaged stdcall` `unmanaged fastcall` 分别分别对应于、、和。</span><span class="sxs-lookup"><span data-stu-id="9e7e7-258">These identifiers are `Cdecl`, `Thiscall`, `Stdcall`, and `Fastcall`, which correspond to `unmanaged cdecl`, `unmanaged thiscall`, `unmanaged stdcall`, and `unmanaged fastcall`, respectively.</span></span> <span data-ttu-id="9e7e7-259">如果指定了多个 `identifer` ，或单个不 `identifier` 属于专门识别的标识符，我们将对标识符执行特殊的名称查找，规则如下：</span><span class="sxs-lookup"><span data-stu-id="9e7e7-259">If more than one `identifer` is specified, or the single `identifier` is not of the specially recognized identifiers, we perform special name lookup on the identifier with the following rules:</span></span>

* <span data-ttu-id="9e7e7-260">在前面追加 `identifier` 字符串 `CallConv`</span><span class="sxs-lookup"><span data-stu-id="9e7e7-260">We prepend the `identifier` with the string `CallConv`</span></span>
* <span data-ttu-id="9e7e7-261">我们仅查看在命名空间中定义的类型 `System.Runtime.CompilerServices` 。</span><span class="sxs-lookup"><span data-stu-id="9e7e7-261">We look only at types defined in the `System.Runtime.CompilerServices` namespace.</span></span>
* <span data-ttu-id="9e7e7-262">我们仅查看在应用程序的核心库中定义的类型，它是用于定义 `System.Object` 和没有依赖项的库。</span><span class="sxs-lookup"><span data-stu-id="9e7e7-262">We look only at types defined in the core library of the application, which is the library that defines `System.Object` and has no dependencies.</span></span>
* <span data-ttu-id="9e7e7-263">我们仅探讨公共类型。</span><span class="sxs-lookup"><span data-stu-id="9e7e7-263">We look only at public types.</span></span>

<span data-ttu-id="9e7e7-264">如果在中指定的所有上 `identifier` 进行查找成功 `unmanaged_calling_convention` ，我们会将编码 `CallKind` 为 `unmanaged ext` ，并对 `modopt` 函数指针签名开头的一组中的每个已解析类型进行编码。</span><span class="sxs-lookup"><span data-stu-id="9e7e7-264">If lookup succeeds on all of the `identifier`s specified in an `unmanaged_calling_convention`, we encode the `CallKind` as `unmanaged ext`, and encode each of the resolved types in the set of `modopt`s at the beginning of the function pointer signature.</span></span> <span data-ttu-id="9e7e7-265">请注意，这些规则意味着用户不能 `identifier` 使用作为的前缀 `CallConv` ，因为这会导致查找 `CallConvCallConvVectorCall` 。</span><span class="sxs-lookup"><span data-stu-id="9e7e7-265">As a note, these rules mean that users cannot prefix these `identifier`s with `CallConv`, as that will result in looking up `CallConvCallConvVectorCall`.</span></span>

<span data-ttu-id="9e7e7-266">解释元数据时，首先查看 `CallKind` 。</span><span class="sxs-lookup"><span data-stu-id="9e7e7-266">When interpreting metadata, we first look at the `CallKind`.</span></span> <span data-ttu-id="9e7e7-267">如果不是 `unmanaged ext` ，我们将忽略 `modopt` 返回类型上的所有，以确定调用约定，并且仅使用 `CallKind` 。</span><span class="sxs-lookup"><span data-stu-id="9e7e7-267">If it is anything other than `unmanaged ext`, we ignore all `modopt`s on the return type for the purposes of determining the calling convention, and use only the `CallKind`.</span></span> <span data-ttu-id="9e7e7-268">如果 `CallKind` 为 `unmanaged ext` ，我们将查看函数指针类型开头的 modopts，并采用满足以下要求的所有类型的并集：</span><span class="sxs-lookup"><span data-stu-id="9e7e7-268">If the `CallKind` is `unmanaged ext`, we look at the modopts at the start of the function pointer type, taking the union of all types that meet the following requirements:</span></span>

* <span data-ttu-id="9e7e7-269">是在核心库中定义的，它是不引用其他库和定义的库 `System.Object` 。</span><span class="sxs-lookup"><span data-stu-id="9e7e7-269">The is defined in the core library, which is the library that references no other libraries and defines `System.Object`.</span></span>
* <span data-ttu-id="9e7e7-270">类型是在命名空间中定义的 `System.Runtime.CompilerServices` 。</span><span class="sxs-lookup"><span data-stu-id="9e7e7-270">The type is defined in the `System.Runtime.CompilerServices` namespace.</span></span>
* <span data-ttu-id="9e7e7-271">类型以前缀开头 `CallConv` 。</span><span class="sxs-lookup"><span data-stu-id="9e7e7-271">The type starts with the prefix `CallConv`.</span></span>
* <span data-ttu-id="9e7e7-272">类型是公共的。</span><span class="sxs-lookup"><span data-stu-id="9e7e7-272">The type is public.</span></span>

 <span data-ttu-id="9e7e7-273">这表示 `identifier` `unmanaged_calling_convention` 当在源中定义函数指针类型时对中的执行查找时必须找到的类型。</span><span class="sxs-lookup"><span data-stu-id="9e7e7-273">These represent the types that must be found when performing lookup on the `identifier`s in an `unmanaged_calling_convention` when defining a function pointer type in source.</span></span>

<span data-ttu-id="9e7e7-274">`CallKind` `unmanaged ext` 如果目标运行时不支持该功能，则尝试将函数指针与的结合使用是错误的。</span><span class="sxs-lookup"><span data-stu-id="9e7e7-274">It is an error to attempt to use a function pointer with a `CallKind` of `unmanaged ext` if the target runtime does not support the feature.</span></span> <span data-ttu-id="9e7e7-275">这将通过查找是否存在常量来确定 `System.Runtime.CompilerServices.RuntimeFeature.UnmanagedCallKind` 。</span><span class="sxs-lookup"><span data-stu-id="9e7e7-275">This will be determined by looking for the presence of the `System.Runtime.CompilerServices.RuntimeFeature.UnmanagedCallKind` constant.</span></span> <span data-ttu-id="9e7e7-276">如果此常量存在，则将运行时视为支持该功能。</span><span class="sxs-lookup"><span data-stu-id="9e7e7-276">If this constant is present, the runtime is considered to support the feature.</span></span>

### `System.Runtime.InteropServices.UnmanagedCallersOnlyAttribute`

<span data-ttu-id="9e7e7-277">`System.Runtime.InteropServices.UnmanagedCallersOnlyAttribute` 是 CLR 用来指示应使用特定调用约定调用方法的属性。</span><span class="sxs-lookup"><span data-stu-id="9e7e7-277">`System.Runtime.InteropServices.UnmanagedCallersOnlyAttribute` is an attribute used by the CLR to indicate that a method should be called with a specific calling convention.</span></span> <span data-ttu-id="9e7e7-278">因此，我们引入了以下对使用该属性的支持：</span><span class="sxs-lookup"><span data-stu-id="9e7e7-278">Because of this, we introduce the following support for working with the attribute:</span></span>

* <span data-ttu-id="9e7e7-279">直接从 c # 中调用使用此属性批注的方法是错误的。</span><span class="sxs-lookup"><span data-stu-id="9e7e7-279">It is an error to directly call a method annotated with this attribute from C#.</span></span> <span data-ttu-id="9e7e7-280">用户必须获取指向方法的函数指针，然后调用该指针。</span><span class="sxs-lookup"><span data-stu-id="9e7e7-280">Users must obtain a function pointer to the method and then invoke that pointer.</span></span>
* <span data-ttu-id="9e7e7-281">将该特性应用于一般静态方法或普通静态本地函数之外的任何内容是错误的。</span><span class="sxs-lookup"><span data-stu-id="9e7e7-281">It is an error to apply the attribute to anything other than an ordinary static method or ordinary static local function.</span></span>
<span data-ttu-id="9e7e7-282">C # 编译器会将从元数据导入的任何非静态或静态非普通方法标记为该语言不支持的属性。</span><span class="sxs-lookup"><span data-stu-id="9e7e7-282">The C# compiler will mark any non-static or static non-ordinary methods imported from metadata with this attribute as unsupported by the language.</span></span>
* <span data-ttu-id="9e7e7-283">对于用特性标记的方法，如果该方法的参数或返回类型不是，则是错误的 `unmanaged_type` 。</span><span class="sxs-lookup"><span data-stu-id="9e7e7-283">It is an error for a method marked with the attribute to have a parameter or return type that is not an `unmanaged_type`.</span></span>
* <span data-ttu-id="9e7e7-284">如果用特性标记的方法具有类型参数，则是错误的，即使这些类型参数被约束为也是如此 `unmanaged` 。</span><span class="sxs-lookup"><span data-stu-id="9e7e7-284">It is an error for a method marked with the attribute to have type parameters, even if those type parameters are constrained to `unmanaged`.</span></span>
* <span data-ttu-id="9e7e7-285">泛型类型中的方法使用属性进行标记是错误的。</span><span class="sxs-lookup"><span data-stu-id="9e7e7-285">It is an error for a method in a generic type to be marked with the attribute.</span></span>
* <span data-ttu-id="9e7e7-286">将用特性标记的方法转换为委托类型是错误的。</span><span class="sxs-lookup"><span data-stu-id="9e7e7-286">It is an error to convert a method marked with the attribute to a delegate type.</span></span>
* <span data-ttu-id="9e7e7-287">为指定的任何类型不 `UnmanagedCallersOnly.CallConvs` 符合 `modopt` 在元数据中调用约定的要求是错误的。</span><span class="sxs-lookup"><span data-stu-id="9e7e7-287">It is an error to specify any types for `UnmanagedCallersOnly.CallConvs` that do not meet the requirements for calling convention `modopt`s in metadata.</span></span>

<span data-ttu-id="9e7e7-288">确定使用有效特性标记的方法的调用约定时 `UnmanagedCallersOnly` ，编译器将对属性中指定的类型执行以下检查， `CallConvs` 以确定 `CallKind` `modopt` 应该用于确定调用约定的有效和。</span><span class="sxs-lookup"><span data-stu-id="9e7e7-288">When determining the calling convention of a method marked with a valid `UnmanagedCallersOnly` attribute, the compiler performs the following checks on the types specified in the `CallConvs` property to determine the effective `CallKind` and `modopt`s that should be used to determine the calling convention:</span></span>

* <span data-ttu-id="9e7e7-289">如果未指定类型，则将 `CallKind` 视为，在 `unmanaged ext` `modopt` 函数指针类型的开头没有调用约定 s。</span><span class="sxs-lookup"><span data-stu-id="9e7e7-289">If no types are specified, the `CallKind` is treated as `unmanaged ext`, with no calling convention `modopt`s at the start of the function pointer type.</span></span>
* <span data-ttu-id="9e7e7-290">如果指定了一种类型，并且该类型命名为、、或，则将 `CallConvCdecl` `CallConvThiscall` `CallConvStdcall` `CallConvFastcall` `CallKind` 分别处理为 `unmanaged cdecl` 、 `unmanaged thiscall` 、 `unmanaged stdcall` 或，并且 `unmanaged fastcall` `modopt` 函数指针类型的开头没有调用约定 s。</span><span class="sxs-lookup"><span data-stu-id="9e7e7-290">If there is one type specified, and that type is named `CallConvCdecl`, `CallConvThiscall`, `CallConvStdcall`, or `CallConvFastcall`, the `CallKind` is treated as `unmanaged cdecl`, `unmanaged thiscall`, `unmanaged stdcall`, or `unmanaged fastcall`, respectively, with no calling convention `modopt`s at the start of the function pointer type.</span></span>
* <span data-ttu-id="9e7e7-291">如果指定了多个类型，或未将单个类型命名为上面专门调用的类型之一，则 `CallKind` 会将视为 `unmanaged ext` ，并将指定的类型的联合视为 `modopt` 函数指针类型的开头。</span><span class="sxs-lookup"><span data-stu-id="9e7e7-291">If multiple types are specified or the single type is not named one of the specially called out types above, the `CallKind` is treated as `unmanaged ext`, with the union of the types specified treated as `modopt`s at the start of the function pointer type.</span></span>

<span data-ttu-id="9e7e7-292">然后，编译器会查看此有效 `CallKind` 和 `modopt` 集合，并使用正常的元数据规则来确定函数指针类型的最终调用约定。</span><span class="sxs-lookup"><span data-stu-id="9e7e7-292">The compiler then looks at this effective `CallKind` and `modopt` collection and uses normal metadata rules to determine the final calling convention of the function pointer type.</span></span>

## <a name="open-questions"></a><span data-ttu-id="9e7e7-293">未解决的问题</span><span class="sxs-lookup"><span data-stu-id="9e7e7-293">Open Questions</span></span>

### <a name="detecting-runtime-support-for-unmanaged-ext"></a><span data-ttu-id="9e7e7-294">检测运行时支持 `unmanaged ext`</span><span class="sxs-lookup"><span data-stu-id="9e7e7-294">Detecting runtime support for `unmanaged ext`</span></span>

<span data-ttu-id="9e7e7-295">https://github.com/dotnet/runtime/issues/38135 跟踪添加此标志。</span><span class="sxs-lookup"><span data-stu-id="9e7e7-295">https://github.com/dotnet/runtime/issues/38135 tracks adding this flag.</span></span> <span data-ttu-id="9e7e7-296">根据评审的反馈，我们将使用问题中指定的属性，或使用的状态 `UnmanagedCallersOnlyAttribute` 作为确定运行时是否支持的标志 `unmanaged ext` 。</span><span class="sxs-lookup"><span data-stu-id="9e7e7-296">Depending on the feedback from review, we will either use the property specified in the issue, or use the presence of `UnmanagedCallersOnlyAttribute` as the flag that determines whether the runtimes supports `unmanaged ext`.</span></span>

## <a name="considerations"></a><span data-ttu-id="9e7e7-297">注意事项</span><span class="sxs-lookup"><span data-stu-id="9e7e7-297">Considerations</span></span>

### <a name="allow-instance-methods"></a><span data-ttu-id="9e7e7-298">允许实例方法</span><span class="sxs-lookup"><span data-stu-id="9e7e7-298">Allow instance methods</span></span>

<span data-ttu-id="9e7e7-299">可以通过使用 `EXPLICITTHIS` `instance` c # 代码) 中 (指定的 CLI 调用约定，将该建议扩展为支持实例方法。</span><span class="sxs-lookup"><span data-stu-id="9e7e7-299">The proposal could be extended to support instance methods by taking advantage of the `EXPLICITTHIS` CLI calling convention (named `instance` in C# code).</span></span> <span data-ttu-id="9e7e7-300">这种形式的 CLI 函数指针将 `this` 参数作为函数指针语法的显式第一个参数。</span><span class="sxs-lookup"><span data-stu-id="9e7e7-300">This form of CLI function pointers puts the `this` parameter as an explicit first parameter of the function pointer syntax.</span></span>

``` csharp
unsafe class Instance {
    void Use() {
        delegate* instance<Instance, string> f = &ToString;
        f(this);
    }
}
```

<span data-ttu-id="9e7e7-301">这听起来很复杂，但在提议中增加一些复杂化。</span><span class="sxs-lookup"><span data-stu-id="9e7e7-301">This is sound but adds some complication to the proposal.</span></span> <span data-ttu-id="9e7e7-302">特别是，由于与调用约定不同的函数指针 `instance` 并不 `managed` 兼容，即使两种情况都用于使用相同的 c # 签名调用托管方法。</span><span class="sxs-lookup"><span data-stu-id="9e7e7-302">Particularly because function pointers which differed by the calling convention `instance` and `managed` would be incompatible even though both cases are used to invoke managed methods with the same C# signature.</span></span> <span data-ttu-id="9e7e7-303">此外，在每种情况下，在这种情况下，有一个简单的解决方法是很有价值的：使用 `static` 本地函数。</span><span class="sxs-lookup"><span data-stu-id="9e7e7-303">Also in every case considered where this would be valuable to have there was a simple work around: use a `static` local function.</span></span>

``` csharp
unsafe class Instance {
    void Use() {
        static string toString(Instance i) => i.ToString();
        delegate*<Instance, string> f = &toString;
        f(this);
    }
}
```

### <a name="dont-require-unsafe-at-declaration"></a><span data-ttu-id="9e7e7-304">声明时不需要 unsafe</span><span class="sxs-lookup"><span data-stu-id="9e7e7-304">Don't require unsafe at declaration</span></span>

<span data-ttu-id="9e7e7-305">不需要 `unsafe` 每次使用 `delegate*` ，只需要在将方法组转换为的点上使用 `delegate*` 。</span><span class="sxs-lookup"><span data-stu-id="9e7e7-305">Instead of requiring `unsafe` at every use of a `delegate*`, only require it at the point where a method group is converted to a `delegate*`.</span></span> <span data-ttu-id="9e7e7-306">在这种情况下，核心安全问题就 (知道在值处于活动状态时不能卸载包含程序集) 。</span><span class="sxs-lookup"><span data-stu-id="9e7e7-306">This is where the core safety issues come into play (knowing that the containing assembly cannot be unloaded while the value is alive).</span></span> <span data-ttu-id="9e7e7-307">如果需要 `unsafe` 其他位置，则可能会出现过多。</span><span class="sxs-lookup"><span data-stu-id="9e7e7-307">Requiring `unsafe` on the other locations can be seen as excessive.</span></span>

<span data-ttu-id="9e7e7-308">这就是设计的最初设计方式。</span><span class="sxs-lookup"><span data-stu-id="9e7e7-308">This is how the design was originally intended.</span></span> <span data-ttu-id="9e7e7-309">但产生的语言规则觉得非常笨拙。</span><span class="sxs-lookup"><span data-stu-id="9e7e7-309">But the resulting language rules felt very awkward.</span></span> <span data-ttu-id="9e7e7-310">这是不可能的，因为这是一个指针值，并且即使不使用关键字，它仍可查看 `unsafe` 。</span><span class="sxs-lookup"><span data-stu-id="9e7e7-310">It's impossible to hide the fact that this is a pointer value and it kept peeking through even without the `unsafe` keyword.</span></span> <span data-ttu-id="9e7e7-311">例如，无法允许转换为， `object` 它不能是等的成员。 `class`C # 设计需要 `unsafe` 使用所有指针，因此这种设计遵循这一设计。</span><span class="sxs-lookup"><span data-stu-id="9e7e7-311">For example the conversion to `object` can't be allowed, it can't be a member of a `class`, etc ... The C# design is to require `unsafe` for all pointer uses and hence this design follows that.</span></span>

<span data-ttu-id="9e7e7-312">开发人员仍可使用与 `delegate*` 今天普通指针类型相同的方式在值顶部呈现安全包装。</span><span class="sxs-lookup"><span data-stu-id="9e7e7-312">Developers will still be capable of presenting a _safe_ wrapper on top of `delegate*` values the same way that they do for normal pointer types today.</span></span> <span data-ttu-id="9e7e7-313">请注意以下几点：</span><span class="sxs-lookup"><span data-stu-id="9e7e7-313">Consider:</span></span>

``` csharp
unsafe struct Action {
    delegate*<void> _ptr;

    Action(delegate*<void> ptr) => _ptr = ptr;
    public void Invoke() => _ptr();
}
```

### <a name="using-delegates"></a><span data-ttu-id="9e7e7-314">使用委托</span><span class="sxs-lookup"><span data-stu-id="9e7e7-314">Using delegates</span></span>

<span data-ttu-id="9e7e7-315">`delegate*`只需使用 `delegate` 具有以下类型的现有类型，而不是使用新的语法元素 `*` ：</span><span class="sxs-lookup"><span data-stu-id="9e7e7-315">Instead of using a new syntax element, `delegate*`, simply use existing `delegate` types with a `*` following the type:</span></span>

``` csharp
Func<object, object, bool>* ptr = &object.ReferenceEquals;
```

<span data-ttu-id="9e7e7-316">可以通过使用指定值的属性对类型进行批注来处理调用约定 `delegate` `CallingConvention` 。</span><span class="sxs-lookup"><span data-stu-id="9e7e7-316">Handling calling convention can be done by annotating the `delegate` types with an attribute that specifies a `CallingConvention` value.</span></span> <span data-ttu-id="9e7e7-317">缺少特性会表示托管调用约定。</span><span class="sxs-lookup"><span data-stu-id="9e7e7-317">The lack of an attribute would signify the managed calling convention.</span></span>

<span data-ttu-id="9e7e7-318">在 IL 中对此进行编码会出现问题。</span><span class="sxs-lookup"><span data-stu-id="9e7e7-318">Encoding this in IL is problematic.</span></span> <span data-ttu-id="9e7e7-319">基础值需要表示为一个指针，但它也必须：</span><span class="sxs-lookup"><span data-stu-id="9e7e7-319">The underlying value needs to be represented as a pointer yet it also must:</span></span>

1. <span data-ttu-id="9e7e7-320">具有唯一的类型，以允许具有不同函数指针类型的重载。</span><span class="sxs-lookup"><span data-stu-id="9e7e7-320">Have a unique type to allow for overloads with different function pointer types.</span></span>
1. <span data-ttu-id="9e7e7-321">对于跨程序集边界的 OHI 是等效的。</span><span class="sxs-lookup"><span data-stu-id="9e7e7-321">Be equivalent for OHI purposes across assembly boundaries.</span></span>

<span data-ttu-id="9e7e7-322">最后一点特别有问题。</span><span class="sxs-lookup"><span data-stu-id="9e7e7-322">The last point is particularly problematic.</span></span> <span data-ttu-id="9e7e7-323">这意味着，使用的每个程序集都 `Func<int>*` 必须在元数据中对等效类型进行编码，即使 `Func<int>*` 是在程序集中定义的，但不进行控制。</span><span class="sxs-lookup"><span data-stu-id="9e7e7-323">This mean that every assembly which uses `Func<int>*` must encode an equivalent type in metadata even though `Func<int>*` is defined in an assembly though don't control.</span></span>
<span data-ttu-id="9e7e7-324">此外，在不是 mscorlib 的程序集中，使用名称定义的任何其他类型 `System.Func<T>` 必须与 mscorlib 中定义的版本不同。</span><span class="sxs-lookup"><span data-stu-id="9e7e7-324">Additionally any other type which is defined with the name `System.Func<T>` in an assembly that is not mscorlib must be different than the version defined in mscorlib.</span></span>

<span data-ttu-id="9e7e7-325">研究的一个选项是发出这样的指针 `mod_req(Func<int>) void*` 。</span><span class="sxs-lookup"><span data-stu-id="9e7e7-325">One option that was explored was emitting such a pointer as `mod_req(Func<int>) void*`.</span></span> <span data-ttu-id="9e7e7-326">但这不起作用，因为 `mod_req` 无法绑定到 `TypeSpec` ，因此不能以泛型实例化为目标。</span><span class="sxs-lookup"><span data-stu-id="9e7e7-326">This doesn't work though as a `mod_req` cannot bind to a `TypeSpec` and hence cannot target generic instantiations.</span></span>

### <a name="named-function-pointers"></a><span data-ttu-id="9e7e7-327">命名函数指针</span><span class="sxs-lookup"><span data-stu-id="9e7e7-327">Named function pointers</span></span>

<span data-ttu-id="9e7e7-328">函数指针语法可能比较繁琐，尤其是在复杂情况下，例如嵌套函数指针。</span><span class="sxs-lookup"><span data-stu-id="9e7e7-328">The function pointer syntax can be cumbersome, particularly in complex cases like nested function pointers.</span></span> <span data-ttu-id="9e7e7-329">不是让开发人员在每次使用时都可以输入签名的函数指针的命名声明 `delegate` 。</span><span class="sxs-lookup"><span data-stu-id="9e7e7-329">Rather than have developers type out the signature every time the language could allow for named declarations of function pointers as is done with `delegate`.</span></span>

``` csharp
func* void Action();

unsafe class NamedExample {
    void M(Action a) {
        a();
    }
}
```

<span data-ttu-id="9e7e7-330">此处所述的问题是基础 CLI 基元没有名称，因此，这只是一个 c # 发明，需要使用一种元数据才能实现。</span><span class="sxs-lookup"><span data-stu-id="9e7e7-330">Part of the problem here is the underlying CLI primitive doesn't have names hence this would be purely a C# invention and require a bit of metadata work to enable.</span></span> <span data-ttu-id="9e7e7-331">这是可行的，但这是一个重要的工作。</span><span class="sxs-lookup"><span data-stu-id="9e7e7-331">That is doable but is a significant about of work.</span></span> <span data-ttu-id="9e7e7-332">实质上，它要求 c # 将类型定义表与这些名称一起使用。</span><span class="sxs-lookup"><span data-stu-id="9e7e7-332">It essentially requires C# to have a companion to the type def table purely for these names.</span></span>

<span data-ttu-id="9e7e7-333">此外，当检查命名函数指针的参数时，我们发现它们可以同样适用于许多其他方案。</span><span class="sxs-lookup"><span data-stu-id="9e7e7-333">Also when the arguments for named function pointers were examined we found they could apply equally well to a number of other scenarios.</span></span> <span data-ttu-id="9e7e7-334">例如，将命名元组声明为在所有情况下都可以轻松地在所有情况下都需要键入完整的签名。</span><span class="sxs-lookup"><span data-stu-id="9e7e7-334">For example it would be just as convenient to declare named tuples to reduce the need to type out the full signature in all cases.</span></span>

``` csharp
(int x, int y) Point;

class NamedTupleExample {
    void M(Point p) {
        Console.WriteLine(p.x);
    }
}
```

<span data-ttu-id="9e7e7-335">讨论后，我们决定不允许类型为的声明声明 `delegate*` 。</span><span class="sxs-lookup"><span data-stu-id="9e7e7-335">After discussion we decided to not allow named declaration of `delegate*` types.</span></span> <span data-ttu-id="9e7e7-336">如果我们发现，根据客户使用反馈，需要特别需要此功能，我们将调查适用于函数指针、元组、泛型等的命名解决方案。这种形式的可能与其他建议类似，如 `typedef` 语言的完全支持。</span><span class="sxs-lookup"><span data-stu-id="9e7e7-336">If we find there is significant need for this based on customer usage feedback then we will investigate a naming solution that works for function pointers, tuples, generics, etc ... This is likely to be similar in form to other suggestions like full `typedef` support in the language.</span></span>

## <a name="future-considerations"></a><span data-ttu-id="9e7e7-337">未来的注意事项</span><span class="sxs-lookup"><span data-stu-id="9e7e7-337">Future Considerations</span></span>

### <a name="static-delegates"></a><span data-ttu-id="9e7e7-338">静态委托</span><span class="sxs-lookup"><span data-stu-id="9e7e7-338">static delegates</span></span>

<span data-ttu-id="9e7e7-339">这是指允许类型声明的 [建议](https://github.com/dotnet/csharplang/issues/302) ， `delegate` 这些类型只能引用 `static` 成员。</span><span class="sxs-lookup"><span data-stu-id="9e7e7-339">This refers to [the proposal](https://github.com/dotnet/csharplang/issues/302) to allow for the declaration of `delegate` types which can only refer to `static` members.</span></span> <span data-ttu-id="9e7e7-340">其优势在于，此类 `delegate` 实例可以在性能敏感的情况下免费分配和更好地分配。</span><span class="sxs-lookup"><span data-stu-id="9e7e7-340">The advantage being that such `delegate` instances can be allocation free and better in performance sensitive scenarios.</span></span>

<span data-ttu-id="9e7e7-341">如果实现函数指针功能， `static delegate` 建议可能会关闭。此功能的建议优点是分配免费性质。</span><span class="sxs-lookup"><span data-stu-id="9e7e7-341">If the function pointer feature is implemented the `static delegate` proposal will likely be closed out. The proposed advantage of that feature is the allocation free nature.</span></span> <span data-ttu-id="9e7e7-342">但最近的调查发现，由于程序集卸载，无法实现。</span><span class="sxs-lookup"><span data-stu-id="9e7e7-342">However recent investigations have found that is not possible to achieve due to assembly unloading.</span></span> <span data-ttu-id="9e7e7-343">必须有一个从到它所引用的方法的强句柄， `static delegate` 以防止程序集从其下卸载。</span><span class="sxs-lookup"><span data-stu-id="9e7e7-343">There must be a strong handle from the `static delegate` to the method it refers to in order to keep the assembly from being unloaded out from under it.</span></span>

<span data-ttu-id="9e7e7-344">若要维护每个 `static delegate` 实例，则需要分配一个新的句柄，该句柄运行到提议的目标。</span><span class="sxs-lookup"><span data-stu-id="9e7e7-344">To maintain every `static delegate` instance would be required to allocate a new handle which runs counter to the goals of the proposal.</span></span> <span data-ttu-id="9e7e7-345">在某些设计中，分配可能会分摊到每个调用站点的单个分配，但这有点复杂，不值得权衡。</span><span class="sxs-lookup"><span data-stu-id="9e7e7-345">There were some designs where the allocation could be amortized to a single allocation per call-site but that was a bit complex and didn't seem worth the trade off.</span></span>

<span data-ttu-id="9e7e7-346">这意味着开发人员实质上必须决定以下权衡：</span><span class="sxs-lookup"><span data-stu-id="9e7e7-346">That means developers essentially have to decide between the following trade offs:</span></span>

1. <span data-ttu-id="9e7e7-347">程序集卸载面临的安全性：这需要分配，因此 `delegate` 已是一个足够的选项。</span><span class="sxs-lookup"><span data-stu-id="9e7e7-347">Safety in the face of assembly unloading: this requires allocations and hence `delegate` is already a sufficient option.</span></span>
1. <span data-ttu-id="9e7e7-348">程序集卸载没有任何安全：使用 `delegate*` 。</span><span class="sxs-lookup"><span data-stu-id="9e7e7-348">No safety in face of assembly unloading: use a `delegate*`.</span></span> <span data-ttu-id="9e7e7-349">这可以包装在中 `struct` ，以允许 `unsafe` 在其余代码的上下文之外使用。</span><span class="sxs-lookup"><span data-stu-id="9e7e7-349">This can be wrapped in a `struct` to allow usage outside an `unsafe` context in the rest of the code.</span></span>

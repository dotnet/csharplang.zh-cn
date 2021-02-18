---
ms.openlocfilehash: ff1e135451b1c911c36022f6e55fc4bc1bfe4557
ms.sourcegitcommit: 1f5b1dc19d21038b59bfce169fd49e121a5a1f4e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/18/2021
ms.locfileid: "101101818"
---
# <a name="static-delegates-superseded-by-csharp-90function-pointersmd"></a><span data-ttu-id="4479d-101">静态委托 (由取代 [。/csharp-9.0/function-pointers.md](../csharp-9.0/function-pointers.md)) </span><span class="sxs-lookup"><span data-stu-id="4479d-101">Static Delegates (superseded by [../csharp-9.0/function-pointers.md](../csharp-9.0/function-pointers.md))</span></span>

* <span data-ttu-id="4479d-102">[x] 建议</span><span class="sxs-lookup"><span data-stu-id="4479d-102">[x] Proposed</span></span>
* <span data-ttu-id="4479d-103">[] 原型：未启动</span><span class="sxs-lookup"><span data-stu-id="4479d-103">[ ] Prototype: Not Started</span></span>
* <span data-ttu-id="4479d-104">[] 实现：未启动</span><span class="sxs-lookup"><span data-stu-id="4479d-104">[ ] Implementation: Not Started</span></span>
* <span data-ttu-id="4479d-105">[] 规范：未启动</span><span class="sxs-lookup"><span data-stu-id="4479d-105">[ ] Specification: Not Started</span></span>

## <a name="summary"></a><span data-ttu-id="4479d-106">摘要</span><span class="sxs-lookup"><span data-stu-id="4479d-106">Summary</span></span>
[summary]: #summary

<span data-ttu-id="4479d-107">为 c # 语言提供常规用途的轻型回调功能。</span><span class="sxs-lookup"><span data-stu-id="4479d-107">Provide a general-purpose, lightweight callback capability to the C# language.</span></span>

## <a name="motivation"></a><span data-ttu-id="4479d-108">动机</span><span class="sxs-lookup"><span data-stu-id="4479d-108">Motivation</span></span>
[motivation]: #motivation

<span data-ttu-id="4479d-109">现在，用户可以使用类型创建回调 `System.Delegate` 。</span><span class="sxs-lookup"><span data-stu-id="4479d-109">Today, users have the ability to create callbacks using the `System.Delegate` type.</span></span> <span data-ttu-id="4479d-110">不过，这些都是相当的重型 (例如，需要一个堆分配，并始终将处理链接回调) 在一起。</span><span class="sxs-lookup"><span data-stu-id="4479d-110">However, these are fairly heavyweight (such as requiring a heap allocation and always having handling for chaining callbacks together).</span></span>

<span data-ttu-id="4479d-111">此外，不 `System.Delegate` 提供与非托管函数指针的最佳互操作性，也就是说，它在跨托管/非托管边界时无需直接复制并需要进行封送处理。</span><span class="sxs-lookup"><span data-stu-id="4479d-111">Additionally, `System.Delegate` does not provide the best interop with unmanaged function pointers, namely due being non-blittable and requiring marshalling anytime it crosses the managed/unmanaged boundary.</span></span>

<span data-ttu-id="4479d-112">只需稍作调整，就能提供一种新类型的委托，该委托类型为轻型、通用和 interops，适用于本机代码。</span><span class="sxs-lookup"><span data-stu-id="4479d-112">With a few minor tweaks, we could provide a new type of delegate that is lightweight, general-purpose, and interops well with native code.</span></span>

## <a name="detailed-design"></a><span data-ttu-id="4479d-113">详细设计</span><span class="sxs-lookup"><span data-stu-id="4479d-113">Detailed design</span></span>
[design]: #detailed-design

<span data-ttu-id="4479d-114">可以通过以下方法声明静态委托：</span><span class="sxs-lookup"><span data-stu-id="4479d-114">One would declare a static delegate via the following:</span></span>

```C#
static delegate int Func()
```

<span data-ttu-id="4479d-115">另外，还可以通过类似于的内容来特性声明 `System.Runtime.InteropServices.UnmanagedFunctionPointer` ，以便可以控制调用约定、字符串封送处理和设置的上一错误行为。</span><span class="sxs-lookup"><span data-stu-id="4479d-115">One could additionally attribute the declaration with something similar to `System.Runtime.InteropServices.UnmanagedFunctionPointer` so that the calling convention, string marshalling, and set last error behavior can be controlled.</span></span> <span data-ttu-id="4479d-116">注意：使用 `System.Runtime.InteropServices.UnmanagedFunctionPointer` 自身将不起作用，因为它仅可用于委托。</span><span class="sxs-lookup"><span data-stu-id="4479d-116">NOTE: Using `System.Runtime.InteropServices.UnmanagedFunctionPointer` itself will not work, as it is only usable on Delegates.</span></span>

<span data-ttu-id="4479d-117">编译器会将声明转换为类似于以下内容的内部表示形式</span><span class="sxs-lookup"><span data-stu-id="4479d-117">The declaration would get translated into an internal representation by the compiler that is similar to the following</span></span>

```C#
struct <Func>e__StaticDelegate
{
    IntPtr pFunction;

    static int WellKnownCompilerName();
}
```

<span data-ttu-id="4479d-118">也就是说，它由一个结构内部表示，该结构具有类型为的单个成员 `IntPtr` (这种结构是直接复制的，并且不会产生任何堆分配) ：</span><span class="sxs-lookup"><span data-stu-id="4479d-118">That is to say, it is internally represented by a struct that has a single member of type `IntPtr` (such a struct is blittable and does not incur any heap allocations):</span></span>
* <span data-ttu-id="4479d-119">成员包含要作为回调的函数的地址。</span><span class="sxs-lookup"><span data-stu-id="4479d-119">The member contains the address of the function that is to be the callback.</span></span>
* <span data-ttu-id="4479d-120">类型声明与回调的方法签名匹配的方法。</span><span class="sxs-lookup"><span data-stu-id="4479d-120">The type declares a method matching the method signature of the callback.</span></span>
* <span data-ttu-id="4479d-121">结构的名称不应是用户可构造 (与其他内部生成的后备结构) 相同。</span><span class="sxs-lookup"><span data-stu-id="4479d-121">The name of the struct should not be user-constructible (as we do with other internally generated backing structures).</span></span>
 * <span data-ttu-id="4479d-122">例如：固定大小缓冲区以 (格式生成名称为的结构，并将其作为 `<name>e__FixedBuffer` `<` 标识符的一部分， `>` 并使标识符在 c # 中不可构造，但在 IL) 中仍可用。</span><span class="sxs-lookup"><span data-stu-id="4479d-122">For example: fixed size buffers generate a struct with a name in the format of `<name>e__FixedBuffer` (`<` and `>` are part of the identifier and make the identifier not constructible in C#, but still useable in IL).</span></span>
* <span data-ttu-id="4479d-123">方法声明的名称应为在所有静态委托类型中使用的已知名称 (这允许编译器知道在确定签名) 时要查找的名称。</span><span class="sxs-lookup"><span data-stu-id="4479d-123">The name of the method declaration should be a well known name used across all static delegate types (this allows the compiler to know the name to look for when determining the signature).</span></span>

<span data-ttu-id="4479d-124">静态委托的值只能绑定到与回调签名匹配的静态方法。</span><span class="sxs-lookup"><span data-stu-id="4479d-124">The value of the static delegate can only be bound to a static method that matches the signature of the callback.</span></span>

<span data-ttu-id="4479d-125">不支持将回调链接在一起。</span><span class="sxs-lookup"><span data-stu-id="4479d-125">Chaining callbacks together is not supported.</span></span>

<span data-ttu-id="4479d-126">回调的调用将由 `calli` 指令实现。</span><span class="sxs-lookup"><span data-stu-id="4479d-126">Invocation of the callback would be implemented by the `calli` instruction.</span></span>

## <a name="drawbacks"></a><span data-ttu-id="4479d-127">缺点</span><span class="sxs-lookup"><span data-stu-id="4479d-127">Drawbacks</span></span>
[drawbacks]: #drawbacks

<span data-ttu-id="4479d-128">静态委托不能与使用常规委托的现有 Api 一起使用 (一个需要在同一签名) 的常规委托中包装说静态委托。</span><span class="sxs-lookup"><span data-stu-id="4479d-128">Static Delegates would not work with existing APIs that use regular delegates (one would need to wrap said static delegate in a regular delegate of the same signature).</span></span>
* <span data-ttu-id="4479d-129">给定，在 `System.Delegate` 内部表示为一组 `object` `IntPtr` (字段，可以 http://source.dot.net/#System.Private.CoreLib/src/System/Delegate.cs) 允许将静态委托隐式转换为 `System.Delegate` 具有匹配方法签名的。</span><span class="sxs-lookup"><span data-stu-id="4479d-129">Given that `System.Delegate` is represented internally as a set of `object` and `IntPtr` fields (http://source.dot.net/#System.Private.CoreLib/src/System/Delegate.cs), it would be possible to allow implicit conversion of a static delegate to a `System.Delegate` that has a matching method signature.</span></span> <span data-ttu-id="4479d-130">还可以按相反的方向提供显式转换，前提是符合 `System.Delegate` 作为静态委托的所有要求。</span><span class="sxs-lookup"><span data-stu-id="4479d-130">It would also be possible to provide an explicit conversion in the opposite direction, provided the `System.Delegate` conformed to all the requirements of being a static delegate.</span></span>

<span data-ttu-id="4479d-131">需要额外的工作，以使静态委托在核心框架中轻松使用。</span><span class="sxs-lookup"><span data-stu-id="4479d-131">Additional work would be needed to make Static Delegate readily usable in the core framework.</span></span>

## <a name="alternatives"></a><span data-ttu-id="4479d-132">备选方法</span><span class="sxs-lookup"><span data-stu-id="4479d-132">Alternatives</span></span>
[alternatives]: #alternatives

<span data-ttu-id="4479d-133">TBD</span><span class="sxs-lookup"><span data-stu-id="4479d-133">TBD</span></span>

## <a name="unresolved-questions"></a><span data-ttu-id="4479d-134">未解决的问题</span><span class="sxs-lookup"><span data-stu-id="4479d-134">Unresolved questions</span></span>
[unresolved]: #unresolved-questions

<span data-ttu-id="4479d-135">设计的哪些部分仍是待定的？</span><span class="sxs-lookup"><span data-stu-id="4479d-135">What parts of the design are still TBD?</span></span>

## <a name="design-meetings"></a><span data-ttu-id="4479d-136">设计会议</span><span class="sxs-lookup"><span data-stu-id="4479d-136">Design meetings</span></span>

<span data-ttu-id="4479d-137">链接到影响此建议的设计说明，并在一个句子中介绍它们所导致的更改。</span><span class="sxs-lookup"><span data-stu-id="4479d-137">Link to design notes that affect this proposal, and describe in one sentence for each what changes they led to.</span></span>



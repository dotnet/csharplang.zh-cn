---
ms.openlocfilehash: e345bb5d9a4f998f80c13a255cec808bd2c9299f
ms.sourcegitcommit: 0c25406d8a99064bb85d934bb32ffcf547753acc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/28/2020
ms.locfileid: "87297258"
---
# <a name="module-initializers"></a><span data-ttu-id="8b156-101">模块初始值设定项</span><span class="sxs-lookup"><span data-stu-id="8b156-101">Module Initializers</span></span>

* <span data-ttu-id="8b156-102">[x] 建议</span><span class="sxs-lookup"><span data-stu-id="8b156-102">[x] Proposed</span></span>
* <span data-ttu-id="8b156-103">[] 原型：[正在进行](https://github.com/jnm2/roslyn/tree/module_initializer)</span><span class="sxs-lookup"><span data-stu-id="8b156-103">[ ] Prototype: [In Progress](https://github.com/jnm2/roslyn/tree/module_initializer)</span></span>
* <span data-ttu-id="8b156-104">[] 实现：[正在进行](https://github.com/dotnet/roslyn/tree/features/module-initializers)</span><span class="sxs-lookup"><span data-stu-id="8b156-104">[ ] Implementation: [In Progress](https://github.com/dotnet/roslyn/tree/features/module-initializers)</span></span>
* <span data-ttu-id="8b156-105">[] 规范：[未启动]()</span><span class="sxs-lookup"><span data-stu-id="8b156-105">[ ] Specification: [Not Started]()</span></span>

## <a name="summary"></a><span data-ttu-id="8b156-106">摘要</span><span class="sxs-lookup"><span data-stu-id="8b156-106">Summary</span></span>
[summary]: #summary

<span data-ttu-id="8b156-107">尽管 .NET 平台具有直接支持编写程序集（从技术上讲为模块）的初始化代码的[功能](https://github.com/dotnet/runtime/blob/master/docs/design/specs/Ecma-335-Augments.md#module-initializer)，但它不是用 c # 公开的。</span><span class="sxs-lookup"><span data-stu-id="8b156-107">Although the .NET platform has a [feature](https://github.com/dotnet/runtime/blob/master/docs/design/specs/Ecma-335-Augments.md#module-initializer) that directly supports writing initialization code for the assembly (technically, the module), it is not exposed in C#.</span></span>  <span data-ttu-id="8b156-108">这是一个相当小的方案，但一旦进入该方案，解决方案就显得非常令人头痛。</span><span class="sxs-lookup"><span data-stu-id="8b156-108">This is a rather niche scenario, but once you run into it the solutions appear to be pretty painful.</span></span>  <span data-ttu-id="8b156-109">有[大量客户](https://www.google.com/search?q=.net+module+constructor+c%23&oq=.net+module+constructor)（在 Microsoft 内部和外部）正在解决问题，并且没有任何不清楚记录的情况。</span><span class="sxs-lookup"><span data-stu-id="8b156-109">There are reports of [a number of customers](https://www.google.com/search?q=.net+module+constructor+c%23&oq=.net+module+constructor) (inside and outside Microsoft) struggling with the problem, and there are no doubt more undocumented cases.</span></span>

## <a name="motivation"></a><span data-ttu-id="8b156-110">动机</span><span class="sxs-lookup"><span data-stu-id="8b156-110">Motivation</span></span>
[motivation]: #motivation

- <span data-ttu-id="8b156-111">允许库在加载时执行预先的一次性初始化，开销最小，用户无需显式调用任何内容</span><span class="sxs-lookup"><span data-stu-id="8b156-111">Enable libraries to do eager, one-time initialization when loaded, with minimal overhead and without the user needing to explicitly call anything</span></span>
- <span data-ttu-id="8b156-112">当前构造函数方法的一个特别难点 `static` 是，运行时必须对使用静态构造函数的类型使用额外的检查，以确定是否需要运行静态构造函数。</span><span class="sxs-lookup"><span data-stu-id="8b156-112">One particular pain point of current `static` constructor approaches is that the runtime must do additional checks on usage of a type with a static constructor, in order to decide whether the static constructor needs to be run or not.</span></span> <span data-ttu-id="8b156-113">这会增加可度量的开销。</span><span class="sxs-lookup"><span data-stu-id="8b156-113">This adds measurable overhead.</span></span>
- <span data-ttu-id="8b156-114">使源生成器无需显式调用任何内容即可运行一些全局初始化逻辑</span><span class="sxs-lookup"><span data-stu-id="8b156-114">Enable source generators to run some global initialization logic without the user needing to explicitly call anything</span></span>

## <a name="detailed-design"></a><span data-ttu-id="8b156-115">详细设计</span><span class="sxs-lookup"><span data-stu-id="8b156-115">Detailed design</span></span>
[design]: #detailed-design

<span data-ttu-id="8b156-116">通过使用属性修饰方法，可以将方法指定为模块初始值设定项 `[ModuleInitializer]` 。</span><span class="sxs-lookup"><span data-stu-id="8b156-116">A method can be designated as a module initializer by decorating it with a `[ModuleInitializer]` attribute.</span></span>

```cs
using System;
namespace System.Runtime.CompilerServices
{
    [AttributeUsage(AttributeTargets.Method, AllowMultiple = false)]
    public sealed class ModuleInitializerAttribute : Attribute { }
}
```

<span data-ttu-id="8b156-117">属性可如下所示：</span><span class="sxs-lookup"><span data-stu-id="8b156-117">The attribute can be used like this:</span></span>

```cs
using System.Runtime.CompilerServices;
class C
{
    [ModuleInitializer]
    internal static void M1()
    {
        // ...
    }
}
```

<span data-ttu-id="8b156-118">某些要求是针对具有此属性的方法施加的：</span><span class="sxs-lookup"><span data-stu-id="8b156-118">Some requirements are imposed on the method targeted with this attribute:</span></span>
1. <span data-ttu-id="8b156-119">方法必须是 `static` 。</span><span class="sxs-lookup"><span data-stu-id="8b156-119">The method must be `static`.</span></span>
1. <span data-ttu-id="8b156-120">方法必须是无参数的。</span><span class="sxs-lookup"><span data-stu-id="8b156-120">The method must be parameterless.</span></span>
1. <span data-ttu-id="8b156-121">该方法必须返回 `void`。</span><span class="sxs-lookup"><span data-stu-id="8b156-121">The method must return `void`.</span></span>
1. <span data-ttu-id="8b156-122">方法不能是泛型或包含在泛型类型中。</span><span class="sxs-lookup"><span data-stu-id="8b156-122">The method must not be generic or be contained in a generic type.</span></span>
1. <span data-ttu-id="8b156-123">此方法必须可从包含模块访问。</span><span class="sxs-lookup"><span data-stu-id="8b156-123">The method must be accessible from the containing module.</span></span>
    - <span data-ttu-id="8b156-124">这意味着该方法的有效可访问性必须为 `internal` 或 `public` 。</span><span class="sxs-lookup"><span data-stu-id="8b156-124">This means the method's effective accessibility must be `internal` or `public`.</span></span>
    - <span data-ttu-id="8b156-125">这也意味着该方法不能是本地函数。</span><span class="sxs-lookup"><span data-stu-id="8b156-125">This also means the method cannot be a local function.</span></span>
    
<span data-ttu-id="8b156-126">在编译中找到一个或多个具有此属性的有效方法时，编译器将发出一个调用每个属性化方法的模块初始值设定项。</span><span class="sxs-lookup"><span data-stu-id="8b156-126">When one or more valid methods with this attribute are found in a compilation, the compiler will emit a module initializer which calls each of the attributed methods.</span></span> <span data-ttu-id="8b156-127">调用将按保留但确定性顺序发出。</span><span class="sxs-lookup"><span data-stu-id="8b156-127">The calls will be emitted in a reserved, but deterministic order.</span></span>

## <a name="drawbacks"></a><span data-ttu-id="8b156-128">缺点</span><span class="sxs-lookup"><span data-stu-id="8b156-128">Drawbacks</span></span>
[drawbacks]: #drawbacks

<span data-ttu-id="8b156-129">为什么*不*应这样做？</span><span class="sxs-lookup"><span data-stu-id="8b156-129">Why should we *not* do this?</span></span>

- <span data-ttu-id="8b156-130">"注入" 模块初始值设定项的现有第三方工具或许足以满足要求使用此功能的用户。</span><span class="sxs-lookup"><span data-stu-id="8b156-130">Perhaps the existing third-party tooling for "injecting" module initializers is sufficient for users who have been asking for this feature.</span></span>

## <a name="design-meetings"></a><span data-ttu-id="8b156-131">设计会议</span><span class="sxs-lookup"><span data-stu-id="8b156-131">Design meetings</span></span>

### <a name="april-8th-2020"></a>[<span data-ttu-id="8b156-132">2020年4月8日</span><span class="sxs-lookup"><span data-stu-id="8b156-132">April 8th, 2020</span></span>](/meetings/2020/LDM-2020-04-08.md#module-initializers)

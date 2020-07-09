---
ms.openlocfilehash: a26af810ced3fe5a7d7108f38a22d40aa64543f5
ms.sourcegitcommit: cc68af0b2a6e2ef5780eeb43935600b5927ee720
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/09/2020
ms.locfileid: "86157164"
---
# <a name="repeated-attributes-in-partial-members"></a><span data-ttu-id="ccdab-101">分部成员中重复的属性</span><span class="sxs-lookup"><span data-stu-id="ccdab-101">Repeated Attributes in Partial Members</span></span>

## <a name="summary"></a><span data-ttu-id="ccdab-102">总结</span><span class="sxs-lookup"><span data-stu-id="ccdab-102">Summary</span></span>

<span data-ttu-id="ccdab-103">允许分部成员的每个声明独立应用未标记为的属性 `[AttributeUsage(AllowMultiple = true)]` ，前提是所有应用程序中的属性参数都是相同的。</span><span class="sxs-lookup"><span data-stu-id="ccdab-103">Allow each declaration of a partial member to independently apply an attribute not marked with `[AttributeUsage(AllowMultiple = true)]`, as long as the attribute arguments are identical in all applications.</span></span>

## <a name="motivation"></a><span data-ttu-id="ccdab-104">动机</span><span class="sxs-lookup"><span data-stu-id="ccdab-104">Motivation</span></span>

<span data-ttu-id="ccdab-105">考虑在 "partial" 方法中存在哪些特性时，语言会将这两个声明中所有相应位置的所有属性联合在一起。</span><span class="sxs-lookup"><span data-stu-id="ccdab-105">When considering what attributes are present on a 'partial' method, the language unions together all the attributes in all corresponding positions on both declarations.</span></span> <span data-ttu-id="ccdab-106">例如，下面的方法 `M` 具有属性 `A` 和 `B` 。</span><span class="sxs-lookup"><span data-stu-id="ccdab-106">For example, the method `M` below has attributes `A` and `B`.</span></span>

```cs
[A]
partial void M();

[B]
partial void M() { }
```

<span data-ttu-id="ccdab-107">这意味着，不标记的属性 `[AttributeUsage(AllowMultiple = true)]` 不能在这两个部分中出现：</span><span class="sxs-lookup"><span data-stu-id="ccdab-107">This means that attributes which are not marked `[AttributeUsage(AllowMultiple = true)]` cannot be present across both parts:</span></span>

```cs
[A]
partial void M();

[A] // error: duplicate attribute!
partial void M() { }
```

<span data-ttu-id="ccdab-108">这就是一个可用性/可读性问题，因为某些特性旨在通知用户和/或 maintainer 方法所需的预定义和后置条件的方法。</span><span class="sxs-lookup"><span data-stu-id="ccdab-108">This presents a usability/readability issue, because some attributes are designed to inform the user and/or maintainer of the method of what pre/postconditions or invariants the method requires.</span></span> <span data-ttu-id="ccdab-109">例如：</span><span class="sxs-lookup"><span data-stu-id="ccdab-109">For example:</span></span>

```cs
public partial bool TryGetValue([NotNullWhen(true)] out object? value);
public partial bool TryGetValue(out object? value) { ... }
```

<span data-ttu-id="ccdab-110">分部成员通常有助于代码生成器与最终用户之间的关系--每个参与方都提供部分成员的声明，以便代码生成器为用户提供功能，或访问生成的代码中的扩展点。</span><span class="sxs-lookup"><span data-stu-id="ccdab-110">A partial member typically facilitates the relationship between a code generator and an end user--each party provides one of the declarations of the partial member in order for a code generator to provide functionality to the user, or for the user to access an extension point in generated code.</span></span> <span data-ttu-id="ccdab-111">在只允许一个声明具有这些单应用程序属性的情况下，生成器和用户无法有效地将其要求传达给彼此。</span><span class="sxs-lookup"><span data-stu-id="ccdab-111">In the situation where only one declaration is allowed to have these single-application attributes, the generator and the user can't effectively communicate their requirements to each other.</span></span> <span data-ttu-id="ccdab-112">例如，如果生成器使用特性生成定义声明， `NotNullWhen` 则即使后置条件适用于实现并由编译器检查，用户也不能使用相同的属性编写实现声明。</span><span class="sxs-lookup"><span data-stu-id="ccdab-112">If a generator produces a defining declaration with a `NotNullWhen` attribute, for instance, the user cannot write an implementing declaration with that same attribute, even though the postcondition is applicable to the implementation, and checked by the compiler.</span></span> <span data-ttu-id="ccdab-113">这会给用户造成混淆，同时跟踪警告的根本原因或尝试了解方法的行为。</span><span class="sxs-lookup"><span data-stu-id="ccdab-113">This creates confusion for users when tracking down the root causes of warnings or when trying to understand the behaviors of a method.</span></span>

## <a name="solution"></a><span data-ttu-id="ccdab-114">解决方案</span><span class="sxs-lookup"><span data-stu-id="ccdab-114">Solution</span></span>

<span data-ttu-id="ccdab-115">只要属性参数相同，就允许在每个分部声明中使用一次非 AllowMultiple 属性。</span><span class="sxs-lookup"><span data-stu-id="ccdab-115">Allow a non-AllowMultiple attribute to be used once in each partial declaration, as long as the attribute arguments are identical.</span></span> <span data-ttu-id="ccdab-116">由于属性参数都是常量，因此，在编译时验证这一点不是很困难。</span><span class="sxs-lookup"><span data-stu-id="ccdab-116">Since attribute arguments are all constants, this should not be very difficult to verify at compile time.</span></span> <span data-ttu-id="ccdab-117">当跨声明与属性时，每个非 AllowMultiple 属性都将被消除重复，并且将只发出一个属性实例。</span><span class="sxs-lookup"><span data-stu-id="ccdab-117">When attributes are unioned across declarations, each non-AllowMultiple attribute will be de-duplicated and only one instance of the attribute will be emitted.</span></span>

```cs
public partial bool TryGetValue([NotNullWhen(true)] out object? value);
public partial bool TryGetValue([NotNullWhen(true)] out object? value) { ... } // ok

// equivalent to:
public bool TryGetValue([NotNullWhen(true)] out object value) { ... }

// error when attribute arguments do not match
public partial bool TryGetValue([NotNullWhen(true)] out object? value);
public partial bool TryGetValue([NotNullWhen(false)] out object? value) { ... } // error
```

### <a name="open-questions"></a><span data-ttu-id="ccdab-118">打开问题</span><span class="sxs-lookup"><span data-stu-id="ccdab-118">Open questions</span></span>

1. <span data-ttu-id="ccdab-119">此类重复是否应允许在 "partial" 类型声明上或仅对非类型成员（例如方法）使用？</span><span class="sxs-lookup"><span data-stu-id="ccdab-119">Should such repetition of attributes be permitted on 'partial' type declarations or only on non-type members (e.g. methods)?</span></span>
2. <span data-ttu-id="ccdab-120">允许符号上的多个*用法允许 "* 选择启用" 以消除特性的等效用法的特性？</span><span class="sxs-lookup"><span data-stu-id="ccdab-120">Should attributes which *do* allow multiple usages on a symbol be permitted to "opt in" to de-duplication of equivalent usages of an attribute?</span></span>

### <a name="design-meetings"></a><span data-ttu-id="ccdab-121">设计会议</span><span class="sxs-lookup"><span data-stu-id="ccdab-121">Design meetings</span></span>
#### <a name="6th-july-2020"></a>[<span data-ttu-id="ccdab-122">2020年7月6日</span><span class="sxs-lookup"><span data-stu-id="ccdab-122">6th July 2020</span></span>](/meetings/2020/LDM-2020-07-06.md#repeated-attributes-on-partial-members)
<span data-ttu-id="ccdab-123">已接受该建议。</span><span class="sxs-lookup"><span data-stu-id="ccdab-123">The proposal is accepted.</span></span>
  - <span data-ttu-id="ccdab-124">允许跨分部类型声明（开放式问题1）重复执行非 AllowMultiple 属性。</span><span class="sxs-lookup"><span data-stu-id="ccdab-124">Repetition of non-AllowMultiple attributes will be permitted across partial type declarations (open question 1).</span></span>
  - <span data-ttu-id="ccdab-125">重复应用 AllowMultiple 属性的行为不会更改，并且可能会在将来的建议中考虑 "选择加入" 机制以消除重复。</span><span class="sxs-lookup"><span data-stu-id="ccdab-125">Repeated application of AllowMultiple attributes will not change in behavior, and an "opt in" mechanism for de-duplication may be considered in a future proposal (open question 2).</span></span>

---
ms.openlocfilehash: 5584f6a67ab21ed92d0b8c98dd0bd18ca7a6bc3d
ms.sourcegitcommit: 1aaa2ce9416639485fd0ccbbd32f68e53be6d3ea
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/08/2020
ms.locfileid: "86074278"
---
# <a name="extend-null-coalescing--and-null-coalescing-assignment--operators-to-pointers"></a><span data-ttu-id="5dd47-101">扩展 null 合并（"？"）和 null 合并分配（"？"=）到指针的运算符</span><span class="sxs-lookup"><span data-stu-id="5dd47-101">Extend null-coalescing (??) and null coalescing assignment (??=) operators to pointers</span></span>

* <span data-ttu-id="5dd47-102">[x] 建议</span><span class="sxs-lookup"><span data-stu-id="5dd47-102">[x] Proposed</span></span>
* <span data-ttu-id="5dd47-103">[] 原型：未启动</span><span class="sxs-lookup"><span data-stu-id="5dd47-103">[ ] Prototype: Not Started</span></span>
* <span data-ttu-id="5dd47-104">[] 实现：未启动</span><span class="sxs-lookup"><span data-stu-id="5dd47-104">[ ] Implementation: Not Started</span></span>
* <span data-ttu-id="5dd47-105">[] 规范：正在进行</span><span class="sxs-lookup"><span data-stu-id="5dd47-105">[ ] Specification: In Progress</span></span>

## <a name="summary"></a><span data-ttu-id="5dd47-106">总结</span><span class="sxs-lookup"><span data-stu-id="5dd47-106">Summary</span></span>
[summary]: #summary

<span data-ttu-id="5dd47-107">此建议扩展了对 c # 中的常用功能的支持（？？</span><span class="sxs-lookup"><span data-stu-id="5dd47-107">This proposal extends support of a commonly used feature in C# (??</span></span> <span data-ttu-id="5dd47-108">和？？=）到不安全代码和指针类型 specifcally。</span><span class="sxs-lookup"><span data-stu-id="5dd47-108">and ??=) to unsafe code and pointer types specifcally.</span></span> 

## <a name="motivation"></a><span data-ttu-id="5dd47-109">动机</span><span class="sxs-lookup"><span data-stu-id="5dd47-109">Motivation</span></span>
[motivation]: #motivation


<span data-ttu-id="5dd47-110">此功能没有理由从此功能中排除，因为它们在语义上适合该功能的用例。</span><span class="sxs-lookup"><span data-stu-id="5dd47-110">There is no reason for pointer types to be excluded from this feature as they semantically fit the feature's use case.</span></span> <span data-ttu-id="5dd47-111">支持此功能可将功能的范围扩展到预期其逻辑支持的范围。</span><span class="sxs-lookup"><span data-stu-id="5dd47-111">Supporting this extends the feature's scope to what one would expect it to logically support.</span></span> <span data-ttu-id="5dd47-112">三元表达式已支持此概念，例如：</span><span class="sxs-lookup"><span data-stu-id="5dd47-112">This concept is already supported in ternary expressions EG:</span></span>

 `T *foo = bar != null ? bar : baz;`

<span data-ttu-id="5dd47-113">因此，为非指针类型提供的相同句法选项应扩展为指针类型。</span><span class="sxs-lookup"><span data-stu-id="5dd47-113">So the same syntactic options that are offered to non-pointer types should be extended to pointer types.</span></span>

## <a name="detailed-design"></a><span data-ttu-id="5dd47-114">详细设计</span><span class="sxs-lookup"><span data-stu-id="5dd47-114">Detailed design</span></span>
[design]: #detailed-design

<span data-ttu-id="5dd47-115">对于这种语言的补充，无需进行语法更改。</span><span class="sxs-lookup"><span data-stu-id="5dd47-115">For this addition to the language, no grammar changes are required.</span></span> <span data-ttu-id="5dd47-116">我们只是向现有操作员添加受支持的类型。</span><span class="sxs-lookup"><span data-stu-id="5dd47-116">We are merely adding supported types to an existing operator.</span></span> <span data-ttu-id="5dd47-117">然后，当前转换规则将确定？？的结果类型。</span><span class="sxs-lookup"><span data-stu-id="5dd47-117">The current conversion rules will then determine the resulting type of the ??</span></span> <span data-ttu-id="5dd47-118">expression。</span><span class="sxs-lookup"><span data-stu-id="5dd47-118">expression.</span></span>
<span data-ttu-id="5dd47-119">其他规则将保持不变，但需要修改的宽松类型规则除外。</span><span class="sxs-lookup"><span data-stu-id="5dd47-119">Other rules will remain the same with the exception of the relaxed type rules which will need to be modified.</span></span>

<span data-ttu-id="5dd47-120">C # 规范将需要更新，以反映此添加的行更改。</span><span class="sxs-lookup"><span data-stu-id="5dd47-120">The  C# spec will need to be updated to reflect this addition with the change of the line.</span></span>
> <span data-ttu-id="5dd47-121">形式为 "？" 的 null 合并表达式 `a` 。</span><span class="sxs-lookup"><span data-stu-id="5dd47-121">A null coalescing expression of the form `a` ??</span></span> <span data-ttu-id="5dd47-122">`b`需要 `a` 是可以为 null 的类型或引用类型。</span><span class="sxs-lookup"><span data-stu-id="5dd47-122">`b` requires `a` to be of a nullable type or reference type.</span></span>

<span data-ttu-id="5dd47-123">to</span><span class="sxs-lookup"><span data-stu-id="5dd47-123">to</span></span>

> <span data-ttu-id="5dd47-124">形式为 "？" 的 null 合并表达式 `a` 。</span><span class="sxs-lookup"><span data-stu-id="5dd47-124">A null coalescing expression of the form `a` ??</span></span> <span data-ttu-id="5dd47-125">`b`需要 `a` 是可以为 null 的类型、引用类型或指针类型。</span><span class="sxs-lookup"><span data-stu-id="5dd47-125">`b` requires `a` to be of a nullable type, reference type or pointer type.</span></span>

<span data-ttu-id="5dd47-126">非常丰富的</span><span class="sxs-lookup"><span data-stu-id="5dd47-126">as well as</span></span> 
> <span data-ttu-id="5dd47-127">如果 `A` 存在并且不是可以为 null 的类型或引用类型，则会发生编译时错误。</span><span class="sxs-lookup"><span data-stu-id="5dd47-127">If `A` exists and is not a nullable type or a reference type, a compile-time error occurs.</span></span>

<span data-ttu-id="5dd47-128">to</span><span class="sxs-lookup"><span data-stu-id="5dd47-128">to</span></span>

> <span data-ttu-id="5dd47-129">如果 `A` 存在并且不是可以为 null 的类型、引用类型或指针类型，则会发生编译时错误。</span><span class="sxs-lookup"><span data-stu-id="5dd47-129">If `A` exists and is not a nullable type, reference type or pointer type, a compile-time error occurs.</span></span>

<span data-ttu-id="5dd47-130">目前，的规则 `??=` 不会禁止指针类型; 但是，运算符的实现方式并不是这样。</span><span class="sxs-lookup"><span data-stu-id="5dd47-130">Currently the rules for `??=` do not prohibit pointer types; however, the operator was not implemented like that.</span></span>


## <a name="drawbacks"></a><span data-ttu-id="5dd47-131">缺点</span><span class="sxs-lookup"><span data-stu-id="5dd47-131">Drawbacks</span></span>
[drawbacks]: #drawbacks

<span data-ttu-id="5dd47-132">不安全代码可视为混乱，并将不会发生的 bug 的向量视为混乱。</span><span class="sxs-lookup"><span data-stu-id="5dd47-132">Unsafe code can be considered confusing and a vector for bugs that would not happen otherwise.</span></span> <span data-ttu-id="5dd47-133">增加指针类型的可用句法选项可能导致开发人员不知道编写内容的语义含义。</span><span class="sxs-lookup"><span data-stu-id="5dd47-133">Increasing the available syntactic options for pointer types could possibly lead to bugs where the developer was unaware of the semantic meaning of what was written.</span></span> 

## <a name="alternatives"></a><span data-ttu-id="5dd47-134">备选方法</span><span class="sxs-lookup"><span data-stu-id="5dd47-134">Alternatives</span></span>
[alternatives]: #alternatives

<span data-ttu-id="5dd47-135">未考虑其他设计，因为这不是一项新功能，而是一个已实现的功能的扩展。</span><span class="sxs-lookup"><span data-stu-id="5dd47-135">No other designs have been considered as this is not a new feature, but it is rather an extension of an already implemented feature.</span></span>

## <a name="unresolved-questions"></a><span data-ttu-id="5dd47-136">未解决的问题</span><span class="sxs-lookup"><span data-stu-id="5dd47-136">Unresolved questions</span></span>
[unresolved]: #unresolved-questions

<span data-ttu-id="5dd47-137">一个可能的问题是，在支持的隐式转换中，是否应将支持扩展到隐式取消引用 `Nullable<T>` 。</span><span class="sxs-lookup"><span data-stu-id="5dd47-137">A possible question is if support should be extended to implicit dereferencing in the same vein as implicit conversions for `Nullable<T>` are supported.</span></span>

<span data-ttu-id="5dd47-138">例如</span><span class="sxs-lookup"><span data-stu-id="5dd47-138">EG:</span></span>

    int* foo = null;
    int bar = foo ?? 3;

## <a name="design-meetings"></a><span data-ttu-id="5dd47-139">设计会议</span><span class="sxs-lookup"><span data-stu-id="5dd47-139">Design meetings</span></span>

https://github.com/dotnet/csharplang/issues/418




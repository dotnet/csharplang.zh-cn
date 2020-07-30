---
ms.openlocfilehash: 0a6b3a4aa86bac8e7855ec2f4c50ac022941000e
ms.sourcegitcommit: 89bbdd101f539cefd41b2b8b9087fc9cdbcf3c62
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/29/2020
ms.locfileid: "87412442"
---
# <a name="static-anonymous-functions"></a><span data-ttu-id="10e59-101">静态匿名函数</span><span class="sxs-lookup"><span data-stu-id="10e59-101">Static anonymous functions</span></span>

## <a name="summary"></a><span data-ttu-id="10e59-102">总结</span><span class="sxs-lookup"><span data-stu-id="10e59-102">Summary</span></span>

<span data-ttu-id="10e59-103">允许对 lambda 和匿名方法使用 "static" 修饰符，这不允许从包含范围捕获局部变量或实例状态。</span><span class="sxs-lookup"><span data-stu-id="10e59-103">Allow a 'static' modifier on lambdas and anonymous methods, which disallows capture of locals or instance state from containing scopes.</span></span>

## <a name="motivation"></a><span data-ttu-id="10e59-104">动机</span><span class="sxs-lookup"><span data-stu-id="10e59-104">Motivation</span></span>

<span data-ttu-id="10e59-105">避免意外捕获来自封闭上下文的状态，这可能导致捕获对象的意外保留或意外的额外分配。</span><span class="sxs-lookup"><span data-stu-id="10e59-105">Avoid unintentionally capturing state from the enclosing context, which can result in unexpected retention of captured objects or unexpected additional allocations.</span></span>

## <a name="detailed-design"></a><span data-ttu-id="10e59-106">详细设计</span><span class="sxs-lookup"><span data-stu-id="10e59-106">Detailed design</span></span>

<span data-ttu-id="10e59-107">Lambda 或匿名方法可能具有 `static` 修饰符。</span><span class="sxs-lookup"><span data-stu-id="10e59-107">A lambda or anonymous method may have a `static` modifier.</span></span> <span data-ttu-id="10e59-108">`static`修饰符指示 lambda 或匿名方法为*静态匿名函数*。</span><span class="sxs-lookup"><span data-stu-id="10e59-108">The `static` modifier indicates that the lambda or anonymous method is a *static anonymous function*.</span></span>

<span data-ttu-id="10e59-109">*静态匿名函数*无法从封闭范围中捕获状态。</span><span class="sxs-lookup"><span data-stu-id="10e59-109">A *static anonymous function* cannot capture state from the enclosing scope.</span></span>
<span data-ttu-id="10e59-110">因此， `this` 不能在*静态匿名函数*内使用封闭范围中的局部变量、参数和。</span><span class="sxs-lookup"><span data-stu-id="10e59-110">As a result, locals, parameters, and `this` from the enclosing scope are not available within a *static anonymous function*.</span></span>

<span data-ttu-id="10e59-111">*静态匿名函数*不能通过隐式或显式或引用来引用实例成员 `this` `base` 。</span><span class="sxs-lookup"><span data-stu-id="10e59-111">A *static anonymous function* cannot reference instance members from an implicit or explicit `this` or `base` reference.</span></span>

<span data-ttu-id="10e59-112">*静态匿名函数*可以引用 `static` 封闭范围内的成员。</span><span class="sxs-lookup"><span data-stu-id="10e59-112">A *static anonymous function* may reference `static` members from the enclosing scope.</span></span>

<span data-ttu-id="10e59-113">*静态匿名函数*可以引用 `constant` 来自封闭范围的定义。</span><span class="sxs-lookup"><span data-stu-id="10e59-113">A *static anonymous function* may reference `constant` definitions from the enclosing scope.</span></span>

<span data-ttu-id="10e59-114">`nameof()`在*静态匿名函数*中，可以引用局部变量、参数或 `this` 或 `base` 从封闭范围引用。</span><span class="sxs-lookup"><span data-stu-id="10e59-114">`nameof()` in a *static anonymous function* may reference locals, parameters, or `this` or `base` from the enclosing scope.</span></span>

<span data-ttu-id="10e59-115">封闭范围中的成员的可访问性规则与 `private` `static` 非 `static` 匿名函数相同。</span><span class="sxs-lookup"><span data-stu-id="10e59-115">Accessibility rules for `private` members in the enclosing scope are the same for `static` and non-`static` anonymous functions.</span></span>

<span data-ttu-id="10e59-116">不保证*静态匿名函数*定义是否作为 `static` 元数据中的方法发出。</span><span class="sxs-lookup"><span data-stu-id="10e59-116">No guarantee is made as to whether a *static anonymous function* definition is emitted as a `static` method in metadata.</span></span> <span data-ttu-id="10e59-117">这留给了编译器实现进行优化。</span><span class="sxs-lookup"><span data-stu-id="10e59-117">This is left up to the compiler implementation to optimize.</span></span>

<span data-ttu-id="10e59-118">非 `static` 本地函数或匿名函数可以从封闭*静态匿名函数*捕获状态，但不能在封闭*静态匿名函数*之外捕获状态。</span><span class="sxs-lookup"><span data-stu-id="10e59-118">A non-`static` local function or anonymous function can capture state from an enclosing *static anonymous function* but cannot capture state outside the enclosing *static anonymous function*.</span></span>

<span data-ttu-id="10e59-119">`static`从有效程序中的匿名函数删除修饰符不会更改程序的含义。</span><span class="sxs-lookup"><span data-stu-id="10e59-119">Removing the `static` modifier from an anonymous function in a valid program does not change the meaning of the program.</span></span>

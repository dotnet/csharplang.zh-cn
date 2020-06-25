---
ms.openlocfilehash: efe927e6d3abdde5a350eadc9208949710bb4a39
ms.sourcegitcommit: b901db48deffcbe2ec7cc08ec6cb376d1f7faecb
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/24/2020
ms.locfileid: "85345207"
---
# <a name="static-lambdas"></a><span data-ttu-id="37d68-101">静态 lambda</span><span class="sxs-lookup"><span data-stu-id="37d68-101">Static lambdas</span></span>

## <a name="summary"></a><span data-ttu-id="37d68-102">摘要</span><span class="sxs-lookup"><span data-stu-id="37d68-102">Summary</span></span>

<span data-ttu-id="37d68-103">支持禁止从封闭范围捕获状态的 lambda。</span><span class="sxs-lookup"><span data-stu-id="37d68-103">Support lambdas that disallow capturing state from the enclosing scope.</span></span>

## <a name="motivation"></a><span data-ttu-id="37d68-104">动机</span><span class="sxs-lookup"><span data-stu-id="37d68-104">Motivation</span></span>

<span data-ttu-id="37d68-105">避免意外捕获来自封闭上下文的状态。</span><span class="sxs-lookup"><span data-stu-id="37d68-105">Avoid unintentionally capturing state from the enclosing context.</span></span>

## <a name="detailed-design"></a><span data-ttu-id="37d68-106">详细设计</span><span class="sxs-lookup"><span data-stu-id="37d68-106">Detailed design</span></span>

<span data-ttu-id="37d68-107">具有的 lambda `static` 无法从封闭范围中捕获状态。</span><span class="sxs-lookup"><span data-stu-id="37d68-107">A lambdas with `static` cannot capture state from the enclosing scope.</span></span>
<span data-ttu-id="37d68-108">因此， `this` 不能在 lambda 中使用封闭范围内的局部变量、参数和 `static` 。</span><span class="sxs-lookup"><span data-stu-id="37d68-108">As a result, locals, parameters, and `this` from the enclosing scope are not available within a `static` lambda.</span></span>

<span data-ttu-id="37d68-109">`static`Lambda 无法通过隐式或显式或引用来引用实例成员 `this` `base` 。</span><span class="sxs-lookup"><span data-stu-id="37d68-109">A `static` lambda cannot reference instance members from an implicit or explicit `this` or `base` reference.</span></span>

<span data-ttu-id="37d68-110">`static`Lambda 可以引用 `static` 封闭范围内的成员。</span><span class="sxs-lookup"><span data-stu-id="37d68-110">A `static` lambda may reference `static` members from the enclosing scope.</span></span>

<span data-ttu-id="37d68-111">`static`Lambda 可以引用 `constant` 来自封闭范围的定义。</span><span class="sxs-lookup"><span data-stu-id="37d68-111">A `static` lambda may reference `constant` definitions from the enclosing scope.</span></span>

<span data-ttu-id="37d68-112">`nameof()`在中， `static` lambda 可以引用局部变量、参数或 `this` 或 `base` 从封闭范围引用。</span><span class="sxs-lookup"><span data-stu-id="37d68-112">`nameof()` in a `static` lambda may reference locals, parameters, or `this` or `base` from the enclosing scope.</span></span>

<span data-ttu-id="37d68-113">封闭范围中的成员的可访问性规则 `private` 对于 `static` 和非 lambda 是相同的 `static` 。</span><span class="sxs-lookup"><span data-stu-id="37d68-113">Accessibility rules for `private` members in the enclosing scope are the same for `static` and non-`static` lambdas.</span></span>

<span data-ttu-id="37d68-114">不保证 `static` lambda 定义是否作为 `static` 元数据中的方法发出。</span><span class="sxs-lookup"><span data-stu-id="37d68-114">No guarantee is made as to whether a `static` lambda definition is emitted as a `static` method in metadata.</span></span> <span data-ttu-id="37d68-115">这留给了编译器实现进行优化。</span><span class="sxs-lookup"><span data-stu-id="37d68-115">This is left up to the compiler implementation to optimize.</span></span>

<span data-ttu-id="37d68-116">非 `static` 本地函数或 lambda 可以从封闭的 lambda 捕获状态 `static` ，但不能在封闭的 lambda 之外捕获状态 `static` 。</span><span class="sxs-lookup"><span data-stu-id="37d68-116">A non-`static` local function or lambda can capture state from an enclosing `static` lambda but cannot capture state outside the enclosing `static` lambda.</span></span>

<span data-ttu-id="37d68-117">`static`从有效程序的 lambda 中删除修饰符不会更改程序的含义。</span><span class="sxs-lookup"><span data-stu-id="37d68-117">Removing the `static` modifier from a lambda in a valid program does not change the meaning of the program.</span></span>

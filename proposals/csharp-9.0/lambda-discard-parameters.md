---
ms.openlocfilehash: bdefce3af78e44136dc2682e9e262cc8d6fae77b
ms.sourcegitcommit: a88d56e3131d7a94c65e637c276379541a3cd491
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/30/2020
ms.locfileid: "87434486"
---
# <a name="lambda-discard-parameters"></a><span data-ttu-id="18000-101">Lambda 丢弃参数</span><span class="sxs-lookup"><span data-stu-id="18000-101">Lambda discard parameters</span></span>

## <a name="summary"></a><span data-ttu-id="18000-102">总结</span><span class="sxs-lookup"><span data-stu-id="18000-102">Summary</span></span>

<span data-ttu-id="18000-103">允许使用丢弃（ `_` ）作为 lambda 和匿名方法的参数。</span><span class="sxs-lookup"><span data-stu-id="18000-103">Allow discards (`_`) to be used as parameters of lambdas and anonymous methods.</span></span>
<span data-ttu-id="18000-104">例如：</span><span class="sxs-lookup"><span data-stu-id="18000-104">For example:</span></span>
- <span data-ttu-id="18000-105">lambda： `(_, _) => 0` ，`(int _, int _) => 0`</span><span class="sxs-lookup"><span data-stu-id="18000-105">lambdas: `(_, _) => 0`, `(int _, int _) => 0`</span></span>
- <span data-ttu-id="18000-106">匿名方法：`delegate(int _, int _) { return 0; }`</span><span class="sxs-lookup"><span data-stu-id="18000-106">anonymous methods: `delegate(int _, int _) { return 0; }`</span></span>

## <a name="motivation"></a><span data-ttu-id="18000-107">动机</span><span class="sxs-lookup"><span data-stu-id="18000-107">Motivation</span></span>

<span data-ttu-id="18000-108">不需要将未使用的参数命名为。</span><span class="sxs-lookup"><span data-stu-id="18000-108">Unused parameters do not need to be named.</span></span> <span data-ttu-id="18000-109">放弃的意图是清晰的，也就是说，它们未被使用/丢弃。</span><span class="sxs-lookup"><span data-stu-id="18000-109">The intent of discards is clear, i.e. they are unused/discarded.</span></span>

## <a name="detailed-design"></a><span data-ttu-id="18000-110">详细设计</span><span class="sxs-lookup"><span data-stu-id="18000-110">Detailed design</span></span>

<span data-ttu-id="18000-111">[方法参数](https://github.com/dotnet/csharplang/blob/master/spec/classes.md#method-parameters)在使用多个名为的参数的 lambda 或匿名方法的参数列表中 `_` ，此类参数是丢弃参数。</span><span class="sxs-lookup"><span data-stu-id="18000-111">[Method parameters](https://github.com/dotnet/csharplang/blob/master/spec/classes.md#method-parameters) In the parameter list of a lambda or anonymous method with more than one parameter named `_`, such parameters are discard parameters.</span></span>
<span data-ttu-id="18000-112">注意：如果将单个参数命名为，则 `_` 它是一个用于向后兼容的常规参数。</span><span class="sxs-lookup"><span data-stu-id="18000-112">Note: if a single parameter is named `_` then it is a regular parameter for backwards compatibility reasons.</span></span>

<span data-ttu-id="18000-113">丢弃参数不会将任何名称引入任何范围。</span><span class="sxs-lookup"><span data-stu-id="18000-113">Discard parameters do not introduce any names to any scopes.</span></span>
<span data-ttu-id="18000-114">请注意，这意味着它们不会导致 `_` 隐藏任何（下划线）名称。</span><span class="sxs-lookup"><span data-stu-id="18000-114">Note this implies they do not cause any `_` (underscore) names to be hidden.</span></span>

<span data-ttu-id="18000-115">[简单名称](../../spec/expressions.md#simple-names)如果 `K` 为零，并且*simple_name*出现在*块*中，并且*块*的（或封闭*块*的）局部变量声明空间（[声明](../../spec/basic-concepts.md#declarations)）包含局部变量、参数（丢弃参数除外）或具有名称的常量 `I` ，则*simple_name*引用该局部变量、参数或常量，并归类为变量或值。</span><span class="sxs-lookup"><span data-stu-id="18000-115">[Simple names](../../spec/expressions.md#simple-names) If `K` is zero and the *simple_name* appears within a *block* and if the *block*'s (or an enclosing *block*'s) local variable declaration space ([Declarations](../../spec/basic-concepts.md#declarations)) contains a local variable, parameter (with the exception of discard parameters) or constant with name `I`, then the *simple_name* refers to that local variable, parameter or constant and is classified as a variable or value.</span></span>

<span data-ttu-id="18000-116">[作用域](../../spec/basic-concepts.md#scopes)除了丢弃参数外，在*lambda_expression* （[匿名函数表达式](../../spec/expressions.md#anonymous-function-expressions)）中声明的参数的作用域是该*lambda_expression*的*anonymous_function_body* ，但在*anonymous_method_expression*中声明的参数的作用域（[匿名函数表达式](../../spec/expressions.md#anonymous-function-expressions)）是该*anonymous_method_expression*的*块*。</span><span class="sxs-lookup"><span data-stu-id="18000-116">[Scopes](../../spec/basic-concepts.md#scopes) With the exception of discard parameters, the scope of a parameter declared in a *lambda_expression* ([Anonymous function expressions](../../spec/expressions.md#anonymous-function-expressions)) is the *anonymous_function_body* of that *lambda_expression* With the exception of discard parameters, the scope of a parameter declared in an *anonymous_method_expression* ([Anonymous function expressions](../../spec/expressions.md#anonymous-function-expressions)) is the *block* of that *anonymous_method_expression*.</span></span>

## <a name="related-spec-sections"></a><span data-ttu-id="18000-117">相关 spec 部分</span><span class="sxs-lookup"><span data-stu-id="18000-117">Related spec sections</span></span>
- [<span data-ttu-id="18000-118">对应的参数</span><span class="sxs-lookup"><span data-stu-id="18000-118">Corresponding parameters</span></span>](../../spec/expressions.md#corresponding-parameters)

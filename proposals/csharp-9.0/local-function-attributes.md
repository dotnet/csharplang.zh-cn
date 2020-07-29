---
ms.openlocfilehash: 0c8bc2b5072ea7f86189b41a1cdbf2a449661b05
ms.sourcegitcommit: 0c25406d8a99064bb85d934bb32ffcf547753acc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/28/2020
ms.locfileid: "87297249"
---
# <a name="attributes-on-local-functions"></a><span data-ttu-id="210ea-101">本地函数的属性</span><span class="sxs-lookup"><span data-stu-id="210ea-101">Attributes on local functions</span></span>

## <a name="attributes"></a><span data-ttu-id="210ea-102">属性</span><span class="sxs-lookup"><span data-stu-id="210ea-102">Attributes</span></span>

<span data-ttu-id="210ea-103">本地函数声明现在允许有[属性](../spec/attributes.md)。</span><span class="sxs-lookup"><span data-stu-id="210ea-103">Local function declarations are now permitted to have [attributes](../spec/attributes.md).</span></span> <span data-ttu-id="210ea-104">本地函数上的参数和类型参数也允许有属性。</span><span class="sxs-lookup"><span data-stu-id="210ea-104">Parameters and type parameters on local functions are also allowed to have attributes.</span></span>

<span data-ttu-id="210ea-105">应用于方法、其参数或其类型参数的属性在应用于本地函数、其参数或其类型参数时具有相同的含义。</span><span class="sxs-lookup"><span data-stu-id="210ea-105">Attributes with a specified meaning when applied to a method, its parameters, or its type parameters will have the same meaning when applied to a local function, its parameters, or its type parameters, respectively.</span></span>

<span data-ttu-id="210ea-106">局部函数可以通过使用将其与[条件方法](../spec/attributes.md#the-conditional-attribute)进行修饰来成为条件方法 `[ConditionalAttribute]` 。</span><span class="sxs-lookup"><span data-stu-id="210ea-106">A local function can be made conditional in the same sense as a [conditional method](../spec/attributes.md#the-conditional-attribute) by decorating it with a `[ConditionalAttribute]`.</span></span> <span data-ttu-id="210ea-107">条件本地函数也必须是 `static` 。</span><span class="sxs-lookup"><span data-stu-id="210ea-107">A conditional local function must also be `static`.</span></span> <span data-ttu-id="210ea-108">条件方法的所有限制也适用于条件本地函数，包括返回类型必须为 `void` 。</span><span class="sxs-lookup"><span data-stu-id="210ea-108">All restrictions on conditional methods also apply to conditional local functions, including that the return type must be `void`.</span></span>

## <a name="extern"></a><span data-ttu-id="210ea-109">部</span><span class="sxs-lookup"><span data-stu-id="210ea-109">Extern</span></span>

<span data-ttu-id="210ea-110">`extern`现在允许对本地函数使用修饰符。</span><span class="sxs-lookup"><span data-stu-id="210ea-110">The `extern` modifier is now permitted on local functions.</span></span> <span data-ttu-id="210ea-111">这使本地函数成为外部[方法](../spec/classes.md#external-methods)的外部函数。</span><span class="sxs-lookup"><span data-stu-id="210ea-111">This makes the local function external in the same sense as an [external method](../spec/classes.md#external-methods).</span></span>

<span data-ttu-id="210ea-112">与外部方法类似，外部本地函数的*本地函数体*必须是分号。</span><span class="sxs-lookup"><span data-stu-id="210ea-112">Similarly to an external method, the *local-function-body* of an external local function must be a semicolon.</span></span> <span data-ttu-id="210ea-113">只允许对外部本地函数使用分号*局部函数体*。</span><span class="sxs-lookup"><span data-stu-id="210ea-113">A semicolon *local-function-body* is only permitted on an external local function.</span></span> 

<span data-ttu-id="210ea-114">外部本地函数也必须是 `static` 。</span><span class="sxs-lookup"><span data-stu-id="210ea-114">An external local function must also be `static`.</span></span>

## <a name="syntax"></a><span data-ttu-id="210ea-115">语法</span><span class="sxs-lookup"><span data-stu-id="210ea-115">Syntax</span></span>

<span data-ttu-id="210ea-116">按如下所示修改[本地函数语法](csharp-7.0/local-functions.md#syntax-grammar)：</span><span class="sxs-lookup"><span data-stu-id="210ea-116">The [local functions grammar](csharp-7.0/local-functions.md#syntax-grammar) is modified as follows:</span></span>
```
local-function-header
    : attributes? local-function-modifiers? return-type identifier type-parameter-list?
        ( formal-parameter-list? ) type-parameter-constraints-clauses
    ;

local-function-modifiers
    : (async | unsafe | static | extern)*
    ;

local-function-body
    : block
    | arrow-expression-body
    | ';'
    ;
```

---
ms.openlocfilehash: 165de098ddbd753416e01c4ff8e44750d9b7f66c
ms.sourcegitcommit: a0b59a6768813152b4b5b6df3637041a64aba2ee
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/19/2020
ms.locfileid: "92185110"
---
# <a name="name-shadowing-in-nested-functions"></a><span data-ttu-id="f9647-101">嵌套函数中的名称隐藏</span><span class="sxs-lookup"><span data-stu-id="f9647-101">Name shadowing in nested functions</span></span>

## <a name="summary"></a><span data-ttu-id="f9647-102">总结</span><span class="sxs-lookup"><span data-stu-id="f9647-102">Summary</span></span>

<span data-ttu-id="f9647-103">允许 lambda 和局部函数中的变量名称重复使用 (，并隐藏封闭方法或函数中的) 名称。</span><span class="sxs-lookup"><span data-stu-id="f9647-103">Permit variable names in lambdas and local functions to reuse (and shadow) names from the enclosing method or function.</span></span>

## <a name="detailed-design"></a><span data-ttu-id="f9647-104">详细设计</span><span class="sxs-lookup"><span data-stu-id="f9647-104">Detailed design</span></span>

<span data-ttu-id="f9647-105">使用时 `-langversion:8` ，lambda 或局部函数内的局部变量、本地函数、参数、类型参数和范围变量的名称可以重复使用局部变量、本地函数、参数、类型参数和范围变量的名称。</span><span class="sxs-lookup"><span data-stu-id="f9647-105">With `-langversion:8`, names of locals, local functions, parameters, type parameters, and range variables within a lambda or local function can reuse names of locals, local functions, parameters, type parameters, and range variables from an enclosing method or function.</span></span> <span data-ttu-id="f9647-106">嵌套函数中的名称将隐藏嵌套函数内的封闭函数中具有相同名称的符号。</span><span class="sxs-lookup"><span data-stu-id="f9647-106">The name in the nested function hides the symbol of the same name from the enclosing function within the nested function.</span></span>

<span data-ttu-id="f9647-107">`static`和非 `static` 本地函数和 lambda 支持隐藏。</span><span class="sxs-lookup"><span data-stu-id="f9647-107">Shadowing is supported for `static` and non-`static` local functions and lambdas.</span></span>

<span data-ttu-id="f9647-108">使用 `-langversion:7.3` 或更早版本的行为没有变化：隐藏封闭方法或函数中的名称的嵌套函数中的名称在这些情况下将报告为错误。</span><span class="sxs-lookup"><span data-stu-id="f9647-108">There is no change in behavior using `-langversion:7.3` or earlier: names in nested functions that shadow names from the enclosing method or function are reported as errors in those cases.</span></span>

<span data-ttu-id="f9647-109">仍支持以前允许的任何映射 `-langversion:8` 。</span><span class="sxs-lookup"><span data-stu-id="f9647-109">Any shadowing previously permitted is still supported with `-langversion:8`.</span></span> <span data-ttu-id="f9647-110">例如：变量名称可能会隐藏类型和成员名称;和变量名可以隐藏封闭方法或局部函数名称。</span><span class="sxs-lookup"><span data-stu-id="f9647-110">For instance: variable names may shadow type and member names; and variable names may shadow enclosing method or local function names.</span></span>

<span data-ttu-id="f9647-111">隐藏在同一 lambda 或局部函数内的封闭范围中声明的名称仍会被报告为错误。</span><span class="sxs-lookup"><span data-stu-id="f9647-111">Shadowing a name declared in an enclosing scope in the same lambda or local function is still reported as an error.</span></span>

<span data-ttu-id="f9647-112">对于在封闭类型、方法或函数中隐藏类型参数的本地函数中的类型参数，将报告警告。</span><span class="sxs-lookup"><span data-stu-id="f9647-112">A warning is reported for a type parameter in a local function that shadows a type parameter in the enclosing type, method, or function.</span></span>

## <a name="design-meetings"></a><span data-ttu-id="f9647-113">设计会议</span><span class="sxs-lookup"><span data-stu-id="f9647-113">Design meetings</span></span>

- https://github.com/dotnet/csharplang/blob/master/meetings/2018/LDM-2018-09-10.md#static-local-functions
- https://github.com/dotnet/csharplang/blob/master/meetings/2019/LDM-2019-01-16.md#shadowing-in-nested-functions

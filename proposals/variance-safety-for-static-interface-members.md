---
ms.openlocfilehash: f1ab0e063cc8b7a44c70ea1e52027cda30e064b0
ms.sourcegitcommit: efac0d1e909d222dce5b18b10ae0d0b1d309b757
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/15/2020
ms.locfileid: "97502844"
---
# <a name="variance-safety-for-static-interface-members"></a><span data-ttu-id="2323d-101">静态接口成员的差异安全</span><span class="sxs-lookup"><span data-stu-id="2323d-101">Variance Safety for static interface members</span></span>

## <a name="summary"></a><span data-ttu-id="2323d-102">摘要</span><span class="sxs-lookup"><span data-stu-id="2323d-102">Summary</span></span>

<span data-ttu-id="2323d-103">允许接口中的静态非虚拟成员将其声明中的类型参数视为固定参数，而不考虑它们的声明方差。</span><span class="sxs-lookup"><span data-stu-id="2323d-103">Allow static, non-virtual members in interfaces to treat type parameters in their declarations as invariant, regardless of their declared variance.</span></span>

## <a name="motivation"></a><span data-ttu-id="2323d-104">动机</span><span class="sxs-lookup"><span data-stu-id="2323d-104">Motivation</span></span>


- https://github.com/dotnet/csharplang/issues/3275
- https://github.com/dotnet/csharplang/blob/master/meetings/2020/LDM-2020-06-24.md#interface-static-member-variance

<span data-ttu-id="2323d-105">在接口成员中考虑了变体 `static` 。</span><span class="sxs-lookup"><span data-stu-id="2323d-105">We considered variance in `static` interface members.</span></span> <span data-ttu-id="2323d-106">目前，对于在这些成员中使用的共同/逆变类型参数，它们必须遵循完整标准方差规则，从而导致某些不一致，与 `static` 字段的处理方式与 `static` 属性或方法不一致：</span><span class="sxs-lookup"><span data-stu-id="2323d-106">Today, for co/contravariant type parameters used in these members, they must follow the full standard rules of variance, leading to some inconsistency with the way that `static` fields are treated vs `static` properties or methods:</span></span>

```cs
public interface I<out T>
{
    static Task<T> F = Task.FromResult(default(T)); // No problem
    static Task<T> P => Task.FromResult(default(T));   //CS1961
    static Task<T> M() => Task.FromResult(default(T));    //CS1961
    static event EventHandler<T> E; // CS1961
}
```

<span data-ttu-id="2323d-107">由于这些成员是 `static` 和非虚拟的，因此不存在任何安全问题：不能通过子类型化接口和重写成员，以某种方式派生更松散/更多受限成员。</span><span class="sxs-lookup"><span data-stu-id="2323d-107">Because these members are `static` and non-virtual, there aren't any safety issues here: you can't derive a looser/more restricted member in some fashion by subtyping the interface and overriding the member.</span></span>

## <a name="detailed-design"></a><span data-ttu-id="2323d-108">详细设计</span><span class="sxs-lookup"><span data-stu-id="2323d-108">Detailed Design</span></span>

<span data-ttu-id="2323d-109">下面是语言规范 (的 Vaiance 安全部分的建议内容 https://github.com/dotnet/csharplang/blob/master/spec/interfaces.md#variance-safety) 。</span><span class="sxs-lookup"><span data-stu-id="2323d-109">Here is the proposed content for Vaiance Safety section of the language specification (https://github.com/dotnet/csharplang/blob/master/spec/interfaces.md#variance-safety).</span></span>
<span data-ttu-id="2323d-110">此更改是添加 "*这些限制不适用于静态成员声明中的 ocurrances 类型"。*</span><span class="sxs-lookup"><span data-stu-id="2323d-110">The change is the addition of "*These restrictions do not apply to ocurrances of types within declarations of static members.*"</span></span> <span data-ttu-id="2323d-111">部分开头的语句。</span><span class="sxs-lookup"><span data-stu-id="2323d-111">sentence at the beginning of the section.</span></span> 

### <a name="variance-safety"></a><span data-ttu-id="2323d-112">差异安全</span><span class="sxs-lookup"><span data-stu-id="2323d-112">Variance safety</span></span>

<span data-ttu-id="2323d-113">类型的类型参数列表中的差异批注的出现次数限制了类型在类型声明内可能出现的位置。</span><span class="sxs-lookup"><span data-stu-id="2323d-113">The occurrence of variance annotations in the type parameter list of a type restricts the places where types can occur within the type declaration.</span></span>
<span data-ttu-id="2323d-114">*这些限制不适用于静态成员声明中的 ocurrances 类型。*</span><span class="sxs-lookup"><span data-stu-id="2323d-114">*These restrictions do not apply to ocurrances of types within declarations of static members.*</span></span>

<span data-ttu-id="2323d-115">`T`如果以下其中一项为，则类型为 \***output-unsafe** _：</span><span class="sxs-lookup"><span data-stu-id="2323d-115">A type `T` is \***output-unsafe** _ if one of the following holds:</span></span>

<span data-ttu-id="2323d-116">_  `T` 是逆变类型参数</span><span class="sxs-lookup"><span data-stu-id="2323d-116">_  `T` is a contravariant type parameter</span></span>
*  <span data-ttu-id="2323d-117">`T` 是一个带有输出不安全元素类型的数组类型</span><span class="sxs-lookup"><span data-stu-id="2323d-117">`T` is an array type with an output-unsafe element type</span></span>
*  <span data-ttu-id="2323d-118">`T` 是从泛型类型构造的接口或委托类型， `S<A1,...,Ak>` `S<X1,...,Xk>` 其中至少 `Ai` 包含以下项之一：</span><span class="sxs-lookup"><span data-stu-id="2323d-118">`T` is an interface or delegate type `S<A1,...,Ak>` constructed from a generic type `S<X1,...,Xk>` where for at least one `Ai` one of the following holds:</span></span>
   * <span data-ttu-id="2323d-119">`Xi` 是协变或固定，并且 `Ai` 是输出不安全的。</span><span class="sxs-lookup"><span data-stu-id="2323d-119">`Xi` is covariant or invariant and `Ai` is output-unsafe.</span></span>
   * <span data-ttu-id="2323d-120">`Xi` 为逆变或固定，并且 `Ai` 是输入安全的。</span><span class="sxs-lookup"><span data-stu-id="2323d-120">`Xi` is contravariant or invariant and `Ai` is input-safe.</span></span>
   
<span data-ttu-id="2323d-121">`T`如果包含以下内容之一，则类型为 \***输入-不安全** _ _：</span><span class="sxs-lookup"><span data-stu-id="2323d-121">A type `T` is \***input-unsafe** _ if one of the following holds:</span></span>

<span data-ttu-id="2323d-122">_  `T` 是协变类型参数</span><span class="sxs-lookup"><span data-stu-id="2323d-122">_  `T` is a covariant type parameter</span></span>
*  <span data-ttu-id="2323d-123">`T` 是具有输入不安全元素类型的数组类型</span><span class="sxs-lookup"><span data-stu-id="2323d-123">`T` is an array type with an input-unsafe element type</span></span>
*  <span data-ttu-id="2323d-124">`T` 是从泛型类型构造的接口或委托类型， `S<A1,...,Ak>` `S<X1,...,Xk>` 其中至少 `Ai` 包含以下项之一：</span><span class="sxs-lookup"><span data-stu-id="2323d-124">`T` is an interface or delegate type `S<A1,...,Ak>` constructed from a generic type `S<X1,...,Xk>` where for at least one `Ai` one of the following holds:</span></span>
   * <span data-ttu-id="2323d-125">`Xi` 是协变或固定的，并且 `Ai` 是输入不安全的。</span><span class="sxs-lookup"><span data-stu-id="2323d-125">`Xi` is covariant or invariant and `Ai` is input-unsafe.</span></span>
   * <span data-ttu-id="2323d-126">`Xi` 为逆变或固定，并且 `Ai` 是输出不安全的。</span><span class="sxs-lookup"><span data-stu-id="2323d-126">`Xi` is contravariant or invariant and `Ai` is output-unsafe.</span></span>

<span data-ttu-id="2323d-127">在直观的输出位置中禁止使用不安全的输出类型，在输入位置禁止输入不安全类型。</span><span class="sxs-lookup"><span data-stu-id="2323d-127">Intuitively, an output-unsafe type is prohibited in an output position, and an input-unsafe type is prohibited in an input position.</span></span>

<span data-ttu-id="2323d-128">如果类型不是输出安全类型，则为 \***输出安全** 的; 如果不是输入安全的，则为 _ \*_输入安全_\*\*。</span><span class="sxs-lookup"><span data-stu-id="2323d-128">A type is ***output-safe** _ if it is not output-unsafe, and _ *_input-safe_** if it is not input-unsafe.</span></span>


## <a name="other-considerations"></a><span data-ttu-id="2323d-129">其他注意事项</span><span class="sxs-lookup"><span data-stu-id="2323d-129">Other Considerations</span></span>

<span data-ttu-id="2323d-130">我们还考虑到这是否可能会干扰我们希望对角色、类型类和扩展进行的其他一些改进。</span><span class="sxs-lookup"><span data-stu-id="2323d-130">We also considered whether this could potentially interfere with some of the other enhancements we hope to make regarding roles, type classes, and extensions.</span></span> <span data-ttu-id="2323d-131">这一切都应该是正确的：我们将无法 retcon 现有静态成员默认为接口的默认值，因为这会最终成为多个级别的重大更改，即使没有更改此处的变化行为也是如此。</span><span class="sxs-lookup"><span data-stu-id="2323d-131">These should all be fine: we won't be able to retcon the existing static members to be virtual-by-default for interfaces, as that would end up being a breaking change on multiple levels, even without changing the variance behavior here.</span></span>

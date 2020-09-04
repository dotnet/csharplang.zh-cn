---
ms.openlocfilehash: 7ff986e42e34c1de8d21a2b99e1c9ecfd9f2f9a9
ms.sourcegitcommit: ac14d87c16f28535299060d510fb4f647029a765
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/04/2020
ms.locfileid: "89446832"
---
## <a name="summary"></a><span data-ttu-id="61c5c-101">总结</span><span class="sxs-lookup"><span data-stu-id="61c5c-101">Summary</span></span>
[summary]: #summary

<span data-ttu-id="61c5c-102">支持 _协变返回类型_。</span><span class="sxs-lookup"><span data-stu-id="61c5c-102">Support _covariant return types_.</span></span> <span data-ttu-id="61c5c-103">具体而言，允许重写方法，以声明派生程度比它重写的方法更派生的返回类型，同样，也可以允许重写只读属性以声明派生程度更高的类型。</span><span class="sxs-lookup"><span data-stu-id="61c5c-103">Specifically, permit the override of a method to declare a more derived return type than the method it overrides, and similarly to permit the override of a read-only property to declare a more derived type.</span></span> <span data-ttu-id="61c5c-104">在更多派生的类型中出现的重写声明需要至少提供返回类型，就像在其基类型的重写中出现一样。</span><span class="sxs-lookup"><span data-stu-id="61c5c-104">Override declarations appearing in more derived types would be required to provide a return type at least as specific as that appearing in overrides in its base types.</span></span> <span data-ttu-id="61c5c-105">方法或属性的调用方将从调用中静态接收更精确的返回类型。</span><span class="sxs-lookup"><span data-stu-id="61c5c-105">Callers of the method or property would statically receive the more refined return type from an invocation.</span></span>

## <a name="motivation"></a><span data-ttu-id="61c5c-106">动机</span><span class="sxs-lookup"><span data-stu-id="61c5c-106">Motivation</span></span>
[motivation]: #motivation

<span data-ttu-id="61c5c-107">代码中的一种常见模式是，必须使用不同的方法名称来解决语言约束，而重写必须返回与重写的方法相同的类型。</span><span class="sxs-lookup"><span data-stu-id="61c5c-107">It is a common pattern in code that different method names have to be invented to work around the language constraint that overrides must return the same type as the overridden method.</span></span>

<span data-ttu-id="61c5c-108">这在工厂模式下非常有用。</span><span class="sxs-lookup"><span data-stu-id="61c5c-108">This would be useful in the factory pattern.</span></span> <span data-ttu-id="61c5c-109">例如，在 Roslyn 代码库中，我们将</span><span class="sxs-lookup"><span data-stu-id="61c5c-109">For example, in the Roslyn code base we would have</span></span>

``` cs
class Compilation ...
{
    public virtual Compilation WithOptions(Options options)...
}
```

``` cs
class CSharpCompilation : Compilation
{
    public override CSharpCompilation WithOptions(Options options)...
}
```

## <a name="detailed-design"></a><span data-ttu-id="61c5c-110">详细设计</span><span class="sxs-lookup"><span data-stu-id="61c5c-110">Detailed design</span></span>
[design]: #detailed-design

<span data-ttu-id="61c5c-111">这是 c # 中 [协变返回类型](https://github.com/dotnet/csharplang/issues/49) 的规范。</span><span class="sxs-lookup"><span data-stu-id="61c5c-111">This is a specification for [covariant return types](https://github.com/dotnet/csharplang/issues/49) in C#.</span></span>  <span data-ttu-id="61c5c-112">我们的目的是允许重写方法以返回比它重写的方法更派生的返回类型，同样，允许重写只读属性以返回派生程度更高的返回类型。</span><span class="sxs-lookup"><span data-stu-id="61c5c-112">Our intent is to permit the override of a method to return a more derived return type than the method it overrides, and similarly to permit the override of a read-only property to return a more derived return type.</span></span>  <span data-ttu-id="61c5c-113">方法或属性的调用方将从调用中静态接收更精确的返回类型，并且在更多派生的类型中出现的重写将要求至少提供一个返回类型，使其在其基类型的重写中出现。</span><span class="sxs-lookup"><span data-stu-id="61c5c-113">Callers of the method or property would statically receive the more refined return type from an invocation, and overrides appearing in more derived types would be required to provide a return type at least as specific as that appearing in overrides in its base types.</span></span>

--------------

### <a name="class-method-override"></a><span data-ttu-id="61c5c-114">类方法重写</span><span class="sxs-lookup"><span data-stu-id="61c5c-114">Class Method Override</span></span>

<span data-ttu-id="61c5c-115">[类重写方法上的现有约束](../../spec/classes.md#override-methods)</span><span class="sxs-lookup"><span data-stu-id="61c5c-115">The [existing constraint on class override](../../spec/classes.md#override-methods) methods</span></span>

> - <span data-ttu-id="61c5c-116">重写方法和重写的基方法具有相同的返回类型。</span><span class="sxs-lookup"><span data-stu-id="61c5c-116">The override method and the overridden base method have the same return type.</span></span>

<span data-ttu-id="61c5c-117">已修改为</span><span class="sxs-lookup"><span data-stu-id="61c5c-117">is modified to</span></span>

> - <span data-ttu-id="61c5c-118">如果方法具有值 return，则重写方法必须具有可通过标识转换转换的返回类型 (; 如果该方法具有值返回，则为该方法的返回类型 [，) 隐](https://github.com/dotnet/csharplang/blob/master/proposals/csharp-7.0/ref-locals-returns.md) 式引用转换为重写基方法的返回类型。</span><span class="sxs-lookup"><span data-stu-id="61c5c-118">The override method must have have a return type that is convertible by an identity conversion or (if the method has a value return - not a [ref return](https://github.com/dotnet/csharplang/blob/master/proposals/csharp-7.0/ref-locals-returns.md)) implicit reference conversion to the return type of the overridden base method.</span></span>

<span data-ttu-id="61c5c-119">以下附加要求将追加到该列表：</span><span class="sxs-lookup"><span data-stu-id="61c5c-119">And the following additional requirements are appended to that list:</span></span>

> - <span data-ttu-id="61c5c-120">重写方法必须有一个通过标识转换转换的返回类型，如果该方法具有值返回，则返回类型为 (; 如果该方法具有 [值) 返回，则返回](https://github.com/dotnet/csharplang/blob/master/proposals/csharp-7.0/ref-locals-returns.md) 类型为对重写方法的 (直接或间接) 基类型中声明的重写基方法的返回类型的隐式引用转换。</span><span class="sxs-lookup"><span data-stu-id="61c5c-120">The override method must have have a return type that is convertible by an identity conversion or (if the method has a value return - not a [ref return](https://github.com/dotnet/csharplang/blob/master/proposals/csharp-7.0/ref-locals-returns.md)) implicit reference conversion to the return type of every override of the overridden base method that is declared in a (direct or indirect) base type of the override method.</span></span>
> - <span data-ttu-id="61c5c-121">重写方法的返回类型必须至少具有与可访问 [性域](../../spec/basic-concepts.md#accessibility-domains))  (重写方法相同的可访问性。</span><span class="sxs-lookup"><span data-stu-id="61c5c-121">The override method's return type must be at least as accessible as the override method  ([Accessibility domains](../../spec/basic-concepts.md#accessibility-domains)).</span></span>

<span data-ttu-id="61c5c-122">此约束允许类中的重写方法 `private` 具有 `private` 返回类型。</span><span class="sxs-lookup"><span data-stu-id="61c5c-122">This constraint permits an override method in a `private` class to have a `private` return type.</span></span>  <span data-ttu-id="61c5c-123">但是，它要求 `public` 类型中的重写方法 `public` 具有 `public` 返回类型。</span><span class="sxs-lookup"><span data-stu-id="61c5c-123">However it requires a `public` override method in a `public` type to have a `public` return type.</span></span>

### <a name="class-property-and-indexer-override"></a><span data-ttu-id="61c5c-124">类属性和索引器替代</span><span class="sxs-lookup"><span data-stu-id="61c5c-124">Class Property and Indexer Override</span></span>

<span data-ttu-id="61c5c-125">[类重写属性的现有约束](../../spec/classes.md#virtual-sealed-override-and-abstract-property-accessors)</span><span class="sxs-lookup"><span data-stu-id="61c5c-125">The [existing constraint on class override](../../spec/classes.md#virtual-sealed-override-and-abstract-property-accessors) properties</span></span>

> <span data-ttu-id="61c5c-126">重写属性声明应将完全相同的可访问性修饰符和名称指定为继承的属性，并且应在 ~~重写的类型和继承的属性之间~~进行标识转换。</span><span class="sxs-lookup"><span data-stu-id="61c5c-126">An overriding property declaration shall specify the exact same accessibility modifiers and name as the inherited property, and there shall be an identity conversion ~~between the type of the overriding and the inherited property~~.</span></span> <span data-ttu-id="61c5c-127">如果继承的属性只有一个访问器 (也就是说，如果继承的属性是只读的或只写的) ，则重写属性应仅包括该访问器。</span><span class="sxs-lookup"><span data-stu-id="61c5c-127">If the inherited property has only a single accessor (i.e., if the inherited property is read-only or write-only), the overriding property shall include only that accessor.</span></span> <span data-ttu-id="61c5c-128">如果继承的属性包含两个访问器 (也就是说，如果继承的属性是读写) ，则重写属性可以包含单个访问器或两个访问器。</span><span class="sxs-lookup"><span data-stu-id="61c5c-128">If the inherited property includes both accessors (i.e., if the inherited property is read-write), the overriding property can include either a single accessor or both accessors.</span></span>

<span data-ttu-id="61c5c-129">已修改为</span><span class="sxs-lookup"><span data-stu-id="61c5c-129">is modified to</span></span>

> <span data-ttu-id="61c5c-130">重写属性声明应将完全相同的可访问性修饰符和名称指定为继承的属性，并且 **如果继承的属性是只读的，并且具有值类型（而不是 [ref return](https://github.com/dotnet/csharplang/blob/master/proposals/csharp-7.0/ref-locals-returns.md) ）) 类型) 隐式引用转换（从重写属性的类型到继承属性**的类型），则应使用标识转换或 (。</span><span class="sxs-lookup"><span data-stu-id="61c5c-130">An overriding property declaration shall specify the exact same accessibility modifiers and name as the inherited property, and there shall be an identity conversion **or (if the inherited property is read-only and has a value type - not a [ref return](https://github.com/dotnet/csharplang/blob/master/proposals/csharp-7.0/ref-locals-returns.md)) type) implicit reference conversion from the type of the overriding property to the type of the inherited property**.</span></span> <span data-ttu-id="61c5c-131">如果继承的属性只有一个访问器 (也就是说，如果继承的属性是只读的或只写的) ，则重写属性应仅包括该访问器。</span><span class="sxs-lookup"><span data-stu-id="61c5c-131">If the inherited property has only a single accessor (i.e., if the inherited property is read-only or write-only), the overriding property shall include only that accessor.</span></span> <span data-ttu-id="61c5c-132">如果继承的属性包含两个访问器 (也就是说，如果继承的属性是读写) ，则重写属性可以包含单个访问器或两个访问器。</span><span class="sxs-lookup"><span data-stu-id="61c5c-132">If the inherited property includes both accessors (i.e., if the inherited property is read-write), the overriding property can include either a single accessor or both accessors.</span></span> <span data-ttu-id="61c5c-133">**重写属性的类型必须至少具有与可访问 [性域](../../spec/basic-concepts.md#accessibility-domains))  (重写属性相同的可访问性。**</span><span class="sxs-lookup"><span data-stu-id="61c5c-133">**The overriding property's type must be at least as accessible as the overriding property ([Accessibility domains](../../spec/basic-concepts.md#accessibility-domains)).**</span></span>

-----------------

<span data-ttu-id="61c5c-134">***下面的草案规范的其余部分建议更进一步扩展接口方法的协变返回，以备稍后考虑。***</span><span class="sxs-lookup"><span data-stu-id="61c5c-134">***The remainder of the draft specification below proposes a further extension to covariant returns of interface methods to be considered later.***</span></span>

### <a name="interface-method-property-and-indexer-override"></a><span data-ttu-id="61c5c-135">接口方法、属性和索引器替代</span><span class="sxs-lookup"><span data-stu-id="61c5c-135">Interface Method, Property, and Indexer Override</span></span>

<span data-ttu-id="61c5c-136">使用 c # 8.0 添加 DIM 功能，将添加到接口中允许的成员种类，进一步增加了对成员的支持 `override` 以及协变返回。</span><span class="sxs-lookup"><span data-stu-id="61c5c-136">Adding to the kinds of members that are permitted in an interface with the addition of the DIM feature in C# 8.0, we further add support for `override` members along with covariant returns.</span></span>  <span data-ttu-id="61c5c-137">这些规则按照为 `override` 类指定的成员规则进行，但存在以下差异：</span><span class="sxs-lookup"><span data-stu-id="61c5c-137">These follow the rules of `override` members as specified for classes, with the following differences:</span></span>

<span data-ttu-id="61c5c-138">类中的以下文本：</span><span class="sxs-lookup"><span data-stu-id="61c5c-138">The following text in classes:</span></span>

> <span data-ttu-id="61c5c-139">重写声明重写的方法称为 ***重写基方法***。</span><span class="sxs-lookup"><span data-stu-id="61c5c-139">The method overridden by an override declaration is known as the ***overridden base method***.</span></span> <span data-ttu-id="61c5c-140">对于 `M` 在类中声明的重写方法 `C` ，重写的基方法是通过检查的每个基类确定的，该方法 `C` 从的直接基类开始， `C` 然后继续使用每个连续的直接基类，直到至少有一个可访问方法与 `M` 类型参数替换后具有相同签名的可访问方法。</span><span class="sxs-lookup"><span data-stu-id="61c5c-140">For an override method `M` declared in a class `C`, the overridden base method is determined by examining each base class of `C`, starting with the direct base class of `C` and continuing with each successive direct base class, until in a given base class type at least one accessible method is located which has the same signature as `M` after substitution of type arguments.</span></span>

<span data-ttu-id="61c5c-141">为提供接口的相应规范：</span><span class="sxs-lookup"><span data-stu-id="61c5c-141">is given the corresponding specification for interfaces:</span></span>

> <span data-ttu-id="61c5c-142">重写声明重写的方法称为 ***重写基方法***。</span><span class="sxs-lookup"><span data-stu-id="61c5c-142">The method overridden by an override declaration is known as the ***overridden base method***.</span></span> <span data-ttu-id="61c5c-143">对于 `M` 在接口中声明的重写方法 `I` ，重写的基方法是通过检查的每个直接或间接基接口确定的 `I` ，收集一组接口，该接口声明一个具有与 `M` 类型参数替换相同的签名的可访问方法。</span><span class="sxs-lookup"><span data-stu-id="61c5c-143">For an override method `M` declared in an interface `I`, the overridden base method is determined by examining each direct or indirect base interface of `I`, collecting the set of interfaces declaring an accessible method which has the same signature as `M` after substitution of type arguments.</span></span>  <span data-ttu-id="61c5c-144">如果这组接口具有派生程度 *最高的类型*，则为该集合中的每个类型都有一个标识或隐式引用转换，并且该类型包含一个唯一的此类方法声明，即 *重写的基方法*。</span><span class="sxs-lookup"><span data-stu-id="61c5c-144">If this set of interfaces has a *most derived type*, to which there is an identity or implicit reference conversion from every type in this set, and that type contains a unique such method declaration, then that is the *overridden base method*.</span></span>

<span data-ttu-id="61c5c-145">同样，允许 `override` 接口中的属性和索引器在 *15.7.6 Virtual、sealed、override 和 abstract 访问器*中为类指定。</span><span class="sxs-lookup"><span data-stu-id="61c5c-145">We similarly permit `override` properties and indexers in interfaces as specified for classes in *15.7.6 Virtual, sealed, override, and abstract accessors*.</span></span>

### <a name="name-lookup"></a><span data-ttu-id="61c5c-146">名称查找</span><span class="sxs-lookup"><span data-stu-id="61c5c-146">Name Lookup</span></span>

<span data-ttu-id="61c5c-147">存在类声明的名称查找 `override` 当前修改名称查找的结果，方法是在类层次结构中从最派生的声明（从 `override` 标识符的 (限定符的类型开始）起，或者在没有 `this`) 限定符时进行。</span><span class="sxs-lookup"><span data-stu-id="61c5c-147">Name lookup in the presence of class `override` declarations currently modify the result of name lookup by imposing on the found member details from the most derived `override` declaration in the class hierarchy starting from the type of the identifier's qualifier (or `this` when there is no qualifier).</span></span>  <span data-ttu-id="61c5c-148">例如，在 *12.6.2.2* 中，有</span><span class="sxs-lookup"><span data-stu-id="61c5c-148">For example, in *12.6.2.2 Corresponding parameters* we have</span></span>

> <span data-ttu-id="61c5c-149">对于在类中定义的虚拟方法和索引器，将从从接收方静态类型开始时找到的函数成员的第一个声明或重写中选取参数列表，并搜索其基类。</span><span class="sxs-lookup"><span data-stu-id="61c5c-149">For virtual methods and indexers defined in classes, the parameter list is picked from the first  declaration or override of the function member found when starting with the static type of the receiver, and searching through its base classes.</span></span>

<span data-ttu-id="61c5c-150">为此，我们将</span><span class="sxs-lookup"><span data-stu-id="61c5c-150">to this we add</span></span>

> <span data-ttu-id="61c5c-151">对于在接口中定义的虚拟方法和索引器，将从派生程度最大的类型中找到的函数成员的声明或重写中选取参数列表，这些类型包含函数成员的重写声明。</span><span class="sxs-lookup"><span data-stu-id="61c5c-151">For virtual methods and indexers defined in interfaces, the parameter list is picked from the declaration or override of the function member found in the most derived type among those types containing the declaration of override of the function member.</span></span>  <span data-ttu-id="61c5c-152">如果不存在任何唯一的此类类型，则会发生编译时错误。</span><span class="sxs-lookup"><span data-stu-id="61c5c-152">It is a compile-time error if no unique such type exists.</span></span>

<span data-ttu-id="61c5c-153">对于属性或索引器访问的结果类型，现有文本</span><span class="sxs-lookup"><span data-stu-id="61c5c-153">For the result type of a property or indexer access, the existing text</span></span>

> - <span data-ttu-id="61c5c-154">如果 I 标识实例属性，则结果是具有关联的实例表达式 E 和关联类型（属性的类型）的属性访问。</span><span class="sxs-lookup"><span data-stu-id="61c5c-154">If I identifies an instance property, then the result is a property access with an associated instance expression of E and an associated type that is the type of the property.</span></span> <span data-ttu-id="61c5c-155">如果 T 是类类型，则将从从 T 开始时找到的属性的第一个声明或重写中选取关联的类型，并搜索其基类。</span><span class="sxs-lookup"><span data-stu-id="61c5c-155">If T is a class type, the associated type is picked from the first declaration or override of the property found when starting with T, and searching through its base classes.</span></span>

<span data-ttu-id="61c5c-156">扩充了</span><span class="sxs-lookup"><span data-stu-id="61c5c-156">is augmented with</span></span>

> <span data-ttu-id="61c5c-157">如果 T 是接口类型，则会从在 T 的派生或其直接或间接基接口中找到的属性的声明或重写中选取关联的类型。</span><span class="sxs-lookup"><span data-stu-id="61c5c-157">If T is an interface type, the associated type is picked from the declaration or override of the property found in the most derived of T or its direct or indirect base interfaces.</span></span>  <span data-ttu-id="61c5c-158">如果不存在任何唯一的此类类型，则会发生编译时错误。</span><span class="sxs-lookup"><span data-stu-id="61c5c-158">It is a compile-time error if no unique such type exists.</span></span>

<span data-ttu-id="61c5c-159">应在*12.7.7.3 索引器访问*中进行类似的更改</span><span class="sxs-lookup"><span data-stu-id="61c5c-159">A similar change should be made in *12.7.7.3 Indexer access*</span></span>

<span data-ttu-id="61c5c-160">在 *12.7.6 调用表达式* 中，我们增加了现有文本</span><span class="sxs-lookup"><span data-stu-id="61c5c-160">In *12.7.6 Invocation expressions* we augment the existing text</span></span>

> - <span data-ttu-id="61c5c-161">否则，结果为具有方法或委托返回类型的关联类型的值。</span><span class="sxs-lookup"><span data-stu-id="61c5c-161">Otherwise, the result is a value, with an associated type of the return type of the method or delegate.</span></span> <span data-ttu-id="61c5c-162">如果调用的是实例方法，并且接收方属于类类型 T，则会从第一个声明中选取关联的类型，或者在从 T 开始并搜索其基类时从找到的方法中选取关联的类型。</span><span class="sxs-lookup"><span data-stu-id="61c5c-162">If the invocation is of an instance method, and the receiver is of a class type T, the associated type is picked from the first declaration or override of the method found when starting with T and searching through its base classes.</span></span>

<span data-ttu-id="61c5c-163">替换为</span><span class="sxs-lookup"><span data-stu-id="61c5c-163">with</span></span>

> <span data-ttu-id="61c5c-164">如果调用的是实例方法，并且接收方是接口类型 T，则会从 T 及其直接和间接基接口的派生接口中找到的方法的声明或重写来选取关联的类型。</span><span class="sxs-lookup"><span data-stu-id="61c5c-164">If the invocation is of an instance method, and the receiver is of an interface type T, the associated type is picked from the declaration or override of the method found in the most derived interface from among T and its direct and indirect base interfaces.</span></span>  <span data-ttu-id="61c5c-165">如果不存在任何唯一的此类类型，则会发生编译时错误。</span><span class="sxs-lookup"><span data-stu-id="61c5c-165">It is a compile-time error if no unique such type exists.</span></span>

### <a name="implicit-interface-implementations"></a><span data-ttu-id="61c5c-166">隐式接口实现</span><span class="sxs-lookup"><span data-stu-id="61c5c-166">Implicit Interface Implementations</span></span>

<span data-ttu-id="61c5c-167">规范的此部分</span><span class="sxs-lookup"><span data-stu-id="61c5c-167">This section of the specification</span></span>

> <span data-ttu-id="61c5c-168">出于接口映射的目的，类成员将 `A` 在以下情况下匹配接口成员 `B` ：</span><span class="sxs-lookup"><span data-stu-id="61c5c-168">For purposes of interface mapping, a class member `A` matches an interface member `B` when:</span></span>
> 
> - <span data-ttu-id="61c5c-169">`A` 和 `B` 是方法，且和的名称、类型和形参列表 `A` `B` 完全相同。</span><span class="sxs-lookup"><span data-stu-id="61c5c-169">`A` and `B` are methods, and the name, type, and formal parameter lists of `A` and `B` are identical.</span></span>
> - <span data-ttu-id="61c5c-170">`A` 和 `B` 是属性、和的名称和类型 `A` `B` 相同，并且具有与 `A` (相同的访问器 `B` ， `A` 如果它不是显式接口成员实现) 。</span><span class="sxs-lookup"><span data-stu-id="61c5c-170">`A` and `B` are properties, the name and type of `A` and `B` are identical, and `A` has the same accessors as `B` (`A` is permitted to have additional accessors if it is not an explicit interface member implementation).</span></span>
> - <span data-ttu-id="61c5c-171">`A` 和 `B` 是事件，而和的名称和类型 `A` `B` 是相同的。</span><span class="sxs-lookup"><span data-stu-id="61c5c-171">`A` and `B` are events, and the name and type of `A` and `B` are identical.</span></span>
> - <span data-ttu-id="61c5c-172">`A` 和 `B` 是索引器、和的类型参数列表和形参列表 `A` `B` 相同，并且 `A` 具有与 (相同的访问器， `B` `A` 如果它不是显式接口成员实现) 。</span><span class="sxs-lookup"><span data-stu-id="61c5c-172">`A` and `B` are indexers, the type and formal parameter lists of `A` and `B` are identical, and `A` has the same accessors as `B` (`A` is permitted to have additional accessors if it is not an explicit interface member implementation).</span></span>

<span data-ttu-id="61c5c-173">按如下所示进行修改：</span><span class="sxs-lookup"><span data-stu-id="61c5c-173">is modified as follows:</span></span>

> <span data-ttu-id="61c5c-174">出于接口映射的目的，类成员将 `A` 在以下情况下匹配接口成员 `B` ：</span><span class="sxs-lookup"><span data-stu-id="61c5c-174">For purposes of interface mapping, a class member `A` matches an interface member `B` when:</span></span>
> 
> - <span data-ttu-id="61c5c-175">`A` 和 `B` 是方法，且和的名称和形参列表相同，并且的返回类型可 `A` `B` `A` `B` 通过隐式引用转换的标识转换为返回类型 `B` 。</span><span class="sxs-lookup"><span data-stu-id="61c5c-175">`A` and `B` are methods, and the name and formal parameter lists of `A` and `B` are identical, and the return type of `A` is convertible to the return type of `B` via an identity of implicit reference convertion to the return type of `B`.</span></span>
> - <span data-ttu-id="61c5c-176">`A` 和 `B` 是属性、和的名称 `A` `B` 相同，具有与 `A` (相同的访问器 `B` ， `A` 如果它不是显式接口成员实现) ，则的类型可 `A` 转换为的返回类型; `B` 如果 `A` 是 readonly 属性，则为隐式引用转换。</span><span class="sxs-lookup"><span data-stu-id="61c5c-176">`A` and `B` are properties, the name of `A` and `B` are identical, `A` has the same accessors as `B` (`A` is permitted to have additional accessors if it is not an explicit interface member implementation), and the type of `A` is convertible to the return type of `B` via an identity conversion or, if `A` is a readonly property, an implicit reference conversion.</span></span>
> - <span data-ttu-id="61c5c-177">`A` 和 `B` 是事件，而和的名称和类型 `A` `B` 是相同的。</span><span class="sxs-lookup"><span data-stu-id="61c5c-177">`A` and `B` are events, and the name and type of `A` and `B` are identical.</span></span>
> - <span data-ttu-id="61c5c-178">`A` 和 `B` 为索引器，和的形参列表 `A` `B` 相同，具有与 `A` (相同的访问器 `B` ， `A` 如果它不是显式接口成员实现) ，则的类型 `A` 可转换为的返回类型; `B` 如果 `A` 为只读索引器，则为隐式引用转换。</span><span class="sxs-lookup"><span data-stu-id="61c5c-178">`A` and `B` are indexers, the formal parameter lists of `A` and `B` are identical, `A` has the same accessors as `B` (`A` is permitted to have additional accessors if it is not an explicit interface member implementation), and the type of `A` is convertible to the return type of `B` via an identity conversion or, if `A` is a readonly indexer, an implicit reference conversion.</span></span>

<span data-ttu-id="61c5c-179">从技术上讲，这是一项重大更改，如以下程序所示： C1。"今天，但会打印" C2。"M"。</span><span class="sxs-lookup"><span data-stu-id="61c5c-179">This is technically a breaking change, as the program below prints "C1.M" today, but would print "C2.M" under the proposed revision.</span></span>

``` c#
using System;

interface I1 { object M(); }
class C1 : I1 { public object M() { return "C1.M"; } }
class C2 : C1, I1 { public new string M() { return "C2.M"; } }
class Program
{
    static void Main()
    {
        I1 i = new C2();
        Console.WriteLine(i.M());
    }
}
```

<span data-ttu-id="61c5c-180">由于此重大更改，我们可能会认为不支持隐式实现的协变返回类型。</span><span class="sxs-lookup"><span data-stu-id="61c5c-180">Due to this breaking change, we might consider not supporting covariant return types on implicit implementations.</span></span>

### <a name="constraints-on-interface-implementation"></a><span data-ttu-id="61c5c-181">接口实现的约束</span><span class="sxs-lookup"><span data-stu-id="61c5c-181">Constraints on Interface Implementation</span></span>

<span data-ttu-id="61c5c-182">**我们将需要一条规则，指出显式接口实现必须声明一个返回类型，该返回类型的派生程度不小于在其基接口中的任何重写中声明的返回类型。**</span><span class="sxs-lookup"><span data-stu-id="61c5c-182">**We will need a rule that an explicit interface implementation must declare a return type no less derived than the return type declared in any override in its base interfaces.**</span></span>

### <a name="api-compatibility-implications"></a><span data-ttu-id="61c5c-183">API 兼容性问题</span><span class="sxs-lookup"><span data-stu-id="61c5c-183">API Compatibility Implications</span></span>

<span data-ttu-id="61c5c-184">*TBD*</span><span class="sxs-lookup"><span data-stu-id="61c5c-184">*TBD*</span></span>

### <a name="open-issues"></a><span data-ttu-id="61c5c-185">未结的问题</span><span class="sxs-lookup"><span data-stu-id="61c5c-185">Open Issues</span></span>

<span data-ttu-id="61c5c-186">该规范并不表示调用方如何获取更精确的返回类型。</span><span class="sxs-lookup"><span data-stu-id="61c5c-186">The specification does not say how the caller gets the more refined return type.</span></span> <span data-ttu-id="61c5c-187">可能会以类似于调用方获取最常获得的重写参数规范的方式来完成。</span><span class="sxs-lookup"><span data-stu-id="61c5c-187">Presumably that would be done in a way similar to the way that callers get the most derived override's parameter specifications.</span></span>

--------------

<span data-ttu-id="61c5c-188">如果有以下接口：</span><span class="sxs-lookup"><span data-stu-id="61c5c-188">If we have the following interfaces:</span></span>

```csharp
interface I1 { I1 M(); }
interface I2 { I2 M(); }
interface I3: I1, I2 { override I3 M(); }
```

<span data-ttu-id="61c5c-189">请注意，在中 `I3` ，方法 `I1.M()` 和已 `I2.M()` "合并"。</span><span class="sxs-lookup"><span data-stu-id="61c5c-189">Note that in `I3`, the methods `I1.M()` and `I2.M()` have been “merged”.</span></span>  <span data-ttu-id="61c5c-190">实现时 `I3` ，必须同时实现它们。</span><span class="sxs-lookup"><span data-stu-id="61c5c-190">When implementing `I3`, it is necessary to implement them both together.</span></span>

<span data-ttu-id="61c5c-191">通常，我们需要显式实现来引用原始方法。</span><span class="sxs-lookup"><span data-stu-id="61c5c-191">Generally, we require an explicit implementation to refer to the original method.</span></span>  <span data-ttu-id="61c5c-192">问题在于，在类中</span><span class="sxs-lookup"><span data-stu-id="61c5c-192">The question is, in a class</span></span>

```csharp
class C : I1, I2, I3
{
    C IN.M();
}
```

<span data-ttu-id="61c5c-193">这是什么意思？</span><span class="sxs-lookup"><span data-stu-id="61c5c-193">What does that mean here?</span></span>  <span data-ttu-id="61c5c-194">*N*应该是什么？</span><span class="sxs-lookup"><span data-stu-id="61c5c-194">What should *N* be?</span></span>

<span data-ttu-id="61c5c-195">我建议您允许实现 `I1.M` 或 `I2.M` (，而不是同时实现) ，并将其视为实现两者。</span><span class="sxs-lookup"><span data-stu-id="61c5c-195">I suggest that we permit implementing either `I1.M` or `I2.M` (but not both), and treat that as an implementation of both.</span></span>

## <a name="drawbacks"></a><span data-ttu-id="61c5c-196">缺点</span><span class="sxs-lookup"><span data-stu-id="61c5c-196">Drawbacks</span></span>
[drawbacks]: #drawbacks

- <span data-ttu-id="61c5c-197">[] 每个语言更改都必须为其本身付费。</span><span class="sxs-lookup"><span data-stu-id="61c5c-197">[ ] Every language change must pay for itself.</span></span>
- <span data-ttu-id="61c5c-198">[] 我们应该确保性能合理，甚至在深层继承层次结构中也是如此</span><span class="sxs-lookup"><span data-stu-id="61c5c-198">[ ] We should ensure that the performance is reasonable, even in the case of deep inheritance hierarchies</span></span>
- <span data-ttu-id="61c5c-199">[] 我们应该确保翻译策略的项目不会影响语言语义，即使在使用旧编译器的新 IL 时也是如此。</span><span class="sxs-lookup"><span data-stu-id="61c5c-199">[ ] We should ensure that artifacts of the translation strategy do not affect language semantics, even when consuming new IL from old compilers.</span></span>

## <a name="alternatives"></a><span data-ttu-id="61c5c-200">备选方法</span><span class="sxs-lookup"><span data-stu-id="61c5c-200">Alternatives</span></span>
[alternatives]: #alternatives

<span data-ttu-id="61c5c-201">我们可以略微放宽语言规则，在源中，</span><span class="sxs-lookup"><span data-stu-id="61c5c-201">We could relax the language rules slightly to allow, in source,</span></span>

```csharp
abstract class Cloneable
{
    public abstract Cloneable Clone();
}

class Digit : Cloneable
{
    public override Cloneable Clone()
    {
        return this.Clone();
    }

    public new Digit Clone() // Error: 'Digit' already defines a member called 'Clone' with the same parameter types
    {
        return this;
    }
}
```

## <a name="unresolved-questions"></a><span data-ttu-id="61c5c-202">未解决的问题</span><span class="sxs-lookup"><span data-stu-id="61c5c-202">Unresolved questions</span></span>
[unresolved]: #unresolved-questions

- <span data-ttu-id="61c5c-203">[] 编译为使用此功能的 Api 将在较旧版本的语言中工作？</span><span class="sxs-lookup"><span data-stu-id="61c5c-203">[ ] How will APIs that have been compiled to use this feature work in older versions of the language?</span></span>

## <a name="design-meetings"></a><span data-ttu-id="61c5c-204">设计会议</span><span class="sxs-lookup"><span data-stu-id="61c5c-204">Design meetings</span></span>

- <span data-ttu-id="61c5c-205">一些讨论 <https://github.com/dotnet/roslyn/issues/357> 。</span><span class="sxs-lookup"><span data-stu-id="61c5c-205">some discussion at <https://github.com/dotnet/roslyn/issues/357>.</span></span>
- https://github.com/dotnet/csharplang/blob/master/meetings/2020/LDM-2020-01-08.md
- <span data-ttu-id="61c5c-206">针对决策的离线讨论，以支持仅在 c # 9.0 中重写类方法。</span><span class="sxs-lookup"><span data-stu-id="61c5c-206">Offline discussion toward a decision to support overriding of class methods only in C# 9.0.</span></span>


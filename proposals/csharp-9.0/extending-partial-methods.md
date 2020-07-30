---
ms.openlocfilehash: c4fc0bd978e02bbd4da6ed77fe1d2ac3533b2c12
ms.sourcegitcommit: a88d56e3131d7a94c65e637c276379541a3cd491
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/30/2020
ms.locfileid: "87434478"
---
<a name="extending-partial-methods"></a><span data-ttu-id="2de54-101">扩展分部方法</span><span class="sxs-lookup"><span data-stu-id="2de54-101">Extending Partial Methods</span></span>
=====

## <a name="summary"></a><span data-ttu-id="2de54-102">总结</span><span class="sxs-lookup"><span data-stu-id="2de54-102">Summary</span></span>
<span data-ttu-id="2de54-103">此建议旨在消除对 c # 中的方法的签名的所有限制 `partial` 。</span><span class="sxs-lookup"><span data-stu-id="2de54-103">This proposal aims to remove all restrictions around the signatures of `partial` methods in C#.</span></span> <span data-ttu-id="2de54-104">目标是扩展一组方案，在这些方案中，这些方法可与源生成器一起使用，并且是更通用的 c # 方法声明形式。</span><span class="sxs-lookup"><span data-stu-id="2de54-104">The goal being to expand the set of scenarios in which these methods can work with source generators as well as being a more general declaration form for C# methods.</span></span>

<span data-ttu-id="2de54-105">另请参阅[原始分部方法规范](../../spec/classes.md#partial-methods)。</span><span class="sxs-lookup"><span data-stu-id="2de54-105">See also the [original partial methods specification](../../spec/classes.md#partial-methods).</span></span>

## <a name="motivation"></a><span data-ttu-id="2de54-106">动机</span><span class="sxs-lookup"><span data-stu-id="2de54-106">Motivation</span></span>
<span data-ttu-id="2de54-107">对于将方法拆分为声明和定义/实现的开发人员，c # 提供了有限的支持。</span><span class="sxs-lookup"><span data-stu-id="2de54-107">C# has limited support for developers splitting methods into declarations and definitions / implementations.</span></span> 

```cs 
partial class C
{
    // The declaration of C.M
    partial void M(string message);
}

partial class C
{
    // The definition of C.M
    partial void M(string message) => Console.WriteLine(message);
}
```

<span data-ttu-id="2de54-108">方法的一个行为 `partial` 是：如果定义不存在，则该语言只会擦除对方法的任何调用 `partial` 。</span><span class="sxs-lookup"><span data-stu-id="2de54-108">One behavior of `partial` methods is that when the definition is absent then the language will simply erase any calls to the `partial` method.</span></span> <span data-ttu-id="2de54-109">实质上，它的行为类似于调用方法，在 `[Conditional]` 此方法中，条件的计算结果为 false。</span><span class="sxs-lookup"><span data-stu-id="2de54-109">Essentially it behaves like a call to a `[Conditional]` method where the condition was evaluated to false.</span></span> 

```cs
partial class D
{
    partial void M(string message);

    void Example()
    {
        M(GetIt()); // Call to M and GetIt erased at compile time
    }

    string GetIt() => "Hello World";
}
```

<span data-ttu-id="2de54-110">此功能的原始动机是以设计器生成的代码的形式生成的源。</span><span class="sxs-lookup"><span data-stu-id="2de54-110">The original motivation for this feature was source generation in the form of designer generated code.</span></span> <span data-ttu-id="2de54-111">用户经常编辑生成的代码，因为他们希望挂钩生成的代码的某个方面。</span><span class="sxs-lookup"><span data-stu-id="2de54-111">Users were constantly editing the generated code because they wanted to hook some aspect of the generated code.</span></span> <span data-ttu-id="2de54-112">初始化组件后，最值得注意的是 Windows 窗体启动过程的一部分。</span><span class="sxs-lookup"><span data-stu-id="2de54-112">Most notably parts of the Windows Forms startup process, after components were initialized.</span></span>

<span data-ttu-id="2de54-113">编辑生成的代码很容易出错，因为任何导致设计器重新生成代码的操作都会导致清除用户编辑。</span><span class="sxs-lookup"><span data-stu-id="2de54-113">Editing the generated code was error prone because any action which caused the designer to regenerate the code would cause the user edit to be erased.</span></span> <span data-ttu-id="2de54-114">`partial`方法功能减轻此张力，因为它允许设计器以方法的形式发出挂钩 `partial` 。</span><span class="sxs-lookup"><span data-stu-id="2de54-114">The `partial` method feature eased this tension because it allowed designers to emit hooks in the form of `partial` methods.</span></span> 

<span data-ttu-id="2de54-115">设计器可以发出挂钩 `partial void OnComponentInit()` ，如，开发人员可以为其定义声明或不定义它们。</span><span class="sxs-lookup"><span data-stu-id="2de54-115">Designers could emit hooks like `partial void OnComponentInit()` and developers could define declarations for them or not define them.</span></span> <span data-ttu-id="2de54-116">在这两种情况下，尽管生成的代码会进行编译，并且对进程感兴趣的开发人员可以根据需要挂钩。</span><span class="sxs-lookup"><span data-stu-id="2de54-116">In either case though the generated code would compile and developers who were interested in the process could hook in as needed.</span></span> 

<span data-ttu-id="2de54-117">这意味着分部方法具有几个限制：</span><span class="sxs-lookup"><span data-stu-id="2de54-117">This does mean that partial methods have several restrictions:</span></span>

1. <span data-ttu-id="2de54-118">必须具有 `void` 返回类型。</span><span class="sxs-lookup"><span data-stu-id="2de54-118">Must have a `void` return type.</span></span>
1. <span data-ttu-id="2de54-119">不能具有 `out` 参数。</span><span class="sxs-lookup"><span data-stu-id="2de54-119">Cannot have `out` parameters.</span></span> 
1. <span data-ttu-id="2de54-120">不能具有任何可访问性（隐式 `private` ）。</span><span class="sxs-lookup"><span data-stu-id="2de54-120">Cannot have any accessibility (implicitly `private`).</span></span>

<span data-ttu-id="2de54-121">存在这些限制的原因是，在清除调用站点时，语言必须能够发出代码。</span><span class="sxs-lookup"><span data-stu-id="2de54-121">These restrictions exist because the language must be able to emit code when the call site is erased.</span></span> <span data-ttu-id="2de54-122">`private`由于无法在程序集元数据中公开该成员，因此可将其删除。</span><span class="sxs-lookup"><span data-stu-id="2de54-122">Given they can be erased `private` is the only possible accessibility because the member can't be exposed in assembly metadata.</span></span> <span data-ttu-id="2de54-123">这些限制还用于限制 `partial` 可应用方法的方案集。</span><span class="sxs-lookup"><span data-stu-id="2de54-123">These restrictions also serve to limit the set of scenarios in which `partial` methods can be applied.</span></span>

<span data-ttu-id="2de54-124">此处的建议是删除有关方法的所有现有限制 `partial` 。</span><span class="sxs-lookup"><span data-stu-id="2de54-124">The proposal here is to remove all of the existing restrictions around `partial` methods.</span></span> <span data-ttu-id="2de54-125">实质上，使它们具有 `out` 非 void 返回类型或任何类型的可访问性。</span><span class="sxs-lookup"><span data-stu-id="2de54-125">Essentially let them have `out`, non-void return types or any type of accessibility.</span></span> <span data-ttu-id="2de54-126">这样 `partial` 的声明将具有定义必须存在的附加要求。</span><span class="sxs-lookup"><span data-stu-id="2de54-126">Such `partial` declarations would then have the added requirement that a definition must exist.</span></span> <span data-ttu-id="2de54-127">这意味着语言不必考虑擦除调用站点的影响。</span><span class="sxs-lookup"><span data-stu-id="2de54-127">That means the language does not have to consider the impact of erasing the call sites.</span></span> 

<span data-ttu-id="2de54-128">这会扩展方法可参与的一组生成器方案， `partial` 因此与源生成器功能非常类似。</span><span class="sxs-lookup"><span data-stu-id="2de54-128">This would expand the set of generator scenarios that `partial` methods could participate in and hence link in nicely with our source generators feature.</span></span> <span data-ttu-id="2de54-129">例如，可使用以下模式定义 regex：</span><span class="sxs-lookup"><span data-stu-id="2de54-129">For example a regex could be defined using the following pattern:</span></span>

```cs
[RegexGenerated("(dog|cat|fish)")]
partial bool IsPetMatch(string input);
```

<span data-ttu-id="2de54-130">这为开发人员提供了一种简单的声明性方法来选择生成器，并为生成器提供一组非常简单的声明，用于在源代码中查找其生成的输出。</span><span class="sxs-lookup"><span data-stu-id="2de54-130">This gives both the developer a simple declarative way of opting into generators as well as giving generators a very easy set of declarations to look through in the source code to drive their generated output.</span></span> 

<span data-ttu-id="2de54-131">与生成器与以下代码片段挂钩的难度进行比较。</span><span class="sxs-lookup"><span data-stu-id="2de54-131">Compare that with the difficulty that a generator would have hooking up the following snippet of code.</span></span> 

```cs
var regex = new RegularExpression("(dog|cat|fish)");
if (regex.IsMatch(someInput))
{

}
```

<span data-ttu-id="2de54-132">假设编译器不允许生成器修改代码挂钩，这种模式对于生成器而言可能会很大。</span><span class="sxs-lookup"><span data-stu-id="2de54-132">Given that the compiler doesn't allow generators to modify code hooking up this pattern would be pretty much impossible for generators.</span></span> <span data-ttu-id="2de54-133">它们需要使用实现中的反射 `IsMatch` ，或要求用户将其调用站点更改为新方法，并重构 regex 以将字符串文本作为参数传递。</span><span class="sxs-lookup"><span data-stu-id="2de54-133">They would need to resort to reflection in the `IsMatch` implementation, or asking users to change their call sites to a new method + refactor the regex to pass the string literal as an argument.</span></span> <span data-ttu-id="2de54-134">这种方法非常杂乱。</span><span class="sxs-lookup"><span data-stu-id="2de54-134">It's pretty messy.</span></span>

## <a name="detailed-design"></a><span data-ttu-id="2de54-135">详细设计</span><span class="sxs-lookup"><span data-stu-id="2de54-135">Detailed Design</span></span>
<span data-ttu-id="2de54-136">语言将更改为允许 `partial` 使用显式可访问性修饰符对方法进行批注。</span><span class="sxs-lookup"><span data-stu-id="2de54-136">The language will change to allow `partial` methods to be annotated with an explicit accessibility modifier.</span></span> <span data-ttu-id="2de54-137">这意味着可以将它们标记为 `private` 、 `public` 等。</span><span class="sxs-lookup"><span data-stu-id="2de54-137">This means they can be labeled as `private`, `public`, etc ...</span></span> 

<span data-ttu-id="2de54-138">如果 `partial` 方法具有显式可访问性修饰符，则该语言将要求声明具有匹配的定义，即使可访问性是 `private` ：</span><span class="sxs-lookup"><span data-stu-id="2de54-138">When a `partial` method has an explicit accessibility modifier though the language will require that the declaration has a matching definition even when the accessibility is `private`:</span></span>

```cs
partial class C
{
    // Okay because no definition is required here
    partial void M1();

    // Okay because M2 has a definition
    private partial void M2();

    // Error: partial method M3 must have a definition
    private partial void M3();
}

partial class C
{
    private partial void M2() { }
}
```

<span data-ttu-id="2de54-139">此外，此语言将删除对 `partial` 具有显式可访问性的方法所显示的所有限制。</span><span class="sxs-lookup"><span data-stu-id="2de54-139">Further the language will remove all restrictions on what can appear on a `partial` method which has an explicit accessibility.</span></span> <span data-ttu-id="2de54-140">此类声明可以包含非 void 返回类型、 `out` 参数、 `extern` 修饰符等 .。。这些签名将具有 c # 语言的完整表现力。</span><span class="sxs-lookup"><span data-stu-id="2de54-140">Such declarations can contain non-void return types, `out` parameters, `extern` modifier, etc ... These signatures will have the full expressivity of the C# language.</span></span>

```cs
partial class D
{
    // Okay
    internal partial bool TryParse(string s, out int i); 
}

partial class D
{
    internal partial bool TryParse(string s, out int i) { }
}
```

<span data-ttu-id="2de54-141">这显式允许 `partial` 方法参与 `overrides` 和 `interface` 实现：</span><span class="sxs-lookup"><span data-stu-id="2de54-141">This explicitly allows for `partial` methods to participate in `overrides` and `interface` implementations:</span></span>

```cs
interface IStudent
{
    string GetName();
}

partial class C : IStudent
{
    public virtual partial string GetName(); 
}

partial class C
{
    public virtual partial string GetName() => "Jarde";
}
```

<span data-ttu-id="2de54-142">编译器将更改它在 `partial` 方法包含非法元素时发出的错误，而本质上说：</span><span class="sxs-lookup"><span data-stu-id="2de54-142">The compiler will change the error it emits when a `partial` method contains an illegal element to essentially say:</span></span>

> <span data-ttu-id="2de54-143">不能 `ref` 对 `partial` 缺少显式辅助功能的方法使用</span><span class="sxs-lookup"><span data-stu-id="2de54-143">Cannot use `ref` on a `partial` method that lacks explicit accessibility</span></span> 

<span data-ttu-id="2de54-144">当使用此功能时，这将有助于指导开发人员正确的方向。</span><span class="sxs-lookup"><span data-stu-id="2de54-144">This will help point developers in the right direction when using this feature.</span></span>

<span data-ttu-id="2de54-145">限制：</span><span class="sxs-lookup"><span data-stu-id="2de54-145">Restrictions:</span></span>
- <span data-ttu-id="2de54-146">`partial`具有显式辅助功能的声明必须具有定义</span><span class="sxs-lookup"><span data-stu-id="2de54-146">`partial` declarations with explicit accessibility must have a definition</span></span>
- <span data-ttu-id="2de54-147">`partial`声明和定义签名必须在所有方法和参数修饰符上都匹配。</span><span class="sxs-lookup"><span data-stu-id="2de54-147">`partial` declarations and definition signatures must match on all method and parameter modifiers.</span></span> <span data-ttu-id="2de54-148">唯一可能不同的方面是参数名称和属性列表（这不是新的，而是方法的现有要求 `partial` ）。</span><span class="sxs-lookup"><span data-stu-id="2de54-148">The only aspects which can differ are parameter names and attribute lists (this is not new but rather an existing requirement of `partial` methods).</span></span>

## <a name="questions"></a><span data-ttu-id="2de54-149">问题</span><span class="sxs-lookup"><span data-stu-id="2de54-149">Questions</span></span>

### <a name="partial-on-all-members"></a><span data-ttu-id="2de54-150">部分在所有成员上</span><span class="sxs-lookup"><span data-stu-id="2de54-150">partial on all members</span></span>
<span data-ttu-id="2de54-151">假设我们要扩展 `partial` 更友好的源生成器，还应将其扩展为适用于所有类成员吗？</span><span class="sxs-lookup"><span data-stu-id="2de54-151">Given that we're expanding `partial` to be more friendly to source generators should we also expand it to work on all class members?</span></span> <span data-ttu-id="2de54-152">例如，我们应该能够声明 `partial` 构造函数、运算符等 .。。</span><span class="sxs-lookup"><span data-stu-id="2de54-152">For example should we be able to declare `partial` constructors, operators, etc ...</span></span>

<span data-ttu-id="2de54-153">**解决方法**思路非常不错，但在 c # 9 计划中，我们正努力避免不必要的功能蔓延。</span><span class="sxs-lookup"><span data-stu-id="2de54-153">**Resolution** The idea is sound but at this point in the C# 9 schedule we're trying to avoid unnecessary feature creep.</span></span> <span data-ttu-id="2de54-154">想要解决将此功能扩展到使用现代源生成器的即时问题。</span><span class="sxs-lookup"><span data-stu-id="2de54-154">Want to solve the immediate problem of expanding the feature to work with modern source generators.</span></span> 

<span data-ttu-id="2de54-155">`partial`C # 10 版本将对扩展以支持其他成员。</span><span class="sxs-lookup"><span data-stu-id="2de54-155">Extending `partial` to support other members will be considered for the C# 10 release.</span></span> <span data-ttu-id="2de54-156">可能会考虑此扩展。</span><span class="sxs-lookup"><span data-stu-id="2de54-156">Seems likely that we will consider this extension.</span></span>

### <a name="use-abstract-instead-of-partial"></a><span data-ttu-id="2de54-157">使用抽象而不是部分</span><span class="sxs-lookup"><span data-stu-id="2de54-157">Use abstract instead of partial</span></span>
<span data-ttu-id="2de54-158">此建议的关键实质上是确保声明具有相应的定义/实现。</span><span class="sxs-lookup"><span data-stu-id="2de54-158">The crux of this proposal is essentially ensuring that a declaration has a corresponding definition / implementation.</span></span> <span data-ttu-id="2de54-159">假设我们要使用， `abstract` 因为它已是一个可强制开发人员考虑实现的语言关键字？</span><span class="sxs-lookup"><span data-stu-id="2de54-159">Given that should we use `abstract` since it's already a language keyword that forces the developer to think about having an implementation?</span></span>

<span data-ttu-id="2de54-160">**解决方法**这是一个关于此操作的正常讨论，但最终决定了它。</span><span class="sxs-lookup"><span data-stu-id="2de54-160">**Resolution** There was a healthy discussion about this but eventually it was decided against.</span></span>
<span data-ttu-id="2de54-161">是的，需要熟悉这些要求，但概念却大相径庭。</span><span class="sxs-lookup"><span data-stu-id="2de54-161">Yes the requirements are familiar but the concepts are significantly different.</span></span>
<span data-ttu-id="2de54-162">如果不这样做，开发人员可以轻松地让开发人员相信它们正在创建虚拟槽。</span><span class="sxs-lookup"><span data-stu-id="2de54-162">Could easily lead the developer to believe they were creating virtual slots when they were not doing so.</span></span>

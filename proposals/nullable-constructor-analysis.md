---
ms.openlocfilehash: 745db71694e948808ba8665dae32c146baa40c80
ms.sourcegitcommit: a69376ba67c6344b1f939f3d87cdb764c8179687
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/14/2020
ms.locfileid: "88216846"
---
# <a name="nullable-constructor-analysis"></a><span data-ttu-id="a23a9-101">可为 null 的构造函数分析</span><span class="sxs-lookup"><span data-stu-id="a23a9-101">Nullable constructor analysis</span></span>

<span data-ttu-id="a23a9-102">此建议旨在使用可以为 null 的构造函数分析来解决几个未解决的问题。</span><span class="sxs-lookup"><span data-stu-id="a23a9-102">This proposal is intended to resolve a few of the outstanding problems with nullable constructor analysis.</span></span>

## <a name="how-it-works-currently"></a><span data-ttu-id="a23a9-103">当前的工作方式</span><span class="sxs-lookup"><span data-stu-id="a23a9-103">How it works currently</span></span>

<span data-ttu-id="a23a9-104">构造函数的可为 null 分析实质上是运行明确的赋值过程，并在构造函数不初始化不可为 null 的引用类型成员的情况下报告警告 (例如：所有代码路径中的字段、自动属性或类似字段的事件) 。</span><span class="sxs-lookup"><span data-stu-id="a23a9-104">Nullable analysis of constructors works by essentially running a definite assignment pass and reporting a warning if a constructor does not initialize a non-nullable reference type member (for example: a field, auto-property, or field-like event) in all code paths.</span></span> <span data-ttu-id="a23a9-105">否则，将构造函数视为用于分析的普通方法。</span><span class="sxs-lookup"><span data-stu-id="a23a9-105">The constructor is otherwise treated like an ordinary method for analysis purposes.</span></span> <span data-ttu-id="a23a9-106">此方法附带了几个问题。</span><span class="sxs-lookup"><span data-stu-id="a23a9-106">This approach comes with a few problems.</span></span>

<span data-ttu-id="a23a9-107">首先，成员的初始流状态不准确：</span><span class="sxs-lookup"><span data-stu-id="a23a9-107">First is that the initial flow state of members is not accurate:</span></span>

```cs
public class C
{
    public string Prop { get; set; }
    public C() // we get no "uninitialized member" warning, as expected
    {
        Prop.ToString(); // unexpectedly, there is no "possible null receiver" warning here
        Prop = "";
    }
}
```

<span data-ttu-id="a23a9-108">另一种情况是断言/"throw" 语句不会阻止字段初始化警告：</span><span class="sxs-lookup"><span data-stu-id="a23a9-108">Another is that assertions/'throw' statements do not prevent field initialization warnings:</span></span>

```cs
public class C
{
    public string Prop { get; set; }
    public C() // unexpected warning: 'Prop' is not initialized
    {
        Init();

        if (Prop is null)
        {
            throw new Exception();
        }
    }

    void Init()
    {
        Prop = "some default";
    }
}
```

## <a name="an-alternative-approach"></a><span data-ttu-id="a23a9-109">替代方法</span><span class="sxs-lookup"><span data-stu-id="a23a9-109">An alternative approach</span></span>

<span data-ttu-id="a23a9-110">我们可以通过使用类似于分析的方法来解决这种情况 `[MemberNotNull]` ，其中，字段标记为 "可能-空"，如果在字段仍处于 "可能为 null" 状态时退出方法，则会发出警告。</span><span class="sxs-lookup"><span data-stu-id="a23a9-110">We can address this by instead taking an approach similar to `[MemberNotNull]` analysis, where fields are marked maybe-null and a warning is given if we ever exit the method when a field is still in a maybe-null state.</span></span> <span data-ttu-id="a23a9-111">为此，我们可以引入以下规则：</span><span class="sxs-lookup"><span data-stu-id="a23a9-111">We can do this by introducing the following rules:</span></span>

<span data-ttu-id="a23a9-112">\**引用类型上没有初始值设定项\*\*\*或*的构造函数</span><span class="sxs-lookup"><span data-stu-id="a23a9-112">**A constructor on a reference type with no initializer** *or*</span></span>  
<span data-ttu-id="a23a9-113">**具有 `: base(...)` 初始值设定项的引用类型上的构造函数** 具有由确定的初始可为 null 的流状态：</span><span class="sxs-lookup"><span data-stu-id="a23a9-113">**A constructor on a reference type with a `: base(...)` initializer** has an initial nullable flow state determined by:</span></span>
- <span data-ttu-id="a23a9-114">将基类型成员初始化为其声明的状态，因为我们希望基构造函数初始化基成员。</span><span class="sxs-lookup"><span data-stu-id="a23a9-114">Initializing base type members to their declared state, since we expect the base constructor to initialize the base members.</span></span>
- <span data-ttu-id="a23a9-115">然后，将类型中的所有适用成员初始化为指定的状态，方法是将 `default` 文本分配给成员。</span><span class="sxs-lookup"><span data-stu-id="a23a9-115">Then initializing all applicable members in the type to the state given by assigning a `default` literal to the member.</span></span> <span data-ttu-id="a23a9-116">如果是以下情况，则成员适用：</span><span class="sxs-lookup"><span data-stu-id="a23a9-116">A member is applicable if:</span></span>
  - <span data-ttu-id="a23a9-117">它没有在意为 null 性， *并且*</span><span class="sxs-lookup"><span data-stu-id="a23a9-117">It does not have oblivious nullability, *and*</span></span>
  - <span data-ttu-id="a23a9-118">它是实例，要分析的构造函数是实例，或者成员是静态的，并且正在分析的构造函数是静态的。</span><span class="sxs-lookup"><span data-stu-id="a23a9-118">It is instance and the constructor being analyzed is instance, or the member is static and the constructor being analyzed is static.</span></span>
  - <span data-ttu-id="a23a9-119">我们期望 `default` 文本生成 `NotNull` 不可为 null 的值类型、 `MaybeNull` 引用类型或可以为 null 的值类型的状态，以及不受 `MaybeDefault` 约束的泛型的状态。</span><span class="sxs-lookup"><span data-stu-id="a23a9-119">We expect the `default` literal to yield a `NotNull` state for non-nullable value types, a `MaybeNull` state for reference types or nullable value types, and a `MaybeDefault` state for unconstrained generics.</span></span>
- <span data-ttu-id="a23a9-120">然后访问相应成员的初始值设定项，相应地更新流状态。</span><span class="sxs-lookup"><span data-stu-id="a23a9-120">Then visiting the initializers for the applicable members, updating the flow state accordingly.</span></span>
  - <span data-ttu-id="a23a9-121">这允许使用字段/属性初始值设定项，并在构造函数中初始化其他不可以为 null 的引用成员。</span><span class="sxs-lookup"><span data-stu-id="a23a9-121">This allows some non-nullable reference members to be initialized using a field/property initializer, and others to be initialized within the constructor.</span></span>
  - <span data-ttu-id="a23a9-122">预期是编译器将按一次对初始值设定项进行流式分析和报告诊断，然后将生成的流状态复制为没有初始值设定项的每个构造函数的初始状态 `: this(...)` 。</span><span class="sxs-lookup"><span data-stu-id="a23a9-122">The expectation is that the compiler will flow-analyze and report diagnostics on the initializers once, then copy the resulting flow state as the initial state for each constructor which does not have a `: this(...)` initializer.</span></span>

<span data-ttu-id="a23a9-123">**对于具有 `: this()` 引用默认值类型构造函数的初始值设定项的值类型，其构造函数** 具有给定的初始流状态，该状态通过向成员分配文本来将所有适用的成员初始化为给定状态 `default` 。</span><span class="sxs-lookup"><span data-stu-id="a23a9-123">**A constructor on a value type with a `: this()` initializer referencing the default value type constructor** has an initial flow state given by initializing all applicable members to the state given by assigning a `default` literal to the member.</span></span>

<span data-ttu-id="a23a9-124">**具有 `: this(...)` 初始值设定项或的引用类型的构造函数** *or*</span><span class="sxs-lookup"><span data-stu-id="a23a9-124">**A constructor on a reference type with a `: this(...)` initializer** *or*</span></span>  
<span data-ttu-id="a23a9-125">\**值类型上的构造函数，其初始值设定项未引用默认值类型构造函数\*\*\*或*</span><span class="sxs-lookup"><span data-stu-id="a23a9-125">**A constructor on a value type with an initializer not referencing the default value type constructor** *or*</span></span>  
<span data-ttu-id="a23a9-126">**没有初始值设定项的值类型上的构造函数** 具有与普通方法相同的初始可以为 null 的流状态。</span><span class="sxs-lookup"><span data-stu-id="a23a9-126">**A constructor on a value type with no initializer** has the same initial nullable flow state as an ordinary method.</span></span>  
<span data-ttu-id="a23a9-127">成员具有基于已声明的注释和可以为 null 的属性的初始状态。</span><span class="sxs-lookup"><span data-stu-id="a23a9-127">Members have an initial state based on the declared annotations and nullability attributes.</span></span> <span data-ttu-id="a23a9-128">对于值类型，在没有初始值设定项的情况下，我们需要明确的赋值分析来提供所需的安全级别 `: this(...)` 。</span><span class="sxs-lookup"><span data-stu-id="a23a9-128">In the case of value types, we expect definite assignment analysis to provide the desired level of safety when there is no `: this(...)` initializer.</span></span> <span data-ttu-id="a23a9-129">这与现有的行为相同。</span><span class="sxs-lookup"><span data-stu-id="a23a9-129">This is the same as the existing behavior.</span></span>

<span data-ttu-id="a23a9-130">**在构造函数中的每个显式或隐式 "return" 中**，我们将为其流状态与其批注和为空性属性不兼容的每个成员提供警告。</span><span class="sxs-lookup"><span data-stu-id="a23a9-130">**At each explicit or implicit 'return' in a constructor**, we give a warning for each member whose flow state is incompatible with its annotations and nullability attributes.</span></span> <span data-ttu-id="a23a9-131">此操作的合理代理是：如果在返回点将成员分配给其自身，则会产生一个为空性警告，然后在返回点生成一个为空性警告。</span><span class="sxs-lookup"><span data-stu-id="a23a9-131">A reasonable proxy for this is: if assigning the member to itself at the return point would produce a nullability warning, then a nullability warning will be produced at the return point.</span></span>

<span data-ttu-id="a23a9-132">在某些情况下，这可能会导致相同成员出现大量警告。</span><span class="sxs-lookup"><span data-stu-id="a23a9-132">It's possible this could result in a lot of warnings for the same members in some scenarios.</span></span> <span data-ttu-id="a23a9-133">作为 "延伸目标"，我认为我们应该考虑以下 "优化"：</span><span class="sxs-lookup"><span data-stu-id="a23a9-133">As a "stretch goal" I think we should consider the following "optimizations":</span></span>
- <span data-ttu-id="a23a9-134">如果某个成员在适用的构造函数中的所有返回点上都具有不兼容的流状态，我们会对构造函数的名称语法而不是每个返回点发出警告。</span><span class="sxs-lookup"><span data-stu-id="a23a9-134">If a member has an incompatible flow state at all return points in an applicable constructor, we warn on the constructor's name syntax instead of on each return point individually.</span></span>
- <span data-ttu-id="a23a9-135">如果在所有适用的构造函数中，成员的所有返回点都具有不兼容的流状态，我们会在成员声明自身上发出警告。</span><span class="sxs-lookup"><span data-stu-id="a23a9-135">If a member has an incompatible flow state at all return points in all applicable constructors, we warn on the member declaration itself.</span></span>

## <a name="consequences-of-this-approach"></a><span data-ttu-id="a23a9-136">此方法的后果</span><span class="sxs-lookup"><span data-stu-id="a23a9-136">Consequences of this approach</span></span>

```cs
public class C
{
    public string Prop { get; set; }
    public C()
    {
        Prop = null; // Warning: cannot assign null to 'Prop'
    } // Warning: Prop may be null when exiting 'C.C()'
    
    // This is consistent with currently shipped behavior:
    [MemberNotNull(nameof(Prop))]
    void M()
    {
        Prop = null; // Warning: cannot assign null to 'Prop'
    } // Warning: Prop may be null when exiting 'C.M()'
}
```

<span data-ttu-id="a23a9-137">上述方案产生了与同一属性相对应的多个警告。</span><span class="sxs-lookup"><span data-stu-id="a23a9-137">The above scenario produces multiple warnings corresponding to the same property.</span></span> <span data-ttu-id="a23a9-138">如果方法中存在更多的返回点，则可能会根据成员处于错误流状态的返回点的数目，无限期地生成很多警告。</span><span class="sxs-lookup"><span data-stu-id="a23a9-138">If there are more return points in the method, indefinitely many warnings could be produced depending on the number of return points in which a member has a bad flow state.</span></span>

<span data-ttu-id="a23a9-139">但是，这与我们已为和属性提供的行为 `[MemberNotNull]` 一致 `[NotNull]` ：当出现错误值时，我们会发出警告，当你返回变量可能包含错误值的位置时，我们将再次警告。</span><span class="sxs-lookup"><span data-stu-id="a23a9-139">However, this is consistent with the behavior we have shipped for `[MemberNotNull]` and `[NotNull]` attributes: we warn when a bad value goes in, and we warn again when you return where the variable could contain a bad value.</span></span>

---

```cs
public class C
{
    public string Prop { get; set; }
    public C()
    {
        Prop.ToString(); // Warning: dereference of a possible null reference.
    } // No warning: Prop's flow state was 'promoted' to NotNull after dereference
    
    // This is also consistent with currently shipped behavior:
    [MemberNotNull(nameof(Prop))]
    void M()
    {
        Prop.ToString(); // Warning: dereference of a possible null reference.
    } // No warning: Prop's flow state was 'promoted' to NotNull after dereference
}
```

<span data-ttu-id="a23a9-140">在此方案中，我们永远不会进行初始化 `Prop` ，但我们知道，如果从该构造函数正常返回，则必须以某种方式对其进行初始化。</span><span class="sxs-lookup"><span data-stu-id="a23a9-140">In this scenario we never really initialize `Prop`, but we know that if we return normally from this constructor then Prop must have somehow gotten initialized.</span></span> <span data-ttu-id="a23a9-141">因此，这种警告虽然并不理想，但似乎足以使用户指向其问题所在的位置。</span><span class="sxs-lookup"><span data-stu-id="a23a9-141">Thus this warning, while not ideal, does seem to be adequate for pointing the user toward where their problem lies.</span></span>

<span data-ttu-id="a23a9-142">同样，这与和的发货行为一致 `[MemberNotNull]` `[NotNull]` 。</span><span class="sxs-lookup"><span data-stu-id="a23a9-142">Similarly, this is consistent with the shipped behavior of `[MemberNotNull]` and `[NotNull]`.</span></span>

---

```cs
class C
{
    string Prop1 { get; set; }
    string Prop2 { get; set; }

    public C(bool a)
    {
        if (a)
        {
            Prop1 = "hello";
            return; // warning for Prop2
        }
        else
        {
            return; // warning for Prop1 and for Prop2
        }
    }
}
```

<span data-ttu-id="a23a9-143">此方案说明了每个返回点的警告的独立性，以及单个构造函数中的单个成员的警告方式。</span><span class="sxs-lookup"><span data-stu-id="a23a9-143">This scenario demonstrates the independence of warnings at each return point, as well as the way warnings can multiply for a single member within a single constructor.</span></span> <span data-ttu-id="a23a9-144">这似乎可能会降低警告的冗余性，但这种优化可能会在以后出现，同时改进 `[MemberNotNull]` / `[NotNull]` 分析。</span><span class="sxs-lookup"><span data-stu-id="a23a9-144">It feels like there might be methods of reducing redundancy of the warnings, but this refinement could come later and improve `[MemberNotNull]`/`[NotNull]` analysis at the same time.</span></span> <span data-ttu-id="a23a9-145">对于具有复杂条件逻辑的构造函数，这似乎是一项改进，即 "在此返回状态下，你尚未初始化某些内容"，而不是只是说 "未初始化某些内容"。</span><span class="sxs-lookup"><span data-stu-id="a23a9-145">For constructors with complex conditional logic, it does seem to be an improvement to say "at this return, you haven't initialized something yet" versus simply saying "somewhere in here, you didn't initialize something".</span></span>

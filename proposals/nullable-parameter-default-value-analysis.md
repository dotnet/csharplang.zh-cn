---
ms.openlocfilehash: bb8c3bd0647940240310e4825d902bdd0f3c9097
ms.sourcegitcommit: 29b0f41953ad7b0d4052715e5b53f933cc42e5ee
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/06/2020
ms.locfileid: "94338413"
---
# <a name="nullable-parameter-default-value-analysis"></a><span data-ttu-id="81a1a-101">可以为 null 的参数默认值分析</span><span class="sxs-lookup"><span data-stu-id="81a1a-101">Nullable Parameter Default Value Analysis</span></span>

## <a name="analysis-of-declarations"></a><span data-ttu-id="81a1a-102">声明分析</span><span class="sxs-lookup"><span data-stu-id="81a1a-102">Analysis of declarations</span></span>

<span data-ttu-id="81a1a-103">在方法声明中，编译器需要为与参数的类型不兼容的参数默认值提供警告。</span><span class="sxs-lookup"><span data-stu-id="81a1a-103">In a method declaration it's desirable for the compiler to give warnings for parameter default values which are incompatible with the parameter's type.</span></span>

```cs
void M(string s = null) // warning CS8600: Converting null literal or possible null value to non-nullable type.
{
}
```

<span data-ttu-id="81a1a-104">但是，不受约束的泛型存在一个问题，即错误的值可能会发生，但出于兼容性原因，我们不会发出警告。</span><span class="sxs-lookup"><span data-stu-id="81a1a-104">However, unconstrained generics present a problem where a bad value can go in but we don't warn about it for compat reasons.</span></span> <span data-ttu-id="81a1a-105">因此，我们采用了一种策略来模拟将默认值分配给方法体中的参数，然后在结果状态下联接，并在方法签名中提供所需的警告以及参数的所需初始可为 null 状态。</span><span class="sxs-lookup"><span data-stu-id="81a1a-105">Therefore we adopted a strategy of simulating a assignment of the default value to the parameter in the method body, then joining in the resulting state, giving us the desired warnings in the method signature as well as the desired initial nullable state for the parameter.</span></span>

```cs
class C<T>
{
    void M0(T t) { }

    void M1(T t = default) // no warning here
    {
        M0(t); // warning CS8604: Possible null reference argument for parameter 't' in 'void C<T>.M0(T t)'.
    }
}
```

<span data-ttu-id="81a1a-106">在所有情况下，很难正确更新参数的初始状态。</span><span class="sxs-lookup"><span data-stu-id="81a1a-106">It's difficult to update the parameter initial state appropriately in all scenarios.</span></span> <span data-ttu-id="81a1a-107">下面是此方法的一些情况：</span><span class="sxs-lookup"><span data-stu-id="81a1a-107">Here are some scenarios where the approach falls over:</span></span>

### <a name="overriding-methods-with-optional-parameters"></a>[<span data-ttu-id="81a1a-108">用可选参数重写方法</span><span class="sxs-lookup"><span data-stu-id="81a1a-108">Overriding methods with optional parameters</span></span>](https://github.com/dotnet/roslyn/issues/48848)
```cs
Base<string> obj = new Override();
obj.M(); // throws NRE at runtime

public class Base<T>
{
    public virtual void M(T t = default) { } // no warning
}

public class Override : Base<string>
{
    public override void M(string s)
    {
        s.ToString(); // no warning today, but something in this sample ought to warn. :)
    }
}
```
<span data-ttu-id="81a1a-109">在上面的示例中，我们可以调用方法 `Base<string>.M()` ，并将调度到 `Override.M()` 。</span><span class="sxs-lookup"><span data-stu-id="81a1a-109">In the above sample we may call the method `Base<string>.M()` and dispatch to `Override.M()`.</span></span> <span data-ttu-id="81a1a-110">我们需要考虑到调用方通过基准隐式地将 `null` 作为参数提供的可能性 `s` ，但目前不会这样做。</span><span class="sxs-lookup"><span data-stu-id="81a1a-110">We need to account for the possibility that the caller implicitly provided `null` as an argument for `s` via the base, but currently we do not do so.</span></span>

---

### <a name="lambda-conversion-to-delegates-which-have-optional-parameters"></a>[<span data-ttu-id="81a1a-111">转换为具有可选参数的委托的 Lambda</span><span class="sxs-lookup"><span data-stu-id="81a1a-111">Lambda conversion to delegates which have optional parameters</span></span>](https://github.com/dotnet/roslyn/issues/48844)
```cs
public delegate void Del<T>(T t = default);

public class C
{
    public static void Main()
    {
        Del<string> del = str => str.ToString(); // expected warning, but didn't get one
        del(); // throws NRE at runtime
    }
}
```
<span data-ttu-id="81a1a-112">在上面的示例中，由于默认值的原因，转换为类型的 lambda `Del<string>` 将具有 `MaybeNull` 其参数的初始状态。</span><span class="sxs-lookup"><span data-stu-id="81a1a-112">In the above sample we expect that a lambda converted to the type `Del<string>` will have a `MaybeNull` initial state for its parameter because of the default value.</span></span> <span data-ttu-id="81a1a-113">当前无法正确处理此事例。</span><span class="sxs-lookup"><span data-stu-id="81a1a-113">Currently we don't handle this case properly.</span></span>

---

### <a name="abstract-methods-and-delegate-declarations-which-have-optional-parameters"></a>[<span data-ttu-id="81a1a-114">具有可选参数的抽象方法和委托声明</span><span class="sxs-lookup"><span data-stu-id="81a1a-114">Abstract methods and delegate declarations which have optional parameters</span></span>](https://github.com/dotnet/roslyn/issues/48847)
```cs
public abstract class C
{
    public abstract void M1(string s = null); // expected warning, but didn't get one
}

interface I
{
    void M1(string s = null); // expected warning, but didn't get one
}

public delegate void Del1(string s = null); // expected warning, but didn't get one
```
<span data-ttu-id="81a1a-115">在上面的示例中，我们需要对这些参数的警告，这些参数不会直接与任何方法实现关联。</span><span class="sxs-lookup"><span data-stu-id="81a1a-115">In the above sample, we want warnings on these parameters which aren't directly associated with any method implementation.</span></span> <span data-ttu-id="81a1a-116">但是，由于这些参数列表没有任何要进行流分析的主体的方法，我们永远不会通过 NullableWalker 中的 EnterParameters 方法来模拟分配并产生警告。</span><span class="sxs-lookup"><span data-stu-id="81a1a-116">However, since these parameter lists don't have any methods with bodies that we want to flow analyze, we never hit the EnterParameters method in NullableWalker which simulates the assignments and produces the warnings.</span></span>

---

### <a name="indexers-with-get-and-set-accessors"></a><span data-ttu-id="81a1a-117">具有 get 和 set 访问器的索引器</span><span class="sxs-lookup"><span data-stu-id="81a1a-117">Indexers with get and set accessors</span></span>
```cs
public class C
{
    public int this[int i, string s = null] // no warning here
    {
        get // entire accessor syntax has warning CS8600: Converting null literal or possible null value to non-nullable type.
        {
            return i;
        }

        set // entire accessor syntax has warning CS8600: Converting null literal or possible null value to non-nullable type.
        {
        }
    }
}
```
<span data-ttu-id="81a1a-118">最后一个示例只是一个令人讨厌的示例。</span><span class="sxs-lookup"><span data-stu-id="81a1a-118">This last sample is just an annoyance.</span></span> <span data-ttu-id="81a1a-119">在这里，我们为每个访问器合成一个不同的参数符号，其位置是整个取值函数语法。</span><span class="sxs-lookup"><span data-stu-id="81a1a-119">Here we synthesize a distinct parameter symbol for each accessor, whose location is the entire accessor syntax.</span></span> <span data-ttu-id="81a1a-120">我们模拟每个访问器中的默认值分配，并对参数发出警告，最终会提供重复的警告，而不会真正显示出现问题的位置。</span><span class="sxs-lookup"><span data-stu-id="81a1a-120">We simulate the default value assignment in each accessor and give a warning on the parameter, which ends up giving duplicate warnings that don't really show where the problem is.</span></span>

## <a name="suggested-change-to-declaration-analysis"></a><span data-ttu-id="81a1a-121">声明分析的建议更改</span><span class="sxs-lookup"><span data-stu-id="81a1a-121">Suggested change to declaration analysis</span></span>

<span data-ttu-id="81a1a-122">**我们不应根据默认值更新流分析中的参数的初始状态。**</span><span class="sxs-lookup"><span data-stu-id="81a1a-122">**We shouldn't update the parameter's initial state in flow analysis based on the default value.**</span></span> <span data-ttu-id="81a1a-123">它在重写、委托转换等方面产生了奇怪的复杂性和丢失的警告，这对不值得考虑，如果我们确实要考虑到这种情况，则会导致用户混淆。</span><span class="sxs-lookup"><span data-stu-id="81a1a-123">It introduces strange complexity and missing warnings around overriding, delegate conversions, etc. that is not worth accounting for, and would cause user confusion if we did account for them.</span></span> <span data-ttu-id="81a1a-124">重新从上述替代示例：</span><span class="sxs-lookup"><span data-stu-id="81a1a-124">Revisiting the overriding sample from above:</span></span>

```cs
public class Base<T>
{
    public virtual void M(T t = default) { } // let's start warning here
}

public class Override : Base<string>
{
    public override void M(string s)
    {
        s.ToString(); // let's not warn here
    }
}
```
<span data-ttu-id="81a1a-125">用户可能会发现关于 `s.ToString()` 混乱和无用的警告，这里所述的问题是中的类型和默认值不兼容 `T t = default` ，而这就是用户的修复需要的地方。</span><span class="sxs-lookup"><span data-stu-id="81a1a-125">As a user you'd probably find a warning on `s.ToString()` confusing and useless--the thing that's broken here is the incompatibility of the type and default value in `T t = default`, and that's where user's fix needs to go.</span></span>

<span data-ttu-id="81a1a-126">**相反，我们应强制默认值与所有方案（包括不约束的泛型）中的参数兼容。**</span><span class="sxs-lookup"><span data-stu-id="81a1a-126">**Instead, we should enforce that the default value is compatible with the parameter in all scenarios, including unconstrained generics.**</span></span> <span data-ttu-id="81a1a-127">我确信这就是我们在 VS 16.9 中如何实现这一点 `/langversion:9` 。</span><span class="sxs-lookup"><span data-stu-id="81a1a-127">I am certain that this is how we should do it in `/langversion:9` in VS 16.9.</span></span> <span data-ttu-id="81a1a-128">我还相信我们应该在 `/langversion:8` "bug 修复" 涵盖下执行此操作。</span><span class="sxs-lookup"><span data-stu-id="81a1a-128">I also believe that we should do this in `/langversion:8` under the "bug fix" umbrella.</span></span> <span data-ttu-id="81a1a-129">`[AllowNull]` 可应用于不受约束的泛型参数以允许 `default` 作为默认值，因此 c # 8 用户不会被阻止。</span><span class="sxs-lookup"><span data-stu-id="81a1a-129">`[AllowNull]` can be applied to unconstrained generic parameters to allow `default` as a default value, so C# 8 users are not blocked.</span></span> <span data-ttu-id="81a1a-130">我可以根据 `/langversion:8` 不同的影响，对其进行相应的操作。</span><span class="sxs-lookup"><span data-stu-id="81a1a-130">I could be convinced otherwise about doing it in `/langversion:8` depending on the impact.</span></span>

<span data-ttu-id="81a1a-131">至于实现策略：我们应在 SourceComplexParameterSymbol 中执行此操作，同时绑定参数的默认值。</span><span class="sxs-lookup"><span data-stu-id="81a1a-131">As far as implementation strategy: we should just do this in SourceComplexParameterSymbol at the same time we bind the parameter's default value.</span></span> <span data-ttu-id="81a1a-132">我们可以通过创建 NullableWalker 并对其最终状态被丢弃的默认值进行 "最小分析"，来确保有足够的一致性以及合理的抑制处理方式。</span><span class="sxs-lookup"><span data-stu-id="81a1a-132">We can ensure sufficient amount of consistency, as well as reasonable handling of suppression, perhaps by creating a NullableWalker and doing a "mini-analysis" of the assignment of the default value whose final state is discarded.</span></span>


---
ms.openlocfilehash: bb8c3bd0647940240310e4825d902bdd0f3c9097
ms.sourcegitcommit: 29b0f41953ad7b0d4052715e5b53f933cc42e5ee
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/06/2020
ms.locfileid: "94338413"
---
# <a name="nullable-parameter-default-value-analysis"></a>可以为 null 的参数默认值分析

## <a name="analysis-of-declarations"></a>声明分析

在方法声明中，编译器需要为与参数的类型不兼容的参数默认值提供警告。

```cs
void M(string s = null) // warning CS8600: Converting null literal or possible null value to non-nullable type.
{
}
```

但是，不受约束的泛型存在一个问题，即错误的值可能会发生，但出于兼容性原因，我们不会发出警告。 因此，我们采用了一种策略来模拟将默认值分配给方法体中的参数，然后在结果状态下联接，并在方法签名中提供所需的警告以及参数的所需初始可为 null 状态。

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

在所有情况下，很难正确更新参数的初始状态。 下面是此方法的一些情况：

### <a name="overriding-methods-with-optional-parameters"></a>[用可选参数重写方法](https://github.com/dotnet/roslyn/issues/48848)
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
在上面的示例中，我们可以调用方法 `Base<string>.M()` ，并将调度到 `Override.M()` 。 我们需要考虑到调用方通过基准隐式地将 `null` 作为参数提供的可能性 `s` ，但目前不会这样做。

---

### <a name="lambda-conversion-to-delegates-which-have-optional-parameters"></a>[转换为具有可选参数的委托的 Lambda](https://github.com/dotnet/roslyn/issues/48844)
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
在上面的示例中，由于默认值的原因，转换为类型的 lambda `Del<string>` 将具有 `MaybeNull` 其参数的初始状态。 当前无法正确处理此事例。

---

### <a name="abstract-methods-and-delegate-declarations-which-have-optional-parameters"></a>[具有可选参数的抽象方法和委托声明](https://github.com/dotnet/roslyn/issues/48847)
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
在上面的示例中，我们需要对这些参数的警告，这些参数不会直接与任何方法实现关联。 但是，由于这些参数列表没有任何要进行流分析的主体的方法，我们永远不会通过 NullableWalker 中的 EnterParameters 方法来模拟分配并产生警告。

---

### <a name="indexers-with-get-and-set-accessors"></a>具有 get 和 set 访问器的索引器
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
最后一个示例只是一个令人讨厌的示例。 在这里，我们为每个访问器合成一个不同的参数符号，其位置是整个取值函数语法。 我们模拟每个访问器中的默认值分配，并对参数发出警告，最终会提供重复的警告，而不会真正显示出现问题的位置。

## <a name="suggested-change-to-declaration-analysis"></a>声明分析的建议更改

**我们不应根据默认值更新流分析中的参数的初始状态。** 它在重写、委托转换等方面产生了奇怪的复杂性和丢失的警告，这对不值得考虑，如果我们确实要考虑到这种情况，则会导致用户混淆。 重新从上述替代示例：

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
用户可能会发现关于 `s.ToString()` 混乱和无用的警告，这里所述的问题是中的类型和默认值不兼容 `T t = default` ，而这就是用户的修复需要的地方。

**相反，我们应强制默认值与所有方案（包括不约束的泛型）中的参数兼容。** 我确信这就是我们在 VS 16.9 中如何实现这一点 `/langversion:9` 。 我还相信我们应该在 `/langversion:8` "bug 修复" 涵盖下执行此操作。 `[AllowNull]` 可应用于不受约束的泛型参数以允许 `default` 作为默认值，因此 c # 8 用户不会被阻止。 我可以根据 `/langversion:8` 不同的影响，对其进行相应的操作。

至于实现策略：我们应在 SourceComplexParameterSymbol 中执行此操作，同时绑定参数的默认值。 我们可以通过创建 NullableWalker 并对其最终状态被丢弃的默认值进行 "最小分析"，来确保有足够的一致性以及合理的抑制处理方式。


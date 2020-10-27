---
ms.openlocfilehash: 18f4447891d10072f4955478c4da7baa3ce9cc93
ms.sourcegitcommit: 9807f9b2e2105255c0a658f3afc908769bb31339
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/26/2020
ms.locfileid: "92550685"
---
# <a name="unconstrained-type-parameter-annotations"></a>不受约束的类型参数批注

## <a name="summary"></a>总结

允许不受值类型或引用类型约束的类型参数的可以为 null 的批注： `T?` 。
```C#
static T? FirstOrDefault<T>(this IEnumerable<T> collection) { ... }
```

## <a name="-annotation"></a>`?` 注释

在 c # 8 中， `?` 批注仅适用于显式约束为值类型或引用类型的类型参数。
在 c # 9 中， `?` 批注可应用于任何类型参数，而不考虑约束。

除非将类型形参显式约束为值类型，否则只能在上下文中应用注释 `#nullable enable` 。

如果类型参数 `T` 替换为引用类型，则 `T?` 表示该引用类型的可以为 null 的实例。
```C#
var s1 = new string[0].FirstOrDefault();  // string? s1
var s2 = new string?[0].FirstOrDefault(); // string? s2
```

如果 `T` 用值类型替换，则 `T?` 表示的实例 `T` 。
```C#
var i1 = new int[0].FirstOrDefault();  // int i1
var i2 = new int?[0].FirstOrDefault(); // int? i2
```

如果 `T` 使用批注类型替换 `U?` ，则 `T?` 表示批注的类型 `U?` 而不是 `U??` 。
```C#
var u1 = new U[0].FirstOrDefault();  // U? u1
var u2 = new U?[0].FirstOrDefault(); // U? u2
```

如果 `T` 将替换为类型 `U` ，则 `T?` 表示 `U?` ，即使在上下文中也是如此 `#nullable disable` 。
```C#
#nullable disable
var u3 = new U[0].FirstOrDefault();  // U? u3
```

对于返回值， `T?` 等效于 `[MaybeNull]T` ; 对于参数值， `T?` 等效于 `[AllowNull]T` 。
在使用 c # 8 编译的程序集中重写或实现接口时，等效性非常重要。
```C#
public abstract class A
{
    [return: MaybeNull] public abstract T F1<T>();
    public abstract void F2<T>([AllowNull] T t);
}

public class B : A
{
    public override T? F1<T>() where T : default { ... }       // matches A.F1<T>()
    public override void F2<T>(T? t) where T : default { ... } // matches A.F2<T>()
}
```

## <a name="default-constraint"></a>`default` constraint

为了与重写的和显式实现的泛型方法的现有代码兼容， `T?` 将被重写或显式实现的方法视为， `Nullable<T>` 其中 `T` 是值类型。

若要允许将类型参数批注用于引用类型，c # 8 允许 `where T : class` `where T : struct` 对已重写或显式实现的方法使用显式和约束。
```C#
class A1
{
    public virtual void F1<T>(T? t) where T : struct { }
    public virtual void F1<T>(T? t) where T : class { }
}

class B1 : A1
{
    public override void F1<T>(T? t) /*where T : struct*/ { }
    public override void F1<T>(T? t) where T : class { }
}
```

若要允许不受引用类型或值类型约束的类型参数的批注，c # 9 允许新的 `where T : default` 约束。
```C#
class A2
{
    public virtual void F2<T>(T? t) where T : struct { }
    public virtual void F2<T>(T? t) { }
}

class B2 : A2
{
    public override void F2<T>(T? t) /*where T : struct*/ { }
    public override void F2<T>(T? t) where T : default { }
}
```

如果对 `default` 方法重写或显式实现使用以外的约束，则是错误的。
`default`如果重写或接口方法中的相应类型参数被约束为引用类型或值类型，则使用约束是错误的。

## <a name="design-meetings"></a>设计会议

- https://github.com/dotnet/csharplang/blob/master/meetings/2019/LDM-2019-11-25.md
- https://github.com/dotnet/csharplang/blob/master/meetings/2020/LDM-2020-06-17.md#t

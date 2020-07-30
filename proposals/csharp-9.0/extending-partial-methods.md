---
ms.openlocfilehash: c4fc0bd978e02bbd4da6ed77fe1d2ac3533b2c12
ms.sourcegitcommit: a88d56e3131d7a94c65e637c276379541a3cd491
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/30/2020
ms.locfileid: "87434478"
---
<a name="extending-partial-methods"></a>扩展分部方法
=====

## <a name="summary"></a>总结
此建议旨在消除对 c # 中的方法的签名的所有限制 `partial` 。 目标是扩展一组方案，在这些方案中，这些方法可与源生成器一起使用，并且是更通用的 c # 方法声明形式。

另请参阅[原始分部方法规范](../../spec/classes.md#partial-methods)。

## <a name="motivation"></a>动机
对于将方法拆分为声明和定义/实现的开发人员，c # 提供了有限的支持。 

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

方法的一个行为 `partial` 是：如果定义不存在，则该语言只会擦除对方法的任何调用 `partial` 。 实质上，它的行为类似于调用方法，在 `[Conditional]` 此方法中，条件的计算结果为 false。 

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

此功能的原始动机是以设计器生成的代码的形式生成的源。 用户经常编辑生成的代码，因为他们希望挂钩生成的代码的某个方面。 初始化组件后，最值得注意的是 Windows 窗体启动过程的一部分。

编辑生成的代码很容易出错，因为任何导致设计器重新生成代码的操作都会导致清除用户编辑。 `partial`方法功能减轻此张力，因为它允许设计器以方法的形式发出挂钩 `partial` 。 

设计器可以发出挂钩 `partial void OnComponentInit()` ，如，开发人员可以为其定义声明或不定义它们。 在这两种情况下，尽管生成的代码会进行编译，并且对进程感兴趣的开发人员可以根据需要挂钩。 

这意味着分部方法具有几个限制：

1. 必须具有 `void` 返回类型。
1. 不能具有 `out` 参数。 
1. 不能具有任何可访问性（隐式 `private` ）。

存在这些限制的原因是，在清除调用站点时，语言必须能够发出代码。 `private`由于无法在程序集元数据中公开该成员，因此可将其删除。 这些限制还用于限制 `partial` 可应用方法的方案集。

此处的建议是删除有关方法的所有现有限制 `partial` 。 实质上，使它们具有 `out` 非 void 返回类型或任何类型的可访问性。 这样 `partial` 的声明将具有定义必须存在的附加要求。 这意味着语言不必考虑擦除调用站点的影响。 

这会扩展方法可参与的一组生成器方案， `partial` 因此与源生成器功能非常类似。 例如，可使用以下模式定义 regex：

```cs
[RegexGenerated("(dog|cat|fish)")]
partial bool IsPetMatch(string input);
```

这为开发人员提供了一种简单的声明性方法来选择生成器，并为生成器提供一组非常简单的声明，用于在源代码中查找其生成的输出。 

与生成器与以下代码片段挂钩的难度进行比较。 

```cs
var regex = new RegularExpression("(dog|cat|fish)");
if (regex.IsMatch(someInput))
{

}
```

假设编译器不允许生成器修改代码挂钩，这种模式对于生成器而言可能会很大。 它们需要使用实现中的反射 `IsMatch` ，或要求用户将其调用站点更改为新方法，并重构 regex 以将字符串文本作为参数传递。 这种方法非常杂乱。

## <a name="detailed-design"></a>详细设计
语言将更改为允许 `partial` 使用显式可访问性修饰符对方法进行批注。 这意味着可以将它们标记为 `private` 、 `public` 等。 

如果 `partial` 方法具有显式可访问性修饰符，则该语言将要求声明具有匹配的定义，即使可访问性是 `private` ：

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

此外，此语言将删除对 `partial` 具有显式可访问性的方法所显示的所有限制。 此类声明可以包含非 void 返回类型、 `out` 参数、 `extern` 修饰符等 .。。这些签名将具有 c # 语言的完整表现力。

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

这显式允许 `partial` 方法参与 `overrides` 和 `interface` 实现：

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

编译器将更改它在 `partial` 方法包含非法元素时发出的错误，而本质上说：

> 不能 `ref` 对 `partial` 缺少显式辅助功能的方法使用 

当使用此功能时，这将有助于指导开发人员正确的方向。

限制：
- `partial`具有显式辅助功能的声明必须具有定义
- `partial`声明和定义签名必须在所有方法和参数修饰符上都匹配。 唯一可能不同的方面是参数名称和属性列表（这不是新的，而是方法的现有要求 `partial` ）。

## <a name="questions"></a>问题

### <a name="partial-on-all-members"></a>部分在所有成员上
假设我们要扩展 `partial` 更友好的源生成器，还应将其扩展为适用于所有类成员吗？ 例如，我们应该能够声明 `partial` 构造函数、运算符等 .。。

**解决方法**思路非常不错，但在 c # 9 计划中，我们正努力避免不必要的功能蔓延。 想要解决将此功能扩展到使用现代源生成器的即时问题。 

`partial`C # 10 版本将对扩展以支持其他成员。 可能会考虑此扩展。

### <a name="use-abstract-instead-of-partial"></a>使用抽象而不是部分
此建议的关键实质上是确保声明具有相应的定义/实现。 假设我们要使用， `abstract` 因为它已是一个可强制开发人员考虑实现的语言关键字？

**解决方法**这是一个关于此操作的正常讨论，但最终决定了它。
是的，需要熟悉这些要求，但概念却大相径庭。
如果不这样做，开发人员可以轻松地让开发人员相信它们正在创建虚拟槽。

---
ms.openlocfilehash: 745db71694e948808ba8665dae32c146baa40c80
ms.sourcegitcommit: a69376ba67c6344b1f939f3d87cdb764c8179687
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/14/2020
ms.locfileid: "88216846"
---
# <a name="nullable-constructor-analysis"></a>可为 null 的构造函数分析

此建议旨在使用可以为 null 的构造函数分析来解决几个未解决的问题。

## <a name="how-it-works-currently"></a>当前的工作方式

构造函数的可为 null 分析实质上是运行明确的赋值过程，并在构造函数不初始化不可为 null 的引用类型成员的情况下报告警告 (例如：所有代码路径中的字段、自动属性或类似字段的事件) 。 否则，将构造函数视为用于分析的普通方法。 此方法附带了几个问题。

首先，成员的初始流状态不准确：

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

另一种情况是断言/"throw" 语句不会阻止字段初始化警告：

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

## <a name="an-alternative-approach"></a>替代方法

我们可以通过使用类似于分析的方法来解决这种情况 `[MemberNotNull]` ，其中，字段标记为 "可能-空"，如果在字段仍处于 "可能为 null" 状态时退出方法，则会发出警告。 为此，我们可以引入以下规则：

**引用类型上没有初始值设定项***或*的构造函数  
**具有 `: base(...)` 初始值设定项的引用类型上的构造函数** 具有由确定的初始可为 null 的流状态：
- 将基类型成员初始化为其声明的状态，因为我们希望基构造函数初始化基成员。
- 然后，将类型中的所有适用成员初始化为指定的状态，方法是将 `default` 文本分配给成员。 如果是以下情况，则成员适用：
  - 它没有在意为 null 性， *并且*
  - 它是实例，要分析的构造函数是实例，或者成员是静态的，并且正在分析的构造函数是静态的。
  - 我们期望 `default` 文本生成 `NotNull` 不可为 null 的值类型、 `MaybeNull` 引用类型或可以为 null 的值类型的状态，以及不受 `MaybeDefault` 约束的泛型的状态。
- 然后访问相应成员的初始值设定项，相应地更新流状态。
  - 这允许使用字段/属性初始值设定项，并在构造函数中初始化其他不可以为 null 的引用成员。
  - 预期是编译器将按一次对初始值设定项进行流式分析和报告诊断，然后将生成的流状态复制为没有初始值设定项的每个构造函数的初始状态 `: this(...)` 。

**对于具有 `: this()` 引用默认值类型构造函数的初始值设定项的值类型，其构造函数** 具有给定的初始流状态，该状态通过向成员分配文本来将所有适用的成员初始化为给定状态 `default` 。

**具有 `: this(...)` 初始值设定项或的引用类型的构造函数** *or*  
**值类型上的构造函数，其初始值设定项未引用默认值类型构造函数***或*  
**没有初始值设定项的值类型上的构造函数** 具有与普通方法相同的初始可以为 null 的流状态。  
成员具有基于已声明的注释和可以为 null 的属性的初始状态。 对于值类型，在没有初始值设定项的情况下，我们需要明确的赋值分析来提供所需的安全级别 `: this(...)` 。 这与现有的行为相同。

**在构造函数中的每个显式或隐式 "return" 中**，我们将为其流状态与其批注和为空性属性不兼容的每个成员提供警告。 此操作的合理代理是：如果在返回点将成员分配给其自身，则会产生一个为空性警告，然后在返回点生成一个为空性警告。

在某些情况下，这可能会导致相同成员出现大量警告。 作为 "延伸目标"，我认为我们应该考虑以下 "优化"：
- 如果某个成员在适用的构造函数中的所有返回点上都具有不兼容的流状态，我们会对构造函数的名称语法而不是每个返回点发出警告。
- 如果在所有适用的构造函数中，成员的所有返回点都具有不兼容的流状态，我们会在成员声明自身上发出警告。

## <a name="consequences-of-this-approach"></a>此方法的后果

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

上述方案产生了与同一属性相对应的多个警告。 如果方法中存在更多的返回点，则可能会根据成员处于错误流状态的返回点的数目，无限期地生成很多警告。

但是，这与我们已为和属性提供的行为 `[MemberNotNull]` 一致 `[NotNull]` ：当出现错误值时，我们会发出警告，当你返回变量可能包含错误值的位置时，我们将再次警告。

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

在此方案中，我们永远不会进行初始化 `Prop` ，但我们知道，如果从该构造函数正常返回，则必须以某种方式对其进行初始化。 因此，这种警告虽然并不理想，但似乎足以使用户指向其问题所在的位置。

同样，这与和的发货行为一致 `[MemberNotNull]` `[NotNull]` 。

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

此方案说明了每个返回点的警告的独立性，以及单个构造函数中的单个成员的警告方式。 这似乎可能会降低警告的冗余性，但这种优化可能会在以后出现，同时改进 `[MemberNotNull]` / `[NotNull]` 分析。 对于具有复杂条件逻辑的构造函数，这似乎是一项改进，即 "在此返回状态下，你尚未初始化某些内容"，而不是只是说 "未初始化某些内容"。

---
ms.openlocfilehash: 92b4be885dc744ca0a0c6d0645ce9f6d3427ddd9
ms.sourcegitcommit: 3a6c79012a8c53fe84330c09080105ac177ff93d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/06/2020
ms.locfileid: "89501648"
---
# <a name="simplified-null-argument-checking"></a>简化的 Null 参数检查

## <a name="summary"></a>摘要
此建议提供简化的语法，用于验证方法自变量不会 `null` `ArgumentNullException` 正确地进行。

## <a name="motivation"></a>动机
设计可以为 null 的引用类型的工作导致我们检查参数验证所需的代码 `null` 。 假设 NRT 不会影响代码执行开发人员仍必须 `if (arg is null) throw` 在完全干净的项目中添加样板板代码 `null` 。 这样，我们就需要探索用语言进行参数验证的最小语法 `null` 。 

尽管此 `null` 参数验证语法应经常与 NRT 配对，但建议完全独立于它。 语法可以独立于 `#nullable` 指令使用。

## <a name="detailed-design"></a>详细设计 

### <a name="null-validation-parameter-syntax"></a>空验证参数语法
感叹号运算符 `!` 可位于参数列表中的参数名之后，这将导致 c # 编译器发出 `null` 该参数的标准检查代码。 这称为 `null` 验证参数语法。 例如：

``` csharp
void M(string name!) {
    ...
}
```

将转换为：

``` csharp
void M(string name) {
    if (name is null) {
        throw new ArgumentNullException(nameof(name));
    }
    ...
}
```

生成的 `null` 检查将在任何开发人员在方法中编写代码之前发生。 如果多个参数包含 `!` 运算符，则检查将按声明参数的顺序进行。

``` csharp
void M(string p1, string p2) {
    if (p1 is null) {
        throw new ArgumentNullException(nameof(p1));
    }
    if (p2 is null) {
        throw new ArgumentNullException(nameof(p2));
    }
    ...
}
```

检查将专门用于引用相等性 `null` ，它不会调用 `==` 或任何用户定义的运算符。 这也意味着， `!` 只能将运算符添加到可对其类型进行测试是否相等的参数 `null` 。 这意味着它不能用于其类型已知为值类型的参数。

``` csharp
// Error: Cannot use ! on parameters who types derive from System.ValueType
void G<T>(T arg!) where T : struct {

}
```

对于构造函数， `null` 验证将在构造函数中的任何其他代码之前发生。 其中包括： 

- 与或链接到其他构造函数 `this``base` 
- 隐式出现在构造函数中的字段初始值设定项

例如：

``` csharp
class C {
    string field = GetString();
    C(string name!): this(name) {
        ...
    }
}
```

将大致转换为以下内容：

``` csharp
class C {
    C(string name)
        if (name is null) {
            throw new ArgumentNullException(nameof(name));
        }
        field = GetString();
        :this(name);
        ...
}
```

注意：这不是合法的 c # 代码，而只是实现实现的近似值。 

`null`验证参数语法在 lambda 参数列表上也是有效的。 即使在缺少括号的单个参数语法中，这也是有效的。

``` csharp
void G() {
    // An identity lambda which throws on a null input
    Func<string, string> s = x! => x;
}
```

语法在迭代器方法的参数上也是有效的。 与迭代器中的其他代码不同，将在 `null` 调用迭代器方法时，而不是在遍历基础枚举器时进行验证。 这适用于传统的或 `async` 迭代器。

``` csharp
class Iterators {
    IEnumerable<char> GetCharacters(string s!) {
        foreach (var c in s) {
            yield return c;
        }
    }

    void Use() {
        // The invocation of GetCharacters will throw
        IEnumerable<char> e = GetCharacters(null);
    }
}
```

`!`运算符只能用于具有关联方法体的参数列表。 这意味着不能在 `abstract` 方法、 `interface` `delegate` 或 `partial` 方法定义中使用它。

### <a name="extending-is-null"></a>扩展为 null
表达式有效的类型 `is null` 将进行扩展以包含不受约束的类型参数。 这将允许它填写 `null` 检查是否有效的所有类型的目的 `null` 。 具体来说，就是不能知道其值类型的类型。 例如，如果类型参数的约束 `struct` 不能与此语法一起使用。

``` csharp
void NullCheck<T1, T2>(T1 p1, T2 p2) where T2 : struct {
    // Okay: T1 could be a class or struct here.
    if (p1 is null) {
        ...
    }

    // Error 
    if (p2 is null) { 
        ...
    }
}
```

类型参数上的行为与今天的行为 `is null` 相同 `== null` 。 在将类型参数实例化为值类型的情况下，代码将计算为 `false` 。 对于引用类型，代码将进行适当的 `is null` 检查。

### <a name="intersection-with-nullable-reference-types"></a>具有可以为 Null 的引用类型的交集
所有 `!` 应用了运算符名称的参数均以可为 null 的状态开始 `null` 。 即使参数本身的类型是可能的，也是如此 `null` 。 使用显式可以为 null 的类型（例如 `string?` ）或使用不受约束的类型参数时，可能会出现这种情况。 

当参数 `!` 语法与参数上的可为 null 的类型组合时，编译器将发出警告：

``` csharp
void WarnCase<T>(
    string? name!, // Warning: combining explicit null checking with a nullable type
    T value1 // Okay
)
```

## <a name="open-issues"></a>未结的问题
无

## <a name="considerations"></a>注意事项

### <a name="constructors"></a>构造函数
构造函数的代码生成意味着从标准验证开始移动时有一个小但可观察的行为更改， `null` `null` () 的验证参数语法 `!` 。 " `null` 检查标准验证" 在字段初始值设定项和 "任何" 或 "调用" 之后发生 `base` `this` 。 这意味着开发人员不一定要将100% 的 `null` 验证迁移到新的语法。 构造函数至少需要一些检查。

讨论完毕后，这种情况很不可能导致任何重大的采用问题。 在 `null` 构造函数中的任何逻辑之前，检查运行的逻辑更多。 如果发现了重要的兼容性问题，则可以重新访问。

### <a name="warning-when-mixing--and-"></a>混合时出现警告？ 与!
在将 `!` 语法应用于显式键入为可以为 null 的类型的参数时，是否应发出警告，这是一个很长的讨论。 在表面上，开发人员似乎是过程声明，但在某些情况下，类型层次结构可能会强制开发人员进入这种情况。 

请考虑在一系列程序集中使用以下类层次结构 (假设所有编译都已在启用检查) 的情况下进行编译 `null` ：

``` csharp
// Assembly1
abstract class C1 {
    protected abstract void M(object o); 
}

// Assembly2
abstract class C2 : C1 {

}

// Assembly3
abstract class C3 : C2 { 
    protected override void M(object o!) {
        ...
    }
}
```

此处的作者 `C3` 决定向参数添加 `null` 验证 `o` 。 这完全与如何使用该功能有关。

现在，假设 Assembly2 的作者决定添加以下替代：

``` csharp
// Assembly2
abstract class C2 : C1 {
   protected override void M(object? o) { 
       ...
   }
}
```

这是可空引用类型允许的，因为它是合法的，使得协定对于输入位置更灵活。 通常，NRT 功能允许在参数/返回为空性上具有合理的 co/逆变。 不过，该语言根据最具体的替代（而不是原始声明）进行 co/逆变检查。 这意味着 Assembly3 的作者将收到一条有关不匹配的类型的警告 `o` ，需要将签名更改为以下内容，以消除这种情况： 

``` csharp
// Assembly3
abstract class C3 : C2 { 
    protected override void M(object? o!) {
        ...
    }
}
```

此时，Assembly3 的作者有几个选择：

- 它们可以接受/禁止显示有关 `object?` 和 `object` 不匹配的警告。
- 它们可以接受/禁止显示有关 `object?` 和 `!` 不匹配的警告。
- 它们只需删除 `null` 验证检查 (删除 `!` 并执行显式检查) 

这是一个真实的方案，但现在这一理念是继续使用警告。 如果发现警告的发生频率比我们预测的更频繁，则可以在以后 (相反) 。

### <a name="implicit-property-setter-arguments"></a>隐式属性 setter 参数
`value`参数的参数是隐式的，并且不会出现在任何参数列表中。 这意味着它不能是此功能的目标。 可以扩展属性 setter 语法，使其包含参数列表，以允许 `!` 应用该运算符。 但这种方法会使 `null` 验证更简单。 这样，隐式 `value` 参数就不会使用此功能。

## <a name="future-considerations"></a>未来的注意事项
无

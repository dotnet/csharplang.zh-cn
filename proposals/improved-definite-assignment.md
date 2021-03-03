---
ms.openlocfilehash: 44980f4dadfb80a5ef1fccb09ef6490f80a12c5c
ms.sourcegitcommit: 3f8f57e294e2952af19a5231e6b9c7ff85d0ed56
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/02/2021
ms.locfileid: "101751635"
---
# <a name="improved-definite-assignment-analysis"></a>改进的明确赋值分析

## <a name="summary"></a>总结
指定的[明确赋值分析](../spec/variables.md#definite-assignment)具有几个缺口，导致用户无法使用。 具体而言，涉及与布尔常量、条件性访问和 null 合并的比较。

## <a name="related-discussions-and-issues"></a>相关讨论和问题
此建议的 csharplang 讨论： https://github.com/dotnet/csharplang/discussions/4240

可能是通过此查询或类似 (查询（例如，搜索 "明确分配" 而不是 "CS0165"）或在 csharplang) 中搜索来查找用户报表。
https://github.com/dotnet/roslyn/issues?q=is%3Aclosed+is%3Aissue+label%3A%22Resolution-By+Design%22+cs0165

我在下面的方案中包含了相关的问题，让你了解每个方案的相对影响。

## <a name="scenarios"></a>方案

作为参考，让我们从一个众所周知的 "满意情况" 开始，该示例在明确赋值和可以为 null 的情况下工作。
```cs
#nullable enable

C c = new C();
if (c != null && c.M(out object obj0))
{
    obj0.ToString(); // ok
}

public class C
{
    public bool M(out object obj)
    {
        obj = new object();
        return true;
    }
}
```

### <a name="comparison-to-bool-constant"></a>与 bool 常量的比较
- https://github.com/dotnet/csharplang/discussions/801
- https://github.com/dotnet/roslyn/issues/45582
  - 链接到其他用户受此影响的其他问题。
```cs
if ((c != null && c.M(out object obj1)) == true)
{
    obj1.ToString(); // undesired error
}

if ((c != null && c.M(out object obj2)) is true)
{
    obj2.ToString(); // undesired error
}
```

### <a name="comparison-between-a-conditional-access-and-a-constant-value"></a>条件访问和常数值之间的比较

- https://github.com/dotnet/roslyn/issues/33559
- https://github.com/dotnet/csharplang/discussions/4214
- https://github.com/dotnet/csharplang/issues/3659
- https://github.com/dotnet/csharplang/issues/3485
- https://github.com/dotnet/csharplang/issues/3659

这种情况可能是最大的。 我们确实支持可以为 null，但不支持明确赋值。

```cs
if (c?.M(out object obj3) == true)
{
    obj3.ToString(); // undesired error
}
```

### <a name="conditional-access-coalesced-to-a-bool-constant"></a>与 bool 常量合并的条件性访问

- https://github.com/dotnet/csharplang/discussions/916
- https://github.com/dotnet/csharplang/issues/3365

此方案与上一个方案非常类似。 这也支持可以为 null，但不能在明确赋值中进行。

```cs
if (c?.M(out object obj4) ?? false)
{
    obj4.ToString(); // undesired error
}
```

### <a name="conditional-expressions-where-one-arm-is-a-bool-constant"></a>一个 arm 为 bool 常量的条件表达式
- https://github.com/dotnet/roslyn/issues/4272

值得一提的是，如果条件表达式是常量 (即) ，则已经具有特殊行为。 `true ? a : b` 我们只是无条件地访问常量条件所指示的 arm，并忽略其他 arm。

另请注意，我们尚未处理此方案，这是可以为 null 的。

```cs
if (c != null ? c.M(out object obj4) : false)
{
    obj4.ToString(); // undesired error
}
```

# <a name="specification"></a>规范

## <a name="-null-conditional-operator-expressions"></a>?.  () 表达式的 null 条件运算符
我们引入了一个新部分 **？。 () 表达式的 null 条件运算符**。 请参阅适用于上下文的 [null 条件运算符](../spec/expressions.md#null-conditional-operator) 规范和 [明确赋值规则](../spec/variables.md#precise-rules-for-determining-definite-assignment) 。

与上面链接的明确赋值规则一样，我们将给定的初始未赋值变量称为 *v*。

我们引入了 "直接包含" 这一概念。 如果满足以下条件之一，则表达式 *e* 称为 "直接包含子表达式 *e <sub>1</sub>* "：
- *E* 为 *e <sub>1</sub>*。 例如， `a?.b()` 直接包含表达式 `a?.b()` 。
- 如果 *e* 是带括号的表达式 `(E2)` ，则 *e <sub>2</sub>* 直接包含 *e <sub>1</sub>*。
- 如果 *e* 是包容性运算符表达式，则 e `E2!` *<sub>2</sub>* 直接包含 *e <sub>1</sub>*。

对于窗体的表达式 *E* `primary_expression null_conditional_operations` ，让 *E <sub>0</sub>* 是通过按时间删除前导的表达式： 从每个包含 *E* 的 *null_conditional_operations* ，如以上链接的规范中所述。

在后面的部分中，我们会将 *E <sub>0</sub>* 引用为与 null 条件表达式 *对应的非条件对应* 项。 请注意，后续部分中的某些表达式将服从其他规则，这些规则仅当其中一个操作数直接包含空条件表达式时才适用。

- 在 *E* 内的任何时间点， *v* 的明确赋值状态与 *E0* 内相应点上的明确赋值状态相同。
- *E* 后 *v* 的明确赋值状态与 *primary_expression* 后 *v* 的明确分配状态相同。

### <a name="remarks"></a>备注
我们使用 "直接包含" 这一概念，以使我们能够在分析与其他值比较的条件访问时跳过相对简单的 "包装器" 表达式。 例如， `((a?.b(out x))!) == true` 预计会产生与一般相同的流状态 `a?.b == true` 。

当我们考虑在 null 条件表达式中的给定点上分配变量时，只需假设在相同的 null 条件表达式内的任何前面的 null 条件运算都已成功。

例如，给定一个条件表达式 `a?.b(out x)?.c(x)` ，非条件对应的是 `a.b(out x).c(x)` 。 例如，如果我们想要了解之前的明确赋值状态 `x` `?.c(x)` ，则执行的 "假设" 分析， `a.b(out x)` 并使用生成的状态作为的输入 `?.c(x)` 。

## <a name="boolean-constant-expressions"></a>布尔常量表达式
我们引入了一个新的 "布尔常量表达式" 部分：

对于 expression *expr* ，其中 *expr* 是带有 bool 值的常量表达式：
- *Expr* 后 *v* 的明确赋值状态由确定：
  - 如果 *expr* 是值 *为 true* 的常量表达式，且 *expr* 之前的 *v* 状态为 "未明确赋值"，则 *expr* 之后的 *v* 状态是 "在 false 时明确赋值"。
  - 如果 *expr* 是值 *为 false* 的常量表达式，且 *expr* 之前的 *v* 的状态为 "未明确赋值"，则 *expr* 后 *v* 的状态为 "如果为 true，则明确赋值"。

### <a name="remarks"></a>备注

例如，如果表达式的布尔值为布尔值 `false` ，则不可能到达需要表达式返回的任何分支 `true` 。 因此，在此类分支中，变量被假定为明确赋值。 这最终会与和等表达式的规格变化非常结合 `??` ， `?:` 并启用很多有用方案。

还值得注意的是，在访问常量表达式 *之前* ，永远不会出现条件状态。 这就是我们不考虑这种情况的原因，例如 "*expr* 是值 *为 true* 的常量表达式，而 *expr* 之前的 *v* 状态是" 在 true 时明确赋值 "。

## <a name="-null-coalescing-expressions-augment"></a>?? ) 增加 (null 合并表达式
我们按如下所示增加了节 [ (null 合并) 表达式](../spec/variables.md#-null-coalescing-expressions) ：

对于以下形式 *的表达式表达式* `expr_first ?? expr_second` ：
- ...
- *Expr* 后 *v* 的明确赋值状态由确定：
  - ...
  - 如果 *expr_first* 直接包含一个 null 条件表达式 *e*，并且在非条件对应的 *e <sub>0</sub>* 后， *v* 是明确赋值的，则在 *expr* 后 *v* 的明确赋值状态与 *expr_second* 后 *v* 的明确赋值状态相同。

### <a name="remarks"></a>备注
上面的规则将针对类似于的表达式进行了假设 `a?.M(out x) ?? (x = false)` ， `a?.M(out x)` 已完全计算并生成了一个非 null 值，在这种情况下，已 `x` 赋值或 `x = false` 已计算，在这种情况下， `x` 也分配了。 因此 `x` ，在此表达式之后始终赋值。

此方法还处理 `dict?.TryGetValue(key, out var value) ?? false` 方案，方法是：观察 *v* 在之后明确赋值 `dict.TryGetValue(key, out var value)` ， *v* 为 "true 时明确赋值"，最后结束时， `false` *v* 必须是 "true 赋值"。

更常见的表述还允许我们处理一些更不常见的方案，例如：
- `if (x?.M(out y) ?? (b && z.M(out y))) y.ToString();`
- `if (x?.M(out y) ?? z?.M(out y) ?? false) y.ToString();`

## <a name="-conditional-expressions"></a>？： (条件) 表达式
我们将对此部分进行扩展： [**： (条件) 表达式**](../spec/variables.md#-conditional-expressions) ，如下所示：

对于以下形式 *的表达式表达式* `expr_cond ? expr_true : expr_false` ：
- ...
- *Expr* 后 *v* 的明确赋值状态由确定：
  - ...
  - 如果 *expr_true* 后的 *v* 状态是 "在 true 时明确赋值"，并且在 *expr_false* 后 *v* 的状态为 "当为 true 时明确赋值"，则在 *expr* 后 *v* 的状态为 "当为 true 时明确赋值"。
  - 如果 *expr_true* 后的 *v* 状态为 "false 时明确赋值"，并且在 *expr_false* 后 *v* 的状态为 "当为 false 时明确赋值"，则在 *expr* 后 *v* 的状态为 "当为 false 时明确赋值"。

### <a name="remarks"></a>备注

这样一来，如果条件表达式的两个扶手都产生条件状态，我们就会加入相应的条件状态，并将其传播而不是 unsplitting 状态，并允许最终状态为非条件。 这会启用如下方案：

```cs
bool b = true;
object x = null;
int y;
if (b ? x != null && Set(out y) : x != null && Set(out y))
{
  y.ToString();
}

bool Set(out int x) { x = 0; return true; }
```

这是一种无可否认的方案，在本机编译器中编译时不会出错，但会在 Roslyn 中断开以匹配该规范。 查看 [内部问题](http://vstfdevdiv:8080/DevDiv2/DevDiv/_workitems/edit/529603)。

## <a name="-relational-equality-operator-expressions"></a>= =/！ = (关系相等运算符) 表达式
我们引入了一个新的节 **= =/！ = (关系相等运算符) 表达式**。

除了下述方案， [使用嵌入表达式的表达式的一般规则](../spec/variables.md#general-rules-for-expressions-with-embedded-expressions) 都适用。

对于窗体 *的表达式表达式* `expr_first == expr_second` （其中 `==` 是 [预定义的比较运算符](../spec/expressions.md#relational-and-type-testing-operators)或 [提升运算符](../spec/expressions.md#lifted-operators)）， *expr* 后 *v* 的明确赋值状态由确定：
  - 如果 *expr_first* 直接包含 null 条件表达式 *E* 并且 *expr_second* 是值 *为 null* 的常量表达式，而与非条件对应的 *E <sub>0</sub>* 的状态为 "明确赋值"，则 *expr* 后 *v* 的状态为 "当为 false 时明确赋值"。
  - 如果 *expr_first* 直接包含 null 条件表达式 *E* 并且 *expr_second* 是不可以为 null 的值类型的表达式，或者是具有非 null 值的常量表达式，而与非 null 值 *对应的常量* 表达式为 "明确赋值"，则在 *expr* 后 *v* 的 *状态为 "<sub></sub>* 在 true 时明确赋值"。
  - 如果 *expr_first* 的类型为 *boolean*， *expr_second* 是值 *为 true* 的常量表达式，则 *expr* 之后的明确赋值状态与 *expr_first* 后的明确赋值状态相同。
  - 如果 *expr_first* 的类型为 *boolean*，并且 *expr_second* 是值 *为 false* 的常量表达式，则 *expr* 之后的明确赋值状态与逻辑求反表达式后 *v* 的明确赋值状态相同 `!expr_first` 。

对于窗体 *的表达式表达式* `expr_first != expr_second` （其中 `!=` 是 [预定义的比较运算符](../spec/expressions.md#relational-and-type-testing-operators)或 [提升运算符](../spec/expressions.md#lifted-operators)）， *expr* 后 *v* 的明确赋值状态由确定：
  - 如果 *expr_first* 直接包含 null 条件表达式 *E* 并且 *expr_second* 是值 *为 null* 的常量表达式，而与非条件对应的 *E <sub>0</sub>* 的状态为 "明确赋值"，则 *expr* 后 *v* 的状态为 "在 true 时明确赋值"。
  - 如果 *expr_first* 直接包含 null 条件表达式 *E* 并且 *expr_second* 是不可以为 null 的值类型的表达式，或者是具有非 null 值的常量表达式，而与非 null 值 *对应的常量* 表达式为 "明确赋值"，则在 *expr* 后 *v* 的 *状态为 "<sub></sub>* 在 false 时明确赋值"。
  - 如果 *expr_first* 的类型为 *boolean*，并且 *expr_second* 是值 *为 true* 的常量表达式，则 *expr* 之后的明确赋值状态与逻辑求反表达式后 *v* 的明确赋值状态相同 `!expr_first` 。
  - 如果 *expr_first* 的类型为 *boolean*， *expr_second* 是值为 *false* 的常量表达式，则 *expr* 之后的明确赋值状态与 *expr_first* 后的明确赋值状态相同。

本部分中的所有上述规则都是可交换的，这意味着，如果规则在窗体中计算时应用 `expr_second op expr_first` ，则它也适用于格式 `expr_first op expr_second` 。

### <a name="remarks"></a>备注
这些规则表示的一般理念是：
- 如果将条件访问与进行比较 `null` ，则我们知道如果比较结果为 `false`
- 如果将条件访问与不可以为 null 的值类型或非 null 常量进行比较，则我们知道如果比较结果为，则确实会发生运算 `true` 。
- 由于我们无法信任用户定义的运算符来提供有关初始化安全问题的可靠答案，因此仅当使用预定义运算符时，新规则才适用 `==` / `!=` 。

我们可能最终会希望将这些规则细化到在成员访问或调用结束时出现的条件状态的线程。 这种情况在明确的赋值中并不真正发生，但在存在和类似的属性中，它们会以可为 null 的形式出现 `[NotNullWhen(true)]` 。 `bool`除了处理/non-null 常量外，这还需要对常量进行特殊处理 `null` 。

这些规则的一些后果：
- `if (a?.b(out var x) == true)) x() else x();` "else" 分支中会出错
- `if (a?.b(out var x) == 42)) x() else x();` "else" 分支中会出错
- `if (a?.b(out var x) == false)) x() else x();` "else" 分支中会出错
- `if (a?.b(out var x) == null)) x() else x();` "then" 分支中将出错
- `if (a?.b(out var x) != true)) x() else x();` "then" 分支中将出错
- `if (a?.b(out var x) != 42)) x() else x();` "then" 分支中将出错
- `if (a?.b(out var x) != false)) x() else x();` "then" 分支中将出错
- `if (a?.b(out var x) != null)) x() else x();` "else" 分支中会出错

## <a name="is-operator-and-is-pattern-expressions"></a>`is` 运算符和 `is` 模式表达式
我们引入了一个新的区段 **`is` 运算符和 `is` 模式表达式**。

对于窗体 *的表达式表达式* `E is T` ，其中 *T* 是任意类型或模式
- *E* 前 *v* 的明确赋值状态与 *expr* 之前的 *v* 的明确赋值状态相同。
- *Expr* 后 *v* 的明确赋值状态由确定：
  - 如果 *E* 直接包含一个 null 条件表达式，而与非条件对应的 *e <sub>0</sub>* 的 *状态为 "* 明确赋值"，并且 `T` 是仅匹配非 null 输入的任何类型或模式，则在 *expr* 后 *v* 的状态为 "在 true 时明确赋值"。
  - 如果 *E* 直接包含一个 null 条件表达式，而与非条件对应的 *e <sub>0</sub>* 的 *状态为 "* 明确赋值"，并且 `T` 是仅匹配 null 输入的模式，则在 *expr* 后 *v* 的状态为 "当为 false 时明确赋值"。
  - 如果 *E* 的类型为布尔值并且 `T` 是常量模式 `true` ，则在 *expr* 后 *v* 的明确赋值状态与 E 后 *v* 的明确赋值状态相同。
  - 如果 *E* 的类型为布尔值并且 `T` 是常量模式 `false` ，则在 *expr* 后 *v* 的明确赋值状态与逻辑求反表达式后 *v* 的明确赋值状态相同 `!expr` 。
  - 否则，如果在 E 后， *v* 的明确赋值状态为 "明确赋值"，则在 *expr* 后 *v* 的明确赋值状态为 "明确赋值"。

### <a name="remarks"></a>备注

本部分旨在解决与上述部分类似的情形 `==` / `!=` 。
此规范不处理递归模式，例如 `(a?.b(out x), c?.d(out y)) is (object, object)` 。 如果时间允许，则会出现此类支持。

# <a name="additional-scenarios"></a>其他方案

此规范当前不处理涉及模式开关表达式和 switch 语句的方案。 例如：

```cs
_ = c?.M(out object obj4) switch
{
    not null => obj4.ToString() // undesired error
};
```

如果时间允许，可能会出现这种情况。

存在几种可为 null 的 bug 类别，这需要我们实质上增加模式分析的复杂性。 我们所做的任何决定会提高明确的分配，还会将其转到可为 null。

https://github.com/dotnet/roslyn/issues/49353  
https://github.com/dotnet/roslyn/issues/46819  
https://github.com/dotnet/roslyn/issues/44127

## <a name="drawbacks"></a>缺点
[drawbacks]: #drawbacks

当一般流分析状态应该向上传播时，分析 "向下传递" 并具有条件访问的特殊识别会感到奇怪。 我们关心此类解决方案如何与可能会执行 null 检查的未来语言功能不厌其烦相交。

## <a name="alternatives"></a>备选方法
[alternatives]: #alternatives

此建议的两种替代方法：
1. 向语言和编译器介绍 "null 时的状态" 和 "非 null 时的状态"。 对于我们尝试解决的方案来说，这是一项很大的努力，但我们仍有可能实现上述建议，然后在不中断人员的情况下，转到 "null/not null" 模型。
2. 不执行任何操作。

## <a name="unresolved-questions"></a>未解决的问题
[unresolved]: #unresolved-questions

应指定的 switch 表达式有影响： https://github.com/dotnet/csharplang/discussions/4240#discussioncomment-343395

## <a name="design-meetings"></a>设计会议

https://github.com/dotnet/csharplang/discussions/4243

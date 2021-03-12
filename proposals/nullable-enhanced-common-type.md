---
ms.openlocfilehash: 7d98694ef42884cc508558955f068f59181d9568
ms.sourcegitcommit: ae69a53cf0cd57b3bcd17263a33a317a2c1dd205
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/11/2021
ms.locfileid: "103026025"
---
# <a name="nullable-enhanced-common-type"></a>Nullable-Enhanced 通用类型

* [x] 建议
* [] 原型：无
* [] 实现：无
* [] 规范：请参见下面

## <a name="summary"></a>总结
[summary]: #summary

在某些情况下，当前通用类型算法结果是非常直观的，并会导致程序员将冗余强制转换为代码。 进行此更改后，表达式（如） `condition ? 1 : null` 将导致类型的值，例如， `int?` `condition ? x : 1.0` where `x` 为的类型 `int?` 将导致类型的值 `double?` 。

## <a name="motivation"></a>动机
[motivation]: #motivation

这是类似于程序员的常见原因，如不必要的样板代码。

## <a name="detailed-design"></a>详细设计
[design]: #detailed-design

我们修改了用于 [查找一组表达式的最佳通用类型的](https://github.com/dotnet/csharplang/blob/master/spec/expressions.md#finding-the-best-common-type-of-a-set-of-expressions) 规范，以影响下列情况：

- 如果一个表达式是不可以为 null 的值类型 `T` ，另一个表达式为 null 文本，则结果为类型 `T?` 。
- 如果一个表达式是可以为 null 的值类型，另一个表达式是 `T?` 值类型 `U` ，并且存在从到的隐式 `T` 转换 `U` ，则结果为类型 `U?` 。

这应该会影响语言的以下方面：

- [三元表达式](https://github.com/dotnet/csharplang/blob/master/spec/expressions.md#conditional-operator)
- 隐式类型的 [数组创建表达式](https://github.com/dotnet/csharplang/blob/master/spec/expressions.md#array-creation-expressions)
- 推断 [lambda 的返回类型](https://github.com/dotnet/csharplang/blob/master/spec/expressions.md#inferred-return-type) 推断类型
- 涉及泛型的事例，如调用 `M<T>(T a, T b)` 为 `M(1, null)` 。

更准确地讲，我们更改了规范的以下部分 (以粗体显示，删除线删除) ：

> #### <a name="output-type-inferences"></a>输出类型推断
> 
> *从* 表达式到类型的 *输出类型推理* `E`  `T` 按以下方式进行：
> 
> *  如果 `E` 是具有推断返回类型的匿名函数 `U` ([推断出的返回](../spec/expressions.md#inferred-return-type)类型) 并且 `T` 是具有返回类型的委托类型或表达式树类型 `Tb` ，则 *从* 到进行) [下限](../spec/expressions.md#lower-bound-inferences)*推理* (`U`  `Tb` 。
> *  否则，如果 `E` 是方法组，并且 `T` 是具有参数类型和返回类型的委托类型或表达式树类型 `T1...Tk` `Tb` ，并且具有类型的重载解析 `E` `T1...Tk` 生成具有返回类型的单个方法 `U` ，则将 *从* 到进行 *下限推理* `U`  `Tb` 。
> *  * * 否则，如果 `E` 是具有可为 null 的值类型的表达式 `U?` ，则将 *从* 到进行 *下限推理*， `U`  `T` 并将 *null 绑定* 添加到中 `T` 。 **
> *  否则，如果 `E` 是类型为的表达式 `U` ，则将 *从* 到进行 *下限推理* `U`  `T` 。
> *  **否则，如果 `E` 是具有值的常量表达式 `null` ，则会将 *null 绑定* 添加到 `T`** 
> *  否则，不进行推断。

> #### <a name="fixing"></a>修正 
> 
> 具有一组界限的未 *固定* 类型变量 `Xi` *固定* 如下：
> 
> *  *候选类型* 集 `Uj` 作为的边界集中的所有类型的集合开始 `Xi` 。
> *  接下来，我们依次检查每个绑定 `Xi` ：对于与 `U` `Xi` `Uj` `U` 从候选集删除的所有类型不同的每个完全绑定，都是如此。 对于 `U` `Xi` 不从其隐式转换的所有类型的每个下限， `Uj`  `U` 从候选集删除。 对于 `U` 不将隐式转换为的所有类型的每个上限，从 `Xi` `Uj`  `U` 候选集删除。
> *  如果其余的候选类型中 `Uj` 有一个唯一类型 `V` ，其中存在到所有其他候选类型的隐式转换，则~~ `Xi` 修复为 `V` 。~~
>     -  **如果 `V` 是值类型且存在 *null 界限* `Xi` ，则 `Xi` 固定到 `V?`**
>     -  **否则   `Xi` ，固定到 `V`**
> *  否则，类型推理将失败。

## <a name="drawbacks"></a>缺点
[drawbacks]: #drawbacks

此建议可能会导致某些不兼容问题。

## <a name="alternatives"></a>备选方法
[alternatives]: #alternatives

无。

## <a name="unresolved-questions"></a>未解决的问题
[unresolved]: #unresolved-questions

- [] 此提议引入的不兼容性严重性（如果有）以及如何进行仲裁？

## <a name="design-meetings"></a>设计会议

无。

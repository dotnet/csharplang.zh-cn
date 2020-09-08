---
ms.openlocfilehash: a774e370a14373ccf308c2cba9944305c0053c05
ms.sourcegitcommit: c3df20406f43fcd460cfedd1cd61b6cc47d27250
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/08/2020
ms.locfileid: "89557559"
---
# <a name="extension-getenumerator-support-for-foreach-loops"></a>扩展 `GetEnumerator` 支持 `foreach` 循环。

## <a name="summary"></a>总结
[summary]: #summary

允许 foreach 循环识别扩展方法 GetEnumerator 方法，该方法以其他方式满足 foreach 模式，并在该表达式被视为错误时循环使用。

## <a name="motivation"></a>动机
[motivation]: #motivation

这会在实现 c # 中的其他功能（包括异步和基于模式的析构）的情况下引入 foreach 内联。

## <a name="detailed-design"></a>详细设计
[design]: #detailed-design

Spec 更改相对简单。 修改 `The foreach statement` 此文本部分：

>Foreach 语句的编译时处理首先确定表达式的 ***集合类型***、 ***枚举器类型*** 和 ***元素类型*** 。 这种决定将按如下方式进行：
>
>*  如果表达式的 `X` 类型*expression*是数组类型，则从到接口 (存在隐式引用转换， `X` `IEnumerable` 因为 `System.Array`) 实现此接口。 ***集合类型***是 `IEnumerable` 接口，***枚举器类型***为 `IEnumerator` 接口，***元素类型***是数组类型的元素类型 `X` 。
>*  如果表达式的 `X` 类型*expression*为，则 `dynamic` 从*expression*到 Interface 的隐式转换 `IEnumerable` ([隐式动态转换](conversions.md#implicit-dynamic-conversions)) 。 ***集合类型***是 `IEnumerable` 接口，而***枚举器类型***是 `IEnumerator` 接口。 如果 `var` 标识符指定为 *local_variable_type* 则该 ***元素类型*** 为 `dynamic` ，否则为 `object` 。
>*  否则，请确定该类型是否 `X` 具有适当的 `GetEnumerator` 方法：
>    * 对 `X` 具有标识符 `GetEnumerator` 且无类型参数的类型执行成员查找。 如果成员查找不会生成匹配项，或者它产生了多义性，或者产生了不是方法组的匹配项，请检查可枚举的接口，如下所述。 如果成员查找产生了除方法组以外的任何内容，则建议发出警告。
>    * 使用生成的方法组和空参数列表执行重载决策。 如果重载决策导致没有适用的方法、导致歧义或产生单个最佳方法，但该方法是静态的或非公共的，请按如下所述检查可枚举的接口。 如果重载决策产生了除明确的公共实例方法之外的任何内容，或者没有适用方法，则建议发出警告。
>    * 如果该方法的返回类型 `E` `GetEnumerator` 不是类、结构或接口类型，则会生成错误，并且不会执行任何其他步骤。
>    * 成员查找是在上 `E` 用标识符 `Current` 而不是类型参数执行的。 如果成员查找没有生成任何匹配项，则结果为错误，或结果为除允许读取的公共实例属性之外的任何内容，并不执行任何其他步骤。
>    * 成员查找是在上 `E` 用标识符 `MoveNext` 而不是类型参数执行的。 如果成员查找没有生成任何匹配项，则结果为错误，或结果为除方法组外的任何内容，并不执行任何其他步骤。
>    * 使用空参数列表对方法组执行重载决策。 如果重载决策导致没有适用的方法，导致不确定性，或者导致单个最佳方法，但该方法是静态的或非公共的，或者它的返回类型不是 `bool` ，则会生成错误，并且不会执行任何其他步骤。
>    * ***集合类型***为 `X` ，***枚举器类型***为 `E` ，***元素类型***为属性的类型 `Current` 。
>
>*  否则，请检查可枚举的接口：
>    * 如果 `Ti` 存在从到的隐式转换到的所有类型 `X` `IEnumerable<Ti>` ，则有一个唯一类型， `T` 它 `T` 不是， `dynamic` 而对于所有其他类型的 `Ti` 都是从到的隐式转换 `IEnumerable<T>` ，而 `IEnumerable<Ti>` ***集合类型*** 是接口 `IEnumerable<T>` ， ***枚举器类型*** 为接口 `IEnumerator<T>` ， ***元素类型*** 为 `T` 。
>    * 否则，如果有多个这样的类型 `T` ，则会生成错误，并且不会执行任何其他步骤。
>    * 否则，如果存在从到接口的隐式转换 `X` `System.Collections.IEnumerable` ，则 ***集合类型*** 为此接口， ***枚举器类型*** 为接口 `System.Collections.IEnumerator` ， ***元素类型*** 为 `object` 。
>*  否则，确定类型 "X" 是否具有适当的 `GetEnumerator` 扩展方法：
>    * 对标识符为的类型执行扩展方法查找 `X` `GetEnumerator` 。 如果成员查找不会生成匹配项，或者它产生了多义性，或者产生了不是方法组的匹配项，则会生成错误，并且不会执行任何其他步骤。 如果成员查找产生了除方法组外的任何内容，或者没有匹配项，则建议发出警告。
>    * 使用生成的方法组和一个类型的参数来执行重载决策 `X` 。 如果重载决策不生成适用的方法、导致歧义，或者导致单个最佳方法，但该方法不可访问，则不会执行任何其他步骤。
>        * 如果是一个结构类型，则此解决方法允许 ref 传递第一个参数 `X` ，而 ref 类型为 `in` 。
>    * 如果该方法的返回类型 `E` `GetEnumerator` 不是类、结构或接口类型，则会生成错误，并且不会执行任何其他步骤。
>    * 成员查找是在上 `E` 用标识符 `Current` 而不是类型参数执行的。 如果成员查找没有生成任何匹配项，则结果为错误，或结果为除允许读取的公共实例属性之外的任何内容，并不执行任何其他步骤。
>    * 成员查找是在上 `E` 用标识符 `MoveNext` 而不是类型参数执行的。 如果成员查找没有生成任何匹配项，则结果为错误，或结果为除方法组外的任何内容，并不执行任何其他步骤。
>    * 使用空参数列表对方法组执行重载决策。 如果重载决策导致没有适用的方法，导致不确定性，或者导致单个最佳方法，但该方法是静态的或非公共的，或者它的返回类型不是 `bool` ，则会生成错误，并且不会执行任何其他步骤。
>    * ***集合类型***为 `X` ，***枚举器类型***为 `E` ，***元素类型***为属性的类型 `Current` 。
>*  否则，将生成错误，并且不执行任何其他步骤。

对于 `await foreach` ，规则的修改方式也类似。 该规范所需的唯一更改是 `Extension methods do not contribute.` 从说明中删除该行，因为该规范的其余部分基于以上规则，这些规则的名称替换为模式方法的不同名称。

## <a name="drawbacks"></a>缺点
[drawbacks]: #drawbacks

每次更改都会增加语言的复杂性，并且这可能会使设计不是 ed 的东西 `foreach` `foreach` （如） `Range` 。

## <a name="alternatives"></a>备选方法
[alternatives]: #alternatives

不执行任何操作。

## <a name="unresolved-questions"></a>未解决的问题
[unresolved]: #unresolved-questions

目前无。

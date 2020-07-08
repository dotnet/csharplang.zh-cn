---
ms.openlocfilehash: 5584f6a67ab21ed92d0b8c98dd0bd18ca7a6bc3d
ms.sourcegitcommit: 1aaa2ce9416639485fd0ccbbd32f68e53be6d3ea
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/08/2020
ms.locfileid: "86074278"
---
# <a name="extend-null-coalescing--and-null-coalescing-assignment--operators-to-pointers"></a>扩展 null 合并（"？"）和 null 合并分配（"？"=）到指针的运算符

* [x] 建议
* [] 原型：未启动
* [] 实现：未启动
* [] 规范：正在进行

## <a name="summary"></a>总结
[summary]: #summary

此建议扩展了对 c # 中的常用功能的支持（？？ 和？？=）到不安全代码和指针类型 specifcally。 

## <a name="motivation"></a>动机
[motivation]: #motivation


此功能没有理由从此功能中排除，因为它们在语义上适合该功能的用例。 支持此功能可将功能的范围扩展到预期其逻辑支持的范围。 三元表达式已支持此概念，例如：

 `T *foo = bar != null ? bar : baz;`

因此，为非指针类型提供的相同句法选项应扩展为指针类型。

## <a name="detailed-design"></a>详细设计
[design]: #detailed-design

对于这种语言的补充，无需进行语法更改。 我们只是向现有操作员添加受支持的类型。 然后，当前转换规则将确定？？的结果类型。 expression。
其他规则将保持不变，但需要修改的宽松类型规则除外。

C # 规范将需要更新，以反映此添加的行更改。
> 形式为 "？" 的 null 合并表达式 `a` 。 `b`需要 `a` 是可以为 null 的类型或引用类型。

to

> 形式为 "？" 的 null 合并表达式 `a` 。 `b`需要 `a` 是可以为 null 的类型、引用类型或指针类型。

非常丰富的 
> 如果 `A` 存在并且不是可以为 null 的类型或引用类型，则会发生编译时错误。

to

> 如果 `A` 存在并且不是可以为 null 的类型、引用类型或指针类型，则会发生编译时错误。

目前，的规则 `??=` 不会禁止指针类型; 但是，运算符的实现方式并不是这样。


## <a name="drawbacks"></a>缺点
[drawbacks]: #drawbacks

不安全代码可视为混乱，并将不会发生的 bug 的向量视为混乱。 增加指针类型的可用句法选项可能导致开发人员不知道编写内容的语义含义。 

## <a name="alternatives"></a>备选方法
[alternatives]: #alternatives

未考虑其他设计，因为这不是一项新功能，而是一个已实现的功能的扩展。

## <a name="unresolved-questions"></a>未解决的问题
[unresolved]: #unresolved-questions

一个可能的问题是，在支持的隐式转换中，是否应将支持扩展到隐式取消引用 `Nullable<T>` 。

例如

    int* foo = null;
    int bar = foo ?? 3;

## <a name="design-meetings"></a>设计会议

https://github.com/dotnet/csharplang/issues/418




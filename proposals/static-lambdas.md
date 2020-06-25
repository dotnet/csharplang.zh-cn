---
ms.openlocfilehash: efe927e6d3abdde5a350eadc9208949710bb4a39
ms.sourcegitcommit: b901db48deffcbe2ec7cc08ec6cb376d1f7faecb
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/24/2020
ms.locfileid: "85345207"
---
# <a name="static-lambdas"></a>静态 lambda

## <a name="summary"></a>摘要

支持禁止从封闭范围捕获状态的 lambda。

## <a name="motivation"></a>动机

避免意外捕获来自封闭上下文的状态。

## <a name="detailed-design"></a>详细设计

具有的 lambda `static` 无法从封闭范围中捕获状态。
因此， `this` 不能在 lambda 中使用封闭范围内的局部变量、参数和 `static` 。

`static`Lambda 无法通过隐式或显式或引用来引用实例成员 `this` `base` 。

`static`Lambda 可以引用 `static` 封闭范围内的成员。

`static`Lambda 可以引用 `constant` 来自封闭范围的定义。

`nameof()`在中， `static` lambda 可以引用局部变量、参数或 `this` 或 `base` 从封闭范围引用。

封闭范围中的成员的可访问性规则 `private` 对于 `static` 和非 lambda 是相同的 `static` 。

不保证 `static` lambda 定义是否作为 `static` 元数据中的方法发出。 这留给了编译器实现进行优化。

非 `static` 本地函数或 lambda 可以从封闭的 lambda 捕获状态 `static` ，但不能在封闭的 lambda 之外捕获状态 `static` 。

`static`从有效程序的 lambda 中删除修饰符不会更改程序的含义。

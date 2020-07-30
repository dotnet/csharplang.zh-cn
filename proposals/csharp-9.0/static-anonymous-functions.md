---
ms.openlocfilehash: 0a6b3a4aa86bac8e7855ec2f4c50ac022941000e
ms.sourcegitcommit: 89bbdd101f539cefd41b2b8b9087fc9cdbcf3c62
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/29/2020
ms.locfileid: "87412442"
---
# <a name="static-anonymous-functions"></a>静态匿名函数

## <a name="summary"></a>总结

允许对 lambda 和匿名方法使用 "static" 修饰符，这不允许从包含范围捕获局部变量或实例状态。

## <a name="motivation"></a>动机

避免意外捕获来自封闭上下文的状态，这可能导致捕获对象的意外保留或意外的额外分配。

## <a name="detailed-design"></a>详细设计

Lambda 或匿名方法可能具有 `static` 修饰符。 `static`修饰符指示 lambda 或匿名方法为*静态匿名函数*。

*静态匿名函数*无法从封闭范围中捕获状态。
因此， `this` 不能在*静态匿名函数*内使用封闭范围中的局部变量、参数和。

*静态匿名函数*不能通过隐式或显式或引用来引用实例成员 `this` `base` 。

*静态匿名函数*可以引用 `static` 封闭范围内的成员。

*静态匿名函数*可以引用 `constant` 来自封闭范围的定义。

`nameof()`在*静态匿名函数*中，可以引用局部变量、参数或 `this` 或 `base` 从封闭范围引用。

封闭范围中的成员的可访问性规则与 `private` `static` 非 `static` 匿名函数相同。

不保证*静态匿名函数*定义是否作为 `static` 元数据中的方法发出。 这留给了编译器实现进行优化。

非 `static` 本地函数或匿名函数可以从封闭*静态匿名函数*捕获状态，但不能在封闭*静态匿名函数*之外捕获状态。

`static`从有效程序中的匿名函数删除修饰符不会更改程序的含义。

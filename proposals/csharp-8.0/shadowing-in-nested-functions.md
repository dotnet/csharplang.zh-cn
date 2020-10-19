---
ms.openlocfilehash: 165de098ddbd753416e01c4ff8e44750d9b7f66c
ms.sourcegitcommit: a0b59a6768813152b4b5b6df3637041a64aba2ee
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/19/2020
ms.locfileid: "92185110"
---
# <a name="name-shadowing-in-nested-functions"></a>嵌套函数中的名称隐藏

## <a name="summary"></a>总结

允许 lambda 和局部函数中的变量名称重复使用 (，并隐藏封闭方法或函数中的) 名称。

## <a name="detailed-design"></a>详细设计

使用时 `-langversion:8` ，lambda 或局部函数内的局部变量、本地函数、参数、类型参数和范围变量的名称可以重复使用局部变量、本地函数、参数、类型参数和范围变量的名称。 嵌套函数中的名称将隐藏嵌套函数内的封闭函数中具有相同名称的符号。

`static`和非 `static` 本地函数和 lambda 支持隐藏。

使用 `-langversion:7.3` 或更早版本的行为没有变化：隐藏封闭方法或函数中的名称的嵌套函数中的名称在这些情况下将报告为错误。

仍支持以前允许的任何映射 `-langversion:8` 。 例如：变量名称可能会隐藏类型和成员名称;和变量名可以隐藏封闭方法或局部函数名称。

隐藏在同一 lambda 或局部函数内的封闭范围中声明的名称仍会被报告为错误。

对于在封闭类型、方法或函数中隐藏类型参数的本地函数中的类型参数，将报告警告。

## <a name="design-meetings"></a>设计会议

- https://github.com/dotnet/csharplang/blob/master/meetings/2018/LDM-2018-09-10.md#static-local-functions
- https://github.com/dotnet/csharplang/blob/master/meetings/2019/LDM-2019-01-16.md#shadowing-in-nested-functions

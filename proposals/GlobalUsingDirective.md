---
ms.openlocfilehash: e752cfc2356bbbad34f23b48791058cde4968765
ms.sourcegitcommit: f0590512a5b191faa4ae591a89bbdd71cec819a2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/26/2021
ms.locfileid: "101635338"
---
# <a name="global-using-directive"></a>全局 Using 指令

Using 指令的语法用 `global` 可在关键字之前的可选关键字进行扩展 `using` ：
```antlr
using_directive
  : 'global'? 'using' ('static' | name_equals)? name ';'
  ;
```

- 仅在编译单元级别允许使用全局 Using 指令， (不能在命名空间声明) 中使用。
- 全局 Using 指令（如果有）必须位于任何非全局 using 指令之前。 
- 全局 Using 指令的作用域通过程序中所有编译单元的命名空间成员声明和非全局使用指令进行扩展。
全局 Using 指令的作用域不包含其他全局 Using 指令。 因此，对等全局使用指令或来自不同编译单元的指令不会相互影响，并且编写它们的顺序无关紧要。

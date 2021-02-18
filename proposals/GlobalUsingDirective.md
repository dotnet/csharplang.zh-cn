---
ms.openlocfilehash: 22d29c5186db5be559647a0c72cc749de4b7890c
ms.sourcegitcommit: 1f5b1dc19d21038b59bfce169fd49e121a5a1f4e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/18/2021
ms.locfileid: "101101824"
---
# <a name="global-using-directive"></a><span data-ttu-id="4ede2-101">全局 Using 指令</span><span class="sxs-lookup"><span data-stu-id="4ede2-101">Global Using Directive</span></span>

<span data-ttu-id="4ede2-102">使用 `global` 可以跟在关键字后面的可选关键字扩展 using 指令的语法 `using` ：</span><span class="sxs-lookup"><span data-stu-id="4ede2-102">Syntax for a using directive is extended with an optional `global` keyword that can follow the `using` keyword:</span></span>
```antlr
using_directive
  : 'using' 'global'? ('static' | name_equals)? name ';'
  ;
```

- <span data-ttu-id="4ede2-103">仅在编译单元级别允许使用全局 Using 指令， (不能在命名空间声明) 中使用。</span><span class="sxs-lookup"><span data-stu-id="4ede2-103">Global Using Directives are allowed only on the Compilation Unit level (cannot be used inside a namespace declaration).</span></span>
- <span data-ttu-id="4ede2-104">全局 Using 指令（如果有）必须位于任何非全局 using 指令之前。</span><span class="sxs-lookup"><span data-stu-id="4ede2-104">Global Using directives, if any, must precede any non-global using directives.</span></span> 
- <span data-ttu-id="4ede2-105">全局 Using 指令的作用域通过程序中所有编译单元的命名空间成员声明和非全局使用指令进行扩展。</span><span class="sxs-lookup"><span data-stu-id="4ede2-105">The scope of a Global Using Directive extends over the namespace member declarations and non-global using directives of all compilation units within the program.</span></span>
<span data-ttu-id="4ede2-106">全局 Using 指令的作用域不包含其他全局 Using 指令。</span><span class="sxs-lookup"><span data-stu-id="4ede2-106">The scope of a Global Using Directive specifically does not include other Global Using Directives.</span></span> <span data-ttu-id="4ede2-107">因此，对等全局使用指令或来自不同编译单元的指令不会相互影响，并且编写它们的顺序无关紧要。</span><span class="sxs-lookup"><span data-stu-id="4ede2-107">Thus, peer Global Using Directives or those from a different compilation unit do not affect each other, and the order in which they are written is insignificant.</span></span>

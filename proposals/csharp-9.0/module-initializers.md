---
ms.openlocfilehash: e345bb5d9a4f998f80c13a255cec808bd2c9299f
ms.sourcegitcommit: 0c25406d8a99064bb85d934bb32ffcf547753acc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/28/2020
ms.locfileid: "87297258"
---
# <a name="module-initializers"></a>模块初始值设定项

* [x] 建议
* [] 原型：[正在进行](https://github.com/jnm2/roslyn/tree/module_initializer)
* [] 实现：[正在进行](https://github.com/dotnet/roslyn/tree/features/module-initializers)
* [] 规范：[未启动]()

## <a name="summary"></a>摘要
[summary]: #summary

尽管 .NET 平台具有直接支持编写程序集（从技术上讲为模块）的初始化代码的[功能](https://github.com/dotnet/runtime/blob/master/docs/design/specs/Ecma-335-Augments.md#module-initializer)，但它不是用 c # 公开的。  这是一个相当小的方案，但一旦进入该方案，解决方案就显得非常令人头痛。  有[大量客户](https://www.google.com/search?q=.net+module+constructor+c%23&oq=.net+module+constructor)（在 Microsoft 内部和外部）正在解决问题，并且没有任何不清楚记录的情况。

## <a name="motivation"></a>动机
[motivation]: #motivation

- 允许库在加载时执行预先的一次性初始化，开销最小，用户无需显式调用任何内容
- 当前构造函数方法的一个特别难点 `static` 是，运行时必须对使用静态构造函数的类型使用额外的检查，以确定是否需要运行静态构造函数。 这会增加可度量的开销。
- 使源生成器无需显式调用任何内容即可运行一些全局初始化逻辑

## <a name="detailed-design"></a>详细设计
[design]: #detailed-design

通过使用属性修饰方法，可以将方法指定为模块初始值设定项 `[ModuleInitializer]` 。

```cs
using System;
namespace System.Runtime.CompilerServices
{
    [AttributeUsage(AttributeTargets.Method, AllowMultiple = false)]
    public sealed class ModuleInitializerAttribute : Attribute { }
}
```

属性可如下所示：

```cs
using System.Runtime.CompilerServices;
class C
{
    [ModuleInitializer]
    internal static void M1()
    {
        // ...
    }
}
```

某些要求是针对具有此属性的方法施加的：
1. 方法必须是 `static` 。
1. 方法必须是无参数的。
1. 该方法必须返回 `void`。
1. 方法不能是泛型或包含在泛型类型中。
1. 此方法必须可从包含模块访问。
    - 这意味着该方法的有效可访问性必须为 `internal` 或 `public` 。
    - 这也意味着该方法不能是本地函数。
    
在编译中找到一个或多个具有此属性的有效方法时，编译器将发出一个调用每个属性化方法的模块初始值设定项。 调用将按保留但确定性顺序发出。

## <a name="drawbacks"></a>缺点
[drawbacks]: #drawbacks

为什么*不*应这样做？

- "注入" 模块初始值设定项的现有第三方工具或许足以满足要求使用此功能的用户。

## <a name="design-meetings"></a>设计会议

### <a name="april-8th-2020"></a>[2020年4月8日](/meetings/2020/LDM-2020-04-08.md#module-initializers)

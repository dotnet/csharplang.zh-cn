---
ms.openlocfilehash: c40d143a933969b80f8902938a6243c33ca09432
ms.sourcegitcommit: 9cf2f666477775bacc56ed5915bc00081d4fcb8e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/09/2020
ms.locfileid: "86208406"
---
# <a name="repeated-attributes-in-partial-members"></a>分部成员中重复的属性

## <a name="summary"></a>总结

允许分部成员的每个声明独立应用未标记为的属性 `[AttributeUsage(AllowMultiple = true)]` ，前提是所有应用程序中的属性参数都是相同的。

## <a name="motivation"></a>动机

考虑在 "partial" 方法中存在哪些特性时，语言会将这两个声明中所有相应位置的所有属性联合在一起。 例如，下面的方法 `M` 具有属性 `A` 和 `B` 。

```cs
[A]
partial void M();

[B]
partial void M() { }
```

这意味着，不标记的属性 `[AttributeUsage(AllowMultiple = true)]` 不能在这两个部分中出现：

```cs
[A]
partial void M();

[A] // error: duplicate attribute!
partial void M() { }
```

这就是一个可用性/可读性问题，因为某些特性旨在通知用户和/或 maintainer 方法所需的预定义和后置条件的方法。 例如：

```cs
public partial bool TryGetValue([NotNullWhen(true)] out object? value);
public partial bool TryGetValue(out object? value) { ... }
```

分部成员通常有助于代码生成器与最终用户之间的关系--每个参与方都提供部分成员的声明，以便代码生成器为用户提供功能，或访问生成的代码中的扩展点。 在只允许一个声明具有这些单应用程序属性的情况下，生成器和用户无法有效地将其要求传达给彼此。 例如，如果生成器使用特性生成定义声明， `NotNullWhen` 则即使后置条件适用于实现并由编译器检查，用户也不能使用相同的属性编写实现声明。 这会给用户造成混淆，同时跟踪警告的根本原因或尝试了解方法的行为。

## <a name="solution"></a>解决方案

只要特性参数相同，就允许对每个分部声明中的每个符号 (member、return、parameter 等 ) 使用一次非 AllowMultiple 属性。 由于属性参数都是常量，因此编译器可以对此进行验证。 当跨声明与属性时，每个非 AllowMultiple 属性都将被消除重复，并且将只发出一个属性实例。

```cs
public partial bool TryGetValue([NotNullWhen(true)] out object? value);
public partial bool TryGetValue([NotNullWhen(true)] out object? value) { ... } // ok

// equivalent to:
public bool TryGetValue([NotNullWhen(true)] out object value) { ... }

// error when attribute arguments do not match
public partial bool TryGetValue([NotNullWhen(true)] out object? value);
public partial bool TryGetValue([NotNullWhen(false)] out object? value) { ... } // error
```

### <a name="open-questions"></a>打开问题

1. 是否应在 "partial" 类型声明上允许此类重复，或只允许在非类型成员上使用 (例如) 方法？
2. 允许符号上的多个*用法允许 "* 选择启用" 以消除特性的等效用法的特性？

### <a name="design-meetings"></a>设计会议
#### <a name="6th-july-2020"></a>[2020年7月6日](/meetings/2020/LDM-2020-07-06.md#repeated-attributes-on-partial-members)
已接受该建议。
  - 对于分部类型声明，将允许重复非 AllowMultiple 属性， (打开问题 1) 。
  - 重复应用 AllowMultiple 属性的行为不会改变，并且可能会在将来的建议中考虑使用 "选择加入" 机制来消除重复， (打开问题 2) 。

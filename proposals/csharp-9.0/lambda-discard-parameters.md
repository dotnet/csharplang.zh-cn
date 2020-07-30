---
ms.openlocfilehash: bdefce3af78e44136dc2682e9e262cc8d6fae77b
ms.sourcegitcommit: a88d56e3131d7a94c65e637c276379541a3cd491
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/30/2020
ms.locfileid: "87434486"
---
# <a name="lambda-discard-parameters"></a>Lambda 丢弃参数

## <a name="summary"></a>总结

允许使用丢弃（ `_` ）作为 lambda 和匿名方法的参数。
例如：
- lambda： `(_, _) => 0` ，`(int _, int _) => 0`
- 匿名方法：`delegate(int _, int _) { return 0; }`

## <a name="motivation"></a>动机

不需要将未使用的参数命名为。 放弃的意图是清晰的，也就是说，它们未被使用/丢弃。

## <a name="detailed-design"></a>详细设计

[方法参数](https://github.com/dotnet/csharplang/blob/master/spec/classes.md#method-parameters)在使用多个名为的参数的 lambda 或匿名方法的参数列表中 `_` ，此类参数是丢弃参数。
注意：如果将单个参数命名为，则 `_` 它是一个用于向后兼容的常规参数。

丢弃参数不会将任何名称引入任何范围。
请注意，这意味着它们不会导致 `_` 隐藏任何（下划线）名称。

[简单名称](../../spec/expressions.md#simple-names)如果 `K` 为零，并且*simple_name*出现在*块*中，并且*块*的（或封闭*块*的）局部变量声明空间（[声明](../../spec/basic-concepts.md#declarations)）包含局部变量、参数（丢弃参数除外）或具有名称的常量 `I` ，则*simple_name*引用该局部变量、参数或常量，并归类为变量或值。

[作用域](../../spec/basic-concepts.md#scopes)除了丢弃参数外，在*lambda_expression* （[匿名函数表达式](../../spec/expressions.md#anonymous-function-expressions)）中声明的参数的作用域是该*lambda_expression*的*anonymous_function_body* ，但在*anonymous_method_expression*中声明的参数的作用域（[匿名函数表达式](../../spec/expressions.md#anonymous-function-expressions)）是该*anonymous_method_expression*的*块*。

## <a name="related-spec-sections"></a>相关 spec 部分
- [对应的参数](../../spec/expressions.md#corresponding-parameters)

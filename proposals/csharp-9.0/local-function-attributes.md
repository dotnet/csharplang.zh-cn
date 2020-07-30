---
ms.openlocfilehash: e48717f85f781acfb69234c139d06688cd828fcc
ms.sourcegitcommit: a88d56e3131d7a94c65e637c276379541a3cd491
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/30/2020
ms.locfileid: "87434445"
---
# <a name="attributes-on-local-functions"></a>本地函数的属性

## <a name="attributes"></a>属性

本地函数声明现在允许有[属性](../../spec/attributes.md)。 本地函数上的参数和类型参数也允许有属性。

应用于方法、其参数或其类型参数的属性在应用于本地函数、其参数或其类型参数时具有相同的含义。

局部函数可以通过使用将其与[条件方法](../../spec/attributes.md#the-conditional-attribute)进行修饰来成为条件方法 `[ConditionalAttribute]` 。 条件本地函数也必须是 `static` 。 条件方法的所有限制也适用于条件本地函数，包括返回类型必须为 `void` 。

## <a name="extern"></a>部

`extern`现在允许对本地函数使用修饰符。 这使本地函数成为外部[方法](../../spec/classes.md#external-methods)的外部函数。

与外部方法类似，外部本地函数的*本地函数体*必须是分号。 只允许对外部本地函数使用分号*局部函数体*。 

外部本地函数也必须是 `static` 。

## <a name="syntax"></a>语法

按如下所示修改[本地函数语法](../csharp-7.0/local-functions.md#syntax-grammar)：
```
local-function-header
    : attributes? local-function-modifiers? return-type identifier type-parameter-list?
        ( formal-parameter-list? ) type-parameter-constraints-clauses
    ;

local-function-modifiers
    : (async | unsafe | static | extern)*
    ;

local-function-body
    : block
    | arrow-expression-body
    | ';'
    ;
```

---
ms.openlocfilehash: bfdd44c81a40c49b26ac15a69d2327f795bd88e6
ms.sourcegitcommit: 0c25406d8a99064bb85d934bb32ffcf547753acc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/28/2020
ms.locfileid: "87297255"
---
# <a name="target-typed-conditional-expression"></a>目标类型的条件表达式

## <a name="conditional-expression-conversion"></a>条件表达式转换

对于条件表达式 `c ? e1 : e2` ，当

1. 和没有通用类型 `e1` `e2` ，或
2. ，它的通用类型为，但其中一个表达式 `e1` 为 `e2` ，或者没有到该类型的隐式转换

我们定义了一个新的隐式*条件表达式转换*，该转换允许从条件表达式到的任何类型的隐式转换 `T` （从到的转换，以及从到的转换） `e1` `T` `e2` `T` 。  如果条件表达式在和之间既没有通用类型，也不符合 `e1` `e2` *条件表达式转换*，则是错误的。

## <a name="better-conversion-from-expression"></a>表达式的更好转换

我们更改

> #### <a name="better-conversion-from-expression"></a>表达式的更好转换
> 
> 给定了 `C1` 从表达式转换为类型的隐式转换 `E` `T1` ，以及 `C2` 从表达式转换为类型的隐式转换， `E` `T2` `C1` 是比***better conversion*** `C2` `E` 不完全匹配 `T2` 且至少包含以下其中一项的更好的转换：
> 
> * `E`完全匹配 `T1` （[完全匹配的表达式](expressions.md#exactly-matching-expression)）
> * `T1`比更好的转换目标 `T2` （[更好的转换目标](expressions.md#better-conversion-target)）

实例部署到 Windows Azure 虚拟机 (VM) 中的

> #### <a name="better-conversion-from-expression"></a>表达式的更好转换
> 
> 给定了 `C1` 从表达式转换为类型的隐式转换 `E` `T1` ，以及 `C2` 从表达式转换为类型的隐式转换， `E` `T2` `C1` 是比***better conversion*** `C2` `E` 不完全匹配 `T2` 且至少包含以下其中一项的更好的转换：
> 
> * `E`完全匹配 `T1` （[完全匹配的表达式](expressions.md#exactly-matching-expression)）
> * **`C1`不是一个*条件表达式转换*，并且 `C2` 是一个 * 条件表达式转换 * * *。
> * `T1`比 `T2` （[更好的转换目标](expressions.md#better-conversion-target)） * * 更好的转换目标，并且 `C1` 和 `C2` 都是*条件表达式*转换，或者两者都不是 * 条件表达式转换 * * *。

## <a name="cast-expression"></a>Cast 表达式

当前 c # 语言规范显示

> 窗体的*cast_expression* `(T)E` ，其中 `T` 是一个*类型*并且 `E` 是一个*unary_expression*，它执行到类型的值的显式转换（[显式转换](conversions.md#explicit-conversions)） `E` `T` 。

存在*条件表达式转换*时，可能有多个从到的转换可能 `E` `T` 。 通过添加*条件表达式转换*，我们更喜欢将任何其他转换转换为*条件表达式转换*，并将*条件表达式转换*仅用作最后的手段。

## <a name="design-notes"></a>设计纪要

*从表达式转换到更好的转换*的原因是处理以下情况：

```csharp
M(b ? 1 : 2);

void M(short);
void M(long);
```

此方法有两个小缺点。  首先，它与 switch 表达式并不完全相同：

```csharp
M(b ? 1 : 2); // calls M(long)
M(b switch { true => 1, false => 2 }); // calls M(short)
```

这仍是一项重大更改，但其作用域不太可能影响实际程序：

```csharp
M(b ? 1 : 2, 1); // calls M(long, long) without this feature; ambiguous with this feature.

M(short, short);
M(long, long);
```

这种方法变得不明确，因为的转换 `long` 更适合第一个参数（因为它不使用*条件表达式转换*），但转换为 `short` 更适合于第二个参数（因为 `short` 比*更好的转换目标* `long` ）。 此重大更改看起来不太严重，因为它不会在无提示的情况下更改现有程序的行为。

转换表达式中的注释的原因是处理以下情况：

```csharp
_ = (short)(b ? 1 : 2);
```

此程序当前使用从到的显式 `int` 转换 `short` ，并且我们希望保留此程序当前的语言含义。  此更改将在运行时 unobservable，但在以下情况下，更改将可观察：

```csharp
_ = (A)(b ? c : d);
```

其中 `c` 的类型为 `C` `d` ，其类型为 `D` ，并且存在从到的隐式用户定义的 `C` 转换 `D` ， `D` `A` `C` `A` 以及从到的隐式用户定义的转换，以及从到的隐式用户定义的转换。 如果在 c # 9.0 之前编译此代码，则在为 true 时， `b` 我们会将从转换为 `c` `D` `A` 。 如果使用*条件表达式转换*，则当 `b` 为 true 时，将直接从转换 `c` 为 `A` ，这将执行一系列不同的用户代码。 因此，我们将*条件表达式转换*视为转换中的最后一个手段，以保留现有行为。

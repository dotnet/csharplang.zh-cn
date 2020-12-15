---
ms.openlocfilehash: f1ab0e063cc8b7a44c70ea1e52027cda30e064b0
ms.sourcegitcommit: efac0d1e909d222dce5b18b10ae0d0b1d309b757
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/15/2020
ms.locfileid: "97502844"
---
# <a name="variance-safety-for-static-interface-members"></a>静态接口成员的差异安全

## <a name="summary"></a>总结

允许接口中的静态非虚拟成员将其声明中的类型参数视为固定参数，而不考虑它们的声明方差。

## <a name="motivation"></a>动机


- https://github.com/dotnet/csharplang/issues/3275
- https://github.com/dotnet/csharplang/blob/master/meetings/2020/LDM-2020-06-24.md#interface-static-member-variance

在接口成员中考虑了变体 `static` 。 目前，对于在这些成员中使用的共同/逆变类型参数，它们必须遵循完整标准方差规则，从而导致某些不一致，与 `static` 字段的处理方式与 `static` 属性或方法不一致：

```cs
public interface I<out T>
{
    static Task<T> F = Task.FromResult(default(T)); // No problem
    static Task<T> P => Task.FromResult(default(T));   //CS1961
    static Task<T> M() => Task.FromResult(default(T));    //CS1961
    static event EventHandler<T> E; // CS1961
}
```

由于这些成员是 `static` 和非虚拟的，因此不存在任何安全问题：不能通过子类型化接口和重写成员，以某种方式派生更松散/更多受限成员。

## <a name="detailed-design"></a>详细设计

下面是语言规范 (的 Vaiance 安全部分的建议内容 https://github.com/dotnet/csharplang/blob/master/spec/interfaces.md#variance-safety) 。
此更改是添加 "*这些限制不适用于静态成员声明中的 ocurrances 类型"。* 部分开头的语句。 

### <a name="variance-safety"></a>差异安全

类型的类型参数列表中的差异批注的出现次数限制了类型在类型声明内可能出现的位置。
*这些限制不适用于静态成员声明中的 ocurrances 类型。*

`T`如果以下其中一项为，则类型为 ***output-unsafe** _：

_  `T` 是逆变类型参数
*  `T` 是一个带有输出不安全元素类型的数组类型
*  `T` 是从泛型类型构造的接口或委托类型， `S<A1,...,Ak>` `S<X1,...,Xk>` 其中至少 `Ai` 包含以下项之一：
   * `Xi` 是协变或固定，并且 `Ai` 是输出不安全的。
   * `Xi` 为逆变或固定，并且 `Ai` 是输入安全的。
   
`T`如果包含以下内容之一，则类型为 ***输入-不安全** _ _：

_  `T` 是协变类型参数
*  `T` 是具有输入不安全元素类型的数组类型
*  `T` 是从泛型类型构造的接口或委托类型， `S<A1,...,Ak>` `S<X1,...,Xk>` 其中至少 `Ai` 包含以下项之一：
   * `Xi` 是协变或固定的，并且 `Ai` 是输入不安全的。
   * `Xi` 为逆变或固定，并且 `Ai` 是输出不安全的。

在直观的输出位置中禁止使用不安全的输出类型，在输入位置禁止输入不安全类型。

如果类型不是输出安全类型，则为 ***输出安全** 的; 如果不是输入安全的，则为 _ *_输入安全_**。


## <a name="other-considerations"></a>其他注意事项

我们还考虑到这是否可能会干扰我们希望对角色、类型类和扩展进行的其他一些改进。 这一切都应该是正确的：我们将无法 retcon 现有静态成员默认为接口的默认值，因为这会最终成为多个级别的重大更改，即使没有更改此处的变化行为也是如此。

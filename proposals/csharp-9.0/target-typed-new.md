---
ms.openlocfilehash: 01fd4f1bc9e0a5b8ea1473e3c7c00fca80c5c8fb
ms.sourcegitcommit: 06ee75e6e3f1e0d9b4859ffed66024364d3d8f26
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/21/2020
ms.locfileid: "88720426"
---

# <a name="target-typed-new-expressions"></a>目标类型的 `new` 表达式

* [x] 建议
* [x] 原型
* [] 实现
* [] 规范

## <a name="summary"></a>摘要
[summary]: #summary

当类型已知时，不需要构造函数的类型规范。 

## <a name="motivation"></a>动机
[motivation]: #motivation

允许字段初始化，而不复制类型。
```cs
Dictionary<string, List<int>> field = new() {
    { "item1", new() { 1, 2, 3 } }
};
```

如果可从用法推断，则允许省略类型。
```cs
XmlReader.Create(reader, new() { IgnoreWhitespace = true });
```

实例化对象，而不会对类型进行拼写检查。
```cs
private readonly static object s_syncObj = new();
```

## <a name="specification"></a>规格
[design]: #detailed-design

接受新的句法窗体，其中的*类型*为可选项*target_typed_new* *object_creation_expression* 。

```antlr
object_creation_expression
    : 'new' type '(' argument_list? ')' object_or_collection_initializer?
    | 'new' type object_or_collection_initializer
    | target_typed_new
    ;
target_typed_new
    : 'new' '(' argument_list? ')' object_or_collection_initializer?
    ;
```

*Target_typed_new*表达式没有类型。 但是，存在一个新的 *对象创建转换* ，它是从表达式到每个类型的 *target_typed_new* 的隐式转换。

如果是的实例，则给定目标类型 `T` `T0` 为 `T` 的基础类型 `T` `System.Nullable` 。 否则 `T0` 为 `T` 。 转换为类型的 *target_typed_new* 表达式的含义与 `T` 指定为类型的相应 *object_creation_expression* 的含义相同 `T0` 。

如果 *target_typed_new* 用作一元运算符或二元运算符的操作数，或者如果在不受 *对象创建转换*的情况下使用，则会发生编译时错误。

> **打开问题：** 我们是否应允许委托和元组作为目标类型？

上述规则包括 (引用类型) 和元组 (结构类型) 的委托。 尽管这两种类型都是可构造，但如果类型为 inferable，则可以使用匿名函数或元组文本。
```cs
(int a, int b) t = new(1, 2); // "new" is redundant
Action a = new(() => {}); // "new" is redundant

(int a, int b) t = new(); // OK; same as (0, 0)
Action a = new(); // no constructor found
```

### <a name="miscellaneous"></a>其他

下面是该规范的结果：

- `throw new()` (目标类型，则允许 `System.Exception`) 
- `new`不允许将目标类型与二元运算符一起使用。
- 没有目标类型时，不允许这样做：一元运算符，在中的 `foreach` `using` 析构，在表达式中，作为匿名类型属性 () ，在语句中，在语句中，在语句中，在语句中，在语句中的 `await` `new { Prop = new() }` `lock` `sizeof` `fixed` 成员访问 (`new().field`) 中 `someDynamic.Method(new())` `is` `??` ，作为运算符的操作数，在 LINQ (查询中，作为运算符的左操作数，.。。
- 它也不允许作为 `ref` 。
- 不允许将以下类型作为转换的目标
  - **枚举类型：** `new()` (为 `new Enum()`) 指定默认值，而 `new(1)` 不会起作用，因为枚举类型没有构造函数。
  - **接口类型：** 它的工作方式与 COM 类型对应的创建表达式相同。
  - **数组类型：** 数组需要一个特殊的语法来提供长度。    
  - **动态：** 我们不允许 `new dynamic()` ，因此不允许 `new()` `dynamic` 作为目标类型。
  - **元组：** 它们与使用基础类型创建对象的含义相同。
  - 还会排除 *object_creation_expression* 中不允许的所有其他类型，例如，指针类型。   

## <a name="drawbacks"></a>缺点
[drawbacks]: #drawbacks

对于目标类型化 `new` 创建新类别的重大更改，存在一些问题，但我们已使用 `null` 和，但 `default` 这并不是一个重要问题。

## <a name="alternatives"></a>备选项
[alternatives]: #alternatives

大多数关于字段初始化中太长时间的投诉都是关于类型 *参数* ，而不是类型本身，我们只能推断 `new Dictionary(...)` (或类似) 类型参数，并在本地从参数或集合初始值设定项推断类型参数。

## <a name="questions"></a>问题
[questions]: #questions

- 是否应在表达式树中禁止使用情况？  (no) 
- 此功能如何与 `dynamic` 参数交互？  (无特殊处理) 
- IntelliSense 应如何操作 `new()` ？ 仅当存在单个目标类型时才 () 

## <a name="design-meetings"></a>设计会议

- [LDM-2017-10-18](https://github.com/dotnet/csharplang/blob/master/meetings/2017/LDM-2017-10-18.md#100)
- [LDM-2018-05-21](https://github.com/dotnet/csharplang/blob/master/meetings/2018/LDM-2018-05-21.md)
- [LDM-2018-06-25](https://github.com/dotnet/csharplang/blob/master/meetings/2018/LDM-2018-06-25.md)
- [LDM-2018-08-22](https://github.com/dotnet/csharplang/blob/master/meetings/2018/LDM-2018-08-22.md#target-typed-new)
- [LDM-2018-10-17](https://github.com/dotnet/csharplang/blob/master/meetings/2018/LDM-2018-10-17.md)
- [LDM-2020-03-25](https://github.com/dotnet/csharplang/blob/master/meetings/2020/LDM-2020-03-25.md)

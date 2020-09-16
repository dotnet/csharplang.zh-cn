---
ms.openlocfilehash: 904efa11b08027f45243974e5a25c9957daca5a3
ms.sourcegitcommit: fcf884f7f8a2c2c7d925a67c2a7fad22e09b9506
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/16/2020
ms.locfileid: "90682278"
---
# <a name="compile-time-enforcement-of-safety-for-ref-like-types"></a>对类似引用类型执行安全编译的时间

## <a name="introduction"></a>简介

处理类型（如和）时其他安全规则的主要原因 `Span<T>` `ReadOnlySpan<T>` 是，此类类型必须局限于执行堆栈。
 
`Span<T>`和类似类型必须是仅包含堆栈的类型有两个原因。

1. `Span<T>` 在语义上是包含引用和范围的结构 `(ref T data, int length)` 。 不考虑实际的实现，因此，对此类结构的写入将不是原子的。 此类结构的并发 "撕裂" 将导致与 `length` 不匹配的可能性 `data` ，从而导致超出范围的访问和类型安全冲突，最终可能导致垃圾回收堆在看似 "安全" 的代码中损坏。
2. 的某些实现 `Span<T>` 实际上在它的一个字段中包含托管指针。 不支持将托管指针作为堆对象的字段，管理用于将托管指针放置在 GC 堆上的代码通常会在 JIT 时崩溃。

如果的实例 `Span<T>` 被约束为仅存在于执行堆栈中，则上述所有问题都将会缓解。 

由于撰写导致的其他问题。 通常需要构建更复杂的数据类型，这些数据类型将嵌入 `Span<T>` 和 `ReadOnlySpan<T>` 实例。 此类复合类型必须是结构，并将共享的所有风险和要求 `Span<T>` 。 因此，应查看此处所述的安全规则，使其适用于 **_类似于引用类型_** 的整个范围。

[草稿语言规范](#draft-language-specification)旨在确保仅在堆栈上出现类似于引用类型的值。

## <a name="generalized-ref-like-types-in-source-code"></a>`ref-like`源代码中的通用类型

`ref-like` 使用修饰符在源代码中显式标记结构 `ref` ：

```csharp
ref struct TwoSpans<T>
{
    // can have ref-like instance fields
    public Span<T> first;
    public Span<T> second;
} 

// error: arrays of ref-like types are not allowed. 
TwoSpans<T>[] arr = null;

```

将结构指定为同类引用后，该结构将允许该结构具有类似于引用的实例字段，并且还会使与该结构相关的 ref 类型的所有要求。 

## <a name="metadata-representation-or-ref-like-structs"></a>元数据表示形式或类似于引用的结构

类似于引用的结构将用 **system.runtime.compilerservices. IsRefLikeAttribute** 属性进行标记。

该特性将添加到公共基库，如 `mscorlib` 。 在这种情况下，如果该属性不可用，编译器将生成一个与其他嵌入的按需属性类似的内部属性，如 `IsReadOnlyAttribute` 。

为了防止在编译器中使用与安全规则不熟悉的类似于引用的结构，将采用其他措施， (这包括在此功能实现) 之前的 c # 编译器。 

如果没有其他适用于旧编译器的替代方法，不提供服务，则 `Obsolete` 会向所有类似于引用的结构中添加具有已知字符串的属性。 知道如何使用类似引用类型的编译器将忽略此特定形式的 `Obsolete` 。

典型的元数据表示形式：

```csharp
    [IsRefLike]
    [Obsolete("Types with embedded references are not supported in this version of your compiler.")]
    public struct TwoSpans<T>
    {
       // . . . .
    }
```

注意：不是这样做的目标，因此在旧编译器上使用类似于引用的类型会导致100%。 这并不是必需的。 例如，总会有一种方法可以绕 `Obsolete` 过动态代码，例如通过反射创建类似于引用类型的数组。

特别是，如果用户想要 `Obsolete` `Deprecated` 在类似于引用的类型上实际放置或属性，则除了不发出预定义的类型之外，不会进行其他选择，因为 `Obsolete` 属性不能应用多次。  

## <a name="examples"></a>示例：

```csharp
SpanLikeType M1(ref SpanLikeType x, Span<byte> y)
{
    // this is all valid, unconcerned with stack-referring stuff
    var local = new SpanLikeType(y);
    x = local;
    return x;
}

void Test1(ref SpanLikeType param1, Span<byte> param2)
{
    Span<byte> stackReferring1 = stackalloc byte[10];
    var stackReferring2 = new SpanLikeType(stackReferring1);

    // this is allowed
    stackReferring2 = M1(ref stackReferring2, stackReferring1);

    // this is NOT allowed
    stackReferring2 = M1(ref param1, stackReferring1);

    // this is NOT allowed
    param1 = M1(ref stackReferring2, stackReferring1);

    // this is NOT allowed
    param2 = stackReferring1.Slice(10);

    // this is allowed
    param1 = new SpanLikeType(param2);

    // this is allowed
    stackReferring2 = param1;
}

ref SpanLikeType M2(ref SpanLikeType x)
{
    return ref x;
}

ref SpanLikeType Test2(ref SpanLikeType param1, Span<byte> param2)
{
    Span<byte> stackReferring1 = stackalloc byte[10];
    var stackReferring2 = new SpanLikeType(stackReferring1);

    ref var stackReferring3 = M2(ref stackReferring2);

    // this is allowed
    stackReferring3 = M1(ref stackReferring2, stackReferring1);

    // this is allowed
    M2(ref stackReferring3) = stackReferring2;

    // this is NOT allowed
    M1(ref param1) = stackReferring2;

    // this is NOT allowed
    param1 = stackReferring3;

    // this is NOT allowed
    return ref stackReferring3;

    // this is allowed
    return ref param1;
}

```

----------------

## <a name="draft-language-specification"></a>草稿语言规范

下面介绍了一组类似于引用类型的安全规则 (`ref struct` s) ，以确保这些类型的值仅出现在堆栈上。 如果无法通过引用传递局部变量，则可以使用一组不同的更简单的安全规则。 此规范还允许重新指定 ref 局部变量。

### <a name="overview"></a>概述

在编译时，与每个表达式相关联的是允许该表达式转义的范围的概念，即 "安全到转义"。 同样，对于每个左值，我们都将保留对其引用范围的概念，以允许对其进行转义，即 "引用安全到转义"。 对于给定的左值表达式，这些表达式可能不同。

它们类似于引用局部变量功能的 "安全返回"，但更细化。 如果表达式的 "安全到返回" 只记录 (或不) 它可以将封闭方法作为一个整体进行转义，则安全对转义的记录可能会被转义，以 (它不会被转义的范围) 。 基本安全机制按如下方式强制执行。 给定从具有安全到转义范围 S1 的 expression E1 到 (左值的赋值) 使用安全到转义范围 S2 的 expression E2，如果 S2 比 S1 更广泛，则是错误的。 按照构造，两个作用域 S1 和 S2 在一个嵌套关系中，因为合法表达式始终是从包含表达式的某些范围中恢复为安全的。

在这种情况下，为了进行分析，只支持两个范围：外部到方法和方法的顶级范围。 这是因为无法创建具有内部范围的类似引用的值，并且引用局部变量不支持重新赋值。 不过，这些规则可支持两个以上的作用域级别。

若要计算表达式的 *安全恢复返回* 状态和管理表达式合法性的规则，请遵循。

### <a name="ref-safe-to-escape"></a>引用安全-转义

*Ref safe 到 escape*是一个范围，其中包含左值表达式，对 lvalue 的引用可以安全地转义给它。 如果该作用域是整个方法，我们说到左值的引用可以 *安全地* 从方法返回。

### <a name="safe-to-escape"></a>安全到转义

*安全 to escape*是一个包含表达式的范围，其中的值可以安全地转义。 如果该作用域是整个方法，我们就会说，从方法返回值是 *安全* 的。

类型不是类型的表达式 `ref struct` 是从整个封闭方法中 *安全返回* 的。 否则，请参阅下面的规则。

#### <a name="parameters"></a>参数

指定形参的左值是引用 *安全对转义* (按引用) ，如下所示：
- 如果参数是 `ref` 、 `out` 或 `in` 参数，则它是对整个方法的 *ref 安全转义* (例如，通过 `return ref` 语句) ; 否则为。
- 如果该参数是 `this` 结构类型的参数，则它是对该 (方法的顶级范围的 *引用是安全* 的，而不是从整个方法自身) ; [示例](#struct-this-escape)
- 否则，该参数是一个值参数，它对方法的顶级范围是 *引用安全* 的，而不是从方法本身)  (。

指定形参使用的右值是 *安全对转义* (的表达式，) 从整个方法 (例如通过 `return` 语句) 。 这也适用于 `this` 参数。

#### <a name="locals"></a>局部变量

按如下方式引用指定局部变量的左值 (引用 *安全*) ：
- 如果该变量是一个 `ref` 变量，则它的 *ref 安全到转义* 将从其初始化表达式的 *ref 安全对转义* ; 否则为。
- 变量是 *引用安全* 的，可对它的声明范围进行转义。

指定局部变量使用的右值表达式是 (按以下方式进行 *安全的转义*) ：
- 但上面的一般规则是，其类型不是类型的本地 `ref struct` 是从整个封闭方法中 *安全返回* 的类型。
- 如果该变量是循环的迭代变量 `foreach` ，则该变量的 *safe 到 escape* 范围与该循环的表达式的 *安全转义* `foreach` 。
- 类型为的局部 `ref struct` 在声明点上未初始化，是从整个封闭方法中 *安全返回* 的。
- 否则，该变量的类型为 `ref struct` 类型，该变量的声明需要初始值设定项。 变量的 *安全到转义* 的范围与它的初始值设定项的 *安全转义* 。

#### <a name="field-reference"></a>字段引用

用于指定对字段的引用的左 `e.F` 值，是引用 *安全对转义* (按引用) ，如下所示：
- 如果 `e` 为引用类型，则它是对整个方法的 *引用安全* 的引用; 否则为。
- 如果 `e` 是值类型，则将从的*ref 安全到转义*获取它的*ref 安全到转义* `e` 。

指定对字段的引用的右 `e.F` 值具有 *安全到转义* 的范围，该范围与的 *安全转义* 范围相同 `e` 。

#### <a name="operators-including-"></a>运算符包括 `?:`

用户定义的运算符的应用程序被视为方法调用。

对于生成右值的运算符（如 `e1 + e2` 或）， `c ? e1 : e2` 结果的 *安全对转义* 是运算符的操作数的 *安全转义* 的最小范围。  因此，对于产生右值的一元运算符（如），结果的 `+e` *安全对* 转义是操作数的 *安全对转义* 。

对于生成左值的运算符，例如 `c ? ref e1 : ref e2`
- 结果的 *ref 安全对转义* 是运算符的操作数的 *ref 安全到转义* 中的最小范围。
- 操作数的 *安全对转义* 必须一致，这是生成的左值的 *安全转义* 。

#### <a name="method-invocation"></a>方法调用

由引用返回的方法调用生成的左 `e1.M(e2, ...)` 值是 *引用安全* 的最小作用域：
- 整个封闭方法
- *ref-safe-to-escape* `ref` (排除接收方的所有和自变量表达式的引用安全对转义 `out`) 
- 对于方法的每个 `in` 参数，如果有一个对应的表达式是左值，则为它的 *ref 安全到转义*，否则为最接近的封闭范围
- 所有参数表达式的 *安全对转义* (包括接收方) 

> 注意：必须具有最后一个项目符号才能处理代码，如
> ```csharp
> var sp = new Span(...)
> return ref sp[0];
> ```
> 或
> ```csharp
> return ref M(sp, 0);
> ```

从以下范围的最小值开始，从方法调用生成的右 `e1.M(e2, ...)` 值是 *安全地转义* ：
- 整个封闭方法
- 所有参数表达式的 *安全对转义* (包括接收方) 

#### <a name="an-rvalue"></a>右值
右值是从最近的封闭范围中 *引用安全的引用* 。 例如，在的调用中， `M(ref d.Length)` 其中 `d` 的类型为 `dynamic` 。 它还与 (和可能的 subsumes) 与参数相对应的参数处理方式一致 `in` 。

#### <a name="property-invocations"></a>属性调用

属性调用 (`get` 或 `set`) 被上述规则视为基础方法的方法调用。

#### `stackalloc`

Stackalloc 表达式是对 (方法的顶级范围进行 *安全转义* 的右值，而不是从整个方法自身) 。

#### <a name="constructor-invocations"></a>构造函数调用

`new`调用构造函数的表达式服从与被视为返回正在构造的类型的方法调用相同的规则。

此外，如果存在初始值设定项，则不会以递归方式在对象初始值设定项表达式的所有参数/操作数的最小*转义*范围内进行*安全到*转义。 

#### <a name="span-constructor"></a>Span 构造函数
语言依赖于 `Span<T>` 没有以下形式的构造函数：

```csharp
void Example(ref int x)
{
    // Create a span of length one
    var span = new Span<int>(ref x); 
}
```

此类构造函数使 `Span<T>` 使用的字段不能与字段区分开来 `ref` 。 本文档中所述的安全规则依赖于 `ref` 非 c # 或 .net 中的有效构造。

#### <a name="default-expressions"></a>`default` 表达式

`default`表达式是从整个封闭方法中*安全地转义*的。

## <a name="language-constraints"></a>语言约束

我们希望确保没有任何 `ref` 本地变量，并且不是类型的变量 `ref struct` ，而是指堆栈内存或不再处于活动状态的变量。 因此，我们具有以下语言约束：

- 不能将 ref 参数和引用本地，也不能将参数或局部 `ref struct` 类型提升为 lambda 或局部函数。

- 引用参数或类型的参数既不 `ref struct` 能是迭代器方法的参数，也不能是 `async` 方法。

- 引用本地和类型的本地都不能位于 `ref struct` 语句或表达式点的范围内 `yield return` `await` 。

- `ref struct`类型不能用作类型参数或元组类型中的元素类型。

- `ref struct`类型可能不是字段的声明类型，只是它可能是另一个的实例字段的声明类型 `ref struct` 。

- `ref struct`类型不能是数组的元素类型。

- 类型的值 `ref struct` 不能装箱：
  - 不存在从 `ref struct` 类型到类型或类型的转换 `object` `System.ValueType` 。
  - `ref struct`类型不能声明为实现任何接口
  - 在或的中未声明的任何实例方法 `object` `System.ValueType` 均可 `ref struct` 使用该类型的接收方调用 `ref struct` 。
  - 不 `ref struct` 能通过方法转换为委托类型来捕获类型的任何实例方法。

- 对于 ref 重新分配 `ref e1 = ref e2` ，的 *ref 安全到转义* 的 `e2` 范围必须至少与的 *引用安全对* 的范围相同 `e1` 。

- 对于 ref 返回语句 `return ref e1` ， *ref-safe-to-escape* `e1` 必须从整个方法中*引用*安全对的引用是安全的。  (TODO：我们还需要一个规则，该规则 `e1` 必须对整个方法是 *安全* 的，也可能是冗余的？ ) 

- 对于 return 语句 `return e1` ，对的 *安全到转义* `e1` 必须是对整个方法的 *安全转义* 。

- 对于分配 `e1 = e2` ，如果的类型 `e1` 是 `ref struct` 类型，则的 *安全对转义* `e2` 必须至少与的 *安全对转义* 作用域 `e1` 的范围。

- 对于方法调用（如果存在类型为的 `ref` 或 `out` 参数 `ref struct` (包括接收方) （包含 *安全对 escape* E1），则不 (包含) 接收方的参数的 *安全对转义* 比 E1 更小。 示例 

- 本地函数或匿名函数不能引用 `ref struct` 封闭范围内声明的类型的局部类型或参数。

> ***打开问题：*** 我们需要一些规则，当需要在 await 表达式中溢出某个类型的堆栈值时 `ref struct` （例如，在代码中），我们可以生成错误
> ```csharp
> Foo(new Span<int>(...), await e2);
> ```

## <a name="explanations"></a>方便
这些说明和示例有助于解释上述许多安全规则存在的原因

### <a name="method-arguments-must-match"></a>方法参数必须匹配
如果调用的方法中有一个 `out` （ `ref` 包括接收方），则 `ref struct` `ref struct` 需要具有相同的生存期。 这是必需的，因为 c # 必须基于方法签名中提供的信息和调用站点上值的生存期，来围绕生存期安全做出所有决策。 

如果有 `ref` 一些参数，则 `ref struct` 这些参数可以在其内容周围交换问题。 因此，在调用站点，我们必须确保所有这些 **可能** 的交换都是兼容的。 如果该语言未强制执行该语言，则会允许错误的代码，如下所示。

```csharp
void M1(ref Span<int> s1)
{
    Span<int> s2 = stackalloc int[1];
    Swap(ref s1, ref s2);
}

void Swap(ref Span<int> x, ref Span<int> y)
{
    // This will effectively assign the stackalloc to the s1 parameter and allow it
    // to escape to the caller of M1
    ref x = ref y; 
}
```

对接收器的限制是必要的，因为它的任何内容都不是引用安全的，它可以存储提供的值。 这意味着，使用不匹配的生存期，可以通过以下方式创建类型安全漏洞：

```csharp
ref struct S
{
    public Span<int> Span;

    public void Set(Span<int> span)
    {
        Span = span;
    }
}

void Broken(ref S s)
{
    Span<int> span = stackalloc int[1];

    // The result of a stackalloc is now stored in s.Span and escaped to the caller
    // of Broken
    s.Set(span); 
}
```

### <a name="struct-this-escape"></a>Struct This Escape
当涉及到跨安全规则时， `this` 实例成员中的值将建模为成员的参数。 现在， `struct` 在中，的类型 `this` 实际上是 `ref S` `class` `S` (名为) 的成员 `class / struct` 。 

还有 `this` 不同于其他参数的转义规则 `ref` 。 具体而言，它不是引用安全的，而是其他参数：

```csharp
ref struct S
{ 
    int Field;

    // Illegal because `this` isn't safe to escape as ref
    ref int Get() => ref Field;

    // Legal
    ref int GetParam(ref int p) => ref p;
}
```

此限制的原因实际上与成员调用几乎没什么用 `struct` 。 对于在其上， `struct` 接收方为右值的成员调用，需要遵循一些规则。 但这非常易学。 

此限制的原因实际上就是接口调用。 具体而言，它会出现在下面的示例中是否应编译;

```csharp
interface I1
{
    ref int Get();
}

ref int Use<T>(T p)
    where T : I1
{
    return ref p.Get();
}
```

请考虑 `T` 实例化为的情况 `struct` 。 如果 `this` 参数为 ref-safe 到 escape，则返回的 `p.Get` 可能指向堆栈 (具体而言，它可以是) 实例化类型内的字段 `T` 。 这意味着，语言不允许此示例编译，因为它可以将返回 `ref` 到堆栈位置。 另一方面，如果不 `this` 是引用安全的到转义 `p.Get` ，则不能引用堆栈，因此可以安全返回。 

这就是为什么中的 escapability `this` `struct` 实际上就是关于接口的原因。 绝对可以正常工作，但它已经有了折衷。 设计最终会使接口更灵活。 

但在今后，我们可能会放松这一点。 

## <a name="future-considerations"></a>未来的注意事项

### <a name="length-one-spant-over-ref-values"></a>覆盖值的长度为 1 \<T>
虽然目前不合法，但在某些情况下，在一个值上创建一个实例的长度会有 `Span<T>` 好处：

```csharp
void RefExample()
{
    int x = ...;

    // Today creating a length one Span<int> requires a stackalloc and a new 
    // local
    Span<int> span1 = stackalloc [] { x };
    Use(span1);
    x = span1[0]; 

    // Simpler to just allow length one span
    var span2 = new Span<int>(ref x);
    Use(span2);
}
```

如果我们对 [固定大小的缓冲区](https://github.com/dotnet/csharplang/blob/master/proposals/fixed-sized-buffers.md) 提出限制，则此功能将变得更加引人注目，因为这样可以获得更 `Span<T>` 大长度的实例。 

如果需要关闭此路径，则该语言可通过确保此类 `Span<T>` 实例仅向下来满足此要求。 这就是，它们只是对创建它们的范围而言是 *安全* 的。 这确保了语言决不需要考虑 `ref` 通过的 `ref struct` 返回或字段来转义方法的值 `ref struct` 。 这可能还需要进一步的更改以 `ref` 通过这种方式来识别此类构造函数。

---
ms.openlocfilehash: 6a641cf1b733156ce6ae0ee247c0e5aa7d990d2d
ms.sourcegitcommit: c3df20406f43fcd460cfedd1cd61b6cc47d27250
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/08/2020
ms.locfileid: "89554638"
---
# <a name="function-pointers"></a>函数指针

## <a name="summary"></a>总结

此建议提供的语言构造提供当前无法有效访问或根本不能在 c # 中访问的 IL 操作码： `ldftn` 和 `calli` 。 这些 IL 操作码在高性能代码中非常重要，开发人员需要一种高效的访问方式。

## <a name="motivation"></a>动机

下面的问题中描述了此功能的动机和背景， (方式是功能) 的可能实现：

https://github.com/dotnet/csharplang/issues/191

这是[编译器内部函数](https://github.com/dotnet/csharplang/blob/master/proposals/intrinsics.md)的替代设计建议

## <a name="detailed-design"></a>详细设计

### <a name="function-pointers"></a>函数指针

该语言将允许使用语法声明函数指针 `delegate*` 。 在下一节中详细介绍了完整的语法，但它应类似于 `Func` 和类型声明所使用的语法 `Action` 。

``` csharp
unsafe class Example {
    void Example(Action<int> a, delegate*<int, void> f) {
        a(42);
        f(42);
    }
}
```

这些类型使用 ECMA-335 中所述的函数指针类型来表示。 这意味着对的调用将 `delegate*` 使用对 `calli` `delegate` 方法使用的调用 `callvirt` `Invoke` 。
当然，对于这两种构造，调用是相同的。

方法指针的 ECMA-335 定义包含调用约定，作为类型签名的一部分 (节 7.1) 。
默认调用约定将为 `managed` 。 通过 `unmanaged` 将关键字叫到 `delegate*` 将使用运行时平台默认的语法，可以指定非托管调用约定。 然后 `unmanaged` ，可以通过在命名空间中指定以命名空间开头的任何类型，在括号中指定特定的非托管约定 `CallConv` `System.Runtime.CompilerServices` ，而不是 `CallConv` 前缀。 这些类型必须来自程序的核心库，并且一组有效的组合依赖于平台。

``` csharp
//This method has a managed calling convention. This is the same as leaving the managed keyword off.
delegate* managed<int, int>;

// This method will be invoked using whatever the default unmanaged calling convention on the runtime
// platform is. This is platform and architecture dependent and is determined by the CLR at runtime.
delegate* unmanaged<int, int>;

// This method will be invoked using the cdecl calling convention
// Cdecl maps to System.Runtime.CompilerServices.CallConvCdecl
delegate* unmanaged[Cdecl] <int, int>;

// This method will be invoked using the stdcall calling convention, and suppresses GC transition
// Stdcall maps to System.Runtime.CompilerServices.CallConvStdcall
// SuppressGCTransition maps to System.Runtime.CompilerServices.CallConvSuppressGCTransition
delegate* unmanaged[Stdcall, SuppressGCTransition] <int, int>;
```

类型之间的转换 `delegate*` 是根据其签名（包括调用约定）完成的。

``` csharp
unsafe class Example {
    void Conversions() {
        delegate*<int, int, int> p1 = ...;
        delegate* managed<int, int, int> p2 = ...;
        delegate* unmanaged<int, int, int> p3 = ...;

        p1 = p2; // okay p1 and p2 have compatible signatures
        Console.WriteLine(p2 == p1); // True
        p2 = p3; // error: calling conventions are incompatible
    }
}
```

`delegate*`类型是指针类型，这意味着它具有标准指针类型的所有功能和限制：

- 仅在上下文中有效 `unsafe` 。
- `delegate*`只能从上下文调用包含参数或返回类型的方法 `unsafe` 。
- 不能转换为 `object` 。
- 不能用作泛型参数。
- 可隐式转换 `delegate*` 为 `void*` 。
- 可以从显式转换 `void*` 为 `delegate*` 。

限制：

- 自定义特性不能应用于 `delegate*` 或它的任何元素。
- `delegate*`不能将参数标记为`params`
- `delegate*`类型具有正常指针类型的所有限制。
- 指针算法不能直接对函数指针类型执行。

### <a name="function-pointer-syntax"></a>函数指针语法

完整的函数指针语法由以下语法表示：

```antlr
pointer_type
    : ...
    | funcptr_type
    ;

funcptr_type
    : 'delegate' '*' calling_convention_specifier? '<' funcptr_parameter_list funcptr_return_type '>'
    ;

calling_convention_specifier
    : 'managed'
    | 'unmanaged' ('[' unmanaged_calling_convention ']')?
    ;

unmanaged_calling_convention
    : 'Cdecl'
    | 'Stdcall'
    | 'Thiscall'
    | 'Fastcall'
    | identifier (',' identifier)*
    ;

funptr_parameter_list
    : (funcptr_parameter ',')*
    ;

funcptr_parameter
    : funcptr_parameter_modifier? type
    ;

funcptr_return_type
    : funcptr_return_modifier? return_type
    ;

funcptr_parameter_modifier
    : 'ref'
    | 'out'
    | 'in'
    ;

funcptr_return_modifier
    : 'ref'
    | 'ref readonly'
    ;
```

如果未 `calling_convention_specifier` 提供，则默认值为 `managed` 。 在 `calling_convention_specifier` `identifier` `unmanaged_calling_convention` [调用约定的元数据表示形式](#metadata-representation-of-calling-conventions)中介绍了的精确元数据编码以及在中有效的。

``` csharp
delegate int Func1(string s);
delegate Func1 Func2(Func1 f);

// Function pointer equivalent without calling convention
delegate*<string, int>;
delegate*<delegate*<string, int>, delegate*<string, int>>;

// Function pointer equivalent with calling convention
delegate* managed<string, int>;
delegate*<delegate* managed<string, int>, delegate*<string, int>>;
```

### <a name="function-pointer-conversions"></a>函数指针转换

在不安全的上下文中，将扩展 (隐式转换) 的可用隐式转换集，使其包含以下隐式指针转换：
- [_现有转换_](https://github.com/dotnet/csharplang/blob/master/spec/unsafe-code.md#pointer-conversions)
- 如果满足以下所有条件，则从 _funcptr \_ 类型_ `F0` 到另一 _funcptr \_ 类型_ `F1` ：
    - `F0` 和 `F1` 具有相同数量的参数，中的每个参数与 `D0n` `F0` `ref` `out` `in` 中的相应参数具有相同的、或修饰符 `D1n` `F1` 。
    - 对于每个 value 参数 (没有 `ref` 、 `out` 或 `in` 修饰符) 的参数，存在从中的参数类型 `F0` 到中的相应参数类型的标识转换、隐式引用转换或隐式指针转换 `F1` 。
    - 对于每个 `ref` 、 `out` 或 `in` 参数，中的参数类型与 `F0` 中相应的参数类型相同 `F1` 。
    - 如果返回类型是通过值 (no `ref` 或 `ref readonly`) 、标识、隐式引用或隐式指针转换从的返回类型 `F1` 到的返回类型 `F0` 。
    - 如果返回类型是按引用 (`ref` 或 `ref readonly`) ，则的返回类型和修饰符与的 `ref` `F1` 返回类型和 `ref` 修饰符相同 `F0` 。
    - 的调用约定与的 `F0` 调用约定相同 `F1` 。

### <a name="allow-address-of-to-target-methods"></a>允许目标方法的地址

现在将允许方法组作为表达式的参数。 此类表达式的类型将为， `delegate*` 它具有目标方法的等效签名和托管调用约定：

``` csharp
unsafe class Util {
    public static void Log() { }

    void Use() {
        delegate*<void> ptr1 = &Util.Log;

        // Error: type "delegate*<void>" not compatible with "delegate*<int>";
        delegate*<int> ptr2 = &Util.Log;
   }
}
```

在不安全的上下文中， `M` `F` 如果满足以下所有条件，则方法与函数指针类型兼容：
- `M` 和 `F` 具有相同数量的参数，中的每个参数与 `M` `ref` `out` `in` 中的相应参数具有相同的、或修饰符 `F` 。
- 对于每个 value 参数 (没有 `ref` 、 `out` 或 `in` 修饰符) 的参数，存在从中的参数类型 `M` 到中的相应参数类型的标识转换、隐式引用转换或隐式指针转换 `F` 。
- 对于每个 `ref` 、 `out` 或 `in` 参数，中的参数类型与 `M` 中相应的参数类型相同 `F` 。
- 如果返回类型是通过值 (no `ref` 或 `ref readonly`) 、标识、隐式引用或隐式指针转换从的返回类型 `F` 到的返回类型 `M` 。
- 如果返回类型是按引用 (`ref` 或 `ref readonly`) ，则的返回类型和修饰符与的 `ref` `F` 返回类型和 `ref` 修饰符相同 `M` 。
- 的调用约定与的 `M` 调用约定相同 `F` 。 这包括调用约定位，以及非托管标识符中指定的任何调用约定标志。
- `M` 是静态方法。

在不安全的上下文中，如果包含的表达式的目标为方法组，而该表达式的目标为方法组，则该表达式的目标为方法组， `E` `F` 前提是至少有 `E` 一个方法适用于通过使用的参数类型和修饰符构造的参数列表 `F` ，如下所述。
- 选择一个方法，该方法 `M` 对应于窗体的方法调用 `E(A)` ，其中包含以下修改：
   - 参数列表 `A` 是一个表达式列表，每个表达式都分类为一个变量，并使用类型和修饰符 (`ref` 、或的 `out` `in` 相应 _funcptr \_ 参数 \_ 列表_ 的) `F` 。
   - 候选方法只是那些适用于其普通窗体的方法，而不是以其展开形式适用的方法。
   - 候选方法仅为静态方法。
- 如果重载决策的算法产生错误，则会发生编译时错误。 否则，该算法将生成单个最佳方法， `M` 该方法的参数数目与 `F` 相同，转换被视为存在。
- 所选的方法 `M` 必须与) 与函数指针类型一起定义 (兼容 `F` 。 否则，将发生编译时错误。
- 转换的结果是类型的函数指针 `F` 。

这意味着开发人员可以依赖重载决策规则与地址运算符结合使用：

``` csharp
unsafe class Util {
    public static void Log() { }
    public static void Log(string p1) { }
    public static void Log(int i) { };

    void Use() {
        delegate*<void> a1 = &Log; // Log()
        delegate*<int, void> a2 = &Log; // Log(int i)

        // Error: ambiguous conversion from method group Log to "void*"
        void* v = &Log;
    }
```

将使用指令实现的地址为的运算符 `ldftn` 。

此功能的限制：

- 仅适用于标记为的方法 `static` 。
- 非 `static` 本地函数不能在中使用 `&` 。 这些方法的实现细节特意不由语言指定。 这包括它们是静态的还是实例的，或者是否与它们一起发出的签名完全相同。


### <a name="operators-on-function-pointer-types"></a>函数指针类型上的运算符

将按如下所示修改不安全代码中的部分：

> 在不安全的上下文中，有多个构造可用于在 \_ 不 _funcptr type_s 的所有 _pointer type_s 上运行 \_ ：
>
> *  `*`运算符可用于 ([指针间接](../../spec/unsafe-code.md#pointer-indirection)寻址) 执行指针间接寻址。
> *  `->`运算符可用于通过指针访问结构的成员 ([指针成员访问](../../spec/unsafe-code.md#pointer-member-access)) 。
> *  `[]`运算符可用于索引指针 ([指针元素访问](../../spec/unsafe-code.md#pointer-element-access)) 。
> *  `&`运算符可用于获取 () [地址的](../../spec/unsafe-code.md#the-address-of-operator)变量的地址。
> *  `++`和 `--` 运算符可用于递增和递减指针 ([指针增量和减量](../../spec/unsafe-code.md#pointer-increment-and-decrement)) 。
> *  `+`和 `-` 运算符可用于 ([指针算法](../../spec/unsafe-code.md#pointer-arithmetic)) 执行指针算法。
> *  `==` `!=` 可以使用、、、、 `<` `>` `<=` 和 `=>` 运算符来比较指针)  ([指针比较](../../spec/unsafe-code.md#pointer-comparison)。
> *  `stackalloc`运算符可用于从调用堆栈 ([固定大小缓冲区](../../spec/unsafe-code.md#fixed-size-buffers)) 分配内存。
> *  `fixed`语句可用于临时修复变量，因此可以 ([fixed 语句](../../spec/unsafe-code.md#the-fixed-statement)) 获取其地址。
> 
> 在不安全的上下文中，有多个构造可用于在所有 _funcptr type_s 上操作 \_ ：
> *  `&`运算符可用于获取静态方法的地址 ([允许地址的目标方法](#allow-address-of-to-target-methods)) 
> *  `==` `!=` 可以使用、、、、 `<` `>` `<=` 和 `=>` 运算符来比较指针)  ([指针比较](../../spec/unsafe-code.md#pointer-comparison)。

此外，我们修改中的所有部分 `Pointers in expressions` 以禁止函数指针类型（和除外） `Pointer comparison` `The sizeof operator` 。

### <a name="better-function-member"></a>更好的函数成员

更好的函数成员规范将更改为包含以下行：

> `delegate*`比`void*`

这意味着，可以在和上重载， `void*` `delegate*` 但仍揭示使用地址运算符。

## <a name="metadata-representation-of-in-out-and-ref-readonly-parameters-and-return-types"></a>`in`、 `out` 、 `ref readonly` 参数和返回类型的元数据表示形式

函数指针签名没有参数标志位置，因此，我们必须使用 modreqs 对参数和返回类型是 `in` 、 `out` 还是进行编码 `ref readonly` 。

### `in`

将 `System.Runtime.InteropServices.InAttribute` 作为 `modreq` 参数或返回类型上的 ref 说明符的重复使用，以表示以下内容：
* 如果应用于参数 ref 说明符，此参数将被视为 `in` 。
* 如果应用于返回类型 ref 说明符，返回类型将被视为 `ref readonly` 。

### `out`

我们使用将 `System.Runtime.InteropServices.OutAttribute` 作为 `modreq` 参数类型上的 ref 说明符的，以表示该参数是一个 `out` 参数。

### <a name="errors"></a>错误

* 将作为 modreq 应用于返回类型是错误的 `OutAttribute` 。
* 将 `InAttribute` 和 `OutAttribute` 作为 modreq 应用到参数类型是错误的。
* 如果通过 modopt 指定任一方法，则它们将被忽略。

### <a name="metadata-representation-of-calling-conventions"></a>调用约定的元数据表示形式

调用约定在元数据的方法签名中通过签名中标志的组合进行编码 `CallKind` ，而在签名的开头有零个或多个 `modopt` 。 ECMA-335 当前声明标志中的以下元素 `CallKind` ：

```antlr
CallKind
   : default
   | unmanaged cdecl
   | unmanaged fastcall
   | unmanaged thiscall
   | unmanaged stdcall
   | varargs
   ;
```

其中，c # 中的函数指针将支持除之外的所有函数 `varargs` 。

此外，运行时 (和最终 335) 将更新为 `CallKind` 在新平台上包含新的。 目前没有正式的名称，但本文档将用作 `unmanaged ext` 新的可扩展调用约定格式的占位符作为替代。 不包含 `modopt` ， `unmanaged ext` 是平台默认调用约定， `unmanaged` 不带方括号。

#### <a name="mapping-the-calling_convention_specifier-to-a-callkind"></a>将映射 `calling_convention_specifier` 到 `CallKind`

`calling_convention_specifier`省略或指定为的将 `managed` 映射到 `default` `CallKind` 。 这是 `CallKind` 未特性化的任何方法的默认值 `UnmanagedCallersOnly` 。

C # 识别从 ECMA 335 映射到特定现有非托管的4个特殊标识符 `CallKind` 。 若要进行此映射，必须自行指定这些标识符，无需任何其他标识符，并且此要求将编码到中的规范中 `unmanaged_calling_convention` 。 这些标识符 `Cdecl` `Thiscall` `Stdcall` `Fastcall` `unmanaged cdecl` `unmanaged thiscall` `unmanaged stdcall` `unmanaged fastcall` 分别分别对应于、、和。 如果指定了多个 `identifer` ，或单个不 `identifier` 属于专门识别的标识符，我们将对标识符执行特殊的名称查找，规则如下：

* 在前面追加 `identifier` 字符串 `CallConv`
* 我们仅查看在命名空间中定义的类型 `System.Runtime.CompilerServices` 。
* 我们仅查看在应用程序的核心库中定义的类型，它是用于定义 `System.Object` 和没有依赖项的库。
* 我们仅探讨公共类型。

如果在中指定的所有上 `identifier` 进行查找成功 `unmanaged_calling_convention` ，我们会将编码 `CallKind` 为 `unmanaged ext` ，并对 `modopt` 函数指针签名开头的一组中的每个已解析类型进行编码。 请注意，这些规则意味着用户不能 `identifier` 使用作为的前缀 `CallConv` ，因为这会导致查找 `CallConvCallConvVectorCall` 。

解释元数据时，首先查看 `CallKind` 。 如果不是 `unmanaged ext` ，我们将忽略 `modopt` 返回类型上的所有，以确定调用约定，并且仅使用 `CallKind` 。 如果 `CallKind` 为 `unmanaged ext` ，我们将查看函数指针类型开头的 modopts，并采用满足以下要求的所有类型的并集：

* 是在核心库中定义的，它是不引用其他库和定义的库 `System.Object` 。
* 类型是在命名空间中定义的 `System.Runtime.CompilerServices` 。
* 类型以前缀开头 `CallConv` 。
* 类型是公共的。

 这表示 `identifier` `unmanaged_calling_convention` 当在源中定义函数指针类型时对中的执行查找时必须找到的类型。

`CallKind` `unmanaged ext` 如果目标运行时不支持该功能，则尝试将函数指针与的结合使用是错误的。 这将通过查找是否存在常量来确定 `System.Runtime.CompilerServices.RuntimeFeature.UnmanagedCallKind` 。 如果此常量存在，则将运行时视为支持该功能。

### `System.Runtime.InteropServices.UnmanagedCallersOnlyAttribute`

`System.Runtime.InteropServices.UnmanagedCallersOnlyAttribute` 是 CLR 用来指示应使用特定调用约定调用方法的属性。 因此，我们引入了以下对使用该属性的支持：

* 直接从 c # 中调用使用此属性批注的方法是错误的。 用户必须获取指向方法的函数指针，然后调用该指针。
* 将该特性应用于一般静态方法或普通静态本地函数之外的任何内容是错误的。
C # 编译器会将从元数据导入的任何非静态或静态非普通方法标记为该语言不支持的属性。
* 对于用特性标记的方法，如果该方法的参数或返回类型不是，则是错误的 `unmanaged_type` 。
* 如果用特性标记的方法具有类型参数，则是错误的，即使这些类型参数被约束为也是如此 `unmanaged` 。
* 泛型类型中的方法使用属性进行标记是错误的。
* 将用特性标记的方法转换为委托类型是错误的。
* 为指定的任何类型不 `UnmanagedCallersOnly.CallConvs` 符合 `modopt` 在元数据中调用约定的要求是错误的。

确定使用有效特性标记的方法的调用约定时 `UnmanagedCallersOnly` ，编译器将对属性中指定的类型执行以下检查， `CallConvs` 以确定 `CallKind` `modopt` 应该用于确定调用约定的有效和。

* 如果未指定类型，则将 `CallKind` 视为，在 `unmanaged ext` `modopt` 函数指针类型的开头没有调用约定 s。
* 如果指定了一种类型，并且该类型命名为、、或，则将 `CallConvCdecl` `CallConvThiscall` `CallConvStdcall` `CallConvFastcall` `CallKind` 分别处理为 `unmanaged cdecl` 、 `unmanaged thiscall` 、 `unmanaged stdcall` 或，并且 `unmanaged fastcall` `modopt` 函数指针类型的开头没有调用约定 s。
* 如果指定了多个类型，或未将单个类型命名为上面专门调用的类型之一，则 `CallKind` 会将视为 `unmanaged ext` ，并将指定的类型的联合视为 `modopt` 函数指针类型的开头。

然后，编译器会查看此有效 `CallKind` 和 `modopt` 集合，并使用正常的元数据规则来确定函数指针类型的最终调用约定。

## <a name="open-questions"></a>未解决的问题

### <a name="detecting-runtime-support-for-unmanaged-ext"></a>检测运行时支持 `unmanaged ext`

https://github.com/dotnet/runtime/issues/38135 跟踪添加此标志。 根据评审的反馈，我们将使用问题中指定的属性，或使用的状态 `UnmanagedCallersOnlyAttribute` 作为确定运行时是否支持的标志 `unmanaged ext` 。

## <a name="considerations"></a>注意事项

### <a name="allow-instance-methods"></a>允许实例方法

可以通过使用 `EXPLICITTHIS` `instance` c # 代码) 中 (指定的 CLI 调用约定，将该建议扩展为支持实例方法。 这种形式的 CLI 函数指针将 `this` 参数作为函数指针语法的显式第一个参数。

``` csharp
unsafe class Instance {
    void Use() {
        delegate* instance<Instance, string> f = &ToString;
        f(this);
    }
}
```

这听起来很复杂，但在提议中增加一些复杂化。 特别是，由于与调用约定不同的函数指针 `instance` 并不 `managed` 兼容，即使两种情况都用于使用相同的 c # 签名调用托管方法。 此外，在每种情况下，在这种情况下，有一个简单的解决方法是很有价值的：使用 `static` 本地函数。

``` csharp
unsafe class Instance {
    void Use() {
        static string toString(Instance i) = i.ToString();
        delgate*<Instance, string> f = &toString;
        f(this);
    }
}
```

### <a name="dont-require-unsafe-at-declaration"></a>声明时不需要 unsafe

不需要 `unsafe` 每次使用 `delegate*` ，只需要在将方法组转换为的点上使用 `delegate*` 。 在这种情况下，核心安全问题就 (知道在值处于活动状态时不能卸载包含程序集) 。 如果需要 `unsafe` 其他位置，则可能会出现过多。

这就是设计的最初设计方式。 但产生的语言规则觉得非常笨拙。 这是不可能的，因为这是一个指针值，并且即使不使用关键字，它仍可查看 `unsafe` 。 例如，无法允许转换为， `object` 它不能是等的成员。 `class`C # 设计需要 `unsafe` 使用所有指针，因此这种设计遵循这一设计。

开发人员仍可使用与_safe_ `delegate*` 今天普通指针类型相同的方式在值顶部呈现安全包装。 请注意以下几点：

``` csharp
unsafe struct Action {
    delegate*<void> _ptr;

    Action(delegate*<void> ptr) => _ptr = ptr;
    public void Invoke() => _ptr();
}
```

### <a name="using-delegates"></a>使用委托

`delegate*`只需使用 `delegate` 具有以下类型的现有类型，而不是使用新的语法元素 `*` ：

``` csharp
Func<object, object, bool>* ptr = &object.ReferenceEquals;
```

可以通过使用指定值的属性对类型进行批注来处理调用约定 `delegate` `CallingConvention` 。 缺少特性会表示托管调用约定。

在 IL 中对此进行编码会出现问题。 基础值需要表示为一个指针，但它也必须：

1. 具有唯一的类型，以允许具有不同函数指针类型的重载。
1. 对于跨程序集边界的 OHI 是等效的。

最后一点特别有问题。 这意味着，使用的每个程序集都 `Func<int>*` 必须在元数据中对等效类型进行编码，即使 `Func<int>*` 是在程序集中定义的，但不进行控制。
此外，在不是 mscorlib 的程序集中，使用名称定义的任何其他类型 `System.Func<T>` 必须与 mscorlib 中定义的版本不同。

研究的一个选项是发出这样的指针 `mod_req(Func<int>) void*` 。 但这不起作用，因为 `mod_req` 无法绑定到 `TypeSpec` ，因此不能以泛型实例化为目标。

### <a name="named-function-pointers"></a>命名函数指针

函数指针语法可能比较繁琐，尤其是在复杂情况下，例如嵌套函数指针。 不是让开发人员在每次使用时都可以输入签名的函数指针的命名声明 `delegate` 。

``` csharp
func* void Action();

unsafe class NamedExample {
    void M(Action a) {
        a();
    }
}
```

此处所述的问题是基础 CLI 基元没有名称，因此，这只是一个 c # 发明，需要使用一种元数据才能实现。 这是可行的，但这是一个重要的工作。 实质上，它要求 c # 将类型定义表与这些名称一起使用。

此外，当检查命名函数指针的参数时，我们发现它们可以同样适用于许多其他方案。 例如，将命名元组声明为在所有情况下都可以轻松地在所有情况下都需要键入完整的签名。

``` csharp
(int x, int y) Point;

class NamedTupleExample {
    void M(Point p) {
        Console.WriteLine(p.x);
    }
}
```

讨论后，我们决定不允许类型为的声明声明 `delegate*` 。 如果我们发现，根据客户使用反馈，需要特别需要此功能，我们将调查适用于函数指针、元组、泛型等的命名解决方案。这种形式的可能与其他建议类似，如 `typedef` 语言的完全支持。

## <a name="future-considerations"></a>未来的注意事项

### <a name="static-delegates"></a>静态委托

这是指允许类型声明的 [建议](https://github.com/dotnet/csharplang/issues/302) ， `delegate` 这些类型只能引用 `static` 成员。 其优势在于，此类 `delegate` 实例可以在性能敏感的情况下免费分配和更好地分配。

如果实现函数指针功能， `static delegate` 建议可能会关闭。此功能的建议优点是分配免费性质。 但最近的调查发现，由于程序集卸载，无法实现。 必须有一个从到它所引用的方法的强句柄， `static delegate` 以防止程序集从其下卸载。

若要维护每个 `static delegate` 实例，则需要分配一个新的句柄，该句柄运行到提议的目标。 在某些设计中，分配可能会分摊到每个调用站点的单个分配，但这有点复杂，不值得权衡。

这意味着开发人员实质上必须决定以下权衡：

1. 程序集卸载面临的安全性：这需要分配，因此 `delegate` 已是一个足够的选项。
1. 程序集卸载没有任何安全：使用 `delegate*` 。 这可以包装在中 `struct` ，以允许 `unsafe` 在其余代码的上下文之外使用。

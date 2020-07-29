---
ms.openlocfilehash: 42839a8c233468dd0b5ec6dad436dc71f056a6d9
ms.sourcegitcommit: 0c25406d8a99064bb85d934bb32ffcf547753acc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/28/2020
ms.locfileid: "87297259"
---
# <a name="native-sized-integers"></a>本机大小的整数

## <a name="summary"></a>摘要
[summary]: #summary

本机大小的有符号和无符号整数类型的语言支持。

动机适用于互操作方案和低级别库。

## <a name="design"></a>设计
[design]: #design

标识符 `nint` 和 `nuint` 是表示本机签名和无符号整数类型的新上下文关键字。
仅当名称查找在该程序位置找不到可行的结果时，才将标识符视为关键字。
```C#
nint x = 3;
string y = nameof(nuint);
_ = nint.Equals(x, 3);
```

类型 `nint` 和由 `nuint` 基础类型表示 `System.IntPtr` ， `System.UIntPtr` 编译器将这些类型的其他转换和操作呈现为本机整数。

### <a name="constants"></a>常量

常数表达式的类型可以是 `nint` 或 `nuint` 。
本机 int 文本没有直接的语法。 可以改为使用其他整数常数值的隐式或显式转换： `const nint i = (nint)42;` 。

`nint`常量处于 [ `int.MinValue` ，] 范围内 `int.MaxValue` 。

`nuint`常量处于 [ `uint.MinValue` ，] 范围内 `uint.MaxValue` 。

`MinValue` `MaxValue` 或上没有字段或， `nint` 因为不 `nuint` `nuint.MinValue` 能将这些值作为常量发出。

所有一元运算符 { `+` ， `-` ， `~` } 和二元运算符 { `+` ， `-` ， `*` `/` `%` `==` `!=` `<` `<=` `>` `>=` `&` `|` `^` `<<` `>>` ，，，，，，，，，，，，，，则支持常量折叠。
不管编译器平台如何，都将用 `Int32` 和 `UInt32` 操作数（而不是本机整数）来计算常量折叠运算。
如果该操作导致32位中的常量值，则会在编译时执行常量折叠。
否则，将在运行时执行该操作，而不会将其视为常数。

### <a name="conversions"></a>转换
在和之间存在一个标识转换，在和之间存在一个恒等转换 `nint` `IntPtr` `nuint` `UIntPtr` 。
仅有不同于本机 int 和基础类型的复合类型之间存在标识转换：数组、 `Nullable<>` 、构造类型和元组。

下表介绍了特殊类型之间的转换。
（每个转换的 IL 包括和上下文的变量（ `unchecked` `checked` 如果不同）。

| 操作数 | 目标 | 转换 | IL |
|:---:|:---:|:---:|:---:|
| `object` | `nint` | 取消装箱 | `unbox` |
| `void*` | `nint` | PointerToVoid | `conv.i` |
| `sbyte` | `nint` | ImplicitNumeric | `conv.i` |
| `byte` | `nint` | ImplicitNumeric | `conv.u` |
| `short` | `nint` | ImplicitNumeric | `conv.i` |
| `ushort` | `nint` | ImplicitNumeric | `conv.u` |
| `int` | `nint` | ImplicitNumeric | `conv.i` |
| `uint` | `nint` | ExplicitNumeric | `conv.u` / `conv.ovf.u` |
| `long` | `nint` | ExplicitNumeric | `conv.i` / `conv.ovf.i` |
| `ulong` | `nint` | ExplicitNumeric | `conv.i` / `conv.ovf.i` |
| `char` | `nint` | ImplicitNumeric | `conv.i` |
| `float` | `nint` | ExplicitNumeric | `conv.i` / `conv.ovf.i` |
| `double` | `nint` | ExplicitNumeric | `conv.i` / `conv.ovf.i` |
| `decimal` | `nint` | ExplicitNumeric | `long decimal.op_Explicit(decimal) conv.i` / `... conv.ovf.i` |
| `IntPtr` | `nint` | 标识 | |
| `UIntPtr` | `nint` | 无 | |
| `object` | `nuint` | 取消装箱 | `unbox` |
| `void*` | `nuint` | PointerToVoid | `conv.u` |
| `sbyte` | `nuint` | ExplicitNumeric | `conv.u` / `conv.ovf.u` |
| `byte` | `nuint` | ImplicitNumeric | `conv.u` |
| `short` | `nuint` | ExplicitNumeric | `conv.u` / `conv.ovf.u` |
| `ushort` | `nuint` | ImplicitNumeric | `conv.u` |
| `int` | `nuint` | ExplicitNumeric | `conv.u` / `conv.ovf.u` |
| `uint` | `nuint` | ImplicitNumeric | `conv.u` |
| `long` | `nuint` | ExplicitNumeric | `conv.u` / `conv.ovf.u` |
| `ulong` | `nuint` | ExplicitNumeric | `conv.u` / `conv.ovf.u` |
| `char` | `nuint` | ImplicitNumeric | `conv.u` |
| `float` | `nuint` | ExplicitNumeric | `conv.u` / `conv.ovf.u` |
| `double` | `nuint` | ExplicitNumeric | `conv.u` / `conv.ovf.u` |
| `decimal` | `nuint` | ExplicitNumeric | `ulong decimal.op_Explicit(decimal) conv.u` / `... conv.ovf.u.un`  |
| `IntPtr` | `nuint` | 无 | |
| `UIntPtr` | `nuint` | 标识 | |

| 操作数 | 目标 | 转换 | IL |
|:---:|:---:|:---:|:---:|
| `nint` | `object` | 装箱 | `box` |
| `nint` | `void*` | PointerToVoid | `conv.i` |
| `nint` | `nuint` | ExplicitNumeric | `conv.u` / `conv.ovf.u` |
| `nint` | `sbyte` | ExplicitNumeric | `conv.i1` / `conv.ovf.i1` |
| `nint` | `byte` | ExplicitNumeric | `conv.u1` / `conv.ovf.u1` |
| `nint` | `short` | ExplicitNumeric | `conv.i2` / `conv.ovf.i2` |
| `nint` | `ushort` | ExplicitNumeric | `conv.u2` / `conv.ovf.u2` |
| `nint` | `int` | ExplicitNumeric | `conv.i4` / `conv.ovf.i4` |
| `nint` | `uint` | ExplicitNumeric | `conv.u4` / `conv.ovf.u4` |
| `nint` | `long` | ImplicitNumeric | `conv.i8` / `conv.ovf.i8` |
| `nint` | `ulong` | ExplicitNumeric | `conv.i8` / `conv.ovf.i8` |
| `nint` | `char` | ExplicitNumeric | `conv.u2` / `conv.ovf.u2` |
| `nint` | `float` | ImplicitNumeric | `conv.r4` |
| `nint` | `double` | ImplicitNumeric | `conv.r8` |
| `nint` | `decimal` | ImplicitNumeric | `conv.i8 decimal decimal.op_Implicit(long)` |
| `nint` | `IntPtr` | 标识 | |
| `nint` | `UIntPtr` | 无 | |
| `nuint` | `object` | 装箱 | `box` |
| `nuint` | `void*` | PointerToVoid | `conv.u` |
| `nuint` | `nint` | ExplicitNumeric | `conv.i` / `conv.ovf.i` |
| `nuint` | `sbyte` | ExplicitNumeric | `conv.i1` / `conv.ovf.i1` |
| `nuint` | `byte` | ExplicitNumeric | `conv.u1` / `conv.ovf.u1` |
| `nuint` | `short` | ExplicitNumeric | `conv.i2` / `conv.ovf.i2` |
| `nuint` | `ushort` | ExplicitNumeric | `conv.u2` / `conv.ovf.u2` |
| `nuint` | `int` | ExplicitNumeric | `conv.i4` / `conv.ovf.i4` |
| `nuint` | `uint` | ExplicitNumeric | `conv.u4` / `conv.ovf.u4` |
| `nuint` | `long` | ExplicitNumeric | `conv.i8` / `conv.ovf.i8` |
| `nuint` | `ulong` | ImplicitNumeric | `conv.u8` / `conv.ovf.u8` |
| `nuint` | `char` | ExplicitNumeric | `conv.u2` / `conv.ovf.u2.un` |
| `nuint` | `float` | ImplicitNumeric | `conv.r.un conv.r4` |
| `nuint` | `double` | ImplicitNumeric | `conv.r.un conv.r8` |
| `nuint` | `decimal` | ImplicitNumeric | `conv.u8 decimal decimal.op_Implicit(ulong)` |
| `nuint` | `IntPtr` | 无 | |
| `nuint` | `UIntPtr` | 标识 | |

从到的转换 `A` `Nullable<B>` 为：
- 如果有标识转换或从到的隐式转换，则为隐式可为 null 的转换 `A` `B` ;
- 如果存在从到的显式转换，则为显式可为 null 的转换 `A` `B` ;
- 否则无效。

从到的转换 `Nullable<A>` `B` 为：
- 如果有标识转换或从到的隐式或显式数字转换，则为显式可为 null 的转换 `A` `B` ;
- 否则无效。

从到的转换 `Nullable<A>` `Nullable<B>` 为：
- 如果存在从到的标识转换，则为标识转换 `A` `B` ;
- 如果从到的隐式或显式数字转换，则为显式可为 null 的转换 `A` `B` ;
- 否则无效。

### <a name="operators"></a>运算符

预定义运算符如下所示。
_如果至少有一个操作数的类型为 `nint` 或 `nuint` _，则在重载解析过程中，将根据隐式转换的常规规则考虑这些运算符。

（如果不同，则每个运算符的 IL 都包含 `unchecked` 和上下文的变量 `checked` 。）

| 一元 | 运算符签名 | IL |
|:---:|:---:|:---:|
| `+` | `nint operator +(nint value)` | `nop` |
| `+` | `nuint operator +(nuint value)` | `nop` |
| `-` | `nint operator -(nint value)` | `neg` |
| `~` | `nint operator ~(nint value)` | `not` |
| `~` | `nuint operator ~(nuint value)` | `not` |

| Binary | 运算符签名 | IL |
|:---:|:---:|:---:|
| `+` | `nint operator +(nint left, nint right)` | `add` / `add.ovf` |
| `+` | `nuint operator +(nuint left, nuint right)` | `add` / `add.ovf.un` |
| `-` | `nint operator -(nint left, nint right)` | `sub` / `sub.ovf` |
| `-` | `nuint operator -(nuint left, nuint right)` | `sub` / `sub.ovf.un` |
| `*` | `nint operator *(nint left, nint right)` | `mul` / `mul.ovf` |
| `*` | `nuint operator *(nuint left, nuint right)` | `mul` / `mul.ovf.un` |
| `/` | `nint operator /(nint left, nint right)` | `div` |
| `/` | `nuint operator /(nuint left, nuint right)` | `div.un` |
| `%` | `nint operator %(nint left, nint right)` | `rem` |
| `%` | `nuint operator %(nuint left, nuint right)` | `rem.un` |
| `==` | `bool operator ==(nint left, nint right)` | `beq` / `ceq` |
| `==` | `bool operator ==(nuint left, nuint right)` | `beq` / `ceq` |
| `!=` | `bool operator !=(nint left, nint right)` | `bne` |
| `!=` | `bool operator !=(nuint left, nuint right)` | `bne` |
| `<` | `bool operator <(nint left, nint right)` | `blt` / `clt` |
| `<` | `bool operator <(nuint left, nuint right)` | `blt.un` / `clt.un` |
| `<=` | `bool operator <=(nint left, nint right)` | `ble` |
| `<=` | `bool operator <=(nuint left, nuint right)` | `ble.un` |
| `>` | `bool operator >(nint left, nint right)` | `bgt` / `cgt` |
| `>` | `bool operator >(nuint left, nuint right)` | `bgt.un` / `cgt.un` |
| `>=` | `bool operator >=(nint left, nint right)` | `bge` |
| `>=` | `bool operator >=(nuint left, nuint right)` | `bge.un` |
| `&` | `nint operator &(nint left, nint right)` | `and` |
| `&` | `nuint operator &(nuint left, nuint right)` | `and` |
| <code>&#124;</code> | <code>nint operator &#124;(nint left, nint right)</code> | `or` |
| <code>&#124;</code> | <code>nuint operator &#124;(nuint left, nuint right)</code> | `or` |
| `^` | `nint operator ^(nint left, nint right)` | `xor` |
| `^` | `nuint operator ^(nuint left, nuint right)` | `xor` |
| `<<` | `nint operator <<(nint left, int right)` | `shl` |
| `<<` | `nuint operator <<(nuint left, int right)` | `shl` |
| `>>` | `nint operator >>(nint left, int right)` | `shr` |
| `>>` | `nuint operator >>(nuint left, int right)` | `shr.un` |

对于某些二元运算符，IL 运算符支持其他操作数类型（请参阅[ECMA-335](https://www.ecma-international.org/publications/files/ECMA-ST/ECMA-335.pdf) III. 1.5 操作数类型表）。
但对于简单起见，c # 支持的操作数类型集是有限的，并且与语言中的现有运算符保持一致。

支持参数和返回类型为和的运算符的提升版本 `nint?` `nuint?` 。

与具有 `x op= y` `x` `y` 预定义运算符的其他基元类型相同，或为本机 int 的复合赋值运算遵循相同的规则。
具体而言，表达式绑定为， `x = (T)(x op y)` 其中 `T` 是的类型 `x` ，其中 `x` 只计算一次。

如果为4，则移位运算符应将位数屏蔽为 shift + 5 位 `sizeof(nint)` ，如果为8，则为6位 `sizeof(nint)` 。
（请参阅 c # 规范中的[移位运算符](https://github.com/dotnet/csharplang/blob/master/spec/expressions.md#shift-operators)）。

### <a name="dynamic"></a>动态

转换和运算符由编译器合成并且不属于基础 `IntPtr` 和 `UIntPtr` 类型。
因此，这些转换和运算符在_are not available_的运行时联编程序中不可用 `dynamic` 。 

```C#
nint x = 2;
nint y = x + x; // ok
dynamic d = x;
nint z = d + x; // RuntimeBinderException: '+' cannot be applied 'System.IntPtr' and 'System.IntPtr'
```

### <a name="type-members"></a>类型成员

或的唯一构造函数是不带 `nint` `nuint` 参数的构造函数。

和的以下成员 `System.IntPtr` `System.UIntPtr` _被显式排除_在 `nint` 或中 `nuint` ：
```C#
// constructors
// arithmetic operators
// implicit and explicit conversions
public static readonly IntPtr Zero; // use 0 instead
public static int Size { get; }     // use sizeof() instead
public static IntPtr Add(IntPtr pointer, int offset);
public static IntPtr Subtract(IntPtr pointer, int offset);
public int ToInt32();
public long ToInt64();
public void* ToPointer();
```

和的其余成员 `System.IntPtr` `System.UIntPtr` _将隐式包含_在 `nint` 和中 `nuint` 。 对于 .NET Framework 4.7.2：
```C#
public override bool Equals(object obj);
public override int GetHashCode();
public override string ToString();
public string ToString(string format);
```

和实现的 `System.IntPtr` 接口 `System.UIntPtr` _将隐式包含_在 `nint` 和中 `nuint` ，其中出现的基础类型由相应的本机整数类型替换。
例如，如果 `IntPtr` 实现 `ISerializable, IEquatable<IntPtr>, IComparable<IntPtr>` ，则 `nint` 实现 `ISerializable, IEquatable<nint>, IComparable<nint>` 。

### <a name="overriding-hiding-and-implementing"></a>重写、隐藏和实现

`nint`和 `System.IntPtr` 、和 `nuint` `System.UIntPtr` 都被视为等效于重写、隐藏和实现。

重载不能单独不同于 `nint` 和 `System.IntPtr` 、和 `nuint` `System.UIntPtr` 。
重写和实现可单独不同于 `nint` `System.IntPtr` 、、或 `nuint` `System.UIntPtr` 。
方法隐藏了不同于 `nint` `System.IntPtr` 、或和的其他 `nuint` 方法 `System.UIntPtr` 。

### <a name="miscellaneous"></a>杂项

`nint`用作 `nuint` 数组索引的表达式将在不进行转换的情况下发出。
```C#
static object GetItem(object[] array, nint index)
{
    return array[index]; // ok
}
```

`nint`和 `nuint` 可用作 `enum` 基类型。
```C#
enum E : nint // ok
{
}
```

对于类型 `nint` 、和的和，读取和写入是原子的 `nuint` `enum` `nint` `nuint` 。

可以将字段标记 `volatile` 为类型 `nint` 和 `nuint` 。
[ECMA-334](https://www.ecma-international.org/publications/files/ECMA-ST/ECMA-334.pdf) 15.5.4 不包括 `enum` 基类型 `System.IntPtr` 或 `System.UIntPtr` 。

`default(nint)`和与 `new nint()` 等效 `(nint)0` 。

`typeof(nint)` 为 `typeof(IntPtr)`。

`sizeof(nint)`支持，但要求在不安全的上下文中进行编译（就像那样 `sizeof(IntPtr)` ）。
该值不是编译时常量。
`sizeof(nint)`实现为 `sizeof(IntPtr)` 而不是 `IntPtr.Size` 。

编译器诊断，适用于涉及或报告的类型引用， `nint` `nuint` `nint` `nuint` 而不是或 `IntPtr` `UIntPtr` 。

### <a name="metadata"></a>元数据

`nint`和 `nuint` 在元数据中表示为 `System.IntPtr` 和 `System.UIntPtr` 。

包含或的类型引用， `nint` `nuint` `System.Runtime.CompilerServices.NativeIntegerAttribute` 用于指示类型引用的哪些部分是本机整数。

```C#
namespace System.Runtime.CompilerServices
{
    [AttributeUsage(
        AttributeTargets.Class |
        AttributeTargets.Event |
        AttributeTargets.Field |
        AttributeTargets.GenericParameter |
        AttributeTargets.Parameter |
        AttributeTargets.Property |
        AttributeTargets.ReturnValue,
        AllowMultiple = false,
        Inherited = false)]
    public sealed class NativeIntegerAttribute : Attribute
    {
        public NativeIntegerAttribute()
        {
            TransformFlags = new[] { true };
        }
        public NativeIntegerAttribute(bool[] flags)
        {
            TransformFlags = flags;
        }
        public IList<bool> TransformFlags { get; }
    }
}
```

编码使用用于编码的方法 `DynamicAttribute` ，尽管很明显地编码类型引用内的类型，而不是类型 `DynamicAttribute` `dynamic` 为本机 int 的类型。
如果编码导致值的数组 `false` ， `NativeIntegerAttribute` 则不需要。
无参数 `NativeIntegerAttribute` 构造函数使用单个值生成编码 `true` 。

```C#
nuint A;                    // [NativeInteger] UIntPtr A
(Stream, nint) B;           // [NativeInteger(new[] { false, false, true })] ValueType<Stream, IntPtr> B
```

## <a name="alternatives"></a>备选项
[alternatives]: #alternatives

上述 "类型擦除" 方法的替代方法是引入新的类型： `System.NativeInt` 和 `System.NativeUInt` 。
```C#
public readonly struct NativeInt
{
    public IntPtr Value;
}
```
Distinct 类型允许重载不同于 `IntPtr` ，并允许不同的分析和 `ToString()` 。
但是，CLR 可以更好地处理这些类型，这违背了功能效率的主要目的。
与使用现有本机 int 代码的互操作性 `IntPtr` 更难。 

另一种替代方法是在框架中添加更多本机 int 支持， `IntPtr` 但没有任何特定的编译器支持。
编译器将自动支持任何新的转换和算术运算。
但该语言不会提供关键字、常量或 `checked` 操作。

## <a name="design-meetings"></a>设计会议

https://github.com/dotnet/csharplang/blob/master/meetings/2017/LDM-2017-05-26.md https://github.com/dotnet/csharplang/blob/master/meetings/2017/LDM-2017-06-13.md https://github.com/dotnet/csharplang/blob/master/meetings/2017/LDM-2017-07-05.md#native-int-and-intptr-operators https://github.com/dotnet/csharplang/blob/master/meetings/2019/LDM-2019-10-23.md https://github.com/dotnet/csharplang/blob/master/meetings/2020/LDM-2020-03-25.md

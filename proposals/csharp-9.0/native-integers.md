---
ms.openlocfilehash: 42839a8c233468dd0b5ec6dad436dc71f056a6d9
ms.sourcegitcommit: 0c25406d8a99064bb85d934bb32ffcf547753acc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/28/2020
ms.locfileid: "87297259"
---
# <a name="native-sized-integers"></a><span data-ttu-id="54e61-101">本机大小的整数</span><span class="sxs-lookup"><span data-stu-id="54e61-101">Native-sized integers</span></span>

## <a name="summary"></a><span data-ttu-id="54e61-102">摘要</span><span class="sxs-lookup"><span data-stu-id="54e61-102">Summary</span></span>
[summary]: #summary

<span data-ttu-id="54e61-103">本机大小的有符号和无符号整数类型的语言支持。</span><span class="sxs-lookup"><span data-stu-id="54e61-103">Language support for a native-sized signed and unsigned integer types.</span></span>

<span data-ttu-id="54e61-104">动机适用于互操作方案和低级别库。</span><span class="sxs-lookup"><span data-stu-id="54e61-104">The motivation is for interop scenarios and for low-level libraries.</span></span>

## <a name="design"></a><span data-ttu-id="54e61-105">设计</span><span class="sxs-lookup"><span data-stu-id="54e61-105">Design</span></span>
[design]: #design

<span data-ttu-id="54e61-106">标识符 `nint` 和 `nuint` 是表示本机签名和无符号整数类型的新上下文关键字。</span><span class="sxs-lookup"><span data-stu-id="54e61-106">The identifiers `nint` and `nuint` are new contextual keywords that represent native signed and unsigned integer types.</span></span>
<span data-ttu-id="54e61-107">仅当名称查找在该程序位置找不到可行的结果时，才将标识符视为关键字。</span><span class="sxs-lookup"><span data-stu-id="54e61-107">The identifiers are only treated as keywords when name lookup does not find a viable result at that program location.</span></span>
```C#
nint x = 3;
string y = nameof(nuint);
_ = nint.Equals(x, 3);
```

<span data-ttu-id="54e61-108">类型 `nint` 和由 `nuint` 基础类型表示 `System.IntPtr` ， `System.UIntPtr` 编译器将这些类型的其他转换和操作呈现为本机整数。</span><span class="sxs-lookup"><span data-stu-id="54e61-108">The types `nint` and `nuint` are represented by the underlying types `System.IntPtr` and `System.UIntPtr` with compiler surfacing additional conversions and operations for those types as native ints.</span></span>

### <a name="constants"></a><span data-ttu-id="54e61-109">常量</span><span class="sxs-lookup"><span data-stu-id="54e61-109">Constants</span></span>

<span data-ttu-id="54e61-110">常数表达式的类型可以是 `nint` 或 `nuint` 。</span><span class="sxs-lookup"><span data-stu-id="54e61-110">Constant expressions may be of type `nint` or `nuint`.</span></span>
<span data-ttu-id="54e61-111">本机 int 文本没有直接的语法。</span><span class="sxs-lookup"><span data-stu-id="54e61-111">There is no direct syntax for native int literals.</span></span> <span data-ttu-id="54e61-112">可以改为使用其他整数常数值的隐式或显式转换： `const nint i = (nint)42;` 。</span><span class="sxs-lookup"><span data-stu-id="54e61-112">Implicit or explicit casts of other integral constant values can be used instead: `const nint i = (nint)42;`.</span></span>

<span data-ttu-id="54e61-113">`nint`常量处于 [ `int.MinValue` ，] 范围内 `int.MaxValue` 。</span><span class="sxs-lookup"><span data-stu-id="54e61-113">`nint` constants are in the range [ `int.MinValue`, `int.MaxValue` ].</span></span>

<span data-ttu-id="54e61-114">`nuint`常量处于 [ `uint.MinValue` ，] 范围内 `uint.MaxValue` 。</span><span class="sxs-lookup"><span data-stu-id="54e61-114">`nuint` constants are in the range [ `uint.MinValue`, `uint.MaxValue` ].</span></span>

<span data-ttu-id="54e61-115">`MinValue` `MaxValue` 或上没有字段或， `nint` 因为不 `nuint` `nuint.MinValue` 能将这些值作为常量发出。</span><span class="sxs-lookup"><span data-stu-id="54e61-115">There are no `MinValue` or `MaxValue` fields on `nint` or `nuint` because, other than `nuint.MinValue`, those values cannot be emitted as constants.</span></span>

<span data-ttu-id="54e61-116">所有一元运算符 { `+` ， `-` ， `~` } 和二元运算符 { `+` ， `-` ， `*` `/` `%` `==` `!=` `<` `<=` `>` `>=` `&` `|` `^` `<<` `>>` ，，，，，，，，，，，，，，则支持常量折叠。</span><span class="sxs-lookup"><span data-stu-id="54e61-116">Constant folding is supported for all unary operators { `+`, `-`, `~` } and binary operators { `+`, `-`, `*`, `/`, `%`, `==`, `!=`, `<`, `<=`, `>`, `>=`, `&`, `|`, `^`, `<<`, `>>` }.</span></span>
<span data-ttu-id="54e61-117">不管编译器平台如何，都将用 `Int32` 和 `UInt32` 操作数（而不是本机整数）来计算常量折叠运算。</span><span class="sxs-lookup"><span data-stu-id="54e61-117">Constant folding operations are evaluated with `Int32` and `UInt32` operands rather than native ints for consistent behavior regardless of compiler platform.</span></span>
<span data-ttu-id="54e61-118">如果该操作导致32位中的常量值，则会在编译时执行常量折叠。</span><span class="sxs-lookup"><span data-stu-id="54e61-118">If the operation results in a constant value in 32-bits, constant folding is performed at compile-time.</span></span>
<span data-ttu-id="54e61-119">否则，将在运行时执行该操作，而不会将其视为常数。</span><span class="sxs-lookup"><span data-stu-id="54e61-119">Otherwise the operation is executed at runtime and not considered a constant.</span></span>

### <a name="conversions"></a><span data-ttu-id="54e61-120">转换</span><span class="sxs-lookup"><span data-stu-id="54e61-120">Conversions</span></span>
<span data-ttu-id="54e61-121">在和之间存在一个标识转换，在和之间存在一个恒等转换 `nint` `IntPtr` `nuint` `UIntPtr` 。</span><span class="sxs-lookup"><span data-stu-id="54e61-121">There is an identity conversion between `nint` and `IntPtr`, and between `nuint` and `UIntPtr`.</span></span>
<span data-ttu-id="54e61-122">仅有不同于本机 int 和基础类型的复合类型之间存在标识转换：数组、 `Nullable<>` 、构造类型和元组。</span><span class="sxs-lookup"><span data-stu-id="54e61-122">There is an identity conversion between compound types that differ by native ints and underlying types only: arrays, `Nullable<>`, constructed types, and tuples.</span></span>

<span data-ttu-id="54e61-123">下表介绍了特殊类型之间的转换。</span><span class="sxs-lookup"><span data-stu-id="54e61-123">The tables below cover the conversions between special types.</span></span>
<span data-ttu-id="54e61-124">（每个转换的 IL 包括和上下文的变量（ `unchecked` `checked` 如果不同）。</span><span class="sxs-lookup"><span data-stu-id="54e61-124">(The IL for each conversion includes the variants for `unchecked` and `checked` contexts if different.)</span></span>

| <span data-ttu-id="54e61-125">操作数</span><span class="sxs-lookup"><span data-stu-id="54e61-125">Operand</span></span> | <span data-ttu-id="54e61-126">目标</span><span class="sxs-lookup"><span data-stu-id="54e61-126">Target</span></span> | <span data-ttu-id="54e61-127">转换</span><span class="sxs-lookup"><span data-stu-id="54e61-127">Conversion</span></span> | <span data-ttu-id="54e61-128">IL</span><span class="sxs-lookup"><span data-stu-id="54e61-128">IL</span></span> |
|:---:|:---:|:---:|:---:|
| `object` | `nint` | <span data-ttu-id="54e61-129">取消装箱</span><span class="sxs-lookup"><span data-stu-id="54e61-129">Unboxing</span></span> | `unbox` |
| `void*` | `nint` | <span data-ttu-id="54e61-130">PointerToVoid</span><span class="sxs-lookup"><span data-stu-id="54e61-130">PointerToVoid</span></span> | `conv.i` |
| `sbyte` | `nint` | <span data-ttu-id="54e61-131">ImplicitNumeric</span><span class="sxs-lookup"><span data-stu-id="54e61-131">ImplicitNumeric</span></span> | `conv.i` |
| `byte` | `nint` | <span data-ttu-id="54e61-132">ImplicitNumeric</span><span class="sxs-lookup"><span data-stu-id="54e61-132">ImplicitNumeric</span></span> | `conv.u` |
| `short` | `nint` | <span data-ttu-id="54e61-133">ImplicitNumeric</span><span class="sxs-lookup"><span data-stu-id="54e61-133">ImplicitNumeric</span></span> | `conv.i` |
| `ushort` | `nint` | <span data-ttu-id="54e61-134">ImplicitNumeric</span><span class="sxs-lookup"><span data-stu-id="54e61-134">ImplicitNumeric</span></span> | `conv.u` |
| `int` | `nint` | <span data-ttu-id="54e61-135">ImplicitNumeric</span><span class="sxs-lookup"><span data-stu-id="54e61-135">ImplicitNumeric</span></span> | `conv.i` |
| `uint` | `nint` | <span data-ttu-id="54e61-136">ExplicitNumeric</span><span class="sxs-lookup"><span data-stu-id="54e61-136">ExplicitNumeric</span></span> | `conv.u` / `conv.ovf.u` |
| `long` | `nint` | <span data-ttu-id="54e61-137">ExplicitNumeric</span><span class="sxs-lookup"><span data-stu-id="54e61-137">ExplicitNumeric</span></span> | `conv.i` / `conv.ovf.i` |
| `ulong` | `nint` | <span data-ttu-id="54e61-138">ExplicitNumeric</span><span class="sxs-lookup"><span data-stu-id="54e61-138">ExplicitNumeric</span></span> | `conv.i` / `conv.ovf.i` |
| `char` | `nint` | <span data-ttu-id="54e61-139">ImplicitNumeric</span><span class="sxs-lookup"><span data-stu-id="54e61-139">ImplicitNumeric</span></span> | `conv.i` |
| `float` | `nint` | <span data-ttu-id="54e61-140">ExplicitNumeric</span><span class="sxs-lookup"><span data-stu-id="54e61-140">ExplicitNumeric</span></span> | `conv.i` / `conv.ovf.i` |
| `double` | `nint` | <span data-ttu-id="54e61-141">ExplicitNumeric</span><span class="sxs-lookup"><span data-stu-id="54e61-141">ExplicitNumeric</span></span> | `conv.i` / `conv.ovf.i` |
| `decimal` | `nint` | <span data-ttu-id="54e61-142">ExplicitNumeric</span><span class="sxs-lookup"><span data-stu-id="54e61-142">ExplicitNumeric</span></span> | `long decimal.op_Explicit(decimal) conv.i` / `... conv.ovf.i` |
| `IntPtr` | `nint` | <span data-ttu-id="54e61-143">标识</span><span class="sxs-lookup"><span data-stu-id="54e61-143">Identity</span></span> | |
| `UIntPtr` | `nint` | <span data-ttu-id="54e61-144">无</span><span class="sxs-lookup"><span data-stu-id="54e61-144">None</span></span> | |
| `object` | `nuint` | <span data-ttu-id="54e61-145">取消装箱</span><span class="sxs-lookup"><span data-stu-id="54e61-145">Unboxing</span></span> | `unbox` |
| `void*` | `nuint` | <span data-ttu-id="54e61-146">PointerToVoid</span><span class="sxs-lookup"><span data-stu-id="54e61-146">PointerToVoid</span></span> | `conv.u` |
| `sbyte` | `nuint` | <span data-ttu-id="54e61-147">ExplicitNumeric</span><span class="sxs-lookup"><span data-stu-id="54e61-147">ExplicitNumeric</span></span> | `conv.u` / `conv.ovf.u` |
| `byte` | `nuint` | <span data-ttu-id="54e61-148">ImplicitNumeric</span><span class="sxs-lookup"><span data-stu-id="54e61-148">ImplicitNumeric</span></span> | `conv.u` |
| `short` | `nuint` | <span data-ttu-id="54e61-149">ExplicitNumeric</span><span class="sxs-lookup"><span data-stu-id="54e61-149">ExplicitNumeric</span></span> | `conv.u` / `conv.ovf.u` |
| `ushort` | `nuint` | <span data-ttu-id="54e61-150">ImplicitNumeric</span><span class="sxs-lookup"><span data-stu-id="54e61-150">ImplicitNumeric</span></span> | `conv.u` |
| `int` | `nuint` | <span data-ttu-id="54e61-151">ExplicitNumeric</span><span class="sxs-lookup"><span data-stu-id="54e61-151">ExplicitNumeric</span></span> | `conv.u` / `conv.ovf.u` |
| `uint` | `nuint` | <span data-ttu-id="54e61-152">ImplicitNumeric</span><span class="sxs-lookup"><span data-stu-id="54e61-152">ImplicitNumeric</span></span> | `conv.u` |
| `long` | `nuint` | <span data-ttu-id="54e61-153">ExplicitNumeric</span><span class="sxs-lookup"><span data-stu-id="54e61-153">ExplicitNumeric</span></span> | `conv.u` / `conv.ovf.u` |
| `ulong` | `nuint` | <span data-ttu-id="54e61-154">ExplicitNumeric</span><span class="sxs-lookup"><span data-stu-id="54e61-154">ExplicitNumeric</span></span> | `conv.u` / `conv.ovf.u` |
| `char` | `nuint` | <span data-ttu-id="54e61-155">ImplicitNumeric</span><span class="sxs-lookup"><span data-stu-id="54e61-155">ImplicitNumeric</span></span> | `conv.u` |
| `float` | `nuint` | <span data-ttu-id="54e61-156">ExplicitNumeric</span><span class="sxs-lookup"><span data-stu-id="54e61-156">ExplicitNumeric</span></span> | `conv.u` / `conv.ovf.u` |
| `double` | `nuint` | <span data-ttu-id="54e61-157">ExplicitNumeric</span><span class="sxs-lookup"><span data-stu-id="54e61-157">ExplicitNumeric</span></span> | `conv.u` / `conv.ovf.u` |
| `decimal` | `nuint` | <span data-ttu-id="54e61-158">ExplicitNumeric</span><span class="sxs-lookup"><span data-stu-id="54e61-158">ExplicitNumeric</span></span> | `ulong decimal.op_Explicit(decimal) conv.u` / `... conv.ovf.u.un`  |
| `IntPtr` | `nuint` | <span data-ttu-id="54e61-159">无</span><span class="sxs-lookup"><span data-stu-id="54e61-159">None</span></span> | |
| `UIntPtr` | `nuint` | <span data-ttu-id="54e61-160">标识</span><span class="sxs-lookup"><span data-stu-id="54e61-160">Identity</span></span> | |

| <span data-ttu-id="54e61-161">操作数</span><span class="sxs-lookup"><span data-stu-id="54e61-161">Operand</span></span> | <span data-ttu-id="54e61-162">目标</span><span class="sxs-lookup"><span data-stu-id="54e61-162">Target</span></span> | <span data-ttu-id="54e61-163">转换</span><span class="sxs-lookup"><span data-stu-id="54e61-163">Conversion</span></span> | <span data-ttu-id="54e61-164">IL</span><span class="sxs-lookup"><span data-stu-id="54e61-164">IL</span></span> |
|:---:|:---:|:---:|:---:|
| `nint` | `object` | <span data-ttu-id="54e61-165">装箱</span><span class="sxs-lookup"><span data-stu-id="54e61-165">Boxing</span></span> | `box` |
| `nint` | `void*` | <span data-ttu-id="54e61-166">PointerToVoid</span><span class="sxs-lookup"><span data-stu-id="54e61-166">PointerToVoid</span></span> | `conv.i` |
| `nint` | `nuint` | <span data-ttu-id="54e61-167">ExplicitNumeric</span><span class="sxs-lookup"><span data-stu-id="54e61-167">ExplicitNumeric</span></span> | `conv.u` / `conv.ovf.u` |
| `nint` | `sbyte` | <span data-ttu-id="54e61-168">ExplicitNumeric</span><span class="sxs-lookup"><span data-stu-id="54e61-168">ExplicitNumeric</span></span> | `conv.i1` / `conv.ovf.i1` |
| `nint` | `byte` | <span data-ttu-id="54e61-169">ExplicitNumeric</span><span class="sxs-lookup"><span data-stu-id="54e61-169">ExplicitNumeric</span></span> | `conv.u1` / `conv.ovf.u1` |
| `nint` | `short` | <span data-ttu-id="54e61-170">ExplicitNumeric</span><span class="sxs-lookup"><span data-stu-id="54e61-170">ExplicitNumeric</span></span> | `conv.i2` / `conv.ovf.i2` |
| `nint` | `ushort` | <span data-ttu-id="54e61-171">ExplicitNumeric</span><span class="sxs-lookup"><span data-stu-id="54e61-171">ExplicitNumeric</span></span> | `conv.u2` / `conv.ovf.u2` |
| `nint` | `int` | <span data-ttu-id="54e61-172">ExplicitNumeric</span><span class="sxs-lookup"><span data-stu-id="54e61-172">ExplicitNumeric</span></span> | `conv.i4` / `conv.ovf.i4` |
| `nint` | `uint` | <span data-ttu-id="54e61-173">ExplicitNumeric</span><span class="sxs-lookup"><span data-stu-id="54e61-173">ExplicitNumeric</span></span> | `conv.u4` / `conv.ovf.u4` |
| `nint` | `long` | <span data-ttu-id="54e61-174">ImplicitNumeric</span><span class="sxs-lookup"><span data-stu-id="54e61-174">ImplicitNumeric</span></span> | `conv.i8` / `conv.ovf.i8` |
| `nint` | `ulong` | <span data-ttu-id="54e61-175">ExplicitNumeric</span><span class="sxs-lookup"><span data-stu-id="54e61-175">ExplicitNumeric</span></span> | `conv.i8` / `conv.ovf.i8` |
| `nint` | `char` | <span data-ttu-id="54e61-176">ExplicitNumeric</span><span class="sxs-lookup"><span data-stu-id="54e61-176">ExplicitNumeric</span></span> | `conv.u2` / `conv.ovf.u2` |
| `nint` | `float` | <span data-ttu-id="54e61-177">ImplicitNumeric</span><span class="sxs-lookup"><span data-stu-id="54e61-177">ImplicitNumeric</span></span> | `conv.r4` |
| `nint` | `double` | <span data-ttu-id="54e61-178">ImplicitNumeric</span><span class="sxs-lookup"><span data-stu-id="54e61-178">ImplicitNumeric</span></span> | `conv.r8` |
| `nint` | `decimal` | <span data-ttu-id="54e61-179">ImplicitNumeric</span><span class="sxs-lookup"><span data-stu-id="54e61-179">ImplicitNumeric</span></span> | `conv.i8 decimal decimal.op_Implicit(long)` |
| `nint` | `IntPtr` | <span data-ttu-id="54e61-180">标识</span><span class="sxs-lookup"><span data-stu-id="54e61-180">Identity</span></span> | |
| `nint` | `UIntPtr` | <span data-ttu-id="54e61-181">无</span><span class="sxs-lookup"><span data-stu-id="54e61-181">None</span></span> | |
| `nuint` | `object` | <span data-ttu-id="54e61-182">装箱</span><span class="sxs-lookup"><span data-stu-id="54e61-182">Boxing</span></span> | `box` |
| `nuint` | `void*` | <span data-ttu-id="54e61-183">PointerToVoid</span><span class="sxs-lookup"><span data-stu-id="54e61-183">PointerToVoid</span></span> | `conv.u` |
| `nuint` | `nint` | <span data-ttu-id="54e61-184">ExplicitNumeric</span><span class="sxs-lookup"><span data-stu-id="54e61-184">ExplicitNumeric</span></span> | `conv.i` / `conv.ovf.i` |
| `nuint` | `sbyte` | <span data-ttu-id="54e61-185">ExplicitNumeric</span><span class="sxs-lookup"><span data-stu-id="54e61-185">ExplicitNumeric</span></span> | `conv.i1` / `conv.ovf.i1` |
| `nuint` | `byte` | <span data-ttu-id="54e61-186">ExplicitNumeric</span><span class="sxs-lookup"><span data-stu-id="54e61-186">ExplicitNumeric</span></span> | `conv.u1` / `conv.ovf.u1` |
| `nuint` | `short` | <span data-ttu-id="54e61-187">ExplicitNumeric</span><span class="sxs-lookup"><span data-stu-id="54e61-187">ExplicitNumeric</span></span> | `conv.i2` / `conv.ovf.i2` |
| `nuint` | `ushort` | <span data-ttu-id="54e61-188">ExplicitNumeric</span><span class="sxs-lookup"><span data-stu-id="54e61-188">ExplicitNumeric</span></span> | `conv.u2` / `conv.ovf.u2` |
| `nuint` | `int` | <span data-ttu-id="54e61-189">ExplicitNumeric</span><span class="sxs-lookup"><span data-stu-id="54e61-189">ExplicitNumeric</span></span> | `conv.i4` / `conv.ovf.i4` |
| `nuint` | `uint` | <span data-ttu-id="54e61-190">ExplicitNumeric</span><span class="sxs-lookup"><span data-stu-id="54e61-190">ExplicitNumeric</span></span> | `conv.u4` / `conv.ovf.u4` |
| `nuint` | `long` | <span data-ttu-id="54e61-191">ExplicitNumeric</span><span class="sxs-lookup"><span data-stu-id="54e61-191">ExplicitNumeric</span></span> | `conv.i8` / `conv.ovf.i8` |
| `nuint` | `ulong` | <span data-ttu-id="54e61-192">ImplicitNumeric</span><span class="sxs-lookup"><span data-stu-id="54e61-192">ImplicitNumeric</span></span> | `conv.u8` / `conv.ovf.u8` |
| `nuint` | `char` | <span data-ttu-id="54e61-193">ExplicitNumeric</span><span class="sxs-lookup"><span data-stu-id="54e61-193">ExplicitNumeric</span></span> | `conv.u2` / `conv.ovf.u2.un` |
| `nuint` | `float` | <span data-ttu-id="54e61-194">ImplicitNumeric</span><span class="sxs-lookup"><span data-stu-id="54e61-194">ImplicitNumeric</span></span> | `conv.r.un conv.r4` |
| `nuint` | `double` | <span data-ttu-id="54e61-195">ImplicitNumeric</span><span class="sxs-lookup"><span data-stu-id="54e61-195">ImplicitNumeric</span></span> | `conv.r.un conv.r8` |
| `nuint` | `decimal` | <span data-ttu-id="54e61-196">ImplicitNumeric</span><span class="sxs-lookup"><span data-stu-id="54e61-196">ImplicitNumeric</span></span> | `conv.u8 decimal decimal.op_Implicit(ulong)` |
| `nuint` | `IntPtr` | <span data-ttu-id="54e61-197">无</span><span class="sxs-lookup"><span data-stu-id="54e61-197">None</span></span> | |
| `nuint` | `UIntPtr` | <span data-ttu-id="54e61-198">标识</span><span class="sxs-lookup"><span data-stu-id="54e61-198">Identity</span></span> | |

<span data-ttu-id="54e61-199">从到的转换 `A` `Nullable<B>` 为：</span><span class="sxs-lookup"><span data-stu-id="54e61-199">Conversion from `A` to `Nullable<B>` is:</span></span>
- <span data-ttu-id="54e61-200">如果有标识转换或从到的隐式转换，则为隐式可为 null 的转换 `A` `B` ;</span><span class="sxs-lookup"><span data-stu-id="54e61-200">an implicit nullable conversion if there is an identity conversion or implicit conversion from `A` to `B`;</span></span>
- <span data-ttu-id="54e61-201">如果存在从到的显式转换，则为显式可为 null 的转换 `A` `B` ;</span><span class="sxs-lookup"><span data-stu-id="54e61-201">an explicit nullable conversion if there is an explicit conversion from `A` to `B`;</span></span>
- <span data-ttu-id="54e61-202">否则无效。</span><span class="sxs-lookup"><span data-stu-id="54e61-202">otherwise invalid.</span></span>

<span data-ttu-id="54e61-203">从到的转换 `Nullable<A>` `B` 为：</span><span class="sxs-lookup"><span data-stu-id="54e61-203">Conversion from `Nullable<A>` to `B` is:</span></span>
- <span data-ttu-id="54e61-204">如果有标识转换或从到的隐式或显式数字转换，则为显式可为 null 的转换 `A` `B` ;</span><span class="sxs-lookup"><span data-stu-id="54e61-204">an explicit nullable conversion if there is an identity conversion or implicit or explicit numeric conversion from `A` to `B`;</span></span>
- <span data-ttu-id="54e61-205">否则无效。</span><span class="sxs-lookup"><span data-stu-id="54e61-205">otherwise invalid.</span></span>

<span data-ttu-id="54e61-206">从到的转换 `Nullable<A>` `Nullable<B>` 为：</span><span class="sxs-lookup"><span data-stu-id="54e61-206">Conversion from `Nullable<A>` to `Nullable<B>` is:</span></span>
- <span data-ttu-id="54e61-207">如果存在从到的标识转换，则为标识转换 `A` `B` ;</span><span class="sxs-lookup"><span data-stu-id="54e61-207">an identity conversion if there is an identity conversion from `A` to `B`;</span></span>
- <span data-ttu-id="54e61-208">如果从到的隐式或显式数字转换，则为显式可为 null 的转换 `A` `B` ;</span><span class="sxs-lookup"><span data-stu-id="54e61-208">an explicit nullable conversion if there is an implicit or explicit numeric conversion from `A` to `B`;</span></span>
- <span data-ttu-id="54e61-209">否则无效。</span><span class="sxs-lookup"><span data-stu-id="54e61-209">otherwise invalid.</span></span>

### <a name="operators"></a><span data-ttu-id="54e61-210">运算符</span><span class="sxs-lookup"><span data-stu-id="54e61-210">Operators</span></span>

<span data-ttu-id="54e61-211">预定义运算符如下所示。</span><span class="sxs-lookup"><span data-stu-id="54e61-211">The predefined operators are as follows.</span></span>
<span data-ttu-id="54e61-212">_如果至少有一个操作数的类型为 `nint` 或 `nuint` _，则在重载解析过程中，将根据隐式转换的常规规则考虑这些运算符。</span><span class="sxs-lookup"><span data-stu-id="54e61-212">These operators are considered during overload resolution based on normal rules for implicit conversions _if at least one of the operands is of type `nint` or `nuint`_.</span></span>

<span data-ttu-id="54e61-213">（如果不同，则每个运算符的 IL 都包含 `unchecked` 和上下文的变量 `checked` 。）</span><span class="sxs-lookup"><span data-stu-id="54e61-213">(The IL for each operator includes the variants for `unchecked` and `checked` contexts if different.)</span></span>

| <span data-ttu-id="54e61-214">一元</span><span class="sxs-lookup"><span data-stu-id="54e61-214">Unary</span></span> | <span data-ttu-id="54e61-215">运算符签名</span><span class="sxs-lookup"><span data-stu-id="54e61-215">Operator Signature</span></span> | <span data-ttu-id="54e61-216">IL</span><span class="sxs-lookup"><span data-stu-id="54e61-216">IL</span></span> |
|:---:|:---:|:---:|
| `+` | `nint operator +(nint value)` | `nop` |
| `+` | `nuint operator +(nuint value)` | `nop` |
| `-` | `nint operator -(nint value)` | `neg` |
| `~` | `nint operator ~(nint value)` | `not` |
| `~` | `nuint operator ~(nuint value)` | `not` |

| <span data-ttu-id="54e61-217">Binary</span><span class="sxs-lookup"><span data-stu-id="54e61-217">Binary</span></span> | <span data-ttu-id="54e61-218">运算符签名</span><span class="sxs-lookup"><span data-stu-id="54e61-218">Operator Signature</span></span> | <span data-ttu-id="54e61-219">IL</span><span class="sxs-lookup"><span data-stu-id="54e61-219">IL</span></span> |
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

<span data-ttu-id="54e61-220">对于某些二元运算符，IL 运算符支持其他操作数类型（请参阅[ECMA-335](https://www.ecma-international.org/publications/files/ECMA-ST/ECMA-335.pdf) III. 1.5 操作数类型表）。</span><span class="sxs-lookup"><span data-stu-id="54e61-220">For some binary operators, the IL operators support additional operand types (see [ECMA-335](https://www.ecma-international.org/publications/files/ECMA-ST/ECMA-335.pdf) III.1.5 Operand type table).</span></span>
<span data-ttu-id="54e61-221">但对于简单起见，c # 支持的操作数类型集是有限的，并且与语言中的现有运算符保持一致。</span><span class="sxs-lookup"><span data-stu-id="54e61-221">But the set of operand types supported by C# is limited for simplicity and for consistency with existing operators in the language.</span></span>

<span data-ttu-id="54e61-222">支持参数和返回类型为和的运算符的提升版本 `nint?` `nuint?` 。</span><span class="sxs-lookup"><span data-stu-id="54e61-222">Lifted versions of the operators, where the arguments and return types are `nint?` and `nuint?`, are supported.</span></span>

<span data-ttu-id="54e61-223">与具有 `x op= y` `x` `y` 预定义运算符的其他基元类型相同，或为本机 int 的复合赋值运算遵循相同的规则。</span><span class="sxs-lookup"><span data-stu-id="54e61-223">Compound assignment operations `x op= y` where `x` or `y` are native ints follow the same rules as with other primitive types with pre-defined operators.</span></span>
<span data-ttu-id="54e61-224">具体而言，表达式绑定为， `x = (T)(x op y)` 其中 `T` 是的类型 `x` ，其中 `x` 只计算一次。</span><span class="sxs-lookup"><span data-stu-id="54e61-224">Specifically the expression is bound as `x = (T)(x op y)` where `T` is the type of `x` and where `x` is only evaluated once.</span></span>

<span data-ttu-id="54e61-225">如果为4，则移位运算符应将位数屏蔽为 shift + 5 位 `sizeof(nint)` ，如果为8，则为6位 `sizeof(nint)` 。</span><span class="sxs-lookup"><span data-stu-id="54e61-225">The shift operators should mask the number of bits to shift - to 5 bits if `sizeof(nint)` is 4, and to 6 bits if `sizeof(nint)` is 8.</span></span>
<span data-ttu-id="54e61-226">（请参阅 c # 规范中的[移位运算符](https://github.com/dotnet/csharplang/blob/master/spec/expressions.md#shift-operators)）。</span><span class="sxs-lookup"><span data-stu-id="54e61-226">(see [shift operators](https://github.com/dotnet/csharplang/blob/master/spec/expressions.md#shift-operators) in C# spec).</span></span>

### <a name="dynamic"></a><span data-ttu-id="54e61-227">动态</span><span class="sxs-lookup"><span data-stu-id="54e61-227">Dynamic</span></span>

<span data-ttu-id="54e61-228">转换和运算符由编译器合成并且不属于基础 `IntPtr` 和 `UIntPtr` 类型。</span><span class="sxs-lookup"><span data-stu-id="54e61-228">The conversions and operators are synthesized by the compiler and are not part of the underlying `IntPtr` and `UIntPtr` types.</span></span>
<span data-ttu-id="54e61-229">因此，这些转换和运算符在_are not available_的运行时联编程序中不可用 `dynamic` 。</span><span class="sxs-lookup"><span data-stu-id="54e61-229">As a result those conversions and operators _are not available_ from the runtime binder for `dynamic`.</span></span> 

```C#
nint x = 2;
nint y = x + x; // ok
dynamic d = x;
nint z = d + x; // RuntimeBinderException: '+' cannot be applied 'System.IntPtr' and 'System.IntPtr'
```

### <a name="type-members"></a><span data-ttu-id="54e61-230">类型成员</span><span class="sxs-lookup"><span data-stu-id="54e61-230">Type members</span></span>

<span data-ttu-id="54e61-231">或的唯一构造函数是不带 `nint` `nuint` 参数的构造函数。</span><span class="sxs-lookup"><span data-stu-id="54e61-231">The only constructor for `nint` or `nuint` is the parameter-less constructor.</span></span>

<span data-ttu-id="54e61-232">和的以下成员 `System.IntPtr` `System.UIntPtr` _被显式排除_在 `nint` 或中 `nuint` ：</span><span class="sxs-lookup"><span data-stu-id="54e61-232">The following members of `System.IntPtr` and `System.UIntPtr` _are explicitly excluded_ from `nint` or `nuint`:</span></span>
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

<span data-ttu-id="54e61-233">和的其余成员 `System.IntPtr` `System.UIntPtr` _将隐式包含_在 `nint` 和中 `nuint` 。</span><span class="sxs-lookup"><span data-stu-id="54e61-233">The remaining members of `System.IntPtr` and `System.UIntPtr` _are implicitly included_ in `nint` and `nuint`.</span></span> <span data-ttu-id="54e61-234">对于 .NET Framework 4.7.2：</span><span class="sxs-lookup"><span data-stu-id="54e61-234">For .NET Framework 4.7.2:</span></span>
```C#
public override bool Equals(object obj);
public override int GetHashCode();
public override string ToString();
public string ToString(string format);
```

<span data-ttu-id="54e61-235">和实现的 `System.IntPtr` 接口 `System.UIntPtr` _将隐式包含_在 `nint` 和中 `nuint` ，其中出现的基础类型由相应的本机整数类型替换。</span><span class="sxs-lookup"><span data-stu-id="54e61-235">Interfaces implemented by `System.IntPtr` and `System.UIntPtr` _are implicitly included_ in `nint` and `nuint`, with occurrences of the underlying types replaced by the corresponding native integer types.</span></span>
<span data-ttu-id="54e61-236">例如，如果 `IntPtr` 实现 `ISerializable, IEquatable<IntPtr>, IComparable<IntPtr>` ，则 `nint` 实现 `ISerializable, IEquatable<nint>, IComparable<nint>` 。</span><span class="sxs-lookup"><span data-stu-id="54e61-236">For instance if `IntPtr` implements `ISerializable, IEquatable<IntPtr>, IComparable<IntPtr>`, then `nint` implements `ISerializable, IEquatable<nint>, IComparable<nint>`.</span></span>

### <a name="overriding-hiding-and-implementing"></a><span data-ttu-id="54e61-237">重写、隐藏和实现</span><span class="sxs-lookup"><span data-stu-id="54e61-237">Overriding, hiding, and implementing</span></span>

<span data-ttu-id="54e61-238">`nint`和 `System.IntPtr` 、和 `nuint` `System.UIntPtr` 都被视为等效于重写、隐藏和实现。</span><span class="sxs-lookup"><span data-stu-id="54e61-238">`nint` and `System.IntPtr`, and `nuint` and `System.UIntPtr`, are considered equivalent for overriding, hiding, and implementing.</span></span>

<span data-ttu-id="54e61-239">重载不能单独不同于 `nint` 和 `System.IntPtr` 、和 `nuint` `System.UIntPtr` 。</span><span class="sxs-lookup"><span data-stu-id="54e61-239">Overloads cannot differ by `nint` and `System.IntPtr`, and `nuint` and `System.UIntPtr`, alone.</span></span>
<span data-ttu-id="54e61-240">重写和实现可单独不同于 `nint` `System.IntPtr` 、、或 `nuint` `System.UIntPtr` 。</span><span class="sxs-lookup"><span data-stu-id="54e61-240">Overrides and implementations may differ by `nint` and `System.IntPtr`, or `nuint` and `System.UIntPtr`, alone.</span></span>
<span data-ttu-id="54e61-241">方法隐藏了不同于 `nint` `System.IntPtr` 、或和的其他 `nuint` 方法 `System.UIntPtr` 。</span><span class="sxs-lookup"><span data-stu-id="54e61-241">Methods hide other methods that differ by `nint` and `System.IntPtr`, or `nuint` and `System.UIntPtr`, alone.</span></span>

### <a name="miscellaneous"></a><span data-ttu-id="54e61-242">杂项</span><span class="sxs-lookup"><span data-stu-id="54e61-242">Miscellaneous</span></span>

<span data-ttu-id="54e61-243">`nint`用作 `nuint` 数组索引的表达式将在不进行转换的情况下发出。</span><span class="sxs-lookup"><span data-stu-id="54e61-243">`nint` and `nuint` expressions used as array indices are emitted without conversion.</span></span>
```C#
static object GetItem(object[] array, nint index)
{
    return array[index]; // ok
}
```

<span data-ttu-id="54e61-244">`nint`和 `nuint` 可用作 `enum` 基类型。</span><span class="sxs-lookup"><span data-stu-id="54e61-244">`nint` and `nuint` can be used as an `enum` base type.</span></span>
```C#
enum E : nint // ok
{
}
```

<span data-ttu-id="54e61-245">对于类型 `nint` 、和的和，读取和写入是原子的 `nuint` `enum` `nint` `nuint` 。</span><span class="sxs-lookup"><span data-stu-id="54e61-245">Reads and writes are atomic for types `nint`, `nuint`, and `enum` with base type `nint` or `nuint`.</span></span>

<span data-ttu-id="54e61-246">可以将字段标记 `volatile` 为类型 `nint` 和 `nuint` 。</span><span class="sxs-lookup"><span data-stu-id="54e61-246">Fields may be marked `volatile` for types `nint` and `nuint`.</span></span>
<span data-ttu-id="54e61-247">[ECMA-334](https://www.ecma-international.org/publications/files/ECMA-ST/ECMA-334.pdf) 15.5.4 不包括 `enum` 基类型 `System.IntPtr` 或 `System.UIntPtr` 。</span><span class="sxs-lookup"><span data-stu-id="54e61-247">[ECMA-334](https://www.ecma-international.org/publications/files/ECMA-ST/ECMA-334.pdf) 15.5.4 does not include `enum` with base type `System.IntPtr` or `System.UIntPtr` however.</span></span>

<span data-ttu-id="54e61-248">`default(nint)`和与 `new nint()` 等效 `(nint)0` 。</span><span class="sxs-lookup"><span data-stu-id="54e61-248">`default(nint)` and `new nint()` are equivalent to `(nint)0`.</span></span>

<span data-ttu-id="54e61-249">`typeof(nint)` 为 `typeof(IntPtr)`。</span><span class="sxs-lookup"><span data-stu-id="54e61-249">`typeof(nint)` is `typeof(IntPtr)`.</span></span>

<span data-ttu-id="54e61-250">`sizeof(nint)`支持，但要求在不安全的上下文中进行编译（就像那样 `sizeof(IntPtr)` ）。</span><span class="sxs-lookup"><span data-stu-id="54e61-250">`sizeof(nint)` is supported but requires compiling in an unsafe context (as does `sizeof(IntPtr)`).</span></span>
<span data-ttu-id="54e61-251">该值不是编译时常量。</span><span class="sxs-lookup"><span data-stu-id="54e61-251">The value is not a compile-time constant.</span></span>
<span data-ttu-id="54e61-252">`sizeof(nint)`实现为 `sizeof(IntPtr)` 而不是 `IntPtr.Size` 。</span><span class="sxs-lookup"><span data-stu-id="54e61-252">`sizeof(nint)` is implemented as `sizeof(IntPtr)` rather than `IntPtr.Size`.</span></span>

<span data-ttu-id="54e61-253">编译器诊断，适用于涉及或报告的类型引用， `nint` `nuint` `nint` `nuint` 而不是或 `IntPtr` `UIntPtr` 。</span><span class="sxs-lookup"><span data-stu-id="54e61-253">Compiler diagnostics for type references involving `nint` or `nuint` report `nint` or `nuint` rather than `IntPtr` or `UIntPtr`.</span></span>

### <a name="metadata"></a><span data-ttu-id="54e61-254">元数据</span><span class="sxs-lookup"><span data-stu-id="54e61-254">Metadata</span></span>

<span data-ttu-id="54e61-255">`nint`和 `nuint` 在元数据中表示为 `System.IntPtr` 和 `System.UIntPtr` 。</span><span class="sxs-lookup"><span data-stu-id="54e61-255">`nint` and `nuint` are represented in metadata as `System.IntPtr` and `System.UIntPtr`.</span></span>

<span data-ttu-id="54e61-256">包含或的类型引用， `nint` `nuint` `System.Runtime.CompilerServices.NativeIntegerAttribute` 用于指示类型引用的哪些部分是本机整数。</span><span class="sxs-lookup"><span data-stu-id="54e61-256">Type references that include `nint` or `nuint` are emitted with a `System.Runtime.CompilerServices.NativeIntegerAttribute` to indicate which parts of the type reference are native ints.</span></span>

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

<span data-ttu-id="54e61-257">编码使用用于编码的方法 `DynamicAttribute` ，尽管很明显地编码类型引用内的类型，而不是类型 `DynamicAttribute` `dynamic` 为本机 int 的类型。</span><span class="sxs-lookup"><span data-stu-id="54e61-257">The encoding uses the approach as used to encode `DynamicAttribute`, although obviously `DynamicAttribute` is encoding which types within the type reference are `dynamic` rather than which types are native ints.</span></span>
<span data-ttu-id="54e61-258">如果编码导致值的数组 `false` ， `NativeIntegerAttribute` 则不需要。</span><span class="sxs-lookup"><span data-stu-id="54e61-258">If the encoding results in an array of `false` values, no `NativeIntegerAttribute` is needed.</span></span>
<span data-ttu-id="54e61-259">无参数 `NativeIntegerAttribute` 构造函数使用单个值生成编码 `true` 。</span><span class="sxs-lookup"><span data-stu-id="54e61-259">The parameterless `NativeIntegerAttribute` constructor generates an encoding with a single `true` value.</span></span>

```C#
nuint A;                    // [NativeInteger] UIntPtr A
(Stream, nint) B;           // [NativeInteger(new[] { false, false, true })] ValueType<Stream, IntPtr> B
```

## <a name="alternatives"></a><span data-ttu-id="54e61-260">备选项</span><span class="sxs-lookup"><span data-stu-id="54e61-260">Alternatives</span></span>
[alternatives]: #alternatives

<span data-ttu-id="54e61-261">上述 "类型擦除" 方法的替代方法是引入新的类型： `System.NativeInt` 和 `System.NativeUInt` 。</span><span class="sxs-lookup"><span data-stu-id="54e61-261">An alternative to the "type erasure" approach above is to introduce new types: `System.NativeInt` and `System.NativeUInt`.</span></span>
```C#
public readonly struct NativeInt
{
    public IntPtr Value;
}
```
<span data-ttu-id="54e61-262">Distinct 类型允许重载不同于 `IntPtr` ，并允许不同的分析和 `ToString()` 。</span><span class="sxs-lookup"><span data-stu-id="54e61-262">Distinct types would allow overloading distinct from `IntPtr` and would allow distinct parsing and `ToString()`.</span></span>
<span data-ttu-id="54e61-263">但是，CLR 可以更好地处理这些类型，这违背了功能效率的主要目的。</span><span class="sxs-lookup"><span data-stu-id="54e61-263">But there would be more work for the CLR to handle these types efficiently which defeats the primary purpose of the feature - efficiency.</span></span>
<span data-ttu-id="54e61-264">与使用现有本机 int 代码的互操作性 `IntPtr` 更难。</span><span class="sxs-lookup"><span data-stu-id="54e61-264">And interop with existing native int code that uses `IntPtr` would be more difficult.</span></span> 

<span data-ttu-id="54e61-265">另一种替代方法是在框架中添加更多本机 int 支持， `IntPtr` 但没有任何特定的编译器支持。</span><span class="sxs-lookup"><span data-stu-id="54e61-265">Another alternative is to add more native int support for `IntPtr` in the framework but without any specific compiler support.</span></span>
<span data-ttu-id="54e61-266">编译器将自动支持任何新的转换和算术运算。</span><span class="sxs-lookup"><span data-stu-id="54e61-266">Any new conversions and arithmetic operations would be supported by the compiler automatically.</span></span>
<span data-ttu-id="54e61-267">但该语言不会提供关键字、常量或 `checked` 操作。</span><span class="sxs-lookup"><span data-stu-id="54e61-267">But the language would not provide keywords, constants, or `checked` operations.</span></span>

## <a name="design-meetings"></a><span data-ttu-id="54e61-268">设计会议</span><span class="sxs-lookup"><span data-stu-id="54e61-268">Design meetings</span></span>

<span data-ttu-id="54e61-269">https://github.com/dotnet/csharplang/blob/master/meetings/2017/LDM-2017-05-26.md https://github.com/dotnet/csharplang/blob/master/meetings/2017/LDM-2017-06-13.md https://github.com/dotnet/csharplang/blob/master/meetings/2017/LDM-2017-07-05.md#native-int-and-intptr-operators https://github.com/dotnet/csharplang/blob/master/meetings/2019/LDM-2019-10-23.md https://github.com/dotnet/csharplang/blob/master/meetings/2020/LDM-2020-03-25.md</span><span class="sxs-lookup"><span data-stu-id="54e61-269">https://github.com/dotnet/csharplang/blob/master/meetings/2017/LDM-2017-05-26.md https://github.com/dotnet/csharplang/blob/master/meetings/2017/LDM-2017-06-13.md https://github.com/dotnet/csharplang/blob/master/meetings/2017/LDM-2017-07-05.md#native-int-and-intptr-operators https://github.com/dotnet/csharplang/blob/master/meetings/2019/LDM-2019-10-23.md https://github.com/dotnet/csharplang/blob/master/meetings/2020/LDM-2020-03-25.md</span></span>

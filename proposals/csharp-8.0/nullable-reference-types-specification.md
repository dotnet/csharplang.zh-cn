---
ms.openlocfilehash: 060d3af7d06033b524e9e967a7f43c7577bc7965
ms.sourcegitcommit: 538df2e3c334d94cac1fac6a382ddfe15452ad96
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/17/2020
ms.locfileid: "84908312"
---
# <a name="nullable-reference-types-specification"></a><span data-ttu-id="f736e-101">可以为 null 的引用类型规范</span><span class="sxs-lookup"><span data-stu-id="f736e-101">Nullable Reference Types Specification</span></span>

<span data-ttu-id="f736e-102">***这是一个正在进行的工作-缺少几个部件或这些部件不完整。***</span><span class="sxs-lookup"><span data-stu-id="f736e-102">***This is a work in progress - several parts are missing or incomplete.***</span></span>

## <a name="syntax"></a><span data-ttu-id="f736e-103">语法</span><span class="sxs-lookup"><span data-stu-id="f736e-103">Syntax</span></span>

### <a name="nullable-reference-types"></a><span data-ttu-id="f736e-104">可为空引用类型</span><span class="sxs-lookup"><span data-stu-id="f736e-104">Nullable reference types</span></span>

<span data-ttu-id="f736e-105">可以为 null 的引用类型与 "可以为 null 的值类型" 的缩写形式具有相同的语法 `T?` ，但没有相应的长格式。</span><span class="sxs-lookup"><span data-stu-id="f736e-105">Nullable reference types have the same syntax `T?` as the short form of nullable value types, but do not have a corresponding long form.</span></span>

<span data-ttu-id="f736e-106">出于规范的目的，当前 `nullable_type` 生产将重命名为 `nullable_value_type` ，并 `nullable_reference_type` 添加生产：</span><span class="sxs-lookup"><span data-stu-id="f736e-106">For the purposes of the specification, the current `nullable_type` production is renamed to `nullable_value_type`, and a `nullable_reference_type` production is added:</span></span>

```antlr
reference_type
    : ...
    | nullable_reference_type
    ;
    
nullable_reference_type
    : non_nullable_reference_type '?'
    ;
    
non_nullable_reference_type
    : type
    ;
```

<span data-ttu-id="f736e-107">`non_nullable_reference_type`在中， `nullable_reference_type` 必须是不可以为 null 的引用类型（类、接口、委托或数组）或被约束为不可为 null 的引用类型的类型参数（通过 `class` 约束或除之外的类 `object` ）。</span><span class="sxs-lookup"><span data-stu-id="f736e-107">The `non_nullable_reference_type` in a `nullable_reference_type` must be a non-nullable reference type (class, interface, delegate or array), or a type parameter that is constrained to be a non-nullable reference type (through the `class` constraint, or a class other than `object`).</span></span>

<span data-ttu-id="f736e-108">可以为 null 的引用类型不能出现在以下位置：</span><span class="sxs-lookup"><span data-stu-id="f736e-108">Nullable reference types cannot occur in the following positions:</span></span>

- <span data-ttu-id="f736e-109">作为基类或接口</span><span class="sxs-lookup"><span data-stu-id="f736e-109">as a base class or interface</span></span>
- <span data-ttu-id="f736e-110">作为的接收方`member_access`</span><span class="sxs-lookup"><span data-stu-id="f736e-110">as the receiver of a `member_access`</span></span>
- <span data-ttu-id="f736e-111">作为 `type` 中的`object_creation_expression`</span><span class="sxs-lookup"><span data-stu-id="f736e-111">as the `type` in an `object_creation_expression`</span></span>
- <span data-ttu-id="f736e-112">作为 `delegate_type` 中的`delegate_creation_expression`</span><span class="sxs-lookup"><span data-stu-id="f736e-112">as the `delegate_type` in a `delegate_creation_expression`</span></span>
- <span data-ttu-id="f736e-113">作为 `type` 中的 `is_expression` ， `catch_clause` 或`type_pattern`</span><span class="sxs-lookup"><span data-stu-id="f736e-113">as the `type` in an `is_expression`, a `catch_clause` or a `type_pattern`</span></span>
- <span data-ttu-id="f736e-114">作为 `interface` 完全限定的接口成员名称中的</span><span class="sxs-lookup"><span data-stu-id="f736e-114">as the `interface` in a fully qualified interface member name</span></span>

<span data-ttu-id="f736e-115">在 `nullable_reference_type` 禁用了可为 null 的注释上下文的位置上，将发出警告。</span><span class="sxs-lookup"><span data-stu-id="f736e-115">A warning is given on a `nullable_reference_type` where the nullable annotation context is disabled.</span></span>

### <a name="nullable-class-constraint"></a><span data-ttu-id="f736e-116">可以为 null 的类约束</span><span class="sxs-lookup"><span data-stu-id="f736e-116">Nullable class constraint</span></span>

<span data-ttu-id="f736e-117">`class`约束具有可为 null 的对应项 `class?` ：</span><span class="sxs-lookup"><span data-stu-id="f736e-117">The `class` constraint has a nullable counterpart `class?`:</span></span>

```antlr
primary_constraint
    : ...
    | 'class' '?'
    ;
```

### <a name="the-null-forgiving-operator"></a><span data-ttu-id="f736e-118">包容性运算符</span><span class="sxs-lookup"><span data-stu-id="f736e-118">The null-forgiving operator</span></span>

<span data-ttu-id="f736e-119">后修复后的 `!` 运算符称为包容性运算符。</span><span class="sxs-lookup"><span data-stu-id="f736e-119">The post-fix `!` operator is called the null-forgiving operator.</span></span>

```antlr
primary_expression
    : ...
    | null_forgiving_expression
    ;
    
null_forgiving_expression
    : primary_expression '!'
    ;
```

<span data-ttu-id="f736e-120">`primary_expression`必须为引用类型。</span><span class="sxs-lookup"><span data-stu-id="f736e-120">The `primary_expression` must be of a reference type.</span></span>  

<span data-ttu-id="f736e-121">后缀 `!` 运算符没有运行时效果，它的计算结果为基础表达式的结果。</span><span class="sxs-lookup"><span data-stu-id="f736e-121">The postfix `!` operator has no runtime effect - it evaluates to the result of the underlying expression.</span></span> <span data-ttu-id="f736e-122">其唯一的作用是更改表达式的 null 状态，并限制其使用时给出的警告。</span><span class="sxs-lookup"><span data-stu-id="f736e-122">Its only role is to change the null state of the expression, and to limit warnings given on its use.</span></span>

### <a name="nullable-implicitly-typed-local-variables"></a><span data-ttu-id="f736e-123">可以为 null 的隐式类型的局部变量</span><span class="sxs-lookup"><span data-stu-id="f736e-123">nullable implicitly typed local variables</span></span>

<span data-ttu-id="f736e-124">`var`推导引用类型的批注类型。</span><span class="sxs-lookup"><span data-stu-id="f736e-124">`var` infers an annotated type for reference types.</span></span>
<span data-ttu-id="f736e-125">例如，在中 `var s = "";` ， `var` 将推断为 `string?` 。</span><span class="sxs-lookup"><span data-stu-id="f736e-125">For instance, in `var s = "";` the `var` is inferred as `string?`.</span></span>

### <a name="nullable-compiler-directives"></a><span data-ttu-id="f736e-126">可以为 null 的编译器指令</span><span class="sxs-lookup"><span data-stu-id="f736e-126">Nullable compiler directives</span></span>

<span data-ttu-id="f736e-127">`#nullable`指令控制可为 null 的批注和警告上下文。</span><span class="sxs-lookup"><span data-stu-id="f736e-127">`#nullable` directives control the nullable annotation and warning contexts.</span></span>

```antlr
pp_directive
    : ...
    | pp_nullable
    ;
    
pp_nullable
    : whitespace? '#' whitespace? 'nullable' whitespace nullable_action pp_new_line
    ;
    
nullable_action
    : 'disable'
    | 'enable'
    | 'restore'
    ;
```

<span data-ttu-id="f736e-128">`#pragma warning`指令将展开以允许更改可为 null 的警告上下文，并允许在默认情况下启用单个警告（即使它们在默认情况下处于禁用状态）：</span><span class="sxs-lookup"><span data-stu-id="f736e-128">`#pragma warning` directives are expanded to allow changing the nullable warning context, and to allow individual warnings to be enabled on even when they're disabled by default:</span></span>

```antlr
pragma_warning_body
    : ...
    | 'warning' whitespace nullable_action whitespace 'nullable'
    ;

warning_action
    : ...
    | 'enable'
    ;
```

<span data-ttu-id="f736e-129">请注意，的新形式 `pragma_warning_body` 使用 `nullable_action` ，而不是 `warning_action` 。</span><span class="sxs-lookup"><span data-stu-id="f736e-129">Note that the new form of `pragma_warning_body` uses `nullable_action`, not `warning_action`.</span></span>

## <a name="nullable-contexts"></a><span data-ttu-id="f736e-130">可为空上下文</span><span class="sxs-lookup"><span data-stu-id="f736e-130">Nullable contexts</span></span>

<span data-ttu-id="f736e-131">源代码的每个行都有*可为 null 的注释上下文*和*可以为 null 的警告上下文*。</span><span class="sxs-lookup"><span data-stu-id="f736e-131">Every line of source code has a *nullable annotation context* and a *nullable warning context*.</span></span> <span data-ttu-id="f736e-132">这些控件控制是否可为 null 的批注是否有效，以及是否给定了可为空性警告。</span><span class="sxs-lookup"><span data-stu-id="f736e-132">These control whether nullable annotations have effect, and whether nullability warnings are given.</span></span> <span data-ttu-id="f736e-133">给定行的批注上下文已*禁用*或*启用*。</span><span class="sxs-lookup"><span data-stu-id="f736e-133">The annotation context of a given line is either *disabled* or *enabled*.</span></span> <span data-ttu-id="f736e-134">给定行的警告上下文已*禁用*或*启用*。</span><span class="sxs-lookup"><span data-stu-id="f736e-134">The warning context of a given line is either *disabled* or *enabled*.</span></span>

<span data-ttu-id="f736e-135">可以在项目级别指定这两个上下文（c # 源代码外），也可以通过预处理器指令在源文件中的任意位置指定 `#nullable` 。</span><span class="sxs-lookup"><span data-stu-id="f736e-135">Both contexts can be specified at the project level (outside of C# source code), or anywhere within a source file via `#nullable` pre-processor directives.</span></span> <span data-ttu-id="f736e-136">如果未提供任何项目级别设置，则默认情况下，这两个上下文都是*禁用*的。</span><span class="sxs-lookup"><span data-stu-id="f736e-136">If no project level settings are provided the default is for both contexts to be *disabled*.</span></span>

<span data-ttu-id="f736e-137">`#nullable`指令控制源文本中的批注和警告上下文，并优先于项目级设置。</span><span class="sxs-lookup"><span data-stu-id="f736e-137">The `#nullable` directive controls the annotation and warning contexts within the source text, and take precedence over the project-level settings.</span></span>

<span data-ttu-id="f736e-138">指令将它控制的上下文设置为后面的代码行，直到另一个指令重写它，或直到源文件的结尾。</span><span class="sxs-lookup"><span data-stu-id="f736e-138">A directive sets the context(s) it controls for subsequent lines of code, until another directive overrides it, or until the end of the source file.</span></span>

<span data-ttu-id="f736e-139">指令的效果如下所示：</span><span class="sxs-lookup"><span data-stu-id="f736e-139">The effect of the directives is as follows:</span></span>

- <span data-ttu-id="f736e-140">`#nullable disable`：将可为 null 的批注和警告上下文设置为*已禁用*</span><span class="sxs-lookup"><span data-stu-id="f736e-140">`#nullable disable`: Sets the nullable annotation and warning contexts to *disabled*</span></span>
- <span data-ttu-id="f736e-141">`#nullable enable`：将可为 null 的批注和警告上下文设置为*enabled*</span><span class="sxs-lookup"><span data-stu-id="f736e-141">`#nullable enable`: Sets the nullable annotation and warning contexts to *enabled*</span></span>
- <span data-ttu-id="f736e-142">`#nullable restore`：将可为 null 的批注和警告上下文还原到项目设置</span><span class="sxs-lookup"><span data-stu-id="f736e-142">`#nullable restore`: Restores the nullable annotation and warning contexts to project settings</span></span>
- <span data-ttu-id="f736e-143">`#nullable disable annotations`：将可为 null 的注释上下文设置为*已禁用*</span><span class="sxs-lookup"><span data-stu-id="f736e-143">`#nullable disable annotations`: Sets the nullable annotation context to *disabled*</span></span>
- <span data-ttu-id="f736e-144">`#nullable enable annotations`：将可为 null 的注释上下文设置为*enabled*</span><span class="sxs-lookup"><span data-stu-id="f736e-144">`#nullable enable annotations`: Sets the nullable annotation context to *enabled*</span></span>
- <span data-ttu-id="f736e-145">`#nullable restore annotations`：将可为 null 的注释上下文还原到项目设置</span><span class="sxs-lookup"><span data-stu-id="f736e-145">`#nullable restore annotations`: Restores the nullable annotation context to project settings</span></span>
- <span data-ttu-id="f736e-146">`#nullable disable warnings`：将可为 null 的警告上下文设置为*已禁用*</span><span class="sxs-lookup"><span data-stu-id="f736e-146">`#nullable disable warnings`: Sets the nullable warning context to *disabled*</span></span>
- <span data-ttu-id="f736e-147">`#nullable enable warnings`：将可为 null 的警告上下文设置为*已启用*</span><span class="sxs-lookup"><span data-stu-id="f736e-147">`#nullable enable warnings`: Sets the nullable warning context to *enabled*</span></span>
- <span data-ttu-id="f736e-148">`#nullable restore warnings`：将可为 null 的警告上下文还原到项目设置</span><span class="sxs-lookup"><span data-stu-id="f736e-148">`#nullable restore warnings`: Restores the nullable warning context to project settings</span></span>

## <a name="nullability-of-types"></a><span data-ttu-id="f736e-149">类型为 Null 性</span><span class="sxs-lookup"><span data-stu-id="f736e-149">Nullability of types</span></span>

<span data-ttu-id="f736e-150">给定的类型可以有以下四个 nullabilities 之一：*在意*、*不可 null*、 *nullable*和*unknown*。</span><span class="sxs-lookup"><span data-stu-id="f736e-150">A given type can have one of four nullabilities: *Oblivious*, *nonnullable*, *nullable* and *unknown*.</span></span> 

<span data-ttu-id="f736e-151">如果将可能的值分配给*不可 null*和*未知*类型，则可能会引发警告 `null` 。</span><span class="sxs-lookup"><span data-stu-id="f736e-151">*Nonnullable* and *unknown* types may cause warnings if a potential `null` value is assigned to them.</span></span> <span data-ttu-id="f736e-152">但是，*在意*和可以*为*null 的类型为 "可*赋值*"，可以 `null` 为其分配值而不会出现警告。</span><span class="sxs-lookup"><span data-stu-id="f736e-152">*Oblivious* and *nullable* types, however, are "*null-assignable*" and can have `null` values assigned to them without warnings.</span></span> 

<span data-ttu-id="f736e-153">可以取消引用或分配*在意*和*不可 null*类型，而不会出现警告。</span><span class="sxs-lookup"><span data-stu-id="f736e-153">*Oblivious* and *nonnullable* types can be dereferenced or assigned without warnings.</span></span> <span data-ttu-id="f736e-154">然而，*可以为*null 的类型和*未知*类型的值都是 "*空*值"，当取消引用或赋值时，如果没有正确的 null 检查，则可能会导致警告。</span><span class="sxs-lookup"><span data-stu-id="f736e-154">Values of *nullable* and *unknown* types, however, are "*null-yielding*" and may cause warnings when dereferenced or assigned without proper null checking.</span></span> 

<span data-ttu-id="f736e-155">Null 生成类型的*默认 null 状态*为 "可能为 null"。</span><span class="sxs-lookup"><span data-stu-id="f736e-155">The *default null state* of a null-yielding type is "maybe null".</span></span> <span data-ttu-id="f736e-156">非 null 生成类型的默认 null 状态为 "not null"。</span><span class="sxs-lookup"><span data-stu-id="f736e-156">The default null state of a non-null-yielding type is "not null".</span></span>

<span data-ttu-id="f736e-157">类型的类型和在确定其为 null 性时所发生的可为 null 的注释上下文：</span><span class="sxs-lookup"><span data-stu-id="f736e-157">The kind of type and the nullable annotation context it occurs in determine its nullability:</span></span>

- <span data-ttu-id="f736e-158">不可 null 值类型 `S` 始终为*不可 null*</span><span class="sxs-lookup"><span data-stu-id="f736e-158">A nonnullable value type `S` is always *nonnullable*</span></span>
- <span data-ttu-id="f736e-159">可以为 null 的值类型 `S?` 始终*可以为 null*</span><span class="sxs-lookup"><span data-stu-id="f736e-159">A nullable value type `S?` is always *nullable*</span></span>
- <span data-ttu-id="f736e-160">`C`*禁用*的注释上下文中的批注引用类型为*在意*</span><span class="sxs-lookup"><span data-stu-id="f736e-160">An unannotated reference type `C` in a *disabled* annotation context is *oblivious*</span></span>
- <span data-ttu-id="f736e-161">`C`*启用*的批注上下文中的批注引用类型为*不可 null*</span><span class="sxs-lookup"><span data-stu-id="f736e-161">An unannotated reference type `C` in an *enabled* annotation context is *nonnullable*</span></span>
- <span data-ttu-id="f736e-162">可以为 null 的引用类型 `C?` *可以为 null* （但在*禁用*的批注上下文中可能生成警告）</span><span class="sxs-lookup"><span data-stu-id="f736e-162">A nullable reference type `C?` is *nullable* (but a warning may be yielded in a *disabled* annotation context)</span></span>

<span data-ttu-id="f736e-163">类型参数还会考虑它们的约束：</span><span class="sxs-lookup"><span data-stu-id="f736e-163">Type parameters additionally take their constraints into account:</span></span>

- <span data-ttu-id="f736e-164">一个类型参数 `T` ，其中所有约束（如果有）为 null 生成类型（*可为 null*和*未知*）或 `class?` 约束*未知*</span><span class="sxs-lookup"><span data-stu-id="f736e-164">A type parameter `T` where all constraints (if any) are either null-yielding types (*nullable* and *unknown*) or the `class?` constraint is *unknown*</span></span>
- <span data-ttu-id="f736e-165">一个类型参数 `T` ，其中至少有一个约束为*在意*或*不可 null* ，或者其中一个 `struct` 或 `class` 约束为</span><span class="sxs-lookup"><span data-stu-id="f736e-165">A type parameter `T` where at least one constraint is either *oblivious* or *nonnullable* or one of the `struct` or `class` constraints is</span></span>
    - <span data-ttu-id="f736e-166">*禁用*的注释上下文中的*在意*</span><span class="sxs-lookup"><span data-stu-id="f736e-166">*oblivious* in a *disabled* annotation context</span></span>
    - <span data-ttu-id="f736e-167">*已启用*的批注上下文中的*不可 null*</span><span class="sxs-lookup"><span data-stu-id="f736e-167">*nonnullable* in an *enabled* annotation context</span></span>
- <span data-ttu-id="f736e-168">一个可以为 null 的类型参数， `T?` 其中至少有一个 `T` 约束是*在意*或*不可 null* ，或者是 `struct` 或 `class` 约束之一，是</span><span class="sxs-lookup"><span data-stu-id="f736e-168">A nullable type parameter `T?` where at least one of `T`'s constraints is *oblivious* or *nonnullable* or one of the `struct` or `class` constraints, is</span></span>
    - <span data-ttu-id="f736e-169">在*禁用*的注释上下文中*可以为 null* （但生成了警告）</span><span class="sxs-lookup"><span data-stu-id="f736e-169">*nullable* in a *disabled* annotation context (but a warning is yielded)</span></span>
    - <span data-ttu-id="f736e-170">在*启用*的批注上下文中*为 null*</span><span class="sxs-lookup"><span data-stu-id="f736e-170">*nullable* in an *enabled* annotation context</span></span>

<span data-ttu-id="f736e-171">对于类型参数 `T` ， `T?` 仅当 `T` 已知为值类型或已知为引用类型时，才允许。</span><span class="sxs-lookup"><span data-stu-id="f736e-171">For a type parameter `T`, `T?` is only allowed if `T` is known to be a value type or known to be a reference type.</span></span>

### <a name="oblivious-vs-nonnullable"></a><span data-ttu-id="f736e-172">在意 vs 不可 null</span><span class="sxs-lookup"><span data-stu-id="f736e-172">Oblivious vs nonnullable</span></span>

<span data-ttu-id="f736e-173">`type`当类型的最后一个标记在该上下文中时，将被视为在给定的批注上下文中发生。</span><span class="sxs-lookup"><span data-stu-id="f736e-173">A `type` is deemed to occur in a given annotation context when the last token of the type is within that context.</span></span>

<span data-ttu-id="f736e-174">源代码中的给定引用类型 `C` 是解释为在意还是不可 null 依赖于源代码的注释上下文。</span><span class="sxs-lookup"><span data-stu-id="f736e-174">Whether a given reference type `C` in source code is interpreted as oblivious or nonnullable depends on the annotation context of that source code.</span></span> <span data-ttu-id="f736e-175">但一旦建立，就会将其视为该类型的一部分，并在替换泛型类型参数的过程中将其视为 "随之一起移动"。</span><span class="sxs-lookup"><span data-stu-id="f736e-175">But once established, it is considered part of that type, and "travels with it" e.g. during substitution of generic type arguments.</span></span> <span data-ttu-id="f736e-176">这就像在类型上有一个批注 `?` ，但不可见。</span><span class="sxs-lookup"><span data-stu-id="f736e-176">It is as if there is an annotation like `?` on the type, but invisible.</span></span>

## <a name="constraints"></a><span data-ttu-id="f736e-177">约束</span><span class="sxs-lookup"><span data-stu-id="f736e-177">Constraints</span></span>

<span data-ttu-id="f736e-178">可以为 null 的引用类型可用作泛型约束。</span><span class="sxs-lookup"><span data-stu-id="f736e-178">Nullable reference types can be used as generic constraints.</span></span> <span data-ttu-id="f736e-179">而且 `object` 现在作为显式约束有效。</span><span class="sxs-lookup"><span data-stu-id="f736e-179">Furthermore `object` is now valid as an explicit constraint.</span></span> <span data-ttu-id="f736e-180">缺少约束现在等效于 `object?` 约束（而不是 `object` ），而 `object` 不是将其 `object?` 视为显式约束（与之前不同）。</span><span class="sxs-lookup"><span data-stu-id="f736e-180">Absence of a constraint is now equivalent to an `object?` constraint (instead of `object`), but (unlike `object` before) `object?` is not prohibited as an explicit constraint.</span></span>

<span data-ttu-id="f736e-181">`class?`表示 "可能可以为 null 的引用类型" 的新约束，而 `class` 表示 "不可 null 引用类型"。</span><span class="sxs-lookup"><span data-stu-id="f736e-181">`class?` is a new constraint denoting "possibly nullable reference type", whereas `class` denotes "nonnullable reference type".</span></span>

<span data-ttu-id="f736e-182">类型参数或约束的为空性并不会影响类型是否满足约束，但目前已有的情况除外（可以为 null 的值类型不满足该 `struct` 约束）。</span><span class="sxs-lookup"><span data-stu-id="f736e-182">The nullability of a type argument or of a constraint does not impact whether the type satisfies the constraint, except where that is already the case today (nullable value types do not satisfy the `struct` constraint).</span></span> <span data-ttu-id="f736e-183">但是，如果类型参数不满足约束的为 null 性要求，则可以提供警告。</span><span class="sxs-lookup"><span data-stu-id="f736e-183">However, if the type argument does not satisfy the nullability requirements of the constraint, a warning may be given.</span></span>

## <a name="null-state-and-null-tracking"></a><span data-ttu-id="f736e-184">Null 状态和 null 跟踪</span><span class="sxs-lookup"><span data-stu-id="f736e-184">Null state and null tracking</span></span>

<span data-ttu-id="f736e-185">给定源位置中的每个表达式都具有*null 状态*，指示其是否被认为可能计算为 null。</span><span class="sxs-lookup"><span data-stu-id="f736e-185">Every expression in a given source location has a *null state*, which indicated whether it is believed to potentially evaluate to null.</span></span> <span data-ttu-id="f736e-186">Null 状态为 "not null" 或 "可能为 null"。</span><span class="sxs-lookup"><span data-stu-id="f736e-186">The null state is either "not null" or "maybe null".</span></span> <span data-ttu-id="f736e-187">Null 状态用于确定是否应为不安全的转换和取消引用提供警告。</span><span class="sxs-lookup"><span data-stu-id="f736e-187">The null state is used to determine whether a warning should be given about null-unsafe conversions and dereferences.</span></span>

### <a name="null-tracking-for-variables"></a><span data-ttu-id="f736e-188">变量的 Null 跟踪</span><span class="sxs-lookup"><span data-stu-id="f736e-188">Null tracking for variables</span></span>

<span data-ttu-id="f736e-189">对于指定变量或属性的某些表达式，将根据对它们的赋值、对它们执行的测试以及它们之间的控制流，在发生之间跟踪 null 状态。</span><span class="sxs-lookup"><span data-stu-id="f736e-189">For certain expressions denoting variables or properties, the null state is tracked between occurrences, based on assignments to them, tests performed on them and the control flow between them.</span></span> <span data-ttu-id="f736e-190">这类似于为变量跟踪明确赋值的方式。</span><span class="sxs-lookup"><span data-stu-id="f736e-190">This is similar to how definite assignment is tracked for variables.</span></span> <span data-ttu-id="f736e-191">跟踪的表达式如下所示：</span><span class="sxs-lookup"><span data-stu-id="f736e-191">The tracked expressions are the ones of the following form:</span></span>

```antlr
tracked_expression
    : simple_name
    | this
    | base
    | tracked_expression '.' identifier
    ;
```

<span data-ttu-id="f736e-192">其中，标识符表示字段或属性。</span><span class="sxs-lookup"><span data-stu-id="f736e-192">Where the identifiers denote fields or properties.</span></span>

<span data-ttu-id="f736e-193">***描述类似于明确赋值的空状态转换***</span><span class="sxs-lookup"><span data-stu-id="f736e-193">***Describe null state transitions similar to definite assignment***</span></span>

### <a name="null-state-for-expressions"></a><span data-ttu-id="f736e-194">表达式为 Null</span><span class="sxs-lookup"><span data-stu-id="f736e-194">Null state for expressions</span></span>

<span data-ttu-id="f736e-195">表达式的 null 状态派生自其形式和类型以及它所涉及的变量的 null 状态。</span><span class="sxs-lookup"><span data-stu-id="f736e-195">The null state of an expression is derived from its form and type, and from the null state of variables involved in it.</span></span>

### <a name="literals"></a><span data-ttu-id="f736e-196">文字</span><span class="sxs-lookup"><span data-stu-id="f736e-196">Literals</span></span>

<span data-ttu-id="f736e-197">文本的 null 状态 `null` 为 "可能为 null"。</span><span class="sxs-lookup"><span data-stu-id="f736e-197">The null state of a `null` literal is "maybe null".</span></span> <span data-ttu-id="f736e-198">`default`正在转换为已知不是不可 null 值类型的类型的文本的 null 状态为 "可能为 null"。</span><span class="sxs-lookup"><span data-stu-id="f736e-198">The null state of a `default` literal that is being converted to a type that is known not to be a nonnullable value type is "maybe null".</span></span> <span data-ttu-id="f736e-199">任何其他文本的 null 状态为 "not null"。</span><span class="sxs-lookup"><span data-stu-id="f736e-199">The null state of any other literal is "not null".</span></span>

### <a name="simple-names"></a><span data-ttu-id="f736e-200">简单名称</span><span class="sxs-lookup"><span data-stu-id="f736e-200">Simple names</span></span>

<span data-ttu-id="f736e-201">如果 `simple_name` 未归类为值，则其 null 状态为 "not null"。</span><span class="sxs-lookup"><span data-stu-id="f736e-201">If a `simple_name` is not classified as a value, its null state is "not null".</span></span> <span data-ttu-id="f736e-202">否则为跟踪的表达式，其 null 状态将为其在此源位置跟踪的 null 状态。</span><span class="sxs-lookup"><span data-stu-id="f736e-202">Otherwise it is a tracked expression, and its null state is its tracked null state at this source location.</span></span>

### <a name="member-access"></a><span data-ttu-id="f736e-203">成员访问</span><span class="sxs-lookup"><span data-stu-id="f736e-203">Member access</span></span>

<span data-ttu-id="f736e-204">如果 `member_access` 未归类为值，则其 null 状态为 "not null"。</span><span class="sxs-lookup"><span data-stu-id="f736e-204">If a `member_access` is not classified as a value, its null state is "not null".</span></span> <span data-ttu-id="f736e-205">否则，如果是跟踪的表达式，则其 null 状态将为其在此源位置跟踪的 null 状态。</span><span class="sxs-lookup"><span data-stu-id="f736e-205">Otherwise, if it is a tracked expression, its null state is its tracked null state at this source location.</span></span> <span data-ttu-id="f736e-206">否则，其 null 状态为其类型的默认 null 状态。</span><span class="sxs-lookup"><span data-stu-id="f736e-206">Otherwise its null state is the default null state of its type.</span></span>

### <a name="invocation-expressions"></a><span data-ttu-id="f736e-207">调用表达式</span><span class="sxs-lookup"><span data-stu-id="f736e-207">Invocation expressions</span></span>

<span data-ttu-id="f736e-208">如果 `invocation_expression` 调用使用一个或多个特性声明的成员以实现特殊的 null 行为，则 null 状态由这些特性决定。</span><span class="sxs-lookup"><span data-stu-id="f736e-208">If an `invocation_expression` invokes a member that is declared with one or more attributes for special null behavior, the null state is determined by those attributes.</span></span> <span data-ttu-id="f736e-209">否则，表达式的 null 状态为其类型的默认 null 状态。</span><span class="sxs-lookup"><span data-stu-id="f736e-209">Otherwise the null state of the expression is the default null state of its type.</span></span>

### <a name="element-access"></a><span data-ttu-id="f736e-210">元素访问</span><span class="sxs-lookup"><span data-stu-id="f736e-210">Element access</span></span>

<span data-ttu-id="f736e-211">如果 `element_access` 调用使用一个或多个特性声明的索引器来实现特殊的 null 行为，则 null 状态由这些特性决定。</span><span class="sxs-lookup"><span data-stu-id="f736e-211">If an `element_access` invokes an indexer that is declared with one or more attributes for special null behavior, the null state is determined by those attributes.</span></span> <span data-ttu-id="f736e-212">否则，表达式的 null 状态为其类型的默认 null 状态。</span><span class="sxs-lookup"><span data-stu-id="f736e-212">Otherwise the null state of the expression is the default null state of its type.</span></span>

### <a name="base-access"></a><span data-ttu-id="f736e-213">基本访问权限</span><span class="sxs-lookup"><span data-stu-id="f736e-213">Base access</span></span>

<span data-ttu-id="f736e-214">如果 `B` 表示封闭类型的基类型， `base.I` 则具有与相同的 null 状态， `((B)this).I` 并且与 `base[E]` 具有相同的 null 状态 `((B)this)[E]` 。</span><span class="sxs-lookup"><span data-stu-id="f736e-214">If `B` denotes the base type of the enclosing type, `base.I` has the same null state as `((B)this).I` and `base[E]` has the same null state as `((B)this)[E]`.</span></span>

### <a name="default-expressions"></a><span data-ttu-id="f736e-215">默认表达式</span><span class="sxs-lookup"><span data-stu-id="f736e-215">Default expressions</span></span>

<span data-ttu-id="f736e-216">`default(T)`如果 `T` 已知为不可 null 值类型，则具有 null 状态 "非 null"。</span><span class="sxs-lookup"><span data-stu-id="f736e-216">`default(T)` has the null state "non-null" if `T` is known to be a nonnullable value type.</span></span> <span data-ttu-id="f736e-217">否则，它的状态为 "可能为 null"。</span><span class="sxs-lookup"><span data-stu-id="f736e-217">Otherwise it has the null state "maybe null".</span></span>

### <a name="null-conditional-expressions"></a><span data-ttu-id="f736e-218">Null 条件表达式</span><span class="sxs-lookup"><span data-stu-id="f736e-218">Null-conditional expressions</span></span>

<span data-ttu-id="f736e-219">`null_conditional_expression`具有 null 状态 "可能为 null"。</span><span class="sxs-lookup"><span data-stu-id="f736e-219">A `null_conditional_expression` has the null state "maybe null".</span></span>

### <a name="cast-expressions"></a><span data-ttu-id="f736e-220">强制转换表达式</span><span class="sxs-lookup"><span data-stu-id="f736e-220">Cast expressions</span></span>

<span data-ttu-id="f736e-221">如果强制转换表达式 `(T)E` 调用用户定义的转换，则表达式的 null 状态为其类型的默认 null 状态。</span><span class="sxs-lookup"><span data-stu-id="f736e-221">If a cast expression `(T)E` invokes a user-defined conversion, then the null state of the expression is the default null state for its type.</span></span> <span data-ttu-id="f736e-222">否则，如果 `T` 为 null 生成（*可为*null 或*未知*），则 null 状态为 "可能为 null"。</span><span class="sxs-lookup"><span data-stu-id="f736e-222">Otherwise, if `T` is null-yielding (*nullable* or *unknown*) then the null state is "maybe null".</span></span> <span data-ttu-id="f736e-223">否则，null 状态与的 null 状态相同 `E` 。</span><span class="sxs-lookup"><span data-stu-id="f736e-223">Otherwise the null state is the same as the null state of `E`.</span></span>

### <a name="await-expressions"></a><span data-ttu-id="f736e-224">Await 表达式</span><span class="sxs-lookup"><span data-stu-id="f736e-224">Await expressions</span></span>

<span data-ttu-id="f736e-225">的 null 状态 `await E` 为其类型的默认 null 状态。</span><span class="sxs-lookup"><span data-stu-id="f736e-225">The null state of `await E` is the default null state of its type.</span></span>

### <a name="the-as-operator"></a><span data-ttu-id="f736e-226">`as`运算符</span><span class="sxs-lookup"><span data-stu-id="f736e-226">The `as` operator</span></span>

<span data-ttu-id="f736e-227">`as`表达式的状态为 "可能为 null"。</span><span class="sxs-lookup"><span data-stu-id="f736e-227">An `as` expression has the null state "maybe null".</span></span>

### <a name="the-null-coalescing-operator"></a><span data-ttu-id="f736e-228">Null 合并运算符</span><span class="sxs-lookup"><span data-stu-id="f736e-228">The null-coalescing operator</span></span>

<span data-ttu-id="f736e-229">`E1 ?? E2`具有与相同的空状态`E2`</span><span class="sxs-lookup"><span data-stu-id="f736e-229">`E1 ?? E2` has the same null state as `E2`</span></span>

### <a name="the-conditional-operator"></a><span data-ttu-id="f736e-230">条件运算符</span><span class="sxs-lookup"><span data-stu-id="f736e-230">The conditional operator</span></span>

<span data-ttu-id="f736e-231">`E1 ? E2 : E3`如果和的 null 状态为 `E2` `E3` "not null"，则的 null 状态为 "not null"。</span><span class="sxs-lookup"><span data-stu-id="f736e-231">The null state of `E1 ? E2 : E3` is "not null" if the null state of both `E2` and `E3` are "not null".</span></span> <span data-ttu-id="f736e-232">否则为 "可能为 null"。</span><span class="sxs-lookup"><span data-stu-id="f736e-232">Otherwise it is "maybe null".</span></span>

### <a name="query-expressions"></a><span data-ttu-id="f736e-233">查询表达式</span><span class="sxs-lookup"><span data-stu-id="f736e-233">Query expressions</span></span>

<span data-ttu-id="f736e-234">查询表达式的 null 状态为其类型的默认 null 状态。</span><span class="sxs-lookup"><span data-stu-id="f736e-234">The null state of a query expression is the default null state of its type.</span></span>

### <a name="assignment-operators"></a><span data-ttu-id="f736e-235">赋值运算符</span><span class="sxs-lookup"><span data-stu-id="f736e-235">Assignment operators</span></span>

<span data-ttu-id="f736e-236">`E1 = E2`和 `E1 op= E2` 都具有与 `E2` 应用任何隐式转换相同的空状态。</span><span class="sxs-lookup"><span data-stu-id="f736e-236">`E1 = E2` and `E1 op= E2` have the same null state as `E2` after any implicit conversions have been applied.</span></span>

### <a name="unary-and-binary-operators"></a><span data-ttu-id="f736e-237">一元运算符和二元运算符</span><span class="sxs-lookup"><span data-stu-id="f736e-237">Unary and binary operators</span></span>

<span data-ttu-id="f736e-238">如果一元或二元运算符调用了用一个或多个特性声明的用户定义的运算符来实现特殊的 null 行为，则 null 状态由这些特性决定。</span><span class="sxs-lookup"><span data-stu-id="f736e-238">If a unary or binary operator invokes an user-defined operator that is declared with one or more attributes for special null behavior, the null state is determined by those attributes.</span></span> <span data-ttu-id="f736e-239">否则，表达式的 null 状态为其类型的默认 null 状态。</span><span class="sxs-lookup"><span data-stu-id="f736e-239">Otherwise the null state of the expression is the default null state of its type.</span></span>

<span data-ttu-id="f736e-240">***对于字符串和委托，二进制文件的特殊操作是什么 `+` ？***</span><span class="sxs-lookup"><span data-stu-id="f736e-240">***Something special to do for binary `+` over strings and delegates?***</span></span>

### <a name="expressions-that-propagate-null-state"></a><span data-ttu-id="f736e-241">传播 null 状态的表达式</span><span class="sxs-lookup"><span data-stu-id="f736e-241">Expressions that propagate null state</span></span>

<span data-ttu-id="f736e-242">`(E)``checked(E)`和 `unchecked(E)` 都具有与相同的 null 状态 `E` 。</span><span class="sxs-lookup"><span data-stu-id="f736e-242">`(E)`, `checked(E)` and `unchecked(E)` all have the same null state as `E`.</span></span>

### <a name="expressions-that-are-never-null"></a><span data-ttu-id="f736e-243">从不为 null 的表达式</span><span class="sxs-lookup"><span data-stu-id="f736e-243">Expressions that are never null</span></span>

<span data-ttu-id="f736e-244">以下表达式格式的 null 状态始终为 "not null"：</span><span class="sxs-lookup"><span data-stu-id="f736e-244">The null state of the following expression forms is always "not null":</span></span>

- <span data-ttu-id="f736e-245">`this` access</span><span class="sxs-lookup"><span data-stu-id="f736e-245">`this` access</span></span>
- <span data-ttu-id="f736e-246">内插字符串</span><span class="sxs-lookup"><span data-stu-id="f736e-246">interpolated strings</span></span>
- <span data-ttu-id="f736e-247">`new`表达式（对象、委托、匿名对象和数组创建表达式）</span><span class="sxs-lookup"><span data-stu-id="f736e-247">`new` expressions (object, delegate, anonymous object and array creation expressions)</span></span>
- <span data-ttu-id="f736e-248">`typeof` 表达式</span><span class="sxs-lookup"><span data-stu-id="f736e-248">`typeof` expressions</span></span>
- <span data-ttu-id="f736e-249">`nameof` 表达式</span><span class="sxs-lookup"><span data-stu-id="f736e-249">`nameof` expressions</span></span>
- <span data-ttu-id="f736e-250">匿名函数（匿名方法和 lambda 表达式）</span><span class="sxs-lookup"><span data-stu-id="f736e-250">anonymous functions (anonymous methods and lambda expressions)</span></span>
- <span data-ttu-id="f736e-251">包容性表达式</span><span class="sxs-lookup"><span data-stu-id="f736e-251">null-forgiving expressions</span></span>
- <span data-ttu-id="f736e-252">`is` 表达式</span><span class="sxs-lookup"><span data-stu-id="f736e-252">`is` expressions</span></span>

## <a name="type-inference"></a><span data-ttu-id="f736e-253">类型推理</span><span class="sxs-lookup"><span data-stu-id="f736e-253">Type inference</span></span>

### <a name="type-inference-for-var"></a><span data-ttu-id="f736e-254">的类型推理`var`</span><span class="sxs-lookup"><span data-stu-id="f736e-254">Type inference for `var`</span></span>

<span data-ttu-id="f736e-255">为声明的局部变量声明的类型 `var` 由初始化表达式的 null 状态通知。</span><span class="sxs-lookup"><span data-stu-id="f736e-255">The type inferred for local variables declared with `var` is informed by the null state of the initializing expression.</span></span>

```csharp
var x = E;
```

<span data-ttu-id="f736e-256">如果的类型 `E` 是可以为 null 的引用类型 `C?` ，并且的 null 状态 `E` 为 "not null"，则为推断的类型 `x` 为 `C` 。</span><span class="sxs-lookup"><span data-stu-id="f736e-256">If the type of `E` is a nullable reference type `C?` and the null state of `E` is "not null" then the type inferred for `x` is `C`.</span></span> <span data-ttu-id="f736e-257">否则，推理出的类型是的类型 `E` 。</span><span class="sxs-lookup"><span data-stu-id="f736e-257">Otherwise, the inferred type is the type of `E`.</span></span>

<span data-ttu-id="f736e-258">为推断的类型的可为 null 性根据 `x` 的注释上下文确定，这一点与该 `var` 位置中显式给定的类型相同。</span><span class="sxs-lookup"><span data-stu-id="f736e-258">The nullability of the type inferred for `x` is determined as described above, based on the annotation context of the `var`, just as if the type had been given explicitly in that position.</span></span>

### <a name="type-inference-for-var"></a><span data-ttu-id="f736e-259">的类型推理`var?`</span><span class="sxs-lookup"><span data-stu-id="f736e-259">Type inference for `var?`</span></span>

<span data-ttu-id="f736e-260">为声明的局部变量的类型与 `var?` 初始化表达式的 null 状态无关。</span><span class="sxs-lookup"><span data-stu-id="f736e-260">The type inferred for local variables declared with `var?` is independent of the null state of the initializing expression.</span></span>

```csharp
var? x = E;
```

<span data-ttu-id="f736e-261">如果的类型 `T` `E` 是可以为 null 的值类型或可以为 null 的引用类型，则为推断的类型 `x` 为 `T` 。</span><span class="sxs-lookup"><span data-stu-id="f736e-261">If the type `T` of `E` is a nullable value type or a nullable reference type then the type inferred for `x` is `T`.</span></span> <span data-ttu-id="f736e-262">否则，如果 `T` 是不可 null 值类型，则 `S` 推断出的类型为 `S?` 。</span><span class="sxs-lookup"><span data-stu-id="f736e-262">Otherwise, if `T` is a nonnullable value type `S` the type inferred is `S?`.</span></span> <span data-ttu-id="f736e-263">否则，如果 `T` 是不可 null 引用类型，则 `C` 推断出的类型为 `C?` 。</span><span class="sxs-lookup"><span data-stu-id="f736e-263">Otherwise, if `T` is a nonnullable reference type `C` the type inferred is `C?`.</span></span> <span data-ttu-id="f736e-264">否则，声明是非法的。</span><span class="sxs-lookup"><span data-stu-id="f736e-264">Otherwise, the declaration is illegal.</span></span>

<span data-ttu-id="f736e-265">为推断的类型的为空性 `x` 始终*可以为 null*。</span><span class="sxs-lookup"><span data-stu-id="f736e-265">The nullability of the type inferred for `x` is always *nullable*.</span></span>

### <a name="generic-type-inference"></a><span data-ttu-id="f736e-266">泛型类型推理</span><span class="sxs-lookup"><span data-stu-id="f736e-266">Generic type inference</span></span>

<span data-ttu-id="f736e-267">泛型类型推理经过了增强，可帮助确定推断的引用类型是否可以为 null。</span><span class="sxs-lookup"><span data-stu-id="f736e-267">Generic type inference is enhanced to help decide whether inferred reference types should be nullable or not.</span></span> <span data-ttu-id="f736e-268">这是一项最大的工作，并且本身不会生成警告，但在将所选重载的推断类型应用于参数时，可能会导致可以为 null 的警告。</span><span class="sxs-lookup"><span data-stu-id="f736e-268">This is a best effort, and does not in and of itself yield warnings, but may lead to nullable warnings when the inferred types of the selected overload are applied to the arguments.</span></span>

<span data-ttu-id="f736e-269">类型推断不依赖于传入类型的注释上下文。</span><span class="sxs-lookup"><span data-stu-id="f736e-269">The type inference does not rely on the annotation context of incoming types.</span></span> <span data-ttu-id="f736e-270">相反 `type` ，将从其 "可能已在" 中获取其自己的注释上下文（如果已显式表示）。</span><span class="sxs-lookup"><span data-stu-id="f736e-270">Instead a `type` is inferred which acquires its own annotation context from where it "would have been" if it had been expressed explicitly.</span></span> <span data-ttu-id="f736e-271">这会将类型推理的角色作为一种简便方式，方便您自己编写的内容。</span><span class="sxs-lookup"><span data-stu-id="f736e-271">This underscores the role of type inference as a convenience for what you could have written yourself.</span></span>

<span data-ttu-id="f736e-272">更准确地说，推理出的类型实参的注释上下文是标记的上下文，该标记后面应跟 `<...>` 有一个类型形参列表，其中有一个，即要调用的泛型方法的名称。</span><span class="sxs-lookup"><span data-stu-id="f736e-272">More precisely, the annotation context for an inferred type argument is the context of the token that would have been followed by the `<...>` type parameter list, had there been one; i.e. the name of the generic method being called.</span></span> <span data-ttu-id="f736e-273">对于转换为此类调用的查询表达式，上下文取自从中生成调用的查询子句的初始上下文关键字。</span><span class="sxs-lookup"><span data-stu-id="f736e-273">For query expressions that translate to such calls, the context is taken from the initial contextual keyword of the query clause from which the call is generated.</span></span>

### <a name="the-first-phase"></a><span data-ttu-id="f736e-274">第一阶段</span><span class="sxs-lookup"><span data-stu-id="f736e-274">The first phase</span></span>

<span data-ttu-id="f736e-275">可以为 null 的引用类型从初始表达式流入边界，如下所述。</span><span class="sxs-lookup"><span data-stu-id="f736e-275">Nullable reference types flow into the bounds from the initial expressions, as described below.</span></span> <span data-ttu-id="f736e-276">此外，引入了两种新的界限，即 `null` 和 `default` 。</span><span class="sxs-lookup"><span data-stu-id="f736e-276">In addition, two new kinds of bounds, namely `null` and `default` are introduced.</span></span> <span data-ttu-id="f736e-277">其目的是在输入表达式中执行 `null` 或 `default` ，这可能会导致推断出的类型可以为 null，即使在其他情况下也是如此。</span><span class="sxs-lookup"><span data-stu-id="f736e-277">Their purpose is to carry through occurrences of `null` or `default` in the input expressions, which may cause an inferred type to be nullable, even when it otherwise wouldn't.</span></span> <span data-ttu-id="f736e-278">这甚至适用于可为 null 的值类型，这些*值*类型得到了增强，可在推断过程中选取 "非 null"。</span><span class="sxs-lookup"><span data-stu-id="f736e-278">This works even for nullable *value* types, which are enhanced to pick up "nullness" in the inference process.</span></span>

<span data-ttu-id="f736e-279">在第一阶段中，确定要添加的界限如下：</span><span class="sxs-lookup"><span data-stu-id="f736e-279">The determination of what bounds to add in the first phase are enhanced as follows:</span></span>

<span data-ttu-id="f736e-280">如果参数 `Ei` 具有引用类型，则 `U` 用于推理的类型取决于的 null 状态及其 `Ei` 声明的类型：</span><span class="sxs-lookup"><span data-stu-id="f736e-280">If an argument `Ei` has a reference type, the type `U` used for inference depends on the null state of `Ei` as well as its declared type:</span></span>
- <span data-ttu-id="f736e-281">如果声明的类型是不可 null 引用类型 `U0` 或可以为 null 的引用类型， `U0?` 则</span><span class="sxs-lookup"><span data-stu-id="f736e-281">If the declared type is a nonnullable reference type `U0` or a nullable reference type `U0?` then</span></span>
    - <span data-ttu-id="f736e-282">如果的 null 状态 `Ei` 为 "not null"，则 `U` 为`U0`</span><span class="sxs-lookup"><span data-stu-id="f736e-282">if the null state of `Ei` is "not null" then `U` is `U0`</span></span>
    - <span data-ttu-id="f736e-283">如果的 null 状态 `Ei` 为 "可能为 null"，则 `U` 为`U0?`</span><span class="sxs-lookup"><span data-stu-id="f736e-283">if the null state of `Ei` is "maybe null" then `U` is `U0?`</span></span>
- <span data-ttu-id="f736e-284">否则 `Ei` ，如果具有已声明的类型， `U` 则为该类型</span><span class="sxs-lookup"><span data-stu-id="f736e-284">Otherwise if `Ei` has a declared type, `U` is that type</span></span>
- <span data-ttu-id="f736e-285">否则，如果为，则 `Ei` `null` `U` 为特殊界限`null`</span><span class="sxs-lookup"><span data-stu-id="f736e-285">Otherwise if `Ei` is `null` then `U` is the special bound `null`</span></span>
- <span data-ttu-id="f736e-286">否则，如果为，则 `Ei` `default` `U` 为特殊界限`default`</span><span class="sxs-lookup"><span data-stu-id="f736e-286">Otherwise if `Ei` is `default` then `U` is the special bound `default`</span></span>
- <span data-ttu-id="f736e-287">否则，不进行推理。</span><span class="sxs-lookup"><span data-stu-id="f736e-287">Otherwise no inference is made.</span></span>

### <a name="exact-upper-bound-and-lower-bound-inferences"></a><span data-ttu-id="f736e-288">精确、上限和下限推理</span><span class="sxs-lookup"><span data-stu-id="f736e-288">Exact, upper-bound and lower-bound inferences</span></span>

<span data-ttu-id="f736e-289">在*从*类型推断 `U` *为*类型时 `V` ，如果 `V` 是可以为 null 的引用类型，则将在 `V0?` `V0` `V` 以下子句中使用而不是。</span><span class="sxs-lookup"><span data-stu-id="f736e-289">In inferences *from* the type `U` *to* the type `V`, if `V` is a nullable reference type `V0?`, then `V0` is used instead of `V` in the following clauses.</span></span>
- <span data-ttu-id="f736e-290">如果 `V` 是未固定的类型变量中的一个， `U` 则会像以前一样，添加一个精确、上下限或下限</span><span class="sxs-lookup"><span data-stu-id="f736e-290">If `V` is one of the unfixed type variables, `U` is added as an exact, upper or lower bound as before</span></span>
- <span data-ttu-id="f736e-291">否则，如果 `U` 为 `null` 或 `default` ，则不进行推理</span><span class="sxs-lookup"><span data-stu-id="f736e-291">Otherwise, if `U` is `null` or `default`, no inference is made</span></span>
- <span data-ttu-id="f736e-292">否则，如果 `U` 是可以为 null 的引用类型 `U0?` ，则 `U0` 将在 `U` 后续子句中使用而不是。</span><span class="sxs-lookup"><span data-stu-id="f736e-292">Otherwise, if `U` is a nullable reference type `U0?`, then `U0` is used instead of `U` in the subsequent clauses.</span></span>

<span data-ttu-id="f736e-293">实质上，与某个未固定的类型变量直接相关的可为 null 将保留在其边界内。</span><span class="sxs-lookup"><span data-stu-id="f736e-293">The essence is that nullability that pertains directly to one of the unfixed type variables is preserved into its bounds.</span></span> <span data-ttu-id="f736e-294">另一方面，如果推断将进一步递归到源和目标类型，则会忽略为空性。</span><span class="sxs-lookup"><span data-stu-id="f736e-294">For the inferences that recurse further into the source and target types, on the other hand, nullability is ignored.</span></span> <span data-ttu-id="f736e-295">它可以或不匹配，但如果不匹配，则会在以后选择并应用重载时发出警告。</span><span class="sxs-lookup"><span data-stu-id="f736e-295">It may or may not match, but if it doesn't, a warning will be issued later if the overload is chosen and applied.</span></span>

### <a name="fixing"></a><span data-ttu-id="f736e-296">修正 </span><span class="sxs-lookup"><span data-stu-id="f736e-296">Fixing</span></span>

<span data-ttu-id="f736e-297">如果多个边界彼此之间相互转换，但不同，则该规范并不是一种很好的描述。</span><span class="sxs-lookup"><span data-stu-id="f736e-297">The spec currently does not do a good job of describing what happens when multiple bounds are identity convertible to each other, but are different.</span></span> <span data-ttu-id="f736e-298">这种情况可能发生 `object` 在与之间， `dynamic` 在不同于元素名称的元组类型之间、构造它们的类型之间以及 `C` `C?` 引用类型中的类型之间。</span><span class="sxs-lookup"><span data-stu-id="f736e-298">This may happen between `object` and `dynamic`, between tuple types that differ only in element names, between types constructed thereof and now also between `C` and `C?` for reference types.</span></span>

<span data-ttu-id="f736e-299">此外，我们还需要将 "非 null" 从输入表达式传播到结果类型。</span><span class="sxs-lookup"><span data-stu-id="f736e-299">In addition we need to propagate "nullness" from the input expressions to the result type.</span></span> 

<span data-ttu-id="f736e-300">为了应对这些情况，我们添加了更多的修复阶段，这现在是：</span><span class="sxs-lookup"><span data-stu-id="f736e-300">To handle these we add more phases to fixing, which is now:</span></span>

1. <span data-ttu-id="f736e-301">将所有边界中的所有类型都作为候选项收集， `?` 从所有可为 null 的引用类型中移除</span><span class="sxs-lookup"><span data-stu-id="f736e-301">Gather all the types in all the bounds as candidates, removing `?` from all that are nullable reference types</span></span>
2. <span data-ttu-id="f736e-302">根据对精确、下限和上限的要求（保持和限制）来消除候选 `null` `default`</span><span class="sxs-lookup"><span data-stu-id="f736e-302">Eliminate candidates based on requirements of exact, lower and upper bounds (keeping `null` and `default` bounds)</span></span>
3. <span data-ttu-id="f736e-303">消除没有隐式转换为所有其他候选项的候选项</span><span class="sxs-lookup"><span data-stu-id="f736e-303">Eliminate candidates that do not have an implicit conversion to all the other candidates</span></span>
4. <span data-ttu-id="f736e-304">如果剩余的候选项彼此之间没有标识转换，则类型推理将失败</span><span class="sxs-lookup"><span data-stu-id="f736e-304">If the remaining candidates do not all have identity conversions to one another, then type inference fails</span></span>
5. <span data-ttu-id="f736e-305">按如下所述*合并*剩余候选项</span><span class="sxs-lookup"><span data-stu-id="f736e-305">*Merge* the remaining candidates as described below</span></span>
6. <span data-ttu-id="f736e-306">如果生成的候选项是引用类型或不可 null 值类型，并且*所有*完全*限定或下限*都是可以为 null 的值类型、可以为 null 的引用类型 `null` 或 `default` ，则 `?` 将其添加到生成的候选项，使其成为可以为 null 的值类型或引用类型。</span><span class="sxs-lookup"><span data-stu-id="f736e-306">If the resulting candidate is a reference type or a nonnullable value type and *all* of the exact bounds or *any* of the lower bounds are nullable value types, nullable reference types, `null` or `default`, then `?` is added to the resulting candidate, making it a nullable value type or reference type.</span></span>

<span data-ttu-id="f736e-307">两个候选类型之间介绍了*合并*。</span><span class="sxs-lookup"><span data-stu-id="f736e-307">*Merging* is described between two candidate types.</span></span> <span data-ttu-id="f736e-308">它是可传递和可交换的，因此，可按任何顺序将候选项合并，并获得相同的最终结果。</span><span class="sxs-lookup"><span data-stu-id="f736e-308">It is transitive and commutative, so the candidates can be merged in any order with the same ultimate result.</span></span> <span data-ttu-id="f736e-309">如果两个候选类型不能相互转换，则它是不确定的。</span><span class="sxs-lookup"><span data-stu-id="f736e-309">It is undefined if the two candidate types are not identity convertible to each other.</span></span>

<span data-ttu-id="f736e-310">*Merge*函数采用两种候选类型和方向（ *+* 或 *-* ）：</span><span class="sxs-lookup"><span data-stu-id="f736e-310">The *Merge* function takes two candidate types and a direction (*+* or *-*):</span></span>

- <span data-ttu-id="f736e-311">*Merge*（ `T` ， `T` ， *d*） = T</span><span class="sxs-lookup"><span data-stu-id="f736e-311">*Merge*(`T`, `T`, *d*) = T</span></span>
- <span data-ttu-id="f736e-312">*Merge*（ `S` ， `T?` ， *+* ） = *merge*（ `S?` ， `T` ， *+* ） = *merge*（ `S` ， `T` ， *+* ）`?`</span><span class="sxs-lookup"><span data-stu-id="f736e-312">*Merge*(`S`, `T?`, *+*) = *Merge*(`S?`, `T`, *+*) = *Merge*(`S`, `T`, *+*)`?`</span></span>
- <span data-ttu-id="f736e-313">*Merge*（ `S` ， `T?` ， *-* ） = *merge*（ `S?` ， `T` ， *-* ） = *merge*（ `S` ， `T` ， *-* ）</span><span class="sxs-lookup"><span data-stu-id="f736e-313">*Merge*(`S`, `T?`, *-*) = *Merge*(`S?`, `T`, *-*) = *Merge*(`S`, `T`, *-*)</span></span>
- <span data-ttu-id="f736e-314">*Merge*（ `C<S1,...,Sn>` ， `C<T1,...,Tn>` ， *+* ） = `C<` *merge*（ `S1` ， `T1` ， *d1*） `,...,` *merge*（，， `Sn` `Tn` *dn*） `>` ，*其中*</span><span class="sxs-lookup"><span data-stu-id="f736e-314">*Merge*(`C<S1,...,Sn>`, `C<T1,...,Tn>`, *+*) = `C<`*Merge*(`S1`, `T1`, *d1*)`,...,`*Merge*(`Sn`, `Tn`, *dn*)`>`, *where*</span></span>
    - <span data-ttu-id="f736e-315">`di` = *+* 如果 `i` 的第一个类型参数 `C<...>` 是协变的</span><span class="sxs-lookup"><span data-stu-id="f736e-315">`di` = *+* if the `i`'th type parameter of `C<...>` is covariant</span></span>
    - <span data-ttu-id="f736e-316">`di` = *-* 如果的 `i` 第一个类型参数 `C<...>` 为逆变或固定</span><span class="sxs-lookup"><span data-stu-id="f736e-316">`di` = *-* if the `i`'th type parameter of `C<...>` is contra- or invariant</span></span>
- <span data-ttu-id="f736e-317">*Merge*（ `C<S1,...,Sn>` ， `C<T1,...,Tn>` ， *-* ） = `C<` *merge*（ `S1` ， `T1` ， *d1*） `,...,` *merge*（，， `Sn` `Tn` *dn*） `>` ，*其中*</span><span class="sxs-lookup"><span data-stu-id="f736e-317">*Merge*(`C<S1,...,Sn>`, `C<T1,...,Tn>`, *-*) = `C<`*Merge*(`S1`, `T1`, *d1*)`,...,`*Merge*(`Sn`, `Tn`, *dn*)`>`, *where*</span></span>
    - <span data-ttu-id="f736e-318">`di` = *-* 如果 `i` 的第一个类型参数 `C<...>` 是协变的</span><span class="sxs-lookup"><span data-stu-id="f736e-318">`di` = *-* if the `i`'th type parameter of `C<...>` is covariant</span></span>
    - <span data-ttu-id="f736e-319">`di` = *+* 如果的 `i` 第一个类型参数 `C<...>` 为逆变或固定</span><span class="sxs-lookup"><span data-stu-id="f736e-319">`di` = *+* if the `i`'th type parameter of `C<...>` is contra- or invariant</span></span>
- <span data-ttu-id="f736e-320">*Merge*（ `(S1 s1,..., Sn sn)` ， `(T1 t1,..., Tn tn)` ， *d*） = `(` *merge*（ `S1` ， `T1` ， *d*） `n1,...,` *merge*（ `Sn` ， `Tn` ， *d*） `nn)` ，*其中*</span><span class="sxs-lookup"><span data-stu-id="f736e-320">*Merge*(`(S1 s1,..., Sn sn)`, `(T1 t1,..., Tn tn)`, *d*) = `(`*Merge*(`S1`, `T1`, *d*)`n1,...,`*Merge*(`Sn`, `Tn`, *d*) `nn)`, *where*</span></span>
    - <span data-ttu-id="f736e-321">`ni`如果 `si` 和 `ti` 不同，或者两者都不存在，则不存在</span><span class="sxs-lookup"><span data-stu-id="f736e-321">`ni` is absent if `si` and `ti` differ, or if both are absent</span></span>
    - <span data-ttu-id="f736e-322">`ni`是 `si` `si` 和 `ti` 是否相同</span><span class="sxs-lookup"><span data-stu-id="f736e-322">`ni` is `si` if `si` and `ti` are the same</span></span>
- <span data-ttu-id="f736e-323">*Merge*（ `object` ， `dynamic` ） = *merge*（ `dynamic` ， `object` ） =`dynamic`</span><span class="sxs-lookup"><span data-stu-id="f736e-323">*Merge*(`object`, `dynamic`) = *Merge*(`dynamic`, `object`) = `dynamic`</span></span>

## <a name="warnings"></a><span data-ttu-id="f736e-324">警告</span><span class="sxs-lookup"><span data-stu-id="f736e-324">Warnings</span></span>

### <a name="potential-null-assignment"></a><span data-ttu-id="f736e-325">潜在 null 赋值</span><span class="sxs-lookup"><span data-stu-id="f736e-325">Potential null assignment</span></span>

### <a name="potential-null-dereference"></a><span data-ttu-id="f736e-326">潜在的空取消引用</span><span class="sxs-lookup"><span data-stu-id="f736e-326">Potential null dereference</span></span>

### <a name="constraint-nullability-mismatch"></a><span data-ttu-id="f736e-327">约束为空性不匹配</span><span class="sxs-lookup"><span data-stu-id="f736e-327">Constraint nullability mismatch</span></span>

### <a name="nullable-types-in-disabled-annotation-context"></a><span data-ttu-id="f736e-328">禁用的注释上下文中可以为 null 的类型</span><span class="sxs-lookup"><span data-stu-id="f736e-328">Nullable types in disabled annotation context</span></span>

## <a name="attributes-for-special-null-behavior"></a><span data-ttu-id="f736e-329">特殊 null 行为的特性</span><span class="sxs-lookup"><span data-stu-id="f736e-329">Attributes for special null behavior</span></span>



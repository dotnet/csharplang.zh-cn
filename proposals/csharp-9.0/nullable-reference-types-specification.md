---
ms.openlocfilehash: e6a784ef90308a5395c6b1db454a67d40c10e3e4
ms.sourcegitcommit: 00d9d791b6f1d9538a979a111e93cf935d9b6cfe
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/10/2020
ms.locfileid: "94423364"
---
# <a name="nullable-reference-types-specification"></a><span data-ttu-id="bc12f-101">可以为 null 的引用类型规范</span><span class="sxs-lookup"><span data-stu-id="bc12f-101">Nullable Reference Types Specification</span></span>

<span data-ttu-id="bc12f-102">\***这是一个正在进行的工作-缺少几个部件或这些部件不完整。** _</span><span class="sxs-lookup"><span data-stu-id="bc12f-102">\***This is a work in progress - several parts are missing or incomplete.** _</span></span>

<span data-ttu-id="bc12f-103">此功能添加了两种新类型的可为 null 的类型 (可以为 null 的引用类型和可以为 null 的现有值类型) 为 null 的泛型类型，并引入了静态流分析以实现 null 安全。</span><span class="sxs-lookup"><span data-stu-id="bc12f-103">This feature adds two new kinds of nullable types (nullable reference types and nullable generic types) to the existing nullable value types, and introduces a static flow analysis for purpose of null-safety.</span></span>

## <a name="syntax"></a><span data-ttu-id="bc12f-104">语法</span><span class="sxs-lookup"><span data-stu-id="bc12f-104">Syntax</span></span>

### <a name="nullable-reference-types-and-nullable-type-parameters"></a><span data-ttu-id="bc12f-105">可以为 null 的引用类型和可以为 null 的类型参数</span><span class="sxs-lookup"><span data-stu-id="bc12f-105">Nullable reference types and nullable type parameters</span></span>

<span data-ttu-id="bc12f-106">可以为 null 的引用类型和可以为 null 的类型参数的语法 `T?` 与可以为 null 的值类型的短格式相同，但没有相应的长格式。</span><span class="sxs-lookup"><span data-stu-id="bc12f-106">Nullable reference types and nullable type parameters have the same syntax `T?` as the short form of nullable value types, but do not have a corresponding long form.</span></span>

<span data-ttu-id="bc12f-107">出于规范的目的，当前 `nullable_type` 生产被重命名为 `nullable_value_type` ，并 `nullable_reference_type` `nullable_type_parameter` 添加了生产：</span><span class="sxs-lookup"><span data-stu-id="bc12f-107">For the purposes of the specification, the current `nullable_type` production is renamed to `nullable_value_type`, and `nullable_reference_type` and `nullable_type_parameter` productions are added:</span></span>

```antlr
type
    : value_type
    | reference_type
    | nullable_type_parameter
    | type_parameter
    | type_unsafe
    ;

reference_type
    : ...
    | nullable_reference_type
    ;

nullable_reference_type
    : non_nullable_reference_type '?'
    ;

non_nullable_reference_type
    : reference_type
    ;

nullable_type_parameter
    : non_nullable_non_value_type_parameter '?'
    ;

non_nullable_non_value_type_parameter
    : type_parameter
    ;
```

<span data-ttu-id="bc12f-108">`non_nullable_reference_type` `nullable_reference_type` 必须是不可 null 引用类型 (类、接口、委托或数组) 。</span><span class="sxs-lookup"><span data-stu-id="bc12f-108">The `non_nullable_reference_type` in a `nullable_reference_type` must be a nonnullable reference type (class, interface, delegate or array).</span></span>

<span data-ttu-id="bc12f-109">`non_nullable_non_value_type_parameter`In `nullable_type_parameter` 必须是不被约束为值类型的类型参数。</span><span class="sxs-lookup"><span data-stu-id="bc12f-109">The `non_nullable_non_value_type_parameter` in `nullable_type_parameter` must be a type parameter that isn't constrained to be a value type.</span></span>

<span data-ttu-id="bc12f-110">可以为 null 的引用类型和可以为 null 的类型参数不能出现在以下位置：</span><span class="sxs-lookup"><span data-stu-id="bc12f-110">Nullable reference types and nullable type parameters cannot occur in the following positions:</span></span>

- <span data-ttu-id="bc12f-111">作为基类或接口</span><span class="sxs-lookup"><span data-stu-id="bc12f-111">as a base class or interface</span></span>
- <span data-ttu-id="bc12f-112">作为的接收方 `member_access`</span><span class="sxs-lookup"><span data-stu-id="bc12f-112">as the receiver of a `member_access`</span></span>
- <span data-ttu-id="bc12f-113">作为 `type` 中的 `object_creation_expression`</span><span class="sxs-lookup"><span data-stu-id="bc12f-113">as the `type` in an `object_creation_expression`</span></span>
- <span data-ttu-id="bc12f-114">作为 `delegate_type` 中的 `delegate_creation_expression`</span><span class="sxs-lookup"><span data-stu-id="bc12f-114">as the `delegate_type` in a `delegate_creation_expression`</span></span>
- <span data-ttu-id="bc12f-115">作为 `type` 中的 `is_expression` ， `catch_clause` 或 `type_pattern`</span><span class="sxs-lookup"><span data-stu-id="bc12f-115">as the `type` in an `is_expression`, a `catch_clause` or a `type_pattern`</span></span>
- <span data-ttu-id="bc12f-116">作为 `interface` 完全限定的接口成员名称中的</span><span class="sxs-lookup"><span data-stu-id="bc12f-116">as the `interface` in a fully qualified interface member name</span></span>

<span data-ttu-id="bc12f-117">在 `nullable_reference_type` 和 `nullable_type_parameter` _disabled \* 可为 null 的注释上下文中提供了警告。</span><span class="sxs-lookup"><span data-stu-id="bc12f-117">A warning is given on a `nullable_reference_type` and `nullable_type_parameter` in a _disabled\* nullable annotation context.</span></span>

### <a name="class-and-class-constraint"></a><span data-ttu-id="bc12f-118">`class` and `class?` 约束</span><span class="sxs-lookup"><span data-stu-id="bc12f-118">`class` and `class?` constraint</span></span>

<span data-ttu-id="bc12f-119">`class`约束具有可为 null 的对应项 `class?` ：</span><span class="sxs-lookup"><span data-stu-id="bc12f-119">The `class` constraint has a nullable counterpart `class?`:</span></span>

```antlr
primary_constraint
    : ...
    | 'class' '?'
    ;
```

<span data-ttu-id="bc12f-120">`class`*已启用* 的批注上下文) 中 (约束的类型参数必须使用不可 null 引用类型进行实例化。</span><span class="sxs-lookup"><span data-stu-id="bc12f-120">A type parameter constrained with `class` (in an *enabled* annotation context) must be instantiated with a nonnullable reference type.</span></span>

<span data-ttu-id="bc12f-121">`class?` (或 `class` *已禁用* 的注释上下文中的类型形参) 可以使用可以为 null 的或不可 null 的引用类型进行实例化。</span><span class="sxs-lookup"><span data-stu-id="bc12f-121">A type parameter constrained with `class?` (or `class` in a *disabled* annotation context) may either be instantiated with a nullable or nonnullable reference type.</span></span>

<span data-ttu-id="bc12f-122">`class?`*已禁用* 批注上下文中的约束提供了警告。</span><span class="sxs-lookup"><span data-stu-id="bc12f-122">A warning is given on a `class?` constraint in a *disabled* annotation context.</span></span>

### <a name="notnull-constraint"></a><span data-ttu-id="bc12f-123">`notnull` constraint</span><span class="sxs-lookup"><span data-stu-id="bc12f-123">`notnull` constraint</span></span>

<span data-ttu-id="bc12f-124">使用约束的类型形参 `notnull` 不能是可以为 null 的类型 (可以为 null 的值类型、可以为 null 的引用类型或可以为 null 的类型参数) </span><span class="sxs-lookup"><span data-stu-id="bc12f-124">A type parameter constrained with `notnull` may not be a nullable type (nullable value type, nullable reference type or nullable type parameter).</span></span>

```antlr
primary_constraint
    : ...
    | 'notnull'
    ;
```

### <a name="default-constraint"></a><span data-ttu-id="bc12f-125">`default` constraint</span><span class="sxs-lookup"><span data-stu-id="bc12f-125">`default` constraint</span></span>

<span data-ttu-id="bc12f-126">`default`约束可用于方法重写或显式实现，以消除 "可以为 null `T?` 的类型参数" 与 "可以为 null 的值类型" (`Nullable<T>`) 。</span><span class="sxs-lookup"><span data-stu-id="bc12f-126">The `default` constraint can be used on a method override or explicit implementation to disambiguate `T?` meaning "nullable type parameter" from "nullable value type" (`Nullable<T>`).</span></span> <span data-ttu-id="bc12f-127">如果缺少 `default` 约束，则 `T?` 重写或显式实现中的语法将被解释为 `Nullable<T>`</span><span class="sxs-lookup"><span data-stu-id="bc12f-127">Lacking the `default` constraint a `T?` syntax in an override or explicit implementation will be interpreted as `Nullable<T>`</span></span>

<span data-ttu-id="bc12f-128">请参见https://github.com/dotnet/csharplang/blob/master/proposals/csharp-9.0/unconstrained-type-parameter-annotations.md#default-constraint</span><span class="sxs-lookup"><span data-stu-id="bc12f-128">See https://github.com/dotnet/csharplang/blob/master/proposals/csharp-9.0/unconstrained-type-parameter-annotations.md#default-constraint</span></span>

### <a name="the-null-forgiving-operator"></a><span data-ttu-id="bc12f-129">包容性运算符</span><span class="sxs-lookup"><span data-stu-id="bc12f-129">The null-forgiving operator</span></span>

<span data-ttu-id="bc12f-130">后修复后的 `!` 运算符称为包容性运算符。</span><span class="sxs-lookup"><span data-stu-id="bc12f-130">The post-fix `!` operator is called the null-forgiving operator.</span></span> <span data-ttu-id="bc12f-131">它可以应用于 *primary_expression* 或 *null_conditional_expression* 内：</span><span class="sxs-lookup"><span data-stu-id="bc12f-131">It can be applied on a *primary_expression* or within a *null_conditional_expression* :</span></span>

```antlr
primary_expression
    : ...
    | null_forgiving_expression
    ;

null_forgiving_expression
    : primary_expression '!'
    ;

null_conditional_expression
    : primary_expression null_conditional_operations_no_suppression suppression?
    ;

null_conditional_operations_no_suppression
    : null_conditional_operations? '?' '.' identifier type_argument_list?
    | null_conditional_operations? '?' '[' argument_list ']'
    | null_conditional_operations '.' identifier type_argument_list?
    | null_conditional_operations '[' argument_list ']'
    | null_conditional_operations '(' argument_list? ')'
    ;

null_conditional_operations
    : null_conditional_operations_no_suppression suppression?
    ;

suppression
    : '!'
    ;
```

<span data-ttu-id="bc12f-132">例如：</span><span class="sxs-lookup"><span data-stu-id="bc12f-132">For example:</span></span>

```csharp
var v = expr!;
expr!.M();
_ = a?.b!.c;
```

<span data-ttu-id="bc12f-133">`primary_expression`和 `null_conditional_operations_no_suppression` 必须是可以为 null 的类型。</span><span class="sxs-lookup"><span data-stu-id="bc12f-133">The `primary_expression` and `null_conditional_operations_no_suppression` must be of a nullable type.</span></span>

<span data-ttu-id="bc12f-134">后缀 `!` 运算符没有运行时效果，它的计算结果为基础表达式的结果。</span><span class="sxs-lookup"><span data-stu-id="bc12f-134">The postfix `!` operator has no runtime effect - it evaluates to the result of the underlying expression.</span></span> <span data-ttu-id="bc12f-135">其唯一的作用是将表达式的 null 状态更改为 "not null"，并限制在使用时给出的警告。</span><span class="sxs-lookup"><span data-stu-id="bc12f-135">Its only role is to change the null state of the expression to "not null", and to limit warnings given on its use.</span></span>

### <a name="nullable-compiler-directives"></a><span data-ttu-id="bc12f-136">可以为 null 的编译器指令</span><span class="sxs-lookup"><span data-stu-id="bc12f-136">Nullable compiler directives</span></span>

<span data-ttu-id="bc12f-137">`#nullable` 指令控制可为 null 的批注和警告上下文。</span><span class="sxs-lookup"><span data-stu-id="bc12f-137">`#nullable` directives control the nullable annotation and warning contexts.</span></span>

```antlr
pp_directive
    : ...
    | pp_nullable
    ;

pp_nullable
    : whitespace? '#' whitespace? 'nullable' whitespace nullable_action (whitespace nullable_target)? pp_new_line
    ;

nullable_action
    : 'disable'
    | 'enable'
    | 'restore'
    ;

nullable_target
    : 'warnings'
    | 'annotations'
    ;
```

<span data-ttu-id="bc12f-138">`#pragma warning` 展开指令以允许更改可为 null 的警告上下文：</span><span class="sxs-lookup"><span data-stu-id="bc12f-138">`#pragma warning` directives are expanded to allow changing the nullable warning context:</span></span>

```antlr
pragma_warning_body
    : ...
    | 'warning' whitespace warning_action whitespace 'nullable'
    ;
```

<span data-ttu-id="bc12f-139">例如：</span><span class="sxs-lookup"><span data-stu-id="bc12f-139">For example:</span></span>

```csharp
#pragma warning disable nullable
```

## <a name="nullable-contexts"></a><span data-ttu-id="bc12f-140">可为空上下文</span><span class="sxs-lookup"><span data-stu-id="bc12f-140">Nullable contexts</span></span>

<span data-ttu-id="bc12f-141">源代码的每个行都有 *可为 null 的注释上下文* 和 *可以为 null 的警告上下文* 。</span><span class="sxs-lookup"><span data-stu-id="bc12f-141">Every line of source code has a *nullable annotation context* and a *nullable warning context*.</span></span> <span data-ttu-id="bc12f-142">这些控件控制是否可为 null 的批注是否有效，以及是否给定了可为空性警告。</span><span class="sxs-lookup"><span data-stu-id="bc12f-142">These control whether nullable annotations have effect, and whether nullability warnings are given.</span></span> <span data-ttu-id="bc12f-143">给定行的批注上下文已 *禁用* 或 *启用* 。</span><span class="sxs-lookup"><span data-stu-id="bc12f-143">The annotation context of a given line is either *disabled* or *enabled*.</span></span> <span data-ttu-id="bc12f-144">给定行的警告上下文已 *禁用* 或 *启用* 。</span><span class="sxs-lookup"><span data-stu-id="bc12f-144">The warning context of a given line is either *disabled* or *enabled*.</span></span>

<span data-ttu-id="bc12f-145">既可在 c # 源代码) 之外的项目级别指定这两个上下文，也可通过预处理器指令在源文件中的任何位置指定 (`#nullable` 。</span><span class="sxs-lookup"><span data-stu-id="bc12f-145">Both contexts can be specified at the project level (outside of C# source code), or anywhere within a source file via `#nullable` pre-processor directives.</span></span> <span data-ttu-id="bc12f-146">如果未提供任何项目级别设置，则默认情况下，这两个上下文都是 *禁用* 的。</span><span class="sxs-lookup"><span data-stu-id="bc12f-146">If no project level settings are provided the default is for both contexts to be *disabled*.</span></span>

<span data-ttu-id="bc12f-147">`#nullable`指令控制源文本中的批注和警告上下文，并优先于项目级设置。</span><span class="sxs-lookup"><span data-stu-id="bc12f-147">The `#nullable` directive controls the annotation and warning contexts within the source text, and take precedence over the project-level settings.</span></span>

<span data-ttu-id="bc12f-148">指令设置上下文 () 它控制后续代码行，直到另一个指令重写它，或直到源文件的结尾。</span><span class="sxs-lookup"><span data-stu-id="bc12f-148">A directive sets the context(s) it controls for subsequent lines of code, until another directive overrides it, or until the end of the source file.</span></span>

<span data-ttu-id="bc12f-149">指令的效果如下所示：</span><span class="sxs-lookup"><span data-stu-id="bc12f-149">The effect of the directives is as follows:</span></span>

- <span data-ttu-id="bc12f-150">`#nullable disable`：将可为 null 的批注和警告上下文设置为 *已禁用*</span><span class="sxs-lookup"><span data-stu-id="bc12f-150">`#nullable disable`: Sets the nullable annotation and warning contexts to *disabled*</span></span>
- <span data-ttu-id="bc12f-151">`#nullable enable`：将可为 null 的批注和警告上下文设置为 *enabled*</span><span class="sxs-lookup"><span data-stu-id="bc12f-151">`#nullable enable`: Sets the nullable annotation and warning contexts to *enabled*</span></span>
- <span data-ttu-id="bc12f-152">`#nullable restore`：将可为 null 的批注和警告上下文还原到项目设置</span><span class="sxs-lookup"><span data-stu-id="bc12f-152">`#nullable restore`: Restores the nullable annotation and warning contexts to project settings</span></span>
- <span data-ttu-id="bc12f-153">`#nullable disable annotations`：将可为 null 的注释上下文设置为 *已禁用*</span><span class="sxs-lookup"><span data-stu-id="bc12f-153">`#nullable disable annotations`: Sets the nullable annotation context to *disabled*</span></span>
- <span data-ttu-id="bc12f-154">`#nullable enable annotations`：将可为 null 的注释上下文设置为 *enabled*</span><span class="sxs-lookup"><span data-stu-id="bc12f-154">`#nullable enable annotations`: Sets the nullable annotation context to *enabled*</span></span>
- <span data-ttu-id="bc12f-155">`#nullable restore annotations`：将可为 null 的注释上下文还原到项目设置</span><span class="sxs-lookup"><span data-stu-id="bc12f-155">`#nullable restore annotations`: Restores the nullable annotation context to project settings</span></span>
- <span data-ttu-id="bc12f-156">`#nullable disable warnings`：将可为 null 的警告上下文设置为 *已禁用*</span><span class="sxs-lookup"><span data-stu-id="bc12f-156">`#nullable disable warnings`: Sets the nullable warning context to *disabled*</span></span>
- <span data-ttu-id="bc12f-157">`#nullable enable warnings`：将可为 null 的警告上下文设置为 *已启用*</span><span class="sxs-lookup"><span data-stu-id="bc12f-157">`#nullable enable warnings`: Sets the nullable warning context to *enabled*</span></span>
- <span data-ttu-id="bc12f-158">`#nullable restore warnings`：将可为 null 的警告上下文还原到项目设置</span><span class="sxs-lookup"><span data-stu-id="bc12f-158">`#nullable restore warnings`: Restores the nullable warning context to project settings</span></span>

## <a name="nullability-of-types"></a><span data-ttu-id="bc12f-159">类型为 Null 性</span><span class="sxs-lookup"><span data-stu-id="bc12f-159">Nullability of types</span></span>

<span data-ttu-id="bc12f-160">给定的类型可以具有以下三个 nullabilities 之一： *在意* 、 *不可 null* 和 *可以为 null* 。</span><span class="sxs-lookup"><span data-stu-id="bc12f-160">A given type can have one of three nullabilities: *oblivious* , *nonnullable* , and *nullable*.</span></span>

<span data-ttu-id="bc12f-161">如果将可能的值分配给 *不可 null* 类型，则可能会引发警告 `null` 。</span><span class="sxs-lookup"><span data-stu-id="bc12f-161">*Nonnullable* types may cause warnings if a potential `null` value is assigned to them.</span></span> <span data-ttu-id="bc12f-162">但是， *在意* 和可以 *为* null 的类型为 "可 *赋值* "，可以 `null` 为其分配值而不会出现警告。</span><span class="sxs-lookup"><span data-stu-id="bc12f-162">*Oblivious* and *nullable* types, however, are " *null-assignable* " and can have `null` values assigned to them without warnings.</span></span>

<span data-ttu-id="bc12f-163">可以取消引用或分配 *在意* 和 *不可 null* 类型的值，而不会出现警告。</span><span class="sxs-lookup"><span data-stu-id="bc12f-163">Values of *oblivious* and *nonnullable* types can be dereferenced or assigned without warnings.</span></span> <span data-ttu-id="bc12f-164">但是， *可以为* null 的类型的值为 " *空* 值"，并可能在取消引用或赋值时导致警告，而不进行正确的 null 检查。</span><span class="sxs-lookup"><span data-stu-id="bc12f-164">Values of *nullable* types, however, are " *null-yielding* " and may cause warnings when dereferenced or assigned without proper null checking.</span></span>

<span data-ttu-id="bc12f-165">Null 产生类型的 *默认 null 状态* 是 "可能是 null" 或 "可能是默认值"。</span><span class="sxs-lookup"><span data-stu-id="bc12f-165">The *default null state* of a null-yielding type is "maybe null" or "maybe default".</span></span> <span data-ttu-id="bc12f-166">非 null 生成类型的默认 null 状态为 "not null"。</span><span class="sxs-lookup"><span data-stu-id="bc12f-166">The default null state of a non-null-yielding type is "not null".</span></span>

<span data-ttu-id="bc12f-167">类型的类型和在确定其为 null 性时所发生的可为 null 的注释上下文：</span><span class="sxs-lookup"><span data-stu-id="bc12f-167">The kind of type and the nullable annotation context it occurs in determine its nullability:</span></span>

- <span data-ttu-id="bc12f-168">不可 null 值类型 `S` 始终为 *不可 null*</span><span class="sxs-lookup"><span data-stu-id="bc12f-168">A nonnullable value type `S` is always *nonnullable*</span></span>
- <span data-ttu-id="bc12f-169">可以为 null 的值类型 `S?` 始终 *可以为 null*</span><span class="sxs-lookup"><span data-stu-id="bc12f-169">A nullable value type `S?` is always *nullable*</span></span>
- <span data-ttu-id="bc12f-170">`C`*禁用* 的注释上下文中的批注引用类型为 *在意*</span><span class="sxs-lookup"><span data-stu-id="bc12f-170">An unannotated reference type `C` in a *disabled* annotation context is *oblivious*</span></span>
- <span data-ttu-id="bc12f-171">`C`*启用* 的批注上下文中的批注引用类型为 *不可 null*</span><span class="sxs-lookup"><span data-stu-id="bc12f-171">An unannotated reference type `C` in an *enabled* annotation context is *nonnullable*</span></span>
- <span data-ttu-id="bc12f-172">可以为 null 的引用类型可以 `C?` *为 null* (但在 *禁用* 的批注上下文中可能生成警告) </span><span class="sxs-lookup"><span data-stu-id="bc12f-172">A nullable reference type `C?` is *nullable* (but a warning may be yielded in a *disabled* annotation context)</span></span>

<span data-ttu-id="bc12f-173">类型参数还会考虑它们的约束：</span><span class="sxs-lookup"><span data-stu-id="bc12f-173">Type parameters additionally take their constraints into account:</span></span>

- <span data-ttu-id="bc12f-174">`T`如果任何) 为可以为 null 的类型或 `class?` 约束 *可以为 null* ，则为所有约束都 (的类型参数</span><span class="sxs-lookup"><span data-stu-id="bc12f-174">A type parameter `T` where all constraints (if any) are either nullable types or the `class?` constraint is *nullable*</span></span>
- <span data-ttu-id="bc12f-175">一个类型参数 `T` ，其中至少有一个约束为 *在意* 或 *不可 null* ，或者其中一个 `struct` 或 `class` 或 `notnull` 约束为</span><span class="sxs-lookup"><span data-stu-id="bc12f-175">A type parameter `T` where at least one constraint is either *oblivious* or *nonnullable* or one of the `struct` or `class` or `notnull` constraints is</span></span>
    - <span data-ttu-id="bc12f-176">*禁用* 的注释上下文中的 *在意*</span><span class="sxs-lookup"><span data-stu-id="bc12f-176">*oblivious* in a *disabled* annotation context</span></span>
    - <span data-ttu-id="bc12f-177">*已启用* 的批注上下文中的 *不可 null*</span><span class="sxs-lookup"><span data-stu-id="bc12f-177">*nonnullable* in an *enabled* annotation context</span></span>
- <span data-ttu-id="bc12f-178">可以为 null 的类型参数 `T?` *可以为 null* ，但如果不是值类型，则 *已禁用* 的批注上下文中会生成一个警告。 `T`</span><span class="sxs-lookup"><span data-stu-id="bc12f-178">A nullable type parameter `T?` is *nullable* , but a warning is yielded in a *disabled* annotation context if `T` isn't a value type</span></span>

### <a name="oblivious-vs-nonnullable"></a><span data-ttu-id="bc12f-179">在意 vs 不可 null</span><span class="sxs-lookup"><span data-stu-id="bc12f-179">Oblivious vs nonnullable</span></span>

<span data-ttu-id="bc12f-180">`type`当类型的最后一个标记在该上下文中时，将被视为在给定的批注上下文中发生。</span><span class="sxs-lookup"><span data-stu-id="bc12f-180">A `type` is deemed to occur in a given annotation context when the last token of the type is within that context.</span></span>

<span data-ttu-id="bc12f-181">源代码中的给定引用类型 `C` 是解释为在意还是不可 null 依赖于源代码的注释上下文。</span><span class="sxs-lookup"><span data-stu-id="bc12f-181">Whether a given reference type `C` in source code is interpreted as oblivious or nonnullable depends on the annotation context of that source code.</span></span> <span data-ttu-id="bc12f-182">但一旦建立，就会将其视为该类型的一部分，并在替换泛型类型参数的过程中将其视为 "随之一起移动"。</span><span class="sxs-lookup"><span data-stu-id="bc12f-182">But once established, it is considered part of that type, and "travels with it" e.g. during substitution of generic type arguments.</span></span> <span data-ttu-id="bc12f-183">这就像在类型上有一个批注 `?` ，但不可见。</span><span class="sxs-lookup"><span data-stu-id="bc12f-183">It is as if there is an annotation like `?` on the type, but invisible.</span></span>

## <a name="constraints"></a><span data-ttu-id="bc12f-184">约束</span><span class="sxs-lookup"><span data-stu-id="bc12f-184">Constraints</span></span>

<span data-ttu-id="bc12f-185">可以为 null 的引用类型可用作泛型约束。</span><span class="sxs-lookup"><span data-stu-id="bc12f-185">Nullable reference types can be used as generic constraints.</span></span>

<span data-ttu-id="bc12f-186">`class?` 表示 "可能为 null 的引用类型" 的新约束，而 `class` *启用* 的批注上下文中表示 "不可 null 引用类型"。</span><span class="sxs-lookup"><span data-stu-id="bc12f-186">`class?` is a new constraint denoting "possibly nullable reference type", whereas `class` in an *enabled* annotation context denotes "nonnullable reference type".</span></span>

<span data-ttu-id="bc12f-187">`default` 一个新约束，用于表示未知类型参数或值类型。</span><span class="sxs-lookup"><span data-stu-id="bc12f-187">`default` is a new constraint denoting a type parameter that isn't known to be a reference or value type.</span></span> <span data-ttu-id="bc12f-188">它只能用于重写和显式实现的方法。</span><span class="sxs-lookup"><span data-stu-id="bc12f-188">It can only be used on overridden and explicitly implemented methods.</span></span> <span data-ttu-id="bc12f-189">对于此约束， `T?` 表示可以为 null 的类型参数，而不是的简写形式 `Nullable<T>` 。</span><span class="sxs-lookup"><span data-stu-id="bc12f-189">With this constraint, `T?` means a nullable type parameter, as opposed to being a shorthand for `Nullable<T>`.</span></span>

<span data-ttu-id="bc12f-190">`notnull` 表示不可 null 类型参数的新约束。</span><span class="sxs-lookup"><span data-stu-id="bc12f-190">`notnull` is a new constraint denoting a type parameter that is nonnullable.</span></span>

<span data-ttu-id="bc12f-191">类型参数或约束的为空性并不会影响类型是否满足约束，但目前已 (可以为 null 的值类型不满足约束) 的情况除外 `struct` 。</span><span class="sxs-lookup"><span data-stu-id="bc12f-191">The nullability of a type argument or of a constraint does not impact whether the type satisfies the constraint, except where that is already the case today (nullable value types do not satisfy the `struct` constraint).</span></span> <span data-ttu-id="bc12f-192">但是，如果类型参数不满足约束的为 null 性要求，则可以提供警告。</span><span class="sxs-lookup"><span data-stu-id="bc12f-192">However, if the type argument does not satisfy the nullability requirements of the constraint, a warning may be given.</span></span>

## <a name="null-state-and-null-tracking"></a><span data-ttu-id="bc12f-193">Null 状态和 null 跟踪</span><span class="sxs-lookup"><span data-stu-id="bc12f-193">Null state and null tracking</span></span>

<span data-ttu-id="bc12f-194">给定源位置中的每个表达式都具有 *null 状态* ，指示其是否被认为可能计算为 null。</span><span class="sxs-lookup"><span data-stu-id="bc12f-194">Every expression in a given source location has a *null state* , which indicated whether it is believed to potentially evaluate to null.</span></span> <span data-ttu-id="bc12f-195">Null 状态为 "not null"、"可能为 null" 或 "可能为默认值"。</span><span class="sxs-lookup"><span data-stu-id="bc12f-195">The null state is either "not null", "maybe null", or "maybe default".</span></span> <span data-ttu-id="bc12f-196">Null 状态用于确定是否应为不安全的转换和取消引用提供警告。</span><span class="sxs-lookup"><span data-stu-id="bc12f-196">The null state is used to determine whether a warning should be given about null-unsafe conversions and dereferences.</span></span>

### <a name="null-tracking-for-variables"></a><span data-ttu-id="bc12f-197">变量的 Null 跟踪</span><span class="sxs-lookup"><span data-stu-id="bc12f-197">Null tracking for variables</span></span>

<span data-ttu-id="bc12f-198">对于指定变量或属性的某些表达式，将根据对它们的赋值、对它们执行的测试以及它们之间的控制流，在发生之间跟踪 null 状态。</span><span class="sxs-lookup"><span data-stu-id="bc12f-198">For certain expressions denoting variables or properties, the null state is tracked between occurrences, based on assignments to them, tests performed on them and the control flow between them.</span></span> <span data-ttu-id="bc12f-199">这类似于为变量跟踪明确赋值的方式。</span><span class="sxs-lookup"><span data-stu-id="bc12f-199">This is similar to how definite assignment is tracked for variables.</span></span> <span data-ttu-id="bc12f-200">跟踪的表达式如下所示：</span><span class="sxs-lookup"><span data-stu-id="bc12f-200">The tracked expressions are the ones of the following form:</span></span>

```antlr
tracked_expression
    : simple_name
    | this
    | base
    | tracked_expression '.' identifier
    ;
```

<span data-ttu-id="bc12f-201">其中，标识符表示字段或属性。</span><span class="sxs-lookup"><span data-stu-id="bc12f-201">Where the identifiers denote fields or properties.</span></span>

<span data-ttu-id="bc12f-202">被跟踪变量的 null 状态在无法访问的代码中为 "not null"。</span><span class="sxs-lookup"><span data-stu-id="bc12f-202">The null state for tracked variables is "not null" in unreachable code.</span></span> <span data-ttu-id="bc12f-203">这遵循了有关无法访问的代码的其他决策，如考虑将所有局部变量明确赋值。</span><span class="sxs-lookup"><span data-stu-id="bc12f-203">This follows other decisions around unreachable code like considering all locals to be definitely assigned.</span></span>

<span data-ttu-id="bc12f-204">\***描述类似于明确赋值的空状态转换** _</span><span class="sxs-lookup"><span data-stu-id="bc12f-204">\***Describe null state transitions similar to definite assignment** _</span></span>

### <a name="null-state-for-expressions"></a><span data-ttu-id="bc12f-205">表达式为 Null</span><span class="sxs-lookup"><span data-stu-id="bc12f-205">Null state for expressions</span></span>

<span data-ttu-id="bc12f-206">表达式的 null 状态派生自其形式和类型以及它所涉及的变量的 null 状态。</span><span class="sxs-lookup"><span data-stu-id="bc12f-206">The null state of an expression is derived from its form and type, and from the null state of variables involved in it.</span></span>

### <a name="literals"></a><span data-ttu-id="bc12f-207">文本</span><span class="sxs-lookup"><span data-stu-id="bc12f-207">Literals</span></span>

<span data-ttu-id="bc12f-208">和文本的 null 状态 `null` `default` 为 "可能是默认值"。</span><span class="sxs-lookup"><span data-stu-id="bc12f-208">The null state of a `null` and `default` literals is "maybe default".</span></span> <span data-ttu-id="bc12f-209">任何其他文本的 null 状态为 "not null"。</span><span class="sxs-lookup"><span data-stu-id="bc12f-209">The null state of any other literal is "not null".</span></span>

### <a name="simple-names"></a><span data-ttu-id="bc12f-210">简单名称</span><span class="sxs-lookup"><span data-stu-id="bc12f-210">Simple names</span></span>

<span data-ttu-id="bc12f-211">如果 `simple_name` 未归类为值，则其 null 状态为 "not null"。</span><span class="sxs-lookup"><span data-stu-id="bc12f-211">If a `simple_name` is not classified as a value, its null state is "not null".</span></span> <span data-ttu-id="bc12f-212">否则为跟踪的表达式，其 null 状态将为其在此源位置跟踪的 null 状态。</span><span class="sxs-lookup"><span data-stu-id="bc12f-212">Otherwise it is a tracked expression, and its null state is its tracked null state at this source location.</span></span>

### <a name="member-access"></a><span data-ttu-id="bc12f-213">成员访问</span><span class="sxs-lookup"><span data-stu-id="bc12f-213">Member access</span></span>

<span data-ttu-id="bc12f-214">如果 `member_access` 未归类为值，则其 null 状态为 "not null"。</span><span class="sxs-lookup"><span data-stu-id="bc12f-214">If a `member_access` is not classified as a value, its null state is "not null".</span></span> <span data-ttu-id="bc12f-215">否则，如果是跟踪的表达式，则其 null 状态将为其在此源位置跟踪的 null 状态。</span><span class="sxs-lookup"><span data-stu-id="bc12f-215">Otherwise, if it is a tracked expression, its null state is its tracked null state at this source location.</span></span> <span data-ttu-id="bc12f-216">否则，其 null 状态为其类型的默认 null 状态。</span><span class="sxs-lookup"><span data-stu-id="bc12f-216">Otherwise its null state is the default null state of its type.</span></span>

### <a name="invocation-expressions"></a><span data-ttu-id="bc12f-217">调用表达式</span><span class="sxs-lookup"><span data-stu-id="bc12f-217">Invocation expressions</span></span>

<span data-ttu-id="bc12f-218">如果 `invocation_expression` 调用使用一个或多个特性声明的成员以实现特殊的 null 行为，则 null 状态由这些特性决定。</span><span class="sxs-lookup"><span data-stu-id="bc12f-218">If an `invocation_expression` invokes a member that is declared with one or more attributes for special null behavior, the null state is determined by those attributes.</span></span> <span data-ttu-id="bc12f-219">否则，表达式的 null 状态为其类型的默认 null 状态。</span><span class="sxs-lookup"><span data-stu-id="bc12f-219">Otherwise the null state of the expression is the default null state of its type.</span></span>

### <a name="element-access"></a><span data-ttu-id="bc12f-220">元素访问</span><span class="sxs-lookup"><span data-stu-id="bc12f-220">Element access</span></span>

<span data-ttu-id="bc12f-221">如果 `element_access` 调用使用一个或多个特性声明的索引器来实现特殊的 null 行为，则 null 状态由这些特性决定。</span><span class="sxs-lookup"><span data-stu-id="bc12f-221">If an `element_access` invokes an indexer that is declared with one or more attributes for special null behavior, the null state is determined by those attributes.</span></span> <span data-ttu-id="bc12f-222">否则，表达式的 null 状态为其类型的默认 null 状态。</span><span class="sxs-lookup"><span data-stu-id="bc12f-222">Otherwise the null state of the expression is the default null state of its type.</span></span>

### <a name="base-access"></a><span data-ttu-id="bc12f-223">基本访问权限</span><span class="sxs-lookup"><span data-stu-id="bc12f-223">Base access</span></span>

<span data-ttu-id="bc12f-224">如果 `B` 表示封闭类型的基类型， `base.I` 则具有与相同的 null 状态， `((B)this).I` 并且与 `base[E]` 具有相同的 null 状态 `((B)this)[E]` 。</span><span class="sxs-lookup"><span data-stu-id="bc12f-224">If `B` denotes the base type of the enclosing type, `base.I` has the same null state as `((B)this).I` and `base[E]` has the same null state as `((B)this)[E]`.</span></span>

### <a name="default-expressions"></a><span data-ttu-id="bc12f-225">默认表达式</span><span class="sxs-lookup"><span data-stu-id="bc12f-225">Default expressions</span></span>

<span data-ttu-id="bc12f-226">`default(T)` 如果 `T` 已知为不可 null 值类型，则具有 null 状态 "not null"。</span><span class="sxs-lookup"><span data-stu-id="bc12f-226">`default(T)` has the null state "not null" if `T` is known to be a nonnullable value type.</span></span> <span data-ttu-id="bc12f-227">否则，它的状态为 "可能是默认值"。</span><span class="sxs-lookup"><span data-stu-id="bc12f-227">Otherwise it has the null state "maybe default".</span></span>

### <a name="null-conditional-expressions"></a><span data-ttu-id="bc12f-228">Null 条件表达式</span><span class="sxs-lookup"><span data-stu-id="bc12f-228">Null-conditional expressions</span></span>

<span data-ttu-id="bc12f-229">`null_conditional_expression`具有 null 状态 "可能为 null"。</span><span class="sxs-lookup"><span data-stu-id="bc12f-229">A `null_conditional_expression` has the null state "maybe null".</span></span>

### <a name="cast-expressions"></a><span data-ttu-id="bc12f-230">强制转换表达式</span><span class="sxs-lookup"><span data-stu-id="bc12f-230">Cast expressions</span></span>

<span data-ttu-id="bc12f-231">如果强制转换表达式 `(T)E` 调用用户定义的转换，则表达式的 null 状态为其类型的默认 null 状态。</span><span class="sxs-lookup"><span data-stu-id="bc12f-231">If a cast expression `(T)E` invokes a user-defined conversion, then the null state of the expression is the default null state for its type.</span></span> <span data-ttu-id="bc12f-232">否则，如果 `T` 为 _nullable \*，则 null 状态为 "可能为 null"。</span><span class="sxs-lookup"><span data-stu-id="bc12f-232">Otherwise, if `T` is _nullable\* then the null state is "maybe null".</span></span> <span data-ttu-id="bc12f-233">否则，null 状态与的 null 状态相同 `E` 。</span><span class="sxs-lookup"><span data-stu-id="bc12f-233">Otherwise the null state is the same as the null state of `E`.</span></span>

<span data-ttu-id="bc12f-234">\***这需要 upddating** _</span><span class="sxs-lookup"><span data-stu-id="bc12f-234">\***This needs upddating** _</span></span>

### <a name="await-expressions"></a><span data-ttu-id="bc12f-235">Await 表达式</span><span class="sxs-lookup"><span data-stu-id="bc12f-235">Await expressions</span></span>

<span data-ttu-id="bc12f-236">的 null 状态 `await E` 为其类型的默认 null 状态。</span><span class="sxs-lookup"><span data-stu-id="bc12f-236">The null state of `await E` is the default null state of its type.</span></span>

### <a name="the-as-operator"></a><span data-ttu-id="bc12f-237">`as` 运算符</span><span class="sxs-lookup"><span data-stu-id="bc12f-237">The `as` operator</span></span>

<span data-ttu-id="bc12f-238">`as`表达式的状态为 "可能为 null"。</span><span class="sxs-lookup"><span data-stu-id="bc12f-238">An `as` expression has the null state "maybe null".</span></span>

### <a name="the-null-coalescing-operator"></a><span data-ttu-id="bc12f-239">Null 合并运算符</span><span class="sxs-lookup"><span data-stu-id="bc12f-239">The null-coalescing operator</span></span>

<span data-ttu-id="bc12f-240">`E1 ?? E2` 具有与相同的空状态 `E2`</span><span class="sxs-lookup"><span data-stu-id="bc12f-240">`E1 ?? E2` has the same null state as `E2`</span></span>

### <a name="the-conditional-operator"></a><span data-ttu-id="bc12f-241">条件运算符</span><span class="sxs-lookup"><span data-stu-id="bc12f-241">The conditional operator</span></span>

<span data-ttu-id="bc12f-242">`E1 ? E2 : E3`如果和的 null 状态为 `E2` `E3` "not null"，则的 null 状态为 "not null"。</span><span class="sxs-lookup"><span data-stu-id="bc12f-242">The null state of `E1 ? E2 : E3` is "not null" if the null state of both `E2` and `E3` are "not null".</span></span> <span data-ttu-id="bc12f-243">否则为 "可能为 null"。</span><span class="sxs-lookup"><span data-stu-id="bc12f-243">Otherwise it is "maybe null".</span></span>

### <a name="query-expressions"></a><span data-ttu-id="bc12f-244">查询表达式</span><span class="sxs-lookup"><span data-stu-id="bc12f-244">Query expressions</span></span>

<span data-ttu-id="bc12f-245">查询表达式的 null 状态为其类型的默认 null 状态。</span><span class="sxs-lookup"><span data-stu-id="bc12f-245">The null state of a query expression is the default null state of its type.</span></span>

### <a name="assignment-operators"></a><span data-ttu-id="bc12f-246">赋值运算符</span><span class="sxs-lookup"><span data-stu-id="bc12f-246">Assignment operators</span></span>

<span data-ttu-id="bc12f-247">`E1 = E2` 和 `E1 op= E2` 都具有与 `E2` 应用任何隐式转换相同的空状态。</span><span class="sxs-lookup"><span data-stu-id="bc12f-247">`E1 = E2` and `E1 op= E2` have the same null state as `E2` after any implicit conversions have been applied.</span></span>

### <a name="unary-and-binary-operators"></a><span data-ttu-id="bc12f-248">一元运算符和二元运算符</span><span class="sxs-lookup"><span data-stu-id="bc12f-248">Unary and binary operators</span></span>

<span data-ttu-id="bc12f-249">如果一元或二元运算符调用了用一个或多个特性声明的用户定义的运算符来实现特殊的 null 行为，则 null 状态由这些特性决定。</span><span class="sxs-lookup"><span data-stu-id="bc12f-249">If a unary or binary operator invokes an user-defined operator that is declared with one or more attributes for special null behavior, the null state is determined by those attributes.</span></span> <span data-ttu-id="bc12f-250">否则，表达式的 null 状态为其类型的默认 null 状态。</span><span class="sxs-lookup"><span data-stu-id="bc12f-250">Otherwise the null state of the expression is the default null state of its type.</span></span>

<span data-ttu-id="bc12f-251">_\*_对于字符串和委托，二进制文件的特殊操作是什么 `+` ？_\*_</span><span class="sxs-lookup"><span data-stu-id="bc12f-251">_*_Something special to do for binary `+` over strings and delegates?_*_</span></span>

### <a name="expressions-that-propagate-null-state"></a><span data-ttu-id="bc12f-252">传播 null 状态的表达式</span><span class="sxs-lookup"><span data-stu-id="bc12f-252">Expressions that propagate null state</span></span>

<span data-ttu-id="bc12f-253">`(E)``checked(E)`和 `unchecked(E)` 都具有与相同的 null 状态 `E` 。</span><span class="sxs-lookup"><span data-stu-id="bc12f-253">`(E)`, `checked(E)` and `unchecked(E)` all have the same null state as `E`.</span></span>

### <a name="expressions-that-are-never-null"></a><span data-ttu-id="bc12f-254">从不为 null 的表达式</span><span class="sxs-lookup"><span data-stu-id="bc12f-254">Expressions that are never null</span></span>

<span data-ttu-id="bc12f-255">以下表达式格式的 null 状态始终为 "not null"：</span><span class="sxs-lookup"><span data-stu-id="bc12f-255">The null state of the following expression forms is always "not null":</span></span>

- <span data-ttu-id="bc12f-256">`this` access</span><span class="sxs-lookup"><span data-stu-id="bc12f-256">`this` access</span></span>
- <span data-ttu-id="bc12f-257">内插字符串</span><span class="sxs-lookup"><span data-stu-id="bc12f-257">interpolated strings</span></span>
- <span data-ttu-id="bc12f-258">`new` 表达式 (对象、委托、匿名对象和数组创建表达式) </span><span class="sxs-lookup"><span data-stu-id="bc12f-258">`new` expressions (object, delegate, anonymous object and array creation expressions)</span></span>
- <span data-ttu-id="bc12f-259">`typeof` 表达式</span><span class="sxs-lookup"><span data-stu-id="bc12f-259">`typeof` expressions</span></span>
- <span data-ttu-id="bc12f-260">`nameof` 表达式</span><span class="sxs-lookup"><span data-stu-id="bc12f-260">`nameof` expressions</span></span>
- <span data-ttu-id="bc12f-261">匿名函数 (匿名方法和 lambda 表达式) </span><span class="sxs-lookup"><span data-stu-id="bc12f-261">anonymous functions (anonymous methods and lambda expressions)</span></span>
- <span data-ttu-id="bc12f-262">包容性表达式</span><span class="sxs-lookup"><span data-stu-id="bc12f-262">null-forgiving expressions</span></span>
- <span data-ttu-id="bc12f-263">`is` 表达式</span><span class="sxs-lookup"><span data-stu-id="bc12f-263">`is` expressions</span></span>

### <a name="nested-functions"></a><span data-ttu-id="bc12f-264">嵌套函数</span><span class="sxs-lookup"><span data-stu-id="bc12f-264">Nested functions</span></span>

<span data-ttu-id="bc12f-265">嵌套函数 (lambda 和局部函数) 被视为方法，但其捕获变量除外。</span><span class="sxs-lookup"><span data-stu-id="bc12f-265">Nested functions (lambdas and local functions) are treated like methods, except in regards to their captured variables.</span></span>
<span data-ttu-id="bc12f-266">Lambda 或局部函数内捕获的变量的初始状态是该嵌套函数或 lambda 的所有 "使用" 中的变量的可以为 null 的状态的交集。</span><span class="sxs-lookup"><span data-stu-id="bc12f-266">The initial state of a captured variable inside a lambda or local function is the intersection of the nullable state of the variable at all the "uses" of that nested function or lambda.</span></span> <span data-ttu-id="bc12f-267">本地函数的使用是对该函数的调用，或将其转换为委托的位置。</span><span class="sxs-lookup"><span data-stu-id="bc12f-267">A use of a local function is either a call to that function, or where it is converted to a delegate.</span></span> <span data-ttu-id="bc12f-268">Lambda 的使用是在源中定义它的点。</span><span class="sxs-lookup"><span data-stu-id="bc12f-268">A use of a lambda is the point at which it is defined in source.</span></span>

## <a name="type-inference"></a><span data-ttu-id="bc12f-269">类型推理</span><span class="sxs-lookup"><span data-stu-id="bc12f-269">Type inference</span></span>

### <a name="nullable-implicitly-typed-local-variables"></a><span data-ttu-id="bc12f-270">可以为 null 的隐式类型的局部变量</span><span class="sxs-lookup"><span data-stu-id="bc12f-270">nullable implicitly typed local variables</span></span>

<span data-ttu-id="bc12f-271">`var` 推理引用类型的批注类型和不被约束为值类型的类型参数。</span><span class="sxs-lookup"><span data-stu-id="bc12f-271">`var` infers an annotated type for reference types, and type parameters that aren't constrained to be a value type.</span></span>
<span data-ttu-id="bc12f-272">例如：</span><span class="sxs-lookup"><span data-stu-id="bc12f-272">For instance:</span></span>
- <span data-ttu-id="bc12f-273">在中， `var s = "";` `var` 将推断为 `string?` 。</span><span class="sxs-lookup"><span data-stu-id="bc12f-273">in `var s = "";` the `var` is inferred as `string?`.</span></span>
- <span data-ttu-id="bc12f-274">在中， `var t = new T();` `T` 不受约束的将 `var` 推断为 `T?` 。</span><span class="sxs-lookup"><span data-stu-id="bc12f-274">in `var t = new T();` with an unconstrained `T` the `var` is inferred as `T?`.</span></span>

### <a name="generic-type-inference"></a><span data-ttu-id="bc12f-275">泛型类型推理</span><span class="sxs-lookup"><span data-stu-id="bc12f-275">Generic type inference</span></span>

<span data-ttu-id="bc12f-276">泛型类型推理经过了增强，可帮助确定推断的引用类型是否可以为 null。</span><span class="sxs-lookup"><span data-stu-id="bc12f-276">Generic type inference is enhanced to help decide whether inferred reference types should be nullable or not.</span></span> <span data-ttu-id="bc12f-277">这是一个最大努力。</span><span class="sxs-lookup"><span data-stu-id="bc12f-277">This is a best effort.</span></span> <span data-ttu-id="bc12f-278">它可能会生成有关空性约束的警告，并且在将所选重载的推断类型应用于参数时，可能会导致可以为 null 的警告。</span><span class="sxs-lookup"><span data-stu-id="bc12f-278">It may yield warnings regarding nullability constraints, and may lead to nullable warnings when the inferred types of the selected overload are applied to the arguments.</span></span>

### <a name="the-first-phase"></a><span data-ttu-id="bc12f-279">第一阶段</span><span class="sxs-lookup"><span data-stu-id="bc12f-279">The first phase</span></span>

<span data-ttu-id="bc12f-280">可以为 null 的引用类型从初始表达式流入边界，如下所述。</span><span class="sxs-lookup"><span data-stu-id="bc12f-280">Nullable reference types flow into the bounds from the initial expressions, as described below.</span></span> <span data-ttu-id="bc12f-281">此外，引入了两种新的界限，即 `null` 和 `default` 。</span><span class="sxs-lookup"><span data-stu-id="bc12f-281">In addition, two new kinds of bounds, namely `null` and `default` are introduced.</span></span> <span data-ttu-id="bc12f-282">其目的是在输入表达式中执行 `null` 或 `default` ，这可能会导致推断出的类型可以为 null，即使在其他情况下也是如此。</span><span class="sxs-lookup"><span data-stu-id="bc12f-282">Their purpose is to carry through occurrences of `null` or `default` in the input expressions, which may cause an inferred type to be nullable, even when it otherwise wouldn't.</span></span> <span data-ttu-id="bc12f-283">这也适用于可为 null 的 _value \* 类型，这些类型经过增强，可在推断过程中选取 "非 null"。</span><span class="sxs-lookup"><span data-stu-id="bc12f-283">This works even for nullable _value\* types, which are enhanced to pick up "nullness" in the inference process.</span></span>

<span data-ttu-id="bc12f-284">在第一阶段中，确定要添加的界限如下：</span><span class="sxs-lookup"><span data-stu-id="bc12f-284">The determination of what bounds to add in the first phase are enhanced as follows:</span></span>

<span data-ttu-id="bc12f-285">如果参数 `Ei` 具有引用类型，则 `U` 用于推理的类型取决于的 null 状态及其 `Ei` 声明的类型：</span><span class="sxs-lookup"><span data-stu-id="bc12f-285">If an argument `Ei` has a reference type, the type `U` used for inference depends on the null state of `Ei` as well as its declared type:</span></span>
- <span data-ttu-id="bc12f-286">如果声明的类型是不可 null 引用类型 `U0` 或可以为 null 的引用类型， `U0?` 则</span><span class="sxs-lookup"><span data-stu-id="bc12f-286">If the declared type is a nonnullable reference type `U0` or a nullable reference type `U0?` then</span></span>
    - <span data-ttu-id="bc12f-287">如果的 null 状态 `Ei` 为 "not null"，则 `U` 为 `U0`</span><span class="sxs-lookup"><span data-stu-id="bc12f-287">if the null state of `Ei` is "not null" then `U` is `U0`</span></span>
    - <span data-ttu-id="bc12f-288">如果的 null 状态 `Ei` 为 "可能为 null"，则 `U` 为 `U0?`</span><span class="sxs-lookup"><span data-stu-id="bc12f-288">if the null state of `Ei` is "maybe null" then `U` is `U0?`</span></span>
- <span data-ttu-id="bc12f-289">否则 `Ei` ，如果具有已声明的类型， `U` 则为该类型</span><span class="sxs-lookup"><span data-stu-id="bc12f-289">Otherwise if `Ei` has a declared type, `U` is that type</span></span>
- <span data-ttu-id="bc12f-290">否则，如果为，则 `Ei` `null` `U` 为特殊界限 `null`</span><span class="sxs-lookup"><span data-stu-id="bc12f-290">Otherwise if `Ei` is `null` then `U` is the special bound `null`</span></span>
- <span data-ttu-id="bc12f-291">否则，如果为，则 `Ei` `default` `U` 为特殊界限 `default`</span><span class="sxs-lookup"><span data-stu-id="bc12f-291">Otherwise if `Ei` is `default` then `U` is the special bound `default`</span></span>
- <span data-ttu-id="bc12f-292">否则，不进行推理。</span><span class="sxs-lookup"><span data-stu-id="bc12f-292">Otherwise no inference is made.</span></span>

### <a name="exact-upper-bound-and-lower-bound-inferences"></a><span data-ttu-id="bc12f-293">精确、上限和下限推理</span><span class="sxs-lookup"><span data-stu-id="bc12f-293">Exact, upper-bound and lower-bound inferences</span></span>

<span data-ttu-id="bc12f-294">在 *从* 类型推断 `U` *为* 类型时 `V` ，如果 `V` 是可以为 null 的引用类型，则将在 `V0?` `V0` `V` 以下子句中使用而不是。</span><span class="sxs-lookup"><span data-stu-id="bc12f-294">In inferences *from* the type `U` *to* the type `V`, if `V` is a nullable reference type `V0?`, then `V0` is used instead of `V` in the following clauses.</span></span>
- <span data-ttu-id="bc12f-295">如果 `V` 是未固定的类型变量中的一个， `U` 则会像以前一样，添加一个精确、上下限或下限</span><span class="sxs-lookup"><span data-stu-id="bc12f-295">If `V` is one of the unfixed type variables, `U` is added as an exact, upper or lower bound as before</span></span>
- <span data-ttu-id="bc12f-296">否则，如果 `U` 为 `null` 或 `default` ，则不进行推理</span><span class="sxs-lookup"><span data-stu-id="bc12f-296">Otherwise, if `U` is `null` or `default`, no inference is made</span></span>
- <span data-ttu-id="bc12f-297">否则，如果 `U` 是可以为 null 的引用类型 `U0?` ，则 `U0` 将在 `U` 后续子句中使用而不是。</span><span class="sxs-lookup"><span data-stu-id="bc12f-297">Otherwise, if `U` is a nullable reference type `U0?`, then `U0` is used instead of `U` in the subsequent clauses.</span></span>

<span data-ttu-id="bc12f-298">实质上，与某个未固定的类型变量直接相关的可为 null 将保留在其边界内。</span><span class="sxs-lookup"><span data-stu-id="bc12f-298">The essence is that nullability that pertains directly to one of the unfixed type variables is preserved into its bounds.</span></span> <span data-ttu-id="bc12f-299">另一方面，如果推断将进一步递归到源和目标类型，则会忽略为空性。</span><span class="sxs-lookup"><span data-stu-id="bc12f-299">For the inferences that recurse further into the source and target types, on the other hand, nullability is ignored.</span></span> <span data-ttu-id="bc12f-300">它可以或不匹配，但如果不匹配，则会在以后选择并应用重载时发出警告。</span><span class="sxs-lookup"><span data-stu-id="bc12f-300">It may or may not match, but if it doesn't, a warning will be issued later if the overload is chosen and applied.</span></span>

### <a name="fixing"></a><span data-ttu-id="bc12f-301">修正 </span><span class="sxs-lookup"><span data-stu-id="bc12f-301">Fixing</span></span>

<span data-ttu-id="bc12f-302">如果多个边界彼此之间相互转换，但不同，则该规范并不是一种很好的描述。</span><span class="sxs-lookup"><span data-stu-id="bc12f-302">The spec currently does not do a good job of describing what happens when multiple bounds are identity convertible to each other, but are different.</span></span> <span data-ttu-id="bc12f-303">这种情况可能发生 `object` 在与之间， `dynamic` 在不同于元素名称的元组类型之间、构造它们的类型之间以及 `C` `C?` 引用类型中的类型之间。</span><span class="sxs-lookup"><span data-stu-id="bc12f-303">This may happen between `object` and `dynamic`, between tuple types that differ only in element names, between types constructed thereof and now also between `C` and `C?` for reference types.</span></span>

<span data-ttu-id="bc12f-304">此外，我们还需要将 "非 null" 从输入表达式传播到结果类型。</span><span class="sxs-lookup"><span data-stu-id="bc12f-304">In addition we need to propagate "nullness" from the input expressions to the result type.</span></span>

<span data-ttu-id="bc12f-305">为了应对这些情况，我们添加了更多的修复阶段，这现在是：</span><span class="sxs-lookup"><span data-stu-id="bc12f-305">To handle these we add more phases to fixing, which is now:</span></span>

1. <span data-ttu-id="bc12f-306">将所有边界中的所有类型都作为候选项收集， `?` 从所有可为 null 的引用类型中移除</span><span class="sxs-lookup"><span data-stu-id="bc12f-306">Gather all the types in all the bounds as candidates, removing `?` from all that are nullable reference types</span></span>
2. <span data-ttu-id="bc12f-307">根据 (保持和限制) 的精确、下限和上限的要求，消除候选项 `null` `default`</span><span class="sxs-lookup"><span data-stu-id="bc12f-307">Eliminate candidates based on requirements of exact, lower and upper bounds (keeping `null` and `default` bounds)</span></span>
3. <span data-ttu-id="bc12f-308">消除没有隐式转换为所有其他候选项的候选项</span><span class="sxs-lookup"><span data-stu-id="bc12f-308">Eliminate candidates that do not have an implicit conversion to all the other candidates</span></span>
4. <span data-ttu-id="bc12f-309">如果剩余的候选项彼此之间没有标识转换，则类型推理将失败</span><span class="sxs-lookup"><span data-stu-id="bc12f-309">If the remaining candidates do not all have identity conversions to one another, then type inference fails</span></span>
5. <span data-ttu-id="bc12f-310">按如下所述 *合并* 剩余候选项</span><span class="sxs-lookup"><span data-stu-id="bc12f-310">*Merge* the remaining candidates as described below</span></span>
6. <span data-ttu-id="bc12f-311">如果生成的候选项是引用类型或不可 null 值类型，并且 *所有* 完全 *限定或下限* 都是可以为 null 的值类型、可以为 null 的引用类型 `null` 或 `default` ，则 `?` 将其添加到生成的候选项，使其成为可以为 null 的值类型或引用类型。</span><span class="sxs-lookup"><span data-stu-id="bc12f-311">If the resulting candidate is a reference type or a nonnullable value type and *all* of the exact bounds or *any* of the lower bounds are nullable value types, nullable reference types, `null` or `default`, then `?` is added to the resulting candidate, making it a nullable value type or reference type.</span></span>

<span data-ttu-id="bc12f-312">两个候选类型之间介绍了 *合并* 。</span><span class="sxs-lookup"><span data-stu-id="bc12f-312">*Merging* is described between two candidate types.</span></span> <span data-ttu-id="bc12f-313">它是可传递和可交换的，因此，可按任何顺序将候选项合并，并获得相同的最终结果。</span><span class="sxs-lookup"><span data-stu-id="bc12f-313">It is transitive and commutative, so the candidates can be merged in any order with the same ultimate result.</span></span> <span data-ttu-id="bc12f-314">如果两个候选类型不能相互转换，则它是不确定的。</span><span class="sxs-lookup"><span data-stu-id="bc12f-314">It is undefined if the two candidate types are not identity convertible to each other.</span></span>

<span data-ttu-id="bc12f-315">*Merge* 函数采用两种候选类型和方向 ( *+* 或 *-* ) ：</span><span class="sxs-lookup"><span data-stu-id="bc12f-315">The *Merge* function takes two candidate types and a direction ( *+* or *-* ):</span></span>

- <span data-ttu-id="bc12f-316">*Merge* (`T` ， `T` ， *d* ) = T</span><span class="sxs-lookup"><span data-stu-id="bc12f-316">*Merge* (`T`, `T`, *d* ) = T</span></span>
- <span data-ttu-id="bc12f-317">*Merge* (`S` ， `T?` ， *+* ) = *merge* (`S?` ， `T` ， *+* ) = *merge* (`S` ， `T` ， *+* ) `?`</span><span class="sxs-lookup"><span data-stu-id="bc12f-317">*Merge* (`S`, `T?`, *+* ) = *Merge* (`S?`, `T`, *+* ) = *Merge* (`S`, `T`, *+* )`?`</span></span>
- <span data-ttu-id="bc12f-318">*Merge* (`S` ， `T?` ， *-* ) = *merge* (`S?` ， `T` ， *-* ) = *merge* (`S` ， `T` ， *-* ) </span><span class="sxs-lookup"><span data-stu-id="bc12f-318">*Merge* (`S`, `T?`, *-* ) = *Merge* (`S?`, `T`, *-* ) = *Merge* (`S`, `T`, *-* )</span></span>
- <span data-ttu-id="bc12f-319">*Merge* (`C<S1,...,Sn>` ， `C<T1,...,Tn>` ， *+* ) = `C<` *merge* (`S1` ， `T1` ， *d1* ) `,...,` *merge* (`Sn` ， `Tn` ， *dn* ) `>` ， *其中*</span><span class="sxs-lookup"><span data-stu-id="bc12f-319">*Merge* (`C<S1,...,Sn>`, `C<T1,...,Tn>`, *+* ) = `C<`*Merge* (`S1`, `T1`, *d1* )`,...,`*Merge* (`Sn`, `Tn`, *dn* )`>`, *where*</span></span>
    - <span data-ttu-id="bc12f-320">`di` = *+* 如果 `i` 的第一个类型参数 `C<...>` 是协变的</span><span class="sxs-lookup"><span data-stu-id="bc12f-320">`di` = *+* if the `i`'th type parameter of `C<...>` is covariant</span></span>
    - <span data-ttu-id="bc12f-321">`di` = *-* 如果的 `i` 第一个类型参数 `C<...>` 为逆变或固定</span><span class="sxs-lookup"><span data-stu-id="bc12f-321">`di` = *-* if the `i`'th type parameter of `C<...>` is contra- or invariant</span></span>
- <span data-ttu-id="bc12f-322">*Merge* (`C<S1,...,Sn>` ， `C<T1,...,Tn>` ， *-* ) = `C<` *merge* (`S1` ， `T1` ， *d1* ) `,...,` *merge* (`Sn` ， `Tn` ， *dn* ) `>` ， *其中*</span><span class="sxs-lookup"><span data-stu-id="bc12f-322">*Merge* (`C<S1,...,Sn>`, `C<T1,...,Tn>`, *-* ) = `C<`*Merge* (`S1`, `T1`, *d1* )`,...,`*Merge* (`Sn`, `Tn`, *dn* )`>`, *where*</span></span>
    - <span data-ttu-id="bc12f-323">`di` = *-* 如果 `i` 的第一个类型参数 `C<...>` 是协变的</span><span class="sxs-lookup"><span data-stu-id="bc12f-323">`di` = *-* if the `i`'th type parameter of `C<...>` is covariant</span></span>
    - <span data-ttu-id="bc12f-324">`di` = *+* 如果的 `i` 第一个类型参数 `C<...>` 为逆变或固定</span><span class="sxs-lookup"><span data-stu-id="bc12f-324">`di` = *+* if the `i`'th type parameter of `C<...>` is contra- or invariant</span></span>
- <span data-ttu-id="bc12f-325">*Merge* (`(S1 s1,..., Sn sn)` ， `(T1 t1,..., Tn tn)` ， *d* ) = `(` *merge* (`S1` ， `T1` ， *d* ) `n1,...,` *合并* (`Sn` ， `Tn` ， *d* ) `nn)` ， *其中*</span><span class="sxs-lookup"><span data-stu-id="bc12f-325">*Merge* (`(S1 s1,..., Sn sn)`, `(T1 t1,..., Tn tn)`, *d* ) = `(`*Merge* (`S1`, `T1`, *d* )`n1,...,`*Merge* (`Sn`, `Tn`, *d* ) `nn)`, *where*</span></span>
    - <span data-ttu-id="bc12f-326">`ni` 如果 `si` 和 `ti` 不同，或者两者都不存在，则不存在</span><span class="sxs-lookup"><span data-stu-id="bc12f-326">`ni` is absent if `si` and `ti` differ, or if both are absent</span></span>
    - <span data-ttu-id="bc12f-327">`ni` 是 `si` `si` 和 `ti` 是否相同</span><span class="sxs-lookup"><span data-stu-id="bc12f-327">`ni` is `si` if `si` and `ti` are the same</span></span>
- <span data-ttu-id="bc12f-328">*Merge* (`object` ， `dynamic`) = *merge* (`dynamic` ， `object`) = `dynamic`</span><span class="sxs-lookup"><span data-stu-id="bc12f-328">*Merge* (`object`, `dynamic`) = *Merge* (`dynamic`, `object`) = `dynamic`</span></span>

## <a name="warnings"></a><span data-ttu-id="bc12f-329">警告</span><span class="sxs-lookup"><span data-stu-id="bc12f-329">Warnings</span></span>

### <a name="potential-null-assignment"></a><span data-ttu-id="bc12f-330">潜在 null 赋值</span><span class="sxs-lookup"><span data-stu-id="bc12f-330">Potential null assignment</span></span>

### <a name="potential-null-dereference"></a><span data-ttu-id="bc12f-331">潜在的空取消引用</span><span class="sxs-lookup"><span data-stu-id="bc12f-331">Potential null dereference</span></span>

### <a name="constraint-nullability-mismatch"></a><span data-ttu-id="bc12f-332">约束为空性不匹配</span><span class="sxs-lookup"><span data-stu-id="bc12f-332">Constraint nullability mismatch</span></span>

### <a name="nullable-types-in-disabled-annotation-context"></a><span data-ttu-id="bc12f-333">禁用的注释上下文中可以为 null 的类型</span><span class="sxs-lookup"><span data-stu-id="bc12f-333">Nullable types in disabled annotation context</span></span>

### <a name="override-and-implementation-nullability-mismatch"></a><span data-ttu-id="bc12f-334">重写和实现为空性不匹配</span><span class="sxs-lookup"><span data-stu-id="bc12f-334">Override and implementation nullability mismatch</span></span>

## <a name="attributes-for-special-null-behavior"></a><span data-ttu-id="bc12f-335">特殊 null 行为的特性</span><span class="sxs-lookup"><span data-stu-id="bc12f-335">Attributes for special null behavior</span></span>


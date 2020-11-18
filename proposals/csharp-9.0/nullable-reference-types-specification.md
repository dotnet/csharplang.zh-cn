---
ms.openlocfilehash: a11b695d8345e521d3d781ed59cd730d83f87fd0
ms.sourcegitcommit: 3d2315ca498ffe0e87848306ca08d0f5b265890d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/18/2020
ms.locfileid: "94717788"
---
# <a name="nullable-reference-types-specification"></a><span data-ttu-id="25396-101">可以为 null 的引用类型规范</span><span class="sxs-lookup"><span data-stu-id="25396-101">Nullable Reference Types Specification</span></span>

<span data-ttu-id="25396-102">\***这是一个正在进行的工作-缺少几个部件或这些部件不完整。** _</span><span class="sxs-lookup"><span data-stu-id="25396-102">\***This is a work in progress - several parts are missing or incomplete.** _</span></span>

<span data-ttu-id="25396-103">此功能添加了两种新类型的可为 null 的类型 (可以为 null 的引用类型和可以为 null 的现有值类型) 为 null 的泛型类型，并引入了静态流分析以实现 null 安全。</span><span class="sxs-lookup"><span data-stu-id="25396-103">This feature adds two new kinds of nullable types (nullable reference types and nullable generic types) to the existing nullable value types, and introduces a static flow analysis for purpose of null-safety.</span></span>

## <a name="syntax"></a><span data-ttu-id="25396-104">语法</span><span class="sxs-lookup"><span data-stu-id="25396-104">Syntax</span></span>

### <a name="nullable-reference-types-and-nullable-type-parameters"></a><span data-ttu-id="25396-105">可以为 null 的引用类型和可以为 null 的类型参数</span><span class="sxs-lookup"><span data-stu-id="25396-105">Nullable reference types and nullable type parameters</span></span>

<span data-ttu-id="25396-106">可以为 null 的引用类型和可以为 null 的类型参数的语法 `T?` 与可以为 null 的值类型的短格式相同，但没有相应的长格式。</span><span class="sxs-lookup"><span data-stu-id="25396-106">Nullable reference types and nullable type parameters have the same syntax `T?` as the short form of nullable value types, but do not have a corresponding long form.</span></span>

<span data-ttu-id="25396-107">出于规范的目的，当前 `nullable_type` 生产被重命名为 `nullable_value_type` ，并 `nullable_reference_type` `nullable_type_parameter` 添加了生产：</span><span class="sxs-lookup"><span data-stu-id="25396-107">For the purposes of the specification, the current `nullable_type` production is renamed to `nullable_value_type`, and `nullable_reference_type` and `nullable_type_parameter` productions are added:</span></span>

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

<span data-ttu-id="25396-108">`non_nullable_reference_type` `nullable_reference_type` 必须是不可 null 引用类型 (类、接口、委托或数组) 。</span><span class="sxs-lookup"><span data-stu-id="25396-108">The `non_nullable_reference_type` in a `nullable_reference_type` must be a nonnullable reference type (class, interface, delegate or array).</span></span>

<span data-ttu-id="25396-109">`non_nullable_non_value_type_parameter`In `nullable_type_parameter` 必须是不被约束为值类型的类型参数。</span><span class="sxs-lookup"><span data-stu-id="25396-109">The `non_nullable_non_value_type_parameter` in `nullable_type_parameter` must be a type parameter that isn't constrained to be a value type.</span></span>

<span data-ttu-id="25396-110">可以为 null 的引用类型和可以为 null 的类型参数不能出现在以下位置：</span><span class="sxs-lookup"><span data-stu-id="25396-110">Nullable reference types and nullable type parameters cannot occur in the following positions:</span></span>

- <span data-ttu-id="25396-111">作为基类或接口</span><span class="sxs-lookup"><span data-stu-id="25396-111">as a base class or interface</span></span>
- <span data-ttu-id="25396-112">作为的接收方 `member_access`</span><span class="sxs-lookup"><span data-stu-id="25396-112">as the receiver of a `member_access`</span></span>
- <span data-ttu-id="25396-113">作为 `type` 中的 `object_creation_expression`</span><span class="sxs-lookup"><span data-stu-id="25396-113">as the `type` in an `object_creation_expression`</span></span>
- <span data-ttu-id="25396-114">作为 `delegate_type` 中的 `delegate_creation_expression`</span><span class="sxs-lookup"><span data-stu-id="25396-114">as the `delegate_type` in a `delegate_creation_expression`</span></span>
- <span data-ttu-id="25396-115">作为 `type` 中的 `is_expression` ， `catch_clause` 或 `type_pattern`</span><span class="sxs-lookup"><span data-stu-id="25396-115">as the `type` in an `is_expression`, a `catch_clause` or a `type_pattern`</span></span>
- <span data-ttu-id="25396-116">作为 `interface` 完全限定的接口成员名称中的</span><span class="sxs-lookup"><span data-stu-id="25396-116">as the `interface` in a fully qualified interface member name</span></span>

<span data-ttu-id="25396-117">在 `nullable_reference_type` 和 `nullable_type_parameter` _disabled \* 可为 null 的注释上下文中提供了警告。</span><span class="sxs-lookup"><span data-stu-id="25396-117">A warning is given on a `nullable_reference_type` and `nullable_type_parameter` in a _disabled\* nullable annotation context.</span></span>

### <a name="class-and-class-constraint"></a><span data-ttu-id="25396-118">`class` and `class?` 约束</span><span class="sxs-lookup"><span data-stu-id="25396-118">`class` and `class?` constraint</span></span>

<span data-ttu-id="25396-119">`class`约束具有可为 null 的对应项 `class?` ：</span><span class="sxs-lookup"><span data-stu-id="25396-119">The `class` constraint has a nullable counterpart `class?`:</span></span>

```antlr
primary_constraint
    : ...
    | 'class' '?'
    ;
```

<span data-ttu-id="25396-120">`class`*已启用* 的批注上下文) 中 (约束的类型参数必须使用不可 null 引用类型进行实例化。</span><span class="sxs-lookup"><span data-stu-id="25396-120">A type parameter constrained with `class` (in an *enabled* annotation context) must be instantiated with a nonnullable reference type.</span></span>

<span data-ttu-id="25396-121">`class?` (或 `class` *已禁用* 的注释上下文中的类型形参) 可以使用可以为 null 的或不可 null 的引用类型进行实例化。</span><span class="sxs-lookup"><span data-stu-id="25396-121">A type parameter constrained with `class?` (or `class` in a *disabled* annotation context) may either be instantiated with a nullable or nonnullable reference type.</span></span>

<span data-ttu-id="25396-122">`class?`*已禁用* 批注上下文中的约束提供了警告。</span><span class="sxs-lookup"><span data-stu-id="25396-122">A warning is given on a `class?` constraint in a *disabled* annotation context.</span></span>

### <a name="notnull-constraint"></a><span data-ttu-id="25396-123">`notnull` constraint</span><span class="sxs-lookup"><span data-stu-id="25396-123">`notnull` constraint</span></span>

<span data-ttu-id="25396-124">使用约束的类型形参 `notnull` 不能是可以为 null 的类型 (可以为 null 的值类型、可以为 null 的引用类型或可以为 null 的类型参数) </span><span class="sxs-lookup"><span data-stu-id="25396-124">A type parameter constrained with `notnull` may not be a nullable type (nullable value type, nullable reference type or nullable type parameter).</span></span>

```antlr
primary_constraint
    : ...
    | 'notnull'
    ;
```

### <a name="default-constraint"></a><span data-ttu-id="25396-125">`default` constraint</span><span class="sxs-lookup"><span data-stu-id="25396-125">`default` constraint</span></span>

<span data-ttu-id="25396-126">`default`约束可用于方法重写或显式实现，以消除 "可以为 null `T?` 的类型参数" 与 "可以为 null 的值类型" (`Nullable<T>`) 。</span><span class="sxs-lookup"><span data-stu-id="25396-126">The `default` constraint can be used on a method override or explicit implementation to disambiguate `T?` meaning "nullable type parameter" from "nullable value type" (`Nullable<T>`).</span></span> <span data-ttu-id="25396-127">如果缺少 `default` 约束，则 `T?` 重写或显式实现中的语法将被解释为 `Nullable<T>`</span><span class="sxs-lookup"><span data-stu-id="25396-127">Lacking the `default` constraint a `T?` syntax in an override or explicit implementation will be interpreted as `Nullable<T>`</span></span>

<span data-ttu-id="25396-128">请参见https://github.com/dotnet/csharplang/blob/master/proposals/csharp-9.0/unconstrained-type-parameter-annotations.md#default-constraint</span><span class="sxs-lookup"><span data-stu-id="25396-128">See https://github.com/dotnet/csharplang/blob/master/proposals/csharp-9.0/unconstrained-type-parameter-annotations.md#default-constraint</span></span>

### <a name="the-null-forgiving-operator"></a><span data-ttu-id="25396-129">包容性运算符</span><span class="sxs-lookup"><span data-stu-id="25396-129">The null-forgiving operator</span></span>

<span data-ttu-id="25396-130">后修复后的 `!` 运算符称为包容性运算符。</span><span class="sxs-lookup"><span data-stu-id="25396-130">The post-fix `!` operator is called the null-forgiving operator.</span></span> <span data-ttu-id="25396-131">它可以应用于 *primary_expression* 或 *null_conditional_expression* 内：</span><span class="sxs-lookup"><span data-stu-id="25396-131">It can be applied on a *primary_expression* or within a *null_conditional_expression*:</span></span>

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

<span data-ttu-id="25396-132">例如：</span><span class="sxs-lookup"><span data-stu-id="25396-132">For example:</span></span>

```csharp
var v = expr!;
expr!.M();
_ = a?.b!.c;
```

<span data-ttu-id="25396-133">`primary_expression`和 `null_conditional_operations_no_suppression` 必须是可以为 null 的类型。</span><span class="sxs-lookup"><span data-stu-id="25396-133">The `primary_expression` and `null_conditional_operations_no_suppression` must be of a nullable type.</span></span>

<span data-ttu-id="25396-134">后缀 `!` 运算符没有运行时效果，它的计算结果为基础表达式的结果。</span><span class="sxs-lookup"><span data-stu-id="25396-134">The postfix `!` operator has no runtime effect - it evaluates to the result of the underlying expression.</span></span> <span data-ttu-id="25396-135">其唯一的作用是将表达式的 null 状态更改为 "not null"，并限制在使用时给出的警告。</span><span class="sxs-lookup"><span data-stu-id="25396-135">Its only role is to change the null state of the expression to "not null", and to limit warnings given on its use.</span></span>

### <a name="nullable-compiler-directives"></a><span data-ttu-id="25396-136">可以为 null 的编译器指令</span><span class="sxs-lookup"><span data-stu-id="25396-136">Nullable compiler directives</span></span>

<span data-ttu-id="25396-137">`#nullable` 指令控制可为 null 的批注和警告上下文。</span><span class="sxs-lookup"><span data-stu-id="25396-137">`#nullable` directives control the nullable annotation and warning contexts.</span></span>

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

<span data-ttu-id="25396-138">`#pragma warning` 展开指令以允许更改可为 null 的警告上下文：</span><span class="sxs-lookup"><span data-stu-id="25396-138">`#pragma warning` directives are expanded to allow changing the nullable warning context:</span></span>

```antlr
pragma_warning_body
    : ...
    | 'warning' whitespace warning_action whitespace 'nullable'
    ;
```

<span data-ttu-id="25396-139">例如：</span><span class="sxs-lookup"><span data-stu-id="25396-139">For example:</span></span>

```csharp
#pragma warning disable nullable
```

## <a name="nullable-contexts"></a><span data-ttu-id="25396-140">可为空上下文</span><span class="sxs-lookup"><span data-stu-id="25396-140">Nullable contexts</span></span>

<span data-ttu-id="25396-141">源代码的每个行都有 *可为 null 的注释上下文* 和 *可以为 null 的警告上下文*。</span><span class="sxs-lookup"><span data-stu-id="25396-141">Every line of source code has a *nullable annotation context* and a *nullable warning context*.</span></span> <span data-ttu-id="25396-142">这些控件控制是否可为 null 的批注是否有效，以及是否给定了可为空性警告。</span><span class="sxs-lookup"><span data-stu-id="25396-142">These control whether nullable annotations have effect, and whether nullability warnings are given.</span></span> <span data-ttu-id="25396-143">给定行的批注上下文已 *禁用* 或 *启用*。</span><span class="sxs-lookup"><span data-stu-id="25396-143">The annotation context of a given line is either *disabled* or *enabled*.</span></span> <span data-ttu-id="25396-144">给定行的警告上下文已 *禁用* 或 *启用*。</span><span class="sxs-lookup"><span data-stu-id="25396-144">The warning context of a given line is either *disabled* or *enabled*.</span></span>

<span data-ttu-id="25396-145">既可在 c # 源代码) 之外的项目级别指定这两个上下文，也可通过预处理器指令在源文件中的任何位置指定 (`#nullable` 。</span><span class="sxs-lookup"><span data-stu-id="25396-145">Both contexts can be specified at the project level (outside of C# source code), or anywhere within a source file via `#nullable` pre-processor directives.</span></span> <span data-ttu-id="25396-146">如果未提供任何项目级别设置，则默认情况下，这两个上下文都是 *禁用* 的。</span><span class="sxs-lookup"><span data-stu-id="25396-146">If no project level settings are provided the default is for both contexts to be *disabled*.</span></span>

<span data-ttu-id="25396-147">`#nullable`指令控制源文本中的批注和警告上下文，并优先于项目级设置。</span><span class="sxs-lookup"><span data-stu-id="25396-147">The `#nullable` directive controls the annotation and warning contexts within the source text, and take precedence over the project-level settings.</span></span>

<span data-ttu-id="25396-148">指令设置上下文 () 它控制后续代码行，直到另一个指令重写它，或直到源文件的结尾。</span><span class="sxs-lookup"><span data-stu-id="25396-148">A directive sets the context(s) it controls for subsequent lines of code, until another directive overrides it, or until the end of the source file.</span></span>

<span data-ttu-id="25396-149">指令的效果如下所示：</span><span class="sxs-lookup"><span data-stu-id="25396-149">The effect of the directives is as follows:</span></span>

- <span data-ttu-id="25396-150">`#nullable disable`：将可为 null 的批注和警告上下文设置为 *已禁用*</span><span class="sxs-lookup"><span data-stu-id="25396-150">`#nullable disable`: Sets the nullable annotation and warning contexts to *disabled*</span></span>
- <span data-ttu-id="25396-151">`#nullable enable`：将可为 null 的批注和警告上下文设置为 *enabled*</span><span class="sxs-lookup"><span data-stu-id="25396-151">`#nullable enable`: Sets the nullable annotation and warning contexts to *enabled*</span></span>
- <span data-ttu-id="25396-152">`#nullable restore`：将可为 null 的批注和警告上下文还原到项目设置</span><span class="sxs-lookup"><span data-stu-id="25396-152">`#nullable restore`: Restores the nullable annotation and warning contexts to project settings</span></span>
- <span data-ttu-id="25396-153">`#nullable disable annotations`：将可为 null 的注释上下文设置为 *已禁用*</span><span class="sxs-lookup"><span data-stu-id="25396-153">`#nullable disable annotations`: Sets the nullable annotation context to *disabled*</span></span>
- <span data-ttu-id="25396-154">`#nullable enable annotations`：将可为 null 的注释上下文设置为 *enabled*</span><span class="sxs-lookup"><span data-stu-id="25396-154">`#nullable enable annotations`: Sets the nullable annotation context to *enabled*</span></span>
- <span data-ttu-id="25396-155">`#nullable restore annotations`：将可为 null 的注释上下文还原到项目设置</span><span class="sxs-lookup"><span data-stu-id="25396-155">`#nullable restore annotations`: Restores the nullable annotation context to project settings</span></span>
- <span data-ttu-id="25396-156">`#nullable disable warnings`：将可为 null 的警告上下文设置为 *已禁用*</span><span class="sxs-lookup"><span data-stu-id="25396-156">`#nullable disable warnings`: Sets the nullable warning context to *disabled*</span></span>
- <span data-ttu-id="25396-157">`#nullable enable warnings`：将可为 null 的警告上下文设置为 *已启用*</span><span class="sxs-lookup"><span data-stu-id="25396-157">`#nullable enable warnings`: Sets the nullable warning context to *enabled*</span></span>
- <span data-ttu-id="25396-158">`#nullable restore warnings`：将可为 null 的警告上下文还原到项目设置</span><span class="sxs-lookup"><span data-stu-id="25396-158">`#nullable restore warnings`: Restores the nullable warning context to project settings</span></span>

## <a name="nullability-of-types"></a><span data-ttu-id="25396-159">类型为 Null 性</span><span class="sxs-lookup"><span data-stu-id="25396-159">Nullability of types</span></span>

<span data-ttu-id="25396-160">给定的类型可以具有以下三个 nullabilities 之一： *在意*、 *不可 null* 和 *可以为 null*。</span><span class="sxs-lookup"><span data-stu-id="25396-160">A given type can have one of three nullabilities: *oblivious*, *nonnullable*, and *nullable*.</span></span>

<span data-ttu-id="25396-161">如果将可能的值分配给 *不可 null* 类型，则可能会引发警告 `null` 。</span><span class="sxs-lookup"><span data-stu-id="25396-161">*Nonnullable* types may cause warnings if a potential `null` value is assigned to them.</span></span> <span data-ttu-id="25396-162">但是，*在意* 和可以 *为* null 的类型为 "可 *赋值*"，可以 `null` 为其分配值而不会出现警告。</span><span class="sxs-lookup"><span data-stu-id="25396-162">*Oblivious* and *nullable* types, however, are "*null-assignable*" and can have `null` values assigned to them without warnings.</span></span>

<span data-ttu-id="25396-163">可以取消引用或分配 *在意* 和 *不可 null* 类型的值，而不会出现警告。</span><span class="sxs-lookup"><span data-stu-id="25396-163">Values of *oblivious* and *nonnullable* types can be dereferenced or assigned without warnings.</span></span> <span data-ttu-id="25396-164">但是， *可以为* null 的类型的值为 "*空* 值"，并可能在取消引用或赋值时导致警告，而不进行正确的 null 检查。</span><span class="sxs-lookup"><span data-stu-id="25396-164">Values of *nullable* types, however, are "*null-yielding*" and may cause warnings when dereferenced or assigned without proper null checking.</span></span>

<span data-ttu-id="25396-165">Null 产生类型的 *默认 null 状态* 是 "可能是 null" 或 "可能是默认值"。</span><span class="sxs-lookup"><span data-stu-id="25396-165">The *default null state* of a null-yielding type is "maybe null" or "maybe default".</span></span> <span data-ttu-id="25396-166">非 null 生成类型的默认 null 状态为 "not null"。</span><span class="sxs-lookup"><span data-stu-id="25396-166">The default null state of a non-null-yielding type is "not null".</span></span>

<span data-ttu-id="25396-167">类型的类型和在确定其为 null 性时所发生的可为 null 的注释上下文：</span><span class="sxs-lookup"><span data-stu-id="25396-167">The kind of type and the nullable annotation context it occurs in determine its nullability:</span></span>

- <span data-ttu-id="25396-168">不可 null 值类型 `S` 始终为 *不可 null*</span><span class="sxs-lookup"><span data-stu-id="25396-168">A nonnullable value type `S` is always *nonnullable*</span></span>
- <span data-ttu-id="25396-169">可以为 null 的值类型 `S?` 始终 *可以为 null*</span><span class="sxs-lookup"><span data-stu-id="25396-169">A nullable value type `S?` is always *nullable*</span></span>
- <span data-ttu-id="25396-170">`C`*禁用* 的注释上下文中的批注引用类型为 *在意*</span><span class="sxs-lookup"><span data-stu-id="25396-170">An unannotated reference type `C` in a *disabled* annotation context is *oblivious*</span></span>
- <span data-ttu-id="25396-171">`C`*启用* 的批注上下文中的批注引用类型为 *不可 null*</span><span class="sxs-lookup"><span data-stu-id="25396-171">An unannotated reference type `C` in an *enabled* annotation context is *nonnullable*</span></span>
- <span data-ttu-id="25396-172">可以为 null 的引用类型可以 `C?` *为 null* (但在 *禁用* 的批注上下文中可能生成警告) </span><span class="sxs-lookup"><span data-stu-id="25396-172">A nullable reference type `C?` is *nullable* (but a warning may be yielded in a *disabled* annotation context)</span></span>

<span data-ttu-id="25396-173">类型参数还会考虑它们的约束：</span><span class="sxs-lookup"><span data-stu-id="25396-173">Type parameters additionally take their constraints into account:</span></span>

- <span data-ttu-id="25396-174">`T`如果任何) 为可以为 null 的类型或 `class?` 约束 *可以为 null* ，则为所有约束都 (的类型参数</span><span class="sxs-lookup"><span data-stu-id="25396-174">A type parameter `T` where all constraints (if any) are either nullable types or the `class?` constraint is *nullable*</span></span>
- <span data-ttu-id="25396-175">一个类型参数 `T` ，其中至少有一个约束为 *在意* 或 *不可 null* ，或者其中一个 `struct` 或 `class` 或 `notnull` 约束为</span><span class="sxs-lookup"><span data-stu-id="25396-175">A type parameter `T` where at least one constraint is either *oblivious* or *nonnullable* or one of the `struct` or `class` or `notnull` constraints is</span></span>
    - <span data-ttu-id="25396-176">*禁用* 的注释上下文中的 *在意*</span><span class="sxs-lookup"><span data-stu-id="25396-176">*oblivious* in a *disabled* annotation context</span></span>
    - <span data-ttu-id="25396-177">*已启用* 的批注上下文中的 *不可 null*</span><span class="sxs-lookup"><span data-stu-id="25396-177">*nonnullable* in an *enabled* annotation context</span></span>
- <span data-ttu-id="25396-178">可以为 null 的类型参数 `T?` *可以为 null*，但如果不是值类型，则 *已禁用* 的批注上下文中会生成一个警告。 `T`</span><span class="sxs-lookup"><span data-stu-id="25396-178">A nullable type parameter `T?` is *nullable*, but a warning is yielded in a *disabled* annotation context if `T` isn't a value type</span></span>

### <a name="oblivious-vs-nonnullable"></a><span data-ttu-id="25396-179">在意 vs 不可 null</span><span class="sxs-lookup"><span data-stu-id="25396-179">Oblivious vs nonnullable</span></span>

<span data-ttu-id="25396-180">`type`当类型的最后一个标记在该上下文中时，将被视为在给定的批注上下文中发生。</span><span class="sxs-lookup"><span data-stu-id="25396-180">A `type` is deemed to occur in a given annotation context when the last token of the type is within that context.</span></span>

<span data-ttu-id="25396-181">源代码中的给定引用类型 `C` 是解释为在意还是不可 null 依赖于源代码的注释上下文。</span><span class="sxs-lookup"><span data-stu-id="25396-181">Whether a given reference type `C` in source code is interpreted as oblivious or nonnullable depends on the annotation context of that source code.</span></span> <span data-ttu-id="25396-182">但一旦建立，就会将其视为该类型的一部分，并在替换泛型类型参数的过程中将其视为 "随之一起移动"。</span><span class="sxs-lookup"><span data-stu-id="25396-182">But once established, it is considered part of that type, and "travels with it" e.g. during substitution of generic type arguments.</span></span> <span data-ttu-id="25396-183">这就像在类型上有一个批注 `?` ，但不可见。</span><span class="sxs-lookup"><span data-stu-id="25396-183">It is as if there is an annotation like `?` on the type, but invisible.</span></span>

## <a name="constraints"></a><span data-ttu-id="25396-184">约束</span><span class="sxs-lookup"><span data-stu-id="25396-184">Constraints</span></span>

<span data-ttu-id="25396-185">可以为 null 的引用类型可用作泛型约束。</span><span class="sxs-lookup"><span data-stu-id="25396-185">Nullable reference types can be used as generic constraints.</span></span>

<span data-ttu-id="25396-186">`class?` 表示 "可能为 null 的引用类型" 的新约束，而 `class` *启用* 的批注上下文中表示 "不可 null 引用类型"。</span><span class="sxs-lookup"><span data-stu-id="25396-186">`class?` is a new constraint denoting "possibly nullable reference type", whereas `class` in an *enabled* annotation context denotes "nonnullable reference type".</span></span>

<span data-ttu-id="25396-187">`default` 一个新约束，用于表示未知类型参数或值类型。</span><span class="sxs-lookup"><span data-stu-id="25396-187">`default` is a new constraint denoting a type parameter that isn't known to be a reference or value type.</span></span> <span data-ttu-id="25396-188">它只能用于重写和显式实现的方法。</span><span class="sxs-lookup"><span data-stu-id="25396-188">It can only be used on overridden and explicitly implemented methods.</span></span> <span data-ttu-id="25396-189">对于此约束， `T?` 表示可以为 null 的类型参数，而不是的简写形式 `Nullable<T>` 。</span><span class="sxs-lookup"><span data-stu-id="25396-189">With this constraint, `T?` means a nullable type parameter, as opposed to being a shorthand for `Nullable<T>`.</span></span>

<span data-ttu-id="25396-190">`notnull` 表示不可 null 类型参数的新约束。</span><span class="sxs-lookup"><span data-stu-id="25396-190">`notnull` is a new constraint denoting a type parameter that is nonnullable.</span></span>

<span data-ttu-id="25396-191">类型参数或约束的为空性并不会影响类型是否满足约束，但目前已 (可以为 null 的值类型不满足约束) 的情况除外 `struct` 。</span><span class="sxs-lookup"><span data-stu-id="25396-191">The nullability of a type argument or of a constraint does not impact whether the type satisfies the constraint, except where that is already the case today (nullable value types do not satisfy the `struct` constraint).</span></span> <span data-ttu-id="25396-192">但是，如果类型参数不满足约束的为 null 性要求，则可以提供警告。</span><span class="sxs-lookup"><span data-stu-id="25396-192">However, if the type argument does not satisfy the nullability requirements of the constraint, a warning may be given.</span></span>

## <a name="null-state-and-null-tracking"></a><span data-ttu-id="25396-193">Null 状态和 null 跟踪</span><span class="sxs-lookup"><span data-stu-id="25396-193">Null state and null tracking</span></span>

<span data-ttu-id="25396-194">给定源位置中的每个表达式都具有 *null 状态*，指示其是否被认为可能计算为 null。</span><span class="sxs-lookup"><span data-stu-id="25396-194">Every expression in a given source location has a *null state*, which indicated whether it is believed to potentially evaluate to null.</span></span> <span data-ttu-id="25396-195">Null 状态为 "not null"、"可能为 null" 或 "可能为默认值"。</span><span class="sxs-lookup"><span data-stu-id="25396-195">The null state is either "not null", "maybe null", or "maybe default".</span></span> <span data-ttu-id="25396-196">Null 状态用于确定是否应为不安全的转换和取消引用提供警告。</span><span class="sxs-lookup"><span data-stu-id="25396-196">The null state is used to determine whether a warning should be given about null-unsafe conversions and dereferences.</span></span>

<span data-ttu-id="25396-197">"可能为 null" 和 "可能为默认值" 之间的区别非常微妙，适用于类型参数。</span><span class="sxs-lookup"><span data-stu-id="25396-197">The distinction between "maybe null" and "maybe default" is subtle and applies to type parameters.</span></span> <span data-ttu-id="25396-198">区别在于， `T` 状态为 "可能为 null" 的类型参数意味着该值在合法值的域中， `T` 但合法值可能包括 `null` 。</span><span class="sxs-lookup"><span data-stu-id="25396-198">The distinction is that a type parameter `T` which has the state "maybe null" means the value is in the domain of legal values for `T` however that legal value may include `null`.</span></span> <span data-ttu-id="25396-199">如果 "可能为默认值"，则表示该值可能在的合法域范围之外 `T` 。</span><span class="sxs-lookup"><span data-stu-id="25396-199">Where as a "maybe default" means that the value may be outside the legal domain of values for `T`.</span></span> 

<span data-ttu-id="25396-200">例如：</span><span class="sxs-lookup"><span data-stu-id="25396-200">Example:</span></span> 

```c#
// The value `t` here has the state "maybe null". It's possible for `T` to be instantiated
// with `string?` in which case `null` would be within the domain of legal values here. The 
// assumption though is the value provided here is within the legal values of `T`. Hence 
// if `T` is `string` then `null` will not be a value, just as we assume that `null` is not
// provided for a normal `string` parameter
void M<T>(T t)
{
    // There is no guarantee that default(T) is within the legal values for T hence the 
    // state *must* be "maybe-default" and hence `local` must be `T?`
    T? local = default(T);
}
```

### <a name="null-tracking-for-variables"></a><span data-ttu-id="25396-201">变量的 Null 跟踪</span><span class="sxs-lookup"><span data-stu-id="25396-201">Null tracking for variables</span></span>

<span data-ttu-id="25396-202">对于表示变量、字段或属性的某些表达式，将基于对它们的赋值、对它们执行的测试以及它们之间的控制流，在发生的情况之间跟踪 null 状态。</span><span class="sxs-lookup"><span data-stu-id="25396-202">For certain expressions denoting variables, fields or properties, the null state is tracked between occurrences, based on assignments to them, tests performed on them and the control flow between them.</span></span> <span data-ttu-id="25396-203">这类似于为变量跟踪明确赋值的方式。</span><span class="sxs-lookup"><span data-stu-id="25396-203">This is similar to how definite assignment is tracked for variables.</span></span> <span data-ttu-id="25396-204">跟踪的表达式如下所示：</span><span class="sxs-lookup"><span data-stu-id="25396-204">The tracked expressions are the ones of the following form:</span></span>

```antlr
tracked_expression
    : simple_name
    | this
    | base
    | tracked_expression '.' identifier
    ;
```

<span data-ttu-id="25396-205">其中，标识符表示字段或属性。</span><span class="sxs-lookup"><span data-stu-id="25396-205">Where the identifiers denote fields or properties.</span></span>

<span data-ttu-id="25396-206">被跟踪变量的 null 状态在无法访问的代码中为 "not null"。</span><span class="sxs-lookup"><span data-stu-id="25396-206">The null state for tracked variables is "not null" in unreachable code.</span></span> <span data-ttu-id="25396-207">这遵循了有关无法访问的代码的其他决策，如考虑将所有局部变量明确赋值。</span><span class="sxs-lookup"><span data-stu-id="25396-207">This follows other decisions around unreachable code like considering all locals to be definitely assigned.</span></span>

<span data-ttu-id="25396-208">\***描述类似于明确赋值的空状态转换** _</span><span class="sxs-lookup"><span data-stu-id="25396-208">\***Describe null state transitions similar to definite assignment** _</span></span>

### <a name="null-state-for-expressions"></a><span data-ttu-id="25396-209">表达式为 Null</span><span class="sxs-lookup"><span data-stu-id="25396-209">Null state for expressions</span></span>

<span data-ttu-id="25396-210">表达式的 null 状态派生自其形式和类型以及它所涉及的变量的 null 状态。</span><span class="sxs-lookup"><span data-stu-id="25396-210">The null state of an expression is derived from its form and type, and from the null state of variables involved in it.</span></span>

### <a name="literals"></a><span data-ttu-id="25396-211">文本</span><span class="sxs-lookup"><span data-stu-id="25396-211">Literals</span></span>

<span data-ttu-id="25396-212">文本的 null 状态 `null` 取决于表达式的目标类型。</span><span class="sxs-lookup"><span data-stu-id="25396-212">The null state of a `null` literal depends on the target type of the expression.</span></span> <span data-ttu-id="25396-213">如果目标类型是受引用类型约束的类型参数，则它是 "可能的默认值"。</span><span class="sxs-lookup"><span data-stu-id="25396-213">If the target type is a type parameter constrained to a reference type then it's "maybe default".</span></span> <span data-ttu-id="25396-214">否则为 "可能为 null"。</span><span class="sxs-lookup"><span data-stu-id="25396-214">Otherwise it is "maybe null".</span></span>

<span data-ttu-id="25396-215">文本的 null 状态 `default` 取决于文本的目标类型 `default` 。</span><span class="sxs-lookup"><span data-stu-id="25396-215">The null state of a `default` literal depends on the target type of the `default` literal.</span></span> <span data-ttu-id="25396-216">`default`目标类型的文本与 `T` 表达式具有相同的 null 状态 `default(T)` 。</span><span class="sxs-lookup"><span data-stu-id="25396-216">A `default` literal with target type `T` has the same null state as the `default(T)` expression.</span></span>

<span data-ttu-id="25396-217">任何其他文本的 null 状态为 "not null"。</span><span class="sxs-lookup"><span data-stu-id="25396-217">The null state of any other literal is "not null".</span></span>

### <a name="simple-names"></a><span data-ttu-id="25396-218">简单名称</span><span class="sxs-lookup"><span data-stu-id="25396-218">Simple names</span></span>

<span data-ttu-id="25396-219">如果 `simple_name` 未归类为值，则其 null 状态为 "not null"。</span><span class="sxs-lookup"><span data-stu-id="25396-219">If a `simple_name` is not classified as a value, its null state is "not null".</span></span> <span data-ttu-id="25396-220">否则为跟踪的表达式，其 null 状态将为其在此源位置跟踪的 null 状态。</span><span class="sxs-lookup"><span data-stu-id="25396-220">Otherwise it is a tracked expression, and its null state is its tracked null state at this source location.</span></span>

### <a name="member-access"></a><span data-ttu-id="25396-221">成员访问</span><span class="sxs-lookup"><span data-stu-id="25396-221">Member access</span></span>

<span data-ttu-id="25396-222">如果 `member_access` 未归类为值，则其 null 状态为 "not null"。</span><span class="sxs-lookup"><span data-stu-id="25396-222">If a `member_access` is not classified as a value, its null state is "not null".</span></span> <span data-ttu-id="25396-223">否则，如果是跟踪的表达式，则其 null 状态将为其在此源位置跟踪的 null 状态。</span><span class="sxs-lookup"><span data-stu-id="25396-223">Otherwise, if it is a tracked expression, its null state is its tracked null state at this source location.</span></span> <span data-ttu-id="25396-224">否则，其 null 状态为其类型的默认 null 状态。</span><span class="sxs-lookup"><span data-stu-id="25396-224">Otherwise its null state is the default null state of its type.</span></span>

```c#
var person = new Person();

// The receiver is a tracked expression hence the member_access of the property 
// is tracked as well 
if (person.FirstName is not null)
{
    Use(person.FirstName);
}

// The return of an invocation is not a tracked expression hence the member_access
// of the return is also not tracked
if (GetAnonymous().FirstName is not null)
{
    // Warning: Cannot convert null literal to non-nullable reference type.
    Use(GetAnonymous().FirstName);
}

void Use(string s) 
{ 
    // ...
}

public class Person
{
    public string? FirstName { get; set; }
    public string? LastName { get; set; }

    private static Person s_anonymous = new Person();
    public static Person GetAnonymous() => s_anonymous;
}
```

### <a name="invocation-expressions"></a><span data-ttu-id="25396-225">调用表达式</span><span class="sxs-lookup"><span data-stu-id="25396-225">Invocation expressions</span></span>

<span data-ttu-id="25396-226">如果 `invocation_expression` 调用使用一个或多个特性声明的成员以实现特殊的 null 行为，则 null 状态由这些特性决定。</span><span class="sxs-lookup"><span data-stu-id="25396-226">If an `invocation_expression` invokes a member that is declared with one or more attributes for special null behavior, the null state is determined by those attributes.</span></span> <span data-ttu-id="25396-227">否则，表达式的 null 状态为其类型的默认 null 状态。</span><span class="sxs-lookup"><span data-stu-id="25396-227">Otherwise the null state of the expression is the default null state of its type.</span></span>

<span data-ttu-id="25396-228">`invocation_expression`编译器不跟踪的 null 状态。</span><span class="sxs-lookup"><span data-stu-id="25396-228">The null state of an `invocation_expression` is not tracked by the compiler.</span></span>

```c#

// The result of an invocation_expression is not tracked
if (GetText() is not null)
{
    // Warning: Converting null literal or possible null value to non-nullable type.
    string s = GetText();
    // Warning: Dereference of a possibly null reference.
    Use(s);
}

// Nullable friendly pattern
if (GetText() is string s)
{
    Use(s);
}

string? GetText() => ... 
Use(string s) {  }
```

### <a name="element-access"></a><span data-ttu-id="25396-229">元素访问</span><span class="sxs-lookup"><span data-stu-id="25396-229">Element access</span></span>

<span data-ttu-id="25396-230">如果 `element_access` 调用使用一个或多个特性声明的索引器来实现特殊的 null 行为，则 null 状态由这些特性决定。</span><span class="sxs-lookup"><span data-stu-id="25396-230">If an `element_access` invokes an indexer that is declared with one or more attributes for special null behavior, the null state is determined by those attributes.</span></span> <span data-ttu-id="25396-231">否则，表达式的 null 状态为其类型的默认 null 状态。</span><span class="sxs-lookup"><span data-stu-id="25396-231">Otherwise the null state of the expression is the default null state of its type.</span></span>

```c#
object?[] array = ...;
if (array[0] != null)
{
    // Warning: Converting null literal or possible null value to non-nullable type.
    object o = array[0];
    // Warning: Dereference of a possibly null reference.
    Console.WriteLine(o.ToString());
}

// Nullable friendly pattern
if (array[0] is {} o)
{
    Console.WriteLine(o.ToString());
}
```

### <a name="base-access"></a><span data-ttu-id="25396-232">基本访问权限</span><span class="sxs-lookup"><span data-stu-id="25396-232">Base access</span></span>

<span data-ttu-id="25396-233">如果 `B` 表示封闭类型的基类型， `base.I` 则具有与相同的 null 状态， `((B)this).I` 并且与 `base[E]` 具有相同的 null 状态 `((B)this)[E]` 。</span><span class="sxs-lookup"><span data-stu-id="25396-233">If `B` denotes the base type of the enclosing type, `base.I` has the same null state as `((B)this).I` and `base[E]` has the same null state as `((B)this)[E]`.</span></span>

### <a name="default-expressions"></a><span data-ttu-id="25396-234">默认表达式</span><span class="sxs-lookup"><span data-stu-id="25396-234">Default expressions</span></span>

<span data-ttu-id="25396-235">`default(T)` 对于类型的属性，具有 null 状态 `T` ：</span><span class="sxs-lookup"><span data-stu-id="25396-235">`default(T)` has the null state based on the properties of the type `T`:</span></span>

- <span data-ttu-id="25396-236">如果类型是 _nonnullable \* 类型，则它的状态为 "not null"。</span><span class="sxs-lookup"><span data-stu-id="25396-236">If the type is a _nonnullable\* type then it has the null state "not null"</span></span>
- <span data-ttu-id="25396-237">如果类型是类型参数，则为; 否则，它的状态为 "可能为默认值"</span><span class="sxs-lookup"><span data-stu-id="25396-237">Else if the type is a type parameter then it has the null state "maybe default"</span></span>
- <span data-ttu-id="25396-238">否则它的状态为 "可能为 null"</span><span class="sxs-lookup"><span data-stu-id="25396-238">Else it has the null state "maybe null"</span></span>

### <a name="null-conditional-expressions-"></a><span data-ttu-id="25396-239">空条件表达式？。</span><span class="sxs-lookup"><span data-stu-id="25396-239">Null-conditional expressions ?.</span></span>

<span data-ttu-id="25396-240">`null_conditional_expression`基于表达式类型，具有 null 状态。</span><span class="sxs-lookup"><span data-stu-id="25396-240">A `null_conditional_expression` has the null state based on the expression type.</span></span> <span data-ttu-id="25396-241">请注意，这是指的类型 `null_conditional_expression` ，而不是所调用的成员的原始类型：</span><span class="sxs-lookup"><span data-stu-id="25396-241">Note that this refers to the type of the `null_conditional_expression`, not the original type of the member being invoked:</span></span>

- <span data-ttu-id="25396-242">如果类型是 *可以为* null 的值类型，则它的状态为 "可能为 null"</span><span class="sxs-lookup"><span data-stu-id="25396-242">If the type is a *nullable* value type then it has the null state "maybe null"</span></span>
- <span data-ttu-id="25396-243">否则，如果该类型是 *可以为* null 的类型参数，则它将具有 null 状态 "可能是默认值"</span><span class="sxs-lookup"><span data-stu-id="25396-243">Else if the type is a *nullable* type parameter then it has the null state "maybe default"</span></span>
- <span data-ttu-id="25396-244">否则它的状态为 "可能为 null"</span><span class="sxs-lookup"><span data-stu-id="25396-244">Else it has the null state "maybe null"</span></span>

### <a name="cast-expressions"></a><span data-ttu-id="25396-245">强制转换表达式</span><span class="sxs-lookup"><span data-stu-id="25396-245">Cast expressions</span></span>

<span data-ttu-id="25396-246">如果强制转换表达式 `(T)E` 调用用户定义的转换，则该表达式的 null 状态为用户定义的转换类型的默认 null 状态。</span><span class="sxs-lookup"><span data-stu-id="25396-246">If a cast expression `(T)E` invokes a user-defined conversion, then the null state of the expression is the default null state for the type of the user-defined conversion.</span></span> <span data-ttu-id="25396-247">否则：</span><span class="sxs-lookup"><span data-stu-id="25396-247">Otherwise:</span></span>

- <span data-ttu-id="25396-248">如果 `T` 是 *不可 null* 值类型，则 `T` 具有 null 状态 "not null"</span><span class="sxs-lookup"><span data-stu-id="25396-248">If `T` is a *nonnullable* value type then `T` has the null state "not null"</span></span>
- <span data-ttu-id="25396-249">否则 `T` ，如果是 *可以为* null 的值类型，则 `T` 具有 null 状态 "可能为 null"</span><span class="sxs-lookup"><span data-stu-id="25396-249">Else if `T` is a *nullable* value type then `T` has the null state "maybe null"</span></span>
- <span data-ttu-id="25396-250">否则，如果 `T` 是形式为 *null* 的类型， `U?` 其中 `U` 为类型参数，则为 `T` null 状态 "可能是默认值"</span><span class="sxs-lookup"><span data-stu-id="25396-250">Else if `T` is a *nullable* type in the form `U?` where `U` is a type parameter then `T` has the null state "maybe default"</span></span>
- <span data-ttu-id="25396-251">否则 `T` ，如果是一个 *可以为* null 的类型，并且 `E` 具有 null 状态 "可能是 null" 或 "可能是默认值"，则将其 `T` 状态设置为 null。</span><span class="sxs-lookup"><span data-stu-id="25396-251">Else if `T` is a *nullable* type, and `E` has null state "maybe null" or "maybe default", then `T` has the null state "maybe null"</span></span>
- <span data-ttu-id="25396-252">否则 `T` ，如果是类型参数，并且 `E` 具有 null 状态 "可能是 null" 或 "可能是默认值"，则将其 `T` 状态设置为 null 状态 "可能是默认值"</span><span class="sxs-lookup"><span data-stu-id="25396-252">Else if `T` is a type parameter, and `E` has null state "maybe null" or "maybe default", then `T` has the null state "maybe default"</span></span>
- <span data-ttu-id="25396-253">Else `T` 的 null 状态与相同 `E`</span><span class="sxs-lookup"><span data-stu-id="25396-253">Else `T` has the same null state as `E`</span></span>

### <a name="unary-and-binary-operators"></a><span data-ttu-id="25396-254">一元运算符和二元运算符</span><span class="sxs-lookup"><span data-stu-id="25396-254">Unary and binary operators</span></span>

<span data-ttu-id="25396-255">如果一元运算符或二元运算符调用用户定义的运算符，则该表达式的 null 状态为用户定义的运算符的类型的默认 null 状态。</span><span class="sxs-lookup"><span data-stu-id="25396-255">If a unary or binary operator invokes an user-defined operator then the null state of the expression is the default null state for the type of the user-defined operator.</span></span> <span data-ttu-id="25396-256">否则为表达式的 null 状态。</span><span class="sxs-lookup"><span data-stu-id="25396-256">Otherwise it is the null state of the expression.</span></span>

<span data-ttu-id="25396-257">\***对于 `+` 字符串和委托，二进制文件的特殊** 操作是什么？_</span><span class="sxs-lookup"><span data-stu-id="25396-257">\***Something special to do for binary `+` over strings and delegates?** _</span></span>

### <a name="await-expressions"></a><span data-ttu-id="25396-258">Await 表达式</span><span class="sxs-lookup"><span data-stu-id="25396-258">Await expressions</span></span>

<span data-ttu-id="25396-259">的 null 状态 `await E` 为其类型的默认 null 状态。</span><span class="sxs-lookup"><span data-stu-id="25396-259">The null state of `await E` is the default null state of its type.</span></span>

### <a name="the-as-operator"></a><span data-ttu-id="25396-260">`as` 运算符</span><span class="sxs-lookup"><span data-stu-id="25396-260">The `as` operator</span></span>

<span data-ttu-id="25396-261">表达式的 null 状态 `E as T` 首先依赖于该类型的属性 `T` 。</span><span class="sxs-lookup"><span data-stu-id="25396-261">The null state of an `E as T` expression depends first on properties of the type `T`.</span></span> <span data-ttu-id="25396-262">如果的类型 `T` 为 _nonnullable \*，则 null 状态为 "not null"。</span><span class="sxs-lookup"><span data-stu-id="25396-262">If the type of `T` is _nonnullable\* then the null state is "not null".</span></span> <span data-ttu-id="25396-263">否则，null 状态取决于从类型到类型的转换 `E` `T` ：</span><span class="sxs-lookup"><span data-stu-id="25396-263">Otherwise the null state depends on the conversion from the type of `E` to type `T`:</span></span>

- <span data-ttu-id="25396-264">如果转换为标识、装箱、隐式引用或隐式可为 null 的转换，则 null 状态为 null 状态 `E`</span><span class="sxs-lookup"><span data-stu-id="25396-264">If the conversion is an identity, boxing, implicit reference, or implicit nullable conversion, then the null state is the null state of `E`</span></span>
- <span data-ttu-id="25396-265">否则 `T` ，如果是类型参数，则它的状态为 "可能是默认值"</span><span class="sxs-lookup"><span data-stu-id="25396-265">Else if `T` is a type parameter then it has the null state "maybe default"</span></span>
- <span data-ttu-id="25396-266">否则它的状态为 "可能为 null"</span><span class="sxs-lookup"><span data-stu-id="25396-266">Else it has the null state "maybe null"</span></span>

### <a name="the-null-coalescing-operator"></a><span data-ttu-id="25396-267">Null 合并运算符</span><span class="sxs-lookup"><span data-stu-id="25396-267">The null-coalescing operator</span></span>

<span data-ttu-id="25396-268">的 null 状态 `E1 ?? E2` 为的空状态 `E2`</span><span class="sxs-lookup"><span data-stu-id="25396-268">The null state of `E1 ?? E2` is the null state of `E2`</span></span>

### <a name="the-conditional-operator"></a><span data-ttu-id="25396-269">条件运算符</span><span class="sxs-lookup"><span data-stu-id="25396-269">The conditional operator</span></span>

<span data-ttu-id="25396-270">的 null 状态 `E1 ? E2 : E3` 基于和的 null 状态 `E2` `E3` ：</span><span class="sxs-lookup"><span data-stu-id="25396-270">The null state of `E1 ? E2 : E3` is based on the null state of `E2` and `E3`:</span></span>

- <span data-ttu-id="25396-271">如果两者都为 "not null"，则 null 状态为 "not null"。</span><span class="sxs-lookup"><span data-stu-id="25396-271">If both are "not null" then the null state is "not null"</span></span>
- <span data-ttu-id="25396-272">否则，如果两者都为 "可能是默认值"，则 null 状态为 "可能是默认值"</span><span class="sxs-lookup"><span data-stu-id="25396-272">Else if either is "maybe default" then the null state is "maybe default"</span></span>
- <span data-ttu-id="25396-273">否则，null 状态为 "not null"</span><span class="sxs-lookup"><span data-stu-id="25396-273">Else the null state is "not null"</span></span>

### <a name="query-expressions"></a><span data-ttu-id="25396-274">查询表达式</span><span class="sxs-lookup"><span data-stu-id="25396-274">Query expressions</span></span>

<span data-ttu-id="25396-275">查询表达式的 null 状态为其类型的默认 null 状态。</span><span class="sxs-lookup"><span data-stu-id="25396-275">The null state of a query expression is the default null state of its type.</span></span>

<span data-ttu-id="25396-276">*此处需要其他工作*</span><span class="sxs-lookup"><span data-stu-id="25396-276">*Additional work needed here*</span></span>

### <a name="assignment-operators"></a><span data-ttu-id="25396-277">赋值运算符</span><span class="sxs-lookup"><span data-stu-id="25396-277">Assignment operators</span></span>

<span data-ttu-id="25396-278">`E1 = E2` 和 `E1 op= E2` 都具有与 `E2` 应用任何隐式转换相同的空状态。</span><span class="sxs-lookup"><span data-stu-id="25396-278">`E1 = E2` and `E1 op= E2` have the same null state as `E2` after any implicit conversions have been applied.</span></span>

### <a name="expressions-that-propagate-null-state"></a><span data-ttu-id="25396-279">传播 null 状态的表达式</span><span class="sxs-lookup"><span data-stu-id="25396-279">Expressions that propagate null state</span></span>

<span data-ttu-id="25396-280">`(E)``checked(E)`和 `unchecked(E)` 都具有与相同的 null 状态 `E` 。</span><span class="sxs-lookup"><span data-stu-id="25396-280">`(E)`, `checked(E)` and `unchecked(E)` all have the same null state as `E`.</span></span>

### <a name="expressions-that-are-never-null"></a><span data-ttu-id="25396-281">从不为 null 的表达式</span><span class="sxs-lookup"><span data-stu-id="25396-281">Expressions that are never null</span></span>

<span data-ttu-id="25396-282">以下表达式格式的 null 状态始终为 "not null"：</span><span class="sxs-lookup"><span data-stu-id="25396-282">The null state of the following expression forms is always "not null":</span></span>

- <span data-ttu-id="25396-283">`this` access</span><span class="sxs-lookup"><span data-stu-id="25396-283">`this` access</span></span>
- <span data-ttu-id="25396-284">内插字符串</span><span class="sxs-lookup"><span data-stu-id="25396-284">interpolated strings</span></span>
- <span data-ttu-id="25396-285">`new` 表达式 (对象、委托、匿名对象和数组创建表达式) </span><span class="sxs-lookup"><span data-stu-id="25396-285">`new` expressions (object, delegate, anonymous object and array creation expressions)</span></span>
- <span data-ttu-id="25396-286">`typeof` 表达式</span><span class="sxs-lookup"><span data-stu-id="25396-286">`typeof` expressions</span></span>
- <span data-ttu-id="25396-287">`nameof` 表达式</span><span class="sxs-lookup"><span data-stu-id="25396-287">`nameof` expressions</span></span>
- <span data-ttu-id="25396-288">匿名函数 (匿名方法和 lambda 表达式) </span><span class="sxs-lookup"><span data-stu-id="25396-288">anonymous functions (anonymous methods and lambda expressions)</span></span>
- <span data-ttu-id="25396-289">包容性表达式</span><span class="sxs-lookup"><span data-stu-id="25396-289">null-forgiving expressions</span></span>
- <span data-ttu-id="25396-290">`is` 表达式</span><span class="sxs-lookup"><span data-stu-id="25396-290">`is` expressions</span></span>

### <a name="nested-functions"></a><span data-ttu-id="25396-291">嵌套函数</span><span class="sxs-lookup"><span data-stu-id="25396-291">Nested functions</span></span>

<span data-ttu-id="25396-292">嵌套函数 (lambda 和局部函数) 被视为方法，但其捕获变量除外。</span><span class="sxs-lookup"><span data-stu-id="25396-292">Nested functions (lambdas and local functions) are treated like methods, except in regards to their captured variables.</span></span>
<span data-ttu-id="25396-293">Lambda 或局部函数内捕获的变量的初始状态是该嵌套函数或 lambda 的所有 "使用" 中的变量的可以为 null 的状态的交集。</span><span class="sxs-lookup"><span data-stu-id="25396-293">The initial state of a captured variable inside a lambda or local function is the intersection of the nullable state of the variable at all the "uses" of that nested function or lambda.</span></span> <span data-ttu-id="25396-294">本地函数的使用是对该函数的调用，或将其转换为委托的位置。</span><span class="sxs-lookup"><span data-stu-id="25396-294">A use of a local function is either a call to that function, or where it is converted to a delegate.</span></span> <span data-ttu-id="25396-295">Lambda 的使用是在源中定义它的点。</span><span class="sxs-lookup"><span data-stu-id="25396-295">A use of a lambda is the point at which it is defined in source.</span></span>

## <a name="type-inference"></a><span data-ttu-id="25396-296">类型推理</span><span class="sxs-lookup"><span data-stu-id="25396-296">Type inference</span></span>

### <a name="nullable-implicitly-typed-local-variables"></a><span data-ttu-id="25396-297">可以为 null 的隐式类型的局部变量</span><span class="sxs-lookup"><span data-stu-id="25396-297">nullable implicitly typed local variables</span></span>

<span data-ttu-id="25396-298">`var` 推理引用类型的批注类型和不被约束为值类型的类型参数。</span><span class="sxs-lookup"><span data-stu-id="25396-298">`var` infers an annotated type for reference types, and type parameters that aren't constrained to be a value type.</span></span>
<span data-ttu-id="25396-299">例如：</span><span class="sxs-lookup"><span data-stu-id="25396-299">For instance:</span></span>
- <span data-ttu-id="25396-300">在中， `var s = "";` `var` 将推断为 `string?` 。</span><span class="sxs-lookup"><span data-stu-id="25396-300">in `var s = "";` the `var` is inferred as `string?`.</span></span>
- <span data-ttu-id="25396-301">在中， `var t = new T();` `T` 不受约束的将 `var` 推断为 `T?` 。</span><span class="sxs-lookup"><span data-stu-id="25396-301">in `var t = new T();` with an unconstrained `T` the `var` is inferred as `T?`.</span></span>

### <a name="generic-type-inference"></a><span data-ttu-id="25396-302">泛型类型推理</span><span class="sxs-lookup"><span data-stu-id="25396-302">Generic type inference</span></span>

<span data-ttu-id="25396-303">泛型类型推理经过了增强，可帮助确定推断的引用类型是否可以为 null。</span><span class="sxs-lookup"><span data-stu-id="25396-303">Generic type inference is enhanced to help decide whether inferred reference types should be nullable or not.</span></span> <span data-ttu-id="25396-304">这是一个最大努力。</span><span class="sxs-lookup"><span data-stu-id="25396-304">This is a best effort.</span></span> <span data-ttu-id="25396-305">它可能会生成有关空性约束的警告，并且在将所选重载的推断类型应用于参数时，可能会导致可以为 null 的警告。</span><span class="sxs-lookup"><span data-stu-id="25396-305">It may yield warnings regarding nullability constraints, and may lead to nullable warnings when the inferred types of the selected overload are applied to the arguments.</span></span>

### <a name="the-first-phase"></a><span data-ttu-id="25396-306">第一阶段</span><span class="sxs-lookup"><span data-stu-id="25396-306">The first phase</span></span>

<span data-ttu-id="25396-307">可以为 null 的引用类型从初始表达式流入边界，如下所述。</span><span class="sxs-lookup"><span data-stu-id="25396-307">Nullable reference types flow into the bounds from the initial expressions, as described below.</span></span> <span data-ttu-id="25396-308">此外，引入了两种新的界限，即 `null` 和 `default` 。</span><span class="sxs-lookup"><span data-stu-id="25396-308">In addition, two new kinds of bounds, namely `null` and `default` are introduced.</span></span> <span data-ttu-id="25396-309">其目的是在输入表达式中执行 `null` 或 `default` ，这可能会导致推断出的类型可以为 null，即使在其他情况下也是如此。</span><span class="sxs-lookup"><span data-stu-id="25396-309">Their purpose is to carry through occurrences of `null` or `default` in the input expressions, which may cause an inferred type to be nullable, even when it otherwise wouldn't.</span></span> <span data-ttu-id="25396-310">这甚至适用于可为 null 的值类型，这些 *值* 类型得到了增强，可在推断过程中选取 "非 null"。</span><span class="sxs-lookup"><span data-stu-id="25396-310">This works even for nullable *value* types, which are enhanced to pick up "nullness" in the inference process.</span></span>

<span data-ttu-id="25396-311">在第一阶段中，确定要添加的界限如下：</span><span class="sxs-lookup"><span data-stu-id="25396-311">The determination of what bounds to add in the first phase are enhanced as follows:</span></span>

<span data-ttu-id="25396-312">如果参数 `Ei` 具有引用类型，则 `U` 用于推理的类型取决于的 null 状态及其 `Ei` 声明的类型：</span><span class="sxs-lookup"><span data-stu-id="25396-312">If an argument `Ei` has a reference type, the type `U` used for inference depends on the null state of `Ei` as well as its declared type:</span></span>
- <span data-ttu-id="25396-313">如果声明的类型是不可 null 引用类型 `U0` 或可以为 null 的引用类型， `U0?` 则</span><span class="sxs-lookup"><span data-stu-id="25396-313">If the declared type is a nonnullable reference type `U0` or a nullable reference type `U0?` then</span></span>
    - <span data-ttu-id="25396-314">如果的 null 状态 `Ei` 为 "not null"，则 `U` 为 `U0`</span><span class="sxs-lookup"><span data-stu-id="25396-314">if the null state of `Ei` is "not null" then `U` is `U0`</span></span>
    - <span data-ttu-id="25396-315">如果的 null 状态 `Ei` 为 "可能为 null"，则 `U` 为 `U0?`</span><span class="sxs-lookup"><span data-stu-id="25396-315">if the null state of `Ei` is "maybe null" then `U` is `U0?`</span></span>
- <span data-ttu-id="25396-316">否则 `Ei` ，如果具有已声明的类型， `U` 则为该类型</span><span class="sxs-lookup"><span data-stu-id="25396-316">Otherwise if `Ei` has a declared type, `U` is that type</span></span>
- <span data-ttu-id="25396-317">否则，如果为，则 `Ei` `null` `U` 为特殊界限 `null`</span><span class="sxs-lookup"><span data-stu-id="25396-317">Otherwise if `Ei` is `null` then `U` is the special bound `null`</span></span>
- <span data-ttu-id="25396-318">否则，如果为，则 `Ei` `default` `U` 为特殊界限 `default`</span><span class="sxs-lookup"><span data-stu-id="25396-318">Otherwise if `Ei` is `default` then `U` is the special bound `default`</span></span>
- <span data-ttu-id="25396-319">否则，不进行推理。</span><span class="sxs-lookup"><span data-stu-id="25396-319">Otherwise no inference is made.</span></span>

### <a name="exact-upper-bound-and-lower-bound-inferences"></a><span data-ttu-id="25396-320">精确、上限和下限推理</span><span class="sxs-lookup"><span data-stu-id="25396-320">Exact, upper-bound and lower-bound inferences</span></span>

<span data-ttu-id="25396-321">在 *从* 类型推断 `U` *为* 类型时 `V` ，如果 `V` 是可以为 null 的引用类型，则将在 `V0?` `V0` `V` 以下子句中使用而不是。</span><span class="sxs-lookup"><span data-stu-id="25396-321">In inferences *from* the type `U` *to* the type `V`, if `V` is a nullable reference type `V0?`, then `V0` is used instead of `V` in the following clauses.</span></span>
- <span data-ttu-id="25396-322">如果 `V` 是未固定的类型变量中的一个， `U` 则会像以前一样，添加一个精确、上下限或下限</span><span class="sxs-lookup"><span data-stu-id="25396-322">If `V` is one of the unfixed type variables, `U` is added as an exact, upper or lower bound as before</span></span>
- <span data-ttu-id="25396-323">否则，如果 `U` 为 `null` 或 `default` ，则不进行推理</span><span class="sxs-lookup"><span data-stu-id="25396-323">Otherwise, if `U` is `null` or `default`, no inference is made</span></span>
- <span data-ttu-id="25396-324">否则，如果 `U` 是可以为 null 的引用类型 `U0?` ，则 `U0` 将在 `U` 后续子句中使用而不是。</span><span class="sxs-lookup"><span data-stu-id="25396-324">Otherwise, if `U` is a nullable reference type `U0?`, then `U0` is used instead of `U` in the subsequent clauses.</span></span>

<span data-ttu-id="25396-325">实质上，与某个未固定的类型变量直接相关的可为 null 将保留在其边界内。</span><span class="sxs-lookup"><span data-stu-id="25396-325">The essence is that nullability that pertains directly to one of the unfixed type variables is preserved into its bounds.</span></span> <span data-ttu-id="25396-326">另一方面，如果推断将进一步递归到源和目标类型，则会忽略为空性。</span><span class="sxs-lookup"><span data-stu-id="25396-326">For the inferences that recurse further into the source and target types, on the other hand, nullability is ignored.</span></span> <span data-ttu-id="25396-327">它可以或不匹配，但如果不匹配，则会在以后选择并应用重载时发出警告。</span><span class="sxs-lookup"><span data-stu-id="25396-327">It may or may not match, but if it doesn't, a warning will be issued later if the overload is chosen and applied.</span></span>

### <a name="fixing"></a><span data-ttu-id="25396-328">修正 </span><span class="sxs-lookup"><span data-stu-id="25396-328">Fixing</span></span>

<span data-ttu-id="25396-329">如果多个边界彼此之间相互转换，但不同，则该规范并不是一种很好的描述。</span><span class="sxs-lookup"><span data-stu-id="25396-329">The spec currently does not do a good job of describing what happens when multiple bounds are identity convertible to each other, but are different.</span></span> <span data-ttu-id="25396-330">这种情况可能发生 `object` 在与之间， `dynamic` 在不同于元素名称的元组类型之间、构造它们的类型之间以及 `C` `C?` 引用类型中的类型之间。</span><span class="sxs-lookup"><span data-stu-id="25396-330">This may happen between `object` and `dynamic`, between tuple types that differ only in element names, between types constructed thereof and now also between `C` and `C?` for reference types.</span></span>

<span data-ttu-id="25396-331">此外，我们还需要将 "非 null" 从输入表达式传播到结果类型。</span><span class="sxs-lookup"><span data-stu-id="25396-331">In addition we need to propagate "nullness" from the input expressions to the result type.</span></span>

<span data-ttu-id="25396-332">为了应对这些情况，我们添加了更多的修复阶段，这现在是：</span><span class="sxs-lookup"><span data-stu-id="25396-332">To handle these we add more phases to fixing, which is now:</span></span>

1. <span data-ttu-id="25396-333">将所有边界中的所有类型都作为候选项收集， `?` 从所有可为 null 的引用类型中移除</span><span class="sxs-lookup"><span data-stu-id="25396-333">Gather all the types in all the bounds as candidates, removing `?` from all that are nullable reference types</span></span>
2. <span data-ttu-id="25396-334">根据 (保持和限制) 的精确、下限和上限的要求，消除候选项 `null` `default`</span><span class="sxs-lookup"><span data-stu-id="25396-334">Eliminate candidates based on requirements of exact, lower and upper bounds (keeping `null` and `default` bounds)</span></span>
3. <span data-ttu-id="25396-335">消除没有隐式转换为所有其他候选项的候选项</span><span class="sxs-lookup"><span data-stu-id="25396-335">Eliminate candidates that do not have an implicit conversion to all the other candidates</span></span>
4. <span data-ttu-id="25396-336">如果剩余的候选项彼此之间没有标识转换，则类型推理将失败</span><span class="sxs-lookup"><span data-stu-id="25396-336">If the remaining candidates do not all have identity conversions to one another, then type inference fails</span></span>
5. <span data-ttu-id="25396-337">按如下所述 *合并* 剩余候选项</span><span class="sxs-lookup"><span data-stu-id="25396-337">*Merge* the remaining candidates as described below</span></span>
6. <span data-ttu-id="25396-338">如果生成的候选项是引用类型或不可 null 值类型，并且 *所有* 完全 *限定或下限* 都是可以为 null 的值类型、可以为 null 的引用类型 `null` 或 `default` ，则 `?` 将其添加到生成的候选项，使其成为可以为 null 的值类型或引用类型。</span><span class="sxs-lookup"><span data-stu-id="25396-338">If the resulting candidate is a reference type or a nonnullable value type and *all* of the exact bounds or *any* of the lower bounds are nullable value types, nullable reference types, `null` or `default`, then `?` is added to the resulting candidate, making it a nullable value type or reference type.</span></span>

<span data-ttu-id="25396-339">两个候选类型之间介绍了 *合并*。</span><span class="sxs-lookup"><span data-stu-id="25396-339">*Merging* is described between two candidate types.</span></span> <span data-ttu-id="25396-340">它是可传递和可交换的，因此，可按任何顺序将候选项合并，并获得相同的最终结果。</span><span class="sxs-lookup"><span data-stu-id="25396-340">It is transitive and commutative, so the candidates can be merged in any order with the same ultimate result.</span></span> <span data-ttu-id="25396-341">如果两个候选类型不能相互转换，则它是不确定的。</span><span class="sxs-lookup"><span data-stu-id="25396-341">It is undefined if the two candidate types are not identity convertible to each other.</span></span>

<span data-ttu-id="25396-342">*Merge* 函数采用两种候选类型和方向 (*+* 或 *-*) ：</span><span class="sxs-lookup"><span data-stu-id="25396-342">The *Merge* function takes two candidate types and a direction (*+* or *-*):</span></span>

- <span data-ttu-id="25396-343">*Merge* (`T` ， `T` ， *d*) = T</span><span class="sxs-lookup"><span data-stu-id="25396-343">*Merge*(`T`, `T`, *d*) = T</span></span>
- <span data-ttu-id="25396-344">*Merge* (`S` ， `T?` ， *+*) = *merge* (`S?` ， `T` ， *+*) = *merge* (`S` ， `T` ， *+*) `?`</span><span class="sxs-lookup"><span data-stu-id="25396-344">*Merge*(`S`, `T?`, *+*) = *Merge*(`S?`, `T`, *+*) = *Merge*(`S`, `T`, *+*)`?`</span></span>
- <span data-ttu-id="25396-345">*Merge* (`S` ， `T?` ， *-*) = *merge* (`S?` ， `T` ， *-*) = *merge* (`S` ， `T` ， *-*) </span><span class="sxs-lookup"><span data-stu-id="25396-345">*Merge*(`S`, `T?`, *-*) = *Merge*(`S?`, `T`, *-*) = *Merge*(`S`, `T`, *-*)</span></span>
- <span data-ttu-id="25396-346">*Merge* (`C<S1,...,Sn>` ， `C<T1,...,Tn>` ， *+*) = `C<` *merge* (`S1` ， `T1` ， *d1*) `,...,` *merge* (`Sn` ， `Tn` ， *dn*) `>` ，*其中*</span><span class="sxs-lookup"><span data-stu-id="25396-346">*Merge*(`C<S1,...,Sn>`, `C<T1,...,Tn>`, *+*) = `C<`*Merge*(`S1`, `T1`, *d1*)`,...,`*Merge*(`Sn`, `Tn`, *dn*)`>`, *where*</span></span>
    - <span data-ttu-id="25396-347">`di` = *+* 如果 `i` 的第一个类型参数 `C<...>` 是协变的</span><span class="sxs-lookup"><span data-stu-id="25396-347">`di` = *+* if the `i`'th type parameter of `C<...>` is covariant</span></span>
    - <span data-ttu-id="25396-348">`di` = *-* 如果的 `i` 第一个类型参数 `C<...>` 为逆变或固定</span><span class="sxs-lookup"><span data-stu-id="25396-348">`di` = *-* if the `i`'th type parameter of `C<...>` is contra- or invariant</span></span>
- <span data-ttu-id="25396-349">*Merge* (`C<S1,...,Sn>` ， `C<T1,...,Tn>` ， *-*) = `C<` *merge* (`S1` ， `T1` ， *d1*) `,...,` *merge* (`Sn` ， `Tn` ， *dn*) `>` ，*其中*</span><span class="sxs-lookup"><span data-stu-id="25396-349">*Merge*(`C<S1,...,Sn>`, `C<T1,...,Tn>`, *-*) = `C<`*Merge*(`S1`, `T1`, *d1*)`,...,`*Merge*(`Sn`, `Tn`, *dn*)`>`, *where*</span></span>
    - <span data-ttu-id="25396-350">`di` = *-* 如果 `i` 的第一个类型参数 `C<...>` 是协变的</span><span class="sxs-lookup"><span data-stu-id="25396-350">`di` = *-* if the `i`'th type parameter of `C<...>` is covariant</span></span>
    - <span data-ttu-id="25396-351">`di` = *+* 如果的 `i` 第一个类型参数 `C<...>` 为逆变或固定</span><span class="sxs-lookup"><span data-stu-id="25396-351">`di` = *+* if the `i`'th type parameter of `C<...>` is contra- or invariant</span></span>
- <span data-ttu-id="25396-352">*Merge* (`(S1 s1,..., Sn sn)` ， `(T1 t1,..., Tn tn)` ， *d*) = `(` *merge* (`S1` ， `T1` ， *d*) `n1,...,` *合并* (`Sn` ， `Tn` ， *d*) `nn)` ，*其中*</span><span class="sxs-lookup"><span data-stu-id="25396-352">*Merge*(`(S1 s1,..., Sn sn)`, `(T1 t1,..., Tn tn)`, *d*) = `(`*Merge*(`S1`, `T1`, *d*)`n1,...,`*Merge*(`Sn`, `Tn`, *d*) `nn)`, *where*</span></span>
    - <span data-ttu-id="25396-353">`ni` 如果 `si` 和 `ti` 不同，或者两者都不存在，则不存在</span><span class="sxs-lookup"><span data-stu-id="25396-353">`ni` is absent if `si` and `ti` differ, or if both are absent</span></span>
    - <span data-ttu-id="25396-354">`ni` 是 `si` `si` 和 `ti` 是否相同</span><span class="sxs-lookup"><span data-stu-id="25396-354">`ni` is `si` if `si` and `ti` are the same</span></span>
- <span data-ttu-id="25396-355">*Merge* (`object` ， `dynamic`) = *merge* (`dynamic` ， `object`) = `dynamic`</span><span class="sxs-lookup"><span data-stu-id="25396-355">*Merge*(`object`, `dynamic`) = *Merge*(`dynamic`, `object`) = `dynamic`</span></span>

## <a name="warnings"></a><span data-ttu-id="25396-356">警告</span><span class="sxs-lookup"><span data-stu-id="25396-356">Warnings</span></span>

### <a name="potential-null-assignment"></a><span data-ttu-id="25396-357">潜在 null 赋值</span><span class="sxs-lookup"><span data-stu-id="25396-357">Potential null assignment</span></span>

### <a name="potential-null-dereference"></a><span data-ttu-id="25396-358">潜在的空取消引用</span><span class="sxs-lookup"><span data-stu-id="25396-358">Potential null dereference</span></span>

### <a name="constraint-nullability-mismatch"></a><span data-ttu-id="25396-359">约束为空性不匹配</span><span class="sxs-lookup"><span data-stu-id="25396-359">Constraint nullability mismatch</span></span>

### <a name="nullable-types-in-disabled-annotation-context"></a><span data-ttu-id="25396-360">禁用的注释上下文中可以为 null 的类型</span><span class="sxs-lookup"><span data-stu-id="25396-360">Nullable types in disabled annotation context</span></span>

### <a name="override-and-implementation-nullability-mismatch"></a><span data-ttu-id="25396-361">重写和实现为空性不匹配</span><span class="sxs-lookup"><span data-stu-id="25396-361">Override and implementation nullability mismatch</span></span>

## <a name="attributes-for-special-null-behavior"></a><span data-ttu-id="25396-362">特殊 null 行为的特性</span><span class="sxs-lookup"><span data-stu-id="25396-362">Attributes for special null behavior</span></span>


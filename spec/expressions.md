---
ms.openlocfilehash: c1e8b38ddc75227afe4677f03ee4df97586f6705
ms.sourcegitcommit: f921de697a44d3ae827f168a8371d7dc3ca66f2d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/02/2021
ms.locfileid: "101642007"
---
# <a name="expressions"></a><span data-ttu-id="2e3bd-101">表达式</span><span class="sxs-lookup"><span data-stu-id="2e3bd-101">Expressions</span></span>

<span data-ttu-id="2e3bd-102">表达式是运算符和操作数构成的序列。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-102">An expression is a sequence of operators and operands.</span></span> <span data-ttu-id="2e3bd-103">本章定义语法、操作数和运算符的计算顺序，以及表达式的含义。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-103">This chapter defines the syntax, order of evaluation of operands and operators, and meaning of expressions.</span></span>

## <a name="expression-classifications"></a><span data-ttu-id="2e3bd-104">表达式分类</span><span class="sxs-lookup"><span data-stu-id="2e3bd-104">Expression classifications</span></span>

<span data-ttu-id="2e3bd-105">表达式分类为以下类别之一：</span><span class="sxs-lookup"><span data-stu-id="2e3bd-105">An expression is classified as one of the following:</span></span>

*  <span data-ttu-id="2e3bd-106">一个 值。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-106">A value.</span></span> <span data-ttu-id="2e3bd-107">每个值都有关联的类型。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-107">Every value has an associated type.</span></span>
*  <span data-ttu-id="2e3bd-108">一个变量。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-108">A variable.</span></span> <span data-ttu-id="2e3bd-109">每个变量都有关联的类型，即变量的声明类型。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-109">Every variable has an associated type, namely the declared type of the variable.</span></span>
*  <span data-ttu-id="2e3bd-110">一个命名空间。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-110">A namespace.</span></span> <span data-ttu-id="2e3bd-111">具有此分类的表达式只能作为 *member_access* ([成员访问](expressions.md#member-access)) 的左侧出现。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-111">An expression with this classification can only appear as the left hand side of a *member_access* ([Member access](expressions.md#member-access)).</span></span> <span data-ttu-id="2e3bd-112">在任何其他上下文中，归类为命名空间的表达式会导致编译时错误。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-112">In any other context, an expression classified as a namespace causes a compile-time error.</span></span>
*  <span data-ttu-id="2e3bd-113">一种类型。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-113">A type.</span></span> <span data-ttu-id="2e3bd-114">具有此分类的表达式只能作为 *member_access* ([成员访问](expressions.md#member-access)) 的左侧，或者作为运算符的操作数 `as` ([为](expressions.md#the-as-operator)) ， `is` 运算符 ([is operator](expressions.md#the-is-operator)) ，或 (`typeof` [typeof 运算符](expressions.md#the-typeof-operator)) 的运算符。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-114">An expression with this classification can only appear as the left hand side of a *member_access* ([Member access](expressions.md#member-access)), or as an operand for the `as` operator ([The as operator](expressions.md#the-as-operator)), the `is` operator ([The is operator](expressions.md#the-is-operator)), or the `typeof` operator ([The typeof operator](expressions.md#the-typeof-operator)).</span></span> <span data-ttu-id="2e3bd-115">在其他任何上下文中，归类为类型的表达式会导致编译时错误。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-115">In any other context, an expression classified as a type causes a compile-time error.</span></span>
*  <span data-ttu-id="2e3bd-116">一个方法组，它是从成员查找 ([成员查找](expressions.md#member-lookup)) 导致的一组重载方法。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-116">A method group, which is a set of overloaded methods resulting from a member lookup ([Member lookup](expressions.md#member-lookup)).</span></span> <span data-ttu-id="2e3bd-117">方法组可以有一个关联的实例表达式和一个关联的类型参数列表。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-117">A method group may have an associated instance expression and an associated type argument list.</span></span> <span data-ttu-id="2e3bd-118">调用实例方法时，实例表达式的计算结果将成为 `this` ([此访问](expressions.md#this-access)) 所表示的实例。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-118">When an instance method is invoked, the result of evaluating the instance expression becomes the instance represented by `this` ([This access](expressions.md#this-access)).</span></span> <span data-ttu-id="2e3bd-119">) *invocation_expression* ([调用表达式](expressions.md#invocation-expressions) 中允许使用方法组， *delegate_creation_expression* ([委托创建表达式](expressions.md#delegate-creation-expressions)) 并将其隐式转换为 ([方法组转换](conversions.md#method-group-conversions)) 的兼容委托类型。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-119">A method group is permitted in an *invocation_expression* ([Invocation expressions](expressions.md#invocation-expressions)) , a *delegate_creation_expression* ([Delegate creation expressions](expressions.md#delegate-creation-expressions)) and as the left hand side of an is operator, and can be implicitly converted to a compatible delegate type ([Method group conversions](conversions.md#method-group-conversions)).</span></span> <span data-ttu-id="2e3bd-120">在其他任何上下文中，归类为方法组的表达式会导致编译时错误。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-120">In any other context, an expression classified as a method group causes a compile-time error.</span></span>
*  <span data-ttu-id="2e3bd-121">空文本。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-121">A null literal.</span></span> <span data-ttu-id="2e3bd-122">具有此分类的表达式可隐式转换为引用类型或可以为 null 的类型。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-122">An expression with this classification can be implicitly converted to a reference type or nullable type.</span></span>
*  <span data-ttu-id="2e3bd-123">匿名函数。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-123">An anonymous function.</span></span> <span data-ttu-id="2e3bd-124">具有此分类的表达式可隐式转换为兼容的委托类型或表达式目录树类型。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-124">An expression with this classification can be implicitly converted to a compatible delegate type or expression tree type.</span></span>
*  <span data-ttu-id="2e3bd-125">属性访问。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-125">A property access.</span></span> <span data-ttu-id="2e3bd-126">每个属性访问都有关联的类型，即属性的类型。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-126">Every property access has an associated type, namely the type of the property.</span></span> <span data-ttu-id="2e3bd-127">而且，属性访问可能具有关联的实例表达式。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-127">Furthermore, a property access may have an associated instance expression.</span></span> <span data-ttu-id="2e3bd-128">当访问器 (`get` `set` 调用实例属性访问的或块) 时，实例表达式的计算结果将成为 `this` ([此访问](expressions.md#this-access)) 所表示的实例。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-128">When an accessor (the `get` or `set` block) of an instance property access is invoked, the result of evaluating the instance expression becomes the instance represented by `this` ([This access](expressions.md#this-access)).</span></span>
*  <span data-ttu-id="2e3bd-129">事件访问。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-129">An event access.</span></span> <span data-ttu-id="2e3bd-130">每个事件访问都有关联的类型，即事件的类型。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-130">Every event access has an associated type, namely the type of the event.</span></span> <span data-ttu-id="2e3bd-131">而且，事件访问可能具有关联的实例表达式。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-131">Furthermore, an event access may have an associated instance expression.</span></span> <span data-ttu-id="2e3bd-132">事件访问可能显示为和运算符的左操作数 `+=` ， `-=` ([事件分配](expressions.md#event-assignment)) 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-132">An event access may appear as the left hand operand of the `+=` and `-=` operators ([Event assignment](expressions.md#event-assignment)).</span></span> <span data-ttu-id="2e3bd-133">在其他任何上下文中，归类为事件访问的表达式会导致编译时错误。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-133">In any other context, an expression classified as an event access causes a compile-time error.</span></span>
*  <span data-ttu-id="2e3bd-134">索引器访问。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-134">An indexer access.</span></span> <span data-ttu-id="2e3bd-135">每个索引器访问都有关联的类型，即索引器的元素类型。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-135">Every indexer access has an associated type, namely the element type of the indexer.</span></span> <span data-ttu-id="2e3bd-136">此外，索引器访问具有关联的实例表达式和关联的参数列表。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-136">Furthermore, an indexer access has an associated instance expression and an associated argument list.</span></span> <span data-ttu-id="2e3bd-137">如果调用器 (`get` `set` 调用索引器访问的或块) ，则实例表达式的计算结果将成为 `this` ([此访问](expressions.md#this-access)) 所表示的实例，而参数列表的计算结果将成为调用的参数列表。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-137">When an accessor (the `get` or `set` block) of an indexer access is invoked, the result of evaluating the instance expression becomes the instance represented by `this` ([This access](expressions.md#this-access)), and the result of evaluating the argument list becomes the parameter list of the invocation.</span></span>
*  <span data-ttu-id="2e3bd-138">无变化。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-138">Nothing.</span></span> <span data-ttu-id="2e3bd-139">当表达式是返回类型为的方法的调用时，会发生这种情况 `void` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-139">This occurs when the expression is an invocation of a method with a return type of `void`.</span></span> <span data-ttu-id="2e3bd-140">归类为 nothing 的表达式仅在) *statement_expression* ([expression 语句](statements.md#expression-statements) 的上下文中有效。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-140">An expression classified as nothing is only valid in the context of a *statement_expression* ([Expression statements](statements.md#expression-statements)).</span></span>

<span data-ttu-id="2e3bd-141">表达式的最终结果绝不会是命名空间、类型、方法组或事件访问。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-141">The final result of an expression is never a namespace, type, method group, or event access.</span></span> <span data-ttu-id="2e3bd-142">相反，如上所述，这些类别的表达式是仅在某些上下文中允许的中间构造。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-142">Rather, as noted above, these categories of expressions are intermediate constructs that are only permitted in certain contexts.</span></span>

<span data-ttu-id="2e3bd-143">通过执行 *get 访问器* 或 *set 访问器* 的调用，始终可以将属性访问或索引器访问重新分类为值。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-143">A property access or indexer access is always reclassified as a value by performing an invocation of the *get accessor* or the *set accessor*.</span></span> <span data-ttu-id="2e3bd-144">特定访问器由属性或索引器访问的上下文确定：如果访问是赋值的目标，则调用 *set 访问器* 来 ([简单的赋值](expressions.md#simple-assignment)) 分配新值。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-144">The particular accessor is determined by the context of the property or indexer access: If the access is the target of an assignment, the *set accessor* is invoked to assign a new value ([Simple assignment](expressions.md#simple-assignment)).</span></span> <span data-ttu-id="2e3bd-145">否则，将调用 *get 访问器* 来获取 () [的表达式值的](expressions.md#values-of-expressions) 当前值。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-145">Otherwise, the *get accessor* is invoked to obtain the current value ([Values of expressions](expressions.md#values-of-expressions)).</span></span>

### <a name="values-of-expressions"></a><span data-ttu-id="2e3bd-146">表达式的值</span><span class="sxs-lookup"><span data-stu-id="2e3bd-146">Values of expressions</span></span>

<span data-ttu-id="2e3bd-147">大多数涉及表达式的构造都需要表达式来表示 ***值***。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-147">Most of the constructs that involve an expression ultimately require the expression to denote a ***value***.</span></span> <span data-ttu-id="2e3bd-148">在这种情况下，如果实际表达式表示命名空间、类型、方法组或不执行任何操作，则会发生编译时错误。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-148">In such cases, if the actual expression denotes a namespace, a type, a method group, or nothing, a compile-time error occurs.</span></span> <span data-ttu-id="2e3bd-149">但是，如果表达式表示属性访问、索引器访问或变量，则将隐式替换属性、索引器或变量的值：</span><span class="sxs-lookup"><span data-stu-id="2e3bd-149">However, if the expression denotes a property access, an indexer access, or a variable, the value of the property, indexer, or variable is implicitly substituted:</span></span>

*  <span data-ttu-id="2e3bd-150">变量的值只是当前存储在变量标识的存储位置中的值。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-150">The value of a variable is simply the value currently stored in the storage location identified by the variable.</span></span> <span data-ttu-id="2e3bd-151">在获取变量的值之前，必须将其视为明确赋值 ([明确赋值](variables.md#definite-assignment)) ，否则将发生编译时错误。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-151">A variable must be considered definitely assigned ([Definite assignment](variables.md#definite-assignment)) before its value can be obtained, or otherwise a compile-time error occurs.</span></span>
*  <span data-ttu-id="2e3bd-152">属性访问表达式的值是通过调用属性的 *get 访问器* 获取的。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-152">The value of a property access expression is obtained by invoking the *get accessor* of the property.</span></span> <span data-ttu-id="2e3bd-153">如果该属性没有 *get 访问器*，则会发生编译时错误。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-153">If the property has no *get accessor*, a compile-time error occurs.</span></span> <span data-ttu-id="2e3bd-154">否则，函数成员调用 (对 [动态重载解析) 执行编译时检查](expressions.md#compile-time-checking-of-dynamic-overload-resolution) ，并且调用的结果将成为属性访问表达式的值。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-154">Otherwise, a function member invocation ([Compile-time checking of dynamic overload resolution](expressions.md#compile-time-checking-of-dynamic-overload-resolution)) is performed, and the result of the invocation becomes the value of the property access expression.</span></span>
*  <span data-ttu-id="2e3bd-155">索引器访问表达式的值是通过调用索引器的 *get 访问器* 获取的。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-155">The value of an indexer access expression is obtained by invoking the *get accessor* of the indexer.</span></span> <span data-ttu-id="2e3bd-156">如果索引器没有 *get 访问器*，则会发生编译时错误。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-156">If the indexer has no *get accessor*, a compile-time error occurs.</span></span> <span data-ttu-id="2e3bd-157">否则，函数成员调用 (对 [动态重载解析](expressions.md#compile-time-checking-of-dynamic-overload-resolution)) 使用与索引器访问表达式关联的参数列表执行的编译时检查，并且调用的结果成为索引器访问表达式的值。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-157">Otherwise, a function member invocation ([Compile-time checking of dynamic overload resolution](expressions.md#compile-time-checking-of-dynamic-overload-resolution)) is performed with the argument list associated with the indexer access expression, and the result of the invocation becomes the value of the indexer access expression.</span></span>

## <a name="static-and-dynamic-binding"></a><span data-ttu-id="2e3bd-158">静态和动态绑定</span><span class="sxs-lookup"><span data-stu-id="2e3bd-158">Static and Dynamic Binding</span></span>

<span data-ttu-id="2e3bd-159">根据构成表达式的类型或值 (参数、操作数、接收方) ，确定操作含义的过程通常称为 " ***绑定***"。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-159">The process of determining the meaning of an operation based on the type or value of constituent expressions (arguments, operands, receivers) is often referred to as ***binding***.</span></span> <span data-ttu-id="2e3bd-160">例如，方法调用的含义是根据接收方和参数的类型确定的。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-160">For instance the meaning of a method call is determined based on the type of the receiver and arguments.</span></span> <span data-ttu-id="2e3bd-161">运算符的含义取决于其操作数的类型。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-161">The meaning of an operator is determined based on the type of its operands.</span></span>

<span data-ttu-id="2e3bd-162">在 c # 中，操作的含义通常基于其构成表达式的编译时类型在编译时确定。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-162">In C# the meaning of an operation is usually determined at compile-time, based on the compile-time type of its constituent expressions.</span></span> <span data-ttu-id="2e3bd-163">同样，如果表达式包含错误，编译器将检测并报告错误。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-163">Likewise, if an expression contains an error, the error is detected and reported by the compiler.</span></span> <span data-ttu-id="2e3bd-164">此方法称为 ***静态绑定***。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-164">This approach is known as ***static binding***.</span></span>

<span data-ttu-id="2e3bd-165">但是，如果表达式是动态表达式 (即) 的类型， `dynamic` 则指示它参与的任何绑定都应基于其运行时类型 (也就是说，它在运行) 时表示的对象的实际类型，而不是在编译时所指定的类型。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-165">However, if an expression is a dynamic expression (i.e. has the type `dynamic`) this indicates that any binding that it participates in should be based on its run-time type (i.e. the actual type of the object it denotes at run-time) rather than the type it has at compile-time.</span></span> <span data-ttu-id="2e3bd-166">这样，此类操作的绑定就会推迟到运行该程序的时间。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-166">The binding of such an operation is therefore deferred until the time where the operation is to be executed during the running of the program.</span></span> <span data-ttu-id="2e3bd-167">这称为 ***动态绑定***。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-167">This is referred to as ***dynamic binding***.</span></span>

<span data-ttu-id="2e3bd-168">当动态绑定操作时，编译器不会执行任何检查。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-168">When an operation is dynamically bound, little or no checking is performed by the compiler.</span></span> <span data-ttu-id="2e3bd-169">相反，如果运行时绑定失败，则在运行时将错误报告为异常。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-169">Instead if the run-time binding fails, errors are reported as exceptions at run-time.</span></span>

<span data-ttu-id="2e3bd-170">C # 中的以下操作服从绑定：</span><span class="sxs-lookup"><span data-stu-id="2e3bd-170">The following operations in C# are subject to binding:</span></span>

*  <span data-ttu-id="2e3bd-171">成员访问： `e.M`</span><span class="sxs-lookup"><span data-stu-id="2e3bd-171">Member access: `e.M`</span></span>
*  <span data-ttu-id="2e3bd-172">方法调用： `e.M(e1, ..., eN)`</span><span class="sxs-lookup"><span data-stu-id="2e3bd-172">Method invocation: `e.M(e1, ..., eN)`</span></span>
*  <span data-ttu-id="2e3bd-173">委托调用：`e(e1, ..., eN)`</span><span class="sxs-lookup"><span data-stu-id="2e3bd-173">Delegate invocation:`e(e1, ..., eN)`</span></span>
*  <span data-ttu-id="2e3bd-174">元素访问： `e[e1, ..., eN]`</span><span class="sxs-lookup"><span data-stu-id="2e3bd-174">Element access: `e[e1, ..., eN]`</span></span>
*  <span data-ttu-id="2e3bd-175">对象创建： `new C(e1, ..., eN)`</span><span class="sxs-lookup"><span data-stu-id="2e3bd-175">Object creation: `new C(e1, ..., eN)`</span></span>
*  <span data-ttu-id="2e3bd-176">重载的一元运算符： `+` 、 `-` 、 `!` 、 `~` 、 `++` 、 `--` `true` 、、 `false`</span><span class="sxs-lookup"><span data-stu-id="2e3bd-176">Overloaded unary operators: `+`, `-`, `!`, `~`, `++`, `--`, `true`, `false`</span></span>
*  <span data-ttu-id="2e3bd-177">重载的二元运算符： `+` 、 `-` 、 `*` 、 `/` 、 `%` 、 `&` `&&` `|` `||` `??` `^` `<<` `>>` `==` `!=` `>` `<` `>=` 、、、、、、、、、、、、、 `<=`</span><span class="sxs-lookup"><span data-stu-id="2e3bd-177">Overloaded binary operators: `+`, `-`, `*`, `/`, `%`, `&`, `&&`, `|`, `||`, `??`, `^`, `<<`, `>>`, `==`,`!=`, `>`, `<`, `>=`, `<=`</span></span>
*  <span data-ttu-id="2e3bd-178">赋值运算符： `=` 、 `+=` 、 `-=` 、 `*=` 、 `/=` 、 `%=` `&=` `|=` `^=` `<<=` 、、、、、 `>>=`</span><span class="sxs-lookup"><span data-stu-id="2e3bd-178">Assignment operators: `=`, `+=`, `-=`, `*=`, `/=`, `%=`, `&=`, `|=`, `^=`, `<<=`, `>>=`</span></span>
*  <span data-ttu-id="2e3bd-179">隐式和显式转换</span><span class="sxs-lookup"><span data-stu-id="2e3bd-179">Implicit and explicit conversions</span></span>

<span data-ttu-id="2e3bd-180">当不涉及动态表达式时，c # 默认为静态绑定，这意味着在选择过程中使用构成表达式的编译时类型。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-180">When no dynamic expressions are involved, C# defaults to static binding, which means that the compile-time types of constituent expressions are used in the selection process.</span></span> <span data-ttu-id="2e3bd-181">但是，当上面列出的操作中的其中一个构成表达式为动态表达式时，将改为动态绑定该操作。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-181">However, when one of the constituent expressions in the operations listed above is a dynamic expression, the operation is instead dynamically bound.</span></span>

### <a name="binding-time"></a><span data-ttu-id="2e3bd-182">绑定时间</span><span class="sxs-lookup"><span data-stu-id="2e3bd-182">Binding-time</span></span>

<span data-ttu-id="2e3bd-183">在编译时进行静态绑定，而动态绑定在运行时发生。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-183">Static binding takes place at compile-time, whereas dynamic binding takes place at run-time.</span></span> <span data-ttu-id="2e3bd-184">在下面几节中，术语 " ***绑定时间*** " 指编译时或运行时，具体取决于绑定发生的时间。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-184">In the following sections, the term ***binding-time*** refers to either compile-time or run-time, depending on when the binding takes place.</span></span>

<span data-ttu-id="2e3bd-185">下面的示例演示了静态和动态绑定的概念以及绑定时间：</span><span class="sxs-lookup"><span data-stu-id="2e3bd-185">The following example illustrates the notions of static and dynamic binding and of binding-time:</span></span>
```csharp
object  o = 5;
dynamic d = 5;

Console.WriteLine(5);  // static  binding to Console.WriteLine(int)
Console.WriteLine(o);  // static  binding to Console.WriteLine(object)
Console.WriteLine(d);  // dynamic binding to Console.WriteLine(int)
```

<span data-ttu-id="2e3bd-186">前两个调用是静态绑定的： `Console.WriteLine` 根据其自变量的编译时类型选取重载。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-186">The first two calls are statically bound: the overload of `Console.WriteLine` is picked based on the compile-time type of their argument.</span></span> <span data-ttu-id="2e3bd-187">因此，绑定时间为编译时。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-187">Thus, the binding-time is compile-time.</span></span>

<span data-ttu-id="2e3bd-188">第三个调用是动态绑定的： `Console.WriteLine` 根据其自变量的运行时类型选取重载。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-188">The third call is dynamically bound: the overload of `Console.WriteLine` is picked based on the run-time type of its argument.</span></span> <span data-ttu-id="2e3bd-189">出现这种情况的原因是，参数是动态表达式，其编译时类型为 `dynamic` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-189">This happens because the argument is a dynamic expression -- its compile-time type is `dynamic`.</span></span> <span data-ttu-id="2e3bd-190">因此，第三次调用的绑定时间是运行时。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-190">Thus, the binding-time for the third call is run-time.</span></span>

### <a name="dynamic-binding"></a><span data-ttu-id="2e3bd-191">动态绑定</span><span class="sxs-lookup"><span data-stu-id="2e3bd-191">Dynamic binding</span></span>

<span data-ttu-id="2e3bd-192">动态绑定的目的是允许 c # 程序与 ***动态对象*** 进行交互，即不遵循 c # 类型系统的常规规则的对象。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-192">The purpose of dynamic binding is to allow C# programs to interact with ***dynamic objects***, i.e. objects that do not follow the normal rules of the C# type system.</span></span> <span data-ttu-id="2e3bd-193">动态对象可以是具有不同类型系统的其他编程语言中的对象，也可以是以编程方式设置以实现不同操作的绑定语义的对象。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-193">Dynamic objects may be objects from other programming languages with different types systems, or they may be objects that are programmatically setup to implement their own binding semantics for different operations.</span></span>

<span data-ttu-id="2e3bd-194">动态对象实现其自身语义的机制是定义的实现。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-194">The mechanism by which a dynamic object implements its own semantics is implementation defined.</span></span> <span data-ttu-id="2e3bd-195">定义的给定接口再次实现--由动态对象实现，以向 c # 运行时发出信号，指示它们具有特殊语义。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-195">A given interface -- again implementation defined -- is implemented by dynamic objects to signal to the C# run-time that they have special semantics.</span></span> <span data-ttu-id="2e3bd-196">因此，无论何时动态地对动态对象的操作进行动态绑定，它们自己的绑定语义，而不是本文档中指定的 c # 的操作，都需要接管。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-196">Thus, whenever operations on a dynamic object are dynamically bound, their own binding semantics, rather than those of C# as specified in this document, take over.</span></span>

<span data-ttu-id="2e3bd-197">尽管动态绑定的目的是允许与动态对象进行互操作，但 c # 允许动态绑定所有对象，无论它们是否为动态的。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-197">While the purpose of dynamic binding is to allow interoperation with dynamic objects, C# allows dynamic binding on all objects, whether they are dynamic or not.</span></span> <span data-ttu-id="2e3bd-198">这允许动态对象的更平滑集成，因为它们的操作结果可能不是动态对象，而是在编译时对程序员来说是未知类型。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-198">This allows for a smoother integration of dynamic objects, as the results of operations on them may not themselves be dynamic objects, but are still of a type unknown to the programmer at compile-time.</span></span> <span data-ttu-id="2e3bd-199">此外，动态绑定还有助于消除容易出错的基于反射的代码，即使在没有任何对象是动态对象时也是如此。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-199">Also dynamic binding can help eliminate error-prone reflection-based code even when no objects involved are dynamic objects.</span></span>

<span data-ttu-id="2e3bd-200">以下各节描述了在应用动态绑定时与语言中的每个构造完全相同的内容、所应用的编译时检查（如果有），以及编译时结果和表达式分类的定义。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-200">The following sections describe for each construct in the language exactly when dynamic binding is applied, what compile time checking -- if any -- is applied, and what the compile-time result and expression classification is.</span></span>

### <a name="types-of-constituent-expressions"></a><span data-ttu-id="2e3bd-201">构成表达式的类型</span><span class="sxs-lookup"><span data-stu-id="2e3bd-201">Types of constituent expressions</span></span>

<span data-ttu-id="2e3bd-202">静态绑定操作时，构成表达式的类型 (例如，接收方、参数、索引或操作数) 始终被认为是该表达式的编译时类型。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-202">When an operation is statically bound, the type of a constituent expression (e.g. a receiver, an argument, an index or an operand) is always considered to be the compile-time type of that expression.</span></span>

<span data-ttu-id="2e3bd-203">动态绑定操作时，将根据构成表达式的编译时类型以不同的方式确定构成表达式的类型：</span><span class="sxs-lookup"><span data-stu-id="2e3bd-203">When an operation is dynamically bound, the type of a constituent expression is determined in different ways depending on the compile-time type of the constituent expression:</span></span>

*  <span data-ttu-id="2e3bd-204">编译时类型的构成表达式被 `dynamic` 视为在运行时计算表达式计算结果的实际值的类型</span><span class="sxs-lookup"><span data-stu-id="2e3bd-204">A constituent expression of compile-time type `dynamic` is considered to have the type of the actual value that the expression evaluates to at runtime</span></span>
*  <span data-ttu-id="2e3bd-205">在运行时将其编译时类型为类型参数的构成表达式视为具有类型参数绑定到的类型</span><span class="sxs-lookup"><span data-stu-id="2e3bd-205">A constituent expression whose compile-time type is a type parameter is considered to have the type which the type parameter is bound to at runtime</span></span>
*  <span data-ttu-id="2e3bd-206">否则，构成表达式被视为具有其编译时类型。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-206">Otherwise the constituent expression is considered to have its compile-time type.</span></span>

## <a name="operators"></a><span data-ttu-id="2e3bd-207">运算符</span><span class="sxs-lookup"><span data-stu-id="2e3bd-207">Operators</span></span>

<span data-ttu-id="2e3bd-208">表达式是从 ***操作数** _ 和 _*_运算符_\*_ 构造而来的。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-208">Expressions are constructed from ***operands** _ and _*_operators_\*_.</span></span> <span data-ttu-id="2e3bd-209">表达式的运算符指明了向操作数应用的运算。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-209">The operators of an expression indicate which operations to apply to the operands.</span></span> <span data-ttu-id="2e3bd-210">运算符的示例包括 `+`、`-`、`_`、`/` 和 `new`。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-210">Examples of operators include `+`, `-`, `_`, `/`, and `new`.</span></span> <span data-ttu-id="2e3bd-211">操作数的示例包括文本、字段、局部变量和表达式。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-211">Examples of operands include literals, fields, local variables, and expressions.</span></span>

<span data-ttu-id="2e3bd-212">有三种类型的运算符：</span><span class="sxs-lookup"><span data-stu-id="2e3bd-212">There are three kinds of operators:</span></span>

*  <span data-ttu-id="2e3bd-213">一元运算符。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-213">Unary operators.</span></span> <span data-ttu-id="2e3bd-214">一元运算符采用一个操作数，并使用前缀表示法 (例如 `--x`) 或后缀表示法 (如 `x++`) 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-214">The unary operators take one operand and use either prefix notation (such as `--x`) or postfix notation (such as `x++`).</span></span>
*  <span data-ttu-id="2e3bd-215">二元运算符。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-215">Binary operators.</span></span> <span data-ttu-id="2e3bd-216">二元运算符采用两个操作数，并使用中缀表示法 (如 `x + y`) 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-216">The binary operators take two operands and all use infix notation (such as `x + y`).</span></span>
*  <span data-ttu-id="2e3bd-217">三元运算符。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-217">Ternary operator.</span></span> <span data-ttu-id="2e3bd-218">只有一个三元运算符， `?:` exists; 它采用三个操作数，并使用中缀符号 (`c ? x : y`) 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-218">Only one ternary operator, `?:`, exists; it takes three operands and uses infix notation (`c ? x : y`).</span></span>

<span data-ttu-id="2e3bd-219">表达式中运算符的计算顺序由运算符的 ***优先级** _ 和 _ *_相关性_** 决定 ([运算符优先级和关联](expressions.md#operator-precedence-and-associativity) 性) 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-219">The order of evaluation of operators in an expression is determined by the ***precedence** _ and _ *_associativity_** of the operators ([Operator precedence and associativity](expressions.md#operator-precedence-and-associativity)).</span></span>

<span data-ttu-id="2e3bd-220">表达式中的操作数从左到右进行计算。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-220">Operands in an expression are evaluated from left to right.</span></span> <span data-ttu-id="2e3bd-221">例如，在中 `F(i) + G(i++) * H(i)` ， `F` 使用旧值调用方法 `i` ，然后使用 `G` 旧值调用方法 `i` ，最后 `H` 使用的新值调用方法 `i` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-221">For example, in `F(i) + G(i++) * H(i)`, method `F` is called using the old value of `i`, then method `G` is called with the old value of `i`, and, finally, method `H` is called with the new value of `i`.</span></span> <span data-ttu-id="2e3bd-222">这与运算符优先级不同。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-222">This is separate from and unrelated to operator precedence.</span></span>

<span data-ttu-id="2e3bd-223">特定的运算符可 ***重载***。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-223">Certain operators can be ***overloaded***.</span></span> <span data-ttu-id="2e3bd-224">运算符重载允许为操作指定用户定义的运算符实现，其中一个或两个操作数属于用户定义的类或结构类型 ([运算符重载](expressions.md#operator-overloading)) 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-224">Operator overloading permits user-defined operator implementations to be specified for operations where one or both of the operands are of a user-defined class or struct type ([Operator overloading](expressions.md#operator-overloading)).</span></span>

### <a name="operator-precedence-and-associativity"></a><span data-ttu-id="2e3bd-225">运算符优先级和关联性</span><span class="sxs-lookup"><span data-stu-id="2e3bd-225">Operator precedence and associativity</span></span>

<span data-ttu-id="2e3bd-226">如果表达式包含多个运算符，则运算符的 \***优先级** _ 控制各个运算符的计算顺序。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-226">When an expression contains multiple operators, the \***precedence** _ of the operators controls the order in which the individual operators are evaluated.</span></span> <span data-ttu-id="2e3bd-227">例如，表达式 `x + y _ z` 的计算结果为 `x + (y * z)`，因为 `*` 运算符的优先级高于二进制 `+` 运算符。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-227">For example, the expression `x + y _ z` is evaluated as `x + (y * z)` because the `*` operator has higher precedence than the binary `+` operator.</span></span> <span data-ttu-id="2e3bd-228">运算符的优先级由其关联的文法产生式的定义来确定。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-228">The precedence of an operator is established by the definition of its associated grammar production.</span></span> <span data-ttu-id="2e3bd-229">例如， *additive_expression* 由或运算符分隔的一系列 *multiplicative_expression* 组成 `+` `-` ，从而使 `+` 和 `-` 运算符的优先级低于 `*` 、 `/` 和 `%` 运算符。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-229">For example, an *additive_expression* consists of a sequence of *multiplicative_expression* s separated by `+` or `-` operators, thus giving the `+` and `-` operators lower precedence than the `*`, `/`, and `%` operators.</span></span>

<span data-ttu-id="2e3bd-230">下表按优先级从高到低的顺序汇总了所有运算符：</span><span class="sxs-lookup"><span data-stu-id="2e3bd-230">The following table summarizes all operators in order of precedence from highest to lowest:</span></span>

| <span data-ttu-id="2e3bd-231">__节__</span><span class="sxs-lookup"><span data-stu-id="2e3bd-231">__Section__</span></span>                                                                                   | <span data-ttu-id="2e3bd-232">__类别__</span><span class="sxs-lookup"><span data-stu-id="2e3bd-232">__Category__</span></span>                | <span data-ttu-id="2e3bd-233">__运算符__</span><span class="sxs-lookup"><span data-stu-id="2e3bd-233">__Operators__</span></span> | 
|-----------------------------------------------------------------------------------------------|-----------------------------|---------------|
| [<span data-ttu-id="2e3bd-234">主要表达式</span><span class="sxs-lookup"><span data-stu-id="2e3bd-234">Primary expressions</span></span>](expressions.md#primary-expressions)                                     | <span data-ttu-id="2e3bd-235">主要</span><span class="sxs-lookup"><span data-stu-id="2e3bd-235">Primary</span></span>                     | <span data-ttu-id="2e3bd-236">`x.y`  `f(x)`  `a[x]`  `x++`  `x--`  `new`  `typeof`  `default`  `checked`  `unchecked`  `delegate`</span><span class="sxs-lookup"><span data-stu-id="2e3bd-236">`x.y`  `f(x)`  `a[x]`  `x++`  `x--`  `new`  `typeof`  `default`  `checked`  `unchecked`  `delegate`</span></span> | 
| [<span data-ttu-id="2e3bd-237">一元运算符</span><span class="sxs-lookup"><span data-stu-id="2e3bd-237">Unary operators</span></span>](expressions.md#unary-operators)                                             | <span data-ttu-id="2e3bd-238">一元</span><span class="sxs-lookup"><span data-stu-id="2e3bd-238">Unary</span></span>                       | <span data-ttu-id="2e3bd-239">`+`  `-`  `!`  `~`  `++x`  `--x`  `(T)x`</span><span class="sxs-lookup"><span data-stu-id="2e3bd-239">`+`  `-`  `!`  `~`  `++x`  `--x`  `(T)x`</span></span> | 
| [<span data-ttu-id="2e3bd-240">算术运算符</span><span class="sxs-lookup"><span data-stu-id="2e3bd-240">Arithmetic operators</span></span>](expressions.md#arithmetic-operators)                                   | <span data-ttu-id="2e3bd-241">乘法性的</span><span class="sxs-lookup"><span data-stu-id="2e3bd-241">Multiplicative</span></span>              | <span data-ttu-id="2e3bd-242">`*`  `/`  `%`</span><span class="sxs-lookup"><span data-stu-id="2e3bd-242">`*`  `/`  `%`</span></span> | 
| [<span data-ttu-id="2e3bd-243">算术运算符</span><span class="sxs-lookup"><span data-stu-id="2e3bd-243">Arithmetic operators</span></span>](expressions.md#arithmetic-operators)                                   | <span data-ttu-id="2e3bd-244">累加性</span><span class="sxs-lookup"><span data-stu-id="2e3bd-244">Additive</span></span>                    | <span data-ttu-id="2e3bd-245">`+`  `-`</span><span class="sxs-lookup"><span data-stu-id="2e3bd-245">`+`  `-`</span></span>      | 
| [<span data-ttu-id="2e3bd-246">移位运算符</span><span class="sxs-lookup"><span data-stu-id="2e3bd-246">Shift operators</span></span>](expressions.md#shift-operators)                                             | <span data-ttu-id="2e3bd-247">移位</span><span class="sxs-lookup"><span data-stu-id="2e3bd-247">Shift</span></span>                       | <span data-ttu-id="2e3bd-248">`<<`  `>>`</span><span class="sxs-lookup"><span data-stu-id="2e3bd-248">`<<`  `>>`</span></span>    | 
| [<span data-ttu-id="2e3bd-249">关系和类型测试运算符</span><span class="sxs-lookup"><span data-stu-id="2e3bd-249">Relational and type-testing operators</span></span>](expressions.md#relational-and-type-testing-operators) | <span data-ttu-id="2e3bd-250">关系和类型测试</span><span class="sxs-lookup"><span data-stu-id="2e3bd-250">Relational and type testing</span></span> | <span data-ttu-id="2e3bd-251">`<`  `>`  `<=`  `>=`  `is`  `as`</span><span class="sxs-lookup"><span data-stu-id="2e3bd-251">`<`  `>`  `<=`  `>=`  `is`  `as`</span></span> | 
| [<span data-ttu-id="2e3bd-252">关系和类型测试运算符</span><span class="sxs-lookup"><span data-stu-id="2e3bd-252">Relational and type-testing operators</span></span>](expressions.md#relational-and-type-testing-operators) | <span data-ttu-id="2e3bd-253">等式</span><span class="sxs-lookup"><span data-stu-id="2e3bd-253">Equality</span></span>                    | <span data-ttu-id="2e3bd-254">`==`  `!=`</span><span class="sxs-lookup"><span data-stu-id="2e3bd-254">`==`  `!=`</span></span>    | 
| [<span data-ttu-id="2e3bd-255">逻辑运算符</span><span class="sxs-lookup"><span data-stu-id="2e3bd-255">Logical operators</span></span>](expressions.md#logical-operators)                                         | <span data-ttu-id="2e3bd-256">逻辑与</span><span class="sxs-lookup"><span data-stu-id="2e3bd-256">Logical AND</span></span>                 | `&`           | 
| [<span data-ttu-id="2e3bd-257">逻辑运算符</span><span class="sxs-lookup"><span data-stu-id="2e3bd-257">Logical operators</span></span>](expressions.md#logical-operators)                                         | <span data-ttu-id="2e3bd-258">逻辑 XOR</span><span class="sxs-lookup"><span data-stu-id="2e3bd-258">Logical XOR</span></span>                 | `^`           | 
| [<span data-ttu-id="2e3bd-259">逻辑运算符</span><span class="sxs-lookup"><span data-stu-id="2e3bd-259">Logical operators</span></span>](expressions.md#logical-operators)                                         | <span data-ttu-id="2e3bd-260">逻辑或</span><span class="sxs-lookup"><span data-stu-id="2e3bd-260">Logical OR</span></span>                  | <code>&#124;</code>           |
| [<span data-ttu-id="2e3bd-261">条件逻辑运算符</span><span class="sxs-lookup"><span data-stu-id="2e3bd-261">Conditional logical operators</span></span>](expressions.md#conditional-logical-operators)                 | <span data-ttu-id="2e3bd-262">条件“与”</span><span class="sxs-lookup"><span data-stu-id="2e3bd-262">Conditional AND</span></span>             | `&&`          | 
| [<span data-ttu-id="2e3bd-263">条件逻辑运算符</span><span class="sxs-lookup"><span data-stu-id="2e3bd-263">Conditional logical operators</span></span>](expressions.md#conditional-logical-operators)                 | <span data-ttu-id="2e3bd-264">条件“或”</span><span class="sxs-lookup"><span data-stu-id="2e3bd-264">Conditional OR</span></span>              | <code>&#124;&#124;</code>          | 
| [<span data-ttu-id="2e3bd-265">Null 合并运算符</span><span class="sxs-lookup"><span data-stu-id="2e3bd-265">The null coalescing operator</span></span>](expressions.md#the-null-coalescing-operator)                   | <span data-ttu-id="2e3bd-266">null 合并</span><span class="sxs-lookup"><span data-stu-id="2e3bd-266">Null coalescing</span></span>             | `??`          | 
| [<span data-ttu-id="2e3bd-267">条件运算符</span><span class="sxs-lookup"><span data-stu-id="2e3bd-267">Conditional operator</span></span>](expressions.md#conditional-operator)                                   | <span data-ttu-id="2e3bd-268">条件逻辑</span><span class="sxs-lookup"><span data-stu-id="2e3bd-268">Conditional</span></span>                 | `?:`          | 
| <span data-ttu-id="2e3bd-269">[赋值运算符](expressions.md#assignment-operators)， [匿名函数表达式](expressions.md#anonymous-function-expressions)</span><span class="sxs-lookup"><span data-stu-id="2e3bd-269">[Assignment operators](expressions.md#assignment-operators), [Anonymous function expressions](expressions.md#anonymous-function-expressions)</span></span>  | <span data-ttu-id="2e3bd-270">赋值和 lambda 表达式</span><span class="sxs-lookup"><span data-stu-id="2e3bd-270">Assignment and lambda expression</span></span> | <span data-ttu-id="2e3bd-271">`=`  `*=`  `/=`  `%=`  `+=`  `-=`  `<<=`  `>>=`  `&=`  `^=`  <code>&#124;=</code>  `=>`</span><span class="sxs-lookup"><span data-stu-id="2e3bd-271">`=`  `*=`  `/=`  `%=`  `+=`  `-=`  `<<=`  `>>=`  `&=`  `^=`  <code>&#124;=</code>  `=>`</span></span> | 

<span data-ttu-id="2e3bd-272">如果操作数两边的两个运算符的优先级相同，那么运算符的结合性决定了运算的执行顺序：</span><span class="sxs-lookup"><span data-stu-id="2e3bd-272">When an operand occurs between two operators with the same precedence, the associativity of the operators controls the order in which the operations are performed:</span></span>

*  <span data-ttu-id="2e3bd-273">除了赋值运算符和 null 合并运算符外，所有二元运算符都是 ***左结合*** 运算符，这意味着运算从左至右执行。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-273">Except for the assignment operators and the null coalescing operator, all binary operators are ***left-associative***, meaning that operations are performed from left to right.</span></span> <span data-ttu-id="2e3bd-274">例如，`x + y + z` 将计算为 `(x + y) + z`。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-274">For example, `x + y + z` is evaluated as `(x + y) + z`.</span></span>
*  <span data-ttu-id="2e3bd-275">赋值运算符、null 合并运算符和条件运算符 (`?:`) 是 ***右结合*** 运算符，这意味着将从右到左执行运算。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-275">The assignment operators, the null coalescing operator and the conditional operator (`?:`) are ***right-associative***, meaning that operations are performed from right to left.</span></span> <span data-ttu-id="2e3bd-276">例如，`x = y = z` 将计算为 `x = (y = z)`。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-276">For example, `x = y = z` is evaluated as `x = (y = z)`.</span></span>

<span data-ttu-id="2e3bd-277">可以使用括号控制优先级和结合性。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-277">Precedence and associativity can be controlled using parentheses.</span></span> <span data-ttu-id="2e3bd-278">例如，`x + y * z` 先计算 `y` 乘 `z`，并将结果与 `x` 相加，而 `(x + y) * z` 则先计算 `x` 加 `y`，然后将结果与 `z` 相乘。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-278">For example, `x + y * z` first multiplies `y` by `z` and then adds the result to `x`, but `(x + y) * z` first adds `x` and `y` and then multiplies the result by `z`.</span></span>

### <a name="operator-overloading"></a><span data-ttu-id="2e3bd-279">运算符重载</span><span class="sxs-lookup"><span data-stu-id="2e3bd-279">Operator overloading</span></span>

<span data-ttu-id="2e3bd-280">所有一元运算符和二元运算符都具有在任何表达式中自动可用的预定义实现。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-280">All unary and binary operators have predefined implementations that are automatically available in any expression.</span></span> <span data-ttu-id="2e3bd-281">除了预定义的实现外，还可以通过在类和结构中包含声明来引入用户定义的实现 `operator`)  ([运算符](classes.md#operators) 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-281">In addition to the predefined implementations, user-defined implementations can be introduced by including `operator` declarations in classes and structs ([Operators](classes.md#operators)).</span></span> <span data-ttu-id="2e3bd-282">用户定义的运算符实现始终优先于预定义运算符实现：只有当不存在适用的用户定义运算符实现时，才会考虑预定义运算符实现，如 [一元运算符重载决策](expressions.md#unary-operator-overload-resolution) 和 [二元运算符重载决策](expressions.md#binary-operator-overload-resolution)中所述。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-282">User-defined operator implementations always take precedence over predefined operator implementations: Only when no applicable user-defined operator implementations exist will the predefined operator implementations be considered, as described in [Unary operator overload resolution](expressions.md#unary-operator-overload-resolution) and [Binary operator overload resolution](expressions.md#binary-operator-overload-resolution).</span></span>

<span data-ttu-id="2e3bd-283">可 ***重载的一元运算符*** 是：</span><span class="sxs-lookup"><span data-stu-id="2e3bd-283">The ***overloadable unary operators*** are:</span></span>
```csharp
+   -   !   ~   ++   --   true   false
```

<span data-ttu-id="2e3bd-284">尽管 `true` 和不 `false` 是在表达式中显式使用的 (，因此不包括在 [运算符优先级和关联](expressions.md#operator-precedence-and-associativity) 性) 的优先级表中，因为它们是在多个表达式上下文中调用的：布尔表达式 ([布尔表达式](expressions.md#boolean-expressions)) 和表达式（涉及条件 ([条件运算符](expressions.md#conditional-operator)) ）和条件逻辑运算符 ([条件](expressions.md#conditional-logical-operators) 逻辑运算符) 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-284">Although `true` and `false` are not used explicitly in expressions (and therefore are not included in the precedence table in [Operator precedence and associativity](expressions.md#operator-precedence-and-associativity)), they are considered operators because they are invoked in several expression contexts: boolean expressions ([Boolean expressions](expressions.md#boolean-expressions)) and expressions involving the conditional ([Conditional operator](expressions.md#conditional-operator)), and conditional logical operators ([Conditional logical operators](expressions.md#conditional-logical-operators)).</span></span>

<span data-ttu-id="2e3bd-285">可 ***重载的二元运算符*** 是：</span><span class="sxs-lookup"><span data-stu-id="2e3bd-285">The ***overloadable binary operators*** are:</span></span>
```csharp
+   -   *   /   %   &   |   ^   <<   >>   ==   !=   >   <   >=   <=
```

<span data-ttu-id="2e3bd-286">只能重载上面列出的运算符。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-286">Only the operators listed above can be overloaded.</span></span> <span data-ttu-id="2e3bd-287">具体而言，无法重载成员访问、方法调用或、、、、、、、、、、、、、、、、、、、 `=` `&&` `||` `??` `?:` `=>` `checked` `unchecked` `new` `typeof` `default` `as` 和 `is` 运算符。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-287">In particular, it is not possible to overload member access, method invocation, or the `=`, `&&`, `||`, `??`, `?:`, `=>`, `checked`, `unchecked`, `new`, `typeof`, `default`, `as`, and `is` operators.</span></span>

<span data-ttu-id="2e3bd-288">重载二元运算符时，也会隐式重载相应的赋值运算符（若有）。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-288">When a binary operator is overloaded, the corresponding assignment operator, if any, is also implicitly overloaded.</span></span> <span data-ttu-id="2e3bd-289">例如，运算符的重载 `*` 也是运算符的重载 `*=` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-289">For example, an overload of operator `*` is also an overload of operator `*=`.</span></span> <span data-ttu-id="2e3bd-290">这将在 [复合赋值](expressions.md#compound-assignment)中进一步说明。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-290">This is described further in [Compound assignment](expressions.md#compound-assignment).</span></span> <span data-ttu-id="2e3bd-291">请注意，赋值运算符本身 (`=`) 无法重载。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-291">Note that the assignment operator itself (`=`) cannot be overloaded.</span></span> <span data-ttu-id="2e3bd-292">赋值始终将值的简单的逐位副本用于变量。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-292">An assignment always performs a simple bit-wise copy of a value into a variable.</span></span>

<span data-ttu-id="2e3bd-293">强制转换运算（如）是通过向用户定义的转换 `(T)x` 提供用户定义的转换来重载的)  ([用户定义的转换](conversions.md#user-defined-conversions) 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-293">Cast operations, such as `(T)x`, are overloaded by providing user-defined conversions ([User-defined conversions](conversions.md#user-defined-conversions)).</span></span>

<span data-ttu-id="2e3bd-294">元素访问（如 `a[x]` ）不被视为可重载的运算符。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-294">Element access, such as `a[x]`, is not considered an overloadable operator.</span></span> <span data-ttu-id="2e3bd-295">而是通过索引器 ([索引器](classes.md#indexers)) 来支持用户定义的索引。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-295">Instead, user-defined indexing is supported through indexers ([Indexers](classes.md#indexers)).</span></span>

<span data-ttu-id="2e3bd-296">在表达式中，运算符是使用运算符表示法引用的，而在声明中，使用函数表示法引用运算符。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-296">In expressions, operators are referenced using operator notation, and in declarations, operators are referenced using functional notation.</span></span> <span data-ttu-id="2e3bd-297">下表显示了一元运算符和二元运算符的运算符和函数表示法之间的关系。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-297">The following table shows the relationship between operator and functional notations for unary and binary operators.</span></span> <span data-ttu-id="2e3bd-298">在第一个条目中， *op* 表示任何可重载的一元前缀运算符。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-298">In the first entry, *op* denotes any overloadable unary prefix operator.</span></span> <span data-ttu-id="2e3bd-299">在第二个条目中， *op* 表示一元后缀 `++` 和 `--` 运算符。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-299">In the second entry, *op* denotes the unary postfix `++` and `--` operators.</span></span> <span data-ttu-id="2e3bd-300">在第三个条目中， *op* 表示任何可重载的二元运算符。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-300">In the third entry, *op* denotes any overloadable binary operator.</span></span>


| <span data-ttu-id="2e3bd-301">__运算符表示法__</span><span class="sxs-lookup"><span data-stu-id="2e3bd-301">__Operator notation__</span></span> | <span data-ttu-id="2e3bd-302">__函数表示法__</span><span class="sxs-lookup"><span data-stu-id="2e3bd-302">__Functional notation__</span></span> |
|-----------------------|-------------------------|
| `op x`                | `operator op(x)`        | 
| `x op`                | `operator op(x)`        | 
| `x op y`              | `operator op(x,y)`      | 

<span data-ttu-id="2e3bd-303">用户定义的运算符声明始终需要至少一个参数成为包含运算符声明的类或结构类型。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-303">User-defined operator declarations always require at least one of the parameters to be of the class or struct type that contains the operator declaration.</span></span> <span data-ttu-id="2e3bd-304">因此，用户定义的运算符不能与预定义的运算符具有相同的签名。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-304">Thus, it is not possible for a user-defined operator to have the same signature as a predefined operator.</span></span>

<span data-ttu-id="2e3bd-305">用户定义的运算符声明不能修改运算符的语法、优先级或关联性。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-305">User-defined operator declarations cannot modify the syntax, precedence, or associativity of an operator.</span></span> <span data-ttu-id="2e3bd-306">例如， `/` 运算符始终为二元运算符，始终具有在 [运算符优先级和关联](expressions.md#operator-precedence-and-associativity)性中指定的优先级别，并且始终为左结合。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-306">For example, the `/` operator is always a binary operator, always has the precedence level specified in [Operator precedence and associativity](expressions.md#operator-precedence-and-associativity), and is always left-associative.</span></span>

<span data-ttu-id="2e3bd-307">尽管用户定义的运算符可以执行 pleases 的任何计算，但强烈不建议使用生成的实现，而不是所需的结果。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-307">While it is possible for a user-defined operator to perform any computation it pleases, implementations that produce results other than those that are intuitively expected are strongly discouraged.</span></span> <span data-ttu-id="2e3bd-308">例如，的实现 `operator ==` 应比较两个操作数的相等性并返回相应的 `bool` 结果。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-308">For example, an implementation of `operator ==` should compare the two operands for equality and return an appropriate `bool` result.</span></span>

<span data-ttu-id="2e3bd-309">通过[条件逻辑运算符](expressions.md#conditional-logical-operators)对[主要表达式](expressions.md#primary-expressions)中的单个运算符进行的说明指定运算符的预定义实现以及适用于每个运算符的任何其他规则。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-309">The descriptions of individual operators in [Primary expressions](expressions.md#primary-expressions) through [Conditional logical operators](expressions.md#conditional-logical-operators) specify the predefined implementations of the operators and any additional rules that apply to each operator.</span></span> <span data-ttu-id="2e3bd-310">这些说明使用术语 ***一元运算符重载决策** _、 _*_二进制运算符重载决策_\*_ 和 _ *_数值升级_* \*，以下部分中提供了这些项的定义。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-310">The descriptions make use of the terms ***unary operator overload resolution** _, _*_binary operator overload resolution_*_, and _*_numeric promotion_\*\*, definitions of which are found in the following sections.</span></span>

### <a name="unary-operator-overload-resolution"></a><span data-ttu-id="2e3bd-311">一元运算符重载决策</span><span class="sxs-lookup"><span data-stu-id="2e3bd-311">Unary operator overload resolution</span></span>

<span data-ttu-id="2e3bd-312">格式为的操作 `op x` `x op` ，其中是可 `op` 重载的一元运算符， `x` 是类型的表达式， `X` 按如下方式进行处理：</span><span class="sxs-lookup"><span data-stu-id="2e3bd-312">An operation of the form `op x` or `x op`, where `op` is an overloadable unary operator, and `x` is an expression of type `X`, is processed as follows:</span></span>

*  <span data-ttu-id="2e3bd-313">为操作提供的候选用户定义运算符集 `X` `operator op(x)` 是使用 [候选用户定义运算符](expressions.md#candidate-user-defined-operators)的规则确定的。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-313">The set of candidate user-defined operators provided by `X` for the operation `operator op(x)` is determined using the rules of [Candidate user-defined operators](expressions.md#candidate-user-defined-operators).</span></span>
*  <span data-ttu-id="2e3bd-314">如果候选用户定义的运算符集不为空，则这将成为操作的候选运算符集。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-314">If the set of candidate user-defined operators is not empty, then this becomes the set of candidate operators for the operation.</span></span> <span data-ttu-id="2e3bd-315">否则，预定义的一元 `operator op` 实现（包括其提升形式）成为操作的候选运算符集。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-315">Otherwise, the predefined unary `operator op` implementations, including their lifted forms, become the set of candidate operators for the operation.</span></span> <span data-ttu-id="2e3bd-316">给定运算符的预定义实现在运算符的说明中指定 ([主表达式](expressions.md#primary-expressions) 和 [一元运算符](expressions.md#unary-operators)) 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-316">The predefined implementations of a given operator are specified in the description of the operator ([Primary expressions](expressions.md#primary-expressions) and [Unary operators](expressions.md#unary-operators)).</span></span>
*  <span data-ttu-id="2e3bd-317">[重载](expressions.md#overload-resolution)决策的重载决策规则应用于候选运算符集，以选择与参数列表相关的最佳运算符 `(x)` ，而此运算符将成为重载决策过程的结果。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-317">The overload resolution rules of [Overload resolution](expressions.md#overload-resolution) are applied to the set of candidate operators to select the best operator with respect to the argument list `(x)`, and this operator becomes the result of the overload resolution process.</span></span> <span data-ttu-id="2e3bd-318">如果重载决策未能选择单个最佳运算符，则会发生绑定时错误。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-318">If overload resolution fails to select a single best operator, a binding-time error occurs.</span></span>

### <a name="binary-operator-overload-resolution"></a><span data-ttu-id="2e3bd-319">二元运算符重载决策</span><span class="sxs-lookup"><span data-stu-id="2e3bd-319">Binary operator overload resolution</span></span>

<span data-ttu-id="2e3bd-320">格式为的操作 `x op y` （其中 `op` 是可重载的二元运算符） `x` 是类型的表达式， `X` `y` 是类型的表达式， `Y` 按如下方式进行处理：</span><span class="sxs-lookup"><span data-stu-id="2e3bd-320">An operation of the form `x op y`, where `op` is an overloadable binary operator, `x` is an expression of type `X`, and `y` is an expression of type `Y`, is processed as follows:</span></span>

*  <span data-ttu-id="2e3bd-321">确定为操作提供的候选用户定义运算符集 `X` `Y` `operator op(x,y)` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-321">The set of candidate user-defined operators provided by `X` and `Y` for the operation `operator op(x,y)` is determined.</span></span> <span data-ttu-id="2e3bd-322">集由提供的候选运算符 `X` 与提供的候选运算符联合组成 `Y` ，每个运算符都是使用 [候选用户定义的运算符](expressions.md#candidate-user-defined-operators)的规则确定的。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-322">The set consists of the union of the candidate operators provided by `X` and the candidate operators provided by `Y`, each determined using the rules of [Candidate user-defined operators](expressions.md#candidate-user-defined-operators).</span></span> <span data-ttu-id="2e3bd-323">如果 `X` 和 `Y` 是相同的类型，或者如果 `X` 和 `Y` 派生自公共基类型，则仅在组合集内发生一次共享的候选运算符。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-323">If `X` and `Y` are the same type, or if `X` and `Y` are derived from a common base type, then shared candidate operators only occur in the combined set once.</span></span>
*  <span data-ttu-id="2e3bd-324">如果候选用户定义的运算符集不为空，则这将成为操作的候选运算符集。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-324">If the set of candidate user-defined operators is not empty, then this becomes the set of candidate operators for the operation.</span></span> <span data-ttu-id="2e3bd-325">否则，预定义的二进制 `operator op` 实现（包括其提升形式）会成为操作的候选运算符集。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-325">Otherwise, the predefined binary `operator op` implementations, including their lifted forms,  become the set of candidate operators for the operation.</span></span> <span data-ttu-id="2e3bd-326">给定运算符的预定义实现通过[条件逻辑运算符](expressions.md#conditional-logical-operators))  ([算术运算符](expressions.md#arithmetic-operators)在运算符说明中指定。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-326">The predefined implementations of a given operator are specified in the description of the operator ([Arithmetic operators](expressions.md#arithmetic-operators) through [Conditional logical operators](expressions.md#conditional-logical-operators)).</span></span> <span data-ttu-id="2e3bd-327">对于预定义的枚举和委托运算符，唯一认为的运算符是由作为一个操作数的绑定时类型的枚举或委托类型定义的运算符。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-327">For predefined enum and delegate operators, the only operators considered are those defined by an enum or delegate type that is the binding-time type of one of the operands.</span></span>
*  <span data-ttu-id="2e3bd-328">[重载](expressions.md#overload-resolution)决策的重载决策规则应用于候选运算符集，以选择与参数列表相关的最佳运算符 `(x,y)` ，而此运算符将成为重载决策过程的结果。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-328">The overload resolution rules of [Overload resolution](expressions.md#overload-resolution) are applied to the set of candidate operators to select the best operator with respect to the argument list `(x,y)`, and this operator becomes the result of the overload resolution process.</span></span> <span data-ttu-id="2e3bd-329">如果重载决策未能选择单个最佳运算符，则会发生绑定时错误。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-329">If overload resolution fails to select a single best operator, a binding-time error occurs.</span></span>

### <a name="candidate-user-defined-operators"></a><span data-ttu-id="2e3bd-330">候选用户定义的运算符</span><span class="sxs-lookup"><span data-stu-id="2e3bd-330">Candidate user-defined operators</span></span>

<span data-ttu-id="2e3bd-331">给定类型 `T` 和操作 `operator op(A)` ，其中是可 `op` 重载的运算符并且 `A` 是自变量列表，由为提供的候选用户定义运算符集的 `T` `operator op(A)` 确定如下：</span><span class="sxs-lookup"><span data-stu-id="2e3bd-331">Given a type `T` and an operation `operator op(A)`, where `op` is an overloadable operator and `A` is an argument list, the set of candidate user-defined operators provided by `T` for `operator op(A)` is determined as follows:</span></span>

*  <span data-ttu-id="2e3bd-332">确定类型 `T0` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-332">Determine the type `T0`.</span></span> <span data-ttu-id="2e3bd-333">如果 `T` 是可以为 null 的类型， `T0` 则为其基础类型，否则 `T0` 等于 `T` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-333">If `T` is a nullable type, `T0` is its underlying type, otherwise `T0` is equal to `T`.</span></span>
*  <span data-ttu-id="2e3bd-334">对于 `operator op` `T0` 以及此类运算符的所有提升形式的所有声明，如果至少有一个运算符适用于与参数列表相关的 ([适用的函数成员](expressions.md#applicable-function-member)) `A` ，则候选运算符集包含中所有此类适用运算符 `T0` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-334">For all `operator op` declarations in `T0` and all lifted forms of such operators, if at least one operator is applicable ([Applicable function member](expressions.md#applicable-function-member)) with respect to the argument list `A`, then the set of candidate operators consists of all such applicable operators in `T0`.</span></span>
*  <span data-ttu-id="2e3bd-335">否则，如果 `T0` 为 `object` ，则候选运算符集为空。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-335">Otherwise, if `T0` is `object`, the set of candidate operators is empty.</span></span>
*  <span data-ttu-id="2e3bd-336">否则，由提供的候选运算符集 `T0` 是由的直接基类提供的候选运算符集 `T0` ， `T0` 如果 `T0` 是一个类型参数，则为的有效基类。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-336">Otherwise, the set of candidate operators provided by `T0` is the set of candidate operators provided by the direct base class of `T0`, or the effective base class of `T0` if `T0` is a type parameter.</span></span>

### <a name="numeric-promotions"></a><span data-ttu-id="2e3bd-337">数值提升</span><span class="sxs-lookup"><span data-stu-id="2e3bd-337">Numeric promotions</span></span>

<span data-ttu-id="2e3bd-338">数值提升包括自动执行预定义的一元运算符和二元数值运算符的操作数的某些隐式转换。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-338">Numeric promotion consists of automatically performing certain implicit conversions of the operands of the predefined unary and binary numeric operators.</span></span> <span data-ttu-id="2e3bd-339">数值升级不是一种不同的机制，而是将重载决策应用于预定义运算符的效果。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-339">Numeric promotion is not a distinct mechanism, but rather an effect of applying overload resolution to the predefined operators.</span></span> <span data-ttu-id="2e3bd-340">尽管可以实现用户定义的运算符来表现出类似的效果，但具体的数值升级却不会影响用户定义的运算符的计算。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-340">Numeric promotion specifically does not affect evaluation of user-defined operators, although user-defined operators can be implemented to exhibit similar effects.</span></span>

<span data-ttu-id="2e3bd-341">作为数值升级的一个示例，请考虑二元运算符的预定义实现 `*` ：</span><span class="sxs-lookup"><span data-stu-id="2e3bd-341">As an example of numeric promotion, consider the predefined implementations of the binary `*` operator:</span></span>

```csharp
int operator *(int x, int y);
uint operator *(uint x, uint y);
long operator *(long x, long y);
ulong operator *(ulong x, ulong y);
float operator *(float x, float y);
double operator *(double x, double y);
decimal operator *(decimal x, decimal y);
```

<span data-ttu-id="2e3bd-342">当重载决策规则 ([重载) 解析](expressions.md#overload-resolution) 规则应用于这组运算符时，将会影响从操作数类型中存在隐式转换的运算符。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-342">When overload resolution rules ([Overload resolution](expressions.md#overload-resolution)) are applied to this set of operators, the effect is to select the first of the operators for which implicit conversions exist from the operand types.</span></span> <span data-ttu-id="2e3bd-343">例如，对于操作 `b * s` （其中为， `b` `byte` 而 `s` 是 `short` ），重载决策选择 `operator *(int,int)` 作为最佳运算符。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-343">For example, for the operation `b * s`, where `b` is a `byte` and `s` is a `short`, overload resolution selects `operator *(int,int)` as the best operator.</span></span> <span data-ttu-id="2e3bd-344">因此，效果为 `b` `s` ，并转换为 `int` ，并且结果的类型为 `int` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-344">Thus, the effect is that `b` and `s` are converted to `int`, and the type of the result is `int`.</span></span> <span data-ttu-id="2e3bd-345">同样，对于操作 `i * d` （其中 `i` 是 `int` 和 `d` 是 `double` ），重载决策选择 `operator *(double,double)` 作为最佳运算符。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-345">Likewise, for the operation `i * d`, where `i` is an `int` and `d` is a `double`, overload resolution selects `operator *(double,double)` as the best operator.</span></span>

#### <a name="unary-numeric-promotions"></a><span data-ttu-id="2e3bd-346">一元数值促销</span><span class="sxs-lookup"><span data-stu-id="2e3bd-346">Unary numeric promotions</span></span>

<span data-ttu-id="2e3bd-347">一元数值升级针对预定义的 `+` 、 `-` 和一元运算符的操作数进行 `~` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-347">Unary numeric promotion occurs for the operands of the predefined `+`, `-`, and `~` unary operators.</span></span> <span data-ttu-id="2e3bd-348">一元数值提升只包含将类型为、、、或的操作数转换 `sbyte` `byte` `short` `ushort` `char` 为类型 `int` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-348">Unary numeric promotion simply consists of converting operands of type `sbyte`, `byte`, `short`, `ushort`, or `char` to type `int`.</span></span> <span data-ttu-id="2e3bd-349">此外，对于一元 `-` 运算符，一元数值提升将类型的操作数转换 `uint` 为类型 `long` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-349">Additionally, for the unary `-` operator, unary numeric promotion converts operands of type `uint` to type `long`.</span></span>

#### <a name="binary-numeric-promotions"></a><span data-ttu-id="2e3bd-350">二进制数值升级</span><span class="sxs-lookup"><span data-stu-id="2e3bd-350">Binary numeric promotions</span></span>

<span data-ttu-id="2e3bd-351">对于预定义的、、、、、、、、、、、、 `+` `-` `*` `/` `%` `&` `|` `^` `==` `!=` `>` `<` `>=` 和 `<=` 二元运算符的操作数，会发生二进制数值升级。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-351">Binary numeric promotion occurs for the operands of the predefined `+`, `-`, `*`, `/`, `%`, `&`, `|`, `^`, `==`, `!=`, `>`, `<`, `>=`, and `<=` binary operators.</span></span> <span data-ttu-id="2e3bd-352">二进制数值升级会隐式地将两个操作数转换为通用类型，在非关系运算符的情况下，也会成为运算的结果类型。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-352">Binary numeric promotion implicitly converts both operands to a common type which, in case of the non-relational operators, also becomes the result type of the operation.</span></span> <span data-ttu-id="2e3bd-353">二进制数值升级包括应用下列规则，顺序如下：</span><span class="sxs-lookup"><span data-stu-id="2e3bd-353">Binary numeric promotion consists of applying the following rules, in the order they appear here:</span></span>

*  <span data-ttu-id="2e3bd-354">如果任一操作数的类型为 `decimal` ，则将另一个操作数转换为类型 `decimal` ，或者，如果另一个操作数的类型为或，则会发生绑定时错误 `float` `double` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-354">If either operand is of type `decimal`, the other operand is converted to type `decimal`, or a binding-time error occurs if the other operand is of type `float` or `double`.</span></span>
*  <span data-ttu-id="2e3bd-355">否则，如果其中一个操作数为类型 `double` ，则另一个操作数将转换为类型 `double` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-355">Otherwise, if either operand is of type `double`, the other operand is converted to type `double`.</span></span>
*  <span data-ttu-id="2e3bd-356">否则，如果其中一个操作数为类型 `float` ，则另一个操作数将转换为类型 `float` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-356">Otherwise, if either operand is of type `float`, the other operand is converted to type `float`.</span></span>
*  <span data-ttu-id="2e3bd-357">否则，如果任一操作数的类型为 `ulong` ，则将另一个操作数转换为类型 `ulong` ，或者，如果另一个操作数的类型为 `sbyte` 、 `short` 、或，则会发生绑定时错误 `int` `long` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-357">Otherwise, if either operand is of type `ulong`, the other operand is converted to type `ulong`, or a binding-time error occurs if the other operand is of type `sbyte`, `short`, `int`, or `long`.</span></span>
*  <span data-ttu-id="2e3bd-358">否则，如果其中一个操作数为类型 `long` ，则另一个操作数将转换为类型 `long` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-358">Otherwise, if either operand is of type `long`, the other operand is converted to type `long`.</span></span>
*  <span data-ttu-id="2e3bd-359">否则，如果其中一个操作数的类型为 `uint` ，而另一个操作数的类型为 `sbyte` 、 `short` 或，则 `int` 这两个操作数都将转换为类型 `long` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-359">Otherwise, if either operand is of type `uint` and the other operand is of type `sbyte`, `short`, or `int`, both operands are converted to type `long`.</span></span>
*  <span data-ttu-id="2e3bd-360">否则，如果其中一个操作数为类型 `uint` ，则另一个操作数将转换为类型 `uint` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-360">Otherwise, if either operand is of type `uint`, the other operand is converted to type `uint`.</span></span>
*  <span data-ttu-id="2e3bd-361">否则，两个操作数都将转换为类型 `int` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-361">Otherwise, both operands are converted to type `int`.</span></span>

<span data-ttu-id="2e3bd-362">请注意，第一个规则不允许将 `decimal` 类型与和类型混合的任何操作 `double` `float` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-362">Note that the first rule disallows any operations that mix the `decimal` type with the `double` and `float` types.</span></span> <span data-ttu-id="2e3bd-363">下面的规则是因为类型与类型之间没有隐式转换 `decimal` `double` `float` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-363">The rule follows from the fact that there are no implicit conversions between the `decimal` type and the `double` and `float` types.</span></span>

<span data-ttu-id="2e3bd-364">另请注意， `ulong` 如果另一个操作数的类型为带符号的整数类型，则操作数不能是类型。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-364">Also note that it is not possible for an operand to be of type `ulong` when the other operand is of a signed integral type.</span></span> <span data-ttu-id="2e3bd-365">原因在于，不存在可以表示完整范围 `ulong` 和有符号整数类型的整数类型。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-365">The reason is that no integral type exists that can represent the full range of `ulong` as well as the signed integral types.</span></span>

<span data-ttu-id="2e3bd-366">在上述两种情况下，强制转换表达式可用于将一个操作数显式转换为与另一个操作数兼容的类型。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-366">In both of the above cases, a cast expression can be used to explicitly convert one operand to a type that is compatible with the other operand.</span></span>

<span data-ttu-id="2e3bd-367">示例中</span><span class="sxs-lookup"><span data-stu-id="2e3bd-367">In the example</span></span>
```csharp
decimal AddPercent(decimal x, double percent) {
    return x * (1.0 + percent / 100.0);
}
```
<span data-ttu-id="2e3bd-368">发生绑定时错误，因为 `decimal` 不能与进行相乘 `double` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-368">a binding-time error occurs because a `decimal` cannot be multiplied by a `double`.</span></span> <span data-ttu-id="2e3bd-369">通过将第二个操作数显式转换为，可以解决此错误 `decimal` ，如下所示：</span><span class="sxs-lookup"><span data-stu-id="2e3bd-369">The error is resolved by explicitly converting the second operand to `decimal`, as follows:</span></span>

```csharp
decimal AddPercent(decimal x, double percent) {
    return x * (decimal)(1.0 + percent / 100.0);
}
```

### <a name="lifted-operators"></a><span data-ttu-id="2e3bd-370">提升的运算符</span><span class="sxs-lookup"><span data-stu-id="2e3bd-370">Lifted operators</span></span>

<span data-ttu-id="2e3bd-371">***提升运算符*** 允许对不可以为 null 的值类型进行运算的预定义运算符和用户定义的运算符也可用于这些类型的可以为 null 的形式。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-371">***Lifted operators*** permit predefined and user-defined operators that operate on non-nullable value types to also be used with nullable forms of those types.</span></span> <span data-ttu-id="2e3bd-372">提升运算符是根据满足某些要求的预定义运算符和用户定义的运算符构造的，如下所述：</span><span class="sxs-lookup"><span data-stu-id="2e3bd-372">Lifted operators are constructed from predefined and user-defined operators that meet certain requirements, as described in the following:</span></span>

*   <span data-ttu-id="2e3bd-373">对于一元运算符</span><span class="sxs-lookup"><span data-stu-id="2e3bd-373">For the unary operators</span></span>

    ```csharp
    +  ++  -  --  !  ~
    ```

    <span data-ttu-id="2e3bd-374">如果操作数和结果类型都是不可为 null 的值类型，则会存在运算符的提升形式。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-374">a lifted form of an operator exists if the operand and result types are both non-nullable value types.</span></span> <span data-ttu-id="2e3bd-375">提升形式是通过向 `?` 操作数和结果类型添加单个修饰符来构造的。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-375">The lifted form is constructed by adding a single `?` modifier to the operand and result types.</span></span> <span data-ttu-id="2e3bd-376">如果操作数为 null，则提升运算符将生成 null 值。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-376">The lifted operator produces a null value if the operand is null.</span></span> <span data-ttu-id="2e3bd-377">否则，提升运算符将对操作数进行解包，应用基础运算符并包装结果。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-377">Otherwise, the lifted operator unwraps the operand, applies the underlying operator, and wraps the result.</span></span>

*   <span data-ttu-id="2e3bd-378">对于二元运算符</span><span class="sxs-lookup"><span data-stu-id="2e3bd-378">For the binary operators</span></span>

    ```csharp
    +  -  *  /  %  &  |  ^  <<  >>
    ```

    <span data-ttu-id="2e3bd-379">如果操作数和结果类型都是不可以为 null 的值类型，则该运算符的提升形式存在。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-379">a lifted form of an operator exists if the operand and result types are all non-nullable value types.</span></span> <span data-ttu-id="2e3bd-380">提升形式是通过 `?` 向每个操作数和结果类型添加单个修饰符来构造的。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-380">The lifted form is constructed by adding a single `?` modifier to each operand and result type.</span></span> <span data-ttu-id="2e3bd-381">如果一个或两个操作数为 null，则提升的运算符将生成 null 值 (异常是该 `&` 类型的和 `|` 运算符 `bool?` ，如) 的 [布尔逻辑运算符](expressions.md#boolean-logical-operators) 中所述。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-381">The lifted operator produces a null value if one or both operands are null (an exception being the `&` and `|` operators of the `bool?` type, as described in [Boolean logical operators](expressions.md#boolean-logical-operators)).</span></span> <span data-ttu-id="2e3bd-382">否则，提升运算符将对操作数进行解包，应用基础运算符并包装结果。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-382">Otherwise, the lifted operator unwraps the operands, applies the underlying operator, and wraps the result.</span></span>

*   <span data-ttu-id="2e3bd-383">对于相等运算符</span><span class="sxs-lookup"><span data-stu-id="2e3bd-383">For the equality operators</span></span>

    ```csharp
    ==  !=
    ```

    <span data-ttu-id="2e3bd-384">如果操作数类型都是不可为 null 的值类型，并且结果类型为，则会存在运算符的提升形式 `bool` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-384">a lifted form of an operator exists if the operand types are both non-nullable value types and if the result type is `bool`.</span></span> <span data-ttu-id="2e3bd-385">提升形式是通过 `?` 向每个操作数类型添加单个修饰符来构造的。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-385">The lifted form is constructed by adding a single `?` modifier to each operand type.</span></span> <span data-ttu-id="2e3bd-386">提升的运算符将两个 null 值视为相等，并将 null 值视为非 null 值。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-386">The lifted operator considers two null values equal, and a null value unequal to any non-null value.</span></span> <span data-ttu-id="2e3bd-387">如果两个操作数都非空，则提升运算符将对操作数进行解包，并应用基础运算符来生成 `bool` 结果。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-387">If both operands are non-null, the lifted operator unwraps the operands and applies the underlying operator to produce the `bool` result.</span></span>

*   <span data-ttu-id="2e3bd-388">对于关系运算符</span><span class="sxs-lookup"><span data-stu-id="2e3bd-388">For the relational operators</span></span>

    ```csharp
    <  >  <=  >=
    ```

    <span data-ttu-id="2e3bd-389">如果操作数类型都是不可为 null 的值类型，并且结果类型为，则会存在运算符的提升形式 `bool` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-389">a lifted form of an operator exists if the operand types are both non-nullable value types and if the result type is `bool`.</span></span> <span data-ttu-id="2e3bd-390">提升形式是通过 `?` 向每个操作数类型添加单个修饰符来构造的。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-390">The lifted form is constructed by adding a single `?` modifier to each operand type.</span></span> <span data-ttu-id="2e3bd-391">`false`如果一个或两个操作数为 null，则提升运算符将生成值。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-391">The lifted operator produces the value `false` if one or both operands are null.</span></span> <span data-ttu-id="2e3bd-392">否则，提升运算符将对操作数进行解包，并应用基础运算符来生成 `bool` 结果。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-392">Otherwise, the lifted operator unwraps the operands and applies the underlying operator to produce the `bool` result.</span></span>

## <a name="member-lookup"></a><span data-ttu-id="2e3bd-393">成员查找</span><span class="sxs-lookup"><span data-stu-id="2e3bd-393">Member lookup</span></span>

<span data-ttu-id="2e3bd-394">成员查找是指确定类型上下文中名称含义的进程。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-394">A member lookup is the process whereby the meaning of a name in the context of a type is determined.</span></span> <span data-ttu-id="2e3bd-395">成员查找可作为计算 *simple_name* ([简单名称](expressions.md#simple-names)) 或表达式中的 *member_access* ([成员访问](expressions.md#member-access)) 的一部分。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-395">A member lookup can occur as part of evaluating a *simple_name* ([Simple names](expressions.md#simple-names)) or a *member_access* ([Member access](expressions.md#member-access)) in an expression.</span></span> <span data-ttu-id="2e3bd-396">如果 *simple_name* 或 *member_access* 是 *invocation_expression* ([方法调用](expressions.md#method-invocations)) 的 *primary_expression* ，则称成员被调用。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-396">If the *simple_name* or *member_access* occurs as the *primary_expression* of an *invocation_expression* ([Method invocations](expressions.md#method-invocations)), the member is said to be invoked.</span></span>

<span data-ttu-id="2e3bd-397">如果成员是方法或事件，或者它是一个常量、委托类型的字段或属性 ([委托](delegates.md)) 或 `dynamic` ([动态类型](types.md#the-dynamic-type)) 的类型，则认为该成员是 *invocable*。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-397">If a member is a method or event, or if it is a constant, field or property of either a delegate type ([Delegates](delegates.md)) or the type `dynamic` ([The dynamic type](types.md#the-dynamic-type)), then the member is said to be *invocable*.</span></span>

<span data-ttu-id="2e3bd-398">成员查找不仅考虑了成员的名称，还考虑了成员具有的类型参数的数目以及该成员是否可访问。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-398">Member lookup considers not only the name of a member but also the number of type parameters the member has and whether the member is accessible.</span></span> <span data-ttu-id="2e3bd-399">出于成员查找的目的，泛型方法和嵌套泛型类型具有其各自声明中指示的类型参数的数目，并且所有其他成员都有零个类型参数。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-399">For the purposes of member lookup, generic methods and nested generic types have the number of type parameters indicated in their respective declarations and all other members have zero type parameters.</span></span>

<span data-ttu-id="2e3bd-400"> `N`类型中具有类型参数的名称的成员查找 `K`    `T` 按如下方式进行处理：</span><span class="sxs-lookup"><span data-stu-id="2e3bd-400">A member lookup of a name `N` with `K` type parameters in a type `T` is processed as follows:</span></span>

*  <span data-ttu-id="2e3bd-401">首先，确定一组名为的可访问成员  `N` ：</span><span class="sxs-lookup"><span data-stu-id="2e3bd-401">First, a set of accessible members named `N` is determined:</span></span>
    * <span data-ttu-id="2e3bd-402">如果 `T` 是一个类型参数，则该集是  `N` 在指定为主约束或辅助约束的每个类型中名为的可访问成员集的并集， ([类型参数约束](classes.md#type-parameter-constraints))  `T` ，以及中名为的可访问成员集  `N` `object` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-402">If `T` is a type parameter, then the set is the union of the sets of accessible members named `N` in each of the types specified as a primary constraint or secondary constraint ([Type parameter constraints](classes.md#type-parameter-constraints)) for `T`, along with the set of accessible members named `N` in `object`.</span></span>
    * <span data-ttu-id="2e3bd-403">否则，该集由中名为的所有可访问 ([成员访问](basic-concepts.md#member-access)) 成员  `N`  `T` ，包括继承成员和中命名的可访问成员  `N` `object` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-403">Otherwise, the set consists of all accessible ([Member access](basic-concepts.md#member-access)) members named `N` in `T`, including inherited members and the accessible members named `N` in `object`.</span></span> <span data-ttu-id="2e3bd-404">如果 `T` 是构造类型，则通过替换类型参数来获取成员集，如 [构造类型成员](classes.md#members-of-constructed-types)中所述。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-404">If `T` is a constructed type, the set of members is obtained by substituting type arguments as described in [Members of constructed types](classes.md#members-of-constructed-types).</span></span> <span data-ttu-id="2e3bd-405">包含修饰符的成员将 `override` 从集合中排除。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-405">Members that include an `override` modifier are excluded from the set.</span></span>
*  <span data-ttu-id="2e3bd-406">接下来，如果 `K` 为零，则将删除所有声明都包含类型参数的嵌套类型。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-406">Next, if `K` is zero, all nested types whose declarations include type parameters are removed.</span></span> <span data-ttu-id="2e3bd-407">如果不 `K` 为零，则删除所有具有不同数量的类型参数的成员。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-407">If `K` is not zero, all members with a different number of type parameters are removed.</span></span> <span data-ttu-id="2e3bd-408">请注意，当 `K` 为零时，不会删除具有类型参数的方法，因为类型推理过程 ([类型推理](expressions.md#type-inference)) 或许能够推断类型参数。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-408">Note that when `K` is zero, methods having type parameters are not removed, since the type inference process ([Type inference](expressions.md#type-inference)) might be able to infer the type arguments.</span></span>
*  <span data-ttu-id="2e3bd-409">接下来，如果 *调用* 成员，则将从集合中删除所有非 *invocable* 成员。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-409">Next, if the member is *invoked*, all non-*invocable* members are removed from the set.</span></span>
*  <span data-ttu-id="2e3bd-410">接下来，将从集合中删除其他成员隐藏的成员。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-410">Next, members that are hidden by other members are removed from the set.</span></span> <span data-ttu-id="2e3bd-411">对于集中的每个成员 `S.M` ，其中 `S` 是声明成员的类型，将  `M` 应用以下规则：</span><span class="sxs-lookup"><span data-stu-id="2e3bd-411">For every member `S.M` in the set, where `S` is the type in which the member `M` is declared, the following rules are applied:</span></span>
    * <span data-ttu-id="2e3bd-412">如果 `M` 是常量、字段、属性、事件或枚举成员，则将从集合中删除在基类型中声明的所有成员 `S` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-412">If `M` is a constant, field, property, event, or enumeration member, then all members declared in a base type of `S` are removed from the set.</span></span>
    * <span data-ttu-id="2e3bd-413">如果 `M` 是一个类型声明，则从该集内删除所有在基类型中声明的非类型 `S` ，并 `M` 从该集内删除与基类型中声明的类型参数数目相同的所有类型声明 `S` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-413">If `M` is a type declaration, then all non-types declared in a base type of `S` are removed from the set, and all type declarations with the same number of type parameters as `M` declared in a base type of `S` are removed from the set.</span></span>
    * <span data-ttu-id="2e3bd-414">如果 `M` 是方法，则将从集合中删除在的基类型中声明的所有非方法成员 `S` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-414">If `M` is a method, then all non-method members declared in a base type of `S` are removed from the set.</span></span>
*  <span data-ttu-id="2e3bd-415">接下来，将从集合中删除类成员隐藏的接口成员。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-415">Next, interface members that are hidden by class members are removed from the set.</span></span> <span data-ttu-id="2e3bd-416">仅当 `T` 是类型参数并且 `T` 既有有效的基类 `object` ，也具有非空的有效接口集 ([类型参数约束](classes.md#type-parameter-constraints)) 时，此步骤才会起作用。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-416">This step only has an effect if `T` is a type parameter and `T` has both an effective base class other than `object` and a non-empty effective interface set ([Type parameter constraints](classes.md#type-parameter-constraints)).</span></span> <span data-ttu-id="2e3bd-417">对于集合中的每个成员 `S.M` ，其中 `S` 是声明成员的类型 `M` ，如果 `S` 是类声明（而不是），则应用以下规则 `object` ：</span><span class="sxs-lookup"><span data-stu-id="2e3bd-417">For every member `S.M` in the set, where `S` is the type in which the member `M` is declared, the following rules are applied if `S` is a class declaration other than `object`:</span></span>
    * <span data-ttu-id="2e3bd-418">如果 `M` 是常量、字段、属性、事件、枚举成员或类型声明，则会从集中删除在接口声明中声明的所有成员。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-418">If `M` is a constant, field, property, event, enumeration member, or type declaration, then all members declared in an interface declaration are removed from the set.</span></span>
    * <span data-ttu-id="2e3bd-419">如果 `M` 是方法，则将从集合中删除在接口声明中声明的所有非方法成员，并 `M` 从集合中删除具有与在接口声明中声明的签名相同的所有方法。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-419">If `M` is a method, then all non-method members declared in an interface declaration are removed from the set, and all methods with the same signature as `M` declared in an interface declaration are removed from the set.</span></span>
*  <span data-ttu-id="2e3bd-420">最后，删除隐藏成员后，将确定查找的结果：</span><span class="sxs-lookup"><span data-stu-id="2e3bd-420">Finally, having removed hidden members, the result of the lookup is determined:</span></span>
    * <span data-ttu-id="2e3bd-421">如果集由不是方法的单个成员组成，则此成员是查找的结果。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-421">If the set consists of a single member that is not a method, then this member is the result of the lookup.</span></span>
    * <span data-ttu-id="2e3bd-422">否则，如果该集仅包含方法，则此方法组是查找的结果。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-422">Otherwise, if the set contains only methods, then this group of methods is the result of the lookup.</span></span>
    * <span data-ttu-id="2e3bd-423">否则，查找将不明确，并发生绑定时错误。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-423">Otherwise, the lookup is ambiguous, and a binding-time error occurs.</span></span>

<span data-ttu-id="2e3bd-424">对于类型参数和接口以外的类型中的成员查找以及严格为单一继承 (接口中的成员查找，继承链中的每个接口都具有完全零个或一个直接基接口) ，查找规则的效果只是派生成员隐藏具有相同名称或签名的基成员。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-424">For member lookups in types other than type parameters and interfaces, and member lookups in interfaces that are strictly single-inheritance (each interface in the inheritance chain has exactly zero or one direct base interface), the effect of the lookup rules is simply that derived members hide base members with the same name or signature.</span></span> <span data-ttu-id="2e3bd-425">这种单一继承查找从来都不明确。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-425">Such single-inheritance lookups are never ambiguous.</span></span> <span data-ttu-id="2e3bd-426">[接口成员访问](interfaces.md#interface-member-access)中介绍了可能从多继承接口中的成员查找产生的歧义。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-426">The ambiguities that can possibly arise from member lookups in multiple-inheritance interfaces are described in [Interface member access](interfaces.md#interface-member-access).</span></span>

### <a name="base-types"></a><span data-ttu-id="2e3bd-427">基类型</span><span class="sxs-lookup"><span data-stu-id="2e3bd-427">Base types</span></span>

<span data-ttu-id="2e3bd-428">出于成员查找的目的，类型被 `T` 视为具有以下基本类型：</span><span class="sxs-lookup"><span data-stu-id="2e3bd-428">For purposes of member lookup, a type `T` is considered to have the following base types:</span></span>

*  <span data-ttu-id="2e3bd-429">如果 `T` 为 `object` ，则 `T` 没有基类型。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-429">If `T` is `object`, then `T` has no base type.</span></span>
*  <span data-ttu-id="2e3bd-430">如果 `T` 是 *enum_type*，则的基类型 `T` 是类类型 `System.Enum` 、 `System.ValueType` 和 `object` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-430">If `T` is an *enum_type*, the base types of `T` are the class types `System.Enum`, `System.ValueType`, and `object`.</span></span>
*  <span data-ttu-id="2e3bd-431">如果 `T` 是 *struct_type*，则的基类型 `T` 是类类型 `System.ValueType` 和 `object` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-431">If `T` is a *struct_type*, the base types of `T` are the class types `System.ValueType` and `object`.</span></span>
*  <span data-ttu-id="2e3bd-432">如果 `T` 是 *class_type*，则的基类型 `T` 是的基类 `T` ，包括类类型 `object` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-432">If `T` is a *class_type*, the base types of `T` are the base classes of `T`, including the class type `object`.</span></span>
*  <span data-ttu-id="2e3bd-433">如果 `T` 是 *interface_type*，则的基类型 `T` 是 `T` 和类类型的基接口 `object` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-433">If `T` is an *interface_type*, the base types of `T` are the base interfaces of `T` and the class type `object`.</span></span>
*  <span data-ttu-id="2e3bd-434">如果 `T` 是 *array_type*，则的基类型 `T` 是类类型 `System.Array` 和 `object` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-434">If `T` is an *array_type*, the base types of `T` are the class types `System.Array` and `object`.</span></span>
*  <span data-ttu-id="2e3bd-435">如果 `T` 是 *delegate_type*，则的基类型 `T` 是类类型 `System.Delegate` 和 `object` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-435">If `T` is a *delegate_type*, the base types of `T` are the class types `System.Delegate` and `object`.</span></span>

## <a name="function-members"></a><span data-ttu-id="2e3bd-436">函数成员</span><span class="sxs-lookup"><span data-stu-id="2e3bd-436">Function members</span></span>

<span data-ttu-id="2e3bd-437">函数成员是包含可执行语句的成员。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-437">Function members are members that contain executable statements.</span></span> <span data-ttu-id="2e3bd-438">函数成员始终是类型的成员，不能是命名空间的成员。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-438">Function members are always members of types and cannot be members of namespaces.</span></span> <span data-ttu-id="2e3bd-439">C # 定义以下类别的函数成员：</span><span class="sxs-lookup"><span data-stu-id="2e3bd-439">C# defines the following categories of function members:</span></span>

*  <span data-ttu-id="2e3bd-440">方法</span><span class="sxs-lookup"><span data-stu-id="2e3bd-440">Methods</span></span>
*  <span data-ttu-id="2e3bd-441">属性</span><span class="sxs-lookup"><span data-stu-id="2e3bd-441">Properties</span></span>
*  <span data-ttu-id="2e3bd-442">事件</span><span class="sxs-lookup"><span data-stu-id="2e3bd-442">Events</span></span>
*  <span data-ttu-id="2e3bd-443">索引器</span><span class="sxs-lookup"><span data-stu-id="2e3bd-443">Indexers</span></span>
*  <span data-ttu-id="2e3bd-444">用户定义的运算符</span><span class="sxs-lookup"><span data-stu-id="2e3bd-444">User-defined operators</span></span>
*  <span data-ttu-id="2e3bd-445">实例构造函数</span><span class="sxs-lookup"><span data-stu-id="2e3bd-445">Instance constructors</span></span>
*  <span data-ttu-id="2e3bd-446">静态构造函数</span><span class="sxs-lookup"><span data-stu-id="2e3bd-446">Static constructors</span></span>
*  <span data-ttu-id="2e3bd-447">析构函数</span><span class="sxs-lookup"><span data-stu-id="2e3bd-447">Destructors</span></span>

<span data-ttu-id="2e3bd-448">除了不能) 显式调用的析构函数和静态构造函数 (，函数成员中包含的语句是通过函数成员调用来执行的。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-448">Except for destructors and static constructors (which cannot be invoked explicitly), the statements contained in function members are executed through function member invocations.</span></span> <span data-ttu-id="2e3bd-449">用于编写函数成员调用的实际语法取决于特定的函数成员类别。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-449">The actual syntax for writing a function member invocation depends on the particular function member category.</span></span>

<span data-ttu-id="2e3bd-450">参数列表 ([参数](expressions.md#argument-lists) 列表) 函数成员调用为函数成员的参数提供实际值或变量引用。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-450">The argument list ([Argument lists](expressions.md#argument-lists)) of a function member invocation provides actual values or variable references for the parameters of the function member.</span></span>

<span data-ttu-id="2e3bd-451">调用泛型方法时，可以使用类型推理来确定要传递给方法的类型参数集。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-451">Invocations of generic methods may employ type inference to determine the set of type arguments to pass to the method.</span></span> <span data-ttu-id="2e3bd-452">此过程在 [类型推理](expressions.md#type-inference)中进行了介绍。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-452">This process is described in [Type inference](expressions.md#type-inference).</span></span>

<span data-ttu-id="2e3bd-453">调用方法、索引器、运算符和实例构造函数会使用重载决策来确定要调用的候选函数成员集中哪一组。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-453">Invocations of methods, indexers, operators and instance constructors employ overload resolution to determine which of a candidate set of function members to invoke.</span></span> <span data-ttu-id="2e3bd-454">[重载决策](expressions.md#overload-resolution)中介绍了此过程。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-454">This process is described in [Overload resolution](expressions.md#overload-resolution).</span></span>

<span data-ttu-id="2e3bd-455">在绑定时确定了特定函数成员（可能通过重载决策）后，调用函数成员的实际运行时过程将在 [编译时检查动态重载决策](expressions.md#compile-time-checking-of-dynamic-overload-resolution)中进行说明。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-455">Once a particular function member has been identified at binding-time, possibly through overload resolution, the actual run-time process of invoking the function member is described in [Compile-time checking of dynamic overload resolution](expressions.md#compile-time-checking-of-dynamic-overload-resolution).</span></span>

<span data-ttu-id="2e3bd-456">下表总结了构造中发生的处理，涉及到可显式调用的六类函数成员。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-456">The following table summarizes the processing that takes place in constructs involving the six categories of function members that can be explicitly invoked.</span></span> <span data-ttu-id="2e3bd-457">在表中，、、 `e` `x` `y` 和 `value` 指示归类为变量或值的表达式， `T` 表示归类为类型的表达式， `F` 是方法的简单名称， `P` 是属性的简单名称。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-457">In the table, `e`, `x`, `y`, and `value` indicate expressions classified as variables or values, `T` indicates an expression classified as a type, `F` is the simple name of a method, and `P` is the simple name of a property.</span></span>


| <span data-ttu-id="2e3bd-458">__构造__</span><span class="sxs-lookup"><span data-stu-id="2e3bd-458">__Construct__</span></span>     | <span data-ttu-id="2e3bd-459">__示例__</span><span class="sxs-lookup"><span data-stu-id="2e3bd-459">__Example__</span></span>    | <span data-ttu-id="2e3bd-460">__说明__</span><span class="sxs-lookup"><span data-stu-id="2e3bd-460">__Description__</span></span> |
|-------------------|----------------|-----------------|
| <span data-ttu-id="2e3bd-461">方法调用</span><span class="sxs-lookup"><span data-stu-id="2e3bd-461">Method invocation</span></span> | `F(x,y)`       | <span data-ttu-id="2e3bd-462">应用重载决策，以选择 `F` 包含类或结构中的最佳方法。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-462">Overload resolution is applied to select the best method `F` in the containing class or struct.</span></span> <span data-ttu-id="2e3bd-463">方法是通过参数列表调用的 `(x,y)` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-463">The method is invoked with the argument list `(x,y)`.</span></span> <span data-ttu-id="2e3bd-464">如果该方法不为 `static` ，则实例表达式为 `this` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-464">If the method is not `static`, the instance expression is `this`.</span></span> | 
|                   | `T.F(x,y)`     | <span data-ttu-id="2e3bd-465">应用重载决策，以选择 `F` 类或结构中的最佳方法 `T` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-465">Overload resolution is applied to select the best method `F` in the class or struct `T`.</span></span> <span data-ttu-id="2e3bd-466">如果该方法不是，则会发生绑定时错误 `static` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-466">A binding-time error occurs if the method is not `static`.</span></span> <span data-ttu-id="2e3bd-467">方法是通过参数列表调用的 `(x,y)` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-467">The method is invoked with the argument list `(x,y)`.</span></span> | 
|                   | `e.F(x,y)`     | <span data-ttu-id="2e3bd-468">重载决策适用于在类、结构或接口中选择由类型给定的最佳方法 F `e` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-468">Overload resolution is applied to select the best method F in the class, struct, or interface given by the type of `e`.</span></span> <span data-ttu-id="2e3bd-469">如果方法为，则发生绑定时错误 `static` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-469">A binding-time error occurs if the method is `static`.</span></span> <span data-ttu-id="2e3bd-470">方法是通过实例表达式 `e` 和参数列表调用的 `(x,y)` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-470">The method is invoked with the instance expression `e` and the argument list `(x,y)`.</span></span> | 
| <span data-ttu-id="2e3bd-471">和</span><span class="sxs-lookup"><span data-stu-id="2e3bd-471">Property access</span></span>   | `P`            | <span data-ttu-id="2e3bd-472">`get` `P` 调用包含类或结构中的属性的访问器。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-472">The `get` accessor of the property `P` in the containing class or struct is invoked.</span></span> <span data-ttu-id="2e3bd-473">如果是只写的，则会发生编译时错误 `P` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-473">A compile-time error occurs if `P` is write-only.</span></span> <span data-ttu-id="2e3bd-474">如果不 `P` 为 `static` ，则实例表达式为 `this` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-474">If `P` is not `static`, the instance expression is `this`.</span></span> | 
|                   | `P = value`    | <span data-ttu-id="2e3bd-475">`set` `P` 包含类或结构中的属性的访问器是通过参数列表调用的 `(value)` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-475">The `set` accessor of the property `P` in the containing class or struct is invoked with the argument list `(value)`.</span></span> <span data-ttu-id="2e3bd-476">如果是只读的，则会发生编译时错误 `P` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-476">A compile-time error occurs if `P` is read-only.</span></span> <span data-ttu-id="2e3bd-477">如果不 `P` 为 `static` ，则实例表达式为 `this` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-477">If `P` is not `static`, the instance expression is `this`.</span></span> | 
|                   | `T.P`          | <span data-ttu-id="2e3bd-478">`get` `P` 调用类或结构中的属性的访问器 `T` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-478">The `get` accessor of the property `P` in the class or struct `T` is invoked.</span></span> <span data-ttu-id="2e3bd-479">如果不 `P` 是 `static` 或 `P` 是只写的，则会发生编译时错误。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-479">A compile-time error occurs if `P` is not `static` or if `P` is write-only.</span></span> | 
|                   | `T.P = value`  | <span data-ttu-id="2e3bd-480">`set`类或结构中的属性的访问器 `P` `T` 是通过参数列表调用的 `(value)` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-480">The `set` accessor of the property `P` in the class or struct `T` is invoked with the argument list `(value)`.</span></span> <span data-ttu-id="2e3bd-481">如果不 `P` 是 `static` 或为只读，则会发生编译时错误 `P` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-481">A compile-time error occurs if `P` is not `static` or if `P` is read-only.</span></span> | 
|                   | `e.P`          | <span data-ttu-id="2e3bd-482">的 `get` `P` 类型提供的类、结构或接口中的属性的访问器由 `e` 实例表达式调用 `e` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-482">The `get` accessor of the property `P` in the class, struct, or interface given by the type of `e` is invoked with the instance expression `e`.</span></span> <span data-ttu-id="2e3bd-483">如果 `P` 为， `static` 或者如果 `P` 为只写，则发生绑定时错误。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-483">A binding-time error occurs if `P` is `static` or if `P` is write-only.</span></span> | 
|                   | `e.P = value`  | <span data-ttu-id="2e3bd-484">的 `set` `P` 类型提供的类、结构或接口中的属性的访问器 `e` 是使用实例表达式 `e` 和参数列表调用的 `(value)` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-484">The `set` accessor of the property `P` in the class, struct, or interface given by the type of `e` is invoked with the instance expression `e` and the argument list `(value)`.</span></span> <span data-ttu-id="2e3bd-485">如果 `P` 为 `static` 或 `P` 为只读，则会发生绑定时错误。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-485">A binding-time error occurs if `P` is `static` or if `P` is read-only.</span></span> | 
| <span data-ttu-id="2e3bd-486">事件访问</span><span class="sxs-lookup"><span data-stu-id="2e3bd-486">Event access</span></span>      | `E += value`   | <span data-ttu-id="2e3bd-487">`add` `E` 调用包含类或结构中的事件的访问器。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-487">The `add` accessor of the event `E` in the containing class or struct is invoked.</span></span> <span data-ttu-id="2e3bd-488">如果不 `E` 是静态的，则实例表达式为 `this` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-488">If `E` is not static, the instance expression is `this`.</span></span> | 
|                   | `E -= value`   | <span data-ttu-id="2e3bd-489">`remove` `E` 调用包含类或结构中的事件的访问器。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-489">The `remove` accessor of the event `E` in the containing class or struct is invoked.</span></span> <span data-ttu-id="2e3bd-490">如果不 `E` 是静态的，则实例表达式为 `this` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-490">If `E` is not static, the instance expression is `this`.</span></span> | 
|                   | `T.E += value` | <span data-ttu-id="2e3bd-491">`add` `E` 调用类或结构中的事件的访问器 `T` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-491">The `add` accessor of the event `E` in the class or struct `T` is invoked.</span></span> <span data-ttu-id="2e3bd-492">如果不是静态的，则会发生绑定时错误 `E` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-492">A binding-time error occurs if `E` is not static.</span></span> | 
|                   | `T.E -= value` | <span data-ttu-id="2e3bd-493">`remove` `E` 调用类或结构中的事件的访问器 `T` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-493">The `remove` accessor of the event `E` in the class or struct `T` is invoked.</span></span> <span data-ttu-id="2e3bd-494">如果不是静态的，则会发生绑定时错误 `E` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-494">A binding-time error occurs if `E` is not static.</span></span> | 
|                   | `e.E += value` | <span data-ttu-id="2e3bd-495">的 `add` `E` 类型提供的类、结构或接口中的事件的访问器由 `e` 实例表达式调用 `e` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-495">The `add` accessor of the event `E` in the class, struct, or interface given by the type of `e` is invoked with the instance expression `e`.</span></span> <span data-ttu-id="2e3bd-496">如果是静态的，则会发生绑定时错误 `E` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-496">A binding-time error occurs if `E` is static.</span></span> | 
|                   | `e.E -= value` | <span data-ttu-id="2e3bd-497">的 `remove` `E` 类型提供的类、结构或接口中的事件的访问器由 `e` 实例表达式调用 `e` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-497">The `remove` accessor of the event `E` in the class, struct, or interface given by the type of `e` is invoked with the instance expression `e`.</span></span> <span data-ttu-id="2e3bd-498">如果是静态的，则会发生绑定时错误 `E` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-498">A binding-time error occurs if `E` is static.</span></span> | 
| <span data-ttu-id="2e3bd-499">索引器访问</span><span class="sxs-lookup"><span data-stu-id="2e3bd-499">Indexer access</span></span>    | `e[x,y]`       | <span data-ttu-id="2e3bd-500">重载决策适用于在由 e 类型给定的类、结构或接口中选择最佳索引器。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-500">Overload resolution is applied to select the best indexer in the class, struct, or interface given by the type of e.</span></span> <span data-ttu-id="2e3bd-501">索引器的 `get` 访问器是通过实例表达式 `e` 和参数列表调用的 `(x,y)` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-501">The `get` accessor of the indexer is invoked with the instance expression `e` and the argument list `(x,y)`.</span></span> <span data-ttu-id="2e3bd-502">如果索引器是只写的，则会发生绑定时错误。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-502">A binding-time error occurs if the indexer is write-only.</span></span> | 
|                   | `e[x,y] = value` | <span data-ttu-id="2e3bd-503">应用重载决策，以选择由类型给定的类、结构或接口中的最佳索引器 `e` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-503">Overload resolution is applied to select the best indexer in the class, struct, or interface given by the type of `e`.</span></span> <span data-ttu-id="2e3bd-504">索引器的 `set` 访问器是通过实例表达式 `e` 和参数列表调用的 `(x,y,value)` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-504">The `set` accessor of the indexer is invoked with the instance expression `e` and the argument list `(x,y,value)`.</span></span> <span data-ttu-id="2e3bd-505">如果索引器是只读的，则会发生绑定时错误。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-505">A binding-time error occurs if the indexer is read-only.</span></span> | 
| <span data-ttu-id="2e3bd-506">运算符调用</span><span class="sxs-lookup"><span data-stu-id="2e3bd-506">Operator invocation</span></span> | `-x`         | <span data-ttu-id="2e3bd-507">应用重载决策，以选择由类型给定的类或结构中的最佳一元运算符 `x` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-507">Overload resolution is applied to select the best unary operator in the class or struct given by the type of `x`.</span></span> <span data-ttu-id="2e3bd-508">用参数列表调用所选运算符 `(x)` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-508">The selected operator is invoked with the argument list `(x)`.</span></span> | 
|                     | `x + y`      | <span data-ttu-id="2e3bd-509">重载决策适用于在和类型给定的类或结构中选择最佳的二元运算符 `x` `y` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-509">Overload resolution is applied to select the best binary operator in the classes or structs given by the types of `x` and `y`.</span></span> <span data-ttu-id="2e3bd-510">用参数列表调用所选运算符 `(x,y)` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-510">The selected operator is invoked with the argument list `(x,y)`.</span></span> | 
| <span data-ttu-id="2e3bd-511">实例构造函数调用</span><span class="sxs-lookup"><span data-stu-id="2e3bd-511">Instance constructor invocation</span></span> | `new T(x,y)` | <span data-ttu-id="2e3bd-512">应用重载决策，以选择类或结构中的最佳实例构造函数 `T` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-512">Overload resolution is applied to select the best instance constructor in the class or struct `T`.</span></span> <span data-ttu-id="2e3bd-513">实例构造函数是用参数列表调用的 `(x,y)` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-513">The instance constructor is invoked with the argument list `(x,y)`.</span></span> | 

### <a name="argument-lists"></a><span data-ttu-id="2e3bd-514">参数列表</span><span class="sxs-lookup"><span data-stu-id="2e3bd-514">Argument lists</span></span>

<span data-ttu-id="2e3bd-515">每个函数成员和委托调用都包含一个参数列表，该列表为函数成员的参数提供实际值或变量引用。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-515">Every function member and delegate invocation includes an argument list which provides actual values or variable references for the parameters of the function member.</span></span> <span data-ttu-id="2e3bd-516">指定函数成员调用的参数列表的语法取决于函数成员类别：</span><span class="sxs-lookup"><span data-stu-id="2e3bd-516">The syntax for specifying the argument list of a function member invocation depends on the function member category:</span></span>

*  <span data-ttu-id="2e3bd-517">对于实例构造函数、方法、索引器和委托，参数被指定为 *argument_list*，如下所述。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-517">For instance constructors, methods, indexers and delegates, the arguments are specified as an *argument_list*, as described below.</span></span> <span data-ttu-id="2e3bd-518">对于索引器，调用 `set` 访问器时，自变量列表还包括指定为赋值运算符的右操作数的表达式。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-518">For indexers, when invoking the `set` accessor, the argument list additionally includes the expression specified as the right operand of the assignment operator.</span></span>
*  <span data-ttu-id="2e3bd-519">对于属性，调用访问器时，参数列表是空的，在 `get` 调用访问器时，参数列表由指定为赋值运算符的右操作数的表达式组成 `set` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-519">For properties, the argument list is empty when invoking the `get` accessor, and consists of the expression specified as the right operand of the assignment operator when invoking the `set` accessor.</span></span>
*  <span data-ttu-id="2e3bd-520">对于事件，参数列表包含指定为 or 运算符的右操作数的表达式 `+=` `-=` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-520">For events, the argument list consists of the expression specified as the right operand of the `+=` or `-=` operator.</span></span>
*  <span data-ttu-id="2e3bd-521">对于用户定义的运算符，参数列表包含一元运算符的单个操作数或二元运算符的两个操作数。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-521">For user-defined operators, the argument list consists of the single operand of the unary operator or the two operands of the binary operator.</span></span>

<span data-ttu-id="2e3bd-522">属性 ([属性](classes.md#properties) 的参数) ，事件 ([事件](classes.md#events)) ，并且用户定义的运算符 ([运算符](classes.md#operators)) 始终作为值参数传递 ([值参数](classes.md#value-parameters)) 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-522">The arguments of properties ([Properties](classes.md#properties)), events ([Events](classes.md#events)), and user-defined operators ([Operators](classes.md#operators)) are always passed as value parameters ([Value parameters](classes.md#value-parameters)).</span></span> <span data-ttu-id="2e3bd-523">索引器 ([索引](classes.md#indexers) 器) 的参数始终作为值参数传递 ([值参数](classes.md#value-parameters)) 或参数数组 ([参数数组](classes.md#parameter-arrays)) 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-523">The arguments of indexers ([Indexers](classes.md#indexers)) are always passed as value parameters ([Value parameters](classes.md#value-parameters)) or parameter arrays ([Parameter arrays](classes.md#parameter-arrays)).</span></span> <span data-ttu-id="2e3bd-524">这些类别的函数成员不支持 Reference 和 output 参数。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-524">Reference and output parameters are not supported for these categories of function members.</span></span>

<span data-ttu-id="2e3bd-525">实例构造函数、方法、索引器或委托调用的参数指定为 *argument_list*：</span><span class="sxs-lookup"><span data-stu-id="2e3bd-525">The arguments of an instance constructor, method, indexer or delegate invocation are specified as an *argument_list*:</span></span>

```antlr
argument_list
    : argument (',' argument)*
    ;

argument
    : argument_name? argument_value
    ;

argument_name
    : identifier ':'
    ;

argument_value
    : expression
    | 'ref' variable_reference
    | 'out' variable_reference
    ;
```

<span data-ttu-id="2e3bd-526">*Argument_list* 由一个或多个由逗号分隔的 *参数* 组成。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-526">An *argument_list* consists of one or more *argument* s, separated by commas.</span></span> <span data-ttu-id="2e3bd-527">每个自变量都包含一个可选的  *argument_name* 后跟一个 *argument_value*。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-527">Each argument consists of an optional  *argument_name* followed by an *argument_value*.</span></span> <span data-ttu-id="2e3bd-528">带有 *argument_name* 的 *参数* 称为 \***命名参数** _，而 _没有 *argument_name* 的参数 \* 是 \***位置参数**_。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-528">An *argument* with an *argument_name* is referred to as a ***named argument** _, whereas an _argument* without an *argument_name* is a \***positional argument**_.</span></span> <span data-ttu-id="2e3bd-529">位置参数出现在 _argument_list \* 中的命名参数之后的错误。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-529">It is an error for a positional argument to appear after a named argument in an _argument_list\*.</span></span>

<span data-ttu-id="2e3bd-530">*Argument_value* 可以采用以下形式之一：</span><span class="sxs-lookup"><span data-stu-id="2e3bd-530">The *argument_value* can take one of the following forms:</span></span>

*  <span data-ttu-id="2e3bd-531">一个 *表达式*，指示参数作为值参数传递， ([值参数](classes.md#value-parameters)) 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-531">An *expression*, indicating that the argument is passed as a value parameter ([Value parameters](classes.md#value-parameters)).</span></span>
*  <span data-ttu-id="2e3bd-532">关键字 `ref` 后跟一个 *Variable_reference* ([变量引用](variables.md#variable-references)) ，指示参数作为引用参数传递 ([引用参数](classes.md#reference-parameters)) 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-532">The keyword `ref` followed by a *variable_reference* ([Variable references](variables.md#variable-references)), indicating that the argument is passed as a reference parameter ([Reference parameters](classes.md#reference-parameters)).</span></span> <span data-ttu-id="2e3bd-533">在将变量作为引用参数传递之前，必须明确地 ([明确赋值](variables.md#definite-assignment)) 赋值。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-533">A variable must be definitely assigned ([Definite assignment](variables.md#definite-assignment)) before it can be passed as a reference parameter.</span></span> <span data-ttu-id="2e3bd-534">关键字 `out` 后跟一个 *Variable_reference* ([变量引用](variables.md#variable-references)) ，指示参数作为输出参数传递 ([输出参数](classes.md#output-parameters)) 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-534">The keyword `out` followed by a *variable_reference* ([Variable references](variables.md#variable-references)), indicating that the argument is passed as an output parameter ([Output parameters](classes.md#output-parameters)).</span></span> <span data-ttu-id="2e3bd-535">在将变量作为输出参数传递的情况下，变量被视为明确赋值 ([明确赋值](variables.md#definite-assignment)) 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-535">A variable is considered definitely assigned ([Definite assignment](variables.md#definite-assignment)) following a function member invocation in which the variable is passed as an output parameter.</span></span>

#### <a name="corresponding-parameters"></a><span data-ttu-id="2e3bd-536">对应的参数</span><span class="sxs-lookup"><span data-stu-id="2e3bd-536">Corresponding parameters</span></span>

<span data-ttu-id="2e3bd-537">对于参数列表中的每个参数，必须在要调用的函数成员或委托中包含相应的参数。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-537">For each argument in an argument list there has to be a corresponding parameter in the function member or delegate being invoked.</span></span>

<span data-ttu-id="2e3bd-538">以下内容中使用的参数列表的确定方式如下：</span><span class="sxs-lookup"><span data-stu-id="2e3bd-538">The parameter list used in the following is determined as follows:</span></span>

*  <span data-ttu-id="2e3bd-539">对于在类中定义的虚拟方法和索引器，将从函数成员的最特定声明或重写中选取参数列表，从接收方的静态类型开始，然后搜索其基类。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-539">For virtual methods and indexers defined in classes, the parameter list is picked from the most specific declaration or override of the function member, starting with the static type of the receiver, and searching through its base classes.</span></span>
*  <span data-ttu-id="2e3bd-540">对于接口方法和索引器，从接口类型和搜索基接口开始，选取参数列表作为成员的最明确定义。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-540">For interface methods and indexers, the parameter list is picked form the most specific definition of the member, starting with the interface type and searching through the base interfaces.</span></span> <span data-ttu-id="2e3bd-541">如果找不到唯一的参数列表，则将构造一个具有不可访问的名称且不带可选参数的参数列表，以便调用不能使用命名参数或省略可选参数。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-541">If no unique parameter list is found, a parameter list with inaccessible names and no optional parameters is constructed, so that invocations cannot use named parameters or omit optional arguments.</span></span>
*  <span data-ttu-id="2e3bd-542">对于分部方法，使用定义分部方法声明的参数列表。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-542">For partial methods, the parameter list of the defining partial method declaration is used.</span></span>
*  <span data-ttu-id="2e3bd-543">对于所有其他函数成员和委托，只有一个参数列表，该列表是使用的。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-543">For all other function members and delegates there is only a single parameter list, which is the one used.</span></span>

<span data-ttu-id="2e3bd-544">参数或参数的位置定义为参数列表或参数列表中其前面的参数或参数的数目。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-544">The position of an argument or parameter is defined as the number of arguments or parameters preceding it in the argument list or parameter list.</span></span>

<span data-ttu-id="2e3bd-545">按如下所示建立函数成员参数的对应参数：</span><span class="sxs-lookup"><span data-stu-id="2e3bd-545">The corresponding parameters for function member arguments are established as follows:</span></span>

*  <span data-ttu-id="2e3bd-546">实例构造函数、方法、索引器和委托的 *argument_list* 中的参数：</span><span class="sxs-lookup"><span data-stu-id="2e3bd-546">Arguments in the *argument_list* of instance constructors, methods, indexers and delegates:</span></span>
    * <span data-ttu-id="2e3bd-547">一个位置参数，其中固定参数出现在参数列表中的同一位置，该参数对应于该参数。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-547">A positional argument where a fixed parameter occurs at the same position in the parameter list corresponds to that parameter.</span></span>
    * <span data-ttu-id="2e3bd-548">函数成员的位置自变量，其正常形式中调用的参数数组对应于参数数组，该参数数组必须出现在参数列表中的同一位置。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-548">A positional argument of a function member with a parameter array invoked in its normal form corresponds to the parameter  array, which must occur at the same position in the parameter list.</span></span>
    * <span data-ttu-id="2e3bd-549">函数成员的位置自变量，其中的参数数组以其展开形式调用，其中没有固定参数出现在参数列表中的同一位置，它对应于参数数组中的一个元素。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-549">A positional argument of a function member with a parameter array invoked in its expanded form, where no fixed parameter occurs at the same position in the parameter list, corresponds to an element in the parameter array.</span></span>
    * <span data-ttu-id="2e3bd-550">命名参数对应于参数列表中具有相同名称的参数。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-550">A named argument corresponds to the parameter of the same name in the parameter list.</span></span>
    * <span data-ttu-id="2e3bd-551">对于索引器，调用 `set` 访问器时，指定为赋值运算符的右操作数的表达式对应于 `value` `set` 访问器声明的隐式参数。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-551">For indexers, when invoking the `set` accessor, the expression specified as the right operand of the assignment operator corresponds to the implicit `value` parameter of the `set` accessor declaration.</span></span>
*  <span data-ttu-id="2e3bd-552">对于属性，在调用 `get` 访问器时没有参数。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-552">For properties, when invoking the `get` accessor there are no arguments.</span></span> <span data-ttu-id="2e3bd-553">调用 `set` 访问器时，指定为赋值运算符的右操作数的表达式对应于 `value` 访问器声明的隐式参数 `set` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-553">When invoking the `set` accessor, the expression specified as the right operand of the assignment operator corresponds to the implicit `value` parameter of the `set` accessor declaration.</span></span>
*  <span data-ttu-id="2e3bd-554">对于用户定义的一元运算符 (包括转换) ，单个操作数对应于运算符声明的单个参数。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-554">For user-defined unary operators (including conversions), the single operand corresponds to the single parameter of the operator declaration.</span></span>
*  <span data-ttu-id="2e3bd-555">对于用户定义的二进制运算符，左操作数对应于第一个参数，右侧操作数对应于运算符声明的第二个参数。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-555">For user-defined binary operators, the left operand corresponds to the first parameter, and the right operand corresponds to the second parameter of the operator declaration.</span></span>

#### <a name="run-time-evaluation-of-argument-lists"></a><span data-ttu-id="2e3bd-556">参数列表的运行时计算</span><span class="sxs-lookup"><span data-stu-id="2e3bd-556">Run-time evaluation of argument lists</span></span>

<span data-ttu-id="2e3bd-557">在运行时处理函数成员调用期间 (对 [动态重载解析) 进行编译时检查](expressions.md#compile-time-checking-of-dynamic-overload-resolution) ，将按从左至右的顺序计算参数列表的表达式或变量引用，如下所示：</span><span class="sxs-lookup"><span data-stu-id="2e3bd-557">During the run-time processing of a function member invocation ([Compile-time checking of dynamic overload resolution](expressions.md#compile-time-checking-of-dynamic-overload-resolution)), the expressions or variable references of an argument list are evaluated in order, from left to right, as follows:</span></span>

*  <span data-ttu-id="2e3bd-558">对于值参数，将计算参数表达式，并在执行相应参数类型)  ([隐](conversions.md#implicit-conversions) 式转换。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-558">For a value parameter, the argument expression is evaluated and an implicit conversion ([Implicit conversions](conversions.md#implicit-conversions)) to the corresponding parameter type is performed.</span></span> <span data-ttu-id="2e3bd-559">生成的值将成为函数成员调用中 value 参数的初始值。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-559">The resulting value becomes the initial value of the value parameter in the function member invocation.</span></span>
*  <span data-ttu-id="2e3bd-560">对于引用参数或输出参数，将对变量引用进行计算，并将生成的存储位置成为函数成员调用中的参数所表示的存储位置。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-560">For a reference or output parameter, the variable reference is evaluated and the resulting storage location becomes the storage location represented by the parameter in the function member invocation.</span></span> <span data-ttu-id="2e3bd-561">如果作为引用或输出参数提供的变量引用是 *reference_type* 的数组元素，则执行运行时检查以确保数组的元素类型与参数的类型相同。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-561">If the variable reference given as a reference or output parameter is an array element of a *reference_type*, a run-time check is performed to ensure that the element type of the array is identical to the type of the parameter.</span></span> <span data-ttu-id="2e3bd-562">如果此检查失败， `System.ArrayTypeMismatchException` 则会引发。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-562">If this check fails, a `System.ArrayTypeMismatchException` is thrown.</span></span>

<span data-ttu-id="2e3bd-563">方法、索引器和实例构造函数可以将其最右侧的参数声明为参数数组， ([参数数组](classes.md#parameter-arrays)) 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-563">Methods, indexers, and instance constructors may declare their right-most parameter to be a parameter array ([Parameter arrays](classes.md#parameter-arrays)).</span></span> <span data-ttu-id="2e3bd-564">此类函数成员的调用方式为其正常形式或采用其展开形式，具体取决于适用 ([适用的函数成员](expressions.md#applicable-function-member)) ：</span><span class="sxs-lookup"><span data-stu-id="2e3bd-564">Such function members are invoked either in their normal form or in their expanded form depending on which is applicable ([Applicable function member](expressions.md#applicable-function-member)):</span></span>

*  <span data-ttu-id="2e3bd-565">当使用参数数组的函数成员以其正常形式调用时，为参数数组提供的参数必须是可隐式转换 () 到参数数组类型的 [隐式转换](conversions.md#implicit-conversions) 的单个表达式。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-565">When a function member with a parameter array is invoked in its normal form, the argument given for the parameter array must be a single expression that is implicitly convertible ([Implicit conversions](conversions.md#implicit-conversions)) to the parameter array type.</span></span> <span data-ttu-id="2e3bd-566">在这种情况下，参数数组的作用与值参数完全相同。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-566">In this case, the parameter array acts precisely like a value parameter.</span></span>
*  <span data-ttu-id="2e3bd-567">当具有参数数组的函数成员以其展开形式调用时，调用必须为参数数组指定零个或多个位置参数，其中每个参数都是隐式转换 (隐式 [转换](conversions.md#implicit-conversions)) 到参数数组的元素类型的表达式。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-567">When a function member with a parameter array is invoked in its expanded form, the invocation must specify zero or more positional arguments for the parameter array, where each argument is an expression that is implicitly convertible ([Implicit conversions](conversions.md#implicit-conversions)) to the element type of the parameter array.</span></span> <span data-ttu-id="2e3bd-568">在这种情况下，调用会创建一个参数数组类型的实例，该实例的长度与参数的数量相对应，使用给定的参数值初始化数组实例的元素，并使用新创建的数组实例作为实参。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-568">In this case, the invocation creates an instance of the parameter array type with a length corresponding to the number of arguments, initializes the elements of the array instance with the given argument values, and uses the newly created array instance as the actual argument.</span></span>

<span data-ttu-id="2e3bd-569">自变量列表的表达式始终按写入它们的顺序进行计算。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-569">The expressions of an argument list are always evaluated in the order they are written.</span></span> <span data-ttu-id="2e3bd-570">因此，示例</span><span class="sxs-lookup"><span data-stu-id="2e3bd-570">Thus, the example</span></span>
```csharp
class Test
{
    static void F(int x, int y = -1, int z = -2) {
        System.Console.WriteLine("x = {0}, y = {1}, z = {2}", x, y, z);
    }

    static void Main() {
        int i = 0;
        F(i++, i++, i++);
        F(z: i++, x: i++);
    }
}
```
<span data-ttu-id="2e3bd-571">生成输出</span><span class="sxs-lookup"><span data-stu-id="2e3bd-571">produces the output</span></span>
```console
x = 0, y = 1, z = 2
x = 4, y = -1, z = 3
```

<span data-ttu-id="2e3bd-572">数组协方差规则 ([数组协方](arrays.md#array-covariance) 差) 允许将数组类型的值 `A[]` 作为对数组类型的实例的引用 `B[]` ，前提是从到存在隐式引用转换 `B` `A` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-572">The array co-variance rules ([Array covariance](arrays.md#array-covariance)) permit a value of an array type `A[]` to be a reference to an instance of an array type `B[]`, provided an implicit reference conversion exists from `B` to `A`.</span></span> <span data-ttu-id="2e3bd-573">由于这些规则，当 *reference_type* 的数组元素作为引用或输出参数传递时，需要运行时检查以确保数组的实际元素类型与参数的类型相同。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-573">Because of these rules, when an array element of a *reference_type* is passed as a reference or output parameter, a run-time check is required to ensure that the actual element type of the array is identical to that of the parameter.</span></span> <span data-ttu-id="2e3bd-574">示例中</span><span class="sxs-lookup"><span data-stu-id="2e3bd-574">In the example</span></span>
```csharp
class Test
{
    static void F(ref object x) {...}

    static void Main() {
        object[] a = new object[10];
        object[] b = new string[10];
        F(ref a[0]);        // Ok
        F(ref b[1]);        // ArrayTypeMismatchException
    }
}
```
<span data-ttu-id="2e3bd-575">的第二次调用 `F` 导致 `System.ArrayTypeMismatchException` 引发，因为的实际元素类型 `b` 为 `string` ，而不是 `object` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-575">the second invocation of `F` causes a `System.ArrayTypeMismatchException` to be thrown because the actual element type of `b` is `string` and not `object`.</span></span>

<span data-ttu-id="2e3bd-576">在以其展开形式调用具有参数数组的函数成员时，调用的处理方式与数组创建表达式中的数组创建表达式)  ([数组创建](expressions.md#array-creation-expressions) 表达式的数组创建表达式相同。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-576">When a function member with a parameter array is invoked in its expanded form, the invocation is processed exactly as if an array creation expression with an array initializer ([Array creation expressions](expressions.md#array-creation-expressions)) was inserted around the expanded parameters.</span></span> <span data-ttu-id="2e3bd-577">例如，给定声明</span><span class="sxs-lookup"><span data-stu-id="2e3bd-577">For example, given the declaration</span></span>
```csharp
void F(int x, int y, params object[] args);
```
<span data-ttu-id="2e3bd-578">对方法的展开形式的以下调用</span><span class="sxs-lookup"><span data-stu-id="2e3bd-578">the following invocations of the expanded form of the method</span></span>
```csharp
F(10, 20);
F(10, 20, 30, 40);
F(10, 20, 1, "hello", 3.0);
```
<span data-ttu-id="2e3bd-579">完全对应于</span><span class="sxs-lookup"><span data-stu-id="2e3bd-579">correspond exactly to</span></span>
```csharp
F(10, 20, new object[] {});
F(10, 20, new object[] {30, 40});
F(10, 20, new object[] {1, "hello", 3.0});
```

<span data-ttu-id="2e3bd-580">特别要注意的是，如果参数数组提供的参数为零，则将创建一个空数组。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-580">In particular, note that an empty array is created when there are zero arguments given for the parameter array.</span></span>

<span data-ttu-id="2e3bd-581">当使用相应的可选参数从函数成员中省略参数时，将隐式传递函数成员声明的默认参数。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-581">When arguments are omitted from a function member with corresponding optional parameters, the default arguments of the function member declaration are implicitly passed.</span></span> <span data-ttu-id="2e3bd-582">因为这些始终是常量，所以其计算不会影响剩余参数的计算顺序。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-582">Because these are always constant, their evaluation will not impact the evaluation order of the remaining arguments.</span></span>

### <a name="type-inference"></a><span data-ttu-id="2e3bd-583">类型推理</span><span class="sxs-lookup"><span data-stu-id="2e3bd-583">Type inference</span></span>

<span data-ttu-id="2e3bd-584">如果在未指定类型实参的情况下调用泛型方法，则 ***类型推理*** 进程将尝试推断调用的类型参数。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-584">When a generic method is called without specifying type arguments, a ***type inference*** process attempts to infer type arguments for the call.</span></span> <span data-ttu-id="2e3bd-585">类型推理的存在允许使用更方便的语法来调用泛型方法，并允许程序员避免指定冗余类型信息。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-585">The presence of type inference allows a more convenient syntax to be used for calling a generic method, and allows the programmer to avoid specifying redundant type information.</span></span> <span data-ttu-id="2e3bd-586">例如，给定方法声明：</span><span class="sxs-lookup"><span data-stu-id="2e3bd-586">For example, given the method declaration:</span></span>
```csharp
class Chooser
{
    static Random rand = new Random();

    public static T Choose<T>(T first, T second) {
        return (rand.Next(2) == 0)? first: second;
    }
}
```
<span data-ttu-id="2e3bd-587">可以调用 `Choose` 方法而无需显式指定类型参数：</span><span class="sxs-lookup"><span data-stu-id="2e3bd-587">it is possible to invoke the `Choose` method without explicitly specifying a type argument:</span></span>
```csharp
int i = Chooser.Choose(5, 213);                 // Calls Choose<int>

string s = Chooser.Choose("foo", "bar");        // Calls Choose<string>
```

<span data-ttu-id="2e3bd-588">通过类型推理，类型参数 `int` 和 `string` 由方法的自变量确定。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-588">Through type inference, the type arguments `int` and `string` are determined from the arguments to the method.</span></span>

<span data-ttu-id="2e3bd-589">类型推理作为方法调用的绑定时处理的一部分进行， ([方法](expressions.md#method-invocations) 调用) 并在调用的重载决策步骤之前发生。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-589">Type inference occurs as part of the binding-time processing of a method invocation ([Method invocations](expressions.md#method-invocations)) and takes place before the overload resolution step of the invocation.</span></span> <span data-ttu-id="2e3bd-590">如果在方法调用中指定了特定方法组，但没有将任何类型参数指定为方法调用的一部分，则会将类型推理应用于方法组中的每个泛型方法。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-590">When a particular method group is specified in a method invocation, and no type arguments are specified as part of the method invocation, type inference is applied to each generic method in the method group.</span></span> <span data-ttu-id="2e3bd-591">如果类型推理成功，则使用推断出的类型参数来确定参数的类型，以供后续重载决策使用。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-591">If type inference succeeds, then the inferred type arguments are used to determine the types of arguments for subsequent overload resolution.</span></span> <span data-ttu-id="2e3bd-592">如果重载决策选择泛型方法作为要调用的方法，则会将推断的类型参数用作调用的实际类型参数。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-592">If overload resolution chooses a generic method as the one to invoke, then the inferred type arguments are used as the actual type arguments for the invocation.</span></span> <span data-ttu-id="2e3bd-593">如果特定方法的类型推理失败，则该方法将不参与重载决策。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-593">If type inference for a particular method fails, that method does not participate in overload resolution.</span></span> <span data-ttu-id="2e3bd-594">类型推理的失败本身不会导致绑定时错误。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-594">The failure of type inference, in and of itself, does not cause a binding-time error.</span></span> <span data-ttu-id="2e3bd-595">但是，当重载决策失败并找不到任何适用的方法时，通常会导致绑定时错误。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-595">However, it often leads to a binding-time error when overload resolution then fails to find any applicable methods.</span></span>

<span data-ttu-id="2e3bd-596">如果所提供的参数数目不同于方法中的参数数量，则推理会立即失败。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-596">If the supplied number of arguments is different than the number of parameters in the method, then inference immediately fails.</span></span> <span data-ttu-id="2e3bd-597">否则，假定泛型方法具有以下签名：</span><span class="sxs-lookup"><span data-stu-id="2e3bd-597">Otherwise, assume that the generic method has the following signature:</span></span>
```csharp
Tr M<X1,...,Xn>(T1 x1, ..., Tm xm)
```

<span data-ttu-id="2e3bd-598">利用形式的方法调用， `M(E1...Em)` 类型推理的任务是查找每个类型参数的唯一类型参数， `S1...Sn` `X1...Xn` 以便调用 `M<S1...Sn>(E1...Em)` 变为有效。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-598">With a method call of the form `M(E1...Em)` the task of type inference is to find unique type arguments `S1...Sn` for each of the type parameters `X1...Xn` so that the call `M<S1...Sn>(E1...Em)` becomes valid.</span></span>

<span data-ttu-id="2e3bd-599">在推断过程中，每个类型形参 `Xi` 都 *固定* 到特定类型或未固定的一 `Si` 组 *边界*。 </span><span class="sxs-lookup"><span data-stu-id="2e3bd-599">During the process of inference each type parameter `Xi` is either *fixed* to a particular type `Si` or *unfixed* with an associated set of *bounds*.</span></span> <span data-ttu-id="2e3bd-600">每个边界都是某种类型 `T` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-600">Each of the bounds is some type `T`.</span></span> <span data-ttu-id="2e3bd-601">最初，每个类型变量 `Xi` 都不包含一组空的界限。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-601">Initially each type variable `Xi` is unfixed with an empty set of bounds.</span></span>

<span data-ttu-id="2e3bd-602">类型推理分阶段发生。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-602">Type inference takes place in phases.</span></span> <span data-ttu-id="2e3bd-603">每个阶段都将尝试根据上一阶段的发现来推导更多类型变量的类型参数。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-603">Each phase will try to infer type arguments for more type variables based on the findings of the previous phase.</span></span> <span data-ttu-id="2e3bd-604">第一阶段会对边界进行一些初始推断，而第二阶段则将类型变量修复为特定类型，并推导出进一步的边界。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-604">The first phase makes some initial inferences of bounds, whereas the second phase fixes type variables to specific types and infers further bounds.</span></span> <span data-ttu-id="2e3bd-605">第二阶段可能需要重复一次。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-605">The second phase may have to be repeated a number of times.</span></span>

<span data-ttu-id="2e3bd-606">*注意：* 仅当调用泛型方法时，才会发生类型推理。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-606">*Note:* Type inference takes place not only when a generic method is called.</span></span> <span data-ttu-id="2e3bd-607">用于转换方法组的类型推理中介绍了用于转换[方法](expressions.md#type-inference-for-conversion-of-method-groups)组的类型推理和查找一组表达式的最佳通用[类型中介绍的方法。](expressions.md#finding-the-best-common-type-of-a-set-of-expressions)</span><span class="sxs-lookup"><span data-stu-id="2e3bd-607">Type inference for conversion of method groups is described in [Type inference for conversion of method groups](expressions.md#type-inference-for-conversion-of-method-groups) and finding the best common type of a set of expressions is described in [Finding the best common type of a set of expressions](expressions.md#finding-the-best-common-type-of-a-set-of-expressions).</span></span>

#### <a name="the-first-phase"></a><span data-ttu-id="2e3bd-608">第一阶段</span><span class="sxs-lookup"><span data-stu-id="2e3bd-608">The first phase</span></span>

<span data-ttu-id="2e3bd-609">对于每个方法参数 `Ei` ：</span><span class="sxs-lookup"><span data-stu-id="2e3bd-609">For each of the method arguments `Ei`:</span></span>

*   <span data-ttu-id="2e3bd-610">如果 `Ei` 是匿名函数，则 *显式参数类型推理* ([显式参数类型](expressions.md#explicit-parameter-type-inferences) 推断，) 从 `Ei` 到 `Ti`</span><span class="sxs-lookup"><span data-stu-id="2e3bd-610">If `Ei` is an anonymous function, an *explicit parameter type inference* ([Explicit parameter type inferences](expressions.md#explicit-parameter-type-inferences)) is made from `Ei` to `Ti`</span></span>
*   <span data-ttu-id="2e3bd-611">否则，如果 `Ei` 具有类型 `U` 并且 `xi` 是值参数，则将 *从* 到进行 *下限推理* `U`  `Ti` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-611">Otherwise, if `Ei` has a type `U` and `xi` is a value parameter then a *lower-bound inference* is made *from* `U` *to* `Ti`.</span></span>
*   <span data-ttu-id="2e3bd-612">否则，如果 `Ei` 具有类型 `U` 并且 `xi` 为 `ref` 或参数， `out` 则将 *从* 到进行 *精确推理* `U`  `Ti` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-612">Otherwise, if `Ei` has a type `U` and `xi` is a `ref` or `out` parameter then an *exact inference* is made *from* `U` *to* `Ti`.</span></span>
*   <span data-ttu-id="2e3bd-613">否则，不会对此参数进行推理。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-613">Otherwise, no inference is made for this argument.</span></span>


#### <a name="the-second-phase"></a><span data-ttu-id="2e3bd-614">第二阶段</span><span class="sxs-lookup"><span data-stu-id="2e3bd-614">The second phase</span></span>

<span data-ttu-id="2e3bd-615">第二个阶段按如下方式进行：</span><span class="sxs-lookup"><span data-stu-id="2e3bd-615">The second phase proceeds as follows:</span></span>

*   <span data-ttu-id="2e3bd-616"> `Xi` 不 *依赖于*) 任何 ([依赖](expressions.md#dependence)关系的所有未固定类型变量 `Xj` 都固定 ([修复](expressions.md#fixing)) 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-616">All *unfixed* type variables `Xi` which do not *depend on* ([Dependence](expressions.md#dependence)) any `Xj` are fixed ([Fixing](expressions.md#fixing)).</span></span>
*   <span data-ttu-id="2e3bd-617">如果不存在这样的类型变量，则所有未 *固定的类型变量* `Xi` 都是 *固定* 的，所有这些变量都是以下类型：</span><span class="sxs-lookup"><span data-stu-id="2e3bd-617">If no such type variables exist, all *unfixed* type variables `Xi` are *fixed* for which all of the following hold:</span></span>
    *   <span data-ttu-id="2e3bd-618">至少有一个 `Xj` 依赖于的类型变量 `Xi`</span><span class="sxs-lookup"><span data-stu-id="2e3bd-618">There is at least one type variable `Xj` that depends on `Xi`</span></span>
    *   <span data-ttu-id="2e3bd-619">`Xi` 具有非空的界限集</span><span class="sxs-lookup"><span data-stu-id="2e3bd-619">`Xi` has a non-empty set of bounds</span></span>
*   <span data-ttu-id="2e3bd-620">如果不存在这样的类型变量，并且仍有未 *固定* 的类型变量，则类型推理将失败。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-620">If no such type variables exist and there are still *unfixed* type variables, type inference fails.</span></span>
*   <span data-ttu-id="2e3bd-621">否则，如果不存在进一步的未 *固定* 类型变量，则类型推理将成功。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-621">Otherwise, if no further *unfixed* type variables exist, type inference succeeds.</span></span>
*   <span data-ttu-id="2e3bd-622">否则，对于 `Ei` 具有相应参数类型的所有参数（ `Ti` 其中 *输出* 类型 [ (输出](expressions.md#output-types)类型) 包含未 *固定* 的类型变量但输入类型 `Xj` ([输入](expressions.md#input-types)类型) ），*输出类型推理* (*从* 到进行) [输出类型](expressions.md#output-type-inferences)推断 `Ei`  `Ti` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-622">Otherwise, for all arguments `Ei` with corresponding parameter type `Ti` where the *output types* ([Output types](expressions.md#output-types)) contain *unfixed* type variables `Xj` but the *input types* ([Input types](expressions.md#input-types)) do not, an *output type inference* ([Output type inferences](expressions.md#output-type-inferences)) is made *from* `Ei` *to* `Ti`.</span></span> <span data-ttu-id="2e3bd-623">然后，重复第二阶段。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-623">Then the second phase is repeated.</span></span>

#### <a name="input-types"></a><span data-ttu-id="2e3bd-624">输入类型</span><span class="sxs-lookup"><span data-stu-id="2e3bd-624">Input types</span></span>

<span data-ttu-id="2e3bd-625">如果 `E` 是方法组或隐式类型的匿名函数，并且 `T` 是委托类型或表达式树类型，则的所有参数类型 `T` 均为类型为的 *输入类型* `E`  `T` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-625">If `E` is a method group or implicitly typed anonymous function and `T` is a delegate type or expression tree type then all the parameter types of `T` are *input types* of `E` *with type* `T`.</span></span>

####  <a name="output-types"></a><span data-ttu-id="2e3bd-626">输出类型</span><span class="sxs-lookup"><span data-stu-id="2e3bd-626">Output types</span></span>

<span data-ttu-id="2e3bd-627">如果 `E` 是方法组或匿名函数，并且 `T` 是委托类型或表达式树类型，则的返回类型 `T` 为类型为 *的输出类型* `E`  `T` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-627">If `E` is a method group or an anonymous function and `T` is a delegate type or expression tree type then the return type of `T` is an *output type of* `E` *with type* `T`.</span></span>

#### <a name="dependence"></a><span data-ttu-id="2e3bd-628">依赖关系</span><span class="sxs-lookup"><span data-stu-id="2e3bd-628">Dependence</span></span>

<span data-ttu-id="2e3bd-629"> `Xi` 如果对于类型为的某个参数出现在类型为的 `Xj` `Ek` `Tk` `Xj` *输入类型* 中 `Ek` 且在类型为 `Tk` `Xi` 的 *输出类型* 中 `Ek` 出现 `Tk` ，则未固定类型变量直接依赖于未固定的类型变量。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-629">An *unfixed* type variable `Xi` *depends directly on* an unfixed type variable `Xj` if for some argument `Ek` with type `Tk` `Xj` occurs in an *input type* of `Ek` with type `Tk` and `Xi` occurs in an *output type* of `Ek` with type `Tk`.</span></span>

<span data-ttu-id="2e3bd-630">`Xj`*依赖于* `Xi`如果 `Xj` *直接依赖* `Xi` ，则为; 如果 `Xi` *直接* 依赖 `Xk` ， `Xk`  `Xj` 则依赖于。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-630">`Xj` *depends on* `Xi` if `Xj` *depends directly on* `Xi` or if `Xi` *depends directly on* `Xk` and `Xk` *depends on* `Xj`.</span></span> <span data-ttu-id="2e3bd-631">因此 "依赖于" 是传递的，而不是 "直接依赖于" 的反身闭包。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-631">Thus "depends on" is the transitive but not reflexive closure of "depends directly on".</span></span>

#### <a name="output-type-inferences"></a><span data-ttu-id="2e3bd-632">输出类型推断</span><span class="sxs-lookup"><span data-stu-id="2e3bd-632">Output type inferences</span></span>

<span data-ttu-id="2e3bd-633">*从* 表达式到类型的 *输出类型推理* `E`  `T` 按以下方式进行：</span><span class="sxs-lookup"><span data-stu-id="2e3bd-633">An *output type inference* is made *from* an expression `E` *to* a type `T` in the following way:</span></span>

*  <span data-ttu-id="2e3bd-634">如果 `E` 是具有推断返回类型的匿名函数 `U` ([推断出的返回](expressions.md#inferred-return-type)类型) 并且 `T` 是具有返回类型的委托类型或表达式树类型 `Tb` ，则 *从* 到进行) [下限](expressions.md#lower-bound-inferences)*推理* (`U`  `Tb` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-634">If `E` is an anonymous function with inferred return type  `U` ([Inferred return type](expressions.md#inferred-return-type)) and `T` is a delegate type or expression tree type with return type `Tb`, then a *lower-bound inference* ([Lower-bound inferences](expressions.md#lower-bound-inferences)) is made *from* `U` *to* `Tb`.</span></span>
*  <span data-ttu-id="2e3bd-635">否则，如果 `E` 是方法组，并且 `T` 是具有参数类型和返回类型的委托类型或表达式树类型 `T1...Tk` `Tb` ，并且具有类型的重载解析 `E` `T1...Tk` 生成具有返回类型的单个方法 `U` ，则将 *从* 到进行 *下限推理* `U`  `Tb` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-635">Otherwise, if `E` is a method group and `T` is a delegate type or expression tree type with parameter types `T1...Tk` and return type `Tb`, and overload resolution of `E` with the types `T1...Tk` yields a single method with return type `U`, then a *lower-bound inference* is made *from* `U` *to* `Tb`.</span></span>
*  <span data-ttu-id="2e3bd-636">否则，如果 `E` 是类型为的表达式 `U` ，则将 *从* 到进行 *下限推理* `U`  `T` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-636">Otherwise, if `E` is an expression with type `U`, then a *lower-bound inference* is made *from* `U` *to* `T`.</span></span>
*  <span data-ttu-id="2e3bd-637">否则，不进行推断。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-637">Otherwise, no inferences are made.</span></span>

#### <a name="explicit-parameter-type-inferences"></a><span data-ttu-id="2e3bd-638">显式参数类型推断</span><span class="sxs-lookup"><span data-stu-id="2e3bd-638">Explicit parameter type inferences</span></span>

<span data-ttu-id="2e3bd-639">*从* 表达式到类型的 *显式参数类型推理* `E`  `T` 按以下方式进行：</span><span class="sxs-lookup"><span data-stu-id="2e3bd-639">An *explicit parameter type inference* is made *from* an expression `E` *to* a type `T` in the following way:</span></span>

*  <span data-ttu-id="2e3bd-640">如果 `E` 是使用参数类型的显式类型化匿名函数 `U1...Uk` ，并且 `T` 是具有参数类型的委托类型或表达式树类型， `V1...Vk` 则对于每个 `Ui` *精确推理* ([精确](expressions.md#exact-inferences) 推断) *从* `Ui` *到* 相应的 `Vi` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-640">If `E` is an explicitly typed anonymous function with parameter types `U1...Uk` and `T` is a delegate type or expression tree type with parameter types `V1...Vk` then for each `Ui` an *exact inference* ([Exact inferences](expressions.md#exact-inferences)) is made *from* `Ui` *to* the corresponding `Vi`.</span></span>

#### <a name="exact-inferences"></a><span data-ttu-id="2e3bd-641">精确推断</span><span class="sxs-lookup"><span data-stu-id="2e3bd-641">Exact inferences</span></span>

<span data-ttu-id="2e3bd-642">*从* 类型到类型的 *精确推理* 如下所示 `U`  `V` ：</span><span class="sxs-lookup"><span data-stu-id="2e3bd-642">An *exact inference* *from* a type `U` *to* a type `V` is made as follows:</span></span>

*  <span data-ttu-id="2e3bd-643">如果 `V` 是一个未 *固定* 的，则 `Xi` `U` 会将添加到的一组完全限定 `Xi` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-643">If `V` is one of the *unfixed* `Xi` then `U` is added to the set of exact bounds for `Xi`.</span></span>

*  <span data-ttu-id="2e3bd-644">否则， `V1...Vk` `U1...Uk` 通过检查是否存在以下任何情况，确定了和的设置：</span><span class="sxs-lookup"><span data-stu-id="2e3bd-644">Otherwise, sets `V1...Vk` and `U1...Uk` are determined by checking if any of the following cases apply:</span></span>

   *  <span data-ttu-id="2e3bd-645">`V`是数组类型 `V1[...]` ，并且 `U` 是同一秩的数组类型 `U1[...]`</span><span class="sxs-lookup"><span data-stu-id="2e3bd-645">`V` is an array type `V1[...]` and `U` is an array type `U1[...]`  of the same rank</span></span>
   *  <span data-ttu-id="2e3bd-646">`V` 的类型为 `V1?` ， `U` 类型为 `U1?`</span><span class="sxs-lookup"><span data-stu-id="2e3bd-646">`V` is the type `V1?` and `U` is the type `U1?`</span></span>
   *  <span data-ttu-id="2e3bd-647">`V` 是构造类型 `C<V1...Vk>` ， `U` 是构造类型 `C<U1...Uk>`</span><span class="sxs-lookup"><span data-stu-id="2e3bd-647">`V` is a constructed type `C<V1...Vk>`and `U` is a constructed type `C<U1...Uk>`</span></span>

   <span data-ttu-id="2e3bd-648">如果这些情况中的任何一个都适用，则会 *从* 每个类到相应的进行 *精确推断* `Ui`  `Vi` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-648">If any of these cases apply then an *exact inference* is made *from* each `Ui` *to* the corresponding `Vi`.</span></span>

*  <span data-ttu-id="2e3bd-649">否则，不进行推断。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-649">Otherwise no inferences are made.</span></span>

#### <a name="lower-bound-inferences"></a><span data-ttu-id="2e3bd-650">下限推理</span><span class="sxs-lookup"><span data-stu-id="2e3bd-650">Lower-bound inferences</span></span>

<span data-ttu-id="2e3bd-651">*从* 类型到类型的 *下限推理* 如下所示 `U`  `V` ：</span><span class="sxs-lookup"><span data-stu-id="2e3bd-651">A *lower-bound inference* *from* a type `U` *to* a type `V` is made as follows:</span></span>

*  <span data-ttu-id="2e3bd-652">如果 `V` 是一个未 *固定* 的，则 `Xi` `U` 会将添加到的下限集 `Xi` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-652">If `V` is one of the *unfixed* `Xi` then `U` is added to the set of lower bounds for `Xi`.</span></span>
*  <span data-ttu-id="2e3bd-653">否则，如果 `V` 为类型 `V1?` ，并且 `U` 为类型， `U1?` 则从到的位置进行下限推理 `U1` `V1` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-653">Otherwise, if `V` is the type `V1?`and `U` is the type `U1?` then a lower bound inference is made from `U1` to `V1`.</span></span>
*  <span data-ttu-id="2e3bd-654">否则， `U1...Uk` `V1...Vk` 通过检查是否存在以下任何情况，确定了和的设置：</span><span class="sxs-lookup"><span data-stu-id="2e3bd-654">Otherwise, sets `U1...Uk` and `V1...Vk` are determined by checking if any of the following cases apply:</span></span>
   *  <span data-ttu-id="2e3bd-655">`V` 是数组类型 `V1[...]` ， `U` 是 (的数组类型， `U1[...]` 或是其有效基类型 `U1[...]`) 相同级别的类型参数</span><span class="sxs-lookup"><span data-stu-id="2e3bd-655">`V` is an array type `V1[...]` and `U` is an array type `U1[...]` (or a type parameter whose effective base type is `U1[...]`) of the same rank</span></span>
   *  <span data-ttu-id="2e3bd-656">`V`为 `IEnumerable<V1>` ，或为 `ICollection<V1>` 一 `IList<V1>` `U` 维数组类型 `U1[]` (或有效基类型为) 的类型参数 `U1[]`</span><span class="sxs-lookup"><span data-stu-id="2e3bd-656">`V` is one of `IEnumerable<V1>`, `ICollection<V1>` or `IList<V1>` and `U` is a one-dimensional array type `U1[]`(or a type parameter whose effective base type is `U1[]`)</span></span>
   *  <span data-ttu-id="2e3bd-657">`V` 是一个构造的类、结构、接口或委托类型 `C<V1...Vk>` ，并且有一个唯一 `C<U1...Uk>` 的类型，这样 `U` (或者，如果 `U` 是一个类型参数，则它的有效的基类或其有效接口集) 的任何成员完全相同、继承自 (直接或间接) ，或直接或间接 () `C<U1...Uk>` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-657">`V` is a constructed class, struct, interface or delegate type `C<V1...Vk>` and there is a unique type `C<U1...Uk>` such that `U` (or, if `U` is a type parameter, its effective base class or any member of its effective interface set) is identical to, inherits from (directly or indirectly), or implements (directly or indirectly) `C<U1...Uk>`.</span></span>

      <span data-ttu-id="2e3bd-658"> ("唯一性" 限制意味着在 case 接口中 `C<T> {} class U: C<X>, C<Y> {}` ，当从推断到时，不会进行推理 `U` ， `C<T>` 因为 `U1` 可能是 `X` 或 `Y` 。 ) </span><span class="sxs-lookup"><span data-stu-id="2e3bd-658">(The "uniqueness" restriction means that in the case interface `C<T> {} class U: C<X>, C<Y> {}`, then no inference is made when inferring from `U` to `C<T>` because `U1` could be `X` or `Y`.)</span></span>

   <span data-ttu-id="2e3bd-659">如果这些情况中的任何一个都适用，则将 *从* 每个的推理 `Ui` *到* 对应的，如下所示 `Vi` ：</span><span class="sxs-lookup"><span data-stu-id="2e3bd-659">If any of these cases apply then an inference is made *from* each `Ui` *to* the corresponding `Vi` as follows:</span></span>

   *  <span data-ttu-id="2e3bd-660">如果 `Ui` 不知道是引用类型，则进行 *精确推理*</span><span class="sxs-lookup"><span data-stu-id="2e3bd-660">If `Ui` is not known to be a reference type then an *exact inference* is made</span></span>
   *  <span data-ttu-id="2e3bd-661">否则，如果 `U` 是数组类型，则进行 *下限推理*</span><span class="sxs-lookup"><span data-stu-id="2e3bd-661">Otherwise, if `U` is an array type then a *lower-bound inference* is made</span></span>
   *  <span data-ttu-id="2e3bd-662">否则，如果 `V` 为， `C<V1...Vk>` 则推理依赖于的第 i 个类型参数 `C` ：</span><span class="sxs-lookup"><span data-stu-id="2e3bd-662">Otherwise, if `V` is `C<V1...Vk>` then inference depends on the i-th type parameter of `C`:</span></span>
      *  <span data-ttu-id="2e3bd-663">如果它是协变的，则进行 *下限推理* 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-663">If it is covariant then a *lower-bound inference* is made.</span></span>
      *  <span data-ttu-id="2e3bd-664">如果它是逆变的，则进行 *上限推断* 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-664">If it is contravariant then an *upper-bound inference* is made.</span></span>
      *  <span data-ttu-id="2e3bd-665">如果它是固定的，则进行 *精确推理* 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-665">If it is invariant then an *exact inference* is made.</span></span>
*  <span data-ttu-id="2e3bd-666">否则，不进行推断。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-666">Otherwise, no inferences are made.</span></span>

#### <a name="upper-bound-inferences"></a><span data-ttu-id="2e3bd-667">上限推理</span><span class="sxs-lookup"><span data-stu-id="2e3bd-667">Upper-bound inferences</span></span>

<span data-ttu-id="2e3bd-668">*从* 类型到类型的 *上限推理* 如下所示 `U`  `V` ：</span><span class="sxs-lookup"><span data-stu-id="2e3bd-668">An *upper-bound inference* *from* a type `U` *to* a type `V` is made as follows:</span></span>

*  <span data-ttu-id="2e3bd-669">如果 `V` 是一个未 *固定* 的，则 `Xi` `U` 会将添加到的上限集 `Xi` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-669">If `V` is one of the *unfixed* `Xi` then `U` is added to the set of upper bounds for `Xi`.</span></span>
*  <span data-ttu-id="2e3bd-670">否则， `V1...Vk` `U1...Uk` 通过检查是否存在以下任何情况，确定了和的设置：</span><span class="sxs-lookup"><span data-stu-id="2e3bd-670">Otherwise, sets `V1...Vk` and `U1...Uk` are determined by checking if any of the following cases apply:</span></span>
   *  <span data-ttu-id="2e3bd-671">`U`是数组类型 `U1[...]` ，并且 `V` 是同一秩的数组类型 `V1[...]`</span><span class="sxs-lookup"><span data-stu-id="2e3bd-671">`U` is an array type `U1[...]` and `V` is an array type `V1[...]` of the same rank</span></span>
   *  <span data-ttu-id="2e3bd-672">`U` 为 `IEnumerable<Ue>` ，或为 `ICollection<Ue>` 一 `IList<Ue>` `V` 维数组类型 `Ve[]`</span><span class="sxs-lookup"><span data-stu-id="2e3bd-672">`U` is one of `IEnumerable<Ue>`, `ICollection<Ue>` or `IList<Ue>` and `V` is a one-dimensional array type `Ve[]`</span></span>
   *  <span data-ttu-id="2e3bd-673">`U` 的类型为 `U1?` ， `V` 类型为 `V1?`</span><span class="sxs-lookup"><span data-stu-id="2e3bd-673">`U` is the type `U1?` and `V` is the type `V1?`</span></span>
   *  <span data-ttu-id="2e3bd-674">`U` 是构造的类、结构、接口或委托类型， `C<U1...Uk>` 并且 `V` 是与相同的类、结构、接口或委托类型，继承自 (直接或间接) ，或直接或间接实现 () 唯一类型 `C<V1...Vk>`</span><span class="sxs-lookup"><span data-stu-id="2e3bd-674">`U` is constructed class, struct, interface or delegate type `C<U1...Uk>` and `V` is a class, struct, interface or delegate type which is identical to, inherits from (directly or indirectly), or implements (directly or indirectly) a unique type `C<V1...Vk>`</span></span>

      <span data-ttu-id="2e3bd-675"> ("唯一性" 限制，这意味着，如果有 `interface C<T>{} class V<Z>: C<X<Z>>, C<Y<Z>>{}` ，则从推导到时不进行推理 `C<U1>` `V<Q>` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-675">(The "uniqueness" restriction means that if we have `interface C<T>{} class V<Z>: C<X<Z>>, C<Y<Z>>{}`, then no inference is made when inferring from `C<U1>` to `V<Q>`.</span></span> <span data-ttu-id="2e3bd-676">不会从或中进行推断 `U1` `X<Q>` `Y<Q>` 。 ) </span><span class="sxs-lookup"><span data-stu-id="2e3bd-676">Inferences are not made from `U1` to either `X<Q>` or `Y<Q>`.)</span></span>

   <span data-ttu-id="2e3bd-677">如果这些情况中的任何一个都适用，则将 *从* 每个的推理 `Ui` *到* 对应的，如下所示 `Vi` ：</span><span class="sxs-lookup"><span data-stu-id="2e3bd-677">If any of these cases apply then an inference is made *from* each `Ui` *to* the corresponding `Vi` as follows:</span></span>
   *  <span data-ttu-id="2e3bd-678">如果 `Ui` 不知道是引用类型，则进行 *精确推理*</span><span class="sxs-lookup"><span data-stu-id="2e3bd-678">If  `Ui` is not known to be a reference type then an *exact inference* is made</span></span>
   *  <span data-ttu-id="2e3bd-679">否则，如果 `V` 是数组类型，则进行 *上限推理*</span><span class="sxs-lookup"><span data-stu-id="2e3bd-679">Otherwise, if `V` is an array type then an *upper-bound inference* is made</span></span>
   *  <span data-ttu-id="2e3bd-680">否则，如果 `U` 为， `C<U1...Uk>` 则推理依赖于的第 i 个类型参数 `C` ：</span><span class="sxs-lookup"><span data-stu-id="2e3bd-680">Otherwise, if `U` is `C<U1...Uk>` then inference depends on the i-th type parameter of `C`:</span></span>
      *  <span data-ttu-id="2e3bd-681">如果它是协变的，则进行 *上限推理* 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-681">If it is covariant then an *upper-bound inference* is made.</span></span>
      *  <span data-ttu-id="2e3bd-682">如果它是逆变的，则进行 *下限推理* 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-682">If it is contravariant then a *lower-bound inference* is made.</span></span>
      *  <span data-ttu-id="2e3bd-683">如果它是固定的，则进行 *精确推理* 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-683">If it is invariant then an *exact inference* is made.</span></span>
*  <span data-ttu-id="2e3bd-684">否则，不进行推断。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-684">Otherwise, no inferences are made.</span></span>   

#### <a name="fixing"></a><span data-ttu-id="2e3bd-685">修正 </span><span class="sxs-lookup"><span data-stu-id="2e3bd-685">Fixing</span></span>

<span data-ttu-id="2e3bd-686">具有一组界限的未 *固定* 类型变量 `Xi` *固定* 如下：</span><span class="sxs-lookup"><span data-stu-id="2e3bd-686">An *unfixed* type variable `Xi` with a set of bounds is *fixed* as follows:</span></span>

*  <span data-ttu-id="2e3bd-687">*候选类型* 集 `Uj` 作为的边界集中的所有类型的集合开始 `Xi` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-687">The set of *candidate types* `Uj` starts out as the set of all types in the set of bounds for `Xi`.</span></span>
*  <span data-ttu-id="2e3bd-688">接下来，我们依次检查每个绑定 `Xi` ：对于与 `U` `Xi` `Uj` `U` 从候选集删除的所有类型不同的每个完全绑定，都是如此。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-688">We then examine each bound for `Xi` in turn: For each exact bound `U` of `Xi` all types `Uj` which are not identical to `U` are removed from the candidate set.</span></span> <span data-ttu-id="2e3bd-689">对于 `U` `Xi` 不从其隐式转换的所有类型的每个下限， `Uj`  `U` 从候选集删除。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-689">For each lower bound `U` of `Xi` all types `Uj` to which there is *not* an implicit conversion from `U` are removed from the candidate set.</span></span> <span data-ttu-id="2e3bd-690">对于 `U` 不将隐式转换为的所有类型的每个上限，从 `Xi` `Uj`  `U` 候选集删除。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-690">For each upper bound `U` of `Xi` all types `Uj` from which there is *not* an implicit conversion to `U` are removed from the candidate set.</span></span>
*  <span data-ttu-id="2e3bd-691">如果其余的候选类型中 `Uj` 有一个唯一类型 `V` ，其中存在到所有其他候选类型的隐式转换，则 `Xi` 修复为 `V` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-691">If among the remaining candidate types `Uj` there is a unique type `V` from which there is an implicit conversion to all the other candidate types, then `Xi` is fixed to `V`.</span></span>
*  <span data-ttu-id="2e3bd-692">否则，类型推理将失败。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-692">Otherwise, type inference fails.</span></span>

#### <a name="inferred-return-type"></a><span data-ttu-id="2e3bd-693">推断出的返回类型</span><span class="sxs-lookup"><span data-stu-id="2e3bd-693">Inferred return type</span></span>

<span data-ttu-id="2e3bd-694">在 `F` 类型推理和重载决策过程中，将使用推断出的匿名函数的返回类型。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-694">The inferred return type of an anonymous function `F` is used during type inference and overload resolution.</span></span> <span data-ttu-id="2e3bd-695">只能为所有参数类型已知的匿名函数确定推理出的返回类型，这可能是因为显式给定了这些参数类型，或者是通过匿名函数转换提供的，或者是在封闭泛型方法调用的类型推理过程中推断出来的。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-695">The inferred return type can only be determined for an anonymous function where all parameter types are known, either because they are explicitly given, provided through an anonymous function conversion or inferred during type inference on an enclosing generic method invocation.</span></span>

<span data-ttu-id="2e3bd-696">按如下方式确定 ***推断结果类型*** ：</span><span class="sxs-lookup"><span data-stu-id="2e3bd-696">The ***inferred result type*** is determined as follows:</span></span>

*  <span data-ttu-id="2e3bd-697">如果的主体 `F` 是具有类型的 *表达式* ，则推断出的结果类型 `F` 是该表达式的类型。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-697">If the body of `F` is an *expression* that has a type, then the inferred result type of `F` is the type of that expression.</span></span>
*  <span data-ttu-id="2e3bd-698">如果的主体 `F` 为 *块* ，并且块的语句中的表达式集 `return` 具有最佳通用类型 `T` () 中 [查找一组表达式的最佳通用类型](expressions.md#finding-the-best-common-type-of-a-set-of-expressions) ，则推断出的结果类型 `F` 为 `T` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-698">If the body of `F` is a *block* and the set of expressions in the block's `return` statements has a best common type `T` ([Finding the best common type of a set of expressions](expressions.md#finding-the-best-common-type-of-a-set-of-expressions)), then the inferred result type of `F` is `T`.</span></span>
*  <span data-ttu-id="2e3bd-699">否则，无法为推断结果类型 `F` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-699">Otherwise, a result type cannot be inferred for `F`.</span></span>

<span data-ttu-id="2e3bd-700">按如下方式确定 ***推断出的返回类型*** ：</span><span class="sxs-lookup"><span data-stu-id="2e3bd-700">The ***inferred return type*** is determined as follows:</span></span>

*  <span data-ttu-id="2e3bd-701">如果 `F` 是异步的，并且的主体 `F` 是被归类为 Nothing ([expression 分类](expressions.md#expression-classifications)) 的表达式，或者是没有 return 语句的语句块具有表达式，则推断出的返回类型为 `System.Threading.Tasks.Task`</span><span class="sxs-lookup"><span data-stu-id="2e3bd-701">If `F` is async and the body of `F` is either an expression classified as nothing ([Expression classifications](expressions.md#expression-classifications)), or a statement block where no return statements have expressions, the inferred return type is `System.Threading.Tasks.Task`</span></span>
*  <span data-ttu-id="2e3bd-702">如果 `F` 为 async 并且具有推理结果类型 `T` ，则推断出的返回类型为 `System.Threading.Tasks.Task<T>` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-702">If `F` is async and has an inferred result type `T`, the inferred return type is `System.Threading.Tasks.Task<T>`.</span></span>
*  <span data-ttu-id="2e3bd-703">如果是非 `F` async 并且具有推断结果类型 `T` ，则推断出的返回类型为 `T` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-703">If `F` is non-async and has an inferred result type `T`, the inferred return type is `T`.</span></span>
*  <span data-ttu-id="2e3bd-704">否则，将无法推断返回类型 `F` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-704">Otherwise a return type cannot be inferred for `F`.</span></span>

<span data-ttu-id="2e3bd-705">作为涉及匿名函数的类型推理的示例，请考虑 `Select` 在类中声明的扩展方法 `System.Linq.Enumerable` ：</span><span class="sxs-lookup"><span data-stu-id="2e3bd-705">As an example of type inference involving anonymous functions, consider the `Select` extension method declared in the `System.Linq.Enumerable` class:</span></span>
```csharp
namespace System.Linq
{
    public static class Enumerable
    {
        public static IEnumerable<TResult> Select<TSource,TResult>(
            this IEnumerable<TSource> source,
            Func<TSource,TResult> selector)
        {
            foreach (TSource element in source) yield return selector(element);
        }
    }
}
```

<span data-ttu-id="2e3bd-706">假设 `System.Linq` 命名空间是使用子句导入的 `using` ，并且给定 `Customer` 具有类型为 `Name` 的属性的类 `string` ，则 `Select` 可使用方法选择客户列表的名称：</span><span class="sxs-lookup"><span data-stu-id="2e3bd-706">Assuming the `System.Linq` namespace was imported with a `using` clause, and given a class `Customer` with a `Name` property of type `string`, the `Select` method can be used to select the names of a list of customers:</span></span>
```csharp
List<Customer> customers = GetCustomerList();
IEnumerable<string> names = customers.Select(c => c.Name);
```

<span data-ttu-id="2e3bd-707">通过将调用重写为静态方法调用来处理 ([扩展方法](expressions.md#extension-method-invocations) 调用) 的扩展方法调用 `Select` ：</span><span class="sxs-lookup"><span data-stu-id="2e3bd-707">The extension method invocation ([Extension method invocations](expressions.md#extension-method-invocations)) of `Select` is processed by rewriting the invocation to a static method invocation:</span></span>
```csharp
IEnumerable<string> names = Enumerable.Select(customers, c => c.Name);
```

<span data-ttu-id="2e3bd-708">由于未显式指定类型参数，因此将使用类型推理来推断类型参数。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-708">Since type arguments were not explicitly specified, type inference is used to infer the type arguments.</span></span> <span data-ttu-id="2e3bd-709">首先， `customers` 自变量与 `source` 参数相关，并推断 `T` 为 `Customer` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-709">First, the `customers` argument is related to the `source` parameter, inferring `T` to be `Customer`.</span></span> <span data-ttu-id="2e3bd-710">然后，使用上面所述的匿名函数类型推理过程， `c` 指定类型 `Customer` ，并且表达式 `c.Name` 与参数的返回类型 `selector` （推断为）相关 `S` `string` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-710">Then, using the anonymous function type inference process described above, `c` is given type `Customer`, and the expression `c.Name` is related to the return type of the `selector` parameter, inferring `S` to be `string`.</span></span> <span data-ttu-id="2e3bd-711">因此，调用等效于</span><span class="sxs-lookup"><span data-stu-id="2e3bd-711">Thus, the invocation is equivalent to</span></span>
```csharp
Sequence.Select<Customer,string>(customers, (Customer c) => c.Name)
```
<span data-ttu-id="2e3bd-712">结果的类型为 `IEnumerable<string>` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-712">and the result is of type `IEnumerable<string>`.</span></span>

<span data-ttu-id="2e3bd-713">下面的示例演示匿名函数类型推理如何允许类型信息在泛型方法调用中的参数之间 "流"。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-713">The following example demonstrates how anonymous function type inference allows type information to "flow" between arguments in a generic method invocation.</span></span> <span data-ttu-id="2e3bd-714">给定方法：</span><span class="sxs-lookup"><span data-stu-id="2e3bd-714">Given the method:</span></span>
```csharp
static Z F<X,Y,Z>(X value, Func<X,Y> f1, Func<Y,Z> f2) {
    return f2(f1(value));
}
```

<span data-ttu-id="2e3bd-715">调用的类型推理：</span><span class="sxs-lookup"><span data-stu-id="2e3bd-715">Type inference for the invocation:</span></span>
```csharp
double seconds = F("1:15:30", s => TimeSpan.Parse(s), t => t.TotalSeconds);
```
<span data-ttu-id="2e3bd-716">按如下方式继续：首先，自变量 `"1:15:30"` 与参数相关 `value` ，推断 `X` 为 `string` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-716">proceeds as follows: First, the argument `"1:15:30"` is related to the `value` parameter, inferring `X` to be `string`.</span></span> <span data-ttu-id="2e3bd-717">然后，为第一个匿名函数的参数 `s` 提供推断类型 `string` ，且表达式 `TimeSpan.Parse(s)` 与的返回类型 `f1` （推断为）相关 `Y` `System.TimeSpan` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-717">Then, the parameter of the first anonymous function, `s`, is given the inferred type `string`, and the expression `TimeSpan.Parse(s)` is related to the return type of `f1`, inferring `Y` to be `System.TimeSpan`.</span></span> <span data-ttu-id="2e3bd-718">最后，为第二个匿名函数的参数 `t` 提供推断类型 `System.TimeSpan` ，且表达式 `t.TotalSeconds` 与的返回类型 `f2` （推断为）相关 `Z` `double` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-718">Finally, the parameter of the second anonymous function, `t`, is given the inferred type `System.TimeSpan`, and the expression `t.TotalSeconds` is related to the return type of `f2`, inferring `Z` to be `double`.</span></span> <span data-ttu-id="2e3bd-719">因此，调用的结果为类型 `double` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-719">Thus, the result of the invocation is of type `double`.</span></span>

#### <a name="type-inference-for-conversion-of-method-groups"></a><span data-ttu-id="2e3bd-720">用于转换方法组的类型推理</span><span class="sxs-lookup"><span data-stu-id="2e3bd-720">Type inference for conversion of method groups</span></span>

<span data-ttu-id="2e3bd-721">与泛型方法的调用类似，当包含泛型方法的方法组转换为给定委托类型时，还必须应用类型推理 `M` `D`)  ([方法组转换](conversions.md#method-group-conversions) 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-721">Similar to calls of generic methods, type inference must also be applied when a method group `M` containing a generic method is converted to a given delegate type `D` ([Method group conversions](conversions.md#method-group-conversions)).</span></span> <span data-ttu-id="2e3bd-722">给定方法</span><span class="sxs-lookup"><span data-stu-id="2e3bd-722">Given a method</span></span>
```csharp
Tr M<X1...Xn>(T1 x1 ... Tm xm)
```
<span data-ttu-id="2e3bd-723">并且要将方法组 `M` 分配给委托类型， `D` 推断类型的任务是查找类型参数， `S1...Sn` 使表达式：</span><span class="sxs-lookup"><span data-stu-id="2e3bd-723">and the method group `M` being assigned to the delegate type `D` the task of type inference is to find type arguments `S1...Sn` so that the expression:</span></span>
```csharp
M<S1...Sn>
```
<span data-ttu-id="2e3bd-724">与)  ([委托声明](delegates.md#delegate-declarations) 是兼容的 `D` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-724">becomes compatible ([Delegate declarations](delegates.md#delegate-declarations)) with `D`.</span></span>

<span data-ttu-id="2e3bd-725">与泛型方法调用的类型推理算法不同，在本例中，只有参数 *类型*，没有参数 *表达式*。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-725">Unlike the type inference algorithm for generic method calls, in this case there are only argument *types*, no argument *expressions*.</span></span> <span data-ttu-id="2e3bd-726">具体而言，不存在任何匿名函数，因此不需要进行多个推理阶段。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-726">In particular, there are no anonymous functions and hence no need for multiple phases of inference.</span></span>

<span data-ttu-id="2e3bd-727">相反，所有 `Xi` 都被视为未 *固定* 的，而 *从* 的每个参数类型到的相应参数类型，都将进行 *下限推理* `Uj` `D`  `Tj` `M` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-727">Instead, all `Xi` are considered *unfixed*, and a *lower-bound inference* is made *from* each argument type `Uj` of `D` *to* the corresponding parameter type `Tj` of `M`.</span></span> <span data-ttu-id="2e3bd-728">如果 `Xi` 未找到任何边界，则类型推理将失败。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-728">If for any of the `Xi` no bounds were found, type inference fails.</span></span> <span data-ttu-id="2e3bd-729">否则，所有的都 `Xi` 是 *固定* 的 `Si` ，这是类型推理的结果。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-729">Otherwise, all `Xi` are *fixed* to corresponding `Si`, which are the result of type inference.</span></span>

#### <a name="finding-the-best-common-type-of-a-set-of-expressions"></a><span data-ttu-id="2e3bd-730">查找一组表达式的最佳通用类型</span><span class="sxs-lookup"><span data-stu-id="2e3bd-730">Finding the best common type of a set of expressions</span></span>

<span data-ttu-id="2e3bd-731">在某些情况下，需要为一组表达式推断通用类型。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-731">In some cases, a common type needs to be inferred for a set of expressions.</span></span> <span data-ttu-id="2e3bd-732">特别是，隐式类型化数组的元素类型和带 *块* 体的匿名函数的返回类型是通过这种方式找到的。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-732">In particular, the element types of implicitly typed arrays and the return types of anonymous functions with *block* bodies are found in this way.</span></span>

<span data-ttu-id="2e3bd-733">直观地说，假设 `E1...Em` 此推理应等效于调用方法</span><span class="sxs-lookup"><span data-stu-id="2e3bd-733">Intuitively, given a set of expressions `E1...Em` this inference should be equivalent to calling a method</span></span>
```csharp
Tr M<X>(X x1 ... X xm)
```
<span data-ttu-id="2e3bd-734">具有 `Ei` 作为参数。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-734">with the `Ei` as arguments.</span></span>

<span data-ttu-id="2e3bd-735">更准确地说，推理是使用未 *固定* 的类型变量开始的 `X` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-735">More precisely, the inference starts out with an *unfixed* type variable `X`.</span></span> <span data-ttu-id="2e3bd-736">然后 *从* 每个到进行 *输出类型推断* `Ei`  `X` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-736">*Output type inferences* are then made *from* each `Ei` *to* `X`.</span></span> <span data-ttu-id="2e3bd-737">最后， `X` 是 *固定* 的，如果成功，则生成的类型 `S` 为表达式得到的最佳通用类型。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-737">Finally, `X` is *fixed* and, if successful, the resulting type `S` is the resulting best common type for the expressions.</span></span> <span data-ttu-id="2e3bd-738">如果不存在这种情况 `S` ，则表达式没有最佳通用类型。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-738">If no such `S` exists, the expressions have no best common type.</span></span>

### <a name="overload-resolution"></a><span data-ttu-id="2e3bd-739">重载决策</span><span class="sxs-lookup"><span data-stu-id="2e3bd-739">Overload resolution</span></span>

<span data-ttu-id="2e3bd-740">重载决策是用于选择在给定参数列表和一组候选函数成员时调用的最佳函数成员的绑定时机制。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-740">Overload resolution is a binding-time mechanism for selecting the best function member to invoke given an argument list and a set of candidate function members.</span></span> <span data-ttu-id="2e3bd-741">重载决策选择要在 c # 内的下列不同上下文中调用的函数成员：</span><span class="sxs-lookup"><span data-stu-id="2e3bd-741">Overload resolution selects the function member to invoke in the following distinct contexts within C#:</span></span>

*  <span data-ttu-id="2e3bd-742">调用 *invocation_expression* 中名为的方法 ([方法调用](expressions.md#method-invocations)) 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-742">Invocation of a method named in an *invocation_expression* ([Method invocations](expressions.md#method-invocations)).</span></span>
*  <span data-ttu-id="2e3bd-743">) *object_creation_expression* ([对象创建表达式](expressions.md#object-creation-expressions) 中名为的实例构造函数的调用。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-743">Invocation of an instance constructor named in an *object_creation_expression* ([Object creation expressions](expressions.md#object-creation-expressions)).</span></span>
*  <span data-ttu-id="2e3bd-744">通过 *element_access* ([元素访问](expressions.md#element-access)) 调用索引器访问器。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-744">Invocation of an indexer accessor through an *element_access* ([Element access](expressions.md#element-access)).</span></span>
*  <span data-ttu-id="2e3bd-745">在表达式中引用的预定义运算符或用户定义的运算符 ([一元运算符重载决策](expressions.md#unary-operator-overload-resolution) 和 [二元运算符重载解析](expressions.md#binary-operator-overload-resolution)) 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-745">Invocation of a predefined or user-defined operator referenced in an expression ([Unary operator overload resolution](expressions.md#unary-operator-overload-resolution) and [Binary operator overload resolution](expressions.md#binary-operator-overload-resolution)).</span></span>

<span data-ttu-id="2e3bd-746">其中每个上下文都定义候选函数成员的集合和参数列表，其独特方式为，如以上所列部分中所述。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-746">Each of these contexts defines the set of candidate function members and the list of arguments in its own unique way, as described in detail in the sections listed above.</span></span> <span data-ttu-id="2e3bd-747">例如，方法调用的候选项的集合不包括标记 `override` ([成员查找](expressions.md#member-lookup)) 的方法，并且如果派生类中的任何方法适用于 ([方法调用](expressions.md#method-invocations)) ，则不是基类中的方法。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-747">For example, the set of candidates for a method invocation does not include methods marked `override` ([Member lookup](expressions.md#member-lookup)), and methods in a base class are not candidates if any method in a derived class is applicable ([Method invocations](expressions.md#method-invocations)).</span></span>

<span data-ttu-id="2e3bd-748">确定候选函数成员和参数列表后，在所有情况下，最佳函数成员的选择是相同的：</span><span class="sxs-lookup"><span data-stu-id="2e3bd-748">Once the candidate function members and the argument list have been identified, the selection of the best function member is the same in all cases:</span></span>

*  <span data-ttu-id="2e3bd-749">给定一组适用的候选函数成员后，该集合中的最佳函数成员就是。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-749">Given the set of applicable candidate function members, the best function member in that set is located.</span></span> <span data-ttu-id="2e3bd-750">如果集只包含一个函数成员，则该函数成员是最佳函数成员。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-750">If the set contains only one function member, then that function member is the best function member.</span></span> <span data-ttu-id="2e3bd-751">否则，最佳函数成员就是比给定自变量列表更好的所有其他函数成员的一个函数成员，前提是将每个函数成员与在 [更好的函数成员](expressions.md#better-function-member)中使用规则的所有其他函数成员进行比较。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-751">Otherwise, the best function member is the one function member that is better than all other function members with respect to the given argument list, provided that each function member is compared to all other function members using the rules in [Better function member](expressions.md#better-function-member).</span></span> <span data-ttu-id="2e3bd-752">如果不是正好有一个比所有其他函数成员更好的函数成员，则函数成员调用是不明确的，并发生绑定时错误。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-752">If there is not exactly one function member that is better than all other function members, then the function member invocation is ambiguous and a binding-time error occurs.</span></span>

<span data-ttu-id="2e3bd-753">以下部分定义了术语 \***适用的函数成员** _ 和 _ *_更好的函数成员_* \* 的确切含义。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-753">The following sections define the exact meanings of the terms ***applicable function member** _ and _*_better function member_\*\*.</span></span>

#### <a name="applicable-function-member"></a><span data-ttu-id="2e3bd-754">适用的函数成员</span><span class="sxs-lookup"><span data-stu-id="2e3bd-754">Applicable function member</span></span>

<span data-ttu-id="2e3bd-755"> `A` 当满足以下所有条件时，函数成员被称为适用于自变量列表的函数成员：</span><span class="sxs-lookup"><span data-stu-id="2e3bd-755">A function member is said to be an ***applicable function member*** with respect to an argument list `A` when all of the following are true:</span></span>

*  <span data-ttu-id="2e3bd-756">中的每个自变量 `A` 都对应于函数成员声明中的一个参数（如 [相应的参数](expressions.md#corresponding-parameters)中所述），并且任何与之相对应的参数均为可选参数。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-756">Each argument in `A` corresponds to a parameter in the function member declaration as described in [Corresponding parameters](expressions.md#corresponding-parameters), and any parameter to which no argument corresponds is an optional parameter.</span></span>
*  <span data-ttu-id="2e3bd-757">对于中的每个自变量 `A` ，参数的参数传递模式 (例如，value、 `ref` 或 `out`) 与相应参数的参数传递模式相同，并且</span><span class="sxs-lookup"><span data-stu-id="2e3bd-757">For each argument in `A`, the parameter passing mode of the argument (i.e., value, `ref`, or `out`) is identical to the parameter passing mode of the corresponding parameter, and</span></span>
   *  <span data-ttu-id="2e3bd-758">对于值参数或参数数组，隐式转换 (从参数到相应参数类型的 [隐式转换](conversions.md#implicit-conversions)) 或</span><span class="sxs-lookup"><span data-stu-id="2e3bd-758">for a value parameter or a parameter array, an implicit conversion ([Implicit conversions](conversions.md#implicit-conversions)) exists from the argument to the type of the corresponding parameter, or</span></span>
   *  <span data-ttu-id="2e3bd-759">对于 `ref` 或 `out` 参数，参数的类型与对应参数的类型相同。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-759">for a `ref` or `out` parameter, the type of the argument is identical to the type of the corresponding parameter.</span></span> <span data-ttu-id="2e3bd-760">毕竟， `ref` 或 `out` 参数是传递的参数的别名。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-760">After all, a `ref` or `out` parameter is an alias for the argument passed.</span></span>

<span data-ttu-id="2e3bd-761">对于包含参数数组的函数成员，如果函数成员适用于上述规则，则表示它适用于 \***normal 窗体** _。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-761">For a function member that includes a parameter array, if the function member is applicable by the above rules, it is said to be applicable in its \***normal form** _.</span></span> <span data-ttu-id="2e3bd-762">如果包含参数数组的函数成员在其正常形式下不适用，则函数成员可以改为适用于其 _ *_展开形式_* \*：</span><span class="sxs-lookup"><span data-stu-id="2e3bd-762">If a function member that includes a parameter array is not applicable in its normal form, the function member may instead be applicable in its _\*_expanded form_\*\*:</span></span>

*  <span data-ttu-id="2e3bd-763">通过将函数成员声明中的参数数组替换为参数数组元素类型的零个或多个值参数，使参数列表中的参数数目与参数的总数匹配，来构造展开后的形式 `A` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-763">The expanded form is constructed by replacing the parameter array in the function member declaration with zero or more value parameters of the element type of the parameter array such that the number of arguments in the argument list `A` matches the total number of parameters.</span></span> <span data-ttu-id="2e3bd-764">如果 `A` 参数少于函数成员声明中的固定参数数目，则无法构造函数成员的展开形式，因此不适用。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-764">If `A` has fewer arguments than the number of fixed parameters in the function member declaration, the expanded form of the function member cannot be constructed and is thus not applicable.</span></span>
*  <span data-ttu-id="2e3bd-765">否则，如果 for 参数传递模式中的每个参数 `A` 都与相应参数的参数传递模式相同，则展开形式适用，并且</span><span class="sxs-lookup"><span data-stu-id="2e3bd-765">Otherwise, the expanded form is applicable if for each argument in `A` the parameter passing mode of the argument is identical to the parameter passing mode of the corresponding parameter, and</span></span>
   *  <span data-ttu-id="2e3bd-766">对于固定值参数或扩展所创建的值参数，隐式转换 ([隐式](conversions.md#implicit-conversions) 转换) 存在从自变量的类型到相应参数类型的隐式转换，或</span><span class="sxs-lookup"><span data-stu-id="2e3bd-766">for a fixed value parameter or a value parameter created by the expansion, an implicit conversion ([Implicit conversions](conversions.md#implicit-conversions)) exists from the type of the argument to the type of the corresponding parameter, or</span></span>
   *  <span data-ttu-id="2e3bd-767">对于 `ref` 或 `out` 参数，参数的类型与对应参数的类型相同。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-767">for a `ref` or `out` parameter, the type of the argument is identical to the type of the corresponding parameter.</span></span>

#### <a name="better-function-member"></a><span data-ttu-id="2e3bd-768">更好的函数成员</span><span class="sxs-lookup"><span data-stu-id="2e3bd-768">Better function member</span></span>

<span data-ttu-id="2e3bd-769">出于确定更好的函数成员的目的，一个被去除的自变量列表 A 构造，它以其在原始参数列表中出现的顺序仅包含自变量表达式本身。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-769">For the purposes of determining the better function member, a stripped-down argument list A is constructed containing just the argument expressions themselves in the order they appear in the original argument list.</span></span>

<span data-ttu-id="2e3bd-770">按以下方式构造每个候选函数成员的参数列表：</span><span class="sxs-lookup"><span data-stu-id="2e3bd-770">Parameter lists for each of the candidate function members are constructed in the following way:</span></span>

*  <span data-ttu-id="2e3bd-771">如果函数成员仅适用于扩展窗体，则使用展开的窗体。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-771">The expanded form is used if the function member was applicable only in the expanded form.</span></span>
*  <span data-ttu-id="2e3bd-772">将从参数列表中删除不具有相应参数的可选参数</span><span class="sxs-lookup"><span data-stu-id="2e3bd-772">Optional parameters with no corresponding arguments are removed from the parameter list</span></span>
*  <span data-ttu-id="2e3bd-773">参数会进行重新排序，以便它们在与参数列表中的相应参数相同的位置上发生。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-773">The parameters are reordered so that they occur at the same position as the corresponding argument in the argument list.</span></span>

<span data-ttu-id="2e3bd-774">给定一个参数列表， `A` 其中包含一组参数表达式 `{E1, E2, ..., En}` 和两个适用的函数成员，并且 `Mp` `Mq` 具有参数类型 `{P1, P2, ..., Pn}` 和 `{Q1, Q2, ..., Qn}` ， `Mp` 定义为比 if ***更好的函数成员*** `Mq`</span><span class="sxs-lookup"><span data-stu-id="2e3bd-774">Given an argument list `A` with a set of argument expressions `{E1, E2, ..., En}` and two applicable function members `Mp` and `Mq` with parameter types `{P1, P2, ..., Pn}` and `{Q1, Q2, ..., Qn}`, `Mp` is defined to be a ***better function member*** than `Mq` if</span></span>

*  <span data-ttu-id="2e3bd-775">对于每个参数，从到的隐式转换 `Ex` `Qx` 不优于从到的隐式转换 `Ex` `Px` ，并且</span><span class="sxs-lookup"><span data-stu-id="2e3bd-775">for each argument, the implicit conversion from `Ex` to `Qx` is not better than the implicit conversion from `Ex` to `Px`, and</span></span>
*  <span data-ttu-id="2e3bd-776">对于至少一个参数，从到的转换 `Ex` `Px` 优于从到的转换 `Ex` `Qx` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-776">for at least one argument, the conversion from `Ex` to `Px` is better than the conversion from `Ex` to `Qx`.</span></span>

<span data-ttu-id="2e3bd-777">当执行此计算时，如果 `Mp` 或 `Mq` 适用于其展开的形式，则 `Px` 为，或 `Qx` 引用参数列表的展开形式的参数。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-777">When performing this evaluation, if `Mp` or `Mq` is applicable in its expanded form, then `Px` or `Qx` refers to a parameter in the expanded form of the parameter list.</span></span>

<span data-ttu-id="2e3bd-778">如果参数类型序列  `{P1, P2, ..., Pn}` 和 `{Q1, Q2, ..., Qn}` 等效 (也就是说，每个都 `Pi` 有一个到相应) 的标识转换 `Qi` ，则会按顺序应用以下附加中断规则来确定更好的函数成员。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-778">In case the parameter type sequences `{P1, P2, ..., Pn}` and `{Q1, Q2, ..., Qn}` are equivalent (i.e. each `Pi` has an identity conversion to the corresponding `Qi`), the following tie-breaking rules are applied, in order, to determine the better function member.</span></span>

*  <span data-ttu-id="2e3bd-779">如果 `Mp` 是一个非泛型方法并且 `Mq` 是一个泛型方法，则 `Mp` 比更好 `Mq` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-779">If `Mp` is a non-generic method and `Mq` is a generic method, then `Mp` is better than `Mq`.</span></span>
*  <span data-ttu-id="2e3bd-780">否则，如果 `Mp` 适用于其正常窗体并且 `Mq` 具有 `params` 数组，并且仅适用于其展开形式，则 `Mp` 比更好 `Mq` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-780">Otherwise, if `Mp` is applicable in its normal form and `Mq` has a `params` array and is applicable only in its expanded form, then `Mp` is better than `Mq`.</span></span>
*  <span data-ttu-id="2e3bd-781">否则，如果 `Mp` 具有的声明参数大于 `Mq` ，则 `Mp` 比更好 `Mq` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-781">Otherwise, if `Mp` has more declared parameters than `Mq`, then `Mp` is better than `Mq`.</span></span> <span data-ttu-id="2e3bd-782">如果这两个方法都具有 `params` 数组，并且仅适用于其展开的形式，则会发生这种情况。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-782">This can occur if both methods have `params` arrays and are applicable only in their expanded forms.</span></span>
*  <span data-ttu-id="2e3bd-783">否则，如果的所有参数 `Mp` 都具有相应的参数，而默认参数需要替换为中至少一个可选参数，则中的 `Mq` `Mp` 更好 `Mq` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-783">Otherwise if all parameters of `Mp` have a corresponding argument whereas default arguments need to be substituted for at least one optional parameter in `Mq` then `Mp` is better than `Mq`.</span></span>
*  <span data-ttu-id="2e3bd-784">否则，如果 `Mp` 具有比更多的特定参数类型 `Mq` ，则 `Mp` 比更好 `Mq` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-784">Otherwise, if `Mp` has more specific parameter types than `Mq`, then `Mp` is better than `Mq`.</span></span> <span data-ttu-id="2e3bd-785">让 `{R1, R2, ..., Rn}` 并表示和的未实例化和未扩展的 `{S1, S2, ..., Sn}` 参数类型 `Mp` `Mq` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-785">Let `{R1, R2, ..., Rn}` and `{S1, S2, ..., Sn}` represent the uninstantiated and unexpanded parameter types of `Mp` and `Mq`.</span></span> <span data-ttu-id="2e3bd-786">`Mp``Mq`如果每个参数的 `Rx` 不太具体， `Sx` 并且对于至少一个参数而言 `Rx` ，其参数类型比更具体 `Sx` ：</span><span class="sxs-lookup"><span data-stu-id="2e3bd-786">`Mp`'s parameter types are more specific than `Mq`'s if, for each parameter, `Rx` is not less specific than `Sx`, and, for at least one parameter, `Rx` is more specific than `Sx`:</span></span>
   *  <span data-ttu-id="2e3bd-787">类型参数不如非类型参数的特定类型。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-787">A type parameter is less specific than a non-type parameter.</span></span>
   *  <span data-ttu-id="2e3bd-788">与另一种类型自变量相比，如果至少有一个类型自变量是更具体) 的类型参数，并且没有类型参数的特定于其他中的相应类型参数，则构造的类型会以递归方式将其特定于其他构造类型 (。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-788">Recursively, a constructed type is more specific than another constructed type (with the same number of type arguments) if at least one type argument is more specific and no type argument is less specific than the corresponding type argument in the other.</span></span>
   *  <span data-ttu-id="2e3bd-789">数组类型比具有相同维数的另一个数组类型 (更为具体：如果第一个数组的元素类型比第二个元素类型更具体，则) 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-789">An array type is more specific than another array type (with the same number of dimensions) if the element type of the first is more specific than the element type of the second.</span></span>
*  <span data-ttu-id="2e3bd-790">否则，如果一个成员是非提升运算符，而另一个成员是提升运算符，则不提升的运算符更好。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-790">Otherwise if one member is a non-lifted operator and  the other is a lifted operator, the non-lifted one is better.</span></span>
*  <span data-ttu-id="2e3bd-791">否则，两个函数成员都不会更好。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-791">Otherwise, neither function member is better.</span></span>

#### <a name="better-conversion-from-expression"></a><span data-ttu-id="2e3bd-792">表达式的更好转换</span><span class="sxs-lookup"><span data-stu-id="2e3bd-792">Better conversion from expression</span></span>

<span data-ttu-id="2e3bd-793">给定了 `C1` 从表达式转换为类型的隐式转换 `E` `T1` ，以及 `C2` 从表达式转换为类型的隐式转换， `E` `T2` `C1` 是比 `C2` `E` 不完全匹配 `T2` 且至少包含以下其中一项的更好的转换：</span><span class="sxs-lookup"><span data-stu-id="2e3bd-793">Given an implicit conversion `C1` that converts from an expression `E` to a type `T1`, and an implicit conversion `C2` that converts from an expression `E` to a type `T2`, `C1` is a ***better conversion*** than `C2` if `E` does not exactly match `T2` and at least one of the following holds:</span></span>

* <span data-ttu-id="2e3bd-794">`E` 完全匹配 `T1` ([完全匹配的表达式](expressions.md#exactly-matching-expression)) </span><span class="sxs-lookup"><span data-stu-id="2e3bd-794">`E` exactly matches `T1` ([Exactly matching Expression](expressions.md#exactly-matching-expression))</span></span>
* <span data-ttu-id="2e3bd-795">`T1` 比 `T2` ([更好的转换目标](expressions.md#better-conversion-target) 更好的转换目标) </span><span class="sxs-lookup"><span data-stu-id="2e3bd-795">`T1` is a better conversion target than `T2` ([Better conversion target](expressions.md#better-conversion-target))</span></span>

#### <a name="exactly-matching-expression"></a><span data-ttu-id="2e3bd-796">完全匹配的表达式</span><span class="sxs-lookup"><span data-stu-id="2e3bd-796">Exactly matching Expression</span></span>

<span data-ttu-id="2e3bd-797">给定表达式 `E` 和类型 `T` ， `E` `T` 如果下列其中一项为，则完全匹配：</span><span class="sxs-lookup"><span data-stu-id="2e3bd-797">Given an expression `E` and a type `T`, `E` exactly matches `T` if one of the following holds:</span></span>

*  <span data-ttu-id="2e3bd-798">`E`具有类型 `S` ，并且存在从到的标识转换 `S``T`</span><span class="sxs-lookup"><span data-stu-id="2e3bd-798">`E` has a type `S`, and an identity conversion exists from `S` to `T`</span></span>
*  <span data-ttu-id="2e3bd-799">`E` 匿名函数为 `T` 委托类型 `D` 或表达式目录树类型 `Expression<D>` 以及以下保留之一：</span><span class="sxs-lookup"><span data-stu-id="2e3bd-799">`E` is an anonymous function, `T` is either a delegate type `D` or an expression tree type `Expression<D>` and one of the following holds:</span></span>
   *  <span data-ttu-id="2e3bd-800">推理出的返回类型 `X` 存在于 `E` `D` ([推导出的返回类型](expressions.md#inferred-return-type)) 的参数列表中，并且存在从 `X` 到的返回类型的标识转换 `D`</span><span class="sxs-lookup"><span data-stu-id="2e3bd-800">An inferred return type `X` exists for `E` in the context of the parameter list of `D` ([Inferred return type](expressions.md#inferred-return-type)), and an identity conversion exists from `X` to the return type of `D`</span></span>
   *  <span data-ttu-id="2e3bd-801">要么 `E` 是非异步的，都 `D` 具有返回类型 `Y` ，或者是异步的，并且 `E` `D` 具有返回类型和 `Task<Y>` 以下保留之一：</span><span class="sxs-lookup"><span data-stu-id="2e3bd-801">Either `E` is non-async and `D` has a return type `Y` or `E` is async and `D` has a return type `Task<Y>`, and one of the following holds:</span></span>
      * <span data-ttu-id="2e3bd-802">的正文 `E` 是一个完全匹配的表达式 `Y`</span><span class="sxs-lookup"><span data-stu-id="2e3bd-802">The body of `E` is an expression that exactly matches `Y`</span></span>
      * <span data-ttu-id="2e3bd-803">的主体 `E` 是一个语句块，其中每个 return 语句返回一个完全匹配的表达式 `Y`</span><span class="sxs-lookup"><span data-stu-id="2e3bd-803">The body of `E` is a statement block where every return statement returns an expression that exactly matches `Y`</span></span>

#### <a name="better-conversion-target"></a><span data-ttu-id="2e3bd-804">更好的转换目标</span><span class="sxs-lookup"><span data-stu-id="2e3bd-804">Better conversion target</span></span>

<span data-ttu-id="2e3bd-805">给定两种不同的类型 `T1` 和时 `T2` ， `T1` 是比 `T2` 从到不存在的隐式转换更好的转换目标 `T2` `T1` ，并且至少包含以下其中一项：</span><span class="sxs-lookup"><span data-stu-id="2e3bd-805">Given two different types `T1` and `T2`, `T1` is a better conversion target than `T2` if no implicit conversion from `T2` to `T1` exists, and at least one of the following holds:</span></span>

*  <span data-ttu-id="2e3bd-806">从到的隐式转换 `T1` `T2`</span><span class="sxs-lookup"><span data-stu-id="2e3bd-806">An implicit conversion from `T1` to `T2` exists</span></span>
*  <span data-ttu-id="2e3bd-807">`T1` 委托类型 `D1` 或表达式目录树类型为 `Expression<D1>` `T2` 委托类型 `D2` 或表达式树类型 `Expression<D2>` ， `D1` 具有返回类型 `S1` 和以下保留之一：</span><span class="sxs-lookup"><span data-stu-id="2e3bd-807">`T1` is either a delegate type `D1` or an expression tree type `Expression<D1>`, `T2` is either a delegate type `D2` or an expression tree type `Expression<D2>`, `D1` has a return type `S1` and one of the following holds:</span></span>
   * <span data-ttu-id="2e3bd-808">`D2` void 返回</span><span class="sxs-lookup"><span data-stu-id="2e3bd-808">`D2` is void returning</span></span>
   * <span data-ttu-id="2e3bd-809">`D2` 具有返回类型 `S2` ，并且 `S1` 是更好的转换目标 `S2`</span><span class="sxs-lookup"><span data-stu-id="2e3bd-809">`D2` has a return type `S2`, and `S1` is a better conversion target than `S2`</span></span>
*  <span data-ttu-id="2e3bd-810">`T1` 为，为 `Task<S1>` `T2` ，是 `Task<S2>` `S1` 比更好的转换目标 `S2`</span><span class="sxs-lookup"><span data-stu-id="2e3bd-810">`T1` is `Task<S1>`, `T2` is `Task<S2>`, and `S1` is a better conversion target than `S2`</span></span>
*  <span data-ttu-id="2e3bd-811">`T1` 为 `S1` 或， `S1?` 其中 `S1` 是带符号的整数类型，而 `T2` 是或， `S2` `S2?` 其中 `S2` 是无符号整数类型。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-811">`T1` is `S1` or `S1?` where `S1` is a signed integral type, and `T2` is `S2` or `S2?` where `S2` is an unsigned integral type.</span></span> <span data-ttu-id="2e3bd-812">尤其是在下列情况下：</span><span class="sxs-lookup"><span data-stu-id="2e3bd-812">Specifically:</span></span>
   * <span data-ttu-id="2e3bd-813">`S1` 为 `sbyte` `S2` ，并且为 `byte` 、、 `ushort` `uint` 或 `ulong`</span><span class="sxs-lookup"><span data-stu-id="2e3bd-813">`S1` is `sbyte` and `S2` is `byte`, `ushort`, `uint`, or `ulong`</span></span>
   * <span data-ttu-id="2e3bd-814">`S1` 为 `short` `S2` ，并且为 `ushort` 、 `uint` 或 `ulong`</span><span class="sxs-lookup"><span data-stu-id="2e3bd-814">`S1` is `short` and `S2` is `ushort`, `uint`, or `ulong`</span></span>
   * <span data-ttu-id="2e3bd-815">`S1` 为 `int` `S2` ，为 `uint` ，或者 `ulong`</span><span class="sxs-lookup"><span data-stu-id="2e3bd-815">`S1` is `int` and `S2` is `uint`, or `ulong`</span></span>
   * <span data-ttu-id="2e3bd-816">`S1` 为 `long` ，并且 `S2` 为 `ulong`</span><span class="sxs-lookup"><span data-stu-id="2e3bd-816">`S1` is `long` and `S2` is `ulong`</span></span>

#### <a name="overloading-in-generic-classes"></a><span data-ttu-id="2e3bd-817">泛型类中的重载</span><span class="sxs-lookup"><span data-stu-id="2e3bd-817">Overloading in generic classes</span></span>

<span data-ttu-id="2e3bd-818">尽管声明的签名必须是唯一的，但类型参数的替换可能导致相同的签名。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-818">While signatures as declared must be unique, it is possible that substitution of type arguments results in identical signatures.</span></span> <span data-ttu-id="2e3bd-819">在这种情况下，以上重载解决方法的分解规则将选取最特定的成员。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-819">In such cases, the tie-breaking rules of overload resolution above will pick the most specific member.</span></span>

<span data-ttu-id="2e3bd-820">下面的示例显示了根据此规则有效和无效的重载：</span><span class="sxs-lookup"><span data-stu-id="2e3bd-820">The following examples show overloads that are valid and invalid according to this rule:</span></span>

```csharp
interface I1<T> {...}

interface I2<T> {...}

class G1<U>
{
    int F1(U u);                  // Overload resolution for G<int>.F1
    int F1(int i);                // will pick non-generic

    void F2(I1<U> a);             // Valid overload
    void F2(I2<U> a);
}

class G2<U,V>
{
    void F3(U u, V v);            // Valid, but overload resolution for
    void F3(V v, U u);            // G2<int,int>.F3 will fail

    void F4(U u, I1<V> v);        // Valid, but overload resolution for    
    void F4(I1<V> v, U u);        // G2<I1<int>,int>.F4 will fail

    void F5(U u1, I1<V> v2);      // Valid overload
    void F5(V v1, U u2);

    void F6(ref U u);             // valid overload
    void F6(out V v);
}
```

### <a name="compile-time-checking-of-dynamic-overload-resolution"></a><span data-ttu-id="2e3bd-821">动态重载解析的编译时检查</span><span class="sxs-lookup"><span data-stu-id="2e3bd-821">Compile-time checking of dynamic overload resolution</span></span>

<span data-ttu-id="2e3bd-822">对于大多数动态绑定操作，解析的可能候选项集在编译时是未知的。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-822">For most dynamically bound operations the set of possible candidates for resolution is unknown at compile-time.</span></span> <span data-ttu-id="2e3bd-823">但在某些情况下，在编译时，候选集是已知的：</span><span class="sxs-lookup"><span data-stu-id="2e3bd-823">In certain cases, however the candidate set is known at compile-time:</span></span>

*  <span data-ttu-id="2e3bd-824">具有动态参数的静态方法调用</span><span class="sxs-lookup"><span data-stu-id="2e3bd-824">Static method calls with dynamic arguments</span></span>
*  <span data-ttu-id="2e3bd-825">接收方不是动态表达式的实例方法调用</span><span class="sxs-lookup"><span data-stu-id="2e3bd-825">Instance method calls where the receiver is not a dynamic expression</span></span>
*  <span data-ttu-id="2e3bd-826">接收方不是动态表达式的索引器调用</span><span class="sxs-lookup"><span data-stu-id="2e3bd-826">Indexer calls where the receiver is not a dynamic expression</span></span>
*  <span data-ttu-id="2e3bd-827">具有动态参数的构造函数调用</span><span class="sxs-lookup"><span data-stu-id="2e3bd-827">Constructor calls with dynamic arguments</span></span>

<span data-ttu-id="2e3bd-828">在这些情况下，将对每个候选项执行有限的编译时检查，以查看是否有可能在运行时应用它们。此检查包括以下步骤：</span><span class="sxs-lookup"><span data-stu-id="2e3bd-828">In these cases a limited compile-time check is performed for each candidate to see if any of them could possibly apply at run-time.This check consists of the following steps:</span></span>

*  <span data-ttu-id="2e3bd-829">分部类型推理：不直接或间接依赖于类型参数的任何类型参数 `dynamic` 都是使用 [类型推理](expressions.md#type-inference)的规则来推断的。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-829">Partial type inference: Any type argument that does not depend directly or indirectly on an argument of type `dynamic` is inferred using the rules of [Type inference](expressions.md#type-inference).</span></span> <span data-ttu-id="2e3bd-830">其余类型参数是未知的。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-830">The remaining type arguments are unknown.</span></span>
*  <span data-ttu-id="2e3bd-831">部分适用性检查：根据 [适用的函数成员](expressions.md#applicable-function-member)检查适用性，但忽略类型未知的参数。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-831">Partial applicability check: Applicability is checked according to [Applicable function member](expressions.md#applicable-function-member), but ignoring parameters whose types are unknown.</span></span>
*  <span data-ttu-id="2e3bd-832">如果没有候选项通过此测试，则会发生编译时错误。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-832">If no candidate passes this test, a compile-time error occurs.</span></span>

### <a name="function-member-invocation"></a><span data-ttu-id="2e3bd-833">函数成员调用</span><span class="sxs-lookup"><span data-stu-id="2e3bd-833">Function member invocation</span></span>

<span data-ttu-id="2e3bd-834">本节介绍在运行时调用特定函数成员的过程。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-834">This section describes the process that takes place at run-time to invoke a particular function member.</span></span> <span data-ttu-id="2e3bd-835">假定绑定时间进程已确定要调用的特定成员（可能通过将重载决策应用于一组候选函数成员）。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-835">It is assumed that a binding-time process has already determined the particular member to invoke, possibly by applying overload resolution to a set of candidate function members.</span></span>

<span data-ttu-id="2e3bd-836">出于描述调用过程的目的，函数成员分为两类：</span><span class="sxs-lookup"><span data-stu-id="2e3bd-836">For purposes of describing the invocation process, function members are divided into two categories:</span></span>

*  <span data-ttu-id="2e3bd-837">静态函数成员。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-837">Static function members.</span></span> <span data-ttu-id="2e3bd-838">这些是实例构造函数、静态方法、静态属性访问器和用户定义的运算符。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-838">These are instance constructors, static methods, static property accessors, and user-defined operators.</span></span> <span data-ttu-id="2e3bd-839">静态函数成员始终是非虚拟的。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-839">Static function members are always non-virtual.</span></span>
*  <span data-ttu-id="2e3bd-840">实例函数成员。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-840">Instance function members.</span></span> <span data-ttu-id="2e3bd-841">这些是实例方法、实例属性访问器和索引器访问器。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-841">These are instance methods, instance property accessors, and indexer accessors.</span></span> <span data-ttu-id="2e3bd-842">实例函数成员既不是虚拟的也不是虚拟的，始终在特定的实例上调用。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-842">Instance function members are either non-virtual or virtual, and are always invoked on a particular instance.</span></span> <span data-ttu-id="2e3bd-843">实例由实例表达式计算，它在函数成员内可访问，就像 `this` ([此访问](expressions.md#this-access)) 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-843">The instance is computed by an instance expression, and it becomes accessible within the function member as `this` ([This access](expressions.md#this-access)).</span></span>

<span data-ttu-id="2e3bd-844">函数成员调用的运行时处理包括以下步骤，其中 `M` 是函数成员，如果 `M` 是实例成员， `E` 则为实例表达式：</span><span class="sxs-lookup"><span data-stu-id="2e3bd-844">The run-time processing of a function member invocation consists of the following steps, where `M` is the function member and, if `M` is an instance member, `E` is the instance expression:</span></span>

*  <span data-ttu-id="2e3bd-845">如果 `M` 是静态函数成员：</span><span class="sxs-lookup"><span data-stu-id="2e3bd-845">If `M` is a static function member:</span></span>
   * <span data-ttu-id="2e3bd-846">参数列表按 [参数](expressions.md#argument-lists)列表中所述进行计算。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-846">The argument list is evaluated as described in [Argument lists](expressions.md#argument-lists).</span></span>
   * <span data-ttu-id="2e3bd-847">调用 `M`。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-847">`M` is invoked.</span></span>

*  <span data-ttu-id="2e3bd-848">如果 `M` 是在 *value_type* 中声明的实例函数成员：</span><span class="sxs-lookup"><span data-stu-id="2e3bd-848">If `M` is an instance function member declared in a *value_type*:</span></span>
   * <span data-ttu-id="2e3bd-849">`E` 进行计算。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-849">`E` is evaluated.</span></span> <span data-ttu-id="2e3bd-850">如果此计算导致异常，则不执行其他步骤。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-850">If this evaluation causes an exception, then no further steps are executed.</span></span>
   * <span data-ttu-id="2e3bd-851">如果 `E` 未归类为变量，则将创建类型的临时本地变量， `E` 并将的值 `E` 分配给该变量。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-851">If `E` is not classified as a variable, then a temporary local variable of `E`'s type is created and the value of `E` is assigned to that variable.</span></span> <span data-ttu-id="2e3bd-852">`E` 然后，将重新分类为对该临时本地变量的引用。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-852">`E` is then reclassified as a reference to that temporary local variable.</span></span> <span data-ttu-id="2e3bd-853">临时变量可作为内的 `this` 访问 `M` ，但不能以任何其他方式进行访问。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-853">The temporary variable is accessible as `this` within `M`, but not in any other way.</span></span> <span data-ttu-id="2e3bd-854">因此，只有在 `E` 为真正的变量时，调用方才能观察到所做的更改 `M` `this` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-854">Thus, only when `E` is a true variable is it possible for the caller to observe the changes that `M` makes to `this`.</span></span>
   * <span data-ttu-id="2e3bd-855">参数列表按 [参数](expressions.md#argument-lists)列表中所述进行计算。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-855">The argument list is evaluated as described in [Argument lists](expressions.md#argument-lists).</span></span>
   * <span data-ttu-id="2e3bd-856">调用 `M`。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-856">`M` is invoked.</span></span> <span data-ttu-id="2e3bd-857">引用的变量 `E` 成为引用的变量 `this` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-857">The variable referenced by `E` becomes the variable referenced by `this`.</span></span>

*  <span data-ttu-id="2e3bd-858">如果 `M` 是在 *reference_type* 中声明的实例函数成员：</span><span class="sxs-lookup"><span data-stu-id="2e3bd-858">If `M` is an instance function member declared in a *reference_type*:</span></span>
   * <span data-ttu-id="2e3bd-859">`E` 进行计算。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-859">`E` is evaluated.</span></span> <span data-ttu-id="2e3bd-860">如果此计算导致异常，则不执行其他步骤。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-860">If this evaluation causes an exception, then no further steps are executed.</span></span>
   * <span data-ttu-id="2e3bd-861">参数列表按 [参数](expressions.md#argument-lists)列表中所述进行计算。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-861">The argument list is evaluated as described in [Argument lists](expressions.md#argument-lists).</span></span>
   * <span data-ttu-id="2e3bd-862">如果的类型 `E` 是 *value_type*，则会执行装箱转换 ([装箱](types.md#boxing-conversions) 转换，) 将其转换 `E` 为类型 `object` ，并 `E` `object` 在以下步骤中将其视为类型。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-862">If the type of `E` is a *value_type*, a boxing conversion ([Boxing conversions](types.md#boxing-conversions)) is performed to convert `E` to type `object`, and `E` is considered to be of type `object` in the following steps.</span></span> <span data-ttu-id="2e3bd-863">在这种情况下， `M` 只能是的成员 `System.Object` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-863">In this case, `M` could only be a member of `System.Object`.</span></span>
   * <span data-ttu-id="2e3bd-864">的值 `E` 被检查为有效。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-864">The value of `E` is checked to be valid.</span></span> <span data-ttu-id="2e3bd-865">如果的值 `E` 为 `null` ，则 `System.NullReferenceException` 会引发，而不执行进一步的步骤。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-865">If the value of `E` is `null`, a `System.NullReferenceException` is thrown and no further steps are executed.</span></span>
   * <span data-ttu-id="2e3bd-866">确定要调用的函数成员实现：</span><span class="sxs-lookup"><span data-stu-id="2e3bd-866">The function member implementation to invoke is determined:</span></span>
     * <span data-ttu-id="2e3bd-867">如果的绑定时类型 `E` 是接口，则调用的函数成员是由 `M` 引用的实例的运行时类型提供的实现 `E` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-867">If the binding-time type of `E` is an interface, the function member to invoke is the implementation of `M` provided by the run-time type of the instance referenced by `E`.</span></span> <span data-ttu-id="2e3bd-868">此函数成员是通过以下方式确定的：将接口映射规则应用 ([接口映射](interfaces.md#interface-mapping)) ，以确定由 `M` 引用的实例的运行时类型提供的实现 `E` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-868">This function member is determined by applying the interface mapping rules ([Interface mapping](interfaces.md#interface-mapping)) to determine the implementation of `M` provided by the run-time type of the instance referenced by `E`.</span></span>
     * <span data-ttu-id="2e3bd-869">否则，如果 `M` 是虚函数成员，则调用的函数成员是由 `M` 引用的实例的运行时类型提供的实现 `E` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-869">Otherwise, if `M` is a virtual function member, the function member to invoke is the implementation of `M` provided by the run-time type of the instance referenced by `E`.</span></span> <span data-ttu-id="2e3bd-870">此函数成员是通过应用用于确定派生程度最高的实现 (虚拟方法的规则来确定的，) 的 [虚拟方法](classes.md#virtual-methods) `M` 与所引用的实例的运行时类型有关 `E` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-870">This function member is determined by applying the rules for determining the most derived implementation ([Virtual methods](classes.md#virtual-methods)) of `M` with respect to the run-time type of the instance referenced by `E`.</span></span>
     * <span data-ttu-id="2e3bd-871">否则， `M` 为非虚拟函数成员，要调用的函数成员本身就是 `M` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-871">Otherwise, `M` is a non-virtual function member, and the function member to invoke is `M` itself.</span></span>
   * <span data-ttu-id="2e3bd-872">调用上述步骤中确定的函数成员实现。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-872">The function member implementation determined in the step above is invoked.</span></span> <span data-ttu-id="2e3bd-873">引用的对象 `E` 成为引用的对象 `this` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-873">The object referenced by `E` becomes the object referenced by `this`.</span></span>

#### <a name="invocations-on-boxed-instances"></a><span data-ttu-id="2e3bd-874">在装箱实例上调用</span><span class="sxs-lookup"><span data-stu-id="2e3bd-874">Invocations on boxed instances</span></span>

<span data-ttu-id="2e3bd-875">在 *value_type* 中实现的函数成员可在以下情况下通过 *value_type* 的装箱实例进行调用：</span><span class="sxs-lookup"><span data-stu-id="2e3bd-875">A function member implemented in a *value_type* can be invoked through a boxed instance of that *value_type* in the following situations:</span></span>

*  <span data-ttu-id="2e3bd-876">如果函数成员是 `override` 从类型继承的方法的 `object` ，则通过类型的实例表达式调用 `object` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-876">When the function member is an `override` of a method inherited from type `object` and is invoked through an instance expression of type `object`.</span></span>
*  <span data-ttu-id="2e3bd-877">当函数成员是接口函数成员的实现并通过 *interface_type* 的实例表达式调用时。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-877">When the function member is an implementation of an interface function member and is invoked through an instance expression of an *interface_type*.</span></span>
*  <span data-ttu-id="2e3bd-878">函数成员通过委托调用时。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-878">When the function member is invoked through a delegate.</span></span>

<span data-ttu-id="2e3bd-879">在这些情况下，已装箱的实例被视为包含 *value_type* 的变量，此变量将成为 `this` 函数成员调用内引用的变量。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-879">In these situations, the boxed instance is considered to contain a variable of the *value_type*, and this variable becomes the variable referenced by `this` within the function member invocation.</span></span> <span data-ttu-id="2e3bd-880">具体而言，这意味着当对装箱实例调用函数成员时，该函数成员可以修改装箱实例中包含的值。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-880">In particular, this means that when a function member is invoked on a boxed instance, it is possible for the function member to modify the value contained in the boxed instance.</span></span>

## <a name="primary-expressions"></a><span data-ttu-id="2e3bd-881">主表达式</span><span class="sxs-lookup"><span data-stu-id="2e3bd-881">Primary expressions</span></span>

<span data-ttu-id="2e3bd-882">主要表达式包括表达式的最简单形式。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-882">Primary expressions include the simplest forms of expressions.</span></span>

```antlr
primary_expression
    : primary_no_array_creation_expression
    | array_creation_expression
    ;

primary_no_array_creation_expression
    : literal
    | interpolated_string_expression
    | simple_name
    | parenthesized_expression
    | member_access
    | invocation_expression
    | element_access
    | this_access
    | base_access
    | post_increment_expression
    | post_decrement_expression
    | object_creation_expression
    | delegate_creation_expression
    | anonymous_object_creation_expression
    | typeof_expression
    | checked_expression
    | unchecked_expression
    | default_value_expression
    | nameof_expression
    | anonymous_method_expression
    | primary_no_array_creation_expression_unsafe
    ;
```

<span data-ttu-id="2e3bd-883">主表达式划分 *array_creation_expression* s 和 *primary_no_array_creation_expression* 之间。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-883">Primary expressions are divided between *array_creation_expression* s and *primary_no_array_creation_expression* s.</span></span> <span data-ttu-id="2e3bd-884">以这种方式对待数组创建表达式，而不是将其与其他简单的表达式窗体一起列出，而是允许语法禁止可能的代码混乱，如</span><span class="sxs-lookup"><span data-stu-id="2e3bd-884">Treating array-creation-expression in this way, rather than listing it along with the other simple expression forms, enables the grammar to disallow potentially confusing code such as</span></span>
```csharp
object o = new int[3][1];
```
<span data-ttu-id="2e3bd-885">否则，会将其解释为</span><span class="sxs-lookup"><span data-stu-id="2e3bd-885">which would otherwise be interpreted as</span></span>
```csharp
object o = (new int[3])[1];
```

### <a name="literals"></a><span data-ttu-id="2e3bd-886">文本</span><span class="sxs-lookup"><span data-stu-id="2e3bd-886">Literals</span></span>

<span data-ttu-id="2e3bd-887">由 *文本* ([文本](lexical-structure.md#literals)) 组成的 *primary_expression* 分类为值。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-887">A *primary_expression* that consists of a *literal* ([Literals](lexical-structure.md#literals)) is classified as a value.</span></span>


### <a name="interpolated-strings"></a><span data-ttu-id="2e3bd-888">内插字符串</span><span class="sxs-lookup"><span data-stu-id="2e3bd-888">Interpolated strings</span></span>

<span data-ttu-id="2e3bd-889">*Interpolated_string_expression* 包含一个符号， `$` 后面跟一个常规或原义字符串文本，其中的洞由和分隔，其中 `{` `}` 包含表达式和格式规范。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-889">An *interpolated_string_expression* consists of a `$` sign followed by a regular or verbatim string literal, wherein holes, delimited by `{` and `}`, enclose expressions and formatting specifications.</span></span> <span data-ttu-id="2e3bd-890">内插字符串表达式是已分解为单独标记的 *interpolated_string_literal* 的结果，如内 [插字符串文本](lexical-structure.md#interpolated-string-literals)中所述。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-890">An interpolated string expression is the result of an *interpolated_string_literal* that has been broken up into individual tokens, as described in [Interpolated string literals](lexical-structure.md#interpolated-string-literals).</span></span>

```antlr
interpolated_string_expression
    : '$' interpolated_regular_string
    | '$' interpolated_verbatim_string
    ;

interpolated_regular_string
    : interpolated_regular_string_whole
    | interpolated_regular_string_start interpolated_regular_string_body interpolated_regular_string_end
    ;

interpolated_regular_string_body
    : interpolation (interpolated_regular_string_mid interpolation)*
    ;

interpolation
    : expression
    | expression ',' constant_expression
    ;

interpolated_verbatim_string
    : interpolated_verbatim_string_whole
    | interpolated_verbatim_string_start interpolated_verbatim_string_body interpolated_verbatim_string_end
    ;

interpolated_verbatim_string_body
    : interpolation (interpolated_verbatim_string_mid interpolation)+
    ;
```

<span data-ttu-id="2e3bd-891">内插中的 *constant_expression* 必须将隐式转换为 `int` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-891">The *constant_expression* in an interpolation must have an implicit conversion to `int`.</span></span>

<span data-ttu-id="2e3bd-892">*Interpolated_string_expression* 分类为值。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-892">An *interpolated_string_expression* is classified as a value.</span></span> <span data-ttu-id="2e3bd-893">如果它立即转换为 `System.IFormattable` 或 `System.FormattableString` 并且带有隐式内插字符串转换 ([隐式内插](conversions.md#implicit-interpolated-string-conversions) 字符串转换) ，则内插字符串表达式具有该类型。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-893">If it is immediately converted to `System.IFormattable` or `System.FormattableString` with an implicit interpolated string conversion ([Implicit interpolated string conversions](conversions.md#implicit-interpolated-string-conversions)), the interpolated string expression has that type.</span></span> <span data-ttu-id="2e3bd-894">否则，它的类型为 `string` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-894">Otherwise, it has the type `string`.</span></span>

<span data-ttu-id="2e3bd-895">如果内插字符串的类型为 `System.IFormattable` 或 `System.FormattableString` ，则表示对的调用 `System.Runtime.CompilerServices.FormattableStringFactory.Create` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-895">If the type of an interpolated string is `System.IFormattable` or `System.FormattableString`, the meaning is a call to `System.Runtime.CompilerServices.FormattableStringFactory.Create`.</span></span> <span data-ttu-id="2e3bd-896">如果类型为 `string` ，则表达式的含义是对的调用 `string.Format` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-896">If the type is `string`, the meaning of the expression is a call to `string.Format`.</span></span> <span data-ttu-id="2e3bd-897">在这两种情况下，调用的参数列表都包含格式字符串文本和每个内插的占位符，以及与占位符相对应的每个表达式的参数。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-897">In both cases, the argument list of the call consists of a format string literal with placeholders for each interpolation, and an argument for each expression corresponding to the place holders.</span></span>

<span data-ttu-id="2e3bd-898">格式字符串的构造如下所示，其中 `N` 是 *interpolated_string_expression* 中的内插数：</span><span class="sxs-lookup"><span data-stu-id="2e3bd-898">The format string literal is constructed as follows, where `N` is the number of interpolations in the *interpolated_string_expression*:</span></span>

*  <span data-ttu-id="2e3bd-899">如果 *interpolated_regular_string_whole* 或 *interpolated_verbatim_string_whole* 后跟 `$` 符号，则格式字符串文字为该令牌。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-899">If an *interpolated_regular_string_whole* or an *interpolated_verbatim_string_whole* follows the `$` sign, then the format string literal is that token.</span></span>
*  <span data-ttu-id="2e3bd-900">否则，格式字符串文本包含：</span><span class="sxs-lookup"><span data-stu-id="2e3bd-900">Otherwise, the format string literal consists of:</span></span> 
   *  <span data-ttu-id="2e3bd-901">首先 *interpolated_regular_string_start* 或 *interpolated_verbatim_string_start*</span><span class="sxs-lookup"><span data-stu-id="2e3bd-901">First the *interpolated_regular_string_start* or *interpolated_verbatim_string_start*</span></span>
   *  <span data-ttu-id="2e3bd-902">然后，将每个数字 `I` 从 `0` 到 `N-1` ：</span><span class="sxs-lookup"><span data-stu-id="2e3bd-902">Then for each number `I` from `0` to `N-1`:</span></span> 
      * <span data-ttu-id="2e3bd-903">的十进制表示形式 `I`</span><span class="sxs-lookup"><span data-stu-id="2e3bd-903">The decimal representation of `I`</span></span>
      * <span data-ttu-id="2e3bd-904">然后，如果相应的 *内插* 具有 *constant_expression*，则 `,` (逗号) 后跟的值的十进制表示形式 *constant_expression*</span><span class="sxs-lookup"><span data-stu-id="2e3bd-904">Then, if the corresponding *interpolation* has a *constant_expression*, a `,` (comma) followed by the decimal representation of the value of the *constant_expression*</span></span>
      * <span data-ttu-id="2e3bd-905">然后， *interpolated_regular_string_mid*、 *interpolated_regular_string_end*、 *interpolated_verbatim_string_mid* 或 *interpolated_verbatim_string_end* 后跟相应的内插。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-905">Then the *interpolated_regular_string_mid*, *interpolated_regular_string_end*, *interpolated_verbatim_string_mid* or *interpolated_verbatim_string_end* immediately following the corresponding interpolation.</span></span>

<span data-ttu-id="2e3bd-906">后续参数只是 *内插* 中的 *表达式*，如有任何) ，按顺序排列 (。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-906">The subsequent arguments are simply the *expressions* from the *interpolations* (if any), in order.</span></span>

<span data-ttu-id="2e3bd-907">TODO：示例。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-907">TODO: examples.</span></span>


### <a name="simple-names"></a><span data-ttu-id="2e3bd-908">简单名称</span><span class="sxs-lookup"><span data-stu-id="2e3bd-908">Simple names</span></span>

<span data-ttu-id="2e3bd-909">*Simple_name* 包含标识符，可选择后跟类型参数列表：</span><span class="sxs-lookup"><span data-stu-id="2e3bd-909">A *simple_name* consists of an identifier, optionally followed by a type argument list:</span></span>

```antlr
simple_name
    : identifier type_argument_list?
    ;
```

<span data-ttu-id="2e3bd-910">*Simple_name* 格式为形式 `I` 或格式 `I<A1,...,Ak>` ，其中 `I` 是单个标识符， `<A1,...,Ak>` 是可选 *type_argument_list*。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-910">A *simple_name* is either of the form `I` or of the form `I<A1,...,Ak>`, where `I` is a single identifier and `<A1,...,Ak>` is an optional *type_argument_list*.</span></span> <span data-ttu-id="2e3bd-911">当未指定 *type_argument_list* 时，请考虑 `K` 为零。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-911">When no *type_argument_list* is specified, consider `K` to be zero.</span></span> <span data-ttu-id="2e3bd-912">将按如下所示对 *simple_name* 进行评估和分类：</span><span class="sxs-lookup"><span data-stu-id="2e3bd-912">The *simple_name* is evaluated and classified as follows:</span></span>

*  <span data-ttu-id="2e3bd-913">如果 `K` 为零，并且 *simple_name* 出现在 *块* 中，并且 *块* 的 (或封闭 *块*) 局部变量声明空间 ([声明](basic-concepts.md#declarations) 中) 包含名称为的局部变量、参数或常量  `I` ，则 *simple_name* 引用该局部变量、参数或常量，并归类为变量或值。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-913">If `K` is zero and the *simple_name* appears within a *block* and if the *block*'s (or an enclosing *block*'s) local variable declaration space ([Declarations](basic-concepts.md#declarations)) contains a local variable, parameter or constant with name `I`, then the *simple_name* refers to that local variable, parameter or constant and is classified as a variable or value.</span></span>
*  <span data-ttu-id="2e3bd-914">如果 `K` 为零，并且 *simple_name* 出现在泛型方法声明的主体中，并且如果声明包含名称为的类型参数  `I` ，则 *simple_name* 引用该类型参数。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-914">If `K` is zero and the *simple_name* appears within the body of a generic method declaration and if that declaration includes a type parameter with name `I`, then the *simple_name* refers to that type parameter.</span></span>
*  <span data-ttu-id="2e3bd-915">否则，对于每个实例类型  `T` ([实例类型](classes.md#the-instance-type)) ，从立即封闭类型声明的实例类型开始，并继续执行每个封闭类或结构声明的实例类型（如果有任何) ） (：</span><span class="sxs-lookup"><span data-stu-id="2e3bd-915">Otherwise, for each instance type `T` ([The instance type](classes.md#the-instance-type)), starting with the instance type of the immediately enclosing type declaration and continuing with the instance type of each enclosing class or struct declaration (if any):</span></span>
   *  <span data-ttu-id="2e3bd-916">如果 `K` 为零，的声明 `T` 包含名为的类型参数  `I` ，则 *simple_name* 引用该类型参数。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-916">If `K` is zero and the declaration of `T` includes a type parameter with name `I`, then the *simple_name* refers to that type parameter.</span></span>
   *  <span data-ttu-id="2e3bd-917">否则，如果中的成员查找 ([成员查找](expressions.md#member-lookup)) `I` 中 `T` 的 `K`   类型自变量产生匹配：</span><span class="sxs-lookup"><span data-stu-id="2e3bd-917">Otherwise, if a member lookup ([Member lookup](expressions.md#member-lookup)) of `I` in `T` with `K` type arguments produces a match:</span></span>
      * <span data-ttu-id="2e3bd-918">如果 `T` 是直接封闭类或结构类型的实例类型，并且查找标识了一个或多个方法，则结果是具有关联的实例表达式的方法组 `this` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-918">If `T` is the instance type of the immediately enclosing class or struct type and the lookup identifies one or more methods, the result is a method group with an associated instance expression of `this`.</span></span> <span data-ttu-id="2e3bd-919">如果指定了类型参数列表，它将用于调用泛型方法 ([方法调用](expressions.md#method-invocations)) 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-919">If a type argument list was specified, it is used in calling a generic method ([Method invocations](expressions.md#method-invocations)).</span></span>
      * <span data-ttu-id="2e3bd-920">否则，如果 `T` 是直接封闭类或结构类型的实例类型，如果查找标识实例成员并且引用出现在实例构造函数、实例方法或实例访问器的主体中，则结果与窗体 ([成员访问](expressions.md#member-access)) 的成员访问相同 `this.I` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-920">Otherwise, if `T` is the instance type of the immediately enclosing class or struct type, if the lookup identifies an instance member, and if the reference occurs within the body of an instance constructor, an instance method, or an instance accessor, the result is the same as a member access ([Member access](expressions.md#member-access)) of the form `this.I`.</span></span> <span data-ttu-id="2e3bd-921">仅当为零时才会发生这种情况 `K` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-921">This can only happen when `K` is zero.</span></span>
      * <span data-ttu-id="2e3bd-922">否则，结果与窗体或 ([成员访问](expressions.md#member-access)) 的成员访问相同 `T.I` `T.I<A1,...,Ak>` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-922">Otherwise, the result is the same as a member access ([Member access](expressions.md#member-access)) of the form `T.I` or `T.I<A1,...,Ak>`.</span></span> <span data-ttu-id="2e3bd-923">在这种情况下， *simple_name* 引用实例成员，则为绑定时错误。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-923">In this case, it is a binding-time error for the *simple_name* to refer to an instance member.</span></span>

*  <span data-ttu-id="2e3bd-924">否则，对于每个命名空间  `N` ，从 *simple_name* 发生的命名空间开始，继续每个封闭命名空间 (如果任何) ，并在全局命名空间结束时，将计算以下步骤，直到找到实体：</span><span class="sxs-lookup"><span data-stu-id="2e3bd-924">Otherwise, for each namespace `N`, starting with the namespace in which the *simple_name* occurs, continuing with each enclosing namespace (if any), and ending with the global namespace, the following steps are evaluated until an entity is located:</span></span>
   *  <span data-ttu-id="2e3bd-925">如果 `K` 为零，并且 `I` 是中的命名空间的名称  `N` ，则：</span><span class="sxs-lookup"><span data-stu-id="2e3bd-925">If `K` is zero and `I` is the name of a namespace in `N`, then:</span></span>
      * <span data-ttu-id="2e3bd-926">如果 *simple_name* 发生的位置由的命名空间声明括起来 `N` ，并且命名空间声明包含将名称与命名空间或类型相关联的 *extern_alias_directive* 或 *using_alias_directive*  `I` ，则 *simple_name* 是不明确的，并发生编译时错误。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-926">If the location where the *simple_name* occurs is enclosed by a namespace declaration for `N` and the namespace declaration contains an *extern_alias_directive* or *using_alias_directive* that associates the name `I` with a namespace or type, then the *simple_name* is ambiguous and a compile-time error occurs.</span></span>
      * <span data-ttu-id="2e3bd-927">否则， *simple_name* 引用中名为的命名 `I` 空间 `N` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-927">Otherwise, the *simple_name* refers to the namespace named `I` in `N`.</span></span>
   *  <span data-ttu-id="2e3bd-928">否则，如果 `N` 包含具有 name 和 type 参数的可访问类型  `I` `K`   ，则：</span><span class="sxs-lookup"><span data-stu-id="2e3bd-928">Otherwise, if `N` contains an accessible type having name `I` and `K` type parameters, then:</span></span>
      * <span data-ttu-id="2e3bd-929">如果 `K` 为零，并且 *simple_name* 的位置由的命名空间声明括起来， `N` 并且命名空间声明包含将名称与命名空间或类型相关联的 *extern_alias_directive* 或 *using_alias_directive*  `I` ，则 *simple_name* 是不明确的，并发生编译时错误。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-929">If `K` is zero and the location where the *simple_name* occurs is enclosed by a namespace declaration for `N` and the namespace declaration contains an *extern_alias_directive* or *using_alias_directive* that associates the name `I` with a namespace or type, then the *simple_name* is ambiguous and a compile-time error occurs.</span></span>
      * <span data-ttu-id="2e3bd-930">否则， *namespace_or_type_name* 引用用给定类型参数构造的类型。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-930">Otherwise, the *namespace_or_type_name* refers to the type constructed with the given type arguments.</span></span>
   *  <span data-ttu-id="2e3bd-931">否则，如果 *simple_name* 发生的位置由的命名空间声明括起来，则  `N` ：</span><span class="sxs-lookup"><span data-stu-id="2e3bd-931">Otherwise, if the location where the *simple_name* occurs is enclosed by a namespace declaration for `N`:</span></span>
      * <span data-ttu-id="2e3bd-932">如果 `K` 为零，并且命名空间声明包含将名称与导入的命名空间或类型相关联的 *extern_alias_directive* 或 *using_alias_directive*  `I` ，则 *simple_name* 引用该命名空间或类型。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-932">If `K` is zero and the namespace declaration contains an *extern_alias_directive* or *using_alias_directive* that associates the name `I` with an imported namespace or type, then the *simple_name* refers to that namespace or type.</span></span>
      * <span data-ttu-id="2e3bd-933">否则，如果 *using_namespace_directive* s 和 *using_static_directive* s 的命名空间和类型声明都包含一个具有 name 和 type 参数的可访问类型或非扩展静态成员  `I` `K`   ，则 *simple_name* 引用使用给定类型参数构造的类型或成员。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-933">Otherwise, if the namespaces and type declarations imported by the *using_namespace_directive* s and *using_static_directive* s of the namespace declaration contain exactly one accessible type or non-extension static member having name `I` and `K` type parameters, then the *simple_name* refers to that type or member constructed with the given type arguments.</span></span>
      * <span data-ttu-id="2e3bd-934">否则，如果命名空间声明中的 *using_namespace_directive* s 所导入的命名空间和类型包含多个具有名称和类型参数的可访问类型或非扩展方法静态成员  `I` `K`   ，则 *simple_name* 不明确，并发生错误。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-934">Otherwise, if the namespaces and types imported by the *using_namespace_directive* s of the namespace declaration contain more than one accessible type or non-extension-method static member having name `I` and `K` type parameters, then the *simple_name* is ambiguous and an error occurs.</span></span>

   <span data-ttu-id="2e3bd-935">请注意，此完整步骤与处理 *namespace_or_type_name* ([命名空间和类型名称](basic-concepts.md#namespace-and-type-names)) 的相应步骤完全相同。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-935">Note that this entire step is exactly parallel to the corresponding step in the processing of a *namespace_or_type_name* ([Namespace and type names](basic-concepts.md#namespace-and-type-names)).</span></span>

*  <span data-ttu-id="2e3bd-936">否则， *simple_name* 是未定义的，并且发生编译时错误。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-936">Otherwise, the *simple_name* is undefined and a compile-time error occurs.</span></span>


### <a name="parenthesized-expressions"></a><span data-ttu-id="2e3bd-937">带括号的表达式</span><span class="sxs-lookup"><span data-stu-id="2e3bd-937">Parenthesized expressions</span></span>

<span data-ttu-id="2e3bd-938">*Parenthesized_expression* 包含用括号括起来的 *表达式*。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-938">A *parenthesized_expression* consists of an *expression* enclosed in parentheses.</span></span>

```antlr
parenthesized_expression
    : '(' expression ')'
    ;
```

<span data-ttu-id="2e3bd-939">通过计算括号内的 *表达式* 来计算 *parenthesized_expression* 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-939">A *parenthesized_expression* is evaluated by evaluating the *expression* within the parentheses.</span></span> <span data-ttu-id="2e3bd-940">如果括号内的 *表达式* 表示命名空间或类型，则会发生编译时错误。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-940">If the *expression* within the parentheses denotes a namespace or type, a compile-time error occurs.</span></span> <span data-ttu-id="2e3bd-941">否则， *parenthesized_expression* 的结果是包含的 *表达式* 的计算结果。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-941">Otherwise, the result of the *parenthesized_expression* is the result of the evaluation of the contained *expression*.</span></span>

### <a name="member-access"></a><span data-ttu-id="2e3bd-942">成员访问</span><span class="sxs-lookup"><span data-stu-id="2e3bd-942">Member access</span></span>

<span data-ttu-id="2e3bd-943">*Member_access* 包含 *primary_expression*、 *predefined_type* 或 *qualified_alias_member*，后跟 " `.` " 标记，后跟一个 *标识符*，可选择后跟 *type_argument_list*。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-943">A *member_access* consists of a *primary_expression*, a *predefined_type*, or a *qualified_alias_member*, followed by a "`.`" token, followed by an *identifier*, optionally followed by a *type_argument_list*.</span></span>

```antlr
member_access
    : primary_expression '.' identifier type_argument_list?
    | predefined_type '.' identifier type_argument_list?
    | qualified_alias_member '.' identifier
    ;

predefined_type
    : 'bool'   | 'byte'  | 'char'  | 'decimal' | 'double' | 'float' | 'int' | 'long'
    | 'object' | 'sbyte' | 'short' | 'string'  | 'uint'   | 'ulong' | 'ushort'
    ;
```

<span data-ttu-id="2e3bd-944">*Qualified_alias_member* 生产是在 [命名空间别名限定符](namespaces.md#namespace-alias-qualifiers)中定义的。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-944">The *qualified_alias_member* production is defined in [Namespace alias qualifiers](namespaces.md#namespace-alias-qualifiers).</span></span>

<span data-ttu-id="2e3bd-945">*Member_access* 的格式为窗体 `E.I` 或窗体 `E.I<A1, ..., Ak>` ，其中 `E` 是主表达式，是 `I` 单个标识符， `<A1, ..., Ak>` 是可选 *type_argument_list*。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-945">A *member_access* is either of the form `E.I` or of the form `E.I<A1, ..., Ak>`, where `E` is a primary-expression, `I` is a single identifier and `<A1, ..., Ak>` is an optional *type_argument_list*.</span></span> <span data-ttu-id="2e3bd-946">当未指定 *type_argument_list* 时，请考虑 `K` 为零。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-946">When no *type_argument_list* is specified, consider `K` to be zero.</span></span>

<span data-ttu-id="2e3bd-947">*Primary_expression* 类型的 *member_access* `dynamic` ([动态绑定](expressions.md#dynamic-binding)) 动态绑定。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-947">A *member_access* with a *primary_expression* of type `dynamic` is dynamically bound ([Dynamic binding](expressions.md#dynamic-binding)).</span></span> <span data-ttu-id="2e3bd-948">在这种情况下，编译器将成员访问归类为类型的属性访问 `dynamic` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-948">In this case the compiler classifies the member access as a property access of type `dynamic`.</span></span> <span data-ttu-id="2e3bd-949">下面的规则使用运行时类型（而不是 *primary_expression* 的编译时类型）在运行时应用 *member_access* 的含义。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-949">The rules below to determine the meaning of the *member_access* are then applied at run-time, using the run-time type instead of the compile-time type of the *primary_expression*.</span></span> <span data-ttu-id="2e3bd-950">如果此运行时分类导致了方法组，则成员访问必须是 *invocation_expression* 的 *primary_expression* 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-950">If this run-time classification leads to a method group, then the member access must be the *primary_expression* of an *invocation_expression*.</span></span>

<span data-ttu-id="2e3bd-951">将按如下所示对 *member_access* 进行评估和分类：</span><span class="sxs-lookup"><span data-stu-id="2e3bd-951">The *member_access* is evaluated and classified as follows:</span></span>

*  <span data-ttu-id="2e3bd-952">如果 `K` 为零，并且 `E` 是一个命名空间，并且 `E` 包含名为的嵌套命名空间  `I` ，则结果为该命名空间。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-952">If `K` is zero and `E` is a namespace and `E` contains a nested namespace with name `I`, then the result is that namespace.</span></span>
*  <span data-ttu-id="2e3bd-953">否则，如果 `E` 是一个命名空间，并且 `E` 包含具有 name 和 type 参数的可访问类型  `I` `K`   ，则结果是使用给定类型参数构造的类型。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-953">Otherwise, if `E` is a namespace and `E` contains an accessible type having name `I` and `K` type parameters, then the result is that type constructed with the given type arguments.</span></span>
*  <span data-ttu-id="2e3bd-954">如果 `E` 是 *predefined_type* 或归类为类型 *primary_expression* ，则如果不是 `E` 类型形参，并且具有类型形参的中的成员查找 ([成员查找](expressions.md#member-lookup)) `I` `E` `K`   产生匹配，则按 `E.I` 如下方式计算和分类：</span><span class="sxs-lookup"><span data-stu-id="2e3bd-954">If `E` is a *predefined_type* or a *primary_expression* classified as a type, if `E` is not a type parameter, and if a member lookup ([Member lookup](expressions.md#member-lookup)) of `I` in `E` with `K` type parameters produces a match, then `E.I` is evaluated and classified as follows:</span></span>
   *  <span data-ttu-id="2e3bd-955">如果 `I` 标识一个类型，则结果为使用给定类型参数构造的类型。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-955">If `I` identifies a type, then the result is that type constructed with the given type arguments.</span></span>
   *  <span data-ttu-id="2e3bd-956">如果 `I` 标识一个或多个方法，则结果是没有关联的实例表达式的方法组。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-956">If `I` identifies one or more methods, then the result is a method group with no associated instance expression.</span></span> <span data-ttu-id="2e3bd-957">如果指定了类型参数列表，它将用于调用泛型方法 ([方法调用](expressions.md#method-invocations)) 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-957">If a type argument list was specified, it is used in calling a generic method ([Method invocations](expressions.md#method-invocations)).</span></span>
   *  <span data-ttu-id="2e3bd-958">如果 `I` 标识 `static` 属性，则结果是没有关联实例表达式的属性访问。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-958">If `I` identifies a `static` property, then the result is a property access with no associated instance expression.</span></span>
   *  <span data-ttu-id="2e3bd-959">如果 `I` 标识 `static` 字段：</span><span class="sxs-lookup"><span data-stu-id="2e3bd-959">If `I` identifies a `static` field:</span></span>
      * <span data-ttu-id="2e3bd-960">如果字段为 `readonly` 并且引用出现在声明该字段的类或结构的静态构造函数之外，则结果为值，即中静态字段的值  `I`  `E` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-960">If the field is `readonly` and the reference occurs outside the static constructor of the class or struct in which the field is declared, then the result is a value, namely the value of the static field `I` in `E`.</span></span>
      * <span data-ttu-id="2e3bd-961">否则，结果为变量，即中的静态字段  `I`  `E` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-961">Otherwise, the result is a variable, namely the static field `I` in `E`.</span></span>
   *  <span data-ttu-id="2e3bd-962">如果 `I` 标识 `static` 事件：</span><span class="sxs-lookup"><span data-stu-id="2e3bd-962">If `I` identifies a `static` event:</span></span>
      * <span data-ttu-id="2e3bd-963">如果引用出现在声明事件的类或结构中，并且声明事件时没有 *event_accessor_declarations* ([事件](classes.md#events)) ，则 `E.I` 将像处理静态字段一样处理事件 `I` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-963">If the reference occurs within the class or struct in which the event is declared, and the event was declared without *event_accessor_declarations* ([Events](classes.md#events)), then `E.I` is processed exactly as if `I` were a static field.</span></span>
      * <span data-ttu-id="2e3bd-964">否则，结果是没有关联的实例表达式的事件访问。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-964">Otherwise, the result is an event access with no associated instance expression.</span></span>
   *  <span data-ttu-id="2e3bd-965">如果 `I` 标识一个常数，则结果为一个值，即该常数的值。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-965">If `I` identifies a constant, then the result is a value, namely the value of that constant.</span></span>
    * <span data-ttu-id="2e3bd-966">如果 `I` 标识枚举成员，则结果为值，即枚举成员的值。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-966">If `I` identifies an enumeration member, then the result is a value, namely the value of that enumeration member.</span></span>
    * <span data-ttu-id="2e3bd-967">否则， `E.I` 是无效的成员引用，并发生编译时错误。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-967">Otherwise, `E.I` is an invalid member reference, and a compile-time error occurs.</span></span>
*  <span data-ttu-id="2e3bd-968">如果 `E` 是属性访问、索引器访问、变量或值，其中的类型为  `T` ，并且具有类型参数的成员查找 ([成员查找](expressions.md#member-lookup)) `I` `T` `K`   生成匹配项，则按 `E.I` 如下方式计算和分类：</span><span class="sxs-lookup"><span data-stu-id="2e3bd-968">If `E` is a property access, indexer access, variable, or value, the type of which is `T`, and a member lookup ([Member lookup](expressions.md#member-lookup)) of `I` in `T` with `K` type arguments produces a match, then `E.I` is evaluated and classified as follows:</span></span>
   *  <span data-ttu-id="2e3bd-969">首先，如果 `E` 是属性或索引器访问，则 () 的 [表达式值](expressions.md#values-of-expressions) 获取属性或索引器访问的值，并将其重新分类 `E` 为值。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-969">First, if `E` is a property or indexer access, then the value of the property or indexer access is obtained ([Values of expressions](expressions.md#values-of-expressions)) and `E` is reclassified as a value.</span></span>
   *  <span data-ttu-id="2e3bd-970">如果 `I` 标识一个或多个方法，则结果为具有的关联实例表达式的方法组 `E` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-970">If `I` identifies one or more methods, then the result is a method group with an associated instance expression of `E`.</span></span> <span data-ttu-id="2e3bd-971">如果指定了类型参数列表，它将用于调用泛型方法 ([方法调用](expressions.md#method-invocations)) 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-971">If a type argument list was specified, it is used in calling a generic method ([Method invocations](expressions.md#method-invocations)).</span></span>
   *  <span data-ttu-id="2e3bd-972">如果 `I` 标识实例属性，</span><span class="sxs-lookup"><span data-stu-id="2e3bd-972">If `I` identifies an instance property,</span></span>
      * <span data-ttu-id="2e3bd-973">如果 `E` 为 `this` ，则 `I` 标识自动实现的属性 ([自动实现](classes.md#automatically-implemented-properties)) 不使用 setter 的属性，并且引用出现在类或结构类型的实例构造函数中 `T` ，则结果是一个变量，即由给定的 `I` 实例中给定的自动属性的隐藏支持字段 `T` `this` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-973">If `E` is `this`, `I` identifies an automatically implemented property ([Automatically implemented properties](classes.md#automatically-implemented-properties)) without a setter, and the reference occurs within an instance constructor for a class or struct type `T`, then the result is a variable, namely the hidden backing field for the auto-property given by `I` in the instance of `T` given by `this`.</span></span>
      * <span data-ttu-id="2e3bd-974">否则，结果为具有的关联实例表达式的属性访问  `E` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-974">Otherwise, the result is a property access with an associated instance expression of `E`.</span></span>
   *  <span data-ttu-id="2e3bd-975">如果 `T` 是 *class_type* 并且 `I` 标识 *class_type* 的实例字段：</span><span class="sxs-lookup"><span data-stu-id="2e3bd-975">If `T` is a *class_type* and `I` identifies an instance field of that *class_type*:</span></span>
      * <span data-ttu-id="2e3bd-976">如果的值 `E` 为 `null` ，则 `System.NullReferenceException` 引发。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-976">If the value of `E` is `null`, then a `System.NullReferenceException` is thrown.</span></span>
      * <span data-ttu-id="2e3bd-977">否则，如果字段为 `readonly` 并且引用出现在声明该字段的类的实例构造函数之外，则结果为值，即引用的对象中的字段的值  `I`  `E` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-977">Otherwise, if the field is `readonly` and the reference occurs outside an instance constructor of the class in which the field is declared, then the result is a value, namely the value of the field `I` in the object referenced by `E`.</span></span>
      * <span data-ttu-id="2e3bd-978">否则，结果为变量，即  `I` 引用的对象中的字段  `E` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-978">Otherwise, the result is a variable, namely the field `I` in the object referenced by `E`.</span></span>
   *  <span data-ttu-id="2e3bd-979">如果 `T` 是 *struct_type* 并且 `I` 标识 *struct_type* 的实例字段：</span><span class="sxs-lookup"><span data-stu-id="2e3bd-979">If `T` is a *struct_type* and `I` identifies an instance field of that *struct_type*:</span></span>
      * <span data-ttu-id="2e3bd-980">如果 `E` 是值，或者如果字段是 `readonly` ，并且引用出现在声明该字段的结构的实例构造函数之外，则结果为值，即  `I` 给定的结构实例中字段的值  `E` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-980">If `E` is a value, or if the field is `readonly` and the reference occurs outside an instance constructor of the struct in which the field is declared, then the result is a value, namely the value of the field `I` in the struct instance given by `E`.</span></span>
      * <span data-ttu-id="2e3bd-981">否则，结果为变量，即  `I` 给定的结构实例中的字段  `E` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-981">Otherwise, the result is a variable, namely the field `I` in the struct instance given by `E`.</span></span>
   *  <span data-ttu-id="2e3bd-982">如果 `I` 标识实例事件：</span><span class="sxs-lookup"><span data-stu-id="2e3bd-982">If `I` identifies an instance event:</span></span>
      * <span data-ttu-id="2e3bd-983">如果引用出现在声明事件的类或结构中，并且声明事件时没有 *event_accessor_declarations* ([事件](classes.md#events)) ，并且引用不是作为或运算符的左侧出现 `+=` ，则 `-=` `E.I` 会完全像处理 `I` 实例字段一样处理。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-983">If the reference occurs within the class or struct in which the event is declared, and the event was declared without *event_accessor_declarations* ([Events](classes.md#events)), and the reference does not occur as the left-hand side of a `+=` or `-=` operator, then `E.I` is processed exactly as if `I` was an instance field.</span></span>
      * <span data-ttu-id="2e3bd-984">否则，结果是具有关联的实例表达式的事件访问  `E` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-984">Otherwise, the result is an event access with an associated instance expression of `E`.</span></span>
*  <span data-ttu-id="2e3bd-985">否则，将尝试处理 `E.I` 为扩展方法调用 ([扩展方法](expressions.md#extension-method-invocations) 调用) 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-985">Otherwise, an attempt is made to process `E.I` as an extension method invocation ([Extension method invocations](expressions.md#extension-method-invocations)).</span></span> <span data-ttu-id="2e3bd-986">如果此操作失败， `E.I` 则为无效的成员引用，并且发生绑定时错误。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-986">If this fails, `E.I` is an invalid member reference, and a binding-time error occurs.</span></span>

#### <a name="identical-simple-names-and-type-names"></a><span data-ttu-id="2e3bd-987">相同的简单名称和类型名称</span><span class="sxs-lookup"><span data-stu-id="2e3bd-987">Identical simple names and type names</span></span>

<span data-ttu-id="2e3bd-988">在窗体的成员访问中 `E.I` ，如果 `E` 是单个标识符，并且 `E` 作为 *simple_name* ([简单名称](expressions.md#simple-names)) 是一个常数、字段、属性、局部变量或参数，其类型与 `E` 作为 *type_name* ([命名空间和类型名称](basic-concepts.md#namespace-and-type-names)) 的含义相同，则这两个可能的含义 `E` 都是允许的。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-988">In a member access of the form `E.I`, if `E` is a single identifier, and if the meaning of `E` as a *simple_name* ([Simple names](expressions.md#simple-names)) is a constant, field, property, local variable, or parameter with the same type as the meaning of `E` as a *type_name* ([Namespace and type names](basic-concepts.md#namespace-and-type-names)), then both possible meanings of `E` are permitted.</span></span> <span data-ttu-id="2e3bd-989">的两个可能含义 `E.I` 不明确，因为 `I` `E` 在这两种情况下都必须是类型的成员。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-989">The two possible meanings of `E.I` are never ambiguous, since `I` must necessarily be a member of the type `E` in both cases.</span></span> <span data-ttu-id="2e3bd-990">换言之，该规则只允许访问 `E` 编译时错误发生的静态成员和嵌套类型。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-990">In other words, the rule simply permits access to the static members and nested types of `E` where a compile-time error would otherwise have occurred.</span></span> <span data-ttu-id="2e3bd-991">例如：</span><span class="sxs-lookup"><span data-stu-id="2e3bd-991">For example:</span></span>
```csharp
struct Color
{
    public static readonly Color White = new Color(...);
    public static readonly Color Black = new Color(...);

    public Color Complement() {...}
}

class A
{
    public Color Color;                // Field Color of type Color

    void F() {
        Color = Color.Black;           // References Color.Black static member
        Color = Color.Complement();    // Invokes Complement() on Color field
    }

    static void G() {
        Color c = Color.White;         // References Color.White static member
    }
}
```

#### <a name="grammar-ambiguities"></a><span data-ttu-id="2e3bd-992">语法多义性</span><span class="sxs-lookup"><span data-stu-id="2e3bd-992">Grammar ambiguities</span></span>

<span data-ttu-id="2e3bd-993">*Simple_name* 的生产 ([简单名称](expressions.md#simple-names)) 和 *Member_access* ([成员访问](expressions.md#member-access)) 可以在表达式的语法中带来歧义。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-993">The productions for *simple_name* ([Simple names](expressions.md#simple-names)) and *member_access* ([Member access](expressions.md#member-access)) can give rise to ambiguities in the grammar for expressions.</span></span> <span data-ttu-id="2e3bd-994">例如，语句：</span><span class="sxs-lookup"><span data-stu-id="2e3bd-994">For example, the statement:</span></span>
```csharp
F(G<A,B>(7));
```
<span data-ttu-id="2e3bd-995">可以解释为对 `F` 具有两个参数和的的 `G < A` 调用 `B > (7)` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-995">could be interpreted as a call to `F` with two arguments, `G < A` and `B > (7)`.</span></span> <span data-ttu-id="2e3bd-996">另外，它也可以解释为 `F` 使用一个自变量进行调用，该参数是对  `G` 具有两个类型参数和一个正则参数的泛型方法的调用。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-996">Alternatively, it could be interpreted as a call to `F` with one argument, which is a call to a generic method `G` with two type arguments and one regular argument.</span></span>

<span data-ttu-id="2e3bd-997">如果可以将标记序列 (在上下文中) *simple_name* ([简单名称](expressions.md#simple-names)) 、 *member_access* ([成员访问](expressions.md#member-access) *) 或 pointer_member_access ()* type_argument_list [类型参数](types.md#type-arguments) (的 [指针成员访问](unsafe-code.md#pointer-member-access)) ，则将检查紧跟结束标记的标记 `>` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-997">If a sequence of tokens can be parsed (in context) as a *simple_name* ([Simple names](expressions.md#simple-names)), *member_access* ([Member access](expressions.md#member-access)), or *pointer_member_access* ([Pointer member access](unsafe-code.md#pointer-member-access)) ending with a *type_argument_list* ([Type arguments](types.md#type-arguments)), the token immediately following the closing `>` token is examined.</span></span> <span data-ttu-id="2e3bd-998">如果是</span><span class="sxs-lookup"><span data-stu-id="2e3bd-998">If it is one of</span></span>
```csharp
(  )  ]  }  :  ;  ,  .  ?  ==  !=  |  ^
```
<span data-ttu-id="2e3bd-999">然后， *type_argument_list* 将作为 *simple_name* 的一部分保留， *member_access* 或 *pointer_member_access* ，并放弃标记序列的任何其他可能分析。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-999">then the *type_argument_list* is retained as part of the *simple_name*, *member_access* or *pointer_member_access* and any other possible parse of the sequence of tokens is discarded.</span></span> <span data-ttu-id="2e3bd-1000">否则，不会将 *type_argument_list* 视为 *simple_name*、 *member_access* 或 *pointer_member_access* 的一部分，即使没有其他可能的标记序列分析也是如此。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1000">Otherwise, the *type_argument_list* is not considered to be part of the *simple_name*, *member_access* or *pointer_member_access*, even if there is no other possible parse of the sequence of tokens.</span></span> <span data-ttu-id="2e3bd-1001">请注意，分析 *namespace_or_type_name* ([命名空间和类型名称](basic-concepts.md#namespace-and-type-names)) 中的 *type_argument_list* 时，不会应用这些规则。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1001">Note that these rules are not applied when parsing a *type_argument_list* in a *namespace_or_type_name* ([Namespace and type names](basic-concepts.md#namespace-and-type-names)).</span></span> <span data-ttu-id="2e3bd-1002">语句</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1002">The statement</span></span>
```csharp
F(G<A,B>(7));
```
<span data-ttu-id="2e3bd-1003">根据此规则，将解释为 `F` 使用一个参数调用，该参数是对 `G` 具有两个类型参数和一个正则参数的泛型方法的调用。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1003">will, according to this rule, be interpreted as a call to `F` with one argument, which is a call to a generic method `G` with two type arguments and one regular argument.</span></span> <span data-ttu-id="2e3bd-1004">语句</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1004">The statements</span></span>
```csharp
F(G < A, B > 7);
F(G < A, B >> 7);
```
<span data-ttu-id="2e3bd-1005">每个都将解释为对 `F` 具有两个参数的的调用。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1005">will each be interpreted as a call to `F` with two arguments.</span></span> <span data-ttu-id="2e3bd-1006">语句</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1006">The statement</span></span>
```csharp
x = F < A > +y;
```
<span data-ttu-id="2e3bd-1007">将解释为小于运算符、大于运算符和一元正运算符，就像已写入语句 `x = (F < A) > (+y)` ，而不是使用 *type_argument_list* 后跟二元加运算符的 *simple_name* 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1007">will be interpreted as a less than operator, greater than operator, and unary plus operator, as if the statement had been written `x = (F < A) > (+y)`, instead of as a *simple_name* with a *type_argument_list* followed by a binary plus operator.</span></span> <span data-ttu-id="2e3bd-1008">在语句中</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1008">In the statement</span></span>
```csharp
x = y is C<T> + z;
```
<span data-ttu-id="2e3bd-1009">令牌 `C<T>` 被解释为具有 *type_argument_list* 的 *namespace_or_type_name* 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1009">the tokens `C<T>` are interpreted as a *namespace_or_type_name* with a *type_argument_list*.</span></span>

### <a name="invocation-expressions"></a><span data-ttu-id="2e3bd-1010">调用表达式</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1010">Invocation expressions</span></span>

<span data-ttu-id="2e3bd-1011">用于调用方法的 *invocation_expression* 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1011">An *invocation_expression* is used to invoke a method.</span></span>

```antlr
invocation_expression
    : primary_expression '(' argument_list? ')'
    ;
```

<span data-ttu-id="2e3bd-1012">如果以下至少一项保留，则 *invocation_expression* ([动态绑定](expressions.md#dynamic-binding)) 动态绑定：</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1012">An *invocation_expression* is dynamically bound ([Dynamic binding](expressions.md#dynamic-binding)) if at least one of the following holds:</span></span>

* <span data-ttu-id="2e3bd-1013">*Primary_expression* 具有编译时类型 `dynamic` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1013">The *primary_expression* has compile-time type `dynamic`.</span></span>
* <span data-ttu-id="2e3bd-1014">至少一个可选 *argument_list* 的参数具有编译时类型 `dynamic` ，而 *primary_expression* 没有委托类型。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1014">At least one argument of the optional *argument_list* has compile-time type `dynamic` and the *primary_expression* does not have a delegate type.</span></span>

<span data-ttu-id="2e3bd-1015">在这种情况下，编译器将 *invocation_expression* 分类为类型的值 `dynamic` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1015">In this case the compiler classifies the *invocation_expression* as a value of type `dynamic`.</span></span> <span data-ttu-id="2e3bd-1016">下面的规则将使用运行时类型（而不是具有编译时类型的 *primary_expression* 和参数的编译时类型）在运行时应用 *invocation_expression* 的含义 `dynamic` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1016">The rules below to determine the meaning of the *invocation_expression* are then applied at run-time, using the run-time type instead of the compile-time type of those of the *primary_expression* and arguments which have the compile-time type `dynamic`.</span></span> <span data-ttu-id="2e3bd-1017">如果 *primary_expression* 没有编译时类型 `dynamic` ，则方法调用会经历有限的编译时检查，如对 [动态重载决策进行编译时检查](expressions.md#compile-time-checking-of-dynamic-overload-resolution)中所述。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1017">If the *primary_expression* does not have compile-time type `dynamic`, then the method invocation undergoes a limited compile time check as described in [Compile-time checking of dynamic overload resolution](expressions.md#compile-time-checking-of-dynamic-overload-resolution).</span></span>

<span data-ttu-id="2e3bd-1018">*Invocation_expression* 的 *primary_expression* 必须为方法组或 *delegate_type* 的值。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1018">The *primary_expression* of an *invocation_expression* must be a method group or a value of a *delegate_type*.</span></span> <span data-ttu-id="2e3bd-1019">如果 *primary_expression* 是方法组，则 *Invocation_expression* 是 ([方法](expressions.md#method-invocations) 调用) 的方法调用。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1019">If the *primary_expression* is a method group, the *invocation_expression* is a method invocation ([Method invocations](expressions.md#method-invocations)).</span></span> <span data-ttu-id="2e3bd-1020">如果 *primary_expression* 是 *delegate_type* 的值，则 *invocation_expression* 为委托调用 () [委托](expressions.md#delegate-invocations) 调用。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1020">If the *primary_expression* is a value of a *delegate_type*, the *invocation_expression* is a delegate invocation ([Delegate invocations](expressions.md#delegate-invocations)).</span></span> <span data-ttu-id="2e3bd-1021">如果 *primary_expression* 既不是方法组也不是 *delegate_type* 的值，则发生绑定时错误。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1021">If the *primary_expression* is neither a method group nor a value of a *delegate_type*, a binding-time error occurs.</span></span>

<span data-ttu-id="2e3bd-1022">可选 *argument_list* ([参数列出](expressions.md#argument-lists)) 提供方法参数的值或变量引用。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1022">The optional *argument_list* ([Argument lists](expressions.md#argument-lists)) provides values or variable references for the parameters of the method.</span></span>

<span data-ttu-id="2e3bd-1023">评估 *invocation_expression* 的结果归类如下：</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1023">The result of evaluating an *invocation_expression* is classified as follows:</span></span>

*  <span data-ttu-id="2e3bd-1024">如果 *invocation_expression* 调用返回的方法或委托，则 `void` 结果为 nothing。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1024">If the *invocation_expression* invokes a method or delegate that returns `void`, the result is nothing.</span></span> <span data-ttu-id="2e3bd-1025">只允许在 *statement_expression* ([expression 语句](statements.md#expression-statements) 的上下文中使用或将表达式归类为 nothing，) 为 *lambda_expression* ([匿名函数表达式](expressions.md#anonymous-function-expressions) 的主体。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1025">An expression that is classified as nothing is permitted only in the context of a *statement_expression* ([Expression statements](statements.md#expression-statements)) or as the body of a *lambda_expression* ([Anonymous function expressions](expressions.md#anonymous-function-expressions)).</span></span> <span data-ttu-id="2e3bd-1026">否则，会发生绑定时错误。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1026">Otherwise a binding-time error occurs.</span></span>
*  <span data-ttu-id="2e3bd-1027">否则，结果为方法或委托返回的类型的值。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1027">Otherwise, the result is a value of the type returned by the method or delegate.</span></span>

#### <a name="method-invocations"></a><span data-ttu-id="2e3bd-1028">方法调用</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1028">Method invocations</span></span>

<span data-ttu-id="2e3bd-1029">对于方法调用， *invocation_expression* 的 *primary_expression* 必须为方法组。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1029">For a method invocation, the *primary_expression* of the *invocation_expression* must be a method group.</span></span> <span data-ttu-id="2e3bd-1030">方法组标识一种要调用的方法或用于从中选择要调用的特定方法的重载方法集。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1030">The method group identifies the one method to invoke or the set of overloaded methods from which to choose a specific method to invoke.</span></span> <span data-ttu-id="2e3bd-1031">在后一种情况下，确定要调用的特定方法的方式取决于 *argument_list* 中的参数类型所提供的上下文。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1031">In the latter case, determination of the specific method to invoke is based on the context provided by the types of the arguments in the *argument_list*.</span></span>

<span data-ttu-id="2e3bd-1032">对窗体的方法调用的绑定时处理 `M(A)` ，其中 `M` 是一个方法组 (可能包括 *type_argument_list*) ，并且 `A` 是可选的 *argument_list*，包括以下步骤：</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1032">The binding-time processing of a method invocation of the form `M(A)`, where `M` is a method group (possibly including a *type_argument_list*), and `A` is an optional *argument_list*, consists of the following steps:</span></span>

*  <span data-ttu-id="2e3bd-1033">构造方法调用的候选方法集。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1033">The set of candidate methods for the method invocation is constructed.</span></span> <span data-ttu-id="2e3bd-1034">对于 `F` 与方法组关联的每个方法 `M` ：</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1034">For each method `F` associated with the method group `M`:</span></span>
   *  <span data-ttu-id="2e3bd-1035">如果 `F` 为非泛型，则在以下情况下 `F` 是候选项：</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1035">If `F` is non-generic, `F` is a candidate when:</span></span>
      * <span data-ttu-id="2e3bd-1036">`M` 没有类型参数列表，并且</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1036">`M` has no type argument list, and</span></span>
      * <span data-ttu-id="2e3bd-1037">`F` 适用于 `A` ([适用的函数成员](expressions.md#applicable-function-member)) 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1037">`F` is applicable with respect to `A` ([Applicable function member](expressions.md#applicable-function-member)).</span></span>
   *  <span data-ttu-id="2e3bd-1038">如果 `F` 是泛型并且没有 `M` 类型参数列表，则在以下情况下 `F` 是候选项：</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1038">If `F` is generic and `M` has no type argument list, `F` is a candidate when:</span></span>
      * <span data-ttu-id="2e3bd-1039">类型推理 ([类型推理](expressions.md#type-inference)) 成功，推断调用的类型参数列表，</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1039">Type inference ([Type inference](expressions.md#type-inference)) succeeds, inferring a list of type arguments for the call, and</span></span>
      * <span data-ttu-id="2e3bd-1040">在将推断类型参数替换为相应的方法类型参数后，F 的参数列表中的所有构造类型都满足其约束 (满足 [约束](types.md#satisfying-constraints)) ，并且的参数列表 `F` 适用于 `A`)  (适用的 [函数成员](expressions.md#applicable-function-member) 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1040">Once the inferred type arguments are substituted for the corresponding method type parameters, all constructed types in the parameter list of F satisfy their constraints ([Satisfying constraints](types.md#satisfying-constraints)), and the parameter list of `F` is applicable with respect to `A` ([Applicable function member](expressions.md#applicable-function-member)).</span></span>
   *  <span data-ttu-id="2e3bd-1041">如果 `F` 为泛型并且 `M` 包含类型参数列表，则在以下情况下 `F` 是候选项：</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1041">If `F` is generic and `M` includes a type argument list, `F` is a candidate when:</span></span>
      * <span data-ttu-id="2e3bd-1042">`F` 具有与在类型参数列表中提供的相同数量的方法类型参数，并</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1042">`F` has the same number of method type parameters as were supplied in the type argument list, and</span></span>
      * <span data-ttu-id="2e3bd-1043">将类型参数替换为相应的方法类型参数后，F 的参数列表中的所有构造类型都满足其约束 (满足 [约束](types.md#satisfying-constraints)) ，并且的参数列表 `F` 适用于 `A`)  ([适用的函数成员](expressions.md#applicable-function-member) 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1043">Once the type arguments are substituted for the corresponding method type parameters, all constructed types in the parameter list of F satisfy their constraints ([Satisfying constraints](types.md#satisfying-constraints)), and the parameter list of `F` is applicable with respect to `A` ([Applicable function member](expressions.md#applicable-function-member)).</span></span>
*  <span data-ttu-id="2e3bd-1044">候选方法集减少为仅包含派生程度最高的类型中的方法：对于集中的每个方法 `C.F` ，其中 `C` 是声明方法的类型，而 `F` 在的基类型中声明的所有方法都将 `C` 从集合中删除。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1044">The set of candidate methods is reduced to contain only methods from the most derived types: For each method `C.F` in the set, where `C` is the type in which the method `F` is declared, all methods declared in a base type of `C` are removed from the set.</span></span> <span data-ttu-id="2e3bd-1045">此外，如果 `C` 是以外的类类型，则将 `object` 从集合中删除在接口类型中声明的所有方法。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1045">Furthermore, if `C` is a class type other than `object`, all methods declared in an interface type are removed from the set.</span></span> <span data-ttu-id="2e3bd-1046"> (此后一规则仅在以下情况下才会发生：方法组是对具有非 object 和非空有效接口集的有效基类的类型参数进行成员查找的结果。 ) </span><span class="sxs-lookup"><span data-stu-id="2e3bd-1046">(This latter rule only has affect when the method group was the result of a member lookup on a type parameter having an effective base class other than object and a non-empty effective interface set.)</span></span>
*  <span data-ttu-id="2e3bd-1047">如果生成的候选方法集为空，则会放弃按照以下步骤进行进一步处理，而是尝试将调用作为扩展方法调用处理 ([扩展方法](expressions.md#extension-method-invocations) 调用) 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1047">If the resulting set of candidate methods is empty, then further processing along the following steps are abandoned, and instead an attempt is made to process the invocation as an extension method invocation ([Extension method invocations](expressions.md#extension-method-invocations)).</span></span> <span data-ttu-id="2e3bd-1048">如果此操作失败，则不存在适用的方法，并且发生绑定时错误。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1048">If this fails, then no applicable methods exist, and a binding-time error occurs.</span></span>
*  <span data-ttu-id="2e3bd-1049">使用 [重载决策](expressions.md#overload-resolution)的重载决策规则标识候选方法集的最佳方法。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1049">The best method of the set of candidate methods is identified using the overload resolution rules of [Overload resolution](expressions.md#overload-resolution).</span></span> <span data-ttu-id="2e3bd-1050">如果无法识别单个最佳方法，则方法调用是不明确的，并发生绑定时错误。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1050">If a single best method cannot be identified, the method invocation is ambiguous, and a binding-time error occurs.</span></span> <span data-ttu-id="2e3bd-1051">执行重载决策时，在将类型参数替换为相应方法类型参数 (提供或推断) 后，将考虑泛型方法的参数。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1051">When performing overload resolution, the parameters of a generic method are considered after substituting the type arguments (supplied or inferred) for the corresponding method type parameters.</span></span>
*  <span data-ttu-id="2e3bd-1052">执行所选最佳方法的最终验证：</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1052">Final validation of the chosen best method is performed:</span></span>
   * <span data-ttu-id="2e3bd-1053">方法在方法组的上下文中进行验证：如果最佳方法为静态方法，则方法组必须通过类型 *simple_name* 或 *member_access* 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1053">The method is validated in the context of the method group: If the best method is a static method, the method group must have resulted from a *simple_name* or a *member_access* through a type.</span></span> <span data-ttu-id="2e3bd-1054">如果最佳方法是实例方法，则方法组必须由 *simple_name*、 *member_access* 通过变量或值或 *base_access* 导致。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1054">If the best method is an instance method, the method group must have resulted from a *simple_name*, a *member_access* through a variable or value, or a *base_access*.</span></span> <span data-ttu-id="2e3bd-1055">如果这两个要求都不成立，则发生绑定时错误。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1055">If neither of these requirements is true, a binding-time error occurs.</span></span>
   * <span data-ttu-id="2e3bd-1056">如果最佳方法是泛型方法，则会对照约束来检查 (提供或推断) 的类型参数， ([满足](types.md#satisfying-constraints) 泛型方法上声明) 的约束。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1056">If the best method is a generic method, the type arguments (supplied or inferred) are checked against the constraints ([Satisfying constraints](types.md#satisfying-constraints)) declared on the generic method.</span></span> <span data-ttu-id="2e3bd-1057">如果任何类型自变量不满足类型参数上的)  (，则会发生绑定时错误。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1057">If any type argument does not satisfy the corresponding constraint(s) on the type parameter, a binding-time error occurs.</span></span>

<span data-ttu-id="2e3bd-1058">按照上述步骤在绑定时选择并验证方法后，将根据 [动态重载解析的编译时检查](expressions.md#compile-time-checking-of-dynamic-overload-resolution)中所述的函数成员调用规则来处理实际的运行时调用。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1058">Once a method has been selected and validated at binding-time by the above steps, the actual run-time invocation is processed according to the rules of function member invocation described in [Compile-time checking of dynamic overload resolution](expressions.md#compile-time-checking-of-dynamic-overload-resolution).</span></span>

<span data-ttu-id="2e3bd-1059">上述解决方法的直观效果如下所示：若要查找由方法调用调用的特定方法，请从方法调用指示的类型开始，然后继续继承链，直到至少找到一个适用的、可访问的、非重写方法声明。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1059">The intuitive effect of the resolution rules described above is as follows: To locate the particular method invoked by a method invocation, start with the type indicated by the method invocation and proceed up the inheritance chain until at least one applicable, accessible, non-override method declaration is found.</span></span> <span data-ttu-id="2e3bd-1060">然后，对该类型中声明的适用的、可访问的、非重写方法集执行类型推理和重载决策，并调用此方法。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1060">Then perform type inference and overload resolution on the set of applicable, accessible, non-override methods declared in that type and invoke the method thus selected.</span></span> <span data-ttu-id="2e3bd-1061">如果未找到方法，请改为尝试将调用作为扩展方法调用来处理。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1061">If no method was found, try instead to process the invocation as an extension method invocation.</span></span>

#### <a name="extension-method-invocations"></a><span data-ttu-id="2e3bd-1062">扩展方法调用</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1062">Extension method invocations</span></span>

<span data-ttu-id="2e3bd-1063">在方法调用中 (在) 一种形式的[装箱实例上调用](expressions.md#invocations-on-boxed-instances)</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1063">In a method invocation ([Invocations on boxed instances](expressions.md#invocations-on-boxed-instances)) of one of the forms</span></span>
```csharp
expr . identifier ( )

expr . identifier ( args )

expr . identifier < typeargs > ( )

expr . identifier < typeargs > ( args )
```
<span data-ttu-id="2e3bd-1064">如果调用的正常处理找不到适用的方法，则会尝试将构造作为扩展方法调用来处理。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1064">if the normal processing of the invocation finds no applicable methods, an attempt is made to process the construct as an extension method invocation.</span></span> <span data-ttu-id="2e3bd-1065">如果 *expr* 或任意 *参数* 具有编译时类型 `dynamic` ，则扩展方法将不适用。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1065">If *expr* or any of the *args* has compile-time type `dynamic`, extension methods will not apply.</span></span>

<span data-ttu-id="2e3bd-1066">目标是查找最佳 *type_name* `C` ，以便可以进行相应的静态方法调用：</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1066">The objective is to find the best *type_name* `C`, so that the corresponding static method invocation can take place:</span></span>
```csharp
C . identifier ( expr )

C . identifier ( expr , args )

C . identifier < typeargs > ( expr )

C . identifier < typeargs > ( expr , args )
```

<span data-ttu-id="2e3bd-1067">扩展方法 `Ci.Mj` ***符合条件*** ：</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1067">An extension method `Ci.Mj` is ***eligible*** if:</span></span>

*  <span data-ttu-id="2e3bd-1068">`Ci` 是非泛型非泛型类</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1068">`Ci` is a non-generic, non-nested class</span></span>
*  <span data-ttu-id="2e3bd-1069">的名称 `Mj` 是 *标识符*</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1069">The name of `Mj` is *identifier*</span></span>
*  <span data-ttu-id="2e3bd-1070">`Mj` 作为静态方法应用于参数时，可访问且适用，如上所述</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1070">`Mj` is accessible and applicable when applied to the arguments as a static method as shown above</span></span>
*  <span data-ttu-id="2e3bd-1071">存在从 *expr* 到的第一个参数类型的隐式标识、引用或装箱转换 `Mj` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1071">An implicit identity, reference or boxing conversion exists from *expr* to the type of the first parameter of `Mj`.</span></span>

<span data-ttu-id="2e3bd-1072">的搜索 `C` 过程如下所示：</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1072">The search for `C` proceeds as follows:</span></span>

*  <span data-ttu-id="2e3bd-1073">从最接近的封闭命名空间声明开始，继续每个封闭命名空间声明，并以包含编译单元结束，接下来尝试查找一组候选的扩展方法：</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1073">Starting with the closest enclosing namespace declaration, continuing with each enclosing namespace declaration, and ending with the containing compilation unit, successive attempts are made to find a candidate set of extension methods:</span></span>
   * <span data-ttu-id="2e3bd-1074">如果给定的命名空间或编译单元直接包含具有符合条件的扩展方法的非泛型类型声明 `Ci` `Mj` ，则这些扩展方法集是候选集。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1074">If the given namespace or compilation unit directly contains non-generic type declarations `Ci` with eligible extension methods `Mj`, then the set of those extension methods is the candidate set.</span></span>
   * <span data-ttu-id="2e3bd-1075">如果 `Ci` 由给定命名空间或编译单元中 *using_namespace_directive* s 导入的 *using_static_declarations* 和直接声明的类型包含符合条件的扩展方法 `Mj` ，则这些扩展方法集是候选集。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1075">If types `Ci` imported by *using_static_declarations* and directly declared in namespaces imported by *using_namespace_directive* s in the given namespace or compilation unit directly contain eligible extension methods `Mj`, then the set of those extension methods is the candidate set.</span></span>
*  <span data-ttu-id="2e3bd-1076">如果在任何封闭命名空间声明或编译单元中均未找到候选集，则会发生编译时错误。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1076">If no candidate set is found in any enclosing namespace declaration or compilation unit, a compile-time error occurs.</span></span>
*  <span data-ttu-id="2e3bd-1077">否则，将重载决策应用于候选集，如 ([重载决策](expressions.md#overload-resolution)) 中所述。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1077">Otherwise, overload resolution is applied to the candidate set as described in ([Overload resolution](expressions.md#overload-resolution)).</span></span> <span data-ttu-id="2e3bd-1078">如果未找到单个最佳方法，则会发生编译时错误。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1078">If no single best method is found, a compile-time error occurs.</span></span>
*  <span data-ttu-id="2e3bd-1079">`C` 是在其中将最佳方法声明为扩展方法的类型。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1079">`C` is the type within which the best method is declared as an extension method.</span></span>

<span data-ttu-id="2e3bd-1080">使用 `C` 作为目标，将方法调用作为静态方法调用处理 () [动态重载解析的编译时检查](expressions.md#compile-time-checking-of-dynamic-overload-resolution) 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1080">Using `C` as a target, the method call is then processed as a static method invocation ([Compile-time checking of dynamic overload resolution](expressions.md#compile-time-checking-of-dynamic-overload-resolution)).</span></span>

<span data-ttu-id="2e3bd-1081">前面的规则意味着实例方法优先于扩展方法，内部命名空间声明中提供的扩展方法优先于外部命名空间声明中提供的扩展方法，并且直接在命名空间中声明的扩展方法优先于通过使用命名空间指令导入到同一命名空间中的扩展方法。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1081">The preceding rules mean that instance methods take precedence over extension methods, that extension methods available in inner namespace declarations take precedence over extension methods available in outer namespace declarations, and that extension methods declared directly in a namespace take precedence over extension methods imported into that same namespace with a using namespace directive.</span></span> <span data-ttu-id="2e3bd-1082">例如：</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1082">For example:</span></span>
```csharp
public static class E
{
    public static void F(this object obj, int i) { }

    public static void F(this object obj, string s) { }
}

class A { }

class B
{
    public void F(int i) { }
}

class C
{
    public void F(object obj) { }
}

class X
{
    static void Test(A a, B b, C c) {
        a.F(1);              // E.F(object, int)
        a.F("hello");        // E.F(object, string)

        b.F(1);              // B.F(int)
        b.F("hello");        // E.F(object, string)

        c.F(1);              // C.F(object)
        c.F("hello");        // C.F(object)
    }
}
```

<span data-ttu-id="2e3bd-1083">在此示例中， `B` 的方法优先于第一个扩展方法，并且 `C` 的方法优先于这两个扩展方法。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1083">In the example, `B`'s method takes precedence over the first extension method, and `C`'s method takes precedence over both extension methods.</span></span>

```csharp
public static class C
{
    public static void F(this int i) { Console.WriteLine("C.F({0})", i); }
    public static void G(this int i) { Console.WriteLine("C.G({0})", i); }
    public static void H(this int i) { Console.WriteLine("C.H({0})", i); }
}

namespace N1
{
    public static class D
    {
        public static void F(this int i) { Console.WriteLine("D.F({0})", i); }
        public static void G(this int i) { Console.WriteLine("D.G({0})", i); }
    }
}

namespace N2
{
    using N1;

    public static class E
    {
        public static void F(this int i) { Console.WriteLine("E.F({0})", i); }
    }

    class Test
    {
        static void Main(string[] args)
        {
            1.F();
            2.G();
            3.H();
        }
    }
}
```

<span data-ttu-id="2e3bd-1084">此示例的输出为：</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1084">The output of this example is:</span></span>
```console
E.F(1)
D.G(2)
C.H(3)
```
<span data-ttu-id="2e3bd-1085">`D.G` 优先于 `C.G` ，并且 `E.F` 优先于 `D.F` 和 `C.F` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1085">`D.G` takes precedence over `C.G`, and `E.F` takes precedence over both `D.F` and `C.F`.</span></span>

#### <a name="delegate-invocations"></a><span data-ttu-id="2e3bd-1086">委托调用</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1086">Delegate invocations</span></span>

<span data-ttu-id="2e3bd-1087">对于委托调用， *invocation_expression* 的 *primary_expression* 必须是 *delegate_type* 的值。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1087">For a delegate invocation, the *primary_expression* of the *invocation_expression* must be a value of a *delegate_type*.</span></span> <span data-ttu-id="2e3bd-1088">此外，将 *delegate_type* 视为与 *delegate_type* 具有相同参数列表的函数成员， *delegate_type* 必须 ([适用的函数成员](expressions.md#applicable-function-member)，) argument_list 的 invocation_expression。  </span><span class="sxs-lookup"><span data-stu-id="2e3bd-1088">Furthermore, considering the *delegate_type* to be a function member with the same parameter list as the *delegate_type*, the *delegate_type* must be applicable ([Applicable function member](expressions.md#applicable-function-member)) with respect to the *argument_list* of the *invocation_expression*.</span></span>

<span data-ttu-id="2e3bd-1089">对窗体的委托调用的运行时处理 `D(A)` ，其中 `D` 是 *delegate_type* 的 *primary_expression* ， `A` 是可选的 *argument_list*，包括以下步骤：</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1089">The run-time processing of a delegate invocation of the form `D(A)`, where `D` is a *primary_expression* of a *delegate_type* and `A` is an optional *argument_list*, consists of the following steps:</span></span>

*  <span data-ttu-id="2e3bd-1090">`D` 进行计算。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1090">`D` is evaluated.</span></span> <span data-ttu-id="2e3bd-1091">如果此计算导致异常，则不执行其他步骤。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1091">If this evaluation causes an exception, no further steps are executed.</span></span>
*  <span data-ttu-id="2e3bd-1092">的值 `D` 被检查为有效。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1092">The value of `D` is checked to be valid.</span></span> <span data-ttu-id="2e3bd-1093">如果的值 `D` 为 `null` ，则 `System.NullReferenceException` 会引发，而不执行进一步的步骤。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1093">If the value of `D` is `null`, a `System.NullReferenceException` is thrown and no further steps are executed.</span></span>
*  <span data-ttu-id="2e3bd-1094">否则， `D` 是对委托实例的引用。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1094">Otherwise, `D` is a reference to a delegate instance.</span></span> <span data-ttu-id="2e3bd-1095">函数成员调用 (对 [动态重载决策的编译时检查](expressions.md#compile-time-checking-of-dynamic-overload-resolution) ，) 在委托调用列表中的每个可调用实体上执行。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1095">Function member invocations ([Compile-time checking of dynamic overload resolution](expressions.md#compile-time-checking-of-dynamic-overload-resolution)) are performed on each of the callable entities in the invocation list of the delegate.</span></span> <span data-ttu-id="2e3bd-1096">对于包含实例和实例方法的可调用实体，调用实例是包含在可调用实体中的实例。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1096">For callable entities consisting of an instance and instance method, the instance for the invocation is the instance contained in the callable entity.</span></span>

### <a name="element-access"></a><span data-ttu-id="2e3bd-1097">元素访问</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1097">Element access</span></span>

<span data-ttu-id="2e3bd-1098">*Element_access* 包含一个 *primary_no_array_creation_expression*，后跟一个 "" 标记，后跟一个 `[` *argument_list*，后跟一个 " `]` " 标记。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1098">An *element_access* consists of a *primary_no_array_creation_expression*, followed by a "`[`" token, followed by an *argument_list*, followed by a "`]`" token.</span></span> <span data-ttu-id="2e3bd-1099">*Argument_list* 由一个或多个由逗号分隔的 *参数* 组成。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1099">The *argument_list* consists of one or more *argument* s, separated by commas.</span></span>

```antlr
element_access
    : primary_no_array_creation_expression '[' expression_list ']'
    ;
```

<span data-ttu-id="2e3bd-1100">不允许 *element_access* 的 *argument_list* 包含 `ref` 或 `out` 参数。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1100">The *argument_list* of an *element_access* is not allowed to contain `ref` or `out` arguments.</span></span>

<span data-ttu-id="2e3bd-1101">如果以下至少一项保留，则 *element_access* ([动态绑定](expressions.md#dynamic-binding)) 动态绑定：</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1101">An *element_access* is dynamically bound ([Dynamic binding](expressions.md#dynamic-binding)) if at least one of the following holds:</span></span>

* <span data-ttu-id="2e3bd-1102">*Primary_no_array_creation_expression* 具有编译时类型 `dynamic` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1102">The *primary_no_array_creation_expression* has compile-time type `dynamic`.</span></span>
* <span data-ttu-id="2e3bd-1103">*Argument_list* 至少有一个表达式的编译时类型为 `dynamic` ， *primary_no_array_creation_expression* 没有数组类型。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1103">At least one expression of the *argument_list* has compile-time type `dynamic` and the *primary_no_array_creation_expression* does not have an array type.</span></span>

<span data-ttu-id="2e3bd-1104">在这种情况下，编译器将 *element_access* 分类为类型的值 `dynamic` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1104">In this case the compiler classifies the *element_access* as a value of type `dynamic`.</span></span> <span data-ttu-id="2e3bd-1105">下面的规则用于确定 *element_access* 的含义，然后使用运行时类型（而不是具有编译时类型的 *primary_no_array_creation_expression* 和 *argument_list* 表达式的编译时类型）在运行时应用 `dynamic` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1105">The rules below to determine the meaning of the *element_access* are then applied at run-time, using the run-time type instead of the compile-time type of those of the *primary_no_array_creation_expression* and *argument_list* expressions which have the compile-time type `dynamic`.</span></span> <span data-ttu-id="2e3bd-1106">如果 *primary_no_array_creation_expression* 没有编译时类型 `dynamic` ，则元素访问会经历有限的编译时检查，如对 [动态重载决策进行编译时检查](expressions.md#compile-time-checking-of-dynamic-overload-resolution)中所述。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1106">If the *primary_no_array_creation_expression* does not have compile-time type `dynamic`, then the element access undergoes a limited compile time check as described in [Compile-time checking of dynamic overload resolution](expressions.md#compile-time-checking-of-dynamic-overload-resolution).</span></span>

<span data-ttu-id="2e3bd-1107">如果 *element_access* 的 *primary_no_array_creation_expression* 是 *array_type* 的值，则 *element_access* 是数组 [访问 (的](expressions.md#array-access)数组访问权限。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1107">If the *primary_no_array_creation_expression* of an *element_access* is a value of an *array_type*, the *element_access* is an array access ([Array access](expressions.md#array-access)).</span></span> <span data-ttu-id="2e3bd-1108">否则， *primary_no_array_creation_expression* 必须是具有一个或多个索引器成员的类、结构或接口类型的变量或值，在这种情况下， *element_access* 是索引器访问 ([索引器访问](expressions.md#indexer-access)) 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1108">Otherwise, the *primary_no_array_creation_expression* must be a variable or value of a class, struct, or interface type that has one or more indexer members, in which case the *element_access* is an indexer access ([Indexer access](expressions.md#indexer-access)).</span></span>

#### <a name="array-access"></a><span data-ttu-id="2e3bd-1109">数组访问</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1109">Array access</span></span>

<span data-ttu-id="2e3bd-1110">对于数组访问， *element_access* 的 *primary_no_array_creation_expression* 必须是 *array_type* 的值。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1110">For an array access, the *primary_no_array_creation_expression* of the *element_access* must be a value of an *array_type*.</span></span> <span data-ttu-id="2e3bd-1111">而且，不允许数组访问的 *argument_list* 包含命名参数。 *Argument_list* 中的表达式的数目必须与 *array_type* 的排名相同，并且每个表达式的类型必须为、、、或，才能 `int` `uint` `long` `ulong` 隐式转换为这些类型中的一个或多个。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1111">Furthermore, the *argument_list* of an array access is not allowed to contain named arguments.The number of expressions in the *argument_list* must be the same as the rank of the *array_type*, and each expression must be of type `int`, `uint`, `long`, `ulong`, or must be implicitly convertible to one or more of these types.</span></span>

<span data-ttu-id="2e3bd-1112">计算数组访问的结果是数组的元素类型的一个变量，即，值所选择的数组元素 (s) 的表达式 (s) 在 *argument_list* 中。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1112">The result of evaluating an array access is a variable of the element type of the array, namely the array element selected by the value(s) of the expression(s) in the *argument_list*.</span></span>

<span data-ttu-id="2e3bd-1113">运行时处理窗体的数组访问 `P[A]` ，其中 `P` 是 *array_type* 的 *primary_no_array_creation_expression* ，而 `A` 是 *argument_list*，其中包含以下步骤：</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1113">The run-time processing of an array access of the form `P[A]`, where `P` is a *primary_no_array_creation_expression* of an *array_type* and `A` is an *argument_list*, consists of the following steps:</span></span>

*  <span data-ttu-id="2e3bd-1114">`P` 进行计算。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1114">`P` is evaluated.</span></span> <span data-ttu-id="2e3bd-1115">如果此计算导致异常，则不执行其他步骤。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1115">If this evaluation causes an exception, no further steps are executed.</span></span>
*  <span data-ttu-id="2e3bd-1116">将按从左到右的顺序计算 *argument_list* 的索引表达式。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1116">The index expressions of the *argument_list* are evaluated in order, from left to right.</span></span> <span data-ttu-id="2e3bd-1117">按照每个索引表达式的计算，将执行隐式转换 ([隐式](conversions.md#implicit-conversions) 转换为以下类型之一) ： `int` 、 `uint` 、 `long` 、 `ulong` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1117">Following evaluation of each index expression, an implicit conversion ([Implicit conversions](conversions.md#implicit-conversions)) to one of the following types is performed: `int`, `uint`, `long`, `ulong`.</span></span> <span data-ttu-id="2e3bd-1118">在此列表中选择了隐式转换的第一种类型。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1118">The first type in this list for which an implicit conversion exists is chosen.</span></span> <span data-ttu-id="2e3bd-1119">例如，如果索引表达式的类型为 `short` ，则将执行隐式转换 `int` ，因为可以从到之间进行隐式转换 `short` `int` `short` `long` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1119">For instance, if the index expression is of type `short` then an implicit conversion to `int` is performed, since implicit conversions from `short` to `int` and from `short` to `long` are possible.</span></span> <span data-ttu-id="2e3bd-1120">如果索引表达式的计算或后续的隐式转换导致异常，则不会再计算索引表达式，也不会执行任何其他步骤。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1120">If evaluation of an index expression or the subsequent implicit conversion causes an exception, then no further index expressions are evaluated and no further steps are executed.</span></span>
*  <span data-ttu-id="2e3bd-1121">的值 `P` 被检查为有效。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1121">The value of `P` is checked to be valid.</span></span> <span data-ttu-id="2e3bd-1122">如果的值 `P` 为 `null` ，则 `System.NullReferenceException` 会引发，而不执行进一步的步骤。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1122">If the value of `P` is `null`, a `System.NullReferenceException` is thrown and no further steps are executed.</span></span>
*  <span data-ttu-id="2e3bd-1123">根据引用的数组实例的每个维度的实际界限检查 *argument_list* 中每个表达式的值 `P` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1123">The value of each expression in the *argument_list* is checked against the actual bounds of each dimension of the array instance referenced by `P`.</span></span> <span data-ttu-id="2e3bd-1124">如果一个或多个值超出范围， `System.IndexOutOfRangeException` 则会引发，而不执行进一步的步骤。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1124">If one or more values are out of range, a `System.IndexOutOfRangeException` is thrown and no further steps are executed.</span></span>
*  <span data-ttu-id="2e3bd-1125">计算 (s) 指定的数组元素位置，此位置成为数组访问的结果。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1125">The location of the array element given by the index expression(s) is computed, and this location becomes the result of the array access.</span></span>

#### <a name="indexer-access"></a><span data-ttu-id="2e3bd-1126">索引器访问</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1126">Indexer access</span></span>

<span data-ttu-id="2e3bd-1127">对于索引器访问， *element_access* 的 *primary_no_array_creation_expression* 必须是类、结构或接口类型的变量或值，并且此类型必须实现适用于 *element_access* 的 *argument_list* 的一个或多个索引器。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1127">For an indexer access, the *primary_no_array_creation_expression* of the *element_access* must be a variable or value of a class, struct, or interface type, and this type must implement one or more indexers that are applicable with respect to the *argument_list* of the *element_access*.</span></span>

<span data-ttu-id="2e3bd-1128">对窗体的索引器访问的绑定时处理 `P[A]` ，其中 `P` 是类、结构或接口类型的 *primary_no_array_creation_expression* `T` ，而 `A` 是 *argument_list*，它包含以下步骤：</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1128">The binding-time processing of an indexer access of the form `P[A]`, where `P` is a *primary_no_array_creation_expression* of a class, struct, or interface type `T`, and `A` is an *argument_list*, consists of the following steps:</span></span>

*  <span data-ttu-id="2e3bd-1129">构造由提供的一组索引器 `T` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1129">The set of indexers provided by `T` is constructed.</span></span> <span data-ttu-id="2e3bd-1130">该集包含在中声明的所有索引器 `T` 或 `T` 不是 `override` 声明且可在当前上下文中访问 ([成员访问](basic-concepts.md#member-access)) 的基类型。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1130">The set consists of all indexers declared in `T` or a base type of `T` that are not `override` declarations and are accessible in the current context ([Member access](basic-concepts.md#member-access)).</span></span>
*  <span data-ttu-id="2e3bd-1131">该集被简化为那些适用且不被其他索引器隐藏的索引器。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1131">The set is reduced to those indexers that are applicable and not hidden by other indexers.</span></span> <span data-ttu-id="2e3bd-1132">以下规则应用于集中的每个索引器 `S.I` ，其中 `S` 是声明索引器的类型 `I` ：</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1132">The following rules are applied to each indexer `S.I` in the set, where `S` is the type in which the indexer `I` is declared:</span></span>
   * <span data-ttu-id="2e3bd-1133">如果不适 `I` 用于 `A` ([适用的函数成员](expressions.md#applicable-function-member)) ，则 `I` 将从集合中删除。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1133">If `I` is not applicable with respect to `A` ([Applicable function member](expressions.md#applicable-function-member)), then `I` is removed from the set.</span></span>
   * <span data-ttu-id="2e3bd-1134">如果 `I` 适用于 `A` ([适用的函数成员](expressions.md#applicable-function-member)) ，则将从集合中删除在的基类型中声明的所有索引器 `S` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1134">If `I` is applicable with respect to `A` ([Applicable function member](expressions.md#applicable-function-member)), then all indexers declared in a base type of `S` are removed from the set.</span></span>
   * <span data-ttu-id="2e3bd-1135">如果 `I` 适用于 `A` ([适用的函数成员](expressions.md#applicable-function-member)) 并且是以外 `S` 的类类型，则将 `object` 从集合中删除在接口中声明的所有索引器。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1135">If `I` is applicable with respect to `A` ([Applicable function member](expressions.md#applicable-function-member)) and `S` is a class type other than `object`, all indexers declared in an interface are removed from the set.</span></span>
*  <span data-ttu-id="2e3bd-1136">如果生成的候选索引器集为空，则不存在适用的索引器，并且发生绑定时错误。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1136">If the resulting set of candidate indexers is empty, then no applicable indexers exist, and a binding-time error occurs.</span></span>
*  <span data-ttu-id="2e3bd-1137">使用 [重载决策](expressions.md#overload-resolution)的重载决策规则识别候选索引器集的最佳索引器。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1137">The best indexer of the set of candidate indexers is identified using the overload resolution rules of [Overload resolution](expressions.md#overload-resolution).</span></span> <span data-ttu-id="2e3bd-1138">如果无法识别单个最佳索引器，索引器访问不明确，并发生绑定时错误。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1138">If a single best indexer cannot be identified, the indexer access is ambiguous, and a binding-time error occurs.</span></span>
*  <span data-ttu-id="2e3bd-1139">将按从左到右的顺序计算 *argument_list* 的索引表达式。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1139">The index expressions of the *argument_list* are evaluated in order, from left to right.</span></span> <span data-ttu-id="2e3bd-1140">处理索引器访问的结果是分类为索引器访问的表达式。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1140">The result of processing the indexer access is an expression classified as an indexer access.</span></span> <span data-ttu-id="2e3bd-1141">索引器访问表达式引用前面步骤中确定的索引器，并且具有的关联实例表达式 `P` 和的关联自变量列表 `A` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1141">The indexer access expression references the indexer determined in the step above, and has an associated instance expression of `P` and an associated argument list of `A`.</span></span>

<span data-ttu-id="2e3bd-1142">索引器访问会导致索引器的 *get* 访问器或 *set 访问* 器的调用，具体取决于使用它的上下文。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1142">Depending on the context in which it is used, an indexer access causes invocation of either the *get accessor* or the *set accessor* of the indexer.</span></span> <span data-ttu-id="2e3bd-1143">如果索引器访问是赋值的目标，则调用 *set 访问器* ([简单赋值](expressions.md#simple-assignment)) 分配新值。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1143">If the indexer access is the target of an assignment, the *set accessor* is invoked to assign a new value ([Simple assignment](expressions.md#simple-assignment)).</span></span> <span data-ttu-id="2e3bd-1144">在所有其他情况下，将调用 *get 访问器* 以获取当前值 () [表达式的值](expressions.md#values-of-expressions) 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1144">In all other cases, the *get accessor* is invoked to obtain the current value ([Values of expressions](expressions.md#values-of-expressions)).</span></span>

### <a name="this-access"></a><span data-ttu-id="2e3bd-1145">此访问</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1145">This access</span></span>

<span data-ttu-id="2e3bd-1146">*This_access* 包含保留字 `this` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1146">A *this_access* consists of the reserved word `this`.</span></span>

```antlr
this_access
    : 'this'
    ;
```

<span data-ttu-id="2e3bd-1147">只能在实例构造函数、实例方法或实例访问器的 *块* 中使用 *this_access* 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1147">A *this_access* is permitted only in the *block* of an instance constructor, an instance method, or an instance accessor.</span></span> <span data-ttu-id="2e3bd-1148">它具有下列含义之一：</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1148">It has one of the following meanings:</span></span>

*  <span data-ttu-id="2e3bd-1149">当 `this` 用于类的实例构造函数中的 *primary_expression* 时，它会归类为值。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1149">When `this` is used in a *primary_expression* within an instance constructor of a class, it is classified as a value.</span></span> <span data-ttu-id="2e3bd-1150">值的类型是类 [的实例类型)  (](classes.md#the-instance-type) 实例类型，在此类中发生了使用，值是对要构造的对象的引用。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1150">The type of the value is the instance type ([The instance type](classes.md#the-instance-type)) of the class within which the usage occurs, and the value is a reference to the object being constructed.</span></span>
*  <span data-ttu-id="2e3bd-1151">当 `this` 用于类的实例方法或实例访问器中的 *primary_expression* 时，它将被分类为值。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1151">When `this` is used in a *primary_expression* within an instance method or instance accessor of a class, it is classified as a value.</span></span> <span data-ttu-id="2e3bd-1152">值的类型为实例类型 (实例类型) 在其中发生使用的类的 [实例](classes.md#the-instance-type) 类型，值是对为其调用方法或访问器的对象的引用。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1152">The type of the value is the instance type ([The instance type](classes.md#the-instance-type)) of the class within which the usage occurs, and the value is a reference to the object for which the method or accessor was invoked.</span></span>
*  <span data-ttu-id="2e3bd-1153">当 `this` 用于结构的实例构造函数中的 *primary_expression* 时，它会归类为变量。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1153">When `this` is used in a *primary_expression* within an instance constructor of a struct, it is classified as a variable.</span></span> <span data-ttu-id="2e3bd-1154">变量的类型是实例类型 (实例类型) 在其中发生使用的结构的 [实例](classes.md#the-instance-type) 类型，而变量表示正在构造的结构。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1154">The type of the variable is the instance type ([The instance type](classes.md#the-instance-type)) of the struct within which the usage occurs, and the variable represents the struct being constructed.</span></span> <span data-ttu-id="2e3bd-1155">`this`结构的实例构造函数的变量的行为与结构类型的参数完全相同， `out` 具体而言，这意味着必须在实例构造函数的每个执行路径中明确赋值变量。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1155">The `this` variable of an instance constructor of a struct behaves exactly the same as an `out` parameter of the struct type—in particular, this means that the variable must be definitely assigned in every execution path of the instance constructor.</span></span>
*  <span data-ttu-id="2e3bd-1156">当 `this` 用于结构的实例方法或实例访问器中的 *primary_expression* 时，它会归类为变量。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1156">When `this` is used in a *primary_expression* within an instance method or instance accessor of a struct, it is classified as a variable.</span></span> <span data-ttu-id="2e3bd-1157">变量的类型是实例类型 (在其中发生使用的结构的 [实例类型](classes.md#the-instance-type)) 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1157">The type of the variable is the instance type ([The instance type](classes.md#the-instance-type)) of the struct within which the usage occurs.</span></span>
   * <span data-ttu-id="2e3bd-1158">如果方法或访问器不是迭代 [器)  (迭代器](classes.md#iterators) ，则该 `this` 变量表示调用了方法或访问器的结构，其行为与 `ref` 结构类型的参数完全相同。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1158">If the method or accessor is not an iterator ([Iterators](classes.md#iterators)), the `this` variable represents the struct for which the method or accessor was invoked, and behaves exactly the same as a `ref` parameter of the struct type.</span></span>
   * <span data-ttu-id="2e3bd-1159">如果方法或访问器是迭代器，则该 `this` 变量表示为其调用了方法或访问器的结构的副本，其行为与结构类型的值参数完全相同。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1159">If the method or accessor is an iterator, the `this` variable represents a copy of the struct for which the method or accessor was invoked, and behaves exactly the same as a value parameter of the struct type.</span></span>

<span data-ttu-id="2e3bd-1160">如果 `this` 在上面列出的上下文之外的 *primary_expression* 中使用了，则会发生编译时错误。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1160">Use of `this` in a *primary_expression* in a context other than the ones listed above is a compile-time error.</span></span> <span data-ttu-id="2e3bd-1161">特别是，不能在 `this` 静态方法、静态属性访问器或字段声明的 *variable_initializer* 中引用。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1161">In particular, it is not possible to refer to `this` in a static method, a static property accessor, or in a *variable_initializer* of a field declaration.</span></span>

### <a name="base-access"></a><span data-ttu-id="2e3bd-1162">基本访问权限</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1162">Base access</span></span>

<span data-ttu-id="2e3bd-1163">*Base_access* 包含保留字 `base` ，后面跟有 " `.` " 标记、标识符或用方括号括起来的 *argument_list* ：</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1163">A *base_access* consists of the reserved word `base` followed by either a "`.`" token and an identifier or an *argument_list* enclosed in square brackets:</span></span>

```antlr
base_access
    : 'base' '.' identifier
    | 'base' '[' expression_list ']'
    ;
```

<span data-ttu-id="2e3bd-1164">*Base_access* 用于访问当前类或结构中名称类似的成员隐藏的基类成员。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1164">A *base_access* is used to access base class members that are hidden by similarly named members in the current class or struct.</span></span> <span data-ttu-id="2e3bd-1165">只能在实例构造函数、实例方法或实例访问器的 *块* 中使用 *base_access* 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1165">A *base_access* is permitted only in the *block* of an instance constructor, an instance method, or an instance accessor.</span></span> <span data-ttu-id="2e3bd-1166">`base.I`在类或结构中发生时， `I` 必须表示该类或结构的基类的成员。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1166">When `base.I` occurs in a class or struct, `I` must denote a member of the base class of that class or struct.</span></span> <span data-ttu-id="2e3bd-1167">同样， `base[E]` 在类中发生时，基类中必须存在适用的索引器。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1167">Likewise, when `base[E]` occurs in a class, an applicable indexer must exist in the base class.</span></span>

<span data-ttu-id="2e3bd-1168">在绑定时，该窗体的 *base_access* 表达式 `base.I` 的 `base[E]` 计算方式与写入它们的方式完全 `((B)this).I` 相同 `((B)this)[E]` ，其中 `B` 是发生构造的类或结构的基类。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1168">At binding-time, *base_access* expressions of the form `base.I` and `base[E]` are evaluated exactly as if they were written `((B)this).I` and `((B)this)[E]`, where `B` is the base class of the class or struct in which the construct occurs.</span></span> <span data-ttu-id="2e3bd-1169">因此， `base.I` 和 `base[E]` 对应于 `this.I` 和 `this[E]` ，只不过 `this` 将视为基类的一个实例。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1169">Thus, `base.I` and `base[E]` correspond to `this.I` and `this[E]`, except `this` is viewed as an instance of the base class.</span></span>

<span data-ttu-id="2e3bd-1170">如果 *base_access* (方法、属性或索引器) 引用虚拟函数成员，则在运行时确定要调用哪个函数成员 (对 [动态重载决策) 的编译时检查](expressions.md#compile-time-checking-of-dynamic-overload-resolution) 会发生更改。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1170">When a *base_access* references a virtual function member (a method, property, or indexer), the determination of which function member to invoke at run-time ([Compile-time checking of dynamic overload resolution](expressions.md#compile-time-checking-of-dynamic-overload-resolution)) is changed.</span></span> <span data-ttu-id="2e3bd-1171">所调用的函数成员是通过 [查找) 函数](classes.md#virtual-methods) (成员的派生程度最高的 (实现来确定的 `B` ，而不是与运行时类型相关，而不是 `this` 像在非基本访问) 中那样常见。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1171">The function member that is invoked is determined by finding the most derived implementation ([Virtual methods](classes.md#virtual-methods)) of the function member with respect to `B` (instead of with respect to the run-time type of `this`, as would be usual in a non-base access).</span></span> <span data-ttu-id="2e3bd-1172">因此，在 `override` `virtual` 函数成员的中，可以使用 *base_access* 来调用函数成员的继承实现。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1172">Thus, within an `override` of a `virtual` function member, a *base_access* can be used to invoke the inherited implementation of the function member.</span></span> <span data-ttu-id="2e3bd-1173">如果 *base_access* 引用的函数成员是抽象的，则会发生绑定时错误。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1173">If the function member referenced by a *base_access* is abstract, a binding-time error occurs.</span></span>

### <a name="postfix-increment-and-decrement-operators"></a><span data-ttu-id="2e3bd-1174">后缀增量和减量运算符</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1174">Postfix increment and decrement operators</span></span>

```antlr
post_increment_expression
    : primary_expression '++'
    ;

post_decrement_expression
    : primary_expression '--'
    ;
```

<span data-ttu-id="2e3bd-1175">后缀递增或递减运算的操作数必须是分类为变量、属性访问或索引器访问的表达式。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1175">The operand of a postfix increment or decrement operation must be an expression classified as a variable, a property access, or an indexer access.</span></span> <span data-ttu-id="2e3bd-1176">操作的结果是与操作数类型相同的值。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1176">The result of the operation is a value of the same type as the operand.</span></span>

<span data-ttu-id="2e3bd-1177">如果 *primary_expression* 具有编译时类型 `dynamic` ，则运算符会动态绑定 ([动态绑定](expressions.md#dynamic-binding)) ， *post_increment_expression* 或 *post_decrement_expression* 具有编译时类型， `dynamic` 并且以下规则在运行时使用 *primary_expression* 的运行时类型应用。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1177">If the *primary_expression* has the compile-time type `dynamic` then the operator is dynamically bound ([Dynamic binding](expressions.md#dynamic-binding)), the *post_increment_expression* or *post_decrement_expression* has the compile-time type `dynamic` and the following rules are applied at run-time using the run-time type of the *primary_expression*.</span></span>

<span data-ttu-id="2e3bd-1178">如果后缀递增或递减运算的操作数是属性或索引器访问，则属性或索引器必须同时具有 `get` 和 `set` 访问器。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1178">If the operand of a postfix increment or decrement operation is a property or indexer access, the property or indexer must have both a `get` and a `set` accessor.</span></span> <span data-ttu-id="2e3bd-1179">如果不是这种情况，则会发生绑定时错误。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1179">If this is not the case, a binding-time error occurs.</span></span>

<span data-ttu-id="2e3bd-1180">一元运算符重载决策 ([一元运算符重载决策](expressions.md#unary-operator-overload-resolution) 应用于选择特定运算符实现) 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1180">Unary operator overload resolution ([Unary operator overload resolution](expressions.md#unary-operator-overload-resolution)) is applied to select a specific operator implementation.</span></span> <span data-ttu-id="2e3bd-1181">预定义 `++` 和 `--` 运算符存在以下类型：、、、、、、、、、、、 `sbyte` `byte` `short` `ushort` `int` `uint` `long` `ulong` `char` `float` `double` `decimal` 和枚举类型。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1181">Predefined `++` and `--` operators exist for the following types: `sbyte`, `byte`, `short`, `ushort`, `int`, `uint`, `long`, `ulong`, `char`, `float`, `double`, `decimal`, and any enum type.</span></span> <span data-ttu-id="2e3bd-1182">预定义 `++` 运算符返回通过向操作数添加1而生成的值，预定义 `--` 运算符返回通过从操作数中减去1而生成的值。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1182">The predefined `++` operators return the value produced by adding 1 to the operand, and the predefined `--` operators return the value produced by subtracting 1 from the operand.</span></span> <span data-ttu-id="2e3bd-1183">在 `checked` 上下文中，如果此加法或减法的结果超出了结果类型的范围，且结果类型为整型类型或枚举类型，则 `System.OverflowException` 会引发。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1183">In a `checked` context, if the result of this addition or subtraction is outside the range of the result type and the result type is an integral type or enum type, a `System.OverflowException` is thrown.</span></span>

<span data-ttu-id="2e3bd-1184">此形式的后缀递增或递减运算的运行时处理， `x++` 或 `x--` 由以下步骤组成：</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1184">The run-time processing of a postfix increment or decrement operation of the form `x++` or `x--` consists of the following steps:</span></span>

*   <span data-ttu-id="2e3bd-1185">如果 `x` 归类为变量：</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1185">If `x` is classified as a variable:</span></span>
    * <span data-ttu-id="2e3bd-1186">`x` 计算以生成变量。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1186">`x` is evaluated to produce the variable.</span></span>
    * <span data-ttu-id="2e3bd-1187">的值已 `x` 保存。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1187">The value of `x` is saved.</span></span>
    * <span data-ttu-id="2e3bd-1188">调用所选运算符时，将的值 `x` 作为其参数。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1188">The selected operator is invoked with the saved value of `x` as its argument.</span></span>
    * <span data-ttu-id="2e3bd-1189">运算符返回的值存储在的计算给定的位置 `x` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1189">The value returned by the operator is stored in the location given by the evaluation of `x`.</span></span>
    * <span data-ttu-id="2e3bd-1190">保存的值 `x` 将成为操作的结果。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1190">The saved value of `x` becomes the result of the operation.</span></span>
*   <span data-ttu-id="2e3bd-1191">如果 `x` 归类为属性或索引器访问：</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1191">If `x` is classified as a property or indexer access:</span></span>
    * <span data-ttu-id="2e3bd-1192">如果) 为，则实例表达式 (如果是，则为 `x` `static` `x` ; 如果是与关联的索引器访问) ，则 (参数列表 `x` ，并且在后续 `get` `set` 调用和访问器调用中使用结果。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1192">The instance expression (if `x` is not `static`) and the argument list (if `x` is an indexer access) associated with `x` are evaluated, and the results are used in the subsequent `get` and `set` accessor invocations.</span></span>
    * <span data-ttu-id="2e3bd-1193">`get`调用的访问器 `x` ，并保存返回值。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1193">The `get` accessor of `x` is invoked and the returned value is saved.</span></span>
    * <span data-ttu-id="2e3bd-1194">调用所选运算符时，将的值 `x` 作为其参数。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1194">The selected operator is invoked with the saved value of `x` as its argument.</span></span>
    * <span data-ttu-id="2e3bd-1195">`set`调用的访问器时，将 `x` 使用由运算符返回的值作为其 `value` 参数。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1195">The `set` accessor of `x` is invoked with the value returned by the operator as its `value` argument.</span></span>
    * <span data-ttu-id="2e3bd-1196">保存的值 `x` 将成为操作的结果。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1196">The saved value of `x` becomes the result of the operation.</span></span>

<span data-ttu-id="2e3bd-1197">`++`和 `--` 运算符还支持前缀符号 ([前缀增量和减量运算符](expressions.md#prefix-increment-and-decrement-operators)) 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1197">The `++` and `--` operators also support prefix notation ([Prefix increment and decrement operators](expressions.md#prefix-increment-and-decrement-operators)).</span></span> <span data-ttu-id="2e3bd-1198">通常，或的结果 `x++` `x--` 是操作之前的值 `x` ，而或的结果 `++x` `--x` 是 `x` 运算后的值。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1198">Typically, the result of `x++` or `x--` is the value of `x` before the operation, whereas the result of `++x` or `--x` is the value of `x` after the operation.</span></span> <span data-ttu-id="2e3bd-1199">在这两种情况下， `x` 其本身在操作后具有相同的值。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1199">In either case, `x` itself has the same value after the operation.</span></span>

<span data-ttu-id="2e3bd-1200">`operator ++` `operator --` 可以使用后缀或前缀表示法来调用或实现。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1200">An `operator ++` or `operator --` implementation can be invoked using either postfix or prefix notation.</span></span> <span data-ttu-id="2e3bd-1201">对于这两个表示法，不能有单独的运算符实现。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1201">It is not possible to have separate operator implementations for the two notations.</span></span>

### <a name="the-new-operator"></a><span data-ttu-id="2e3bd-1202">调用 new 运算符</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1202">The new operator</span></span>

<span data-ttu-id="2e3bd-1203">`new`运算符用于创建类型的新实例。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1203">The `new` operator is used to create new instances of types.</span></span>

<span data-ttu-id="2e3bd-1204">有三种形式的 `new` 表达式：</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1204">There are three forms of `new` expressions:</span></span>

*  <span data-ttu-id="2e3bd-1205">对象创建表达式用于创建类类型和值类型的新实例。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1205">Object creation expressions are used to create new instances of class types and value types.</span></span>
*  <span data-ttu-id="2e3bd-1206">数组创建表达式用于创建数组类型的新实例。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1206">Array creation expressions are used to create new instances of array types.</span></span>
*  <span data-ttu-id="2e3bd-1207">委托创建表达式用于创建委托类型的新实例。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1207">Delegate creation expressions are used to create new instances of delegate types.</span></span>

<span data-ttu-id="2e3bd-1208">`new`运算符表示创建类型的实例，但不一定表示动态分配内存。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1208">The `new` operator implies creation of an instance of a type, but does not necessarily imply dynamic allocation of memory.</span></span> <span data-ttu-id="2e3bd-1209">具体而言，值类型的实例不需要超出它们所在的变量的额外内存，并且当 `new` 用于创建值类型的实例时，不会发生动态分配。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1209">In particular, instances of value types require no additional memory beyond the variables in which they reside, and no dynamic allocations occur when `new` is used to create instances of value types.</span></span>

#### <a name="object-creation-expressions"></a><span data-ttu-id="2e3bd-1210">对象创建表达式</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1210">Object creation expressions</span></span>

<span data-ttu-id="2e3bd-1211">*Object_creation_expression* 用于创建 *class_type* 或 *value_type* 的新实例。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1211">An *object_creation_expression* is used to create a new instance of a *class_type* or a *value_type*.</span></span>

```antlr
object_creation_expression
    : 'new' type '(' argument_list? ')' object_or_collection_initializer?
    | 'new' type object_or_collection_initializer
    ;

object_or_collection_initializer
    : object_initializer
    | collection_initializer
    ;
```

<span data-ttu-id="2e3bd-1212">*Object_creation_expression* 的 *类型* 必须是 *class_type*、 *value_type* 或 *type_parameter*。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1212">The *type* of an *object_creation_expression* must be a *class_type*, a *value_type* or a *type_parameter*.</span></span> <span data-ttu-id="2e3bd-1213">*类型* 不能是 `abstract` *class_type*。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1213">The *type* cannot be an `abstract` *class_type*.</span></span>

<span data-ttu-id="2e3bd-1214">仅当 *类型* 为 *class_type* 或 *struct_type* 时，才允许使用可选 *argument_list* ([参数列出](expressions.md#argument-lists)) 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1214">The optional *argument_list* ([Argument lists](expressions.md#argument-lists)) is permitted only if the *type* is a *class_type* or a *struct_type*.</span></span>

<span data-ttu-id="2e3bd-1215">如果对象创建表达式包括对象初始值设定项或集合初始值设定项，则它可以省略构造函数参数列表和外括号。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1215">An object creation expression can omit the constructor argument list and enclosing parentheses provided it includes an object initializer or collection initializer.</span></span> <span data-ttu-id="2e3bd-1216">省略构造函数参数列表和外括号等效于指定空参数列表。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1216">Omitting the constructor argument list and enclosing parentheses is equivalent to specifying an empty argument list.</span></span>

<span data-ttu-id="2e3bd-1217">处理包括对象初始值设定项或集合初始值设定项的对象创建表达式包含第一次处理实例构造函数，然后将对象初始值设定项指定的成员或元素初始化处理 ([对象初始值设定](expressions.md#object-initializers) 项) 或集合初始值设定项 ([集合](expressions.md#collection-initializers) 初始值设定项) 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1217">Processing of an object creation expression that includes an object initializer or collection initializer consists of first processing the instance constructor and then processing the member or element initializations specified by the object initializer ([Object initializers](expressions.md#object-initializers)) or collection initializer ([Collection initializers](expressions.md#collection-initializers)).</span></span>

<span data-ttu-id="2e3bd-1218">如果可选 *argument_list* 中的任何参数具有编译时类型， `dynamic` 则 *object_creation_expression* 会动态绑定 ([动态绑定](expressions.md#dynamic-binding)) ，并使用具有编译时类型的 *argument_list* 的这些参数的运行时类型在运行时应用以下规则 `dynamic` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1218">If any of the arguments in the optional *argument_list* has the compile-time type `dynamic` then the *object_creation_expression* is dynamically bound ([Dynamic binding](expressions.md#dynamic-binding)) and the following rules are applied at run-time using the run-time type of those arguments of the *argument_list* that have the compile time type `dynamic`.</span></span> <span data-ttu-id="2e3bd-1219">但是，对象创建会经历有限的编译时检查，如对 [动态重载决策进行编译时检查](expressions.md#compile-time-checking-of-dynamic-overload-resolution)中所述。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1219">However, the object creation undergoes a limited compile time check as described in [Compile-time checking of dynamic overload resolution](expressions.md#compile-time-checking-of-dynamic-overload-resolution).</span></span>

<span data-ttu-id="2e3bd-1220">格式 *object_creation_expression* 的绑定时处理 `new T(A)` ，其中 `T` 是 *class_type* 或 *value_type* ，并且 `A` 是可选的 *argument_list*，包括以下步骤：</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1220">The binding-time processing of an *object_creation_expression* of the form `new T(A)`, where `T` is a *class_type* or a *value_type* and `A` is an optional *argument_list*, consists of the following steps:</span></span>

*   <span data-ttu-id="2e3bd-1221">如果 `T` 是 *value_type* 且 `A` 不存在：</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1221">If `T` is a *value_type* and `A` is not present:</span></span>
    * <span data-ttu-id="2e3bd-1222">*Object_creation_expression* 是默认的构造函数调用。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1222">The *object_creation_expression* is a default constructor invocation.</span></span> <span data-ttu-id="2e3bd-1223">*Object_creation_expression* 的结果是类型为的值，它是 `T` `T` 在 [系统 ValueType 类型](types.md#the-systemvaluetype-type)中定义的的默认值。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1223">The result of the *object_creation_expression* is a value of type `T`, namely the default value for `T` as defined in [The System.ValueType type](types.md#the-systemvaluetype-type).</span></span>
*   <span data-ttu-id="2e3bd-1224">否则，如果 `T` 是 *type_parameter* 且 `A` 不存在：</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1224">Otherwise, if `T` is a *type_parameter* and `A` is not present:</span></span>
    * <span data-ttu-id="2e3bd-1225">如果没有为指定) [类型参数约束](classes.md#type-parameter-constraints) (值类型约束或构造函数约束 `T` ，则会发生绑定时错误。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1225">If no value type constraint or constructor constraint ([Type parameter constraints](classes.md#type-parameter-constraints)) has been specified for `T`, a binding-time error occurs.</span></span>
    * <span data-ttu-id="2e3bd-1226">*Object_creation_expression* 的结果是类型参数已绑定到的运行时类型的值，即调用该类型的默认构造函数的结果。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1226">The result of the *object_creation_expression* is a value of the run-time type that the type parameter has been bound to, namely the result of invoking the default constructor of that type.</span></span> <span data-ttu-id="2e3bd-1227">运行时类型可以是引用类型或值类型。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1227">The run-time type may be a reference type or a value type.</span></span>
*   <span data-ttu-id="2e3bd-1228">否则，如果 `T` 是 *class_type* 或 *struct_type*：</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1228">Otherwise, if `T` is a *class_type* or a *struct_type*:</span></span>
    * <span data-ttu-id="2e3bd-1229">如果 `T` 是 `abstract` *class_type*，则会发生编译时错误。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1229">If `T` is an `abstract` *class_type*, a compile-time error occurs.</span></span>
    * <span data-ttu-id="2e3bd-1230">使用 [重载决策](expressions.md#overload-resolution)的重载决策规则确定要调用的实例构造函数。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1230">The instance constructor to invoke is determined using the overload resolution rules of [Overload resolution](expressions.md#overload-resolution).</span></span> <span data-ttu-id="2e3bd-1231">候选实例构造函数集包含声明的所有可访问实例构造函数， `T` 它们适用于 `A` (适用的 [函数成员](expressions.md#applicable-function-member)) 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1231">The set of candidate instance constructors consists of all accessible instance constructors declared in `T` which are applicable with respect to `A` ([Applicable function member](expressions.md#applicable-function-member)).</span></span> <span data-ttu-id="2e3bd-1232">如果候选实例构造函数集为空，或者无法识别单个最佳实例构造函数，则会发生绑定时错误。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1232">If the set of candidate instance constructors is empty, or if a single best instance constructor cannot be identified, a binding-time error occurs.</span></span>
    * <span data-ttu-id="2e3bd-1233">*Object_creation_expression* 的结果为类型的值 `T` ，即通过调用上述步骤中确定的实例构造函数而生成的值。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1233">The result of the *object_creation_expression* is a value of type `T`, namely the value produced by invoking the instance constructor determined in the step above.</span></span>
*  <span data-ttu-id="2e3bd-1234">否则， *object_creation_expression* 无效，并发生绑定时错误。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1234">Otherwise, the *object_creation_expression* is invalid, and a binding-time error occurs.</span></span>

<span data-ttu-id="2e3bd-1235">即使 *object_creation_expression* 是动态绑定的，编译时类型仍为 `T` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1235">Even if the *object_creation_expression* is dynamically bound, the compile-time type is still `T`.</span></span>

<span data-ttu-id="2e3bd-1236">对 *object_creation_expression* 窗体的运行时处理 `new T(A)` ，其中 `T` 是 *class_type* 或 *struct_type* ，并且 `A` 是可选的 *argument_list*，包括以下步骤：</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1236">The run-time processing of an *object_creation_expression* of the form `new T(A)`, where `T` is *class_type* or a *struct_type* and `A` is an optional *argument_list*, consists of the following steps:</span></span>

*   <span data-ttu-id="2e3bd-1237">如果 `T` 是 *class_type*：</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1237">If `T` is a *class_type*:</span></span>
    * <span data-ttu-id="2e3bd-1238">分配类的新实例 `T` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1238">A new instance of class `T` is allocated.</span></span> <span data-ttu-id="2e3bd-1239">如果没有足够的内存可用于分配新的实例， `System.OutOfMemoryException` 则会引发，而不执行进一步的步骤。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1239">If there is not enough memory available to allocate the new instance, a `System.OutOfMemoryException` is thrown and no further steps are executed.</span></span>
    * <span data-ttu-id="2e3bd-1240">新实例的所有字段都初始化为其默认值 ([默认值](variables.md#default-values)) 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1240">All fields of the new instance are initialized to their default values ([Default values](variables.md#default-values)).</span></span>
    * <span data-ttu-id="2e3bd-1241">实例构造函数根据函数成员调用的规则进行调用 (对 [动态重载解析) 的编译时检查](expressions.md#compile-time-checking-of-dynamic-overload-resolution) 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1241">The instance constructor is invoked according to the rules of function member invocation ([Compile-time checking of dynamic overload resolution](expressions.md#compile-time-checking-of-dynamic-overload-resolution)).</span></span> <span data-ttu-id="2e3bd-1242">对新分配的实例的引用会自动传递到实例构造函数，并且可以从该构造函数中将实例作为进行访问 `this` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1242">A reference to the newly allocated instance is automatically passed to the instance constructor and the instance can be accessed from within that constructor as `this`.</span></span>
*   <span data-ttu-id="2e3bd-1243">如果 `T` 是 *struct_type*：</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1243">If `T` is a *struct_type*:</span></span>
    * <span data-ttu-id="2e3bd-1244">`T`通过分配临时本地变量来创建类型的实例。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1244">An instance of type `T` is created by allocating a temporary local variable.</span></span> <span data-ttu-id="2e3bd-1245">由于需要 *struct_type* 的实例构造函数来明确地为要创建的实例的每个字段赋值，因此不需要初始化临时变量。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1245">Since an instance constructor of a *struct_type* is required to definitely assign a value to each field of the instance being created, no initialization of the temporary variable is necessary.</span></span>
    * <span data-ttu-id="2e3bd-1246">实例构造函数根据函数成员调用的规则进行调用 (对 [动态重载解析) 的编译时检查](expressions.md#compile-time-checking-of-dynamic-overload-resolution) 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1246">The instance constructor is invoked according to the rules of function member invocation ([Compile-time checking of dynamic overload resolution](expressions.md#compile-time-checking-of-dynamic-overload-resolution)).</span></span> <span data-ttu-id="2e3bd-1247">对新分配的实例的引用会自动传递到实例构造函数，并且可以从该构造函数中将实例作为进行访问 `this` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1247">A reference to the newly allocated instance is automatically passed to the instance constructor and the instance can be accessed from within that constructor as `this`.</span></span>

#### <a name="object-initializers"></a><span data-ttu-id="2e3bd-1248">对象初始值设定项</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1248">Object initializers</span></span>

<span data-ttu-id="2e3bd-1249">***对象初始值设定项*** 为零个或多个字段、属性或对象的索引元素指定值。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1249">An ***object initializer*** specifies values for zero or more fields, properties or indexed elements of an object.</span></span>

```antlr
object_initializer
    : '{' member_initializer_list? '}'
    | '{' member_initializer_list ',' '}'
    ;

member_initializer_list
    : member_initializer (',' member_initializer)*
    ;

member_initializer
    : initializer_target '=' initializer_value
    ;

initializer_target
    : identifier
    | '[' argument_list ']'
    ;

initializer_value
    : expression
    | object_or_collection_initializer
    ;
```

<span data-ttu-id="2e3bd-1250">对象初始值设定项由成员初始值设定项的序列组成，括在和中，并用 `{` `}` 逗号分隔。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1250">An object initializer consists of a sequence of member initializers, enclosed by `{` and `}` tokens and separated by commas.</span></span> <span data-ttu-id="2e3bd-1251">每个 *member_initializer* 为初始化指定一个目标。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1251">Each *member_initializer* designates a target for the initialization.</span></span> <span data-ttu-id="2e3bd-1252">*标识符* 必须命名要初始化的对象的可访问字段或属性，而用方括号括起来的 *argument_list* 必须在要初始化的对象上指定可访问索引器的参数。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1252">An *identifier* must name an accessible field or property of the object being initialized, whereas an *argument_list* enclosed in square brackets must specify arguments for an accessible indexer on the object being initialized.</span></span> <span data-ttu-id="2e3bd-1253">对象初始值设定项为同一个字段或属性包含多个成员初始值设定项是错误的。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1253">It is an error for an object initializer to include more than one member initializer for the same field or property.</span></span>

<span data-ttu-id="2e3bd-1254">每个 *initializer_target* 后跟一个等号和一个表达式、一个对象初始值设定项或集合初始值设定项。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1254">Each *initializer_target* is followed by an equals sign and either an expression, an object initializer or a collection initializer.</span></span> <span data-ttu-id="2e3bd-1255">对象初始值设定项中的表达式不能引用它要初始化的新创建的对象。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1255">It is not possible for expressions within the object initializer to refer to the newly created object it is initializing.</span></span>

<span data-ttu-id="2e3bd-1256">在处理等号后指定表达式的成员初始值设定项的处理方式与对目标 (简单的 [赋值](expressions.md#simple-assignment)) 相同。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1256">A member initializer that specifies an expression after the equals sign is processed in the same way as an assignment ([Simple assignment](expressions.md#simple-assignment)) to the target.</span></span>

<span data-ttu-id="2e3bd-1257">在等号后指定对象初始值设定项的成员初始值设定项是 ***嵌套的对象初始值设定*** 项，即嵌入对象的初始化。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1257">A member initializer that specifies an object initializer after the equals sign is a ***nested object initializer***, i.e. an initialization of an embedded object.</span></span> <span data-ttu-id="2e3bd-1258">嵌套的对象初始值设定项中的赋值被视为对该字段或属性的成员的赋值，而不是为字段或属性赋值。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1258">Instead of assigning a new value to the field or property, the assignments in the nested object initializer are treated as assignments to members of the field or property.</span></span> <span data-ttu-id="2e3bd-1259">嵌套的对象初始值设定项不能应用于值类型的属性，也不能应用于具有值类型的只读字段。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1259">Nested object initializers cannot be applied to properties with a value type, or to read-only fields with a value type.</span></span>

<span data-ttu-id="2e3bd-1260">在等号后指定集合初始值设定项的成员初始值设定项是嵌入集合的初始化。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1260">A member initializer that specifies a collection initializer after the equals sign is an initialization of an embedded collection.</span></span> <span data-ttu-id="2e3bd-1261">将初始值设定项中给定的元素添加到目标所引用的集合，而不是将新集合分配给目标字段、属性或索引器。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1261">Instead of assigning a new collection to the target field, property or indexer, the elements given in the initializer are added to the collection referenced by the target.</span></span> <span data-ttu-id="2e3bd-1262">目标必须为满足 [集合初始值设定项](expressions.md#collection-initializers)中指定的要求的集合类型。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1262">The target must be of a collection type that satisfies the requirements specified in [Collection initializers](expressions.md#collection-initializers).</span></span>

<span data-ttu-id="2e3bd-1263">索引初始值设定项的参数将始终只计算一次。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1263">The arguments to an index initializer will always be evaluated exactly once.</span></span> <span data-ttu-id="2e3bd-1264">因此，即使参数最终不会使用 (例如，因为) 的嵌套初始值设定项为空，它们会对其副作用进行评估。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1264">Thus, even if the arguments end up never getting used (e.g. because of an empty nested initializer), they will be evaluated for their side effects.</span></span>

<span data-ttu-id="2e3bd-1265">下面的类表示一个具有两个坐标的点：</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1265">The following class represents a point with two coordinates:</span></span>
```csharp
public class Point
{
    int x, y;

    public int X { get { return x; } set { x = value; } }
    public int Y { get { return y; } set { y = value; } }
}
```

<span data-ttu-id="2e3bd-1266">`Point`可按如下所示创建和初始化实例：</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1266">An instance of `Point` can be created and initialized as follows:</span></span>
```csharp
Point a = new Point { X = 0, Y = 1 };
```
<span data-ttu-id="2e3bd-1267">这与</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1267">which has the same effect as</span></span>
```csharp
Point __a = new Point();
__a.X = 0;
__a.Y = 1; 
Point a = __a;
```
<span data-ttu-id="2e3bd-1268">其中 `__a` ，是其他不可见且无法访问的临时变量。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1268">where `__a` is an otherwise invisible and inaccessible temporary variable.</span></span> <span data-ttu-id="2e3bd-1269">下面的类表示从两个点创建的矩形：</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1269">The following class represents a rectangle created from two points:</span></span>
```csharp
public class Rectangle
{
    Point p1, p2;

    public Point P1 { get { return p1; } set { p1 = value; } }
    public Point P2 { get { return p2; } set { p2 = value; } }
}
```

<span data-ttu-id="2e3bd-1270">`Rectangle`可按如下所示创建和初始化实例：</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1270">An instance of `Rectangle` can be created and initialized as follows:</span></span>
```csharp
Rectangle r = new Rectangle {
    P1 = new Point { X = 0, Y = 1 },
    P2 = new Point { X = 2, Y = 3 }
};
```
<span data-ttu-id="2e3bd-1271">这与</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1271">which has the same effect as</span></span>
```csharp
Rectangle __r = new Rectangle();
Point __p1 = new Point();
__p1.X = 0;
__p1.Y = 1;
__r.P1 = __p1;
Point __p2 = new Point();
__p2.X = 2;
__p2.Y = 3;
__r.P2 = __p2; 
Rectangle r = __r;
```
<span data-ttu-id="2e3bd-1272">其中 `__r` ， `__p1` 和 `__p2` 是在其他情况下不可见且不可访问的临时变量。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1272">where `__r`, `__p1` and `__p2` are temporary variables that are otherwise invisible and inaccessible.</span></span>

<span data-ttu-id="2e3bd-1273">If `Rectangle` 的构造函数分配两个嵌入的 `Point` 实例</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1273">If `Rectangle`'s constructor allocates the two embedded `Point` instances</span></span>
```csharp
public class Rectangle
{
    Point p1 = new Point();
    Point p2 = new Point();

    public Point P1 { get { return p1; } }
    public Point P2 { get { return p2; } }
}
```
<span data-ttu-id="2e3bd-1274">以下构造可用于初始化嵌入 `Point` 实例，而不是分配新实例：</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1274">the following construct can be used to initialize the embedded `Point` instances instead of assigning new instances:</span></span>
```csharp
Rectangle r = new Rectangle {
    P1 = { X = 0, Y = 1 },
    P2 = { X = 2, Y = 3 }
};
```
<span data-ttu-id="2e3bd-1275">这与</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1275">which has the same effect as</span></span>
```csharp
Rectangle __r = new Rectangle();
__r.P1.X = 0;
__r.P1.Y = 1;
__r.P2.X = 2;
__r.P2.Y = 3;
Rectangle r = __r;
```

<span data-ttu-id="2e3bd-1276">给定 C 的适当定义，以下示例：</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1276">Given an appropriate definition of C, the following example:</span></span>
```csharp
var c = new C {
    x = true,
    y = { a = "Hello" },
    z = { 1, 2, 3 },
    ["x"] = 5,
    [0,0] = { "a", "b" },
    [1,2] = {}
};
```
<span data-ttu-id="2e3bd-1277">等效于这一系列的赋值：</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1277">is equivalent to this series of assignments:</span></span>
```csharp
C __c = new C();
__c.x = true;
__c.y.a = "Hello";
__c.z.Add(1); 
__c.z.Add(2);
__c.z.Add(3);
string __i1 = "x";
__c[__i1] = 5;
int __i2 = 0, __i3 = 0;
__c[__i2,__i3].Add("a");
__c[__i2,__i3].Add("b");
int __i4 = 1, __i5 = 2;
var c = __c;
```
<span data-ttu-id="2e3bd-1278">where `__c` ，等等，生成的变量不可见且无法访问源代码。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1278">where `__c`, etc., are generated variables that are invisible and inaccessible to the source code.</span></span> <span data-ttu-id="2e3bd-1279">请注意，的参数 `[0,0]` 只计算一次，并且的参数只 `[1,2]` 计算一次，即使从未使用过它们也是如此。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1279">Note that the arguments for `[0,0]` are evaluated only once, and the arguments for `[1,2]` are evaluated once even though they are never used.</span></span>

#### <a name="collection-initializers"></a><span data-ttu-id="2e3bd-1280">集合初始值设定项</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1280">Collection initializers</span></span>

<span data-ttu-id="2e3bd-1281">集合初始值设定项指定集合的元素。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1281">A collection initializer specifies the elements of a collection.</span></span>

```antlr
collection_initializer
    : '{' element_initializer_list '}'
    | '{' element_initializer_list ',' '}'
    ;

element_initializer_list
    : element_initializer (',' element_initializer)*
    ;

element_initializer
    : non_assignment_expression
    | '{' expression_list '}'
    ;

expression_list
    : expression (',' expression)*
    ;
```

<span data-ttu-id="2e3bd-1282">集合初始值设定项由元素初始值设定项序列组成，括在和中，并用 `{` `}` 逗号分隔。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1282">A collection initializer consists of a sequence of element initializers, enclosed by `{` and `}` tokens and separated by commas.</span></span> <span data-ttu-id="2e3bd-1283">每个元素初始值设定项指定一个元素，该元素将被添加到要初始化的集合对象中，并由括在 `{` 和 `}` 标记中并以逗号分隔的表达式列表组成。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1283">Each element initializer specifies an element to be added to the collection object being initialized, and consists of a list of expressions enclosed by `{` and `}` tokens and separated by commas.</span></span>  <span data-ttu-id="2e3bd-1284">单表达式元素初始值设定项可以不带大括号编写，但是不能是赋值表达式，以避免成员初始值设定项出现歧义。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1284">A single-expression element initializer can be written without braces, but cannot then be an assignment expression, to avoid ambiguity with member initializers.</span></span> <span data-ttu-id="2e3bd-1285">*Non_assignment_expression* 生产是在 [expression](expressions.md#expression)中定义的。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1285">The *non_assignment_expression* production is defined in [Expression](expressions.md#expression).</span></span>

<span data-ttu-id="2e3bd-1286">下面是包含集合初始值设定项的对象创建表达式的示例：</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1286">The following is an example of an object creation expression that includes a collection initializer:</span></span>
```csharp
List<int> digits = new List<int> { 0, 1, 2, 3, 4, 5, 6, 7, 8, 9 };
```

<span data-ttu-id="2e3bd-1287">集合初始值设定项应用于的集合对象必须是实现 `System.Collections.IEnumerable` 或发生编译时错误的类型。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1287">The collection object to which a collection initializer is applied must be of a type that implements `System.Collections.IEnumerable` or a compile-time error occurs.</span></span> <span data-ttu-id="2e3bd-1288">对于按顺序排列的每个指定元素，集合初始值设定项 `Add` 使用元素初始值设定项的表达式列表作为参数列表调用目标对象上的方法，并对每个调用应用常规成员查找和重载解析。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1288">For each specified element in order, the collection initializer invokes an `Add` method on the target object with the expression list of the element initializer as argument list, applying normal member lookup and overload resolution for each invocation.</span></span> <span data-ttu-id="2e3bd-1289">因此，集合对象必须具有适用于 `Add` 每个元素初始值设定项的名称的适用实例或扩展方法。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1289">Thus, the collection object must have an applicable instance or extension method with the name `Add` for each element initializer.</span></span>

<span data-ttu-id="2e3bd-1290">下面的类表示一个联系人，其中包含名称和电话号码的列表：</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1290">The following class represents a contact with a name and a list of phone numbers:</span></span>
```csharp
public class Contact
{
    string name;
    List<string> phoneNumbers = new List<string>();

    public string Name { get { return name; } set { name = value; } }

    public List<string> PhoneNumbers { get { return phoneNumbers; } }
}
```

<span data-ttu-id="2e3bd-1291">`List<Contact>`可以按如下所示创建和初始化：</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1291">A `List<Contact>` can be created and initialized as follows:</span></span>
```csharp
var contacts = new List<Contact> {
    new Contact {
        Name = "Chris Smith",
        PhoneNumbers = { "206-555-0101", "425-882-8080" }
    },
    new Contact {
        Name = "Bob Harris",
        PhoneNumbers = { "650-555-0199" }
    }
};
```
<span data-ttu-id="2e3bd-1292">这与</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1292">which has the same effect as</span></span>
```csharp
var __clist = new List<Contact>();
Contact __c1 = new Contact();
__c1.Name = "Chris Smith";
__c1.PhoneNumbers.Add("206-555-0101");
__c1.PhoneNumbers.Add("425-882-8080");
__clist.Add(__c1);
Contact __c2 = new Contact();
__c2.Name = "Bob Harris";
__c2.PhoneNumbers.Add("650-555-0199");
__clist.Add(__c2);
var contacts = __clist;
```
<span data-ttu-id="2e3bd-1293">其中 `__clist` ， `__c1` 和 `__c2` 是在其他情况下不可见且不可访问的临时变量。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1293">where `__clist`, `__c1` and `__c2` are temporary variables that are otherwise invisible and inaccessible.</span></span>

#### <a name="array-creation-expressions"></a><span data-ttu-id="2e3bd-1294">数组创建表达式</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1294">Array creation expressions</span></span>

<span data-ttu-id="2e3bd-1295">*Array_creation_expression* 用于创建 *array_type* 的新实例。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1295">An *array_creation_expression* is used to create a new instance of an *array_type*.</span></span>

```antlr
array_creation_expression
    : 'new' non_array_type '[' expression_list ']' rank_specifier* array_initializer?
    | 'new' array_type array_initializer
    | 'new' rank_specifier array_initializer
    ;
```

<span data-ttu-id="2e3bd-1296">第一种形式的数组创建表达式将分配从表达式列表中删除每个表达式所得到的类型的数组实例。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1296">An array creation expression of the first form allocates an array instance of the type that results from deleting each of the individual expressions from the expression list.</span></span> <span data-ttu-id="2e3bd-1297">例如，数组创建表达式 `new int[10,20]` 生成类型为的数组实例 `int[,]` ，数组创建表达式将 `new int[10][,]` 生成类型为的数组 `int[][,]` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1297">For example, the array creation expression `new int[10,20]` produces an array instance of type `int[,]`, and the array creation expression `new int[10][,]` produces an array of type `int[][,]`.</span></span> <span data-ttu-id="2e3bd-1298">表达式列表中的每个表达式的类型必须为 `int` 、 `uint` 、 `long` 或 `ulong` ，或者可隐式转换为这些类型中的一个或多个。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1298">Each expression in the expression list must be of type `int`, `uint`, `long`, or `ulong`, or implicitly convertible to one or more of these types.</span></span> <span data-ttu-id="2e3bd-1299">每个表达式的值决定了新分配的数组实例中相应维度的长度。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1299">The value of each expression determines the length of the corresponding dimension in the newly allocated array instance.</span></span> <span data-ttu-id="2e3bd-1300">由于数组维度的长度必须为非负，因此在表达式列表中有一个具有负值的 *constant_expression* 编译时错误。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1300">Since the length of an array dimension must be nonnegative, it is a compile-time error to have a *constant_expression* with a negative value in the expression list.</span></span>

<span data-ttu-id="2e3bd-1301">除非在不安全的上下文中 ([不安全](unsafe-code.md#unsafe-contexts) 上下文) ，否则不指定数组的布局。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1301">Except in an unsafe context ([Unsafe contexts](unsafe-code.md#unsafe-contexts)), the layout of arrays is unspecified.</span></span>

<span data-ttu-id="2e3bd-1302">如果第一种形式的数组创建表达式包含数组初始值设定项，则表达式列表中的每个表达式都必须是常量，并且表达式列表指定的秩和维度长度必须与数组初始值设定项的匹配项和维度长度匹配。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1302">If an array creation expression of the first form includes an array initializer, each expression in the expression list must be a constant and the rank and dimension lengths specified by the expression list must match those of the array initializer.</span></span>

<span data-ttu-id="2e3bd-1303">在第二个或第三个窗体的数组创建表达式中，指定数组类型或秩说明符的秩必须与数组初始值设定项的秩匹配。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1303">In an array creation expression of the second or third form, the rank of the specified array type or rank specifier must match that of the array initializer.</span></span> <span data-ttu-id="2e3bd-1304">各个维度长度是从数组初始值设定项的每个相应嵌套级别中的元素数推断出来的。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1304">The individual dimension lengths are inferred from the number of elements in each of the corresponding nesting levels of the array initializer.</span></span> <span data-ttu-id="2e3bd-1305">因此，表达式</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1305">Thus, the expression</span></span>
```csharp
new int[,] {{0, 1}, {2, 3}, {4, 5}}
```
<span data-ttu-id="2e3bd-1306">完全对应于</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1306">exactly corresponds to</span></span>
```csharp
new int[3, 2] {{0, 1}, {2, 3}, {4, 5}}
```

<span data-ttu-id="2e3bd-1307">第三种形式的数组创建表达式称为 \***隐式类型化数组创建表达式** _。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1307">An array creation expression of the third form is referred to as an \***implicitly typed array creation expression** _.</span></span> <span data-ttu-id="2e3bd-1308">它与第二种形式类似，不同之处在于数组的元素类型不是显式给定的，而是作为最佳通用类型 (查找数组初始值设定项中一组表达式) 的 [最佳通用类型](expressions.md#finding-the-best-common-type-of-a-set-of-expressions) 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1308">It is similar to the second form, except that the element type of the array is not explicitly given, but determined as the best common type ([Finding the best common type of a set of expressions](expressions.md#finding-the-best-common-type-of-a-set-of-expressions)) of the set of expressions in the array initializer.</span></span> <span data-ttu-id="2e3bd-1309">对于多维数组（即，其中 _rank_specifier \* 至少包含一个逗号），此集包含在嵌套 *array_initializer* 中找到的所有 *表达式*。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1309">For a multidimensional array, i.e., one where the _rank_specifier\* contains at least one comma, this set comprises all *expression* s found in nested *array_initializer* s.</span></span>

<span data-ttu-id="2e3bd-1310">数组 [初始值设定项中进一步](arrays.md#array-initializers)介绍了数组初始值设定项。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1310">Array initializers are described further in [Array initializers](arrays.md#array-initializers).</span></span>

<span data-ttu-id="2e3bd-1311">计算数组创建表达式的结果是一个值，即对新分配的数组实例的引用。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1311">The result of evaluating an array creation expression is classified as a value, namely a reference to the newly allocated array instance.</span></span> <span data-ttu-id="2e3bd-1312">数组创建表达式的运行时处理包括以下步骤：</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1312">The run-time processing of an array creation expression consists of the following steps:</span></span>

*  <span data-ttu-id="2e3bd-1313">将按从左到右的顺序计算 *expression_list* 的维度长度表达式。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1313">The dimension length expressions of the *expression_list* are evaluated in order, from left to right.</span></span> <span data-ttu-id="2e3bd-1314">对每个表达式进行以下计算后，将执行隐式转换 (将 [隐式](conversions.md#implicit-conversions) 转换) 为以下类型之一： `int` 、 `uint` 、 `long` 和 `ulong` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1314">Following evaluation of each expression, an implicit conversion ([Implicit conversions](conversions.md#implicit-conversions)) to one of the following types is performed: `int`, `uint`, `long`, `ulong`.</span></span> <span data-ttu-id="2e3bd-1315">在此列表中选择了隐式转换的第一种类型。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1315">The first type in this list for which an implicit conversion exists is chosen.</span></span> <span data-ttu-id="2e3bd-1316">如果表达式的计算或后续的隐式转换导致异常，则不会计算进一步的表达式，也不会执行任何其他步骤。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1316">If evaluation of an expression or the subsequent implicit conversion causes an exception, then no further expressions are evaluated and no further steps are executed.</span></span>
*  <span data-ttu-id="2e3bd-1317">按如下所示验证维度长度的计算值。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1317">The computed values for the dimension lengths are validated as follows.</span></span> <span data-ttu-id="2e3bd-1318">如果一个或多个值小于零，则将引发， `System.OverflowException` 而不执行进一步的步骤。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1318">If one or more of the values are less than zero, a `System.OverflowException` is thrown and no further steps are executed.</span></span>
*  <span data-ttu-id="2e3bd-1319">分配具有给定维度长度的数组实例。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1319">An array instance with the given dimension lengths is allocated.</span></span> <span data-ttu-id="2e3bd-1320">如果没有足够的内存可用于分配新的实例， `System.OutOfMemoryException` 则会引发，而不执行进一步的步骤。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1320">If there is not enough memory available to allocate the new instance, a `System.OutOfMemoryException` is thrown and no further steps are executed.</span></span>
*  <span data-ttu-id="2e3bd-1321">新数组实例的所有元素都初始化为其默认值 ([默认值](variables.md#default-values)) 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1321">All elements of the new array instance are initialized to their default values ([Default values](variables.md#default-values)).</span></span>
*  <span data-ttu-id="2e3bd-1322">如果数组创建表达式包含数组初始值设定项，则计算数组初始值设定项中的每个表达式并将其分配给其相应的数组元素。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1322">If the array creation expression contains an array initializer, then each expression in the array initializer is evaluated and assigned to its corresponding array element.</span></span> <span data-ttu-id="2e3bd-1323">计算和分配按表达式在数组初始值设定项中写入的顺序执行，换言之，元素按递增的索引顺序进行初始化，最右边的维度首先增长。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1323">The evaluations and assignments are performed in the order the expressions are written in the array initializer—in other words, elements are initialized in increasing index order, with the rightmost dimension increasing first.</span></span> <span data-ttu-id="2e3bd-1324">如果给定表达式的计算或对相应数组元素的后续赋值导致了异常，则 (不会初始化更多的元素，因此剩余元素将) 其默认值。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1324">If evaluation of a given expression or the subsequent assignment to the corresponding array element causes an exception, then no further elements are initialized (and the remaining elements will thus have their default values).</span></span>

<span data-ttu-id="2e3bd-1325">数组创建表达式允许实例化包含数组类型的元素的数组，但此类数组的元素必须手动初始化。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1325">An array creation expression permits instantiation of an array with elements of an array type, but the elements of such an array must be manually initialized.</span></span> <span data-ttu-id="2e3bd-1326">例如，语句</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1326">For example, the statement</span></span>
```csharp
int[][] a = new int[100][];
```
<span data-ttu-id="2e3bd-1327">创建具有100类型的元素的一维数组 `int[]` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1327">creates a single-dimensional array with 100 elements of type `int[]`.</span></span> <span data-ttu-id="2e3bd-1328">每个元素的初始值均为 `null` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1328">The initial value of each element is `null`.</span></span> <span data-ttu-id="2e3bd-1329">同一个数组创建表达式不可能同时实例化子数组和语句</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1329">It is not possible for the same array creation expression to also instantiate the sub-arrays, and the statement</span></span>
```csharp
int[][] a = new int[100][5];        // Error
```
<span data-ttu-id="2e3bd-1330">导致编译时错误。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1330">results in a compile-time error.</span></span> <span data-ttu-id="2e3bd-1331">子数组的实例化必须改为手动执行，如下所示</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1331">Instantiation of the sub-arrays must instead be performed manually, as in</span></span>
```csharp
int[][] a = new int[100][];
for (int i = 0; i < 100; i++) a[i] = new int[5];
```

<span data-ttu-id="2e3bd-1332">当数组的数组具有 "矩形" 形状时，即当子数组的长度完全相同时，使用多维数组更为有效。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1332">When an array of arrays has a "rectangular" shape, that is when the sub-arrays are all of the same length, it is more efficient to use a multi-dimensional array.</span></span> <span data-ttu-id="2e3bd-1333">在上面的示例中，数组数组的实例化将创建101对象，即一个外部数组和100子数组。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1333">In the example above, instantiation of the array of arrays creates 101 objects—one outer array and 100 sub-arrays.</span></span> <span data-ttu-id="2e3bd-1334">相反，</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1334">In contrast,</span></span>
```csharp
int[,] = new int[100, 5];
```
<span data-ttu-id="2e3bd-1335">仅创建单个对象和二维数组，并在单个语句中完成分配。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1335">creates only a single object, a two-dimensional array, and accomplishes the allocation in a single statement.</span></span>

<span data-ttu-id="2e3bd-1336">下面是隐式类型的数组创建表达式的示例：</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1336">The following are examples of implicitly typed array creation expressions:</span></span>
```csharp
var a = new[] { 1, 10, 100, 1000 };                       // int[]

var b = new[] { 1, 1.5, 2, 2.5 };                         // double[]

var c = new[,] { { "hello", null }, { "world", "!" } };   // string[,]

var d = new[] { 1, "one", 2, "two" };                     // Error
```

<span data-ttu-id="2e3bd-1337">最后一个表达式会导致编译时错误，因为和都不能 `int` `string` 隐式转换为另一个表达式，因此没有最常见的类型。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1337">The last expression causes a compile-time error because neither `int` nor `string` is implicitly convertible to the other, and so there is no best common type.</span></span> <span data-ttu-id="2e3bd-1338">在这种情况下，必须使用显式类型化的数组创建表达式，例如，将类型指定为 `object[]` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1338">An explicitly typed array creation expression must be used in this case, for example specifying the type to be `object[]`.</span></span> <span data-ttu-id="2e3bd-1339">或者，可以将其中一个元素强制转换为公共基类型，后者随后将成为推断元素类型。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1339">Alternatively, one of the elements can be cast to a common base type, which would then become the inferred element type.</span></span>

<span data-ttu-id="2e3bd-1340">隐式类型的数组创建表达式可与匿名对象初始值设定项组合 ([匿名对象创建表达式](expressions.md#anonymous-object-creation-expressions)) 创建匿名类型的数据结构。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1340">Implicitly typed array creation expressions can be combined with anonymous object initializers ([Anonymous object creation expressions](expressions.md#anonymous-object-creation-expressions)) to create anonymously typed data structures.</span></span> <span data-ttu-id="2e3bd-1341">例如：</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1341">For example:</span></span>
```csharp
var contacts = new[] {
    new {
        Name = "Chris Smith",
        PhoneNumbers = new[] { "206-555-0101", "425-882-8080" }
    },
    new {
        Name = "Bob Harris",
        PhoneNumbers = new[] { "650-555-0199" }
    }
};
```

#### <a name="delegate-creation-expressions"></a><span data-ttu-id="2e3bd-1342">委托创建表达式</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1342">Delegate creation expressions</span></span>

<span data-ttu-id="2e3bd-1343">*Delegate_creation_expression* 用于创建 *delegate_type* 的新实例。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1343">A *delegate_creation_expression* is used to create a new instance of a *delegate_type*.</span></span>

```antlr
delegate_creation_expression
    : 'new' delegate_type '(' expression ')'
    ;
```

<span data-ttu-id="2e3bd-1344">委托创建表达式的参数必须是方法组、匿名函数或编译时类型 `dynamic` 或 *delegate_type* 的值。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1344">The argument of a delegate creation expression must be a method group, an anonymous function or a value of either the compile time type `dynamic` or a *delegate_type*.</span></span> <span data-ttu-id="2e3bd-1345">如果参数为方法组，则它将标识方法，并为实例方法标识要为其创建委托的对象。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1345">If the argument is a method group, it identifies the method and, for an instance method, the object for which to create a delegate.</span></span> <span data-ttu-id="2e3bd-1346">如果参数是匿名函数，它将直接定义委托目标的参数和方法主体。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1346">If the argument is an anonymous function it directly defines the parameters and method body of the delegate target.</span></span> <span data-ttu-id="2e3bd-1347">如果参数是一个值，则它标识要创建副本的委托实例。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1347">If the argument is a value it identifies a delegate instance of which to create a copy.</span></span>

<span data-ttu-id="2e3bd-1348">如果 *表达式* 具有编译时类型 `dynamic` ，则 *delegate_creation_expression* 会动态绑定 ([动态绑定](expressions.md#dynamic-binding)) ，并使用 *表达式* 的运行时类型在运行时应用下面的规则。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1348">If the *expression* has the compile-time type `dynamic`, the *delegate_creation_expression* is dynamically bound ([Dynamic binding](expressions.md#dynamic-binding)), and the rules below are applied at run-time using the run-time type of the *expression*.</span></span> <span data-ttu-id="2e3bd-1349">否则，规则将在编译时应用。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1349">Otherwise the rules are applied at compile-time.</span></span>

<span data-ttu-id="2e3bd-1350">格式 *delegate_creation_expression* 的绑定时处理 `new D(E)` ，其中 `D` 是一个 *delegate_type* 并且 `E` 是一个 *表达式*，它包含以下步骤：</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1350">The binding-time processing of a *delegate_creation_expression* of the form `new D(E)`, where `D` is a *delegate_type* and `E` is an *expression*, consists of the following steps:</span></span>

*  <span data-ttu-id="2e3bd-1351">如果 `E` 是方法组，则以与方法组转换 ([方法](conversions.md#method-group-conversions) 组转换相同的方式处理委托创建表达式，) 从转换 `E` 为 `D` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1351">If `E` is a method group, the delegate creation expression is processed in the same way as a method group conversion ([Method group conversions](conversions.md#method-group-conversions)) from `E` to `D`.</span></span>
*  <span data-ttu-id="2e3bd-1352">如果 `E` 是匿名函数，则用与匿名函数转换相同的方式处理委托创建表达式，) 从到 ([匿名函数](conversions.md#anonymous-function-conversions) 转换 `E` `D` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1352">If `E` is an anonymous function, the delegate creation expression is processed in the same way as an anonymous function conversion ([Anonymous function conversions](conversions.md#anonymous-function-conversions)) from `E` to `D`.</span></span>
*  <span data-ttu-id="2e3bd-1353">如果 `E` 是值，则 `E` 必须与)  ([委托声明](delegates.md#delegate-declarations) 兼容 `D` ，并且结果是对类型为的新创建的委托的引用，该委托 `D` 引用与相同的调用列表 `E` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1353">If `E` is a value, `E` must be compatible ([Delegate declarations](delegates.md#delegate-declarations)) with `D`, and the result is a reference to a newly created delegate of type `D` that refers to the same invocation list as `E`.</span></span> <span data-ttu-id="2e3bd-1354">如果与 `E` 不兼容 `D` ，则会发生编译时错误。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1354">If `E` is not compatible with `D`, a compile-time error occurs.</span></span>

<span data-ttu-id="2e3bd-1355">窗体的 *delegate_creation_expression* 的运行时处理 `new D(E)` ，其中 `D` 是一个 *delegate_type* 并且 `E` 是一个 *表达式*，它包含以下步骤：</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1355">The run-time processing of a *delegate_creation_expression* of the form `new D(E)`, where `D` is a *delegate_type* and `E` is an *expression*, consists of the following steps:</span></span>

*   <span data-ttu-id="2e3bd-1356">如果 `E` 是方法组，则将委托创建表达式作为方法组转换进行计算， ([方法组](conversions.md#method-group-conversions) 将) 从转换 `E` 为 `D` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1356">If `E` is a method group, the delegate creation expression is evaluated as a method group conversion ([Method group conversions](conversions.md#method-group-conversions)) from `E` to `D`.</span></span>
*   <span data-ttu-id="2e3bd-1357">如果 `E` 是匿名函数，则会将委托创建作为从 `E` 到 `D` ([匿名函数](conversions.md#anonymous-function-conversions) 转换的匿名函数转换进行计算) 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1357">If `E` is an anonymous function, the delegate creation is evaluated as an anonymous function conversion from `E` to `D` ([Anonymous function conversions](conversions.md#anonymous-function-conversions)).</span></span>
*   <span data-ttu-id="2e3bd-1358">如果 `E` 是 *delegate_type* 的值：</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1358">If `E` is a value of a *delegate_type*:</span></span>
    * <span data-ttu-id="2e3bd-1359">`E` 进行计算。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1359">`E` is evaluated.</span></span> <span data-ttu-id="2e3bd-1360">如果此计算导致异常，则不执行其他步骤。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1360">If this evaluation causes an exception, no further steps are executed.</span></span>
    * <span data-ttu-id="2e3bd-1361">如果的值 `E` 为 `null` ，则 `System.NullReferenceException` 会引发，而不执行进一步的步骤。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1361">If the value of `E` is `null`, a `System.NullReferenceException` is thrown and no further steps are executed.</span></span>
    * <span data-ttu-id="2e3bd-1362">分配委托类型的新实例 `D` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1362">A new instance of the delegate type `D` is allocated.</span></span> <span data-ttu-id="2e3bd-1363">如果没有足够的内存可用于分配新的实例， `System.OutOfMemoryException` 则会引发，而不执行进一步的步骤。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1363">If there is not enough memory available to allocate the new instance, a `System.OutOfMemoryException` is thrown and no further steps are executed.</span></span>
    * <span data-ttu-id="2e3bd-1364">使用与给定的委托实例相同的调用列表来初始化新的委托实例 `E` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1364">The new delegate instance is initialized with the same invocation list as the delegate instance given by `E`.</span></span>

<span data-ttu-id="2e3bd-1365">委托的调用列表是在对委托进行实例化时确定的，然后在该委托的整个生存期内保持不变。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1365">The invocation list of a delegate is determined when the delegate is instantiated and then remains constant for the entire lifetime of the delegate.</span></span> <span data-ttu-id="2e3bd-1366">换句话说，创建委托后，不能更改其目标可调用的实体。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1366">In other words, it is not possible to change the target callable entities of a delegate once it has been created.</span></span> <span data-ttu-id="2e3bd-1367">合并两个委托或从另一个 ([委托) 声明](delegates.md#delegate-declarations) 中移除一个委托时，将返回一个新的委托结果;现有委托的内容未发生更改。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1367">When two delegates are combined or one is removed from another ([Delegate declarations](delegates.md#delegate-declarations)), a new delegate results; no existing delegate has its contents changed.</span></span>

<span data-ttu-id="2e3bd-1368">不能创建引用属性、索引器、用户定义的运算符、实例构造函数、析构函数或静态构造函数的委托。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1368">It is not possible to create a delegate that refers to a property, indexer, user-defined operator, instance constructor, destructor, or static constructor.</span></span>

<span data-ttu-id="2e3bd-1369">如上所述，从方法组创建委托时，委托的形参列表和返回类型确定要选择的重载方法。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1369">As described above, when a delegate is created from a method group, the formal parameter list and return type of the delegate determine which of the overloaded methods to select.</span></span> <span data-ttu-id="2e3bd-1370">示例中</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1370">In the example</span></span>
```csharp
delegate double DoubleFunc(double x);

class A
{
    DoubleFunc f = new DoubleFunc(Square);

    static float Square(float x) {
        return x * x;
    }

    static double Square(double x) {
        return x * x;
    }
}
```
<span data-ttu-id="2e3bd-1371">`A.f`使用引用第二种方法的委托来初始化字段 `Square` ，因为该方法与的形参列表和返回类型完全匹配 `DoubleFunc` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1371">the `A.f` field is initialized with a delegate that refers to the second `Square` method because that method exactly matches the formal parameter list and return type of `DoubleFunc`.</span></span> <span data-ttu-id="2e3bd-1372">如果第二 `Square` 种方法不存在，则会发生编译时错误。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1372">Had the second `Square` method not been present, a compile-time error would have occurred.</span></span>

#### <a name="anonymous-object-creation-expressions"></a><span data-ttu-id="2e3bd-1373">匿名对象创建表达式</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1373">Anonymous object creation expressions</span></span>

<span data-ttu-id="2e3bd-1374">*Anonymous_object_creation_expression* 用于创建匿名类型的对象。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1374">An *anonymous_object_creation_expression* is used to create an object of an anonymous type.</span></span>

```antlr
anonymous_object_creation_expression
    : 'new' anonymous_object_initializer
    ;

anonymous_object_initializer
    : '{' member_declarator_list? '}'
    | '{' member_declarator_list ',' '}'
    ;

member_declarator_list
    : member_declarator (',' member_declarator)*
    ;

member_declarator
    : simple_name
    | member_access
    | base_access
    | null_conditional_member_access
    | identifier '=' expression
    ;
```

<span data-ttu-id="2e3bd-1375">匿名对象初始值设定项声明匿名类型并返回该类型的实例。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1375">An anonymous object initializer declares an anonymous type and returns an instance of that type.</span></span> <span data-ttu-id="2e3bd-1376">匿名类型是直接从继承的无类类类型 `object` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1376">An anonymous type is a nameless class type that inherits directly from `object`.</span></span> <span data-ttu-id="2e3bd-1377">匿名类型的成员是从用于创建该类型实例的匿名对象初始值设定项中推断出的只读属性的序列。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1377">The members of an anonymous type are a sequence of read-only properties inferred from the anonymous object initializer used to create an instance of the type.</span></span> <span data-ttu-id="2e3bd-1378">具体而言，是窗体的匿名对象初始值设定项</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1378">Specifically, an anonymous object initializer of the form</span></span>
```csharp
new { p1 = e1, p2 = e2, ..., pn = en }
```
<span data-ttu-id="2e3bd-1379">声明窗体的匿名类型</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1379">declares an anonymous type of the form</span></span>
```csharp
class __Anonymous1
{
    private readonly T1 f1;
    private readonly T2 f2;
    ...
    private readonly Tn fn;

    public __Anonymous1(T1 a1, T2 a2, ..., Tn an) {
        f1 = a1;
        f2 = a2;
        ...
        fn = an;
    }

    public T1 p1 { get { return f1; } }
    public T2 p2 { get { return f2; } }
    ...
    public Tn pn { get { return fn; } }

    public override bool Equals(object __o) { ... }
    public override int GetHashCode() { ... }
}
```
<span data-ttu-id="2e3bd-1380">其中每个 `Tx` 是对应的表达式的类型 `ex` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1380">where each `Tx` is the type of the corresponding expression `ex`.</span></span> <span data-ttu-id="2e3bd-1381">*Member_declarator* 中使用的表达式的类型必须为。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1381">The expression used in a *member_declarator* must have a type.</span></span> <span data-ttu-id="2e3bd-1382">因此， *member_declarator* 中的表达式为 null 或匿名函数是编译时错误。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1382">Thus, it is a compile-time error for an expression in a *member_declarator* to be null or an anonymous function.</span></span> <span data-ttu-id="2e3bd-1383">这也是表达式具有不安全类型的编译时错误。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1383">It is also a compile-time error for the expression to have an unsafe type.</span></span>

<span data-ttu-id="2e3bd-1384">匿名类型和其方法的参数的名称 `Equals` 是由编译器自动生成的，不能在程序文本中引用。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1384">The names of an anonymous type and of the parameter to its `Equals` method are automatically generated by the compiler and cannot be referenced in program text.</span></span>

<span data-ttu-id="2e3bd-1385">在同一程序中，两个用相同顺序指定相同名称和编译时类型的属性序列的匿名对象初始值设定项将生成相同的匿名类型的实例。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1385">Within the same program, two anonymous object initializers that specify a sequence of properties of the same names and compile-time types in the same order will produce instances of the same anonymous type.</span></span>

<span data-ttu-id="2e3bd-1386">示例中</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1386">In the example</span></span>
```csharp
var p1 = new { Name = "Lawnmower", Price = 495.00 };
var p2 = new { Name = "Shovel", Price = 26.95 };
p1 = p2;
```
<span data-ttu-id="2e3bd-1387">允许在最后一行上进行赋值，因为 `p1` 和 `p2` 具有相同的匿名类型。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1387">the assignment on the last line is permitted because `p1` and `p2` are of the same anonymous type.</span></span>

<span data-ttu-id="2e3bd-1388">`Equals` `GetHashcode` 匿名类型上的和方法会重写从继承的方法 `object` ，并根据属性的和进行定义 `Equals` `GetHashcode` ，因此，当且仅当所有属性都相等时，相同匿名类型的两个实例才相等。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1388">The `Equals` and `GetHashcode` methods on anonymous types override the methods inherited from `object`, and are defined in terms of the `Equals` and `GetHashcode` of the properties, so that two instances of the same anonymous type are equal if and only if all their properties are equal.</span></span>

<span data-ttu-id="2e3bd-1389">成员声明符可以缩写为简单名称 ([类型推理](expressions.md#type-inference)) ，成员访问 ([动态重载决策的编译时检查](expressions.md#compile-time-checking-of-dynamic-overload-resolution)) ，基本访问 ([基本](expressions.md#base-access) 访问) 或 null 条件成员访问 (为) 的 [投影初始值设定项](expressions.md#null-conditional-expressions-as-projection-initializers) 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1389">A member declarator can be abbreviated to a simple name ([Type inference](expressions.md#type-inference)), a member access ([Compile-time checking of dynamic overload resolution](expressions.md#compile-time-checking-of-dynamic-overload-resolution)), a base access ([Base access](expressions.md#base-access)) or a null-conditional member access ([Null-conditional expressions as projection initializers](expressions.md#null-conditional-expressions-as-projection-initializers)).</span></span> <span data-ttu-id="2e3bd-1390">这称为 ***投影初始值设定项*** ，是的声明和对具有相同名称的属性赋值的简写形式。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1390">This is called a ***projection initializer*** and is shorthand for a declaration of and assignment to a property with the same name.</span></span> <span data-ttu-id="2e3bd-1391">具体而言，是窗体的成员声明符</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1391">Specifically, member declarators of the forms</span></span>
```csharp
identifier
expr.identifier
```
<span data-ttu-id="2e3bd-1392">与以下各项完全等效：</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1392">are precisely equivalent to the following, respectively:</span></span>
```csharp
identifier = identifier
identifier = expr.identifier
```

<span data-ttu-id="2e3bd-1393">因此，在投影初始值设定项中， *标识符* 既选择值，又选择要向其分配值的字段或属性。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1393">Thus, in a projection initializer the *identifier* selects both the value and the field or property to which the value is assigned.</span></span> <span data-ttu-id="2e3bd-1394">直观而言，投影初始值设定项不只是一个值，而是值的名称。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1394">Intuitively, a projection initializer projects not just a value, but also the name of the value.</span></span>

### <a name="the-typeof-operator"></a><span data-ttu-id="2e3bd-1395">Typeof 运算符</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1395">The typeof operator</span></span>

<span data-ttu-id="2e3bd-1396">`typeof`运算符用于获取 `System.Type` 类型的对象。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1396">The `typeof` operator is used to obtain the `System.Type` object for a type.</span></span>

```antlr
typeof_expression
    : 'typeof' '(' type ')'
    | 'typeof' '(' unbound_type_name ')'
    | 'typeof' '(' 'void' ')'
    ;

unbound_type_name
    : identifier generic_dimension_specifier?
    | identifier '::' identifier generic_dimension_specifier?
    | unbound_type_name '.' identifier generic_dimension_specifier?
    ;

generic_dimension_specifier
    : '<' comma* '>'
    ;

comma
    : ','
    ;
```

<span data-ttu-id="2e3bd-1397">第一种形式的 *typeof_expression* 由 `typeof` 关键字后跟带括号的 *类型* 组成。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1397">The first form of *typeof_expression* consists of a `typeof` keyword followed by a parenthesized *type*.</span></span> <span data-ttu-id="2e3bd-1398">此窗体的表达式的结果是所 `System.Type` 指示类型的对象。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1398">The result of an expression of this form is the `System.Type` object for the indicated type.</span></span> <span data-ttu-id="2e3bd-1399">`System.Type`任何给定类型都只有一个对象。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1399">There is only one `System.Type` object for any given type.</span></span> <span data-ttu-id="2e3bd-1400">这意味着对于类型  `T` ，始终为 `typeof(T) == typeof(T)` true。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1400">This means that for a type `T`, `typeof(T) == typeof(T)` is always true.</span></span> <span data-ttu-id="2e3bd-1401">*类型* 不能为 `dynamic` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1401">The *type* cannot be `dynamic`.</span></span>

<span data-ttu-id="2e3bd-1402">第二种形式的 *typeof_expression* 由 `typeof` 关键字后跟带括号的 *unbound_type_name* 组成。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1402">The second form of *typeof_expression* consists of a `typeof` keyword followed by a parenthesized *unbound_type_name*.</span></span> <span data-ttu-id="2e3bd-1403">*Unbound_type_name* 与 *Type_name* ([命名空间和类型) 名称](basic-concepts.md#namespace-and-type-names)相同，只不过 *unbound_type_name* 包含 generic_dimension_specifier *包含* *type_name 的 type_argument_list*。 </span><span class="sxs-lookup"><span data-stu-id="2e3bd-1403">An *unbound_type_name* is very similar to a *type_name* ([Namespace and type names](basic-concepts.md#namespace-and-type-names)) except that an *unbound_type_name* contains *generic_dimension_specifier* s where a *type_name* contains *type_argument_list* s.</span></span> <span data-ttu-id="2e3bd-1404">当 *typeof_expression* 的操作数为满足 *unbound_type_name* 和 *type_name* 语法的标记序列时，即当它既不包含 *generic_dimension_specifier* 也不包含 *type_argument_list* 时，将标记序列视为 *type_name*。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1404">When the operand of a *typeof_expression* is a sequence of tokens that satisfies the grammars of both *unbound_type_name* and *type_name*, namely when it contains neither a *generic_dimension_specifier* nor a *type_argument_list*, the sequence of tokens is considered to be a *type_name*.</span></span> <span data-ttu-id="2e3bd-1405">确定 *unbound_type_name* 的含义，如下所示：</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1405">The meaning of an *unbound_type_name* is determined as follows:</span></span>

*  <span data-ttu-id="2e3bd-1406">通过将每个 *generic_dimension_specifier* 替换为与每个 type_argument 具有相同数量的逗号和关键字的 *type_argument_list* ，将标记序列转换为 type_name `object` 。 </span><span class="sxs-lookup"><span data-stu-id="2e3bd-1406">Convert the sequence of tokens to a *type_name* by replacing each *generic_dimension_specifier* with a *type_argument_list* having the same number of commas and the keyword `object` as each *type_argument*.</span></span>
*  <span data-ttu-id="2e3bd-1407">计算生成的 *type_name*，同时忽略所有类型参数约束。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1407">Evaluate the resulting *type_name*, while ignoring all type parameter constraints.</span></span>
*  <span data-ttu-id="2e3bd-1408">*Unbound_type_name* 解析为与生成的构造 [类型 (关联的未绑定的](types.md#bound-and-unbound-types)泛型类型) 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1408">The *unbound_type_name* resolves to the unbound generic type associated with the resulting constructed type ([Bound and unbound types](types.md#bound-and-unbound-types)).</span></span>

<span data-ttu-id="2e3bd-1409">*Typeof_expression* 的结果是 `System.Type` 生成的未绑定泛型类型的对象。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1409">The result of the *typeof_expression* is the `System.Type` object for the resulting unbound generic type.</span></span>

<span data-ttu-id="2e3bd-1410">第三种形式的 *typeof_expression* 由 `typeof` 关键字后跟带括号的 `void` 关键字构成。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1410">The third form of *typeof_expression* consists of a `typeof` keyword followed by a parenthesized `void` keyword.</span></span> <span data-ttu-id="2e3bd-1411">此形式的表达式的结果是 `System.Type` 表示缺少类型的对象。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1411">The result of an expression of this form is the `System.Type` object that represents the absence of a type.</span></span> <span data-ttu-id="2e3bd-1412">返回的类型对象 `typeof(void)` 不同于为任何类型返回的类型对象。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1412">The type object returned by `typeof(void)` is distinct from the type object returned for any type.</span></span> <span data-ttu-id="2e3bd-1413">此特殊类型对象在允许反射到语言中的方法的类库中很有用，在这种情况下，这些方法希望有一种方法来表示任何方法（包括 void 方法）的返回类型以及实例 `System.Type` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1413">This special type object is useful in class libraries that allow reflection onto methods in the language, where those methods wish to have a way to represent the return type of any method, including void methods, with an instance of `System.Type`.</span></span>

<span data-ttu-id="2e3bd-1414">`typeof`运算符可在类型参数上使用。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1414">The `typeof` operator can be used on a type parameter.</span></span> <span data-ttu-id="2e3bd-1415">结果是 `System.Type` 绑定到类型参数的运行时类型的对象。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1415">The result is the `System.Type` object for the run-time type that was bound to the type parameter.</span></span> <span data-ttu-id="2e3bd-1416">`typeof`运算符还可用于构造类型或未绑定的泛型类型 ([绑定和未绑定的类型](types.md#bound-and-unbound-types)) 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1416">The `typeof` operator can also be used on a constructed type or an unbound generic type ([Bound and unbound types](types.md#bound-and-unbound-types)).</span></span> <span data-ttu-id="2e3bd-1417">`System.Type`未绑定的泛型类型的对象与实例类型的对象不同 `System.Type` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1417">The `System.Type` object for an unbound generic type is not the same as the `System.Type` object of the instance type.</span></span> <span data-ttu-id="2e3bd-1418">实例类型在运行时始终是封闭式构造类型，因此它的 `System.Type` 对象依赖于正在使用中的运行时类型参数，而未绑定的泛型类型没有类型参数。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1418">The instance type is always a closed constructed type at run-time so its `System.Type` object depends on the run-time type arguments in use, while the unbound generic type has no type arguments.</span></span>

<span data-ttu-id="2e3bd-1419">示例</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1419">The example</span></span>
```csharp
using System;

class X<T>
{
    public static void PrintTypes() {
        Type[] t = {
            typeof(int),
            typeof(System.Int32),
            typeof(string),
            typeof(double[]),
            typeof(void),
            typeof(T),
            typeof(X<T>),
            typeof(X<X<T>>),
            typeof(X<>)
        };
        for (int i = 0; i < t.Length; i++) {
            Console.WriteLine(t[i]);
        }
    }
}

class Test
{
    static void Main() {
        X<int>.PrintTypes();
    }
}
```
<span data-ttu-id="2e3bd-1420">生成以下输出：</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1420">produces the following output:</span></span>
```console
System.Int32
System.Int32
System.String
System.Double[]
System.Void
System.Int32
X`1[System.Int32]
X`1[X`1[System.Int32]]
X`1[T]
```

<span data-ttu-id="2e3bd-1421">请注意， `int` 和 `System.Int32` 是相同的类型。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1421">Note that `int` and `System.Int32` are the same type.</span></span>

<span data-ttu-id="2e3bd-1422">另请注意，的结果 `typeof(X<>)` 不取决于类型参数，但的结果是 `typeof(X<T>)` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1422">Also note that the result of `typeof(X<>)` does not depend on the type argument but the result of `typeof(X<T>)` does.</span></span>

### <a name="the-checked-and-unchecked-operators"></a><span data-ttu-id="2e3bd-1423">checked 和 unchecked 运算符</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1423">The checked and unchecked operators</span></span>

<span data-ttu-id="2e3bd-1424">`checked`和 `unchecked` 运算符用于控制整型类型算术运算和转换的 ***溢出检查上下文***。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1424">The `checked` and `unchecked` operators are used to control the ***overflow checking context*** for integral-type arithmetic operations and conversions.</span></span>

```antlr
checked_expression
    : 'checked' '(' expression ')'
    ;

unchecked_expression
    : 'unchecked' '(' expression ')'
    ;
```

<span data-ttu-id="2e3bd-1425">`checked`运算符在已检查的上下文中计算包含的表达式， `unchecked` 运算符在未检查的上下文中计算包含的表达式。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1425">The `checked` operator evaluates the contained expression in a checked context, and the `unchecked` operator evaluates the contained expression in an unchecked context.</span></span> <span data-ttu-id="2e3bd-1426">*Checked_expression* 或 *unchecked_expression* 完全对应于 *parenthesized_expression* ([带圆括号的表达式](expressions.md#parenthesized-expressions)) ，只是在给定溢出检查上下文中计算包含的表达式。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1426">A *checked_expression* or *unchecked_expression* corresponds exactly to a *parenthesized_expression* ([Parenthesized expressions](expressions.md#parenthesized-expressions)), except that the contained expression is evaluated in the given overflow checking context.</span></span>

<span data-ttu-id="2e3bd-1427">还可以通过 `checked` `unchecked`) [检查和未](statements.md#the-checked-and-unchecked-statements) 检查的语句 (的和语句来控制溢出检查上下文。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1427">The overflow checking context can also be controlled through the `checked` and `unchecked` statements ([The checked and unchecked statements](statements.md#the-checked-and-unchecked-statements)).</span></span>

<span data-ttu-id="2e3bd-1428">`checked`和运算符和语句所建立的溢出检查上下文会影响以下操作 `unchecked` ：</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1428">The following operations are affected by the overflow checking context established by the `checked` and `unchecked` operators and statements:</span></span>

*  <span data-ttu-id="2e3bd-1429">`++` `--` 当操作数为整数类型时，预定义的和一元运算符 ([后缀递增和递减运算符](expressions.md#postfix-increment-and-decrement-operators)以及[前缀增量和减量运算符](expressions.md#prefix-increment-and-decrement-operators)) 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1429">The predefined `++` and `--` unary operators ([Postfix increment and decrement operators](expressions.md#postfix-increment-and-decrement-operators) and [Prefix increment and decrement operators](expressions.md#prefix-increment-and-decrement-operators)), when the operand is of an integral type.</span></span>
*  <span data-ttu-id="2e3bd-1430">`-`当操作数为整数类型时，预定义的一元运算符 ([一元负运算符](expressions.md#unary-minus-operator)) 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1430">The predefined `-` unary operator ([Unary minus operator](expressions.md#unary-minus-operator)), when the operand is of an integral type.</span></span>
*  <span data-ttu-id="2e3bd-1431">`+` `-` `*` 当两个操作数均为整型时，预定义的、、和 `/` 二元运算符)  ([算术运算符](expressions.md#arithmetic-operators)。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1431">The predefined `+`, `-`, `*`, and `/` binary operators ([Arithmetic operators](expressions.md#arithmetic-operators)), when both operands are of integral types.</span></span>
*  <span data-ttu-id="2e3bd-1432">显式数值转换 ([](conversions.md#explicit-numeric-conversions)从一个整型类型到另一个整型类型或从 `float` 或 `double` 到整型的显式数字转换) 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1432">Explicit numeric conversions ([Explicit numeric conversions](conversions.md#explicit-numeric-conversions)) from one integral type to another integral type, or from `float` or `double` to an integral type.</span></span>

<span data-ttu-id="2e3bd-1433">当上述操作之一产生的结果太大而无法在目标类型中表示时，执行该操作的上下文控制产生的行为：</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1433">When one of the above operations produce a result that is too large to represent in the destination type, the context in which the operation is performed controls the resulting behavior:</span></span>

*  <span data-ttu-id="2e3bd-1434">在 `checked` 上下文中，如果操作是常量表达式)  ([常数](expressions.md#constant-expressions) 表达式，则发生编译时错误。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1434">In a `checked` context, if the operation is a constant expression ([Constant expressions](expressions.md#constant-expressions)), a compile-time error occurs.</span></span> <span data-ttu-id="2e3bd-1435">否则，在运行时执行操作时， `System.OverflowException` 将引发。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1435">Otherwise, when the operation is performed at run-time, a `System.OverflowException` is thrown.</span></span>
*  <span data-ttu-id="2e3bd-1436">在 `unchecked` 上下文中，将放弃不适合目标类型的任何高序位，从而截断结果。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1436">In an `unchecked` context, the result is truncated by discarding any high-order bits that do not fit in the destination type.</span></span>

<span data-ttu-id="2e3bd-1437">对于非常量表达式 (在运行时计算的表达式（不是由任何 `checked` 或运算符或语句括起来的) `unchecked` ）， `unchecked` 除非外部因素 (如编译器开关和执行环境配置) 调用来进行 `checked` 求值。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1437">For non-constant expressions (expressions that are evaluated at run-time) that are not enclosed by any `checked` or `unchecked` operators or statements, the default overflow checking context is `unchecked` unless external factors (such as compiler switches and execution environment configuration) call for `checked` evaluation.</span></span>

<span data-ttu-id="2e3bd-1438">对于可以在编译时完全计算)  (表达式的常量表达式，默认溢出检查上下文始终为 `checked` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1438">For constant expressions (expressions that can be fully evaluated at compile-time), the default overflow checking context is always `checked`.</span></span> <span data-ttu-id="2e3bd-1439">除非常量表达式显式放置在上下文中，否则在 `unchecked` 表达式的编译时计算过程中发生的溢出将始终导致编译时错误。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1439">Unless a constant expression is explicitly placed in an `unchecked` context, overflows that occur during the compile-time evaluation of the expression always cause compile-time errors.</span></span>

<span data-ttu-id="2e3bd-1440">匿名函数的主体不受 `checked` 或在 `unchecked` 其中发生匿名函数的上下文影响。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1440">The body of an anonymous function is not affected by `checked` or `unchecked` contexts in which the anonymous function occurs.</span></span>

<span data-ttu-id="2e3bd-1441">示例中</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1441">In the example</span></span>
```csharp
class Test
{
    static readonly int x = 1000000;
    static readonly int y = 1000000;

    static int F() {
        return checked(x * y);      // Throws OverflowException
    }

    static int G() {
        return unchecked(x * y);    // Returns -727379968
    }

    static int H() {
        return x * y;               // Depends on default
    }
}
```
<span data-ttu-id="2e3bd-1442">不会报告编译时错误，因为在编译时无法计算两个表达式。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1442">no compile-time errors are reported since neither of the expressions can be evaluated at compile-time.</span></span> <span data-ttu-id="2e3bd-1443">在运行时，方法会 `F` 引发 `System.OverflowException` ， `G` 方法返回-727379968 (超出范围的结果) 的低32位。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1443">At run-time, the `F` method throws a `System.OverflowException`, and the `G` method returns -727379968 (the lower 32 bits of the out-of-range result).</span></span> <span data-ttu-id="2e3bd-1444">方法的行为 `H` 取决于编译的默认溢出检查上下文，但与相同 `F` 或相同 `G` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1444">The behavior of the `H` method depends on the default overflow checking context for the compilation, but it is either the same as `F` or the same as `G`.</span></span>

<span data-ttu-id="2e3bd-1445">示例中</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1445">In the example</span></span>
```csharp
class Test
{
    const int x = 1000000;
    const int y = 1000000;

    static int F() {
        return checked(x * y);      // Compile error, overflow
    }

    static int G() {
        return unchecked(x * y);    // Returns -727379968
    }

    static int H() {
        return x * y;               // Compile error, overflow
    }
}
```
<span data-ttu-id="2e3bd-1446">在中计算常数表达式时发生的溢出， `F` `H` 导致报告编译时错误，因为在上下文中计算表达式 `checked` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1446">the overflows that occur when evaluating the constant expressions in `F` and `H` cause compile-time errors to be reported because the expressions are evaluated in a `checked` context.</span></span> <span data-ttu-id="2e3bd-1447">当计算中的常数表达式时，也会发生溢出， `G` 但由于计算发生在 `unchecked` 上下文中，因此不会报告溢出。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1447">An overflow also occurs when evaluating the constant expression in `G`, but since the evaluation takes place in an `unchecked` context, the overflow is not reported.</span></span>

<span data-ttu-id="2e3bd-1448">`checked`和 `unchecked` 运算符仅对那些在 " `(` " 和 "" 标记中包含的按 `)`</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1448">The `checked` and `unchecked` operators only affect the overflow checking context for those operations that are textually contained within the "`(`" and "`)`" tokens.</span></span> <span data-ttu-id="2e3bd-1449">运算符对在计算包含的表达式时被调用的函数成员不起作用。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1449">The operators have no effect on function members that are invoked as a result of evaluating the contained expression.</span></span> <span data-ttu-id="2e3bd-1450">示例中</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1450">In the example</span></span>
```csharp
class Test
{
    static int Multiply(int x, int y) {
        return x * y;
    }

    static int F() {
        return checked(Multiply(1000000, 1000000));
    }
}
```
<span data-ttu-id="2e3bd-1451">`checked`在中使用不 `F` 会影响中的计算 `x * y` `Multiply` ，因此 `x * y` 在默认溢出检查上下文中计算。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1451">the use of `checked` in `F` does not affect the evaluation of `x * y` in `Multiply`, so `x * y` is evaluated in the default overflow checking context.</span></span>

<span data-ttu-id="2e3bd-1452">`unchecked`用十六进制表示法编写带符号整数类型的常量时，运算符非常方便。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1452">The `unchecked` operator is convenient when writing constants of the signed integral types in hexadecimal notation.</span></span> <span data-ttu-id="2e3bd-1453">例如：</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1453">For example:</span></span>
```csharp
class Test
{
    public const int AllBits = unchecked((int)0xFFFFFFFF);

    public const int HighBit = unchecked((int)0x80000000);
}
```

<span data-ttu-id="2e3bd-1454">上述两个十六进制常量均为类型 `uint` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1454">Both of the hexadecimal constants above are of type `uint`.</span></span> <span data-ttu-id="2e3bd-1455">由于常量不在 `int` 此范围内，因此， `unchecked` 转换为 `int` 将产生编译时错误。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1455">Because the constants are outside the `int` range, without the `unchecked` operator, the casts to `int` would produce compile-time errors.</span></span>

<span data-ttu-id="2e3bd-1456">`checked`和 `unchecked` 运算符和语句使程序员能够控制某些数值计算的某些方面。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1456">The `checked` and `unchecked` operators and statements allow programmers to control certain aspects of some numeric calculations.</span></span> <span data-ttu-id="2e3bd-1457">但是，某些数值运算符的行为取决于其操作数的数据类型。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1457">However, the behavior of some numeric operators depends on their operands' data types.</span></span> <span data-ttu-id="2e3bd-1458">例如，两个小数相乘始终导致溢出异常，即使在显式构造中也是如此 `unchecked` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1458">For example, multiplying two decimals always results in an exception on overflow even within an explicitly `unchecked` construct.</span></span> <span data-ttu-id="2e3bd-1459">同样，将两个浮点数相乘会导致溢出异常，即使在显式构造中也是如此 `checked` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1459">Similarly, multiplying two floats never results in an exception on overflow even within an explicitly `checked` construct.</span></span> <span data-ttu-id="2e3bd-1460">此外，其他运算符永远不会受检查模式（无论是默认的还是显式的）的影响。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1460">In addition, other operators are never affected by the mode of checking, whether default or explicit.</span></span>

### <a name="default-value-expressions"></a><span data-ttu-id="2e3bd-1461">默认值表达式</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1461">Default value expressions</span></span>

<span data-ttu-id="2e3bd-1462">默认值表达式用于获取类型)  ([默认值](variables.md#default-values) 的默认值。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1462">A default value expression is used to obtain the default value ([Default values](variables.md#default-values)) of a type.</span></span> <span data-ttu-id="2e3bd-1463">通常，默认值表达式用于类型参数，因为如果类型参数是值类型或引用类型，则它可能不是已知的。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1463">Typically a default value expression is used for type parameters, since it may not be known if the type parameter is a value type or a reference type.</span></span> <span data-ttu-id="2e3bd-1464"> (不存在从 `null` 文本到类型参数的转换，除非已知类型参数是引用类型。 ) </span><span class="sxs-lookup"><span data-stu-id="2e3bd-1464">(No conversion exists from the `null` literal to a type parameter unless the type parameter is known to be a reference type.)</span></span>

```antlr
default_value_expression
    : 'default' '(' type ')'
    ;
```

<span data-ttu-id="2e3bd-1465">如果 *default_value_expression* 中的 *类型* 在运行时计算为引用类型，则结果将 `null` 转换为该类型。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1465">If the *type* in a *default_value_expression* evaluates at run-time to a reference type, the result is `null` converted to that type.</span></span> <span data-ttu-id="2e3bd-1466">如果 *default_value_expression* 中的 *类型* 在运行时计算到值类型，则结果为 *Value_type* 的默认值 ([默认构造函数](types.md#default-constructors)) 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1466">If the *type* in a *default_value_expression* evaluates at run-time to a value type, the result is the *value_type*'s default value ([Default constructors](types.md#default-constructors)).</span></span>

<span data-ttu-id="2e3bd-1467">如果类型是引用类型或类型参数（已知为 ([类型参数) 约束](classes.md#type-parameter-constraints)的引用类型），则 *default_value_expression* 是常量表达式 ([常数](expressions.md#constant-expressions)表达式) 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1467">A *default_value_expression* is a constant expression ([Constant expressions](expressions.md#constant-expressions)) if the type is a reference type or a type parameter that is known to be a reference type ([Type parameter constraints](classes.md#type-parameter-constraints)).</span></span> <span data-ttu-id="2e3bd-1468">此外，如果类型是以下值类型之一，则 *default_value_expression* 是常量表达式：、、、、、、、、、、、、 `sbyte` `byte` `short` `ushort` `int` `uint` `long` `ulong` `char` `float` `double` `decimal` `bool` 或任何枚举类型。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1468">In addition, a *default_value_expression* is a constant expression if the type is one of the following value types: `sbyte`, `byte`, `short`, `ushort`, `int`, `uint`, `long`, `ulong`, `char`, `float`, `double`, `decimal`, `bool`, or any enumeration type.</span></span>


### <a name="nameof-expressions"></a><span data-ttu-id="2e3bd-1469">Nameof 表达式</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1469">Nameof expressions</span></span>

<span data-ttu-id="2e3bd-1470">*Nameof_expression* 用于获取作为常量字符串的程序实体的名称。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1470">A *nameof_expression* is used to obtain the name of a program entity as a constant string.</span></span>

```antlr
nameof_expression
    : 'nameof' '(' named_entity ')'
    ;

named_entity
    : simple_name
    | named_entity_target '.' identifier type_argument_list?
    ;

named_entity_target
    : 'this'
    | 'base'
    | named_entity 
    | predefined_type 
    | qualified_alias_member
    ;
```

<span data-ttu-id="2e3bd-1471">语法上说， *named_entity* 操作数始终是表达式。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1471">Grammatically speaking, the *named_entity* operand is always an expression.</span></span> <span data-ttu-id="2e3bd-1472">由于不是 `nameof` 保留关键字，因此，nameof 表达式在语法上始终不明确，因为调用了简单名称 `nameof` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1472">Because `nameof` is not a reserved keyword, a nameof expression is always syntactically ambiguous with an invocation of the simple name `nameof`.</span></span> <span data-ttu-id="2e3bd-1473">出于兼容性原因，如果名称查找 (名称的 [简单名称](expressions.md#simple-names)) `nameof` 成功，则无论调用是否合法，都将表达式视为 *invocation_expression* --。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1473">For compatibility reasons, if a name lookup ([Simple names](expressions.md#simple-names)) of the name `nameof` succeeds, the expression is treated as an *invocation_expression* -- regardless of whether the invocation is legal.</span></span> <span data-ttu-id="2e3bd-1474">否则为 *nameof_expression*。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1474">Otherwise it is a *nameof_expression*.</span></span>

<span data-ttu-id="2e3bd-1475">*Nameof_expression* 的 *named_entity* 的含义是表达式的含义;即，作为 *simple_name*、 *base_access* 或 *member_access*。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1475">The meaning of the *named_entity* of a *nameof_expression* is the meaning of it as an expression; that is, either as a *simple_name*, a *base_access* or a *member_access*.</span></span> <span data-ttu-id="2e3bd-1476">但是，在 [简单名称](expressions.md#simple-names) 和 [成员访问](expressions.md#member-access) 中描述的查找会导致错误，因为实例成员是在静态上下文中找到的，所以 *nameof_expression* 不产生这样的错误。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1476">However, where the lookup described in [Simple names](expressions.md#simple-names) and [Member access](expressions.md#member-access) results in an error because an instance member was found in a static context, a *nameof_expression* produces no such error.</span></span>

<span data-ttu-id="2e3bd-1477">如果 *named_entity* 指定方法组具有 *type_argument_list*，则会发生编译时错误。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1477">It is a compile-time error for a *named_entity* designating a method group to have a *type_argument_list*.</span></span> <span data-ttu-id="2e3bd-1478">如果 *named_entity_target* 具有类型，则会发生编译时错误 `dynamic` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1478">It is a compile time error for a *named_entity_target* to have the type `dynamic`.</span></span>

<span data-ttu-id="2e3bd-1479">*Nameof_expression* 是类型的常量表达式 `string` ，在运行时不起作用。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1479">A *nameof_expression* is a constant expression of type `string`, and has no effect at runtime.</span></span> <span data-ttu-id="2e3bd-1480">具体而言，它的 *named_entity* 不会进行评估，因此，在明确的赋值分析中将忽略它 () [简单表达式的常规规则](variables.md#general-rules-for-simple-expressions) 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1480">Specifically, its *named_entity* is not evaluated, and is ignored for the purposes of definite assignment analysis ([General rules for simple expressions](variables.md#general-rules-for-simple-expressions)).</span></span> <span data-ttu-id="2e3bd-1481">它的值是可选的最终 *type_argument_list* 之前 *named_entity* 的最后一个标识符，转换方式如下：</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1481">Its value is the last identifier of the *named_entity* before the optional final *type_argument_list*, transformed in the following way:</span></span>

* <span data-ttu-id="2e3bd-1482">删除前缀 " `@` " （如果使用）。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1482">The prefix "`@`", if used, is removed.</span></span>
* <span data-ttu-id="2e3bd-1483">每个 *unicode_escape_sequence* 都转换为其对应的 unicode 字符。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1483">Each *unicode_escape_sequence* is transformed into its corresponding Unicode character.</span></span>
* <span data-ttu-id="2e3bd-1484">删除任何 *formatting_characters* 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1484">Any *formatting_characters* are removed.</span></span>

<span data-ttu-id="2e3bd-1485">当在标识符之间测试相等性时， [标识符](lexical-structure.md#identifiers) 中会应用相同的转换。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1485">These are the same transformations applied in [Identifiers](lexical-structure.md#identifiers) when testing equality between identifiers.</span></span>

<span data-ttu-id="2e3bd-1486">TODO：示例</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1486">TODO: examples</span></span>

### <a name="anonymous-method-expressions"></a><span data-ttu-id="2e3bd-1487">匿名方法表达式</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1487">Anonymous method expressions</span></span>

<span data-ttu-id="2e3bd-1488">*Anonymous_method_expression* 是定义匿名函数的两种方法之一。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1488">An *anonymous_method_expression* is one of two ways of defining an anonymous function.</span></span> <span data-ttu-id="2e3bd-1489">[匿名函数表达式](expressions.md#anonymous-function-expressions)中进一步介绍了这些功能。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1489">These are further described in [Anonymous function expressions](expressions.md#anonymous-function-expressions).</span></span>

## <a name="unary-operators"></a><span data-ttu-id="2e3bd-1490">一元运算符</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1490">Unary operators</span></span>

<span data-ttu-id="2e3bd-1491">`?`、、 `+` `-` 、 `!` 、 `~` 、 `++` 、 `--` 、强制转换和 `await` 运算符称为一元运算符。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1491">The `?`, `+`, `-`, `!`, `~`, `++`, `--`, cast, and `await` operators are called the unary operators.</span></span>

```antlr
unary_expression
    : primary_expression
    | null_conditional_expression
    | '+' unary_expression
    | '-' unary_expression
    | '!' unary_expression
    | '~' unary_expression
    | pre_increment_expression
    | pre_decrement_expression
    | cast_expression
    | await_expression
    | unary_expression_unsafe
    ;
```

<span data-ttu-id="2e3bd-1492">如果 *unary_expression* 的操作数具有编译时类型 `dynamic` ，则它 ([动态](expressions.md#dynamic-binding) 绑定) 动态绑定。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1492">If the operand of a *unary_expression* has the compile-time type `dynamic`, it is dynamically bound ([Dynamic binding](expressions.md#dynamic-binding)).</span></span> <span data-ttu-id="2e3bd-1493">在这种情况下， *unary_expression* 的编译时类型为 `dynamic` ，并将使用操作数的运行时类型在运行时进行以下描述的解决方法。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1493">In this case the compile-time type of the *unary_expression* is `dynamic`, and the resolution described below will take place at run-time using the run-time type of the operand.</span></span>

### <a name="null-conditional-operator"></a><span data-ttu-id="2e3bd-1494">Null 条件运算符</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1494">Null-conditional operator</span></span>

<span data-ttu-id="2e3bd-1495">仅当操作数为非 null 时，null 条件运算符才将操作列表应用于其操作数。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1495">The null-conditional operator applies a list of operations to its operand only if that operand is non-null.</span></span> <span data-ttu-id="2e3bd-1496">否则，应用运算符的结果为 `null` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1496">Otherwise the result of applying the operator is `null`.</span></span>

```antlr
null_conditional_expression
    : primary_expression null_conditional_operations
    ;

null_conditional_operations
    : null_conditional_operations? '?' '.' identifier type_argument_list?
    | null_conditional_operations? '?' '[' argument_list ']'
    | null_conditional_operations '.' identifier type_argument_list?
    | null_conditional_operations '[' argument_list ']'
    | null_conditional_operations '(' argument_list? ')'
    ;
```

<span data-ttu-id="2e3bd-1497">操作列表可以包括成员访问和元素访问操作 (这些操作本身可能为 null 条件) ，以及调用。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1497">The list of operations can include member access and element access operations (which may themselves be null-conditional), as well as invocation.</span></span>

<span data-ttu-id="2e3bd-1498">例如，表达式 `a.b?[0]?.c()` 是具有 *primary_expression* 的 *null_conditional_expression* ， `a.b` *null_conditional_operations* `?[0]` (null 条件元素访问) ， (`?.c` null 条件成员访问) 和 `()` (调用) 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1498">For example, the expression `a.b?[0]?.c()` is a *null_conditional_expression* with a *primary_expression* `a.b` and *null_conditional_operations* `?[0]` (null-conditional element access), `?.c` (null-conditional member access) and `()` (invocation).</span></span>

<span data-ttu-id="2e3bd-1499">对于 `E` 包含 primary_expression 的 null_conditional_expression  `P` ，让我们以从上到的 `E0` `?` 每个 *null_conditional_operations* 中删除前导的方式获取表达式 `E` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1499">For a *null_conditional_expression* `E` with a *primary_expression* `P`, let `E0` be the expression obtained by textually removing the leading `?` from each of the *null_conditional_operations* of `E` that have one.</span></span> <span data-ttu-id="2e3bd-1500">从概念上讲， `E0` 是指如果由 s 查找的 null 检查都不表示，将计算的表达式 `?` `null` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1500">Conceptually, `E0` is the expression that will be evaluated if none of the null checks represented by the `?`s do find a `null`.</span></span>

<span data-ttu-id="2e3bd-1501">同时，让我们 `E1` 通过 `?` 以从上到 *null_conditional_operations* 的第一个中删除前导的方式来获取表达式 `E` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1501">Also, let `E1` be the expression obtained by textually removing the leading `?` from just the first of the *null_conditional_operations* in `E`.</span></span> <span data-ttu-id="2e3bd-1502">如果只有一个 `?`) 或另一个 *null_conditional_expression*，则这可能会导致主表达式 (。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1502">This may lead to a *primary-expression* (if there was just one `?`) or to another *null_conditional_expression*.</span></span>

<span data-ttu-id="2e3bd-1503">例如，如果 `E` 是表达式，则 `a.b?[0]?.c()` 为表达式， `E0` `a.b[0].c()` `E1` 是表达式 `a.b[0]?.c()` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1503">For example, if `E` is the expression `a.b?[0]?.c()`, then `E0` is the expression `a.b[0].c()` and `E1` is the expression `a.b[0]?.c()`.</span></span>

<span data-ttu-id="2e3bd-1504">如果 `E0` 归类为 nothing，则 `E` 分类为 nothing。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1504">If `E0` is classified as nothing, then `E` is classified as nothing.</span></span> <span data-ttu-id="2e3bd-1505">否则，将 E 分类为值。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1505">Otherwise E is classified as a value.</span></span>

<span data-ttu-id="2e3bd-1506">`E0` 和 `E1` 用于确定的含义 `E` ：</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1506">`E0` and `E1` are used to determine the meaning of `E`:</span></span>

*  <span data-ttu-id="2e3bd-1507">如果 `E` 作为 *statement_expression* 出现，则的含义与 `E` 语句相同</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1507">If `E` occurs as a *statement_expression* the meaning of `E` is the same as the statement</span></span>

   ```csharp
   if ((object)P != null) E1;
   ```

   <span data-ttu-id="2e3bd-1508">但 P 只计算一次。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1508">except that P is evaluated only once.</span></span>

*  <span data-ttu-id="2e3bd-1509">否则，如果 `E0` 归类为 nothing，则会发生编译时错误。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1509">Otherwise, if `E0` is classified as nothing a compile-time error occurs.</span></span>

*  <span data-ttu-id="2e3bd-1510">否则，请 `T0` 键入 `E0` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1510">Otherwise, let `T0` be the type of `E0`.</span></span>

   *  <span data-ttu-id="2e3bd-1511">如果 `T0` 是一个类型参数，该参数不知道是引用类型或不可为 null 的值类型，则会发生编译时错误。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1511">If `T0` is a type parameter that is not known to be a reference type or a non-nullable value type, a compile-time error occurs.</span></span>

   *  <span data-ttu-id="2e3bd-1512">如果 `T0` 是不可以为 null 的值类型，则的类型 `E` 为 `T0?` ，并且的含义与 `E` 相同。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1512">If `T0` is a non-nullable value type, then the type of `E` is `T0?`, and the meaning of `E` is the same as</span></span>

      ```csharp
      ((object)P == null) ? (T0?)null : E1
      ```

      <span data-ttu-id="2e3bd-1513">除外， `P` 只计算一次。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1513">except that `P` is evaluated only once.</span></span>

   *  <span data-ttu-id="2e3bd-1514">否则，E 的类型为 T0，E 的含义与</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1514">Otherwise the type of E is T0, and the meaning of E is the same as</span></span>

      ```csharp
      ((object)P == null) ? null : E1
      ```

      <span data-ttu-id="2e3bd-1515">除外， `P` 只计算一次。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1515">except that `P` is evaluated only once.</span></span>

<span data-ttu-id="2e3bd-1516">如果 `E1` 本身是 *null_conditional_expression*，则将再次应用这些规则，将测试嵌套， `null` 直到没有更多 `?` 的，并且表达式已缩小为主要表达式 `E0` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1516">If `E1` is itself a *null_conditional_expression*, then these rules are applied again, nesting the tests for `null` until there are no further `?`'s, and the expression has been reduced all the way down to the primary-expression `E0`.</span></span>

<span data-ttu-id="2e3bd-1517">例如，如果表达式 `a.b?[0]?.c()` 作为语句表达式出现，如语句中所示：</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1517">For example, if the expression `a.b?[0]?.c()` occurs as a statement-expression, as in the statement:</span></span>
```csharp
a.b?[0]?.c();
```
<span data-ttu-id="2e3bd-1518">其含义等效于：</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1518">its meaning is equivalent to:</span></span>
```csharp
if (a.b != null) a.b[0]?.c();
```
<span data-ttu-id="2e3bd-1519">这同样等效于：</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1519">which again is equivalent to:</span></span>
```csharp
if (a.b != null) if (a.b[0] != null) a.b[0].c();
```
<span data-ttu-id="2e3bd-1520">除外， `a.b` `a.b[0]` 只计算一次。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1520">Except that `a.b` and `a.b[0]` are evaluated only once.</span></span>

<span data-ttu-id="2e3bd-1521">如果它发生在使用其值的上下文中，如下所示：</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1521">If it occurs in a context where its value is used, as in:</span></span>
```csharp
var x = a.b?[0]?.c();
```
<span data-ttu-id="2e3bd-1522">并且假设最终调用的类型不是可以为 null 的值类型，其含义等效于：</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1522">and assuming that the type of the final invocation is not a non-nullable value type, its meaning is equivalent to:</span></span>
```csharp
var x = (a.b == null) ? null : (a.b[0] == null) ? null : a.b[0].c();
```
<span data-ttu-id="2e3bd-1523">除外， `a.b` `a.b[0]` 只计算一次。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1523">except that `a.b` and `a.b[0]` are evaluated only once.</span></span>

#### <a name="null-conditional-expressions-as-projection-initializers"></a><span data-ttu-id="2e3bd-1524">空条件表达式作为投影初始值设定项</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1524">Null-conditional expressions as projection initializers</span></span>

<span data-ttu-id="2e3bd-1525">只允许将 null 条件表达式作为 *anonymous_object_creation_expression* ([匿名对象创建表达式](expressions.md#anonymous-object-creation-expressions)的 *member_declarator* ，) 如果它以 (（可为 null）条件) 成员访问结束。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1525">A null-conditional expression is only allowed as a *member_declarator* in an *anonymous_object_creation_expression* ([Anonymous object creation expressions](expressions.md#anonymous-object-creation-expressions)) if it ends with an (optionally null-conditional) member access.</span></span> <span data-ttu-id="2e3bd-1526">语法上，此要求可表示为：</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1526">Grammatically, this requirement can be expressed as:</span></span>

```antlr
null_conditional_member_access
    : primary_expression null_conditional_operations? '?' '.' identifier type_argument_list?
    | primary_expression null_conditional_operations '.' identifier type_argument_list?
    ;
```

<span data-ttu-id="2e3bd-1527">这是上述 *null_conditional_expression* 语法的一种特殊情况。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1527">This is a special case of the grammar for *null_conditional_expression* above.</span></span> <span data-ttu-id="2e3bd-1528">在 [匿名对象创建表达式](expressions.md#anonymous-object-creation-expressions)中 *member_declarator* 的生产仅包括 *null_conditional_member_access*。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1528">The production for *member_declarator* in [Anonymous object creation expressions](expressions.md#anonymous-object-creation-expressions) then includes only *null_conditional_member_access*.</span></span>

#### <a name="null-conditional-expressions-as-statement-expressions"></a><span data-ttu-id="2e3bd-1529">空条件表达式作为语句表达式</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1529">Null-conditional expressions as statement expressions</span></span>

<span data-ttu-id="2e3bd-1530">只允许将 null 条件表达式作为 *statement_expression* ([expression 语句](statements.md#expression-statements) ，) 如果它以调用结束。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1530">A null-conditional expression is only allowed as a *statement_expression* ([Expression statements](statements.md#expression-statements)) if it ends with an invocation.</span></span> <span data-ttu-id="2e3bd-1531">语法上，此要求可表示为：</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1531">Grammatically, this requirement can be expressed as:</span></span>

```antlr
null_conditional_invocation_expression
    : primary_expression null_conditional_operations '(' argument_list? ')'
    ;
```

<span data-ttu-id="2e3bd-1532">这是上述 *null_conditional_expression* 语法的一种特殊情况。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1532">This is a special case of the grammar for *null_conditional_expression* above.</span></span> <span data-ttu-id="2e3bd-1533">然后，[表达式语句](statements.md#expression-statements)中 *statement_expression* 的生产仅包括 *null_conditional_invocation_expression*。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1533">The production for *statement_expression* in [Expression statements](statements.md#expression-statements) then includes only *null_conditional_invocation_expression*.</span></span>


### <a name="unary-plus-operator"></a><span data-ttu-id="2e3bd-1534">一元加运算符</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1534">Unary plus operator</span></span>

<span data-ttu-id="2e3bd-1535">对于该窗体的操作 `+x` ，一元运算符重载决策 ([一元运算符重载决策](expressions.md#unary-operator-overload-resolution) 将应用于选择特定运算符实现) 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1535">For an operation of the form `+x`, unary operator overload resolution ([Unary operator overload resolution](expressions.md#unary-operator-overload-resolution)) is applied to select a specific operator implementation.</span></span> <span data-ttu-id="2e3bd-1536">操作数将转换为所选运算符的参数类型，结果的类型是运算符的返回类型。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1536">The operand is converted to the parameter type of the selected operator, and the type of the result is the return type of the operator.</span></span> <span data-ttu-id="2e3bd-1537">预定义的一元加法运算符是：</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1537">The predefined unary plus operators are:</span></span>

```csharp
int operator +(int x);
uint operator +(uint x);
long operator +(long x);
ulong operator +(ulong x);
float operator +(float x);
double operator +(double x);
decimal operator +(decimal x);
```

<span data-ttu-id="2e3bd-1538">对于每个运算符，结果只是操作数的值。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1538">For each of these operators, the result is simply the value of the operand.</span></span>

### <a name="unary-minus-operator"></a><span data-ttu-id="2e3bd-1539">一元减运算符</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1539">Unary minus operator</span></span>

<span data-ttu-id="2e3bd-1540">对于该窗体的操作 `-x` ，一元运算符重载决策 ([一元运算符重载决策](expressions.md#unary-operator-overload-resolution) 将应用于选择特定运算符实现) 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1540">For an operation of the form `-x`, unary operator overload resolution ([Unary operator overload resolution](expressions.md#unary-operator-overload-resolution)) is applied to select a specific operator implementation.</span></span> <span data-ttu-id="2e3bd-1541">操作数将转换为所选运算符的参数类型，结果的类型是运算符的返回类型。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1541">The operand is converted to the parameter type of the selected operator, and the type of the result is the return type of the operator.</span></span> <span data-ttu-id="2e3bd-1542">预定义的求反运算符是：</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1542">The predefined negation operators are:</span></span>

*  <span data-ttu-id="2e3bd-1543">整数取反：</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1543">Integer negation:</span></span>

   ```csharp
   int operator -(int x);
   long operator -(long x);
   ```

   <span data-ttu-id="2e3bd-1544">通过从零进行减计算来计算结果 `x` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1544">The result is computed by subtracting `x` from zero.</span></span> <span data-ttu-id="2e3bd-1545">如果的值 `x` 为操作数类型的最小可表示值 (-2 ^ 31 用于 `int` 或-2 ^ 63 （对于 `long`) ），则 `x` 不能在操作数类型内表示。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1545">If the value of `x` is the smallest representable value of the operand type (-2^31 for `int` or -2^63 for `long`), then the mathematical negation of `x` is not representable within the operand type.</span></span> <span data-ttu-id="2e3bd-1546">如果在上下文中发生此情况 `checked` ，则 `System.OverflowException` 会引发; 如果发生在 `unchecked` 上下文中，则结果为操作数的值并且不报告溢出。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1546">If this occurs within a `checked` context, a `System.OverflowException` is thrown; if it occurs within an `unchecked` context, the result is the value of the operand and the overflow is not reported.</span></span>

   <span data-ttu-id="2e3bd-1547">如果求反运算符的操作数的类型为，则将 `uint` 其转换为类型 `long` ，并且结果的类型为 `long` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1547">If the operand of the negation operator is of type `uint`, it is converted to type `long`, and the type of the result is `long`.</span></span> <span data-ttu-id="2e3bd-1548">例外情况是，允许将 `int` 2147483648 (-2 ^ 31) 值的规则作为十进制整数文本写入 ([整数文本](lexical-structure.md#integer-literals)) 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1548">An exception is the rule that permits the `int` value -2147483648 (-2^31) to be written as a decimal integer literal ([Integer literals](lexical-structure.md#integer-literals)).</span></span>

   <span data-ttu-id="2e3bd-1549">如果求反运算符的操作数为类型，则 `ulong` 会发生编译时错误。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1549">If the operand of the negation operator is of type `ulong`, a compile-time error occurs.</span></span> <span data-ttu-id="2e3bd-1550">例外情况是允许将 `long` 值-9223372036854775808 (-2 ^ 63) 写入 ([整数文本](lexical-structure.md#integer-literals)) 的十进制整数。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1550">An exception is the rule that permits the `long` value -9223372036854775808 (-2^63) to be written as a decimal integer literal ([Integer literals](lexical-structure.md#integer-literals)).</span></span>

*  <span data-ttu-id="2e3bd-1551">浮点反：</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1551">Floating-point negation:</span></span>

   ```csharp
   float operator -(float x);
   double operator -(double x);
   ```

   <span data-ttu-id="2e3bd-1552">结果为 `x` 其符号反转的值。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1552">The result is the value of `x` with its sign inverted.</span></span> <span data-ttu-id="2e3bd-1553">如果 `x` 为 nan，则结果也为 nan。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1553">If `x` is NaN, the result is also NaN.</span></span>

*  <span data-ttu-id="2e3bd-1554">小数求反：</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1554">Decimal negation:</span></span>

   ```csharp
   decimal operator -(decimal x);
   ```

   <span data-ttu-id="2e3bd-1555">通过从零进行减计算来计算结果 `x` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1555">The result is computed by subtracting `x` from zero.</span></span> <span data-ttu-id="2e3bd-1556">Decimal 求反等效于使用类型的一元减号运算符 `System.Decimal` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1556">Decimal negation is equivalent to using the unary minus operator of type `System.Decimal`.</span></span>

### <a name="logical-negation-operator"></a><span data-ttu-id="2e3bd-1557">逻辑求反运算符</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1557">Logical negation operator</span></span>

<span data-ttu-id="2e3bd-1558">对于该窗体的操作 `!x` ，一元运算符重载决策 ([一元运算符重载决策](expressions.md#unary-operator-overload-resolution) 将应用于选择特定运算符实现) 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1558">For an operation of the form `!x`, unary operator overload resolution ([Unary operator overload resolution](expressions.md#unary-operator-overload-resolution)) is applied to select a specific operator implementation.</span></span> <span data-ttu-id="2e3bd-1559">操作数将转换为所选运算符的参数类型，结果的类型是运算符的返回类型。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1559">The operand is converted to the parameter type of the selected operator, and the type of the result is the return type of the operator.</span></span> <span data-ttu-id="2e3bd-1560">只存在一个预定义的逻辑求反运算符：</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1560">Only one predefined logical negation operator exists:</span></span>
```csharp
bool operator !(bool x);
```

<span data-ttu-id="2e3bd-1561">此运算符计算操作数的逻辑非运算：如果操作数为 `true` ，则结果为 `false` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1561">This operator computes the logical negation of the operand: If the operand is `true`, the result is `false`.</span></span> <span data-ttu-id="2e3bd-1562">如果操作数为 `false`，则结果为 `true`。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1562">If the operand is `false`, the result is `true`.</span></span>

### <a name="bitwise-complement-operator"></a><span data-ttu-id="2e3bd-1563">按位求补运算符</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1563">Bitwise complement operator</span></span>

<span data-ttu-id="2e3bd-1564">对于该窗体的操作 `~x` ，一元运算符重载决策 ([一元运算符重载决策](expressions.md#unary-operator-overload-resolution) 将应用于选择特定运算符实现) 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1564">For an operation of the form `~x`, unary operator overload resolution ([Unary operator overload resolution](expressions.md#unary-operator-overload-resolution)) is applied to select a specific operator implementation.</span></span> <span data-ttu-id="2e3bd-1565">操作数将转换为所选运算符的参数类型，结果的类型是运算符的返回类型。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1565">The operand is converted to the parameter type of the selected operator, and the type of the result is the return type of the operator.</span></span> <span data-ttu-id="2e3bd-1566">预定义的按位求补运算符是：</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1566">The predefined bitwise complement operators are:</span></span>
```csharp
int operator ~(int x);
uint operator ~(uint x);
long operator ~(long x);
ulong operator ~(ulong x);
```

<span data-ttu-id="2e3bd-1567">对于每个运算符，操作的结果为的按位求补 `x` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1567">For each of these operators, the result of the operation is the bitwise complement of `x`.</span></span>

<span data-ttu-id="2e3bd-1568">每个枚举类型都 `E` 隐式提供以下按位求补运算符：</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1568">Every enumeration type `E` implicitly provides the following bitwise complement operator:</span></span>

```csharp
E operator ~(E x);
```

<span data-ttu-id="2e3bd-1569">计算结果 `~x` （其中， `x` 是具有基础类型的枚举类型的表达式 `E` ）与 `U` 计算完全相同， `(E)(~(U)x)` 不同之处在于，转换为时，将 `E` 始终像在 `unchecked` 上下文 ([已检查和未检查的运算符](expressions.md#the-checked-and-unchecked-operators)) 一样执行。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1569">The result of evaluating `~x`, where `x` is an expression of an enumeration type `E` with an underlying type `U`, is exactly the same as evaluating `(E)(~(U)x)`, except that the conversion to `E` is always performed as if in an `unchecked` context ([The checked and unchecked operators](expressions.md#the-checked-and-unchecked-operators)).</span></span>

### <a name="prefix-increment-and-decrement-operators"></a><span data-ttu-id="2e3bd-1570">前缀增量和减量运算符</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1570">Prefix increment and decrement operators</span></span>

```antlr
pre_increment_expression
    : '++' unary_expression
    ;

pre_decrement_expression
    : '--' unary_expression
    ;
```

<span data-ttu-id="2e3bd-1571">前缀递增或递减运算的操作数必须是分类为变量、属性访问或索引器访问的表达式。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1571">The operand of a prefix increment or decrement operation must be an expression classified as a variable, a property access, or an indexer access.</span></span> <span data-ttu-id="2e3bd-1572">操作的结果是与操作数类型相同的值。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1572">The result of the operation is a value of the same type as the operand.</span></span>

<span data-ttu-id="2e3bd-1573">如果前缀递增或递减运算的操作数是属性或索引器访问，则属性或索引器必须同时具有 `get` 和 `set` 访问器。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1573">If the operand of a prefix increment or decrement operation is a property or indexer access, the property or indexer must have both a `get` and a `set` accessor.</span></span> <span data-ttu-id="2e3bd-1574">如果不是这种情况，则会发生绑定时错误。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1574">If this is not the case, a binding-time error occurs.</span></span>

<span data-ttu-id="2e3bd-1575">一元运算符重载决策 ([一元运算符重载决策](expressions.md#unary-operator-overload-resolution) 应用于选择特定运算符实现) 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1575">Unary operator overload resolution ([Unary operator overload resolution](expressions.md#unary-operator-overload-resolution)) is applied to select a specific operator implementation.</span></span> <span data-ttu-id="2e3bd-1576">预定义 `++` 和 `--` 运算符存在以下类型：、、、、、、、、、、、 `sbyte` `byte` `short` `ushort` `int` `uint` `long` `ulong` `char` `float` `double` `decimal` 和枚举类型。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1576">Predefined `++` and `--` operators exist for the following types: `sbyte`, `byte`, `short`, `ushort`, `int`, `uint`, `long`, `ulong`, `char`, `float`, `double`, `decimal`, and any enum type.</span></span> <span data-ttu-id="2e3bd-1577">预定义 `++` 运算符返回通过向操作数添加1而生成的值，预定义 `--` 运算符返回通过从操作数中减去1而生成的值。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1577">The predefined `++` operators return the value produced by adding 1 to the operand, and the predefined `--` operators return the value produced by subtracting 1 from the operand.</span></span> <span data-ttu-id="2e3bd-1578">在 `checked` 上下文中，如果此加法或减法的结果超出了结果类型的范围，且结果类型为整型类型或枚举类型，则 `System.OverflowException` 会引发。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1578">In a `checked` context, if the result of this addition or subtraction is outside the range of the result type and the result type is an integral type or enum type, a `System.OverflowException` is thrown.</span></span>

<span data-ttu-id="2e3bd-1579">此形式的前缀递增或递减运算的运行时处理， `++x` 或 `--x` 包含以下步骤：</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1579">The run-time processing of a prefix increment or decrement operation of the form `++x` or `--x` consists of the following steps:</span></span>

*   <span data-ttu-id="2e3bd-1580">如果 `x` 归类为变量：</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1580">If `x` is classified as a variable:</span></span>
    * <span data-ttu-id="2e3bd-1581">`x` 计算以生成变量。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1581">`x` is evaluated to produce the variable.</span></span>
    * <span data-ttu-id="2e3bd-1582">将调用所选运算符，并将值 `x` 作为其参数。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1582">The selected operator is invoked with the value of `x` as its argument.</span></span>
    * <span data-ttu-id="2e3bd-1583">运算符返回的值存储在的计算给定的位置 `x` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1583">The value returned by the operator is stored in the location given by the evaluation of `x`.</span></span>
    * <span data-ttu-id="2e3bd-1584">运算符返回的值成为操作的结果。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1584">The value returned by the operator becomes the result of the operation.</span></span>
*   <span data-ttu-id="2e3bd-1585">如果 `x` 归类为属性或索引器访问：</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1585">If `x` is classified as a property or indexer access:</span></span>
    * <span data-ttu-id="2e3bd-1586">如果) 为，则实例表达式 (如果是，则为 `x` `static` `x` ; 如果是与关联的索引器访问) ，则 (参数列表 `x` ，并且在后续 `get` `set` 调用和访问器调用中使用结果。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1586">The instance expression (if `x` is not `static`) and the argument list (if `x` is an indexer access) associated with `x` are evaluated, and the results are used in the subsequent `get` and `set` accessor invocations.</span></span>
    * <span data-ttu-id="2e3bd-1587">`get`调用的访问器 `x` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1587">The `get` accessor of `x` is invoked.</span></span>
    * <span data-ttu-id="2e3bd-1588">使用访问器作为参数返回的值调用所选运算符 `get` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1588">The selected operator is invoked with the value returned by the `get` accessor as its argument.</span></span>
    * <span data-ttu-id="2e3bd-1589">`set`调用的访问器时，将 `x` 使用由运算符返回的值作为其 `value` 参数。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1589">The `set` accessor of `x` is invoked with the value returned by the operator as its `value` argument.</span></span>
    * <span data-ttu-id="2e3bd-1590">运算符返回的值成为操作的结果。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1590">The value returned by the operator becomes the result of the operation.</span></span>

<span data-ttu-id="2e3bd-1591">`++`和 `--` 运算符还支持后缀表示法[， (后缀递增和递减运算符](expressions.md#postfix-increment-and-decrement-operators)) 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1591">The `++` and `--` operators also support postfix notation ([Postfix increment and decrement operators](expressions.md#postfix-increment-and-decrement-operators)).</span></span> <span data-ttu-id="2e3bd-1592">通常，或的结果 `x++` `x--` 是操作之前的值 `x` ，而或的结果 `++x` `--x` 是 `x` 运算后的值。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1592">Typically, the result of `x++` or `x--` is the value of `x` before the operation, whereas the result of `++x` or `--x` is the value of `x` after the operation.</span></span> <span data-ttu-id="2e3bd-1593">在这两种情况下， `x` 其本身在操作后具有相同的值。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1593">In either case, `x` itself has the same value after the operation.</span></span>

<span data-ttu-id="2e3bd-1594">`operator++` `operator--` 可以使用后缀或前缀表示法来调用或实现。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1594">An `operator++` or `operator--` implementation can be invoked using either postfix or prefix notation.</span></span> <span data-ttu-id="2e3bd-1595">对于这两个表示法，不能有单独的运算符实现。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1595">It is not possible to have separate operator implementations for the two notations.</span></span>

### <a name="cast-expressions"></a><span data-ttu-id="2e3bd-1596">强制转换表达式</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1596">Cast expressions</span></span>

<span data-ttu-id="2e3bd-1597">*Cast_expression* 用于将表达式显式转换为给定类型。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1597">A *cast_expression* is used to explicitly convert an expression to a given type.</span></span>

```antlr
cast_expression
    : '(' type ')' unary_expression
    ;
```

<span data-ttu-id="2e3bd-1598">窗体的 *cast_expression* `(T)E` ，其中 `T` 是一个 *类型* 并且 `E` 是一个 *unary_expression*，它执行显式转换， (将的值) 到类型的 [显式](conversions.md#explicit-conversions) 转换 `E` `T` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1598">A *cast_expression* of the form `(T)E`, where `T` is a *type* and `E` is a *unary_expression*, performs an explicit conversion ([Explicit conversions](conversions.md#explicit-conversions)) of the value of `E` to type `T`.</span></span> <span data-ttu-id="2e3bd-1599">如果不存在从到的显式转换 `E` `T` ，则发生绑定时错误。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1599">If no explicit conversion exists from `E` to `T`, a binding-time error occurs.</span></span> <span data-ttu-id="2e3bd-1600">否则，结果为显式转换生成的值。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1600">Otherwise, the result is the value produced by the explicit conversion.</span></span> <span data-ttu-id="2e3bd-1601">即使表示变量，结果也始终归类为值 `E` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1601">The result is always classified as a value, even if `E` denotes a variable.</span></span>

<span data-ttu-id="2e3bd-1602">*Cast_expression* 的语法导致某些语义歧义。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1602">The grammar for a *cast_expression* leads to certain syntactic ambiguities.</span></span> <span data-ttu-id="2e3bd-1603">例如，表达式 `(x)-y` 可以被解释为类型) 的强制转换为类型的 *cast_expression* (`-y` `x` *additive_expression* 或与计算值的 *parenthesized_expression* (组合 `x - y)` 在一起。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1603">For example, the expression `(x)-y` could either be interpreted as a *cast_expression* (a cast of `-y` to type `x`) or as an *additive_expression* combined with a *parenthesized_expression* (which computes the value `x - y)`.</span></span>

<span data-ttu-id="2e3bd-1604">若要解决 *cast_expression* 歧义，存在以下规则：只有在满足以下条件之一时，才会将包含在括号中的一个或多个 *标记* () [空格](lexical-structure.md#white-space) 的序列视为 *cast_expression* 的开头：</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1604">To resolve *cast_expression* ambiguities, the following rule exists: A sequence of one or more *token* s ([White space](lexical-structure.md#white-space)) enclosed in parentheses is considered the start of a *cast_expression* only if at least one of the following are true:</span></span>

*  <span data-ttu-id="2e3bd-1605">标记序列对于 *类型* 是正确的语法，而不是 *表达式* 的语法。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1605">The sequence of tokens is correct grammar for a *type*, but not for an *expression*.</span></span>
*  <span data-ttu-id="2e3bd-1606">标记序列对于 *类型* 是正确的语法，紧跟在右括号后面的标记是标记 " `~` "、标记 ""、标记 `!` " `(` "、 *标识符* ([Unicode 字符转义序列) Unicode 字符转义序列](lexical-structure.md#unicode-character-escape-sequences) 、 *文本* ([文本](lexical-structure.md#literals)) 或除和以外的任何 *关键字* ([关键字](lexical-structure.md#keywords) `as` `is` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1606">The sequence of tokens is correct grammar for a *type*, and the token immediately following the closing parentheses is the token "`~`", the token "`!`", the token "`(`", an *identifier* ([Unicode character escape sequences](lexical-structure.md#unicode-character-escape-sequences)), a *literal* ([Literals](lexical-structure.md#literals)), or any *keyword* ([Keywords](lexical-structure.md#keywords)) except `as` and `is`.</span></span>

<span data-ttu-id="2e3bd-1607">上面的 "更正语法" 一词只是指标记序列必须符合特定的语法生产。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1607">The term "correct grammar" above means only that the sequence of tokens must conform to the particular grammatical production.</span></span> <span data-ttu-id="2e3bd-1608">具体而言，它不考虑任何构成标识符的实际含义。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1608">It specifically does not consider the actual meaning of any constituent identifiers.</span></span> <span data-ttu-id="2e3bd-1609">例如，如果 `x` 和 `y` 是标识符，则 `x.y` 是类型的正确语法，即使 `x.y` 实际上不表示类型也是如此。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1609">For example, if `x` and `y` are identifiers, then `x.y` is correct grammar for a type, even if `x.y` doesn't actually denote a type.</span></span>

<span data-ttu-id="2e3bd-1610">从消除歧义规则开始，如果 `x` 和是标识符、、cast_expression 和，则为，而 `y` `(x)y` `(x)(y)` `(x)(-y)`  `(x)-y` 不是，即使标识一个类型，也是如此 `x` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1610">From the disambiguation rule it follows that, if `x` and `y` are identifiers, `(x)y`, `(x)(y)`, and `(x)(-y)` are *cast_expression* s, but `(x)-y` is not, even if `x` identifies a type.</span></span> <span data-ttu-id="2e3bd-1611">但是，如果 `x` 是标识预定义类型的关键字 (如 `int`) ，则所有四个窗体 *cast_expression* s (，因为这种关键字) 本身不可能是表达式。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1611">However, if `x` is a keyword that identifies a predefined type (such as `int`), then all four forms are *cast_expression* s (because such a keyword could not possibly be an expression by itself).</span></span>

### <a name="await-expressions"></a><span data-ttu-id="2e3bd-1612">Await 表达式</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1612">Await expressions</span></span>

<span data-ttu-id="2e3bd-1613">Await 运算符用于暂停封闭异步函数的计算，直到操作数表示的异步操作完成。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1613">The await operator is used to suspend evaluation of the enclosing async function until the asynchronous operation represented by the operand has completed.</span></span>

```antlr
await_expression
    : 'await' unary_expression
    ;
```

<span data-ttu-id="2e3bd-1614">只允许在异步函数的主体中使用 *await_expression* () [异步函数](classes.md#async-functions) 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1614">An *await_expression* is only allowed in the body of an async function ([Async functions](classes.md#async-functions)).</span></span> <span data-ttu-id="2e3bd-1615">在最近的封闭 async 函数中， *await_expression* 可能不会出现在以下位置：</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1615">Within the nearest enclosing async function, an *await_expression* may not occur in these places:</span></span>

*  <span data-ttu-id="2e3bd-1616">在嵌套的 (非异步) 匿名函数内</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1616">Inside a nested (non-async) anonymous function</span></span>
*  <span data-ttu-id="2e3bd-1617">在 *lock_statement* 块内</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1617">Inside the block of a *lock_statement*</span></span>
*  <span data-ttu-id="2e3bd-1618">在不安全的上下文中</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1618">In an unsafe context</span></span>

<span data-ttu-id="2e3bd-1619">请注意， *await_expression* 不能出现在 *query_expression* 内的大多数位置，因为这些是在语法上转换为使用非 async lambda 表达式。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1619">Note that an *await_expression* cannot occur in most places within a *query_expression*, because those are syntactically transformed to use non-async lambda expressions.</span></span>

<span data-ttu-id="2e3bd-1620">在异步函数内， `await` 不能用作标识符。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1620">Inside of an async function, `await` cannot be used as an identifier.</span></span> <span data-ttu-id="2e3bd-1621">因此，await 表达式和涉及标识符的各种表达式之间没有语法多义性。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1621">There is therefore no syntactic ambiguity between await-expressions and various expressions involving identifiers.</span></span> <span data-ttu-id="2e3bd-1622">在异步函数的外部，作为 `await` 常规标识符。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1622">Outside of async functions, `await` acts as a normal identifier.</span></span>

<span data-ttu-id="2e3bd-1623">*Await_expression* 的操作数称为 \***task** _。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1623">The operand of an *await_expression* is called the \***task** _.</span></span> <span data-ttu-id="2e3bd-1624">它表示一个异步操作，该操作在评估 _await_expression \* 时可能会或可能不会完成。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1624">It represents an asynchronous operation that may or may not be complete at the time the _await_expression\* is evaluated.</span></span> <span data-ttu-id="2e3bd-1625">Await 运算符的用途是在等待的任务完成之前挂起封闭异步函数的执行，然后获取其结果。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1625">The purpose of the await operator is to suspend execution of the enclosing async function until the awaited task is complete, and then obtain its outcome.</span></span>

#### <a name="awaitable-expressions"></a><span data-ttu-id="2e3bd-1626">可等待表达式</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1626">Awaitable expressions</span></span>

<span data-ttu-id="2e3bd-1627">Await 表达式的任务需要 ***可等待***。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1627">The task of an await expression is required to be ***awaitable***.</span></span> <span data-ttu-id="2e3bd-1628">`t`如果以下包含其中一项，则表达式为可等待：</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1628">An expression `t` is awaitable if one of the following holds:</span></span>

*  <span data-ttu-id="2e3bd-1629">`t` 属于编译时类型 `dynamic`</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1629">`t` is of compile time type `dynamic`</span></span>
*  <span data-ttu-id="2e3bd-1630">`t` 具有调用的可访问实例或扩展方法 `GetAwaiter` ，其中不包含任何参数和类型参数，以及 `A` 以下所有保留的返回类型：</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1630">`t` has an accessible instance or extension method called `GetAwaiter` with no parameters and no type parameters, and a return type `A` for which all of the following hold:</span></span>
   * <span data-ttu-id="2e3bd-1631">`A` 实现接口 `System.Runtime.CompilerServices.INotifyCompletion` (以后称为的简称 `INotifyCompletion`) </span><span class="sxs-lookup"><span data-stu-id="2e3bd-1631">`A` implements the interface `System.Runtime.CompilerServices.INotifyCompletion` (hereafter known as `INotifyCompletion` for brevity)</span></span>
   * <span data-ttu-id="2e3bd-1632">`A`具有类型的可访问的可读实例属性 `IsCompleted``bool`</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1632">`A` has an accessible, readable instance property `IsCompleted` of type `bool`</span></span>
   * <span data-ttu-id="2e3bd-1633">`A` 具有一个 `GetResult` 无参数且无类型参数的可访问实例方法</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1633">`A` has an accessible instance method `GetResult` with no parameters and no type parameters</span></span>

<span data-ttu-id="2e3bd-1634">此方法的目的 `GetAwaiter` 是获取任务的 \***awaiter** _。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1634">The purpose of the `GetAwaiter` method is to obtain an \***awaiter** _ for the task.</span></span> <span data-ttu-id="2e3bd-1635">`A`对于 await 表达式，类型称为 _ \*_awaiter type_\*\*。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1635">The type `A` is called the _ *_awaiter type_*\* for the await expression.</span></span>

<span data-ttu-id="2e3bd-1636">属性的用途 `IsCompleted` 是确定任务是否已完成。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1636">The purpose of the `IsCompleted` property is to determine if the task is already complete.</span></span> <span data-ttu-id="2e3bd-1637">如果是这样，则无需暂停评估。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1637">If so, there is no need to suspend evaluation.</span></span>

<span data-ttu-id="2e3bd-1638">此方法的目的 `INotifyCompletion.OnCompleted` 是将 "延续" 注册到任务，即) 类型的委托 (`System.Action` ，一旦任务完成，就会调用该委托。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1638">The purpose of the `INotifyCompletion.OnCompleted` method is to sign up a "continuation" to the task; i.e. a delegate (of type `System.Action`) that will be invoked once the task is complete.</span></span>

<span data-ttu-id="2e3bd-1639">此方法的目的 `GetResult` 是在任务完成后获取任务的结果。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1639">The purpose of the `GetResult` method is to obtain the outcome of the task once it is complete.</span></span> <span data-ttu-id="2e3bd-1640">此结果可能会成功完成（可能包含结果值），也可能是由方法引发的异常 `GetResult` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1640">This outcome may be successful completion, possibly with a result value, or it may be an exception which is thrown by the `GetResult` method.</span></span>

#### <a name="classification-of-await-expressions"></a><span data-ttu-id="2e3bd-1641">Await 表达式的分类</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1641">Classification of await expressions</span></span>

<span data-ttu-id="2e3bd-1642">表达式 `await t` 的分类方式与表达式相同 `(t).GetAwaiter().GetResult()` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1642">The expression `await t` is classified the same way as the expression `(t).GetAwaiter().GetResult()`.</span></span> <span data-ttu-id="2e3bd-1643">因此，如果的返回类型 `GetResult` 为 `void` ，则 *await_expression* 分类为 nothing。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1643">Thus, if the return type of `GetResult` is `void`, the *await_expression* is classified as nothing.</span></span> <span data-ttu-id="2e3bd-1644">如果它具有非 void 返回类型 `T` ，则将 *await_expression* 分类为类型的值 `T` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1644">If it has a non-void return type `T`, the *await_expression* is classified as a value of type `T`.</span></span>

#### <a name="runtime-evaluation-of-await-expressions"></a><span data-ttu-id="2e3bd-1645">Await 表达式的运行时计算</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1645">Runtime evaluation of await expressions</span></span>

<span data-ttu-id="2e3bd-1646">在运行时，表达式的 `await t` 计算方式如下：</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1646">At runtime, the expression `await t` is evaluated as follows:</span></span>

*  <span data-ttu-id="2e3bd-1647">`a`通过计算表达式来获取 awaiter `(t).GetAwaiter()` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1647">An awaiter `a` is obtained by evaluating the expression `(t).GetAwaiter()`.</span></span>
*  <span data-ttu-id="2e3bd-1648">`bool` `b` 通过计算表达式获得 `(a).IsCompleted` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1648">A `bool` `b` is obtained by evaluating the expression `(a).IsCompleted`.</span></span>
*  <span data-ttu-id="2e3bd-1649">如果 `b` 为， `false` 则计算结果取决于是否 `a` 实现了接口 `System.Runtime.CompilerServices.ICriticalNotifyCompletion` (以后 `ICriticalNotifyCompletion`) 的简称。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1649">If `b` is `false` then evaluation depends on whether `a` implements the interface `System.Runtime.CompilerServices.ICriticalNotifyCompletion` (hereafter known as `ICriticalNotifyCompletion` for brevity).</span></span> <span data-ttu-id="2e3bd-1650">此检查是在绑定时进行的;例如，在运行时 `a` ，如果具有编译时类型，则为 `dynamic` ; 否则为。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1650">This check is done at binding time; i.e. at runtime if `a` has the compile time type `dynamic`, and at compile time otherwise.</span></span> <span data-ttu-id="2e3bd-1651">让 `r` ([异步函数](classes.md#async-functions)) 恢复委托：</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1651">Let `r` denote the resumption delegate ([Async functions](classes.md#async-functions)):</span></span>
    * <span data-ttu-id="2e3bd-1652">如果不 `a` 实现 `ICriticalNotifyCompletion` ，则 `(a as (INotifyCompletion)).OnCompleted(r)` 计算表达式。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1652">If `a` does not implement `ICriticalNotifyCompletion`, then the expression `(a as (INotifyCompletion)).OnCompleted(r)` is evaluated.</span></span>
    * <span data-ttu-id="2e3bd-1653">如果 `a` 实现 `ICriticalNotifyCompletion` ，则 `(a as (ICriticalNotifyCompletion)).UnsafeOnCompleted(r)` 计算表达式。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1653">If `a` does implement `ICriticalNotifyCompletion`, then the expression `(a as (ICriticalNotifyCompletion)).UnsafeOnCompleted(r)` is evaluated.</span></span>
    * <span data-ttu-id="2e3bd-1654">然后，将挂起计算，并将控制权返回给 async 函数的当前调用方。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1654">Evaluation is then suspended, and control is returned to the current caller of the async function.</span></span>
*  <span data-ttu-id="2e3bd-1655">如果 `b` `true`) ，则 (立即后，或在以后调用恢复委托)  (时 `b` ，将 `false` 计算该表达式 `(a).GetResult()` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1655">Either immediately after (if `b` was `true`), or upon later invocation of the resumption delegate (if `b` was `false`), the expression `(a).GetResult()` is evaluated.</span></span> <span data-ttu-id="2e3bd-1656">如果返回值，则该值是 *await_expression* 的结果。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1656">If it returns a value, that value is the result of the *await_expression*.</span></span> <span data-ttu-id="2e3bd-1657">否则，结果为 nothing。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1657">Otherwise the result is nothing.</span></span>

<span data-ttu-id="2e3bd-1658">Awaiter 的接口方法实现 `INotifyCompletion.OnCompleted` 并 `ICriticalNotifyCompletion.UnsafeOnCompleted` 应导致委托 `r` 最多调用一次。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1658">An awaiter's implementation of the interface methods `INotifyCompletion.OnCompleted` and `ICriticalNotifyCompletion.UnsafeOnCompleted` should cause the delegate `r` to be invoked at most once.</span></span> <span data-ttu-id="2e3bd-1659">否则，封闭异步函数的行为是不确定的。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1659">Otherwise, the behavior of the enclosing async function is undefined.</span></span>

## <a name="arithmetic-operators"></a><span data-ttu-id="2e3bd-1660">算术运算符</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1660">Arithmetic operators</span></span>

<span data-ttu-id="2e3bd-1661">`*`、、 `/` `%` 、 `+` 和 `-` 运算符称为算术运算符。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1661">The `*`, `/`, `%`, `+`, and `-` operators are called the arithmetic operators.</span></span>

```antlr
multiplicative_expression
    : unary_expression
    | multiplicative_expression '*' unary_expression
    | multiplicative_expression '/' unary_expression
    | multiplicative_expression '%' unary_expression
    ;

additive_expression
    : multiplicative_expression
    | additive_expression '+' multiplicative_expression
    | additive_expression '-' multiplicative_expression
    ;
```

<span data-ttu-id="2e3bd-1662">如果算术运算符的操作数具有编译时类型 `dynamic` ，则该表达式将动态绑定 ([动态绑定](expressions.md#dynamic-binding)) 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1662">If an operand of an arithmetic operator has the compile-time type `dynamic`, then the expression is dynamically bound ([Dynamic binding](expressions.md#dynamic-binding)).</span></span> <span data-ttu-id="2e3bd-1663">在这种情况下，表达式的编译时类型为 `dynamic` ，并使用具有编译时类型的操作数的运行时类型在运行时执行下面所述的解决方案 `dynamic` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1663">In this case the compile-time type of the expression is `dynamic`, and the resolution described below will take place at run-time using the run-time type of those operands that have the compile-time type `dynamic`.</span></span>

### <a name="multiplication-operator"></a><span data-ttu-id="2e3bd-1664">乘法运算符</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1664">Multiplication operator</span></span>

<span data-ttu-id="2e3bd-1665">对于窗体操作 `x * y` ，二元运算符重载决策 ([二元运算符重载决策](expressions.md#binary-operator-overload-resolution) ，) 应用于选择特定的运算符实现。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1665">For an operation of the form `x * y`, binary operator overload resolution ([Binary operator overload resolution](expressions.md#binary-operator-overload-resolution)) is applied to select a specific operator implementation.</span></span> <span data-ttu-id="2e3bd-1666">操作数将转换为所选运算符的参数类型，结果的类型就是运算符的返回类型。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1666">The operands are converted to the parameter types of the selected operator, and the type of the result is the return type of the operator.</span></span>

<span data-ttu-id="2e3bd-1667">下面列出了预定义的乘法运算符。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1667">The predefined multiplication operators are listed below.</span></span> <span data-ttu-id="2e3bd-1668">运算符都计算和的乘积 `x` `y` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1668">The operators all compute the product of `x` and `y`.</span></span>

*  <span data-ttu-id="2e3bd-1669">整数乘法：</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1669">Integer multiplication:</span></span>

   ```csharp
   int operator *(int x, int y);
   uint operator *(uint x, uint y);
   long operator *(long x, long y);
   ulong operator *(ulong x, ulong y);
   ```

   <span data-ttu-id="2e3bd-1670">在 `checked` 上下文中，如果该产品超出了结果类型的范围， `System.OverflowException` 则会引发。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1670">In a `checked` context, if the product is outside the range of the result type, a `System.OverflowException` is thrown.</span></span> <span data-ttu-id="2e3bd-1671">在 `unchecked` 上下文中，不会报告溢出，并且放弃结果类型范围外的任何重要高序位。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1671">In an `unchecked` context, overflows are not reported and any significant high-order bits outside the range of the result type are discarded.</span></span>


*  <span data-ttu-id="2e3bd-1672">浮点乘法：</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1672">Floating-point multiplication:</span></span>

   ```csharp
   float operator *(float x, float y);
   double operator *(double x, double y);
   ```

   <span data-ttu-id="2e3bd-1673">根据 IEEE 754 算法的规则计算产品。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1673">The product is computed according to the rules of IEEE 754 arithmetic.</span></span> <span data-ttu-id="2e3bd-1674">下表列出了非零有限值、零、无限值和 NaN 的所有可能组合的结果。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1674">The following table lists the results of all possible combinations of nonzero finite values, zeros, infinities, and NaN's.</span></span> <span data-ttu-id="2e3bd-1675">在表中，`x` 和 `y` 为正有限值。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1675">In the table, `x` and `y` are positive finite values.</span></span> <span data-ttu-id="2e3bd-1676">`x * y` 的结果为 `z`。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1676">`z` is the result of `x * y`.</span></span> <span data-ttu-id="2e3bd-1677">如果结果相对于目标类型而言太大，则 `z` 为无穷值。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1677">If the result is too large for the destination type, `z` is infinity.</span></span> <span data-ttu-id="2e3bd-1678">如果结果相对于目标类型而言太小，则 `z` 为零。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1678">If the result is too small for the destination type, `z` is zero.</span></span>

   |      |      |      |     |     |      |      |     |
   |:----:|-----:|:----:|:---:|:---:|:----:|:----:|:----|
   |      | <span data-ttu-id="2e3bd-1679">+y</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1679">+y</span></span>   | <span data-ttu-id="2e3bd-1680">-y</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1680">-y</span></span>   | <span data-ttu-id="2e3bd-1681">+0</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1681">+0</span></span>  | <span data-ttu-id="2e3bd-1682">-0</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1682">-0</span></span>  | <span data-ttu-id="2e3bd-1683">+inf</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1683">+inf</span></span> | <span data-ttu-id="2e3bd-1684">-inf</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1684">-inf</span></span> | <span data-ttu-id="2e3bd-1685">NaN</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1685">NaN</span></span> | 
   | <span data-ttu-id="2e3bd-1686">+x</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1686">+x</span></span>   | <span data-ttu-id="2e3bd-1687">+z</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1687">+z</span></span>   | <span data-ttu-id="2e3bd-1688">-Z</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1688">-z</span></span>   | <span data-ttu-id="2e3bd-1689">+0</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1689">+0</span></span>  | <span data-ttu-id="2e3bd-1690">-0</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1690">-0</span></span>  | <span data-ttu-id="2e3bd-1691">+inf</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1691">+inf</span></span> | <span data-ttu-id="2e3bd-1692">-inf</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1692">-inf</span></span> | <span data-ttu-id="2e3bd-1693">NaN</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1693">NaN</span></span> | 
   | <span data-ttu-id="2e3bd-1694">-X</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1694">-x</span></span>   | <span data-ttu-id="2e3bd-1695">-Z</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1695">-z</span></span>   | <span data-ttu-id="2e3bd-1696">+z</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1696">+z</span></span>   | <span data-ttu-id="2e3bd-1697">-0</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1697">-0</span></span>  | <span data-ttu-id="2e3bd-1698">+0</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1698">+0</span></span>  | <span data-ttu-id="2e3bd-1699">-inf</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1699">-inf</span></span> | <span data-ttu-id="2e3bd-1700">+inf</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1700">+inf</span></span> | <span data-ttu-id="2e3bd-1701">NaN</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1701">NaN</span></span> | 
   | <span data-ttu-id="2e3bd-1702">+0</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1702">+0</span></span>   | <span data-ttu-id="2e3bd-1703">+0</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1703">+0</span></span>   | <span data-ttu-id="2e3bd-1704">-0</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1704">-0</span></span>   | <span data-ttu-id="2e3bd-1705">+0</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1705">+0</span></span>  | <span data-ttu-id="2e3bd-1706">-0</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1706">-0</span></span>  | <span data-ttu-id="2e3bd-1707">NaN</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1707">NaN</span></span>  | <span data-ttu-id="2e3bd-1708">NaN</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1708">NaN</span></span>  | <span data-ttu-id="2e3bd-1709">NaN</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1709">NaN</span></span> | 
   | <span data-ttu-id="2e3bd-1710">-0</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1710">-0</span></span>   | <span data-ttu-id="2e3bd-1711">-0</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1711">-0</span></span>   | <span data-ttu-id="2e3bd-1712">+0</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1712">+0</span></span>   | <span data-ttu-id="2e3bd-1713">-0</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1713">-0</span></span>  | <span data-ttu-id="2e3bd-1714">+0</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1714">+0</span></span>  | <span data-ttu-id="2e3bd-1715">NaN</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1715">NaN</span></span>  | <span data-ttu-id="2e3bd-1716">NaN</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1716">NaN</span></span>  | <span data-ttu-id="2e3bd-1717">NaN</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1717">NaN</span></span> | 
   | <span data-ttu-id="2e3bd-1718">+inf</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1718">+inf</span></span> | <span data-ttu-id="2e3bd-1719">+inf</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1719">+inf</span></span> | <span data-ttu-id="2e3bd-1720">-inf</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1720">-inf</span></span> | <span data-ttu-id="2e3bd-1721">NaN</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1721">NaN</span></span> | <span data-ttu-id="2e3bd-1722">NaN</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1722">NaN</span></span> | <span data-ttu-id="2e3bd-1723">+inf</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1723">+inf</span></span> | <span data-ttu-id="2e3bd-1724">-inf</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1724">-inf</span></span> | <span data-ttu-id="2e3bd-1725">NaN</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1725">NaN</span></span> | 
   | <span data-ttu-id="2e3bd-1726">-inf</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1726">-inf</span></span> | <span data-ttu-id="2e3bd-1727">-inf</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1727">-inf</span></span> | <span data-ttu-id="2e3bd-1728">+inf</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1728">+inf</span></span> | <span data-ttu-id="2e3bd-1729">NaN</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1729">NaN</span></span> | <span data-ttu-id="2e3bd-1730">NaN</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1730">NaN</span></span> | <span data-ttu-id="2e3bd-1731">-inf</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1731">-inf</span></span> | <span data-ttu-id="2e3bd-1732">+inf</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1732">+inf</span></span> | <span data-ttu-id="2e3bd-1733">NaN</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1733">NaN</span></span> | 
   | <span data-ttu-id="2e3bd-1734">NaN</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1734">NaN</span></span>  | <span data-ttu-id="2e3bd-1735">NaN</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1735">NaN</span></span>  | <span data-ttu-id="2e3bd-1736">NaN</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1736">NaN</span></span>  | <span data-ttu-id="2e3bd-1737">NaN</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1737">NaN</span></span> | <span data-ttu-id="2e3bd-1738">NaN</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1738">NaN</span></span> | <span data-ttu-id="2e3bd-1739">NaN</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1739">NaN</span></span>  | <span data-ttu-id="2e3bd-1740">NaN</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1740">NaN</span></span>  | <span data-ttu-id="2e3bd-1741">NaN</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1741">NaN</span></span> | 

*  <span data-ttu-id="2e3bd-1742">小数乘法：</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1742">Decimal multiplication:</span></span>

   ```csharp
   decimal operator *(decimal x, decimal y);
   ```

   <span data-ttu-id="2e3bd-1743">如果生成的值太大而无法以格式表示 `decimal` ， `System.OverflowException` 则会引发。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1743">If the resulting value is too large to represent in the `decimal` format, a `System.OverflowException` is thrown.</span></span> <span data-ttu-id="2e3bd-1744">如果结果值太小而无法以 `decimal` 格式表示，则结果为零。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1744">If the result value is too small to represent in the `decimal` format, the result is zero.</span></span> <span data-ttu-id="2e3bd-1745">在进行任何舍入之前，结果的小数位数是两个操作数的刻度之和。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1745">The scale of the result, before any rounding, is the sum of the scales of the two operands.</span></span>

   <span data-ttu-id="2e3bd-1746">Decimal 乘法等效于使用类型的乘法运算符 `System.Decimal` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1746">Decimal multiplication is equivalent to using the multiplication operator of type `System.Decimal`.</span></span>


### <a name="division-operator"></a><span data-ttu-id="2e3bd-1747">除法运算符</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1747">Division operator</span></span>

<span data-ttu-id="2e3bd-1748">对于窗体操作 `x / y` ，二元运算符重载决策 ([二元运算符重载决策](expressions.md#binary-operator-overload-resolution) ，) 应用于选择特定的运算符实现。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1748">For an operation of the form `x / y`, binary operator overload resolution ([Binary operator overload resolution](expressions.md#binary-operator-overload-resolution)) is applied to select a specific operator implementation.</span></span> <span data-ttu-id="2e3bd-1749">操作数将转换为所选运算符的参数类型，结果的类型就是运算符的返回类型。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1749">The operands are converted to the parameter types of the selected operator, and the type of the result is the return type of the operator.</span></span>

<span data-ttu-id="2e3bd-1750">下面列出了预定义的除法运算符。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1750">The predefined division operators are listed below.</span></span> <span data-ttu-id="2e3bd-1751">运算符都计算和的商 `x` `y` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1751">The operators all compute the quotient of `x` and `y`.</span></span>

*  <span data-ttu-id="2e3bd-1752">整数相除：</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1752">Integer division:</span></span>

   ```csharp
   int operator /(int x, int y);
   uint operator /(uint x, uint y);
   long operator /(long x, long y);
   ulong operator /(ulong x, ulong y);
   ```

   <span data-ttu-id="2e3bd-1753">如果右操作数的值为零， `System.DivideByZeroException` 则将引发。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1753">If the value of the right operand is zero, a `System.DivideByZeroException` is thrown.</span></span>

   <span data-ttu-id="2e3bd-1754">相除将结果舍入到零。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1754">The division rounds the result towards zero.</span></span> <span data-ttu-id="2e3bd-1755">因此，结果的绝对值是小于或等于两个操作数的商的绝对值的最大整数。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1755">Thus the absolute value of the result is the largest possible integer that is less than or equal to the absolute value of the quotient of the two operands.</span></span> <span data-ttu-id="2e3bd-1756">如果两个操作数具有相同的符号，则结果为零或正; 如果两个操作数具有相反的符号，则结果为零或负。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1756">The result is zero or positive when the two operands have the same sign and zero or negative when the two operands have opposite signs.</span></span>

   <span data-ttu-id="2e3bd-1757">如果左操作数是最小的可 `int` `long` 表示或值并且右操作数为 `-1` ，则发生溢出。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1757">If the left operand is the smallest representable `int` or `long` value and the right operand is `-1`, an overflow occurs.</span></span> <span data-ttu-id="2e3bd-1758">在 `checked` 上下文中，这将导致 `System.ArithmeticException` 引发)  (或子类。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1758">In a `checked` context, this causes a `System.ArithmeticException` (or a subclass thereof) to be thrown.</span></span> <span data-ttu-id="2e3bd-1759">在 `unchecked` 上下文中，它的实现定义为：是否引发了 `System.ArithmeticException`) 的 (或子类，或溢出与左操作数的结果值无报告。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1759">In an `unchecked` context, it is implementation-defined as to whether a `System.ArithmeticException` (or a subclass thereof) is thrown or the overflow goes unreported with the resulting value being that of the left operand.</span></span>

*  <span data-ttu-id="2e3bd-1760">浮点除法：</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1760">Floating-point division:</span></span>

   ```csharp
   float operator /(float x, float y);
   double operator /(double x, double y);
   ```

   <span data-ttu-id="2e3bd-1761">根据 IEEE 754 算法的规则计算商。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1761">The quotient is computed according to the rules of IEEE 754 arithmetic.</span></span> <span data-ttu-id="2e3bd-1762">下表列出了非零有限值、零、无限值和 NaN 的所有可能组合的结果。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1762">The following table lists the results of all possible combinations of nonzero finite values, zeros, infinities, and NaN's.</span></span> <span data-ttu-id="2e3bd-1763">在表中，`x` 和 `y` 为正有限值。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1763">In the table, `x` and `y` are positive finite values.</span></span> <span data-ttu-id="2e3bd-1764">`x / y` 的结果为 `z`。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1764">`z` is the result of `x / y`.</span></span> <span data-ttu-id="2e3bd-1765">如果结果相对于目标类型而言太大，则 `z` 为无穷值。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1765">If the result is too large for the destination type, `z` is infinity.</span></span> <span data-ttu-id="2e3bd-1766">如果结果相对于目标类型而言太小，则 `z` 为零。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1766">If the result is too small for the destination type, `z` is zero.</span></span>

   |      |      |      |      |      |      |      |      |
   |:----:|:----:|:----:|:----:|:----:|:----:|:----:|:----:|
   |      | <span data-ttu-id="2e3bd-1767">+y</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1767">+y</span></span>   | <span data-ttu-id="2e3bd-1768">-y</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1768">-y</span></span>   | <span data-ttu-id="2e3bd-1769">+0</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1769">+0</span></span>   | <span data-ttu-id="2e3bd-1770">-0</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1770">-0</span></span>   | <span data-ttu-id="2e3bd-1771">+inf</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1771">+inf</span></span> | <span data-ttu-id="2e3bd-1772">-inf</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1772">-inf</span></span> | <span data-ttu-id="2e3bd-1773">NaN</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1773">NaN</span></span>  | 
   | <span data-ttu-id="2e3bd-1774">+x</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1774">+x</span></span>   | <span data-ttu-id="2e3bd-1775">+z</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1775">+z</span></span>   | <span data-ttu-id="2e3bd-1776">-Z</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1776">-z</span></span>   | <span data-ttu-id="2e3bd-1777">+inf</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1777">+inf</span></span> | <span data-ttu-id="2e3bd-1778">-inf</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1778">-inf</span></span> | <span data-ttu-id="2e3bd-1779">+0</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1779">+0</span></span>   | <span data-ttu-id="2e3bd-1780">-0</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1780">-0</span></span>   | <span data-ttu-id="2e3bd-1781">NaN</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1781">NaN</span></span>  | 
   | <span data-ttu-id="2e3bd-1782">-X</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1782">-x</span></span>   | <span data-ttu-id="2e3bd-1783">-Z</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1783">-z</span></span>   | <span data-ttu-id="2e3bd-1784">+z</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1784">+z</span></span>   | <span data-ttu-id="2e3bd-1785">-inf</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1785">-inf</span></span> | <span data-ttu-id="2e3bd-1786">+inf</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1786">+inf</span></span> | <span data-ttu-id="2e3bd-1787">-0</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1787">-0</span></span>   | <span data-ttu-id="2e3bd-1788">+0</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1788">+0</span></span>   | <span data-ttu-id="2e3bd-1789">NaN</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1789">NaN</span></span>  | 
   | <span data-ttu-id="2e3bd-1790">+0</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1790">+0</span></span>   | <span data-ttu-id="2e3bd-1791">+0</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1791">+0</span></span>   | <span data-ttu-id="2e3bd-1792">-0</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1792">-0</span></span>   | <span data-ttu-id="2e3bd-1793">NaN</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1793">NaN</span></span>  | <span data-ttu-id="2e3bd-1794">NaN</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1794">NaN</span></span>  | <span data-ttu-id="2e3bd-1795">+0</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1795">+0</span></span>   | <span data-ttu-id="2e3bd-1796">-0</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1796">-0</span></span>   | <span data-ttu-id="2e3bd-1797">NaN</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1797">NaN</span></span>  | 
   | <span data-ttu-id="2e3bd-1798">-0</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1798">-0</span></span>   | <span data-ttu-id="2e3bd-1799">-0</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1799">-0</span></span>   | <span data-ttu-id="2e3bd-1800">+0</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1800">+0</span></span>   | <span data-ttu-id="2e3bd-1801">NaN</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1801">NaN</span></span>  | <span data-ttu-id="2e3bd-1802">NaN</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1802">NaN</span></span>  | <span data-ttu-id="2e3bd-1803">-0</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1803">-0</span></span>   | <span data-ttu-id="2e3bd-1804">+0</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1804">+0</span></span>   | <span data-ttu-id="2e3bd-1805">NaN</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1805">NaN</span></span>  | 
   | <span data-ttu-id="2e3bd-1806">+inf</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1806">+inf</span></span> | <span data-ttu-id="2e3bd-1807">+inf</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1807">+inf</span></span> | <span data-ttu-id="2e3bd-1808">-inf</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1808">-inf</span></span> | <span data-ttu-id="2e3bd-1809">+inf</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1809">+inf</span></span> | <span data-ttu-id="2e3bd-1810">-inf</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1810">-inf</span></span> | <span data-ttu-id="2e3bd-1811">NaN</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1811">NaN</span></span>  | <span data-ttu-id="2e3bd-1812">NaN</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1812">NaN</span></span>  | <span data-ttu-id="2e3bd-1813">NaN</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1813">NaN</span></span>  | 
   | <span data-ttu-id="2e3bd-1814">-inf</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1814">-inf</span></span> | <span data-ttu-id="2e3bd-1815">-inf</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1815">-inf</span></span> | <span data-ttu-id="2e3bd-1816">+inf</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1816">+inf</span></span> | <span data-ttu-id="2e3bd-1817">-inf</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1817">-inf</span></span> | <span data-ttu-id="2e3bd-1818">+inf</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1818">+inf</span></span> | <span data-ttu-id="2e3bd-1819">NaN</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1819">NaN</span></span>  | <span data-ttu-id="2e3bd-1820">NaN</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1820">NaN</span></span>  | <span data-ttu-id="2e3bd-1821">NaN</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1821">NaN</span></span>  | 
   | <span data-ttu-id="2e3bd-1822">NaN</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1822">NaN</span></span>  | <span data-ttu-id="2e3bd-1823">NaN</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1823">NaN</span></span>  | <span data-ttu-id="2e3bd-1824">NaN</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1824">NaN</span></span>  | <span data-ttu-id="2e3bd-1825">NaN</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1825">NaN</span></span>  | <span data-ttu-id="2e3bd-1826">NaN</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1826">NaN</span></span>  | <span data-ttu-id="2e3bd-1827">NaN</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1827">NaN</span></span>  | <span data-ttu-id="2e3bd-1828">NaN</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1828">NaN</span></span>  | <span data-ttu-id="2e3bd-1829">NaN</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1829">NaN</span></span>  | 

*  <span data-ttu-id="2e3bd-1830">小数部分：</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1830">Decimal division:</span></span>

   ```csharp
   decimal operator /(decimal x, decimal y);
   ```

   <span data-ttu-id="2e3bd-1831">如果右操作数的值为零， `System.DivideByZeroException` 则将引发。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1831">If the value of the right operand is zero, a `System.DivideByZeroException` is thrown.</span></span> <span data-ttu-id="2e3bd-1832">如果生成的值太大而无法以格式表示 `decimal` ， `System.OverflowException` 则会引发。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1832">If the resulting value is too large to represent in the `decimal` format, a `System.OverflowException` is thrown.</span></span> <span data-ttu-id="2e3bd-1833">如果结果值太小而无法以 `decimal` 格式表示，则结果为零。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1833">If the result value is too small to represent in the `decimal` format, the result is zero.</span></span> <span data-ttu-id="2e3bd-1834">结果的小数位数是最小的小数位数，将结果等于最接近的可表示的十进制值到真正的数学结果。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1834">The scale of the result is the smallest scale that will preserve a result equal to the nearest representable decimal value to the true mathematical result.</span></span>

   <span data-ttu-id="2e3bd-1835">小数部分等效于使用类型的除法运算符 `System.Decimal` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1835">Decimal division is equivalent to using the division operator of type `System.Decimal`.</span></span>


### <a name="remainder-operator"></a><span data-ttu-id="2e3bd-1836">余数运算符</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1836">Remainder operator</span></span>

<span data-ttu-id="2e3bd-1837">对于窗体操作 `x % y` ，二元运算符重载决策 ([二元运算符重载决策](expressions.md#binary-operator-overload-resolution) ，) 应用于选择特定的运算符实现。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1837">For an operation of the form `x % y`, binary operator overload resolution ([Binary operator overload resolution](expressions.md#binary-operator-overload-resolution)) is applied to select a specific operator implementation.</span></span> <span data-ttu-id="2e3bd-1838">操作数将转换为所选运算符的参数类型，结果的类型就是运算符的返回类型。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1838">The operands are converted to the parameter types of the selected operator, and the type of the result is the return type of the operator.</span></span>

<span data-ttu-id="2e3bd-1839">下面列出了预定义的余数运算符。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1839">The predefined remainder operators are listed below.</span></span> <span data-ttu-id="2e3bd-1840">运算符都计算和之间相除的余数 `x` `y` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1840">The operators all compute the remainder of the division between `x` and `y`.</span></span>

*  <span data-ttu-id="2e3bd-1841">整数余数：</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1841">Integer remainder:</span></span>

   ```csharp
   int operator %(int x, int y);
   uint operator %(uint x, uint y);
   long operator %(long x, long y);
   ulong operator %(ulong x, ulong y);
   ```

   <span data-ttu-id="2e3bd-1842">的结果 `x % y` 是生成的值 `x - (x / y) * y` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1842">The result of `x % y` is the value produced by `x - (x / y) * y`.</span></span> <span data-ttu-id="2e3bd-1843">如果 `y` 为零，将 `System.DivideByZeroException` 引发。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1843">If `y` is zero, a `System.DivideByZeroException` is thrown.</span></span>

   <span data-ttu-id="2e3bd-1844">如果左操作数是最小 `int` 值或 `long` 值并且右操作数为 `-1` ，则 `System.OverflowException` 引发。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1844">If the left operand is the smallest `int` or `long` value and the right operand is `-1`, a `System.OverflowException` is thrown.</span></span> <span data-ttu-id="2e3bd-1845">在任何情况下，都不会引发异常 `x % y` ， `x / y` 而不会引发异常。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1845">In no case does `x % y` throw an exception where `x / y` would not throw an exception.</span></span>

*  <span data-ttu-id="2e3bd-1846">浮点余数：</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1846">Floating-point remainder:</span></span>

   ```csharp
   float operator %(float x, float y);
   double operator %(double x, double y);
   ```

   <span data-ttu-id="2e3bd-1847">下表列出了非零有限值、零、无限值和 NaN 的所有可能组合的结果。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1847">The following table lists the results of all possible combinations of nonzero finite values, zeros, infinities, and NaN's.</span></span> <span data-ttu-id="2e3bd-1848">在表中，`x` 和 `y` 为正有限值。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1848">In the table, `x` and `y` are positive finite values.</span></span> <span data-ttu-id="2e3bd-1849">`z` 和的结果 `x % y` 计算为 `x - n * y` ，其中 `n` 是小于或等于的最大整数 `x / y` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1849">`z` is the result of `x % y` and is computed as `x - n * y`, where `n` is the largest possible integer that is less than or equal to `x / y`.</span></span> <span data-ttu-id="2e3bd-1850">计算余数的这种方法类似于用于整数操作数，但不同于 IEEE 754 定义 (，这 `n` 是最接近) 的整数 `x / y` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1850">This method of computing the remainder is analogous to that used for integer operands, but differs from the IEEE 754 definition (in which `n` is the integer closest to `x / y`).</span></span>

   |      |      |      |      |      |      |      |      |
   |:----:|:----:|:----:|:----:|:----:|:----:|:----:|:----:|
   |      | <span data-ttu-id="2e3bd-1851">+y</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1851">+y</span></span>   | <span data-ttu-id="2e3bd-1852">-y</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1852">-y</span></span>   | <span data-ttu-id="2e3bd-1853">+0</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1853">+0</span></span>   | <span data-ttu-id="2e3bd-1854">-0</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1854">-0</span></span>   | <span data-ttu-id="2e3bd-1855">+inf</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1855">+inf</span></span> | <span data-ttu-id="2e3bd-1856">-inf</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1856">-inf</span></span> | <span data-ttu-id="2e3bd-1857">NaN</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1857">NaN</span></span>  | 
   | <span data-ttu-id="2e3bd-1858">+x</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1858">+x</span></span>   | <span data-ttu-id="2e3bd-1859">+z</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1859">+z</span></span>   | <span data-ttu-id="2e3bd-1860">+z</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1860">+z</span></span>   | <span data-ttu-id="2e3bd-1861">NaN</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1861">NaN</span></span>  | <span data-ttu-id="2e3bd-1862">NaN</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1862">NaN</span></span>  | <span data-ttu-id="2e3bd-1863">x</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1863">x</span></span>    | <span data-ttu-id="2e3bd-1864">x</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1864">x</span></span>    | <span data-ttu-id="2e3bd-1865">NaN</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1865">NaN</span></span>  | 
   | <span data-ttu-id="2e3bd-1866">-X</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1866">-x</span></span>   | <span data-ttu-id="2e3bd-1867">-Z</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1867">-z</span></span>   | <span data-ttu-id="2e3bd-1868">-Z</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1868">-z</span></span>   | <span data-ttu-id="2e3bd-1869">NaN</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1869">NaN</span></span>  | <span data-ttu-id="2e3bd-1870">NaN</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1870">NaN</span></span>  | <span data-ttu-id="2e3bd-1871">-X</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1871">-x</span></span>   | <span data-ttu-id="2e3bd-1872">-X</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1872">-x</span></span>   | <span data-ttu-id="2e3bd-1873">NaN</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1873">NaN</span></span>  | 
   | <span data-ttu-id="2e3bd-1874">+0</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1874">+0</span></span>   | <span data-ttu-id="2e3bd-1875">+0</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1875">+0</span></span>   | <span data-ttu-id="2e3bd-1876">+0</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1876">+0</span></span>   | <span data-ttu-id="2e3bd-1877">NaN</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1877">NaN</span></span>  | <span data-ttu-id="2e3bd-1878">NaN</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1878">NaN</span></span>  | <span data-ttu-id="2e3bd-1879">+0</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1879">+0</span></span>   | <span data-ttu-id="2e3bd-1880">+0</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1880">+0</span></span>   | <span data-ttu-id="2e3bd-1881">NaN</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1881">NaN</span></span>  | 
   | <span data-ttu-id="2e3bd-1882">-0</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1882">-0</span></span>   | <span data-ttu-id="2e3bd-1883">-0</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1883">-0</span></span>   | <span data-ttu-id="2e3bd-1884">-0</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1884">-0</span></span>   | <span data-ttu-id="2e3bd-1885">NaN</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1885">NaN</span></span>  | <span data-ttu-id="2e3bd-1886">NaN</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1886">NaN</span></span>  | <span data-ttu-id="2e3bd-1887">-0</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1887">-0</span></span>   | <span data-ttu-id="2e3bd-1888">-0</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1888">-0</span></span>   | <span data-ttu-id="2e3bd-1889">NaN</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1889">NaN</span></span>  | 
   | <span data-ttu-id="2e3bd-1890">+inf</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1890">+inf</span></span> | <span data-ttu-id="2e3bd-1891">NaN</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1891">NaN</span></span>  | <span data-ttu-id="2e3bd-1892">NaN</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1892">NaN</span></span>  | <span data-ttu-id="2e3bd-1893">NaN</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1893">NaN</span></span>  | <span data-ttu-id="2e3bd-1894">NaN</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1894">NaN</span></span>  | <span data-ttu-id="2e3bd-1895">NaN</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1895">NaN</span></span>  | <span data-ttu-id="2e3bd-1896">NaN</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1896">NaN</span></span>  | <span data-ttu-id="2e3bd-1897">NaN</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1897">NaN</span></span>  | 
   | <span data-ttu-id="2e3bd-1898">-inf</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1898">-inf</span></span> | <span data-ttu-id="2e3bd-1899">NaN</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1899">NaN</span></span>  | <span data-ttu-id="2e3bd-1900">NaN</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1900">NaN</span></span>  | <span data-ttu-id="2e3bd-1901">NaN</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1901">NaN</span></span>  | <span data-ttu-id="2e3bd-1902">NaN</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1902">NaN</span></span>  | <span data-ttu-id="2e3bd-1903">NaN</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1903">NaN</span></span>  | <span data-ttu-id="2e3bd-1904">NaN</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1904">NaN</span></span>  | <span data-ttu-id="2e3bd-1905">NaN</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1905">NaN</span></span>  | 
   | <span data-ttu-id="2e3bd-1906">NaN</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1906">NaN</span></span>  | <span data-ttu-id="2e3bd-1907">NaN</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1907">NaN</span></span>  | <span data-ttu-id="2e3bd-1908">NaN</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1908">NaN</span></span>  | <span data-ttu-id="2e3bd-1909">NaN</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1909">NaN</span></span>  | <span data-ttu-id="2e3bd-1910">NaN</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1910">NaN</span></span>  | <span data-ttu-id="2e3bd-1911">NaN</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1911">NaN</span></span>  | <span data-ttu-id="2e3bd-1912">NaN</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1912">NaN</span></span>  | <span data-ttu-id="2e3bd-1913">NaN</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1913">NaN</span></span>  | 

*  <span data-ttu-id="2e3bd-1914">小数余数：</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1914">Decimal remainder:</span></span>

   ```csharp
   decimal operator %(decimal x, decimal y);
   ```

   <span data-ttu-id="2e3bd-1915">如果右操作数的值为零， `System.DivideByZeroException` 则将引发。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1915">If the value of the right operand is zero, a `System.DivideByZeroException` is thrown.</span></span> <span data-ttu-id="2e3bd-1916">在进行舍入之前，结果的小数位数大于两个操作数的刻度，如果非零，则结果的符号与相同 `x` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1916">The scale of the result, before any rounding, is the larger of the scales of the two operands, and the sign of the result, if non-zero, is the same as that of `x`.</span></span>

   <span data-ttu-id="2e3bd-1917">小数余数等效于使用类型的余数运算符 `System.Decimal` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1917">Decimal remainder is equivalent to using the remainder operator of type `System.Decimal`.</span></span>


### <a name="addition-operator"></a><span data-ttu-id="2e3bd-1918">加法运算符</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1918">Addition operator</span></span>

<span data-ttu-id="2e3bd-1919">对于窗体操作 `x + y` ，二元运算符重载决策 ([二元运算符重载决策](expressions.md#binary-operator-overload-resolution) ，) 应用于选择特定的运算符实现。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1919">For an operation of the form `x + y`, binary operator overload resolution ([Binary operator overload resolution](expressions.md#binary-operator-overload-resolution)) is applied to select a specific operator implementation.</span></span> <span data-ttu-id="2e3bd-1920">操作数将转换为所选运算符的参数类型，结果的类型就是运算符的返回类型。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1920">The operands are converted to the parameter types of the selected operator, and the type of the result is the return type of the operator.</span></span>

<span data-ttu-id="2e3bd-1921">下面列出了预定义的加法运算符。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1921">The predefined addition operators are listed below.</span></span> <span data-ttu-id="2e3bd-1922">对于数值类型和枚举类型，预定义的加法运算符计算两个操作数之和。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1922">For numeric and enumeration types, the predefined addition operators compute the sum of the two operands.</span></span> <span data-ttu-id="2e3bd-1923">当一个或两个操作数为字符串类型时，预定义的加法运算符将连接操作数的字符串表示形式。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1923">When one or both operands are of type string, the predefined addition operators concatenate the string representation of the operands.</span></span>

*  <span data-ttu-id="2e3bd-1924">整数加法：</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1924">Integer addition:</span></span>

   ```csharp
   int operator +(int x, int y);
   uint operator +(uint x, uint y);
   long operator +(long x, long y);
   ulong operator +(ulong x, ulong y);
   ```

   <span data-ttu-id="2e3bd-1925">在 `checked` 上下文中，如果求和超出了结果类型的范围， `System.OverflowException` 则会引发。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1925">In a `checked` context, if the sum is outside the range of the result type, a `System.OverflowException` is thrown.</span></span> <span data-ttu-id="2e3bd-1926">在 `unchecked` 上下文中，不会报告溢出，并且放弃结果类型范围外的任何重要高序位。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1926">In an `unchecked` context, overflows are not reported and any significant high-order bits outside the range of the result type are discarded.</span></span>

*  <span data-ttu-id="2e3bd-1927">浮点加法：</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1927">Floating-point addition:</span></span>

   ```csharp
   float operator +(float x, float y);
   double operator +(double x, double y);
   ```

   <span data-ttu-id="2e3bd-1928">Sum 根据 IEEE 754 算法的规则进行计算。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1928">The sum is computed according to the rules of IEEE 754 arithmetic.</span></span> <span data-ttu-id="2e3bd-1929">下表列出了非零有限值、零、无限值和 NaN 的所有可能组合的结果。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1929">The following table lists the results of all possible combinations of nonzero finite values, zeros, infinities, and NaN's.</span></span> <span data-ttu-id="2e3bd-1930">在表中，`x` 和 `y` 是非零有限值，`z` 是 `x + y` 的结果。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1930">In the table, `x` and `y` are nonzero finite values, and `z` is the result of `x + y`.</span></span> <span data-ttu-id="2e3bd-1931">如果 `x` 和 `y` 度量值相同但异号，则 `z` 为正零。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1931">If `x` and `y` have the same magnitude but opposite signs, `z` is positive zero.</span></span> <span data-ttu-id="2e3bd-1932">如果 `x + y` 太大而无法在目标类型中表示， `z` 则是一个无穷大，其符号与相同 `x + y` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1932">If `x + y` is too large to represent in the destination type, `z` is an infinity with the same sign as `x + y`.</span></span>

   |      |      |      |      |      |      |      |
   |:----:|:----:|:----:|:----:|:----:|:----:|:----:|
   |      | <span data-ttu-id="2e3bd-1933">y</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1933">y</span></span>    | <span data-ttu-id="2e3bd-1934">+0</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1934">+0</span></span>   | <span data-ttu-id="2e3bd-1935">-0</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1935">-0</span></span>   | <span data-ttu-id="2e3bd-1936">+inf</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1936">+inf</span></span> | <span data-ttu-id="2e3bd-1937">-inf</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1937">-inf</span></span> | <span data-ttu-id="2e3bd-1938">NaN</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1938">NaN</span></span>  | 
   | <span data-ttu-id="2e3bd-1939">x</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1939">x</span></span>    | <span data-ttu-id="2e3bd-1940">z</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1940">z</span></span>    | <span data-ttu-id="2e3bd-1941">x</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1941">x</span></span>    | <span data-ttu-id="2e3bd-1942">x</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1942">x</span></span>    | <span data-ttu-id="2e3bd-1943">+inf</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1943">+inf</span></span> | <span data-ttu-id="2e3bd-1944">-inf</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1944">-inf</span></span> | <span data-ttu-id="2e3bd-1945">NaN</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1945">NaN</span></span>  | 
   | <span data-ttu-id="2e3bd-1946">+0</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1946">+0</span></span>   | <span data-ttu-id="2e3bd-1947">y</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1947">y</span></span>    | <span data-ttu-id="2e3bd-1948">+0</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1948">+0</span></span>   | <span data-ttu-id="2e3bd-1949">+0</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1949">+0</span></span>   | <span data-ttu-id="2e3bd-1950">+inf</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1950">+inf</span></span> | <span data-ttu-id="2e3bd-1951">-inf</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1951">-inf</span></span> | <span data-ttu-id="2e3bd-1952">NaN</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1952">NaN</span></span>  | 
   | <span data-ttu-id="2e3bd-1953">-0</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1953">-0</span></span>   | <span data-ttu-id="2e3bd-1954">y</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1954">y</span></span>    | <span data-ttu-id="2e3bd-1955">+0</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1955">+0</span></span>   | <span data-ttu-id="2e3bd-1956">-0</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1956">-0</span></span>   | <span data-ttu-id="2e3bd-1957">+inf</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1957">+inf</span></span> | <span data-ttu-id="2e3bd-1958">-inf</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1958">-inf</span></span> | <span data-ttu-id="2e3bd-1959">NaN</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1959">NaN</span></span>  | 
   | <span data-ttu-id="2e3bd-1960">+inf</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1960">+inf</span></span> | <span data-ttu-id="2e3bd-1961">+inf</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1961">+inf</span></span> | <span data-ttu-id="2e3bd-1962">+inf</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1962">+inf</span></span> | <span data-ttu-id="2e3bd-1963">+inf</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1963">+inf</span></span> | <span data-ttu-id="2e3bd-1964">+inf</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1964">+inf</span></span> | <span data-ttu-id="2e3bd-1965">NaN</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1965">NaN</span></span>  | <span data-ttu-id="2e3bd-1966">NaN</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1966">NaN</span></span>  | 
   | <span data-ttu-id="2e3bd-1967">-inf</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1967">-inf</span></span> | <span data-ttu-id="2e3bd-1968">-inf</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1968">-inf</span></span> | <span data-ttu-id="2e3bd-1969">-inf</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1969">-inf</span></span> | <span data-ttu-id="2e3bd-1970">-inf</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1970">-inf</span></span> | <span data-ttu-id="2e3bd-1971">NaN</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1971">NaN</span></span>  | <span data-ttu-id="2e3bd-1972">-inf</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1972">-inf</span></span> | <span data-ttu-id="2e3bd-1973">NaN</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1973">NaN</span></span>  | 
   | <span data-ttu-id="2e3bd-1974">NaN</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1974">NaN</span></span>  | <span data-ttu-id="2e3bd-1975">NaN</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1975">NaN</span></span>  | <span data-ttu-id="2e3bd-1976">NaN</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1976">NaN</span></span>  | <span data-ttu-id="2e3bd-1977">NaN</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1977">NaN</span></span>  | <span data-ttu-id="2e3bd-1978">NaN</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1978">NaN</span></span>  | <span data-ttu-id="2e3bd-1979">NaN</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1979">NaN</span></span>  | <span data-ttu-id="2e3bd-1980">NaN</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1980">NaN</span></span>  | 

*  <span data-ttu-id="2e3bd-1981">小数加：</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1981">Decimal addition:</span></span>

   ```csharp
   decimal operator +(decimal x, decimal y);
   ```

   <span data-ttu-id="2e3bd-1982">如果生成的值太大而无法以格式表示 `decimal` ， `System.OverflowException` 则会引发。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1982">If the resulting value is too large to represent in the `decimal` format, a `System.OverflowException` is thrown.</span></span> <span data-ttu-id="2e3bd-1983">在进行舍入之前，结果的小数位数是两个操作数的刻度中较大的一个。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1983">The scale of the result, before any rounding, is the larger of the scales of the two operands.</span></span>

   <span data-ttu-id="2e3bd-1984">Decimal 加法等效于使用类型的加法运算符 `System.Decimal` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1984">Decimal addition is equivalent to using the addition operator of type `System.Decimal`.</span></span>

*  <span data-ttu-id="2e3bd-1985">枚举加法。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1985">Enumeration addition.</span></span> <span data-ttu-id="2e3bd-1986">每个枚举类型都隐式提供以下预定义运算符，其中 `E` 是枚举类型， `U` 是的基础类型 `E` ：</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1986">Every enumeration type implicitly provides the following predefined operators, where `E` is the enum type, and `U` is the underlying type of `E`:</span></span>

   ```csharp
   E operator +(E x, U y);
   E operator +(U x, E y);
   ```

   <span data-ttu-id="2e3bd-1987">在运行时，这些运算符的计算结果完全相同 `(E)((U)x + (U)y)` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1987">At run-time these operators are evaluated exactly as `(E)((U)x + (U)y)`.</span></span>

*  <span data-ttu-id="2e3bd-1988">字符串串联：</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1988">String concatenation:</span></span>

   ```csharp
   string operator +(string x, string y);
   string operator +(string x, object y);
   string operator +(object x, string y);
   ```

   <span data-ttu-id="2e3bd-1989">二元运算符的这些重载 `+` 执行字符串串联。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1989">These overloads of the binary `+` operator perform string concatenation.</span></span> <span data-ttu-id="2e3bd-1990">如果字符串串联的操作数为 `null` ，则将替换空字符串。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1990">If an operand of string concatenation is `null`, an empty string is substituted.</span></span> <span data-ttu-id="2e3bd-1991">否则，通过调用从类型继承的虚拟方法，将任何非字符串参数转换为其字符串表示形式 `ToString` `object` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1991">Otherwise, any non-string argument is converted to its string representation by invoking the virtual `ToString` method inherited from type `object`.</span></span> <span data-ttu-id="2e3bd-1992">如果 `ToString` 返回 `null` ，则替换空字符串。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1992">If `ToString` returns `null`, an empty string is substituted.</span></span>

   ```csharp
   using System;
   
   class Test
   {
       static void Main() {
           string s = null;
           Console.WriteLine("s = >" + s + "<");        // displays s = ><
           int i = 1;
           Console.WriteLine("i = " + i);               // displays i = 1
           float f = 1.2300E+15F;
           Console.WriteLine("f = " + f);               // displays f = 1.23E+15
           decimal d = 2.900m;
           Console.WriteLine("d = " + d);               // displays d = 2.900
       }
   }
   ```

   字符串串联运算符的结果是一个字符串，该字符串由左操作数的字符后跟右操作数的字符组成。 字符串串联运算符从不返回 `null` 值。 <span data-ttu-id="2e3bd-1995">`System.OutOfMemoryException`如果没有足够的内存可用于分配生成的字符串，可能会引发。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1995">A `System.OutOfMemoryException` may be thrown if there is not enough memory available to allocate the resulting string.</span></span>

*  <span data-ttu-id="2e3bd-1996">委托组合。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1996">Delegate combination.</span></span> <span data-ttu-id="2e3bd-1997">每个委托类型都隐式提供以下预定义运算符，其中 `D` 是委托类型：</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1997">Every delegate type implicitly provides the following predefined operator, where `D` is the delegate type:</span></span>

   ```csharp
   D operator +(D x, D y);
   ```

   <span data-ttu-id="2e3bd-1998">二元 `+` 运算符在两个操作数均为某个委托类型时执行委托组合 `D` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1998">The binary `+` operator performs delegate combination when both operands are of some delegate type `D`.</span></span> <span data-ttu-id="2e3bd-1999"> (如果操作数具有不同的委托类型，则发生绑定时错误 ) 。如果第一个操作数为 `null` ，则操作的结果为第二个操作数的值 (即使还) 也是如此 `null` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-1999">(If the operands have different delegate types, a binding-time error occurs.) If the first operand is `null`, the result of the operation is the value of the second operand (even if that is also `null`).</span></span> <span data-ttu-id="2e3bd-2000">否则，如果第二个操作数为 `null` ，则运算结果为第一个操作数的值。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2000">Otherwise, if the second operand is `null`, then the result of the operation is the value of the first operand.</span></span> <span data-ttu-id="2e3bd-2001">否则，该操作的结果是一个新的委托实例，它在调用时调用第一个操作数，然后调用第二个操作数。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2001">Otherwise, the result of the operation is a new delegate instance that, when invoked, invokes the first operand and then invokes the second operand.</span></span> <span data-ttu-id="2e3bd-2002">有关委托组合的示例，请参阅 [减法运算符](expressions.md#subtraction-operator) 和 [委托调用](delegates.md#delegate-invocation)。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2002">For examples of delegate combination, see [Subtraction operator](expressions.md#subtraction-operator) and [Delegate invocation](delegates.md#delegate-invocation).</span></span> <span data-ttu-id="2e3bd-2003">由于不是 `System.Delegate` 委托类型， `operator`  `+` 因此不会为其定义。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2003">Since `System.Delegate` is not a delegate type, `operator` `+` is not defined for it.</span></span>

### <a name="subtraction-operator"></a><span data-ttu-id="2e3bd-2004">减法运算符</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2004">Subtraction operator</span></span>

<span data-ttu-id="2e3bd-2005">对于窗体操作 `x - y` ，二元运算符重载决策 ([二元运算符重载决策](expressions.md#binary-operator-overload-resolution) ，) 应用于选择特定的运算符实现。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2005">For an operation of the form `x - y`, binary operator overload resolution ([Binary operator overload resolution](expressions.md#binary-operator-overload-resolution)) is applied to select a specific operator implementation.</span></span> <span data-ttu-id="2e3bd-2006">操作数将转换为所选运算符的参数类型，结果的类型就是运算符的返回类型。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2006">The operands are converted to the parameter types of the selected operator, and the type of the result is the return type of the operator.</span></span>

<span data-ttu-id="2e3bd-2007">下面列出了预定义的减法运算符。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2007">The predefined subtraction operators are listed below.</span></span> <span data-ttu-id="2e3bd-2008">运算符均 `y` 从中减去 `x` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2008">The operators all subtract `y` from `x`.</span></span>

*  <span data-ttu-id="2e3bd-2009">整数减法：</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2009">Integer subtraction:</span></span>

   ```csharp
   int operator -(int x, int y);
   uint operator -(uint x, uint y);
   long operator -(long x, long y);
   ulong operator -(ulong x, ulong y);
   ```

   <span data-ttu-id="2e3bd-2010">在 `checked` 上下文中，如果差异超出了结果类型的范围， `System.OverflowException` 则会引发。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2010">In a `checked` context, if the difference is outside the range of the result type, a `System.OverflowException` is thrown.</span></span> <span data-ttu-id="2e3bd-2011">在 `unchecked` 上下文中，不会报告溢出，并且放弃结果类型范围外的任何重要高序位。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2011">In an `unchecked` context, overflows are not reported and any significant high-order bits outside the range of the result type are discarded.</span></span>

*  <span data-ttu-id="2e3bd-2012">浮点减法：</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2012">Floating-point subtraction:</span></span>

   ```csharp
   float operator -(float x, float y);
   double operator -(double x, double y);
   ```

   <span data-ttu-id="2e3bd-2013">根据 IEEE 754 算法的规则计算差异。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2013">The difference is computed according to the rules of IEEE 754 arithmetic.</span></span> <span data-ttu-id="2e3bd-2014">下表列出了非零有限值、零、无穷大和 Nan 的所有可能组合的结果。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2014">The following table lists the results of all possible combinations of nonzero finite values, zeros, infinities, and NaNs.</span></span> <span data-ttu-id="2e3bd-2015">在表中，`x` 和 `y` 是非零有限值，`z` 是 `x - y` 的结果。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2015">In the table, `x` and `y` are nonzero finite values, and `z` is the result of `x - y`.</span></span> <span data-ttu-id="2e3bd-2016">如果 `x` 和 `y` 相等，则 `z` 为正零。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2016">If `x` and `y` are equal, `z` is positive zero.</span></span> <span data-ttu-id="2e3bd-2017">如果 `x - y` 太大而无法在目标类型中表示， `z` 则是一个无穷大，其符号与相同 `x - y` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2017">If `x - y` is too large to represent in the destination type, `z` is an infinity with the same sign as `x - y`.</span></span>

   |      |      |      |      |      |      |     |
   |:----:|:----:|:----:|:----:|:----:|:----:|:---:|
   |      | <span data-ttu-id="2e3bd-2018">y</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2018">y</span></span>    | <span data-ttu-id="2e3bd-2019">+0</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2019">+0</span></span>   | <span data-ttu-id="2e3bd-2020">-0</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2020">-0</span></span>   | <span data-ttu-id="2e3bd-2021">+inf</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2021">+inf</span></span> | <span data-ttu-id="2e3bd-2022">-inf</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2022">-inf</span></span> | <span data-ttu-id="2e3bd-2023">NaN</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2023">NaN</span></span> | 
   | <span data-ttu-id="2e3bd-2024">x</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2024">x</span></span>    | <span data-ttu-id="2e3bd-2025">z</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2025">z</span></span>    | <span data-ttu-id="2e3bd-2026">x</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2026">x</span></span>    | <span data-ttu-id="2e3bd-2027">x</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2027">x</span></span>    | <span data-ttu-id="2e3bd-2028">-inf</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2028">-inf</span></span> | <span data-ttu-id="2e3bd-2029">+inf</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2029">+inf</span></span> | <span data-ttu-id="2e3bd-2030">NaN</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2030">NaN</span></span> | 
   | <span data-ttu-id="2e3bd-2031">+0</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2031">+0</span></span>   | <span data-ttu-id="2e3bd-2032">-y</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2032">-y</span></span>   | <span data-ttu-id="2e3bd-2033">+0</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2033">+0</span></span>   | <span data-ttu-id="2e3bd-2034">+0</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2034">+0</span></span>   | <span data-ttu-id="2e3bd-2035">-inf</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2035">-inf</span></span> | <span data-ttu-id="2e3bd-2036">+inf</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2036">+inf</span></span> | <span data-ttu-id="2e3bd-2037">NaN</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2037">NaN</span></span> | 
   | <span data-ttu-id="2e3bd-2038">-0</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2038">-0</span></span>   | <span data-ttu-id="2e3bd-2039">-y</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2039">-y</span></span>   | <span data-ttu-id="2e3bd-2040">-0</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2040">-0</span></span>   | <span data-ttu-id="2e3bd-2041">+0</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2041">+0</span></span>   | <span data-ttu-id="2e3bd-2042">-inf</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2042">-inf</span></span> | <span data-ttu-id="2e3bd-2043">+inf</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2043">+inf</span></span> | <span data-ttu-id="2e3bd-2044">NaN</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2044">NaN</span></span> | 
   | <span data-ttu-id="2e3bd-2045">+inf</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2045">+inf</span></span> | <span data-ttu-id="2e3bd-2046">+inf</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2046">+inf</span></span> | <span data-ttu-id="2e3bd-2047">+inf</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2047">+inf</span></span> | <span data-ttu-id="2e3bd-2048">+inf</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2048">+inf</span></span> | <span data-ttu-id="2e3bd-2049">NaN</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2049">NaN</span></span>  | <span data-ttu-id="2e3bd-2050">+inf</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2050">+inf</span></span> | <span data-ttu-id="2e3bd-2051">NaN</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2051">NaN</span></span> | 
   | <span data-ttu-id="2e3bd-2052">-inf</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2052">-inf</span></span> | <span data-ttu-id="2e3bd-2053">-inf</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2053">-inf</span></span> | <span data-ttu-id="2e3bd-2054">-inf</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2054">-inf</span></span> | <span data-ttu-id="2e3bd-2055">-inf</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2055">-inf</span></span> | <span data-ttu-id="2e3bd-2056">-inf</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2056">-inf</span></span> | <span data-ttu-id="2e3bd-2057">NaN</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2057">NaN</span></span>  | <span data-ttu-id="2e3bd-2058">NaN</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2058">NaN</span></span> | 
   | <span data-ttu-id="2e3bd-2059">NaN</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2059">NaN</span></span>  | <span data-ttu-id="2e3bd-2060">NaN</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2060">NaN</span></span>  | <span data-ttu-id="2e3bd-2061">NaN</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2061">NaN</span></span>  | <span data-ttu-id="2e3bd-2062">NaN</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2062">NaN</span></span>  | <span data-ttu-id="2e3bd-2063">NaN</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2063">NaN</span></span>  | <span data-ttu-id="2e3bd-2064">NaN</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2064">NaN</span></span>  | <span data-ttu-id="2e3bd-2065">NaN</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2065">NaN</span></span> | 

*  <span data-ttu-id="2e3bd-2066">小数减：</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2066">Decimal subtraction:</span></span>

   ```csharp
   decimal operator -(decimal x, decimal y);
   ```

   <span data-ttu-id="2e3bd-2067">如果生成的值太大而无法以格式表示 `decimal` ， `System.OverflowException` 则会引发。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2067">If the resulting value is too large to represent in the `decimal` format, a `System.OverflowException` is thrown.</span></span> <span data-ttu-id="2e3bd-2068">在进行舍入之前，结果的小数位数是两个操作数的刻度中较大的一个。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2068">The scale of the result, before any rounding, is the larger of the scales of the two operands.</span></span>

   <span data-ttu-id="2e3bd-2069">小数减等效于使用类型的减法运算符 `System.Decimal` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2069">Decimal subtraction is equivalent to using the subtraction operator of type `System.Decimal`.</span></span>

*  <span data-ttu-id="2e3bd-2070">枚举减法。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2070">Enumeration subtraction.</span></span> <span data-ttu-id="2e3bd-2071">每个枚举类型都隐式提供以下预定义运算符，其中 `E` 是枚举类型， `U` 是的基础类型 `E` ：</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2071">Every enumeration type implicitly provides the following predefined operator, where `E` is the enum type, and `U` is the underlying type of `E`:</span></span>

   ```csharp
   U operator -(E x, E y);
   ```

   <span data-ttu-id="2e3bd-2072">此运算符的计算结果完全相同 `(U)((U)x - (U)y)` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2072">This operator is evaluated exactly as `(U)((U)x - (U)y)`.</span></span> <span data-ttu-id="2e3bd-2073">换言之，运算符计算和的序号值之间的差 `x` `y` ，结果的类型为枚举的基础类型。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2073">In other words, the operator computes the difference between the ordinal values of `x` and `y`, and the type of the result is the underlying type of the enumeration.</span></span>

   ```csharp
   E operator -(E x, U y);
   ```

   <span data-ttu-id="2e3bd-2074">此运算符的计算结果完全相同 `(E)((U)x - y)` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2074">This operator is evaluated exactly as `(E)((U)x - y)`.</span></span> <span data-ttu-id="2e3bd-2075">换言之，运算符从枚举的基础类型中减去一个值，从而生成一个枚举值。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2075">In other words, the operator subtracts a value from the underlying type of the enumeration, yielding a value of the enumeration.</span></span>

*  <span data-ttu-id="2e3bd-2076">委托删除。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2076">Delegate removal.</span></span> <span data-ttu-id="2e3bd-2077">每个委托类型都隐式提供以下预定义运算符，其中 `D` 是委托类型：</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2077">Every delegate type implicitly provides the following predefined operator, where `D` is the delegate type:</span></span>

   ```csharp
   D operator -(D x, D y);
   ```

   <span data-ttu-id="2e3bd-2078">`-`当两个操作数均为某个委托类型时，二元运算符执行委托删除 `D` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2078">The binary `-` operator performs delegate removal when both operands are of some delegate type `D`.</span></span> <span data-ttu-id="2e3bd-2079">如果操作数具有不同的委托类型，则会发生绑定时错误。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2079">If the operands have different delegate types, a binding-time error occurs.</span></span> <span data-ttu-id="2e3bd-2080">如果第一个操作数为 `null`，则操作结果为 `null`。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2080">If the first operand is `null`, the result of the operation is `null`.</span></span> <span data-ttu-id="2e3bd-2081">否则，如果第二个操作数为 `null` ，则运算结果为第一个操作数的值。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2081">Otherwise, if the second operand is `null`, then the result of the operation is the value of the first operand.</span></span> <span data-ttu-id="2e3bd-2082">否则，两个操作数都表示) 有一个或多个条目 (的 [委托声明](delegates.md#delegate-declarations) 中的调用列表，结果是一个新的调用列表，其中包含第一个操作数的列表，第二个操作数的列表被删除，前提是第二个操作数的列表是第一个操作数的正确连续子列表。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2082">Otherwise, both operands represent invocation lists ([Delegate declarations](delegates.md#delegate-declarations)) having one or more entries, and the result is a new invocation list consisting of the first operand's list with the second operand's entries removed from it, provided the second operand's list is a proper contiguous sublist of the first's.</span></span>     <span data-ttu-id="2e3bd-2083"> (确定子列表相等性，则会将对应的项与委托相等运算符进行比较， ([委托相等运算符](expressions.md#delegate-equality-operators)) 。 ) 否则，结果为左操作数的值。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2083">(To determine sublist equality, corresponding entries are compared as for the delegate equality operator ([Delegate equality operators](expressions.md#delegate-equality-operators)).) Otherwise, the result is the value of the left operand.</span></span> <span data-ttu-id="2e3bd-2084">进程中的两个操作数都不是更改的。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2084">Neither of the operands' lists is changed in the process.</span></span> <span data-ttu-id="2e3bd-2085">如果第二个操作数的列表与第一个操作数列表中连续条目的多个子列表匹配，则会删除最右侧匹配的子列表。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2085">If the second operand's list matches multiple sublists of contiguous entries in the first operand's list, the right-most matching sublist of contiguous entries is removed.</span></span> <span data-ttu-id="2e3bd-2086">如果删除行为导致出现空列表，则结果为 `null`。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2086">If removal results in an empty list, the result is `null`.</span></span> <span data-ttu-id="2e3bd-2087">例如：</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2087">For example:</span></span>

   ```csharp
   delegate void D(int x);
   
   class C
   {
       public static void M1(int i) { /* ... */ }
       public static void M2(int i) { /* ... */ }
   }

   class Test
   {
       static void Main() { 
           D cd1 = new D(C.M1);
           D cd2 = new D(C.M2);
           D cd3 = cd1 + cd2 + cd2 + cd1;   // M1 + M2 + M2 + M1
           cd3 -= cd1;                      // => M1 + M2 + M2
   
           cd3 = cd1 + cd2 + cd2 + cd1;     // M1 + M2 + M2 + M1
           cd3 -= cd1 + cd2;                // => M2 + M1
   
           cd3 = cd1 + cd2 + cd2 + cd1;     // M1 + M2 + M2 + M1
           cd3 -= cd2 + cd2;                // => M1 + M1
   
           cd3 = cd1 + cd2 + cd2 + cd1;     // M1 + M2 + M2 + M1
           cd3 -= cd2 + cd1;                // => M1 + M2
   
           cd3 = cd1 + cd2 + cd2 + cd1;     // M1 + M2 + M2 + M1
           cd3 -= cd1 + cd1;                // => M1 + M2 + M2 + M1
       }
   }
   ```

## <a name="shift-operators"></a><span data-ttu-id="2e3bd-2088">移位运算符</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2088">Shift operators</span></span>

<span data-ttu-id="2e3bd-2089">`<<`和 `>>` 运算符用于执行移位运算。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2089">The `<<` and `>>` operators are used to perform bit shifting operations.</span></span>

```antlr
shift_expression
    : additive_expression
    | shift_expression '<<' additive_expression
    | shift_expression right_shift additive_expression
    ;
```

<span data-ttu-id="2e3bd-2090">如果 *shift_expression* 的操作数具有编译时类型 `dynamic` ，则表达式 ([动态](expressions.md#dynamic-binding) 绑定) 动态绑定。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2090">If an operand of a *shift_expression* has the compile-time type `dynamic`, then the expression is dynamically bound ([Dynamic binding](expressions.md#dynamic-binding)).</span></span> <span data-ttu-id="2e3bd-2091">在这种情况下，表达式的编译时类型为 `dynamic` ，并使用具有编译时类型的操作数的运行时类型在运行时执行下面所述的解决方案 `dynamic` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2091">In this case the compile-time type of the expression is `dynamic`, and the resolution described below will take place at run-time using the run-time type of those operands that have the compile-time type `dynamic`.</span></span>

<span data-ttu-id="2e3bd-2092">对于窗体或的操作 `x << count` `x >> count` ，二元运算符重载决策 ([二元运算符重载解析](expressions.md#binary-operator-overload-resolution)) 应用于选择特定的运算符实现。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2092">For an operation of the form `x << count` or `x >> count`, binary operator overload resolution ([Binary operator overload resolution](expressions.md#binary-operator-overload-resolution)) is applied to select a specific operator implementation.</span></span> <span data-ttu-id="2e3bd-2093">操作数将转换为所选运算符的参数类型，结果的类型就是运算符的返回类型。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2093">The operands are converted to the parameter types of the selected operator, and the type of the result is the return type of the operator.</span></span>

<span data-ttu-id="2e3bd-2094">在声明重载移位运算符时，第一个操作数的类型必须始终为包含运算符声明的类或结构，并且第二个操作数的类型必须始终为 `int` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2094">When declaring an overloaded shift operator, the type of the first operand must always be the class or struct containing the operator declaration, and the type of the second operand must always be `int`.</span></span>

<span data-ttu-id="2e3bd-2095">下面列出了预定义的移位运算符。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2095">The predefined shift operators are listed below.</span></span>

*  <span data-ttu-id="2e3bd-2096">左移：</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2096">Shift left:</span></span>

   ```csharp
   int operator <<(int x, int count);
   uint operator <<(uint x, int count);
   long operator <<(long x, int count);
   ulong operator <<(ulong x, int count);
   ```

   <span data-ttu-id="2e3bd-2097">`<<`运算符 `x` 向下移位按下面所述计算的位数。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2097">The `<<` operator shifts `x` left by a number of bits computed as described below.</span></span>

   <span data-ttu-id="2e3bd-2098">将丢弃结果类型范围外的高序位 `x` ，剩余位将向左移动，并将低序位空位位置设置为零。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2098">The high-order bits outside the range of the result type of `x` are discarded, the remaining bits are shifted left, and the low-order empty bit positions are set to zero.</span></span>

*  <span data-ttu-id="2e3bd-2099">右移：</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2099">Shift right:</span></span>

   ```csharp
   int operator >>(int x, int count);
   uint operator >>(uint x, int count);
   long operator >>(long x, int count);
   ulong operator >>(ulong x, int count);
   ```

   <span data-ttu-id="2e3bd-2100">`>>`运算符 `x` 向右移位按如下所述计算的位数。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2100">The `>>` operator shifts `x` right by a number of bits computed as described below.</span></span>

   <span data-ttu-id="2e3bd-2101">当 `x` 的类型为 `int` 或时 `long` ，将丢弃的低序位， `x` 其余位将向右移动，如果为非负，则将高顺序空位位置设置为零; 如果为负，则设置 `x` 为 1 `x` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2101">When `x` is of type `int` or `long`, the low-order bits of `x` are discarded, the remaining bits are shifted right, and the high-order empty bit positions are set to zero if `x` is non-negative and set to one if `x` is negative.</span></span>

   <span data-ttu-id="2e3bd-2102">当 `x` 的类型为 `uint` 或时 `ulong` ，将丢弃的低序位， `x` 其余位将向右移动，而高阶空位位置设置为零。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2102">When `x` is of type `uint` or `ulong`, the low-order bits of `x` are discarded, the remaining bits are shifted right, and the high-order empty bit positions are set to zero.</span></span>

<span data-ttu-id="2e3bd-2103">对于预定义的运算符，将计算要移位的位数，如下所示：</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2103">For the predefined operators, the number of bits to shift is computed as follows:</span></span>

*  <span data-ttu-id="2e3bd-2104">当的类型 `x` 为 `int` 或时 `uint` ，移位计数由的低序位5位给定 `count` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2104">When the type of `x` is `int` or `uint`, the shift count is given by the low-order five bits of `count`.</span></span> <span data-ttu-id="2e3bd-2105">换句话说，移位计数是从计算得出的 `count & 0x1F` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2105">In other words, the shift count is computed from `count & 0x1F`.</span></span>
*  <span data-ttu-id="2e3bd-2106">当的类型 `x` 为 `long` 或时 `ulong` ，移位计数由的低序位六位给定 `count` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2106">When the type of `x` is `long` or `ulong`, the shift count is given by the low-order six bits of `count`.</span></span> <span data-ttu-id="2e3bd-2107">换句话说，移位计数是从计算得出的 `count & 0x3F` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2107">In other words, the shift count is computed from `count & 0x3F`.</span></span>

<span data-ttu-id="2e3bd-2108">如果生成的移位计数为零，则移位运算符只返回的值 `x` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2108">If the resulting shift count is zero, the shift operators simply return the value of `x`.</span></span>

<span data-ttu-id="2e3bd-2109">移位运算从不导致溢出并在和上下文中产生相同的结果 `checked` `unchecked` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2109">Shift operations never cause overflows and produce the same results in `checked` and `unchecked` contexts.</span></span>

<span data-ttu-id="2e3bd-2110">当运算符的左操作数 `>>` 为有符号整数类型时，运算符将执行算术右移，其中最高有效位的值 (操作数的符号位) 传播到高顺序空位位置。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2110">When the left operand of the `>>` operator is of a signed integral type, the operator performs an arithmetic shift right wherein the value of the most significant bit (the sign bit) of the operand is propagated to the high-order empty bit positions.</span></span> <span data-ttu-id="2e3bd-2111">当运算符的左操作数 `>>` 是无符号整数类型时，运算符将执行逻辑移位，其中高阶空位位置始终设置为零。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2111">When the left operand of the `>>` operator is of an unsigned integral type, the operator performs a logical shift right wherein high-order empty bit positions are always set to zero.</span></span> <span data-ttu-id="2e3bd-2112">若要执行从操作数类型推断得出的相反运算，可以使用显式强制转换。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2112">To perform the opposite operation of that inferred from the operand type, explicit casts can be used.</span></span> <span data-ttu-id="2e3bd-2113">例如，如果 `x` 是类型为的变量 `int` ，则该操作将 `unchecked((int)((uint)x >> y))` 执行的逻辑移位 `x` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2113">For example, if `x` is a variable of type `int`, the operation `unchecked((int)((uint)x >> y))` performs a logical shift right of `x`.</span></span>

## <a name="relational-and-type-testing-operators"></a><span data-ttu-id="2e3bd-2114">关系和类型测试运算符</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2114">Relational and type-testing operators</span></span>

<span data-ttu-id="2e3bd-2115">`==`、、 `!=` `<` 、 `>` 、 `<=` 、 `>=` `is` 和 `as` 运算符称为关系和类型测试运算符。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2115">The `==`, `!=`, `<`, `>`, `<=`, `>=`, `is` and `as` operators are called the relational and type-testing operators.</span></span>

```antlr
relational_expression
    : shift_expression
    | relational_expression '<' shift_expression
    | relational_expression '>' shift_expression
    | relational_expression '<=' shift_expression
    | relational_expression '>=' shift_expression
    | relational_expression 'is' type
    | relational_expression 'as' type
    ;

equality_expression
    : relational_expression
    | equality_expression '==' relational_expression
    | equality_expression '!=' relational_expression
    ;
```

<span data-ttu-id="2e3bd-2116">`is`运算符在[is 运算符](expressions.md#the-is-operator)中进行了描述， `as` 运算符在[as 运算符](expressions.md#the-as-operator)中进行了描述。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2116">The `is` operator is described in [The is operator](expressions.md#the-is-operator) and the `as` operator is described in [The as operator](expressions.md#the-as-operator).</span></span>

<span data-ttu-id="2e3bd-2117">`==`、、 `!=` `<` 、 `>` `<=` 和 `>=` 运算符是 ***比较运算符***。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2117">The `==`, `!=`, `<`, `>`, `<=` and `>=` operators are ***comparison operators***.</span></span>

<span data-ttu-id="2e3bd-2118">如果比较运算符的操作数具有编译时类型 `dynamic` ，则该表达式将动态绑定 ([动态绑定](expressions.md#dynamic-binding)) 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2118">If an operand of a comparison operator has the compile-time type `dynamic`, then the expression is dynamically bound ([Dynamic binding](expressions.md#dynamic-binding)).</span></span> <span data-ttu-id="2e3bd-2119">在这种情况下，表达式的编译时类型为 `dynamic` ，并使用具有编译时类型的操作数的运行时类型在运行时执行下面所述的解决方案 `dynamic` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2119">In this case the compile-time type of the expression is `dynamic`, and the resolution described below will take place at run-time using the run-time type of those operands that have the compile-time type `dynamic`.</span></span>

<span data-ttu-id="2e3bd-2120">对于 op 格式的操作 `x`  `y` ，其中 *op* 是比较运算符， ([二元运算符重载决策](expressions.md#binary-operator-overload-resolution)的重载决策) 应用于选择特定的运算符实现。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2120">For an operation of the form `x` *op* `y`, where *op* is a comparison operator, overload resolution ([Binary operator overload resolution](expressions.md#binary-operator-overload-resolution)) is applied to select a specific operator implementation.</span></span> <span data-ttu-id="2e3bd-2121">操作数将转换为所选运算符的参数类型，结果的类型就是运算符的返回类型。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2121">The operands are converted to the parameter types of the selected operator, and the type of the result is the return type of the operator.</span></span>

<span data-ttu-id="2e3bd-2122">以下各节介绍了预定义的比较运算符。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2122">The predefined comparison operators are described in the following sections.</span></span> <span data-ttu-id="2e3bd-2123">所有预定义的比较运算符都返回一个类型的结果 `bool` ，如下表所述。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2123">All predefined comparison operators return a result of type `bool`, as described in the following table.</span></span>


| <span data-ttu-id="2e3bd-2124">__操作__</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2124">__Operation__</span></span> | <span data-ttu-id="2e3bd-2125">__结果__</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2125">__Result__</span></span>                                                       |
|---------------|------------------------------------------------------------------|
| `x == y`      | <span data-ttu-id="2e3bd-2126">`true` 如果 `x` 等于，则 `y` 为 `false` ; 否则为</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2126">`true` if `x` is equal to `y`, `false` otherwise</span></span>                 | 
| `x != y`      | <span data-ttu-id="2e3bd-2127">`true` 如果 `x` 不等于，则 `y` 为 `false` ; 否则为</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2127">`true` if `x` is not equal to `y`, `false` otherwise</span></span>             | 
| `x < y`       | <span data-ttu-id="2e3bd-2128">如果 `x` 小于 `y`，则为 `true`；否则为 `false`</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2128">`true` if `x` is less than `y`, `false` otherwise</span></span>                | 
| `x > y`       | <span data-ttu-id="2e3bd-2129">如果 `x` 大于 `y`，则为 `true`；否则为 `false`</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2129">`true` if `x` is greater than `y`, `false` otherwise</span></span>             | 
| `x <= y`      | <span data-ttu-id="2e3bd-2130">如果 `x` 小于等于 `y`，则为 `true`；否则为 `false`</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2130">`true` if `x` is less than or equal to `y`, `false` otherwise</span></span>    | 
| `x >= y`      | <span data-ttu-id="2e3bd-2131">如果 `x` 大于等于 `y`，则为 `true`；否则为 `false`</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2131">`true` if `x` is greater than or equal to `y`, `false` otherwise</span></span> | 

### <a name="integer-comparison-operators"></a><span data-ttu-id="2e3bd-2132">整数比较运算符</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2132">Integer comparison operators</span></span>

<span data-ttu-id="2e3bd-2133">预定义的整数比较运算符是：</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2133">The predefined integer comparison operators are:</span></span>
```csharp
bool operator ==(int x, int y);
bool operator ==(uint x, uint y);
bool operator ==(long x, long y);
bool operator ==(ulong x, ulong y);

bool operator !=(int x, int y);
bool operator !=(uint x, uint y);
bool operator !=(long x, long y);
bool operator !=(ulong x, ulong y);

bool operator <(int x, int y);
bool operator <(uint x, uint y);
bool operator <(long x, long y);
bool operator <(ulong x, ulong y);

bool operator >(int x, int y);
bool operator >(uint x, uint y);
bool operator >(long x, long y);
bool operator >(ulong x, ulong y);

bool operator <=(int x, int y);
bool operator <=(uint x, uint y);
bool operator <=(long x, long y);
bool operator <=(ulong x, ulong y);

bool operator >=(int x, int y);
bool operator >=(uint x, uint y);
bool operator >=(long x, long y);
bool operator >=(ulong x, ulong y);
```

<span data-ttu-id="2e3bd-2134">其中每个运算符比较两个整数操作数的数值，并返回一个 `bool` 值，该值指示特定关系是 `true` 还是 `false` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2134">Each of these operators compares the numeric values of the two integer operands and returns a `bool` value that indicates whether the particular relation is `true` or `false`.</span></span>

### <a name="floating-point-comparison-operators"></a><span data-ttu-id="2e3bd-2135">浮点比较运算符</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2135">Floating-point comparison operators</span></span>

<span data-ttu-id="2e3bd-2136">预定义的浮点比较运算符是：</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2136">The predefined floating-point comparison operators are:</span></span>
```csharp
bool operator ==(float x, float y);
bool operator ==(double x, double y);

bool operator !=(float x, float y);
bool operator !=(double x, double y);

bool operator <(float x, float y);
bool operator <(double x, double y);

bool operator >(float x, float y);
bool operator >(double x, double y);

bool operator <=(float x, float y);
bool operator <=(double x, double y);

bool operator >=(float x, float y);
bool operator >=(double x, double y);
```

<span data-ttu-id="2e3bd-2137">运算符根据 IEEE 754 标准的规则对操作数进行比较：</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2137">The operators compare the operands according to the rules of the IEEE 754 standard:</span></span>

*  <span data-ttu-id="2e3bd-2138">如果任一操作数为 NaN，则结果为 `false` 除之外的所有运算符 `!=` `true` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2138">If either operand is NaN, the result is `false` for all operators except `!=`, for which the result is `true`.</span></span> <span data-ttu-id="2e3bd-2139">对于任意两个操作数， `x != y` 始终产生与相同的结果 `!(x == y)` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2139">For any two operands, `x != y` always produces the same result as `!(x == y)`.</span></span> <span data-ttu-id="2e3bd-2140">但是，当一个或两个操作数为 NaN 时， `<` 、 `>` 、 `<=` 和 `>=` 运算符不会生成与相反运算符的逻辑求反的结果。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2140">However, when one or both operands are NaN, the `<`, `>`, `<=`, and `>=` operators do not produce the same results as the logical negation of the opposite operator.</span></span> <span data-ttu-id="2e3bd-2141">例如，如果 `x` 和 `y` 均为 NaN，则 `x < y` 为 `false` ，而为 `!(x >= y)` `true` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2141">For example, if either of `x` and `y` is NaN, then `x < y` is `false`, but `!(x >= y)` is `true`.</span></span>
*  <span data-ttu-id="2e3bd-2142">当两个操作数均为 NaN 时，运算符比较两个浮点操作数的值与排序相关</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2142">When neither operand is NaN, the operators compare the values of the two floating-point operands with respect to the ordering</span></span>

   ```csharp
   -inf < -max < ... < -min < -0.0 == +0.0 < +min < ... < +max < +inf
   ```

   <span data-ttu-id="2e3bd-2143">其中 `min` ，和 `max` 是可采用给定浮点格式表示的最小和最大正有限值。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2143">where `min` and `max` are the smallest and largest positive finite values that can be represented in the given floating-point format.</span></span> <span data-ttu-id="2e3bd-2144">此排序的明显效果是：</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2144">Notable effects of this ordering are:</span></span>
   * <span data-ttu-id="2e3bd-2145">负零和正零被视为相等。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2145">Negative and positive zeros are considered equal.</span></span>
   * <span data-ttu-id="2e3bd-2146">负无穷被视为小于所有其他值，但等于其他负无穷。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2146">A negative infinity is considered less than all other values, but equal to another negative infinity.</span></span>
   * <span data-ttu-id="2e3bd-2147">正无穷视为大于所有其他值，但等于其他正无穷。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2147">A positive infinity is considered greater than all other values, but equal to another positive infinity.</span></span>

### <a name="decimal-comparison-operators"></a><span data-ttu-id="2e3bd-2148">小数比较运算符</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2148">Decimal comparison operators</span></span>

<span data-ttu-id="2e3bd-2149">预定义的小数比较运算符是：</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2149">The predefined decimal comparison operators are:</span></span>
```csharp
bool operator ==(decimal x, decimal y);
bool operator !=(decimal x, decimal y);
bool operator <(decimal x, decimal y);
bool operator >(decimal x, decimal y);
bool operator <=(decimal x, decimal y);
bool operator >=(decimal x, decimal y);
```

<span data-ttu-id="2e3bd-2150">其中每个运算符比较两个 decimal 操作数的数值，并返回一个 `bool` 值，该值指示特定关系是 `true` 还是 `false` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2150">Each of these operators compares the numeric values of the two decimal operands and returns a `bool` value that indicates whether the particular relation is `true` or `false`.</span></span> <span data-ttu-id="2e3bd-2151">每个 decimal 比较等效于使用类型的对应关系或相等运算符 `System.Decimal` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2151">Each decimal comparison is equivalent to using the corresponding relational or equality operator of type `System.Decimal`.</span></span>

### <a name="boolean-equality-operators"></a><span data-ttu-id="2e3bd-2152">布尔相等运算符</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2152">Boolean equality operators</span></span>

<span data-ttu-id="2e3bd-2153">预定义的布尔相等运算符是：</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2153">The predefined boolean equality operators are:</span></span>
```csharp
bool operator ==(bool x, bool y);
bool operator !=(bool x, bool y);
```

<span data-ttu-id="2e3bd-2154">如果和均为，则的结果 `==` 为 `true` `x` `y` `true` 或 `x` `y` `false` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2154">The result of `==` is `true` if both `x` and `y` are `true` or if both `x` and `y` are `false`.</span></span> <span data-ttu-id="2e3bd-2155">否则，结果为 `false`。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2155">Otherwise, the result is `false`.</span></span>

<span data-ttu-id="2e3bd-2156">如果和均为，则的结果 `!=` 为 `false` `x` `y` `true` 或 `x` `y` `false` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2156">The result of `!=` is `false` if both `x` and `y` are `true` or if both `x` and `y` are `false`.</span></span> <span data-ttu-id="2e3bd-2157">否则，结果为 `true`。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2157">Otherwise, the result is `true`.</span></span> <span data-ttu-id="2e3bd-2158">当操作数为类型时 `bool` ， `!=` 运算符产生与运算符相同的结果 `^` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2158">When the operands are of type `bool`, the `!=` operator produces the same result as the `^` operator.</span></span>

### <a name="enumeration-comparison-operators"></a><span data-ttu-id="2e3bd-2159">枚举比较运算符</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2159">Enumeration comparison operators</span></span>

<span data-ttu-id="2e3bd-2160">每个枚举类型都隐式提供以下预定义的比较运算符：</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2160">Every enumeration type implicitly provides the following predefined comparison operators:</span></span>
```csharp
bool operator ==(E x, E y);
bool operator !=(E x, E y);
bool operator <(E x, E y);
bool operator >(E x, E y);
bool operator <=(E x, E y);
bool operator >=(E x, E y);
```

<span data-ttu-id="2e3bd-2161">计算结果 `x op y` ，其中 `x` 和 `y` 是具有基础类型的枚举类型的表达式 `E` `U` ， `op` 是比较运算符之一，与计算完全相同 `((U)x) op ((U)y)` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2161">The result of evaluating `x op y`, where `x` and `y` are expressions of an enumeration type `E` with an underlying type `U`, and `op` is one of the comparison operators, is exactly the same as evaluating `((U)x) op ((U)y)`.</span></span> <span data-ttu-id="2e3bd-2162">换言之，枚举类型比较运算符只是比较两个操作数的基础整数值。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2162">In other words, the enumeration type comparison operators simply compare the underlying integral values of the two operands.</span></span>

### <a name="reference-type-equality-operators"></a><span data-ttu-id="2e3bd-2163">引用类型相等运算符</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2163">Reference type equality operators</span></span>

<span data-ttu-id="2e3bd-2164">预定义的引用类型相等运算符是：</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2164">The predefined reference type equality operators are:</span></span>
```csharp
bool operator ==(object x, object y);
bool operator !=(object x, object y);
```

<span data-ttu-id="2e3bd-2165">运算符返回比较两个引用是否相等或不相等的结果。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2165">The operators return the result of comparing the two references for equality or non-equality.</span></span>

<span data-ttu-id="2e3bd-2166">由于预定义的引用类型相等运算符接受类型的操作数 `object` ，因此它们适用于不声明适用的和成员的所有类型 `operator ==` `operator !=` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2166">Since the predefined reference type equality operators accept operands of type `object`, they apply to all types that do not declare applicable `operator ==` and `operator !=` members.</span></span> <span data-ttu-id="2e3bd-2167">相反，任何适用的用户定义的相等运算符都可以有效地隐藏预定义的引用类型相等运算符。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2167">Conversely, any applicable user-defined equality operators effectively hide the predefined reference type equality operators.</span></span>

<span data-ttu-id="2e3bd-2168">预定义的引用类型相等运算符需要以下项之一：</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2168">The predefined reference type equality operators require one of the following:</span></span>

*  <span data-ttu-id="2e3bd-2169">这两个操作数都是已知为 *reference_type* 或文本的类型的值 `null` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2169">Both operands are a value of a type known to be a *reference_type* or the literal `null`.</span></span> <span data-ttu-id="2e3bd-2170">此外，显式引用转换 (显式 [引用](conversions.md#explicit-reference-conversions) 转换) 从任一操作数的类型到另一个操作数的类型。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2170">Furthermore, an explicit reference conversion ([Explicit reference conversions](conversions.md#explicit-reference-conversions)) exists from the type of either operand to the type of the other operand.</span></span>
*  <span data-ttu-id="2e3bd-2171">一个操作数是类型的值， `T` 其中 `T` 是 *type_parameter* ，另一个操作数是文本 `null` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2171">One operand is a value of type `T` where `T` is a *type_parameter* and the other operand is the literal `null`.</span></span> <span data-ttu-id="2e3bd-2172">此外 `T` ，也没有值类型约束。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2172">Furthermore `T` does not have the value type constraint.</span></span>

<span data-ttu-id="2e3bd-2173">除非满足这些条件之一，否则将发生绑定时错误。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2173">Unless one of these conditions are true, a binding-time error occurs.</span></span> <span data-ttu-id="2e3bd-2174">这些规则的明显含义是：</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2174">Notable implications of these rules are:</span></span>

*  <span data-ttu-id="2e3bd-2175">使用预定义的引用类型相等运算符比较已知在绑定时不同的两个引用是绑定时错误。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2175">It is a binding-time error to use the predefined reference type equality operators to compare two references that are known to be different at binding-time.</span></span> <span data-ttu-id="2e3bd-2176">例如，如果操作数的绑定时类型是两个类类型，并且 `A` `B` ，如果两者都不是 `A` `B` 派生的，则两个操作数不可能引用同一个对象。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2176">For example, if the binding-time types of the operands are two class types `A` and `B`, and if neither `A` nor `B` derives from the other, then it would be impossible for the two operands to reference the same object.</span></span> <span data-ttu-id="2e3bd-2177">因此，操作被视为绑定时错误。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2177">Thus, the operation is considered a binding-time error.</span></span>
*  <span data-ttu-id="2e3bd-2178">预定义的引用类型相等运算符不允许比较值类型操作数。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2178">The predefined reference type equality operators do not permit value type operands to be compared.</span></span> <span data-ttu-id="2e3bd-2179">因此，除非结构类型声明自己的相等运算符，否则不能比较该结构类型的值。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2179">Therefore, unless a struct type declares its own equality operators, it is not possible to compare values of that struct type.</span></span>
*  <span data-ttu-id="2e3bd-2180">预定义的引用类型相等运算符永远不会导致其操作数发生装箱操作。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2180">The predefined reference type equality operators never cause boxing operations to occur for their operands.</span></span> <span data-ttu-id="2e3bd-2181">执行此类装箱操作会毫无意义，因为对新分配的装箱实例的引用必须与所有其他引用不同。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2181">It would be meaningless to perform such boxing operations, since references to the newly allocated boxed instances would necessarily differ from all other references.</span></span>
*  <span data-ttu-id="2e3bd-2182">如果将类型参数类型的操作数 `T` 与进行比较 `null` ，并且的运行时类型 `T` 为值类型，则比较结果为 `false` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2182">If an operand of a type parameter type `T` is compared to `null`, and the run-time type of `T` is a value type, the result of the comparison is `false`.</span></span>

<span data-ttu-id="2e3bd-2183">下面的示例检查不受约束的类型参数类型的参数是否为 `null` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2183">The following example checks whether an argument of an unconstrained type parameter type is `null`.</span></span>
```csharp
class C<T>
{
    void F(T x) {
        if (x == null) throw new ArgumentNullException();
        ...
    }
}
```

<span data-ttu-id="2e3bd-2184">`x == null`即使 `T` 可能表示值类型，也允许构造，并且仅将结果定义为 `false` `T` 值类型。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2184">The `x == null` construct is permitted even though `T` could represent a value type, and the result is simply defined to be `false` when `T` is a value type.</span></span>

<span data-ttu-id="2e3bd-2185">对于窗体的操作 `x == y` `x != y` （如果有任何适用 `operator ==` 或 `operator !=` 存在），运算符重载决策 ([二元运算符重载解析](expressions.md#binary-operator-overload-resolution)) 规则将选择该运算符而不是预定义的引用类型相等运算符。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2185">For an operation of the form `x == y` or `x != y`, if any applicable `operator ==` or `operator !=` exists, the operator overload resolution ([Binary operator overload resolution](expressions.md#binary-operator-overload-resolution)) rules will select that operator instead of the predefined reference type equality operator.</span></span> <span data-ttu-id="2e3bd-2186">但是，始终可以通过将一个或两个操作数显式强制转换为类型来选择预定义的引用类型相等运算符 `object` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2186">However, it is always possible to select the predefined reference type equality operator by explicitly casting one or both of the operands to type `object`.</span></span> <span data-ttu-id="2e3bd-2187">示例</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2187">The example</span></span>
```csharp
using System;

class Test
{
    static void Main() {
        string s = "Test";
        string t = string.Copy(s);
        Console.WriteLine(s == t);
        Console.WriteLine((object)s == t);
        Console.WriteLine(s == (object)t);
        Console.WriteLine((object)s == (object)t);
    }
}
```
<span data-ttu-id="2e3bd-2188">生成输出</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2188">produces the output</span></span>
```console
True
False
False
False
```

<span data-ttu-id="2e3bd-2189">`s`和 `t` 变量引用 `string` 包含相同字符的两个不同实例。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2189">The `s` and `t` variables refer to two distinct `string` instances containing the same characters.</span></span> <span data-ttu-id="2e3bd-2190">第一个比较输出， `True` 因为当两个操作数为类型时，选择的是预定义的字符串相等运算符 ([字符串相等运算符](expressions.md#string-equality-operators)) `string` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2190">The first comparison outputs `True` because the predefined string equality operator ([String equality operators](expressions.md#string-equality-operators)) is selected when both operands are of type `string`.</span></span> <span data-ttu-id="2e3bd-2191">`False`由于在一个或两个操作数为类型时选择了预定义引用类型相等运算符，因此剩余的比较比较 `object` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2191">The remaining comparisons all output `False` because the predefined reference type equality operator is selected when one or both of the operands are of type `object`.</span></span>

<span data-ttu-id="2e3bd-2192">请注意，上述方法对于值类型没有意义。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2192">Note that the above technique is not meaningful for value types.</span></span> <span data-ttu-id="2e3bd-2193">示例</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2193">The example</span></span>
```csharp
class Test
{
    static void Main() {
        int i = 123;
        int j = 123;
        System.Console.WriteLine((object)i == (object)j);
    }
}
```
<span data-ttu-id="2e3bd-2194">输出， `False` 因为强制转换会创建对两个单独的装箱值实例的引用 `int` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2194">outputs `False` because the casts create references to two separate instances of boxed `int` values.</span></span>

### <a name="string-equality-operators"></a><span data-ttu-id="2e3bd-2195">字符串相等运算符</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2195">String equality operators</span></span>

<span data-ttu-id="2e3bd-2196">预定义的字符串相等运算符是：</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2196">The predefined string equality operators are:</span></span>
```csharp
bool operator ==(string x, string y);
bool operator !=(string x, string y);
```

<span data-ttu-id="2e3bd-2197">`string`如果满足以下任一条件，则将两个值视为相等：</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2197">Two `string` values are considered equal when one of the following is true:</span></span>

*  <span data-ttu-id="2e3bd-2198">这两个值都是 `null` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2198">Both values are `null`.</span></span>
*  <span data-ttu-id="2e3bd-2199">对于在每个字符位置都具有相同长度和相同字符的字符串实例，这两个值都是非空引用。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2199">Both values are non-null references to string instances that have identical lengths and identical characters in each character position.</span></span>

<span data-ttu-id="2e3bd-2200">字符串相等运算符比较字符串值而不是字符串引用。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2200">The string equality operators compare string values rather than string references.</span></span> <span data-ttu-id="2e3bd-2201">当两个单独的字符串实例包含完全相同的字符序列时，字符串的值相等，但引用不同。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2201">When two separate string instances contain the exact same sequence of characters, the values of the strings are equal, but the references are different.</span></span> <span data-ttu-id="2e3bd-2202">如 [引用类型相等运算符](expressions.md#reference-type-equality-operators)中所述，引用类型相等运算符可用于比较字符串引用而不是字符串值。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2202">As described in [Reference type equality operators](expressions.md#reference-type-equality-operators), the reference type equality operators can be used to compare string references instead of string values.</span></span>

### <a name="delegate-equality-operators"></a><span data-ttu-id="2e3bd-2203">委托相等运算符</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2203">Delegate equality operators</span></span>

<span data-ttu-id="2e3bd-2204">每个委托类型都隐式提供以下预定义的比较运算符：</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2204">Every delegate type implicitly provides the following predefined comparison operators:</span></span>

```csharp
bool operator ==(System.Delegate x, System.Delegate y);
bool operator !=(System.Delegate x, System.Delegate y);
```

<span data-ttu-id="2e3bd-2205">两个委托实例被视为相等，如下所示：</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2205">Two delegate instances are considered equal as follows:</span></span>

*  <span data-ttu-id="2e3bd-2206">如果任何一个委托实例为 `null` ，则当且仅当两者都为时，它们才相等 `null` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2206">If either of the delegate instances is `null`, they are equal if and only if both are `null`.</span></span>
*  <span data-ttu-id="2e3bd-2207">如果委托具有不同的运行时类型，则它们永远不相等。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2207">If the delegates have different run-time type they are never equal.</span></span>
*  <span data-ttu-id="2e3bd-2208">如果两个委托实例都 ([委托声明](delegates.md#delegate-declarations)) 调用列表，则当且仅当其调用列表相同时，这些实例相等，并且一个调用列表中的每个条目都相等 (如下所示) 到其他的调用列表中的相应条目。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2208">If both of the delegate instances have an invocation list ([Delegate declarations](delegates.md#delegate-declarations)), those instances are equal if and only if their invocation lists are the same length, and each entry in one's invocation list is equal (as defined below) to the corresponding entry, in order, in the other's invocation list.</span></span>

<span data-ttu-id="2e3bd-2209">以下规则控制调用列表项的相等性：</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2209">The following rules govern the equality of invocation list entries:</span></span>

*  <span data-ttu-id="2e3bd-2210">如果两个调用列表项引用相同的静态方法，则这些项相等。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2210">If two invocation list entries both refer to the same static method then the entries are equal.</span></span>
*  <span data-ttu-id="2e3bd-2211">如果两个调用列表项引用相同的目标对象上与引用相等运算符定义 (相同的非静态方法) 则这些项相等。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2211">If two invocation list entries both refer to the same non-static method on the same target object (as defined by the reference equality operators) then the entries are equal.</span></span>
*  <span data-ttu-id="2e3bd-2212">从语义上完全相同的 *anonymous_method_expression* s 或 *lambda_expression* 的计算生成的调用列表条目具有相同的 (可能为空) 允许的一组捕获的外部变量实例 (但不要求) 相等。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2212">Invocation list entries produced from evaluation of semantically identical *anonymous_method_expression* s or *lambda_expression* s with the same (possibly empty) set of captured outer variable instances are permitted (but not required) to be equal.</span></span>

### <a name="equality-operators-and-null"></a><span data-ttu-id="2e3bd-2213">相等运算符和 null</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2213">Equality operators and null</span></span>

<span data-ttu-id="2e3bd-2214">`==`和 `!=` 运算符允许一个操作数为可以为 null 的类型的值，另一个操作数为 `null` 原义值，即使操作中没有预定义或用户定义的运算符 (unlifted 或提升形式) 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2214">The `==` and `!=` operators permit one operand to be a value of a nullable type and the other to be the `null` literal, even if no predefined or user-defined operator (in unlifted or lifted form) exists for the operation.</span></span>

<span data-ttu-id="2e3bd-2215">对于某个窗体的操作</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2215">For an operation of one of the forms</span></span>
```csharp
x == null
null == x
x != null
null != x
```
<span data-ttu-id="2e3bd-2216">其中 `x` 是可以为 null 的类型的表达式，如果运算符重载决策 ([二元运算符重载决策](expressions.md#binary-operator-overload-resolution)) 未能找到适用的运算符，则结果是从的属性计算而来的 `HasValue` `x` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2216">where `x` is an expression of a nullable type, if operator overload resolution ([Binary operator overload resolution](expressions.md#binary-operator-overload-resolution)) fails to find an applicable operator, the result is instead computed from the `HasValue` property of `x`.</span></span> <span data-ttu-id="2e3bd-2217">具体而言，前两个窗体转换为 `!x.HasValue` ，最后两个窗体转换为 `x.HasValue` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2217">Specifically, the first two forms are translated into `!x.HasValue`, and last two forms are translated into `x.HasValue`.</span></span>

### <a name="the-is-operator"></a><span data-ttu-id="2e3bd-2218">is 运算符</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2218">The is operator</span></span>

<span data-ttu-id="2e3bd-2219">`is`运算符用于动态检查对象的运行时类型与给定类型是否兼容。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2219">The `is` operator is used to dynamically check if the run-time type of an object is compatible with a given type.</span></span> <span data-ttu-id="2e3bd-2220">操作的结果 `E is T` （其中是一个 `E` 表达式， `T` 是一个类型），它是一个布尔值，指示是否 `E` 可以 `T` 通过引用转换、装箱转换或取消装箱转换成功转换为类型。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2220">The result of the operation `E is T`, where `E` is an expression and `T` is a type, is a boolean value indicating whether `E` can successfully be converted to type `T` by a reference conversion, a boxing conversion, or an unboxing conversion.</span></span> <span data-ttu-id="2e3bd-2221">在将类型参数替换为所有类型参数后，按如下所示计算运算：</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2221">The operation is evaluated as follows, after type arguments have been substituted for all type parameters:</span></span>

*  <span data-ttu-id="2e3bd-2222">如果 `E` 是匿名函数，则会发生编译时错误。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2222">If `E` is an anonymous function, a compile-time error occurs</span></span>
*  <span data-ttu-id="2e3bd-2223">如果 `E` 是方法组或 `null` 文本，则如果的类型 `E` 是引用类型或可以为 null 的类型并且的值 `E` 为 null，则结果为 false。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2223">If `E` is a method group or the `null` literal, of if the type of `E` is a reference type or a nullable type and the value of `E` is null, the result is false.</span></span>
*  <span data-ttu-id="2e3bd-2224">否则，让我们按如下所示 `D` 表示动态类型 `E` ：</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2224">Otherwise, let `D` represent the dynamic type of `E` as follows:</span></span>
   * <span data-ttu-id="2e3bd-2225">如果的类型 `E` 为引用类型， `D` 则是实例引用的运行时类型 `E` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2225">If the type of `E` is a reference type, `D` is the run-time type of the instance reference by `E`.</span></span>
   * <span data-ttu-id="2e3bd-2226">如果的类型 `E` 是可以为 null 的类型， `D` 则为该可为 null 的类型的基础类型。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2226">If the type of `E` is a nullable type, `D` is the underlying type of that nullable type.</span></span>
   * <span data-ttu-id="2e3bd-2227">如果的类型 `E` 是不可以为 null 的值类型， `D` 则是的类型 `E` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2227">If the type of `E` is a non-nullable value type, `D` is the type of `E`.</span></span>
*  <span data-ttu-id="2e3bd-2228">操作的结果取决于 `D` 和，如下所示 `T` ：</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2228">The result of the operation depends on `D` and `T` as follows:</span></span>
   * <span data-ttu-id="2e3bd-2229">如果 `T` 是引用类型，并且如果是 `D` `T` `D` 引用类型且从到的隐式引用转换为，则结果为 true; `D` `T` 如果 `D` 是值类型，并且从到的装箱转换 `D` 为 `T` ，则结果为 true。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2229">If `T` is a reference type, the result is true if `D` and `T` are the same type, if `D` is a reference type and an implicit reference conversion from `D` to `T` exists, or if `D` is a value type and a boxing conversion from `D` to `T` exists.</span></span>
   * <span data-ttu-id="2e3bd-2230">如果 `T` 是可以为 null 的类型，并且是的基础类型，则结果为 true `D` `T` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2230">If `T` is a nullable type, the result is true if `D` is the underlying type of `T`.</span></span>
   * <span data-ttu-id="2e3bd-2231">如果 `T` 是不可以为 null 的值类型，则如果 `D` 和 `T` 是相同类型，则结果为 true。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2231">If `T` is a non-nullable value type, the result is true if `D` and `T` are the same type.</span></span>
   * <span data-ttu-id="2e3bd-2232">否则，结果为 false。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2232">Otherwise, the result is false.</span></span>

<span data-ttu-id="2e3bd-2233">请注意，运算符不考虑用户定义的转换 `is` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2233">Note that user defined conversions, are not considered by the `is` operator.</span></span>

### <a name="the-as-operator"></a><span data-ttu-id="2e3bd-2234">as 运算符</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2234">The as operator</span></span>

<span data-ttu-id="2e3bd-2235">`as`运算符用于将值显式转换为给定引用类型或可以为 null 的类型。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2235">The `as` operator is used to explicitly convert a value to a given reference type or nullable type.</span></span> <span data-ttu-id="2e3bd-2236">与转换表达式)  ([强制转换](expressions.md#cast-expressions) 表达式不同， `as` 运算符永远不会引发异常。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2236">Unlike a cast expression ([Cast expressions](expressions.md#cast-expressions)), the `as` operator never throws an exception.</span></span> <span data-ttu-id="2e3bd-2237">相反，如果不可能指定的转换，则生成的值为 `null` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2237">Instead, if the indicated conversion is not possible, the resulting value is `null`.</span></span>

<span data-ttu-id="2e3bd-2238">在窗体的操作中 `E as T` ， `E` 必须是表达式，并且 `T` 必须是引用类型、已知为引用类型的类型参数或可以为 null 的类型。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2238">In an operation of the form `E as T`, `E` must be an expression and `T` must be a reference type, a type parameter known to be a reference type, or a nullable type.</span></span> <span data-ttu-id="2e3bd-2239">此外，必须至少满足以下条件之一，否则将发生编译时错误：</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2239">Furthermore, at least one of the following must be true, or otherwise a compile-time error occurs:</span></span>

*  <span data-ttu-id="2e3bd-2240">标识 ([标识转换](conversions.md#identity-conversion)) ，隐式可为 Null (隐式可为 null 的[转换](conversions.md#implicit-nullable-conversions)) ，隐式引用 ([隐式引用转换](conversions.md#implicit-reference-conversions)) ，装箱 ([装箱转换](conversions.md#boxing-conversions)) ，显式引用[ (显式引用](conversions.md#explicit-nullable-conversions)[转换](conversions.md#explicit-reference-conversions)) 或取消装箱 (从到的[转换) ](conversions.md#unboxing-conversions)转换 `E` `T` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2240">An identity ([Identity conversion](conversions.md#identity-conversion)), implicit nullable ([Implicit nullable conversions](conversions.md#implicit-nullable-conversions)), implicit reference ([Implicit reference conversions](conversions.md#implicit-reference-conversions)), boxing ([Boxing conversions](conversions.md#boxing-conversions)), explicit nullable ([Explicit nullable conversions](conversions.md#explicit-nullable-conversions)), explicit reference ([Explicit reference conversions](conversions.md#explicit-reference-conversions)), or unboxing ([Unboxing conversions](conversions.md#unboxing-conversions)) conversion exists from `E` to `T`.</span></span>
*  <span data-ttu-id="2e3bd-2241">或的类型 `E` `T` 为开放类型。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2241">The type of `E` or `T` is an open type.</span></span>
*  <span data-ttu-id="2e3bd-2242">`E``null`文本。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2242">`E` is the `null` literal.</span></span>

<span data-ttu-id="2e3bd-2243">如果的编译时类型 `E` 不是 `dynamic` ，则该操作 `E as T` 生成的结果与</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2243">If the compile-time type of `E` is not `dynamic`, the operation `E as T` produces the same result as</span></span>
```csharp
E is T ? (T)(E) : (T)null
```
<span data-ttu-id="2e3bd-2244">不同的是 `E` 只计算一次。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2244">except that `E` is only evaluated once.</span></span> <span data-ttu-id="2e3bd-2245">编译器可以优化 `E as T` 到最多执行一次动态类型检查，而不是上述扩展隐含的两种动态类型检查。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2245">The compiler can be expected to optimize `E as T` to perform at most one dynamic type check as opposed to the two dynamic type checks implied by the expansion above.</span></span>

<span data-ttu-id="2e3bd-2246">如果的编译时类型 `E` 为，则 `dynamic` 与强制转换运算符不同， `as` 运算符不 ([动态绑定](expressions.md#dynamic-binding)) 动态绑定。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2246">If the compile-time type of `E` is `dynamic`, unlike the cast operator the `as` operator is not dynamically bound ([Dynamic binding](expressions.md#dynamic-binding)).</span></span> <span data-ttu-id="2e3bd-2247">因此，在这种情况下，展开是：</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2247">Therefore the expansion in this case is:</span></span>
```csharp
E is T ? (T)(object)(E) : (T)null
```

<span data-ttu-id="2e3bd-2248">请注意，某些转换（如用户定义的转换）无法与运算符一起使用， `as` 应改为使用强制转换表达式执行。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2248">Note that some conversions, such as user defined conversions, are not possible with the `as` operator and should instead be performed using cast expressions.</span></span>

<span data-ttu-id="2e3bd-2249">示例中</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2249">In the example</span></span>
```csharp
class X
{

    public string F(object o) {
        return o as string;        // OK, string is a reference type
    }

    public T G<T>(object o) where T: Attribute {
        return o as T;             // Ok, T has a class constraint
    }

    public U H<U>(object o) {
        return o as U;             // Error, U is unconstrained 
    }
}
```
<span data-ttu-id="2e3bd-2250">已知的类型 `T` 参数 `G` 是引用类型，因为它具有类约束。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2250">the type parameter `T` of `G` is known to be a reference type, because it has the class constraint.</span></span> <span data-ttu-id="2e3bd-2251">但不是的类型参数， `U` `H` 因此不 `as` 允许在中使用运算符 `H` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2251">The type parameter `U` of `H` is not however; hence the use of the `as` operator in `H` is disallowed.</span></span>

## <a name="logical-operators"></a><span data-ttu-id="2e3bd-2252">逻辑运算符</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2252">Logical operators</span></span>

<span data-ttu-id="2e3bd-2253">`&`、 `^` 和 `|` 运算符称为逻辑运算符。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2253">The `&`, `^`, and `|` operators are called the logical operators.</span></span>

```antlr
and_expression
    : equality_expression
    | and_expression '&' equality_expression
    ;

exclusive_or_expression
    : and_expression
    | exclusive_or_expression '^' and_expression
    ;

inclusive_or_expression
    : exclusive_or_expression
    | inclusive_or_expression '|' exclusive_or_expression
    ;
```

<span data-ttu-id="2e3bd-2254">如果逻辑运算符的操作数具有编译时类型 `dynamic` ，则该表达式将动态绑定 ([动态绑定](expressions.md#dynamic-binding)) 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2254">If an operand of a logical operator has the compile-time type `dynamic`, then the expression is dynamically bound ([Dynamic binding](expressions.md#dynamic-binding)).</span></span> <span data-ttu-id="2e3bd-2255">在这种情况下，表达式的编译时类型为 `dynamic` ，并使用具有编译时类型的操作数的运行时类型在运行时执行下面所述的解决方案 `dynamic` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2255">In this case the compile-time type of the expression is `dynamic`, and the resolution described below will take place at run-time using the run-time type of those operands that have the compile-time type `dynamic`.</span></span>

<span data-ttu-id="2e3bd-2256">对于该窗体的操作 `x op y` ，其中 `op` 是一个逻辑运算符，重载决策 ([二元运算符重载解析](expressions.md#binary-operator-overload-resolution)) 应用于选择特定运算符实现。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2256">For an operation of the form `x op y`, where `op` is one of the logical operators, overload resolution ([Binary operator overload resolution](expressions.md#binary-operator-overload-resolution)) is applied to select a specific operator implementation.</span></span> <span data-ttu-id="2e3bd-2257">操作数将转换为所选运算符的参数类型，结果的类型就是运算符的返回类型。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2257">The operands are converted to the parameter types of the selected operator, and the type of the result is the return type of the operator.</span></span>

<span data-ttu-id="2e3bd-2258">以下各节介绍了预定义的逻辑运算符。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2258">The predefined logical operators are described in the following sections.</span></span>

### <a name="integer-logical-operators"></a><span data-ttu-id="2e3bd-2259">整数逻辑运算符</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2259">Integer logical operators</span></span>

<span data-ttu-id="2e3bd-2260">预定义的整数逻辑运算符为：</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2260">The predefined integer logical operators are:</span></span>
```csharp
int operator &(int x, int y);
uint operator &(uint x, uint y);
long operator &(long x, long y);
ulong operator &(ulong x, ulong y);

int operator |(int x, int y);
uint operator |(uint x, uint y);
long operator |(long x, long y);
ulong operator |(ulong x, ulong y);

int operator ^(int x, int y);
uint operator ^(uint x, uint y);
long operator ^(long x, long y);
ulong operator ^(ulong x, ulong y);
```

<span data-ttu-id="2e3bd-2261">`&`运算符计算两个操作数的按位逻辑 `AND` ， `|` 运算符计算两个操作数的按位逻辑 `OR` ， `^` 运算符计算 `OR` 两个操作数的按位逻辑异。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2261">The `&` operator computes the bitwise logical `AND` of the two operands, the `|` operator computes the bitwise logical `OR` of the two operands, and the `^` operator computes the bitwise logical exclusive `OR` of the two operands.</span></span> <span data-ttu-id="2e3bd-2262">这些操作不能有溢出。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2262">No overflows are possible from these operations.</span></span>

### <a name="enumeration-logical-operators"></a><span data-ttu-id="2e3bd-2263">枚举逻辑运算符</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2263">Enumeration logical operators</span></span>

<span data-ttu-id="2e3bd-2264">每个枚举类型都 `E` 隐式提供以下预定义的逻辑运算符：</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2264">Every enumeration type `E` implicitly provides the following predefined logical operators:</span></span>

```csharp
E operator &(E x, E y);
E operator |(E x, E y);
E operator ^(E x, E y);
```

<span data-ttu-id="2e3bd-2265">计算结果 `x op y` （其中 `x` 和 `y` 是具有基础类型的枚举类型的表达式 `E` `U` ）， `op` 是一个逻辑运算符，与计算完全相同 `(E)((U)x op (U)y)` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2265">The result of evaluating `x op y`, where `x` and `y` are expressions of an enumeration type `E` with an underlying type `U`, and `op` is one of the logical operators, is exactly the same as evaluating `(E)((U)x op (U)y)`.</span></span> <span data-ttu-id="2e3bd-2266">换言之，枚举类型逻辑运算符只对两个操作数的基础类型执行逻辑运算。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2266">In other words, the enumeration type logical operators simply perform the logical operation on the underlying type of the two operands.</span></span>

### <a name="boolean-logical-operators"></a><span data-ttu-id="2e3bd-2267">Boolean 逻辑运算符</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2267">Boolean logical operators</span></span>

<span data-ttu-id="2e3bd-2268">预定义的布尔逻辑运算符为：</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2268">The predefined boolean logical operators are:</span></span>
```csharp
bool operator &(bool x, bool y);
bool operator |(bool x, bool y);
bool operator ^(bool x, bool y);
```

<span data-ttu-id="2e3bd-2269">如果 `x` 和 `y` 都为 `true`，则 `x & y` 的结果为 `true`。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2269">The result of `x & y` is `true` if both `x` and `y` are `true`.</span></span> <span data-ttu-id="2e3bd-2270">否则，结果为 `false`。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2270">Otherwise, the result is `false`.</span></span>

<span data-ttu-id="2e3bd-2271">`x | y` `true` 如果 `x` 或 `y` 为，则的结果为 `true` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2271">The result of `x | y` is `true` if either `x` or `y` is `true`.</span></span> <span data-ttu-id="2e3bd-2272">否则，结果为 `false`。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2272">Otherwise, the result is `false`.</span></span>

<span data-ttu-id="2e3bd-2273">的结果 `x ^ y` 是 `true` ，如果 `x` 为 `true` ，并且为 `y` `false` ，或 `x` 为 `false` 且 `y` 为 `true` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2273">The result of `x ^ y` is `true` if `x` is `true` and `y` is `false`, or `x` is `false` and `y` is `true`.</span></span> <span data-ttu-id="2e3bd-2274">否则，结果为 `false`。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2274">Otherwise, the result is `false`.</span></span> <span data-ttu-id="2e3bd-2275">当操作数为类型时 `bool` ，运算符将 `^` 计算与运算符相同的结果 `!=` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2275">When the operands are of type `bool`, the `^` operator computes the same result as the `!=` operator.</span></span>

### <a name="nullable-boolean-logical-operators"></a><span data-ttu-id="2e3bd-2276">可以为 null 的布尔逻辑运算符</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2276">Nullable boolean logical operators</span></span>

<span data-ttu-id="2e3bd-2277">可以为 null 的布尔类型 `bool?` 可以表示三个值： `true` 、 `false` 和 `null` ，在概念上类似于用于 SQL 中的布尔表达式的三值类型。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2277">The nullable boolean type `bool?` can represent three values, `true`, `false`, and `null`, and is conceptually similar to the three-valued type used for boolean expressions in SQL.</span></span> <span data-ttu-id="2e3bd-2278">为了确保操作数和运算符生成的结果 `&` `|` `bool?` 与 SQL 的三值逻辑一致，提供了以下预定义运算符：</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2278">To ensure that the results produced by the `&` and `|` operators for `bool?` operands are consistent with SQL's three-valued logic, the following predefined operators are provided:</span></span>

```csharp
bool? operator &(bool? x, bool? y);
bool? operator |(bool? x, bool? y);
```

<span data-ttu-id="2e3bd-2279">下表列出了这些运算符为所有值、和组合所产生的结果 `true` `false` `null` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2279">The following table lists the results produced by these operators for all combinations of the values `true`, `false`, and `null`.</span></span>

| `x`     | `y`     | `x & y` | <code>x &#124; y</code> |
|:-------:|:-------:|:-------:|:-------:|
| `true`  | `true`  | `true`  | `true`  | 
| `true`  | `false` | `false` | `true`  | 
| `true`  | `null`  | `null`  | `true`  | 
| `false` | `true`  | `false` | `true`  | 
| `false` | `false` | `false` | `false` | 
| `false` | `null`  | `false` | `null`  | 
| `null`  | `true`  | `null`  | `true`  | 
| `null`  | `false` | `false` | `null`  | 
| `null`  | `null`  | `null`  | `null`  | 

## <a name="conditional-logical-operators"></a><span data-ttu-id="2e3bd-2280">条件逻辑运算符</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2280">Conditional logical operators</span></span>

<span data-ttu-id="2e3bd-2281">`&&` 和 `||` 运算符称为条件逻辑运算符。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2281">The `&&` and `||` operators are called the conditional logical operators.</span></span> <span data-ttu-id="2e3bd-2282">它们也称为 "短路" 逻辑运算符。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2282">They are also called the "short-circuiting" logical operators.</span></span>

```antlr
conditional_and_expression
    : inclusive_or_expression
    | conditional_and_expression '&&' inclusive_or_expression
    ;

conditional_or_expression
    : conditional_and_expression
    | conditional_or_expression '||' conditional_and_expression
    ;
```

<span data-ttu-id="2e3bd-2283">`&&`和 `||` 运算符是和运算符的条件版本 `&` `|` ：</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2283">The `&&` and `||` operators are conditional versions of the `&` and `|` operators:</span></span>

*  <span data-ttu-id="2e3bd-2284">操作 `x && y` 对应于操作，只是在不是时 `x & y` 计算的除外 `y` `x` `false` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2284">The operation `x && y` corresponds to the operation `x & y`, except that `y` is evaluated only if `x` is not `false`.</span></span>
*  <span data-ttu-id="2e3bd-2285">操作 `x || y` 对应于操作，只是在不是时 `x | y` 计算的除外 `y` `x` `true` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2285">The operation `x || y` corresponds to the operation `x | y`, except that `y` is evaluated only if `x` is not `true`.</span></span>

<span data-ttu-id="2e3bd-2286">如果条件逻辑运算符的操作数具有编译时类型 `dynamic` ，则该表达式将动态绑定 ([动态绑定](expressions.md#dynamic-binding)) 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2286">If an operand of a conditional logical operator has the compile-time type `dynamic`, then the expression is dynamically bound ([Dynamic binding](expressions.md#dynamic-binding)).</span></span> <span data-ttu-id="2e3bd-2287">在这种情况下，表达式的编译时类型为 `dynamic` ，并使用具有编译时类型的操作数的运行时类型在运行时执行下面所述的解决方案 `dynamic` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2287">In this case the compile-time type of the expression is `dynamic`, and the resolution described below will take place at run-time using the run-time type of those operands that have the compile-time type `dynamic`.</span></span>

<span data-ttu-id="2e3bd-2288">`x && y` `x || y` 通过 ([二元运算符重载解析](expressions.md#binary-operator-overload-resolution)) 应用重载决策来处理窗体或的操作，就像编写操作的方法 `x & y` 一样 `x | y` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2288">An operation of the form `x && y` or `x || y` is processed by applying overload resolution ([Binary operator overload resolution](expressions.md#binary-operator-overload-resolution)) as if the operation was written `x & y` or `x | y`.</span></span> <span data-ttu-id="2e3bd-2289">那么：</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2289">Then,</span></span>

*  <span data-ttu-id="2e3bd-2290">如果重载决策未能找到单个最佳运算符，或者重载决策选择预定义的整数逻辑运算符之一，则会发生绑定时错误。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2290">If overload resolution fails to find a single best operator, or if overload resolution selects one of the predefined integer logical operators, a binding-time error occurs.</span></span>
*  <span data-ttu-id="2e3bd-2291">否则，如果所选运算符是预定义的布尔逻辑运算符之一 ([布尔逻辑运算符](expressions.md#boolean-logical-operators)) 或可以为 null 的布尔逻辑运算符)  ([可以为 null 的布尔](expressions.md#nullable-boolean-logical-operators) 逻辑运算符，则按照 [布尔条件逻辑运算符](expressions.md#boolean-conditional-logical-operators)中所述处理操作。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2291">Otherwise, if the selected operator is one of the predefined boolean logical operators ([Boolean logical operators](expressions.md#boolean-logical-operators)) or nullable boolean logical operators ([Nullable boolean logical operators](expressions.md#nullable-boolean-logical-operators)), the operation is processed as described in [Boolean conditional logical operators](expressions.md#boolean-conditional-logical-operators).</span></span>
*  <span data-ttu-id="2e3bd-2292">否则，所选运算符是用户定义的运算符，并且按照 [用户定义的条件逻辑运算符](expressions.md#user-defined-conditional-logical-operators)中的描述处理操作。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2292">Otherwise, the selected operator is a user-defined operator, and the operation is processed as described in [User-defined conditional logical operators](expressions.md#user-defined-conditional-logical-operators).</span></span>

<span data-ttu-id="2e3bd-2293">不能直接重载条件逻辑运算符。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2293">It is not possible to directly overload the conditional logical operators.</span></span> <span data-ttu-id="2e3bd-2294">但是，因为条件逻辑运算符是按常规逻辑运算符计算的，所以常规逻辑运算符的重载在某些限制条件下也被视为条件逻辑运算符的重载。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2294">However, because the conditional logical operators are evaluated in terms of the regular logical operators, overloads of the regular logical operators are, with certain restrictions, also considered overloads of the conditional logical operators.</span></span> <span data-ttu-id="2e3bd-2295">[用户定义的条件逻辑运算符](expressions.md#user-defined-conditional-logical-operators)中对此进行了进一步说明。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2295">This is described further in [User-defined conditional logical operators](expressions.md#user-defined-conditional-logical-operators).</span></span>

### <a name="boolean-conditional-logical-operators"></a><span data-ttu-id="2e3bd-2296">布尔条件逻辑运算符</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2296">Boolean conditional logical operators</span></span>

<span data-ttu-id="2e3bd-2297">当或的操作数 `&&` 为 `||` 类型时 `bool` ，或者当操作数的类型未定义适用的 `operator &` 或 `operator |` 但确实定义了到的隐式转换时， `bool` 将按如下所示处理操作：</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2297">When the operands of `&&` or `||` are of type `bool`, or when the operands are of types that do not define an applicable `operator &` or `operator |`, but do define implicit conversions to `bool`, the operation is processed as follows:</span></span>

*  <span data-ttu-id="2e3bd-2298">运算的 `x && y` 计算结果为 `x ? y : false` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2298">The operation `x && y` is evaluated as `x ? y : false`.</span></span> <span data-ttu-id="2e3bd-2299">换言之， `x` 首先计算并转换为类型 `bool` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2299">In other words, `x` is first evaluated and converted to type `bool`.</span></span> <span data-ttu-id="2e3bd-2300">然后，如果 `x` 为 `true` ， `y` 则计算并转换为类型 `bool` ，这将成为运算的结果。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2300">Then, if `x` is `true`, `y` is evaluated and converted to type `bool`, and this becomes the result of the operation.</span></span> <span data-ttu-id="2e3bd-2301">否则，操作的结果为 `false` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2301">Otherwise, the result of the operation is `false`.</span></span>
*  <span data-ttu-id="2e3bd-2302">运算的 `x || y` 计算结果为 `x ? true : y` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2302">The operation `x || y` is evaluated as `x ? true : y`.</span></span> <span data-ttu-id="2e3bd-2303">换言之， `x` 首先计算并转换为类型 `bool` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2303">In other words, `x` is first evaluated and converted to type `bool`.</span></span> <span data-ttu-id="2e3bd-2304">然后，如果 `x` 为 `true` ，则操作的结果为 `true` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2304">Then, if `x` is `true`, the result of the operation is `true`.</span></span> <span data-ttu-id="2e3bd-2305">否则， `y` 将计算并转换为类型 `bool` ，这将成为运算的结果。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2305">Otherwise, `y` is evaluated and converted to type `bool`, and this becomes the result of the operation.</span></span>

### <a name="user-defined-conditional-logical-operators"></a><span data-ttu-id="2e3bd-2306">用户定义的条件逻辑运算符</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2306">User-defined conditional logical operators</span></span>

<span data-ttu-id="2e3bd-2307">如果或的操作数 `&&` 属于 `||` 声明适用的用户定义的或的类型 `operator &` ，则 `operator |` 以下两个条件都必须为 true，其中 `T` 是声明所选运算符的类型：</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2307">When the operands of `&&` or `||` are of types that declare an applicable user-defined `operator &` or `operator |`, both of the following must be true, where `T` is the type in which the selected operator is declared:</span></span>

*  <span data-ttu-id="2e3bd-2308">所选运算符的每个参数的返回类型和类型必须为 `T` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2308">The return type and the type of each parameter of the selected operator must be `T`.</span></span> <span data-ttu-id="2e3bd-2309">换言之，运算符必须计算 `AND` `OR` 类型为的两个操作数的逻辑或逻辑 `T` ，并且必须返回类型的结果 `T` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2309">In other words, the operator must compute the logical `AND` or the logical `OR` of two operands of type `T`, and must return a result of type `T`.</span></span>
*  <span data-ttu-id="2e3bd-2310">`T` 必须包含和的 `operator true` 声明 `operator false` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2310">`T` must contain declarations of `operator true` and `operator false`.</span></span>

<span data-ttu-id="2e3bd-2311">如果不满足上述任一要求，则会发生绑定时错误。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2311">A binding-time error occurs if either of these requirements is not satisfied.</span></span> <span data-ttu-id="2e3bd-2312">否则， `&&` 通过将 `||` 用户定义的 `operator true` 或 `operator false` 与选定的用户定义运算符相结合来计算或运算：</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2312">Otherwise, the `&&` or `||` operation is evaluated by combining the user-defined `operator true` or `operator false` with the selected user-defined operator:</span></span>

*  <span data-ttu-id="2e3bd-2313">运算的 `x && y` 计算结果为 `T.false(x) ? x : T.&(x, y)` ，其中 `T.false(x)` 是中声明的的调用 `operator false` `T` ，并且是对 `T.&(x, y)` 所选的的调用 `operator &` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2313">The operation `x && y` is evaluated as `T.false(x) ? x : T.&(x, y)`, where `T.false(x)` is an invocation of the `operator false` declared in `T`, and `T.&(x, y)` is an invocation of the selected `operator &`.</span></span> <span data-ttu-id="2e3bd-2314">换言之， `x` 首先计算并 `operator false` 对结果调用，以确定 `x` 是否肯定为 false。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2314">In other words, `x` is first evaluated and `operator false` is invoked on the result to determine if `x` is definitely false.</span></span> <span data-ttu-id="2e3bd-2315">然后，如果的 `x` 值为 true，则运算的结果是先前为计算得出的值 `x` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2315">Then, if `x` is definitely false, the result of the operation is the value previously computed for `x`.</span></span> <span data-ttu-id="2e3bd-2316">否则， `y` 将计算，并 `operator &` 对之前为计算的值调用选择的，并对的值进行调用 `x` `y` 以生成操作的结果。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2316">Otherwise, `y` is evaluated, and the selected `operator &` is invoked on the value previously computed for `x` and the value computed for `y` to produce the result of the operation.</span></span>
*  <span data-ttu-id="2e3bd-2317">运算的 `x || y` 计算结果为 `T.true(x) ? x : T.|(x, y)` ，其中 `T.true(x)` 是中声明的的调用 `operator true` `T` ，并且是对 `T.|(x,y)` 所选的的调用 `operator|` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2317">The operation `x || y` is evaluated as `T.true(x) ? x : T.|(x, y)`, where `T.true(x)` is an invocation of the `operator true` declared in `T`, and `T.|(x,y)` is an invocation of the selected `operator|`.</span></span> <span data-ttu-id="2e3bd-2318">换言之， `x` 首先计算并 `operator true` 对结果调用，以确定是否 `x` 确实为 true。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2318">In other words, `x` is first evaluated and `operator true` is invoked on the result to determine if `x` is definitely true.</span></span> <span data-ttu-id="2e3bd-2319">如果肯定为 `x` true，则运算的结果是先前为计算得出的值 `x` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2319">Then, if `x` is definitely true, the result of the operation is the value previously computed for `x`.</span></span> <span data-ttu-id="2e3bd-2320">否则， `y` 将计算，并 `operator |` 对之前为计算的值调用选择的，并对的值进行调用 `x` `y` 以生成操作的结果。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2320">Otherwise, `y` is evaluated, and the selected `operator |` is invoked on the value previously computed for `x` and the value computed for `y` to produce the result of the operation.</span></span>

<span data-ttu-id="2e3bd-2321">在上述任一操作中，由给定的表达式 `x` 只计算一次，并且由给定的表达式 `y` 不会进行任何计算或只计算一次。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2321">In either of these operations, the expression given by `x` is only evaluated once, and the expression given by `y` is either not evaluated or evaluated exactly once.</span></span>

<span data-ttu-id="2e3bd-2322">有关实现和的类型的示例 `operator true` `operator false` ，请参阅 [数据库布尔类型](structs.md#database-boolean-type)。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2322">For an example of a type that implements `operator true` and `operator false`, see [Database boolean type](structs.md#database-boolean-type).</span></span>

## <a name="the-null-coalescing-operator"></a><span data-ttu-id="2e3bd-2323">Null 合并运算符</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2323">The null coalescing operator</span></span>

<span data-ttu-id="2e3bd-2324">`??`运算符称为 null 合并运算符。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2324">The `??` operator is called the null coalescing operator.</span></span>

```antlr
null_coalescing_expression
    : conditional_or_expression
    | conditional_or_expression '??' null_coalescing_expression
    ;
```

<span data-ttu-id="2e3bd-2325">窗体的 null 合并表达式 `a ?? b` 需要 `a` 是可以为 null 的类型或引用类型。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2325">A null coalescing expression of the form `a ?? b` requires `a` to be of a nullable type or reference type.</span></span> <span data-ttu-id="2e3bd-2326">如果 `a` 为非 null，则的结果 `a ?? b` 为 `a` ; 否则，结果为 `b` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2326">If `a` is non-null, the result of `a ?? b` is `a`; otherwise, the result is `b`.</span></span> <span data-ttu-id="2e3bd-2327">`b`仅当为 null 时，操作才 `a` 进行评估。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2327">The operation evaluates `b` only if `a` is null.</span></span>

<span data-ttu-id="2e3bd-2328">Null 合并运算符是右结合运算符，这意味着运算从右到左分组。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2328">The null coalescing operator is right-associative, meaning that operations are grouped from right to left.</span></span> <span data-ttu-id="2e3bd-2329">例如，窗体的表达式的 `a ?? b ?? c` 计算结果为 `a ?? (b ?? c)` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2329">For example, an expression of the form `a ?? b ?? c` is evaluated as `a ?? (b ?? c)`.</span></span> <span data-ttu-id="2e3bd-2330">通常情况下，窗体的表达式将 `E1 ?? E2 ?? ... ?? En` 返回非 null 的第一个操作数，如果所有操作数为 null，则返回 null。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2330">In general terms, an expression of the form `E1 ?? E2 ?? ... ?? En` returns the first of the operands that is non-null, or null if all operands are null.</span></span>

<span data-ttu-id="2e3bd-2331">表达式的类型 `a ?? b` 取决于在操作数上可用的隐式转换。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2331">The type of the expression `a ?? b` depends on which implicit conversions are available on the operands.</span></span> <span data-ttu-id="2e3bd-2332">按照优先顺序，的类型 `a ?? b` 为，， `A0` 或为，其中是具有类型) 的所提供 (的类型，是所) 提供的类型为的 (类型， `A` `B` `A` `a` `a` `B` `b` `b` `A0` `A` 如果 `A` 是可以为 null 的类型，则为基础类型， `A` 否则为。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2332">In order of preference, the type of `a ?? b` is `A0`, `A`, or `B`, where `A` is the type of `a` (provided that `a` has a type), `B` is the type of `b` (provided that `b` has a type), and `A0` is the underlying type of `A` if `A` is a nullable type, or `A` otherwise.</span></span> <span data-ttu-id="2e3bd-2333">具体而言， `a ?? b` 按如下方式进行处理：</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2333">Specifically, `a ?? b` is processed as follows:</span></span>

*  <span data-ttu-id="2e3bd-2334">如果 `A` 存在并且不是可以为 null 的类型或引用类型，则会发生编译时错误。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2334">If `A` exists and is not a nullable type or a reference type, a compile-time error occurs.</span></span>
*  <span data-ttu-id="2e3bd-2335">如果 `b` 是动态表达式，则结果类型为 `dynamic` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2335">If `b` is a dynamic expression, the result type is `dynamic`.</span></span> <span data-ttu-id="2e3bd-2336">在运行时， `a` 首先计算。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2336">At run-time, `a` is first evaluated.</span></span> <span data-ttu-id="2e3bd-2337">如果 `a` 不为 null， `a` 则将转换为动态，这将成为结果。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2337">If `a` is not null, `a` is converted to dynamic, and this becomes the result.</span></span> <span data-ttu-id="2e3bd-2338">否则， `b` 将计算，这将成为结果。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2338">Otherwise, `b` is evaluated, and this becomes the result.</span></span>
*  <span data-ttu-id="2e3bd-2339">否则，如果 `A` 存在并且是可以为 null 的类型并且存在从到的隐式转换 `b` `A0` ，则结果类型为 `A0` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2339">Otherwise, if `A` exists and is a nullable type and an implicit conversion exists from `b` to `A0`, the result type is `A0`.</span></span> <span data-ttu-id="2e3bd-2340">在运行时， `a` 首先计算。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2340">At run-time, `a` is first evaluated.</span></span> <span data-ttu-id="2e3bd-2341">如果不为 `a` null， `a` 则将解包到类型 `A0` ，这就成为了结果。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2341">If `a` is not null, `a` is unwrapped to type `A0`, and this becomes the result.</span></span> <span data-ttu-id="2e3bd-2342">否则， `b` 将计算并转换为类型 `A0` ，这将成为结果。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2342">Otherwise, `b` is evaluated and converted to type `A0`, and this becomes the result.</span></span>
*  <span data-ttu-id="2e3bd-2343">否则，如果 `A` 存在并且存在隐式转换 `b` `A` ，则结果类型为 `A` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2343">Otherwise, if `A` exists and an implicit conversion exists from `b` to `A`, the result type is `A`.</span></span> <span data-ttu-id="2e3bd-2344">在运行时， `a` 首先计算。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2344">At run-time, `a` is first evaluated.</span></span> <span data-ttu-id="2e3bd-2345">如果 `a` 不为 null，则 `a` 成为结果。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2345">If `a` is not null, `a` becomes the result.</span></span> <span data-ttu-id="2e3bd-2346">否则， `b` 将计算并转换为类型 `A` ，这将成为结果。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2346">Otherwise, `b` is evaluated and converted to type `A`, and this becomes the result.</span></span>
*  <span data-ttu-id="2e3bd-2347">否则，如果 `b` 具有类型 `B` 且存在从到的隐式 `a` 转换 `B` ，则结果类型为 `B` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2347">Otherwise, if `b` has a type `B` and an implicit conversion exists from `a` to `B`, the result type is `B`.</span></span> <span data-ttu-id="2e3bd-2348">在运行时， `a` 首先计算。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2348">At run-time, `a` is first evaluated.</span></span> <span data-ttu-id="2e3bd-2349">如果 `a` 不为 null， `a` 则将解包为类型 `A0` (如果 `A` 存在且可以为 null) 并转换为类型 `B` ，则结果为。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2349">If `a` is not null, `a` is unwrapped to type `A0` (if `A` exists and is nullable) and converted to type `B`, and this becomes the result.</span></span> <span data-ttu-id="2e3bd-2350">否则， `b` 将计算并返回结果。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2350">Otherwise, `b` is evaluated and becomes the result.</span></span>
*  <span data-ttu-id="2e3bd-2351">否则， `a` 与 `b` 不兼容，并发生编译时错误。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2351">Otherwise, `a` and `b` are incompatible, and a compile-time error occurs.</span></span>

## <a name="conditional-operator"></a><span data-ttu-id="2e3bd-2352">条件运算符</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2352">Conditional operator</span></span>

<span data-ttu-id="2e3bd-2353">`?:`运算符称为条件运算符。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2353">The `?:` operator is called the conditional operator.</span></span> <span data-ttu-id="2e3bd-2354">有时也称为三元运算符。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2354">It is at times also called the ternary operator.</span></span>

```antlr
conditional_expression
    : null_coalescing_expression
    | null_coalescing_expression '?' expression ':' expression
    ;
```

<span data-ttu-id="2e3bd-2355">表单的条件表达式 `b ? x : y` 首先计算条件 `b` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2355">A conditional expression of the form `b ? x : y` first evaluates the condition `b`.</span></span> <span data-ttu-id="2e3bd-2356">然后，如果 `b` 为 `true` ， `x` 则将计算并成为操作的结果。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2356">Then, if `b` is `true`, `x` is evaluated and becomes the result of the operation.</span></span> <span data-ttu-id="2e3bd-2357">否则， `y` 将计算并成为操作的结果。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2357">Otherwise, `y` is evaluated and becomes the result of the operation.</span></span> <span data-ttu-id="2e3bd-2358">条件表达式绝不会计算 `x` 和 `y` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2358">A conditional expression never evaluates both `x` and `y`.</span></span>

<span data-ttu-id="2e3bd-2359">条件运算符是右结合运算符，这意味着运算从右到左分组。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2359">The conditional operator is right-associative, meaning that operations are grouped from right to left.</span></span> <span data-ttu-id="2e3bd-2360">例如，窗体的表达式的 `a ? b : c ? d : e` 计算结果为 `a ? b : (c ? d : e)` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2360">For example, an expression of the form `a ? b : c ? d : e` is evaluated as `a ? b : (c ? d : e)`.</span></span>

<span data-ttu-id="2e3bd-2361">运算符的第一个操作数 `?:` 必须是可以隐式转换为的表达式 `bool` ，或者是实现的类型的表达式 `operator true` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2361">The first operand of the `?:` operator must be an expression that can be implicitly converted to `bool`, or an expression of a type that implements `operator true`.</span></span> <span data-ttu-id="2e3bd-2362">如果满足这两个要求，则会发生编译时错误。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2362">If neither of these requirements is satisfied, a compile-time error occurs.</span></span>

<span data-ttu-id="2e3bd-2363">运算符的第二个和第三个操作数 `x` `y` `?:` 控制条件表达式的类型。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2363">The second and third operands, `x` and `y`, of the `?:` operator control the type of the conditional expression.</span></span>

*  <span data-ttu-id="2e3bd-2364">如果 `x` 具有类型 `X` 并且 `y` 具有类型， `Y` 则</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2364">If `x` has type `X` and `y` has type `Y` then</span></span>
   * <span data-ttu-id="2e3bd-2365">如果隐式转换 ([隐式](conversions.md#implicit-conversions) 转换) 从 `X` 到 `Y` ，但不 `Y` 是从到 `X` ，则 `Y` 是条件表达式的类型。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2365">If an implicit conversion ([Implicit conversions](conversions.md#implicit-conversions)) exists from `X` to `Y`, but not from `Y` to `X`, then `Y` is the type of the conditional expression.</span></span>
   * <span data-ttu-id="2e3bd-2366">如果隐式转换 ([隐式](conversions.md#implicit-conversions) 转换) 从 `Y` 到 `X` ，但不 `X` 是从到 `Y` ，则 `X` 是条件表达式的类型。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2366">If an implicit conversion ([Implicit conversions](conversions.md#implicit-conversions)) exists from `Y` to `X`, but not from `X` to `Y`, then `X` is the type of the conditional expression.</span></span>
   * <span data-ttu-id="2e3bd-2367">否则，不能确定表达式类型，并且会发生编译时错误。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2367">Otherwise, no expression type can be determined, and a compile-time error occurs.</span></span>
*  <span data-ttu-id="2e3bd-2368">如果仅有一个 `x` 类型，且和都 `y` `x` `y` 可隐式转换为该类型，则这是条件表达式的类型。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2368">If only one of `x` and `y` has a type, and both `x` and `y`, of are implicitly convertible to that type, then that is the type of the conditional expression.</span></span>
*  <span data-ttu-id="2e3bd-2369">否则，不能确定表达式类型，并且会发生编译时错误。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2369">Otherwise, no expression type can be determined, and a compile-time error occurs.</span></span>

<span data-ttu-id="2e3bd-2370">此形式的条件表达式的运行时处理 `b ? x : y` 包括以下步骤：</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2370">The run-time processing of a conditional expression of the form `b ? x : y` consists of the following steps:</span></span>

*  <span data-ttu-id="2e3bd-2371">首先， `b` 将计算，并 `bool` 确定的值 `b` ：</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2371">First, `b` is evaluated, and the `bool` value of `b` is determined:</span></span>
   * <span data-ttu-id="2e3bd-2372">如果从类型到的隐式转换 `b` `bool` 存在，则执行此隐式转换以生成 `bool` 值。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2372">If an implicit conversion from the type of `b` to `bool` exists, then this implicit conversion is performed to produce a `bool` value.</span></span>
   * <span data-ttu-id="2e3bd-2373">否则，将 `operator true` 调用类型的定义， `b` 以生成 `bool` 值。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2373">Otherwise, the `operator true` defined by the type of `b` is invoked to produce a `bool` value.</span></span>
*  <span data-ttu-id="2e3bd-2374">如果 `bool` 以上步骤生成的值为，则 `true` `x` 将计算并转换为条件表达式的类型，这将成为条件表达式的结果。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2374">If the `bool` value produced by the step above is `true`, then `x` is evaluated and converted to the type of the conditional expression, and this becomes the result of the conditional expression.</span></span>
*  <span data-ttu-id="2e3bd-2375">否则， `y` 将计算并转换为条件表达式的类型，这将成为条件表达式的结果。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2375">Otherwise, `y` is evaluated and converted to the type of the conditional expression, and this becomes the result of the conditional expression.</span></span>

## <a name="anonymous-function-expressions"></a><span data-ttu-id="2e3bd-2376">匿名函数表达式</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2376">Anonymous function expressions</span></span>

<span data-ttu-id="2e3bd-2377">***匿名函数*** 是表示 "内联" 方法定义的表达式。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2377">An ***anonymous function*** is an expression that represents an "in-line" method definition.</span></span> <span data-ttu-id="2e3bd-2378">匿名函数本身不具有值或类型，但可以转换为兼容的委托或表达式树类型。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2378">An anonymous function does not have a value or type in and of itself, but is convertible to a compatible delegate or expression tree type.</span></span> <span data-ttu-id="2e3bd-2379">匿名函数转换的计算取决于转换的目标类型：如果该类型为委托类型，则转换将计算为引用匿名函数定义的方法的委托值。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2379">The evaluation of an anonymous function conversion depends on the target type of the conversion: If it is a delegate type, the conversion evaluates to a delegate value referencing the method which the anonymous function defines.</span></span> <span data-ttu-id="2e3bd-2380">如果该类型为表达式树类型，则转换为表达式树，该树将方法的结构表示为对象结构。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2380">If it is an expression tree type, the conversion evaluates to an expression tree which represents the structure of the method as an object structure.</span></span>

<span data-ttu-id="2e3bd-2381">由于历史原因，匿名函数有两种语法风格，即 *lambda_expression* s 和 *anonymous_method_expression* s。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2381">For historical reasons there are two syntactic flavors of anonymous functions, namely *lambda_expression* s and *anonymous_method_expression* s.</span></span> <span data-ttu-id="2e3bd-2382">几乎所有情况下， *lambda_expression* 比 *anonymous_method_expression* 的更简洁、更具表现力，这仍然是用于向后兼容的语言。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2382">For almost all purposes, *lambda_expression* s are more concise and expressive than *anonymous_method_expression* s, which remain in the language for backwards compatibility.</span></span>

```antlr
lambda_expression
    : anonymous_function_signature '=>' anonymous_function_body
    ;

anonymous_method_expression
    : 'delegate' explicit_anonymous_function_signature? block
    ;

anonymous_function_signature
    : explicit_anonymous_function_signature
    | implicit_anonymous_function_signature
    ;

explicit_anonymous_function_signature
    : '(' explicit_anonymous_function_parameter_list? ')'
    ;

explicit_anonymous_function_parameter_list
    : explicit_anonymous_function_parameter (',' explicit_anonymous_function_parameter)*
    ;

explicit_anonymous_function_parameter
    : anonymous_function_parameter_modifier? type identifier
    ;

anonymous_function_parameter_modifier
    : 'ref'
    | 'out'
    ;

implicit_anonymous_function_signature
    : '(' implicit_anonymous_function_parameter_list? ')'
    | implicit_anonymous_function_parameter
    ;

implicit_anonymous_function_parameter_list
    : implicit_anonymous_function_parameter (',' implicit_anonymous_function_parameter)*
    ;

implicit_anonymous_function_parameter
    : identifier
    ;

anonymous_function_body
    : expression
    | block
    ;
```

<span data-ttu-id="2e3bd-2383">`=>`运算符具有与赋值 () 相同的优先级 `=` ，并且是右结合运算符。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2383">The `=>` operator has the same precedence as assignment (`=`) and is right-associative.</span></span>

<span data-ttu-id="2e3bd-2384">带有修饰符的匿名函数 `async` 是一个异步函数，并遵循 [异步](classes.md#async-functions)函数中所述的规则。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2384">An anonymous function with the `async` modifier is an async function and follows the rules described in [Async functions](classes.md#async-functions).</span></span>

<span data-ttu-id="2e3bd-2385">可以显式或隐式类型地 *lambda_expression* 形式的匿名函数的参数。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2385">The parameters of an anonymous function in the form of a *lambda_expression* can be explicitly or implicitly typed.</span></span> <span data-ttu-id="2e3bd-2386">在显式类型化参数列表中，每个参数的类型都是显式声明的。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2386">In an explicitly typed parameter list, the type of each parameter is explicitly stated.</span></span> <span data-ttu-id="2e3bd-2387">在隐式类型的参数列表中，参数的类型是从发生匿名函数的上下文中推断出来的：具体而言，当匿名函数转换为兼容的委托类型或表达式目录树类型时，该类型提供 ([匿名函数转换](conversions.md#anonymous-function-conversions)) 的参数类型。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2387">In an implicitly typed parameter list, the types of the parameters are inferred from the context in which the anonymous function occurs—specifically, when the anonymous function is converted to a compatible delegate type or expression tree type, that type provides the parameter types ([Anonymous function conversions](conversions.md#anonymous-function-conversions)).</span></span>

<span data-ttu-id="2e3bd-2388">在具有单个隐式类型化参数的匿名函数中，可以从参数列表中省略括号。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2388">In an anonymous function with a single, implicitly typed parameter, the parentheses may be omitted from the parameter list.</span></span> <span data-ttu-id="2e3bd-2389">换句话说，形式的匿名函数</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2389">In other words, an anonymous function of the form</span></span>
```csharp
( param ) => expr
```
<span data-ttu-id="2e3bd-2390">可以缩写为</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2390">can be abbreviated to</span></span>
```csharp
param => expr
```

<span data-ttu-id="2e3bd-2391">*Anonymous_method_expression* 格式的匿名函数的参数列表是可选的。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2391">The parameter list of an anonymous function in the form of an *anonymous_method_expression* is optional.</span></span> <span data-ttu-id="2e3bd-2392">如果给定，则必须显式键入参数。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2392">If given, the parameters must be explicitly typed.</span></span> <span data-ttu-id="2e3bd-2393">如果不是，则匿名函数可以转换为具有任何不包含参数的参数列表的委托 `out` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2393">If not, the anonymous function is convertible to a delegate with any parameter list not containing `out` parameters.</span></span>

<span data-ttu-id="2e3bd-2394">匿名函数的 *块* 体可 ([终结点和可访问](statements.md#end-points-and-reachability) 性) ，除非该匿名函数发生在无法访问的语句中。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2394">A *block* body of an anonymous function is reachable ([End points and reachability](statements.md#end-points-and-reachability)) unless the anonymous function occurs inside an unreachable statement.</span></span>

<span data-ttu-id="2e3bd-2395">下面是匿名函数的一些示例：</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2395">Some examples of anonymous functions follow below:</span></span>

```csharp
x => x + 1                              // Implicitly typed, expression body
x => { return x + 1; }                  // Implicitly typed, statement body
(int x) => x + 1                        // Explicitly typed, expression body
(int x) => { return x + 1; }            // Explicitly typed, statement body
(x, y) => x * y                         // Multiple parameters
() => Console.WriteLine()               // No parameters
async (t1,t2) => await t1 + await t2    // Async
delegate (int x) { return x + 1; }      // Anonymous method expression
delegate { return 1 + 1; }              // Parameter list omitted
```

<span data-ttu-id="2e3bd-2396">*Lambda_expression* 和 *anonymous_method_expression* s 的行为是相同的，但以下情况除外：</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2396">The behavior of *lambda_expression* s and *anonymous_method_expression* s is the same except for the following points:</span></span>

*  <span data-ttu-id="2e3bd-2397">*anonymous_method_expression* 允许完全省略参数列表，则可以 convertibility 委托任何值参数列表的类型。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2397">*anonymous_method_expression* s permit the parameter list to be omitted entirely, yielding convertibility to delegate types of any list of value parameters.</span></span>
*  <span data-ttu-id="2e3bd-2398">*lambda_expression* 允许省略和推断参数类型，而 *anonymous_method_expression* 则需要显式声明参数类型。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2398">*lambda_expression* s permit parameter types to be omitted and inferred whereas *anonymous_method_expression* s require parameter types to be explicitly stated.</span></span>
*  <span data-ttu-id="2e3bd-2399">*Lambda_expression* 的主体可以是表达式或语句块，而 *anonymous_method_expression* 的主体必须是语句块。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2399">The body of a *lambda_expression* can be an expression or a statement block whereas the body of an *anonymous_method_expression* must be a statement block.</span></span>
*  <span data-ttu-id="2e3bd-2400">仅 *lambda_expression* s ([表达式树类型](types.md#expression-tree-types)) 转换为兼容的表达式树类型。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2400">Only *lambda_expression* s have conversions to compatible expression tree types ([Expression tree types](types.md#expression-tree-types)).</span></span>

### <a name="anonymous-function-signatures"></a><span data-ttu-id="2e3bd-2401">匿名函数签名</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2401">Anonymous function signatures</span></span>

<span data-ttu-id="2e3bd-2402">匿名函数的可选 *anonymous_function_signature* 定义匿名函数的名称和可选的形参类型。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2402">The optional *anonymous_function_signature* of an anonymous function defines the names and optionally the types of the formal parameters for the anonymous function.</span></span> <span data-ttu-id="2e3bd-2403">匿名函数的参数的作用域是 *anonymous_function_body*。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2403">The scope of the parameters of the anonymous function is the *anonymous_function_body*.</span></span> <span data-ttu-id="2e3bd-2404"> ([范围](basic-concepts.md#scopes)) 与参数列表一起使用 (如果给定) 匿名方法体构成声明空间 ([声明](basic-concepts.md#declarations)) 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2404">([Scopes](basic-concepts.md#scopes)) Together with the parameter list (if given) the anonymous-method-body constitutes a declaration space ([Declarations](basic-concepts.md#declarations)).</span></span> <span data-ttu-id="2e3bd-2405">因此，匿名函数的参数名称的编译时错误与本地变量、本地常量或参数的名称（其范围包含 *anonymous_method_expression* 或 *lambda_expression*）相匹配。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2405">It is thus a compile-time error for the name of a parameter of the anonymous function to match the name of a local variable, local constant or parameter whose scope includes the *anonymous_method_expression* or *lambda_expression*.</span></span>

<span data-ttu-id="2e3bd-2406">如果匿名函数有 *explicit_anonymous_function_signature*，则兼容的委托类型集和表达式树类型将被限制为具有相同顺序的相同参数类型和修饰符的类型。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2406">If an anonymous function has an *explicit_anonymous_function_signature*, then the set of compatible delegate types and expression tree types is restricted to those that have the same parameter types and modifiers in the same order.</span></span> <span data-ttu-id="2e3bd-2407">与方法组转换不同 ([方法组转换](conversions.md#method-group-conversions)) ，不支持匿名函数参数类型的逆变差。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2407">In contrast to method group conversions ([Method group conversions](conversions.md#method-group-conversions)), contra-variance of anonymous function parameter types is not supported.</span></span> <span data-ttu-id="2e3bd-2408">如果匿名函数没有 *anonymous_function_signature*，则兼容的委托类型集和表达式树类型将被限制为没有参数的类型 `out` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2408">If an anonymous function does not have an *anonymous_function_signature*, then the set of compatible delegate types and expression tree types is restricted to those that have no `out` parameters.</span></span>

<span data-ttu-id="2e3bd-2409">请注意， *anonymous_function_signature* 不能包含特性或参数数组。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2409">Note that an *anonymous_function_signature* cannot include attributes or a parameter array.</span></span> <span data-ttu-id="2e3bd-2410">尽管如此， *anonymous_function_signature* 可能与其参数列表包含参数数组的委托类型兼容。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2410">Nevertheless, an *anonymous_function_signature* may be compatible with a delegate type whose parameter list contains a parameter array.</span></span>

<span data-ttu-id="2e3bd-2411">另请注意，即使兼容，转换为表达式树类型也可能仍会在编译时失败 ([表达式树类型](types.md#expression-tree-types)) 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2411">Note also that conversion to an expression tree type, even if compatible, may still fail at compile-time ([Expression tree types](types.md#expression-tree-types)).</span></span>

### <a name="anonymous-function-bodies"></a><span data-ttu-id="2e3bd-2412">匿名函数体</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2412">Anonymous function bodies</span></span>

<span data-ttu-id="2e3bd-2413">匿名函数的主体 (*表达式* 或 *块*) 遵循以下规则：</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2413">The body (*expression* or *block*) of an anonymous function is subject to the following rules:</span></span>

*  <span data-ttu-id="2e3bd-2414">如果匿名函数包含签名，则在签名中指定的参数在正文中可用。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2414">If the anonymous function includes a signature, the parameters specified in the signature are available in the body.</span></span> <span data-ttu-id="2e3bd-2415">如果匿名函数没有签名，则可以将其转换为具有参数 ([匿名函数转换](conversions.md#anonymous-function-conversions)) 的委托类型或表达式类型，但不能在正文中访问这些参数。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2415">If the anonymous function has no signature it can be converted to a delegate type or expression type having parameters ([Anonymous function conversions](conversions.md#anonymous-function-conversions)), but the parameters cannot be accessed in the body.</span></span>
*  <span data-ttu-id="2e3bd-2416">除了 `ref` `out` 在签名中指定的或参数 (如果最近的封闭匿名函数有任何) ，则正文访问或参数的编译时错误 `ref` `out` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2416">Except for `ref` or `out` parameters specified in the signature (if any) of the nearest enclosing anonymous function, it is a compile-time error for the body to access a `ref` or `out` parameter.</span></span>
*  <span data-ttu-id="2e3bd-2417">当的类型 `this` 为结构类型时，主体将访问的编译时错误 `this` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2417">When the type of `this` is a struct type, it is a compile-time error for the body to access `this`.</span></span> <span data-ttu-id="2e3bd-2418">无论访问是显式 (如) 还是隐式 (，这一点都是如此， `this.x` `x` 其中 `x` ，是结构) 的实例成员。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2418">This is true whether the access is explicit (as in `this.x`) or implicit (as in `x` where `x` is an instance member of the struct).</span></span> <span data-ttu-id="2e3bd-2419">此规则只是禁止此类访问，不会影响成员查找是否会导致结构的成员。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2419">This rule simply prohibits such access and does not affect whether member lookup results in a member of the struct.</span></span>
*  <span data-ttu-id="2e3bd-2420">主体有权访问外部变量 () 匿名函数的 [外部变量](expressions.md#outer-variables) 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2420">The body has access to the outer variables ([Outer variables](expressions.md#outer-variables)) of the anonymous function.</span></span> <span data-ttu-id="2e3bd-2421">外部变量的访问权限将引用 *lambda_expression* 或 *anonymous_method_expression* 计算时处于活动状态的变量的实例 () [的匿名函数表达式的计算](expressions.md#evaluation-of-anonymous-function-expressions) 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2421">Access of an outer variable will reference the instance of the variable that is active at the time the *lambda_expression* or *anonymous_method_expression* is evaluated ([Evaluation of anonymous function expressions](expressions.md#evaluation-of-anonymous-function-expressions)).</span></span>
*  <span data-ttu-id="2e3bd-2422">如果正文包含的 `goto` 语句、 `break` 语句或 `continue` 语句的目标在正文外或包含的匿名函数的主体中，则这是编译时错误。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2422">It is a compile-time error for the body to contain a `goto` statement, `break` statement, or `continue` statement whose target is outside the body or within the body of a contained anonymous function.</span></span>
*  <span data-ttu-id="2e3bd-2423">`return`正文中的语句从最近的封闭匿名函数（而不是来自封闭函数成员）的调用返回控制权。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2423">A `return` statement in the body returns control from an invocation of the nearest enclosing anonymous function, not from the enclosing function member.</span></span> <span data-ttu-id="2e3bd-2424">语句中指定的表达式 `return` 必须可隐式转换为委托类型或表达式目录树类型的返回类型，该类型或表达式树类型最接近的包含 *lambda_expression* 或 *anonymous_method_expression* 将 ([匿名函数转换](conversions.md#anonymous-function-conversions)) 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2424">An expression specified in a `return` statement must be implicitly convertible to the return type of the delegate type or expression tree type to which the nearest enclosing *lambda_expression* or *anonymous_method_expression* is converted ([Anonymous function conversions](conversions.md#anonymous-function-conversions)).</span></span>

<span data-ttu-id="2e3bd-2425">明确指出，无论是否有任何方法可以执行匿名函数的块，而不是通过计算和调用 *lambda_expression* 或 *anonymous_method_expression*。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2425">It is explicitly unspecified whether there is any way to execute the block of an anonymous function other than through evaluation and invocation of the *lambda_expression* or *anonymous_method_expression*.</span></span> <span data-ttu-id="2e3bd-2426">特别是，编译器可以通过综合一个或多个命名方法或类型来选择实现匿名函数。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2426">In particular, the compiler may choose to implement an anonymous function by synthesizing one or more named methods or types.</span></span> <span data-ttu-id="2e3bd-2427">任何此类合成元素的名称必须是保留供编译器使用的形式。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2427">The names of any such synthesized elements must be of a form reserved for compiler use.</span></span>

### <a name="overload-resolution-and-anonymous-functions"></a><span data-ttu-id="2e3bd-2428">重载决策和匿名函数</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2428">Overload resolution and anonymous functions</span></span>

<span data-ttu-id="2e3bd-2429">自变量列表中的匿名函数参与类型推理和重载解析。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2429">Anonymous functions in an argument list participate in type inference and overload resolution.</span></span> <span data-ttu-id="2e3bd-2430">请参阅 [类型推理](expressions.md#type-inference) 和 [重载决策](expressions.md#overload-resolution) ，了解确切的规则。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2430">Please refer to [Type inference](expressions.md#type-inference) and [Overload resolution](expressions.md#overload-resolution) for the exact rules.</span></span>

<span data-ttu-id="2e3bd-2431">下面的示例演示匿名函数对重载决策的影响。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2431">The following example illustrates the effect of anonymous functions on overload resolution.</span></span>

```csharp
class ItemList<T>: List<T>
{
    public int Sum(Func<T,int> selector) {
        int sum = 0;
        foreach (T item in this) sum += selector(item);
        return sum;
    }

    public double Sum(Func<T,double> selector) {
        double sum = 0;
        foreach (T item in this) sum += selector(item);
        return sum;
    }
}
```

<span data-ttu-id="2e3bd-2432">`ItemList<T>`类有两种 `Sum` 方法。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2432">The `ItemList<T>` class has two `Sum` methods.</span></span> <span data-ttu-id="2e3bd-2433">每个都采用一个 `selector` 自变量，该自变量从列表项中提取要求和的值。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2433">Each takes a `selector` argument, which extracts the value to sum over from a list item.</span></span> <span data-ttu-id="2e3bd-2434">提取的值可以是 `int` 或 `double` ，结果的总和也可以是 `int` 或 `double` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2434">The extracted value can be either an `int` or a `double` and the resulting sum is likewise either an `int` or a `double`.</span></span>

<span data-ttu-id="2e3bd-2435">`Sum`例如，方法可用于计算按顺序列出的详细信息行的总和。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2435">The `Sum` methods could for example be used to compute sums from a list of detail lines in an order.</span></span>

```csharp
class Detail
{
    public int UnitCount;
    public double UnitPrice;
    ...
}

void ComputeSums() {
    ItemList<Detail> orderDetails = GetOrderDetails(...);
    int totalUnits = orderDetails.Sum(d => d.UnitCount);
    double orderTotal = orderDetails.Sum(d => d.UnitPrice * d.UnitCount);
    ...
}
```

<span data-ttu-id="2e3bd-2436">在的第一次调用中 `orderDetails.Sum` ，这两种 `Sum` 方法都适用，因为匿名函数 `d => d. UnitCount` 与 `Func<Detail,int>` 和兼容 `Func<Detail,double>` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2436">In the first invocation of `orderDetails.Sum`, both `Sum` methods are applicable because the anonymous function `d => d. UnitCount` is compatible with both `Func<Detail,int>` and `Func<Detail,double>`.</span></span> <span data-ttu-id="2e3bd-2437">但重载决策将选取第一 `Sum` 种方法，因为与 `Func<Detail,int>` 的转换更好于转换到的情况 `Func<Detail,double>` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2437">However, overload resolution picks the first `Sum` method because the conversion to `Func<Detail,int>` is better than the conversion to `Func<Detail,double>`.</span></span>

<span data-ttu-id="2e3bd-2438">在的第二次调用中 `orderDetails.Sum` ，只有第二个 `Sum` 方法适用，因为匿名函数会 `d => d.UnitPrice * d.UnitCount` 生成类型为的值 `double` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2438">In the second invocation of `orderDetails.Sum`, only the second `Sum` method is applicable because the anonymous function `d => d.UnitPrice * d.UnitCount` produces a value of type `double`.</span></span> <span data-ttu-id="2e3bd-2439">因此，重载决策 `Sum` 为该调用选择第二种方法。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2439">Thus, overload resolution picks the second `Sum` method for that invocation.</span></span>

### <a name="anonymous-functions-and-dynamic-binding"></a><span data-ttu-id="2e3bd-2440">匿名函数和动态绑定</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2440">Anonymous functions and dynamic binding</span></span>

<span data-ttu-id="2e3bd-2441">匿名函数不能是动态绑定操作的接收方、参数或操作数。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2441">An anonymous function cannot be a receiver, argument or operand of a dynamically bound operation.</span></span>

### <a name="outer-variables"></a><span data-ttu-id="2e3bd-2442">外部变量</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2442">Outer variables</span></span>

<span data-ttu-id="2e3bd-2443">任何本地变量、值参数或其范围包含 *lambda_expression* 或 *anonymous_method_expression* 的参数数组称为匿名函数的 ***外部变量*** 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2443">Any local variable, value parameter, or parameter array whose scope includes the *lambda_expression* or *anonymous_method_expression* is called an ***outer variable*** of the anonymous function.</span></span> <span data-ttu-id="2e3bd-2444">在类的实例函数成员中， `this` 值被视为值参数，并且是函数成员内包含的任何匿名函数的外部变量。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2444">In an instance function member of a class, the `this` value is considered a value parameter and is an outer variable of any anonymous function contained within the function member.</span></span>

#### <a name="captured-outer-variables"></a><span data-ttu-id="2e3bd-2445">捕获的外部变量</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2445">Captured outer variables</span></span>

<span data-ttu-id="2e3bd-2446">当某个外部变量被匿名函数引用时，该外部变量被视为已被匿名函数 ***捕获*** 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2446">When an outer variable is referenced by an anonymous function, the outer variable is said to have been ***captured*** by the anonymous function.</span></span> <span data-ttu-id="2e3bd-2447">通常情况下，局部变量的生存期仅限于执行它与)  ([局部变量](variables.md#local-variables) 关联的块或语句。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2447">Ordinarily, the lifetime of a local variable is limited to execution of the block or statement with which it is associated ([Local variables](variables.md#local-variables)).</span></span> <span data-ttu-id="2e3bd-2448">但是，已捕获的外部变量的生存期至少会进行扩展，直到从匿名函数创建的委托或表达式树变为可进行垃圾回收的条件。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2448">However, the lifetime of a captured outer variable is extended at least until the delegate or expression tree created from the anonymous function becomes eligible for garbage collection.</span></span>

<span data-ttu-id="2e3bd-2449">示例中</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2449">In the example</span></span>
```csharp
using System;

delegate int D();

class Test
{
    static D F() {
        int x = 0;
        D result = () => ++x;
        return result;
    }

    static void Main() {
        D d = F();
        Console.WriteLine(d());
        Console.WriteLine(d());
        Console.WriteLine(d());
    }
}
```
<span data-ttu-id="2e3bd-2450">局部变量 `x` 由匿名函数捕获，并至少扩展的生存期， `x` 直到从返回的委托 `F` 符合垃圾回收要求， (这在程序) 的最末尾之前不会发生。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2450">the local variable `x` is captured by the anonymous function, and the lifetime of `x` is extended at least until the delegate returned from `F` becomes eligible for garbage collection (which doesn't happen until the very end of the program).</span></span> <span data-ttu-id="2e3bd-2451">由于匿名函数的每个调用都在同一实例上运行 `x` ，因此该示例的输出为：</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2451">Since each invocation of the anonymous function operates on the same instance of `x`, the output of the example is:</span></span>
```console
1
2
3
```

<span data-ttu-id="2e3bd-2452">当匿名函数捕获本地变量或值参数时，本地变量或参数将不再被视为固定变量 ([固定和可移动的变量](unsafe-code.md#fixed-and-moveable-variables)) ，而是被视为可移动的变量。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2452">When a local variable or a value parameter is captured by an anonymous function, the local variable or parameter is no longer considered to be a fixed variable ([Fixed and moveable variables](unsafe-code.md#fixed-and-moveable-variables)), but is instead considered to be a moveable variable.</span></span> <span data-ttu-id="2e3bd-2453">因此 `unsafe` ，采用已捕获外部变量地址的任何代码都必须首先使用 `fixed` 语句来修复变量。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2453">Thus any `unsafe` code that takes the address of a captured outer variable must first use the `fixed` statement to fix the variable.</span></span>

<span data-ttu-id="2e3bd-2454">请注意，与 uncaptured 变量不同，捕获的局部变量可以同时公开给多个执行线程。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2454">Note that unlike an uncaptured variable, a captured local variable can be simultaneously exposed to multiple threads of execution.</span></span>

#### <a name="instantiation-of-local-variables"></a><span data-ttu-id="2e3bd-2455">局部变量的实例化</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2455">Instantiation of local variables</span></span>

<span data-ttu-id="2e3bd-2456">当执行进入变量的作用域时，本地变量被视为被 ***实例化*** 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2456">A local variable is considered to be ***instantiated*** when execution enters the scope of the variable.</span></span> <span data-ttu-id="2e3bd-2457">例如，在调用以下方法时，本地变量 `x` 将实例化并初始化三次，一次针对循环的每次迭代。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2457">For example, when the following method is invoked, the local variable `x` is instantiated and initialized three times—once for each iteration of the loop.</span></span>

```csharp
static void F() {
    for (int i = 0; i < 3; i++) {
        int x = i * 2 + 1;
        ...
    }
}
```

<span data-ttu-id="2e3bd-2458">但是，移动该循环外的声明会 `x` 导致单一实例化 `x` ：</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2458">However, moving the declaration of `x` outside the loop results in a single instantiation of `x`:</span></span>
```csharp
static void F() {
    int x;
    for (int i = 0; i < 3; i++) {
        x = i * 2 + 1;
        ...
    }
}
```

<span data-ttu-id="2e3bd-2459">如果未捕获，则无法准确观察局部变量的实例化频率（因为实例化的生存期是不连续的），因此每个实例化都可以只使用相同的存储位置。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2459">When not captured, there is no way to observe exactly how often a local variable is instantiated—because the lifetimes of the instantiations are disjoint, it is possible for each instantiation to simply use the same storage location.</span></span> <span data-ttu-id="2e3bd-2460">但是，当匿名函数捕获本地变量时，实例化的效果会变得很明显。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2460">However, when an anonymous function captures a local variable, the effects of instantiation become apparent.</span></span>

<span data-ttu-id="2e3bd-2461">示例</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2461">The example</span></span>
```csharp
using System;

delegate void D();

class Test
{
    static D[] F() {
        D[] result = new D[3];
        for (int i = 0; i < 3; i++) {
            int x = i * 2 + 1;
            result[i] = () => { Console.WriteLine(x); };
        }
        return result;
    }

    static void Main() {
        foreach (D d in F()) d();
    }
}
```
<span data-ttu-id="2e3bd-2462">生成输出：</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2462">produces the output:</span></span>
```console
1
3
5
```

<span data-ttu-id="2e3bd-2463">但是，当的声明 `x` 移到循环外时：</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2463">However, when the declaration of `x` is moved outside the loop:</span></span>
```csharp
static D[] F() {
    D[] result = new D[3];
    int x;
    for (int i = 0; i < 3; i++) {
        x = i * 2 + 1;
        result[i] = () => { Console.WriteLine(x); };
    }
    return result;
}
```
<span data-ttu-id="2e3bd-2464">输出为：</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2464">the output is:</span></span>
```console
5
5
5
```

<span data-ttu-id="2e3bd-2465">如果 for 循环声明迭代变量，则该变量本身被视为在循环外部声明。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2465">If a for-loop declares an iteration variable, that variable itself is considered to be declared outside of the loop.</span></span> <span data-ttu-id="2e3bd-2466">因此，如果将该示例更改为捕获迭代变量自身：</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2466">Thus, if the example is changed to capture the iteration variable itself:</span></span>

```csharp
static D[] F() {
    D[] result = new D[3];
    for (int i = 0; i < 3; i++) {
        result[i] = () => { Console.WriteLine(i); };
    }
    return result;
}
```
<span data-ttu-id="2e3bd-2467">只捕获迭代变量的一个实例，这将生成输出：</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2467">only one instance of the iteration variable is captured, which produces the output:</span></span>
```console
3
3
3
```

<span data-ttu-id="2e3bd-2468">匿名函数委托可以共享一些捕获的变量，但其他实例却有单独的实例。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2468">It is possible for anonymous function delegates to share some captured variables yet have separate instances of others.</span></span> <span data-ttu-id="2e3bd-2469">例如，如果 `F` 将更改为</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2469">For example, if `F` is changed to</span></span>
```csharp
static D[] F() {
    D[] result = new D[3];
    int x = 0;
    for (int i = 0; i < 3; i++) {
        int y = 0;
        result[i] = () => { Console.WriteLine("{0} {1}", ++x, ++y); };
    }
    return result;
}
```
<span data-ttu-id="2e3bd-2470">这三个委托捕获的实例与的实例 `x` 不同 `y` ，输出如下：</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2470">the three delegates capture the same instance of `x` but separate instances of `y`, and the output is:</span></span>
```console
1 1
2 1
3 1
```

<span data-ttu-id="2e3bd-2471">单独的匿名函数可以捕获外部变量的同一个实例。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2471">Separate anonymous functions can capture the same instance of an outer variable.</span></span> <span data-ttu-id="2e3bd-2472">在示例中：</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2472">In the example:</span></span>
```csharp
using System;

delegate void Setter(int value);

delegate int Getter();

class Test
{
    static void Main() {
        int x = 0;
        Setter s = (int value) => { x = value; };
        Getter g = () => { return x; };
        s(5);
        Console.WriteLine(g());
        s(10);
        Console.WriteLine(g());
    }
}
```
<span data-ttu-id="2e3bd-2473">这两个匿名函数捕获本地变量的同一个实例 `x` ，因此它们可以通过该变量 "进行通信"。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2473">the two anonymous functions capture the same instance of the local variable `x`, and they can thus "communicate" through that variable.</span></span> <span data-ttu-id="2e3bd-2474">该示例的输出为：</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2474">The output of the example is:</span></span>
```console
5
10
```

### <a name="evaluation-of-anonymous-function-expressions"></a><span data-ttu-id="2e3bd-2475">匿名函数表达式的计算</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2475">Evaluation of anonymous function expressions</span></span>

<span data-ttu-id="2e3bd-2476">匿名函数 `F` 必须始终 `D` `E` 直接或通过执行委托创建表达式来转换为委托类型或表达式树类型 `new D(F)` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2476">An anonymous function `F` must always be converted to a delegate type `D` or an expression tree type `E`, either directly or through the execution of a delegate creation expression `new D(F)`.</span></span> <span data-ttu-id="2e3bd-2477">此转换确定匿名函数的结果，如 [匿名函数转换](conversions.md#anonymous-function-conversions)中所述。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2477">This conversion determines the result of the anonymous function, as described in [Anonymous function conversions](conversions.md#anonymous-function-conversions).</span></span>

## <a name="query-expressions"></a><span data-ttu-id="2e3bd-2478">查询表达式</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2478">Query expressions</span></span>

<span data-ttu-id="2e3bd-2479">***查询表达式*** 为类似于关系和分层查询语言（如 SQL 和 XQuery）的查询提供了语言集成语法。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2479">***Query expressions*** provide a language integrated syntax for queries that is similar to relational and hierarchical query languages such as SQL and XQuery.</span></span>

```antlr
query_expression
    : from_clause query_body
    ;

from_clause
    : 'from' type? identifier 'in' expression
    ;

query_body
    : query_body_clauses? select_or_group_clause query_continuation?
    ;

query_body_clauses
    : query_body_clause
    | query_body_clauses query_body_clause
    ;

query_body_clause
    : from_clause
    | let_clause
    | where_clause
    | join_clause
    | join_into_clause
    | orderby_clause
    ;

let_clause
    : 'let' identifier '=' expression
    ;

where_clause
    : 'where' boolean_expression
    ;

join_clause
    : 'join' type? identifier 'in' expression 'on' expression 'equals' expression
    ;

join_into_clause
    : 'join' type? identifier 'in' expression 'on' expression 'equals' expression 'into' identifier
    ;

orderby_clause
    : 'orderby' orderings
    ;

orderings
    : ordering (',' ordering)*
    ;

ordering
    : expression ordering_direction?
    ;

ordering_direction
    : 'ascending'
    | 'descending'
    ;

select_or_group_clause
    : select_clause
    | group_clause
    ;

select_clause
    : 'select' expression
    ;

group_clause
    : 'group' expression 'by' expression
    ;

query_continuation
    : 'into' identifier query_body
    ;
```

<span data-ttu-id="2e3bd-2480">查询表达式以 `from` 子句开头，以 `select` 或子句结尾 `group` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2480">A query expression begins with a `from` clause and ends with either a `select` or `group` clause.</span></span> <span data-ttu-id="2e3bd-2481">初始 `from` 子句后面可以跟零个或多个 `from` 、 `let` 、 `where` `join` 或 `orderby` 子句。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2481">The initial `from` clause can be followed by zero or more `from`, `let`, `where`, `join` or `orderby` clauses.</span></span> <span data-ttu-id="2e3bd-2482">每个 `from` 子句都是一个生成 \***范围变量** 的生成器，该变量范围为 _ *_序列_* \* 的元素。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2482">Each `from` clause is a generator introducing a ***range variable** _ which ranges over the elements of a _*_sequence_\*\*.</span></span> <span data-ttu-id="2e3bd-2483">每个 `let` 子句都引入一个范围变量，表示通过以前的范围变量计算得出的值。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2483">Each `let` clause introduces a range variable representing a value computed by means of previous range variables.</span></span> <span data-ttu-id="2e3bd-2484">每个 `where` 子句都是一个从结果中排除项的筛选器。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2484">Each `where` clause is a filter that excludes items from the result.</span></span> <span data-ttu-id="2e3bd-2485">每个 `join` 子句将源序列的指定键与另一个序列的键进行比较，从而生成匹配对。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2485">Each `join` clause compares specified keys of the source sequence with keys of another sequence, yielding matching pairs.</span></span> <span data-ttu-id="2e3bd-2486">每个 `orderby` 子句都根据指定的条件对项进行重新排序。Final `select` 或 `group` 子句根据范围变量指定结果的形状。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2486">Each `orderby` clause reorders items according to specified criteria.The final `select` or `group` clause specifies the shape of the result in terms of the range variables.</span></span> <span data-ttu-id="2e3bd-2487">最后， `into` 子句可用于将一个查询的结果视为后续查询中的生成器来 "拼接" 查询。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2487">Finally, an `into` clause can be used to "splice" queries by treating the results of one query as a generator in a subsequent query.</span></span>

### <a name="ambiguities-in-query-expressions"></a><span data-ttu-id="2e3bd-2488">查询表达式中的多义性</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2488">Ambiguities in query expressions</span></span>

<span data-ttu-id="2e3bd-2489">查询表达式包含许多 "上下文关键字"，即在给定上下文中具有特殊意义的标识符。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2489">Query expressions contain a number of "contextual keywords", i.e., identifiers that have special meaning in a given context.</span></span> <span data-ttu-id="2e3bd-2490">具体而言，包括、、、 `from` `where` `join` `on` 、、、、、、、 `equals` `into` `let` `orderby` `ascending` `descending` `select` `group` 和 `by` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2490">Specifically these are `from`, `where`, `join`, `on`, `equals`, `into`, `let`, `orderby`, `ascending`, `descending`, `select`, `group` and `by`.</span></span> <span data-ttu-id="2e3bd-2491">为了避免因混合使用这些标识符作为关键字或简单名称而导致的查询表达式中出现歧义，在查询表达式中的任何位置出现时，这些标识符都被视为关键字。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2491">In order to avoid ambiguities in query expressions caused by mixed use of these identifiers as keywords or simple names, these identifiers are considered keywords when occurring anywhere within a query expression.</span></span>

<span data-ttu-id="2e3bd-2492">出于此目的，查询表达式是以 " `from identifier` " 后跟任何标记（除 " `;` "、"" `=` 或 "" 除外）开头的任何表达式 `,` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2492">For this purpose, a query expression is any expression that starts with "`from identifier`" followed by any token except "`;`", "`=`" or "`,`".</span></span>

<span data-ttu-id="2e3bd-2493">为了在查询表达式中使用这些字词作为标识符，可以使用 " `@` " ([标识符](lexical-structure.md#identifiers)) 作为前缀。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2493">In order to use these words as identifiers within a query expression, they can be prefixed with "`@`" ([Identifiers](lexical-structure.md#identifiers)).</span></span>

### <a name="query-expression-translation"></a><span data-ttu-id="2e3bd-2494">查询表达式转换</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2494">Query expression translation</span></span>

<span data-ttu-id="2e3bd-2495">C # 语言不指定查询表达式的执行语义。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2495">The C# language does not specify the execution semantics of query expressions.</span></span> <span data-ttu-id="2e3bd-2496">相反，查询表达式会转换为方法的调用，这些方法遵循 *查询表达式模式* ([查询表达式模式](expressions.md#the-query-expression-pattern)) 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2496">Rather, query expressions are translated into invocations of methods that adhere to the *query expression pattern* ([The query expression pattern](expressions.md#the-query-expression-pattern)).</span></span> <span data-ttu-id="2e3bd-2497">具体而言，查询表达式被转换为名为、、、、、、、、、和的方法调用 `Where` `Select` `SelectMany` `Join` `GroupJoin` `OrderBy` `OrderByDescending` `ThenBy` `ThenByDescending` `GroupBy` `Cast` 。这些方法应具有特定的签名和结果类型，如 [查询表达式模式](expressions.md#the-query-expression-pattern)中所述。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2497">Specifically, query expressions are translated into invocations of methods named `Where`, `Select`, `SelectMany`, `Join`, `GroupJoin`, `OrderBy`, `OrderByDescending`, `ThenBy`, `ThenByDescending`, `GroupBy`, and `Cast`.These methods are expected to have particular signatures and result types, as described in [The query expression pattern](expressions.md#the-query-expression-pattern).</span></span> <span data-ttu-id="2e3bd-2498">这些方法可以是要查询的对象的实例方法或对象外部的扩展方法，并实现查询的实际执行。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2498">These methods can be instance methods of the object being queried or extension methods that are external to the object, and they implement the actual execution of the query.</span></span>

<span data-ttu-id="2e3bd-2499">从查询表达式转换为方法调用是在执行任何类型绑定或重载决策之前发生的语法映射。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2499">The translation from query expressions to method invocations is a syntactic mapping that occurs before any type binding or overload resolution has been performed.</span></span> <span data-ttu-id="2e3bd-2500">转换确保语法正确，但不保证生成语义正确的 c # 代码。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2500">The translation is guaranteed to be syntactically correct, but it is not guaranteed to produce semantically correct C# code.</span></span> <span data-ttu-id="2e3bd-2501">转换查询表达式后，生成的方法调用将作为常规方法调用进行处理，这可能会导致错误（例如，如果方法不存在，如果参数具有错误的类型），或者如果方法是泛型，则类型推理将失败。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2501">Following translation of query expressions, the resulting method invocations are processed as regular method invocations, and this may in turn uncover errors, for example if the methods do not exist, if arguments have wrong types, or if the methods are generic and type inference fails.</span></span>

<span data-ttu-id="2e3bd-2502">通过重复应用以下翻译来处理查询表达式，直到不能进一步缩减。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2502">A query expression is processed by repeatedly applying the following translations until no further reductions are possible.</span></span> <span data-ttu-id="2e3bd-2503">翻译按照应用程序顺序列出：每个部分都假设之前部分中的翻译已进行了详尽的操作，一旦完成，就不会再在处理同一查询表达式时再次使用部分。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2503">The translations are listed in order of application: each section assumes that the translations in the preceding sections have been performed exhaustively, and once exhausted, a section will not later be revisited in the processing of the same query expression.</span></span>

<span data-ttu-id="2e3bd-2504">不允许在查询表达式中分配范围变量。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2504">Assignment to range variables is not allowed in query expressions.</span></span> <span data-ttu-id="2e3bd-2505">但允许 c # 实现并非始终强制实施此限制，因为有时可能不能使用此处提供的语法转换方案。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2505">However a C# implementation is permitted to not always enforce this restriction, since this may sometimes not be possible with the syntactic translation scheme presented here.</span></span>

<span data-ttu-id="2e3bd-2506">某些翻译注入范围变量，其中包含由表示的透明标识符 `*` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2506">Certain translations inject range variables with transparent identifiers denoted by `*`.</span></span> <span data-ttu-id="2e3bd-2507">[透明标识符中进一步](expressions.md#transparent-identifiers)讨论了透明标识符的特殊属性。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2507">The special properties of transparent identifiers are discussed further in [Transparent identifiers](expressions.md#transparent-identifiers).</span></span>

#### <a name="select-and-groupby-clauses-with-continuations"></a><span data-ttu-id="2e3bd-2508">带有延续的 Select 和 groupby 子句</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2508">Select and groupby clauses with continuations</span></span>

<span data-ttu-id="2e3bd-2509">具有延续的查询表达式</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2509">A query expression with a continuation</span></span>
```csharp
from ... into x ...
```
<span data-ttu-id="2e3bd-2510">转换为</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2510">is translated into</span></span>
```csharp
from x in ( from ... ) ...
```

<span data-ttu-id="2e3bd-2511">以下部分中的翻译假设查询没有 `into` 延续任务。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2511">The translations in the following sections assume that queries have no `into` continuations.</span></span>

<span data-ttu-id="2e3bd-2512">示例</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2512">The example</span></span>
```csharp
from c in customers
group c by c.Country into g
select new { Country = g.Key, CustCount = g.Count() }
```
<span data-ttu-id="2e3bd-2513">转换为</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2513">is translated into</span></span>
```csharp
from g in
    from c in customers
    group c by c.Country
select new { Country = g.Key, CustCount = g.Count() }
```
<span data-ttu-id="2e3bd-2514">的最终转换是</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2514">the final translation of which is</span></span>
```csharp
customers.
GroupBy(c => c.Country).
Select(g => new { Country = g.Key, CustCount = g.Count() })
```

#### <a name="explicit-range-variable-types"></a><span data-ttu-id="2e3bd-2515">显式范围变量类型</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2515">Explicit range variable types</span></span>

<span data-ttu-id="2e3bd-2516">`from`显式指定范围变量类型的子句</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2516">A `from` clause that explicitly specifies a range variable type</span></span>
```csharp
from T x in e
```
<span data-ttu-id="2e3bd-2517">转换为</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2517">is translated into</span></span>
```csharp
from x in ( e ) . Cast < T > ( )
```

<span data-ttu-id="2e3bd-2518">`join`显式指定范围变量类型的子句</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2518">A `join` clause that explicitly specifies a range variable type</span></span>
```csharp
join T x in e on k1 equals k2
```
<span data-ttu-id="2e3bd-2519">转换为</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2519">is translated into</span></span>
```csharp
join x in ( e ) . Cast < T > ( ) on k1 equals k2
```

<span data-ttu-id="2e3bd-2520">以下部分中的翻译假设查询没有显式范围变量类型。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2520">The translations in the following sections assume that queries have no explicit range variable types.</span></span>

<span data-ttu-id="2e3bd-2521">示例</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2521">The example</span></span>
```csharp
from Customer c in customers
where c.City == "London"
select c
```
<span data-ttu-id="2e3bd-2522">转换为</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2522">is translated into</span></span>
```csharp
from c in customers.Cast<Customer>()
where c.City == "London"
select c
```
<span data-ttu-id="2e3bd-2523">的最终转换是</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2523">the final translation of which is</span></span>
```csharp
customers.
Cast<Customer>().
Where(c => c.City == "London")
```

<span data-ttu-id="2e3bd-2524">显式范围变量类型用于查询实现非泛型接口的集合 `IEnumerable` ，但不能用于泛型 `IEnumerable<T>` 接口。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2524">Explicit range variable types are useful for querying collections that implement the non-generic `IEnumerable` interface, but not the generic `IEnumerable<T>` interface.</span></span> <span data-ttu-id="2e3bd-2525">在上述示例中，如果的类型为，则会出现这种情况 `customers` `ArrayList` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2525">In the example above, this would be the case if `customers` were of type `ArrayList`.</span></span>

#### <a name="degenerate-query-expressions"></a><span data-ttu-id="2e3bd-2526">退化查询表达式</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2526">Degenerate query expressions</span></span>

<span data-ttu-id="2e3bd-2527">格式为的查询表达式</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2527">A query expression of the form</span></span>
```csharp
from x in e select x
```
<span data-ttu-id="2e3bd-2528">转换为</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2528">is translated into</span></span>
```csharp
( e ) . Select ( x => x )
```

<span data-ttu-id="2e3bd-2529">示例</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2529">The example</span></span>
```csharp
from c in customers
select c
```
<span data-ttu-id="2e3bd-2530">转换为</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2530">is translated into</span></span>
```csharp
customers.Select(c => c)
```

<span data-ttu-id="2e3bd-2531">退化查询表达式是完全选择源中的元素的表达式。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2531">A degenerate query expression is one that trivially selects the elements of the source.</span></span> <span data-ttu-id="2e3bd-2532">转换的更晚阶段会删除其他翻译步骤引入的退化查询，方法是将其替换为其源。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2532">A later phase of the translation removes degenerate queries introduced by other translation steps by replacing them with their source.</span></span> <span data-ttu-id="2e3bd-2533">但要确保查询表达式的结果绝不是源对象本身，这一点很重要，因为这会将源的类型和标识显示到查询的客户端。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2533">It is important however to ensure that the result of a query expression is never the source object itself, as that would reveal the type and identity of the source to the client of the query.</span></span> <span data-ttu-id="2e3bd-2534">因此，此步骤通过显式调用源来保护直接在源代码中编写的退化查询 `Select` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2534">Therefore this step protects degenerate queries written directly in source code by explicitly calling `Select` on the source.</span></span> <span data-ttu-id="2e3bd-2535">然后由 `Select` 和其他查询运算符的实现者来确保这些方法从不返回源对象本身。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2535">It is then up to the implementers of `Select` and other query operators to ensure that these methods never return the source object itself.</span></span>

#### <a name="from-let-where-join-and-orderby-clauses"></a><span data-ttu-id="2e3bd-2536">From、let、where、join 和 orderby 子句</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2536">From, let, where, join and orderby clauses</span></span>

<span data-ttu-id="2e3bd-2537">包含第二个 `from` 子句后跟子句的查询 `select` 表达式</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2537">A query expression with a second `from` clause followed by a `select` clause</span></span>
```csharp
from x1 in e1
from x2 in e2
select v
```
<span data-ttu-id="2e3bd-2538">转换为</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2538">is translated into</span></span>
```csharp
( e1 ) . SelectMany( x1 => e2 , ( x1 , x2 ) => v )
```

<span data-ttu-id="2e3bd-2539">一个查询表达式，该表达式的第二个 `from` 子句后跟除子句之外的其他内容 `select` ：</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2539">A query expression with a second `from` clause followed by something other than a `select` clause:</span></span>

```csharp
from x1 in e1
from x2 in e2
...
```
<span data-ttu-id="2e3bd-2540">转换为</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2540">is translated into</span></span>
```csharp
from * in ( e1 ) . SelectMany( x1 => e2 , ( x1 , x2 ) => new { x1 , x2 } )
...
```

<span data-ttu-id="2e3bd-2541">带有子句的查询表达式 `let`</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2541">A query expression with a `let` clause</span></span>
```csharp
from x in e
let y = f
...
```
<span data-ttu-id="2e3bd-2542">转换为</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2542">is translated into</span></span>
```csharp
from * in ( e ) . Select ( x => new { x , y = f } )
...
```

<span data-ttu-id="2e3bd-2543">带有子句的查询表达式 `where`</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2543">A query expression with a `where` clause</span></span>
```csharp
from x in e
where f
...
```
<span data-ttu-id="2e3bd-2544">转换为</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2544">is translated into</span></span>
```csharp
from x in ( e ) . Where ( x => f )
...
```

<span data-ttu-id="2e3bd-2545">带有子句的查询表达式， `join` 后面没有 `into` `select` 子句</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2545">A query expression with a `join` clause without an `into` followed by a `select` clause</span></span>
```csharp
from x1 in e1
join x2 in e2 on k1 equals k2
select v
```
<span data-ttu-id="2e3bd-2546">转换为</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2546">is translated into</span></span>
```csharp
( e1 ) . Join( e2 , x1 => k1 , x2 => k2 , ( x1 , x2 ) => v )
```

<span data-ttu-id="2e3bd-2547">带有子句的查询表达式， `join` 其后不 `into` 跟 `select` 子句</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2547">A query expression with a `join` clause without an `into` followed by something other than a `select` clause</span></span>
```csharp
from x1 in e1
join x2 in e2 on k1 equals k2
...
```
<span data-ttu-id="2e3bd-2548">转换为</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2548">is translated into</span></span>
```csharp
from * in ( e1 ) . Join( e2 , x1 => k1 , x2 => k2 , ( x1 , x2 ) => new { x1 , x2 })
...
```

<span data-ttu-id="2e3bd-2549">带有子句的查询表达式， `join` `into` 后面跟有 `select` 子句</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2549">A query expression with a `join` clause with an `into` followed by a `select` clause</span></span>
```csharp
from x1 in e1
join x2 in e2 on k1 equals k2 into g
select v
```
<span data-ttu-id="2e3bd-2550">转换为</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2550">is translated into</span></span>
```csharp
( e1 ) . GroupJoin( e2 , x1 => k1 , x2 => k2 , ( x1 , g ) => v )
```

<span data-ttu-id="2e3bd-2551">带有子句的查询表达式， `join` 后面跟有子句以外的 `into` 其他内容 `select`</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2551">A query expression with a `join` clause with an `into` followed by something other than a `select` clause</span></span>
```csharp
from x1 in e1
join x2 in e2 on k1 equals k2 into g
...
```
<span data-ttu-id="2e3bd-2552">转换为</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2552">is translated into</span></span>
```csharp
from * in ( e1 ) . GroupJoin( e2 , x1 => k1 , x2 => k2 , ( x1 , g ) => new { x1 , g })
...
```

<span data-ttu-id="2e3bd-2553">带有子句的查询表达式 `orderby`</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2553">A query expression with an `orderby` clause</span></span>
```csharp
from x in e
orderby k1 , k2 , ..., kn
...
```
<span data-ttu-id="2e3bd-2554">转换为</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2554">is translated into</span></span>
```csharp
from x in ( e ) . 
OrderBy ( x => k1 ) . 
ThenBy ( x => k2 ) .
... .
ThenBy ( x => kn )
...
```

<span data-ttu-id="2e3bd-2555">如果排序子句指定了 `descending` 方向指示器， `OrderByDescending` 则改为生成或的调用 `ThenByDescending` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2555">If an ordering clause specifies a `descending` direction indicator, an invocation of `OrderByDescending` or `ThenByDescending` is produced instead.</span></span>

<span data-ttu-id="2e3bd-2556">以下翻译假设 `let` `where` `join` `orderby` `from` 每个查询表达式中没有、或子句，且不超过一个初始子句。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2556">The following translations assume that there are no `let`, `where`, `join` or `orderby` clauses, and no more than the one initial `from` clause in each query expression.</span></span>

<span data-ttu-id="2e3bd-2557">示例</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2557">The example</span></span>
```csharp
from c in customers
from o in c.Orders
select new { c.Name, o.OrderID, o.Total }
```
<span data-ttu-id="2e3bd-2558">转换为</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2558">is translated into</span></span>
```csharp
customers.
SelectMany(c => c.Orders,
     (c,o) => new { c.Name, o.OrderID, o.Total }
)
```

<span data-ttu-id="2e3bd-2559">示例</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2559">The example</span></span>
```csharp
from c in customers
from o in c.Orders
orderby o.Total descending
select new { c.Name, o.OrderID, o.Total }
```
<span data-ttu-id="2e3bd-2560">转换为</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2560">is translated into</span></span>
```csharp
from * in customers.
    SelectMany(c => c.Orders, (c,o) => new { c, o })
orderby o.Total descending
select new { c.Name, o.OrderID, o.Total }
```
<span data-ttu-id="2e3bd-2561">的最终转换是</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2561">the final translation of which is</span></span>
```csharp
customers.
SelectMany(c => c.Orders, (c,o) => new { c, o }).
OrderByDescending(x => x.o.Total).
Select(x => new { x.c.Name, x.o.OrderID, x.o.Total })
```
<span data-ttu-id="2e3bd-2562">其中 `x` ，是编译器生成的标识符，否则不可见且不可访问。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2562">where `x` is a compiler generated identifier that is otherwise invisible and inaccessible.</span></span>

<span data-ttu-id="2e3bd-2563">示例</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2563">The example</span></span>
```csharp
from o in orders
let t = o.Details.Sum(d => d.UnitPrice * d.Quantity)
where t >= 1000
select new { o.OrderID, Total = t }
```
<span data-ttu-id="2e3bd-2564">转换为</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2564">is translated into</span></span>
```csharp
from * in orders.
    Select(o => new { o, t = o.Details.Sum(d => d.UnitPrice * d.Quantity) })
where t >= 1000 
select new { o.OrderID, Total = t }
```
<span data-ttu-id="2e3bd-2565">的最终转换是</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2565">the final translation of which is</span></span>
```csharp
orders.
Select(o => new { o, t = o.Details.Sum(d => d.UnitPrice * d.Quantity) }).
Where(x => x.t >= 1000).
Select(x => new { x.o.OrderID, Total = x.t })
```
<span data-ttu-id="2e3bd-2566">其中 `x` ，是编译器生成的标识符，否则不可见且不可访问。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2566">where `x` is a compiler generated identifier that is otherwise invisible and inaccessible.</span></span>

<span data-ttu-id="2e3bd-2567">示例</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2567">The example</span></span>
```csharp
from c in customers
join o in orders on c.CustomerID equals o.CustomerID
select new { c.Name, o.OrderDate, o.Total }
```
<span data-ttu-id="2e3bd-2568">转换为</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2568">is translated into</span></span>
```csharp
customers.Join(orders, c => c.CustomerID, o => o.CustomerID,
    (c, o) => new { c.Name, o.OrderDate, o.Total })
```

<span data-ttu-id="2e3bd-2569">示例</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2569">The example</span></span>
```csharp
from c in customers
join o in orders on c.CustomerID equals o.CustomerID into co
let n = co.Count()
where n >= 10
select new { c.Name, OrderCount = n }
```
<span data-ttu-id="2e3bd-2570">转换为</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2570">is translated into</span></span>
```csharp
from * in customers.
    GroupJoin(orders, c => c.CustomerID, o => o.CustomerID,
        (c, co) => new { c, co })
let n = co.Count()
where n >= 10 
select new { c.Name, OrderCount = n }
```
<span data-ttu-id="2e3bd-2571">的最终转换是</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2571">the final translation of which is</span></span>
```csharp
customers.
GroupJoin(orders, c => c.CustomerID, o => o.CustomerID,
    (c, co) => new { c, co }).
Select(x => new { x, n = x.co.Count() }).
Where(y => y.n >= 10).
Select(y => new { y.x.c.Name, OrderCount = y.n)
```
<span data-ttu-id="2e3bd-2572">where `x` 和 `y` 是编译器生成的标识符，这些标识符不可见且不可访问。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2572">where `x` and `y` are compiler generated identifiers that are otherwise invisible and inaccessible.</span></span>

<span data-ttu-id="2e3bd-2573">示例</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2573">The example</span></span>
```csharp
from o in orders
orderby o.Customer.Name, o.Total descending
select o
```
<span data-ttu-id="2e3bd-2574">具有最终翻译</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2574">has the final translation</span></span>
```csharp
orders.
OrderBy(o => o.Customer.Name).
ThenByDescending(o => o.Total)
```

#### <a name="select-clauses"></a><span data-ttu-id="2e3bd-2575">Select 子句</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2575">Select clauses</span></span>

<span data-ttu-id="2e3bd-2576">格式为的查询表达式</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2576">A query expression of the form</span></span>
```csharp
from x in e select v
```
<span data-ttu-id="2e3bd-2577">转换为</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2577">is translated into</span></span>
```csharp
( e ) . Select ( x => v )
```
<span data-ttu-id="2e3bd-2578">除了 v 是标识符 x 时，转换只是</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2578">except when v is the identifier x, the translation is simply</span></span>
```csharp
( e )
```

<span data-ttu-id="2e3bd-2579">例如</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2579">For example</span></span>
```csharp
from c in customers.Where(c => c.City == "London")
select c
```
<span data-ttu-id="2e3bd-2580">只转换为</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2580">is simply translated into</span></span>
```csharp
customers.Where(c => c.City == "London")
```

#### <a name="groupby-clauses"></a><span data-ttu-id="2e3bd-2581">Groupby 子句</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2581">Groupby clauses</span></span>

<span data-ttu-id="2e3bd-2582">格式为的查询表达式</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2582">A query expression of the form</span></span>
```csharp
from x in e group v by k
```
<span data-ttu-id="2e3bd-2583">转换为</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2583">is translated into</span></span>
```csharp
( e ) . GroupBy ( x => k , x => v )
```
<span data-ttu-id="2e3bd-2584">除非 v 是标识符 x，否则转换为</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2584">except when v is the identifier x, the translation is</span></span>
```csharp
( e ) . GroupBy ( x => k )
```

<span data-ttu-id="2e3bd-2585">示例</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2585">The example</span></span>
```csharp
from c in customers
group c.Name by c.Country
```
<span data-ttu-id="2e3bd-2586">转换为</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2586">is translated into</span></span>
```csharp
customers.
GroupBy(c => c.Country, c => c.Name)
```

#### <a name="transparent-identifiers"></a><span data-ttu-id="2e3bd-2587">透明标识符</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2587">Transparent identifiers</span></span>

<span data-ttu-id="2e3bd-2588">某些翻译注入范围变量，其中包含 \***透明标识符** _ 表示 `_` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2588">Certain translations inject range variables with \***transparent identifiers** _ denoted by `_`.</span></span> <span data-ttu-id="2e3bd-2589">透明标识符不是正确的语言功能;它们仅作为查询表达式转换过程中的一个中间步骤存在。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2589">Transparent identifiers are not a proper language feature; they exist only as an intermediate step in the query expression translation process.</span></span>

<span data-ttu-id="2e3bd-2590">当查询转换注入透明标识符时，进一步的转换步骤会将透明标识符传播到匿名函数和匿名对象初始值设定项。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2590">When a query translation injects a transparent identifier, further translation steps propagate the transparent identifier into anonymous functions and anonymous object initializers.</span></span> <span data-ttu-id="2e3bd-2591">在这些上下文中，透明标识符具有以下行为：</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2591">In those contexts, transparent identifiers have the following behavior:</span></span>

*  <span data-ttu-id="2e3bd-2592">当透明标识符作为匿名函数中的参数出现时，关联的匿名类型的成员将自动出现在匿名函数主体的范围内。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2592">When a transparent identifier occurs as a parameter in an anonymous function, the members of the associated anonymous type are automatically in scope in the body of the anonymous function.</span></span>
*  <span data-ttu-id="2e3bd-2593">当具有透明标识符的成员位于范围内时，该成员的成员也会在范围内。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2593">When a member with a transparent identifier is in scope, the members of that member are in scope as well.</span></span>
*  <span data-ttu-id="2e3bd-2594">当透明标识符作为匿名对象初始值设定项中的成员声明符出现时，它会引入一个具有透明标识符的成员。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2594">When a transparent identifier occurs as a member declarator in an anonymous object initializer, it introduces a member with a transparent identifier.</span></span>
*  <span data-ttu-id="2e3bd-2595">在上面所述的翻译步骤中，透明标识符始终与匿名类型一起引入，目的是将多个范围变量捕获为单个对象的成员。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2595">In the translation steps described above, transparent identifiers are always introduced together with anonymous types, with the intent of capturing multiple range variables as members of a single object.</span></span> <span data-ttu-id="2e3bd-2596">允许 c # 的实现使用与匿名类型不同的机制将多个范围变量组合在一起。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2596">An implementation of C# is permitted to use a different mechanism than anonymous types to group together multiple range variables.</span></span> <span data-ttu-id="2e3bd-2597">以下翻译示例假设使用匿名类型，并演示如何将透明标识符转换为。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2597">The following translation examples assume that anonymous types are used, and show how transparent identifiers can be translated away.</span></span>

<span data-ttu-id="2e3bd-2598">示例</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2598">The example</span></span>
```csharp
from c in customers
from o in c.Orders
orderby o.Total descending
select new { c.Name, o.Total }
```
<span data-ttu-id="2e3bd-2599">转换为</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2599">is translated into</span></span>
```csharp
from * in customers.
    SelectMany(c => c.Orders, (c,o) => new { c, o })
orderby o.Total descending
select new { c.Name, o.Total }
```

<span data-ttu-id="2e3bd-2600">这会进一步转换为</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2600">which is further translated into</span></span>
```csharp
customers.
SelectMany(c => c.Orders, (c,o) => new { c, o }).
OrderByDescending(* => o.Total).
Select(* => new { c.Name, o.Total })
```
<span data-ttu-id="2e3bd-2601">当清除透明标识符时，它等效于</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2601">which, when transparent identifiers are erased, is equivalent to</span></span>
```csharp
customers.
SelectMany(c => c.Orders, (c,o) => new { c, o }).
OrderByDescending(x => x.o.Total).
Select(x => new { x.c.Name, x.o.Total })
```
<span data-ttu-id="2e3bd-2602">其中 `x` ，是编译器生成的标识符，否则不可见且不可访问。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2602">where `x` is a compiler generated identifier that is otherwise invisible and inaccessible.</span></span>

<span data-ttu-id="2e3bd-2603">示例</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2603">The example</span></span>
```csharp
from c in customers
join o in orders on c.CustomerID equals o.CustomerID
join d in details on o.OrderID equals d.OrderID
join p in products on d.ProductID equals p.ProductID
select new { c.Name, o.OrderDate, p.ProductName }
```
<span data-ttu-id="2e3bd-2604">转换为</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2604">is translated into</span></span>
```csharp
from * in customers.
    Join(orders, c => c.CustomerID, o => o.CustomerID, 
        (c, o) => new { c, o })
join d in details on o.OrderID equals d.OrderID
join p in products on d.ProductID equals p.ProductID
select new { c.Name, o.OrderDate, p.ProductName }
```
<span data-ttu-id="2e3bd-2605">这会进一步减小到</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2605">which is further reduced to</span></span>
```csharp
customers.
Join(orders, c => c.CustomerID, o => o.CustomerID, (c, o) => new { c, o }).
Join(details, * => o.OrderID, d => d.OrderID, (*, d) => new { *, d }).
Join(products, * => d.ProductID, p => p.ProductID, (*, p) => new { *, p }).
Select(* => new { c.Name, o.OrderDate, p.ProductName })
```
<span data-ttu-id="2e3bd-2606">的最终转换是</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2606">the final translation of which is</span></span>
```csharp
customers.
Join(orders, c => c.CustomerID, o => o.CustomerID,
    (c, o) => new { c, o }).
Join(details, x => x.o.OrderID, d => d.OrderID,
    (x, d) => new { x, d }).
Join(products, y => y.d.ProductID, p => p.ProductID,
    (y, p) => new { y, p }).
Select(z => new { z.y.x.c.Name, z.y.x.o.OrderDate, z.p.ProductName })
```
<span data-ttu-id="2e3bd-2607">其中 `x` ，、 `y` 和 `z` 是编译器生成的标识符，这些标识符不可见且不可访问。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2607">where `x`, `y`, and `z` are compiler generated identifiers that are otherwise invisible and inaccessible.</span></span>

### <a name="the-query-expression-pattern"></a><span data-ttu-id="2e3bd-2608">查询表达式模式</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2608">The query expression pattern</span></span>

<span data-ttu-id="2e3bd-2609">***查询表达式模式*** 建立了一种方法，这些方法可实现类型以支持查询表达式。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2609">The ***Query expression pattern*** establishes a pattern of methods that types can implement to support query expressions.</span></span> <span data-ttu-id="2e3bd-2610">由于查询表达式是通过语法映射转换为方法调用的，因此在实现查询表达式模式的方式上，类型具有相当大的灵活性。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2610">Because query expressions are translated to method invocations by means of a syntactic mapping, types have considerable flexibility in how they implement the query expression pattern.</span></span> <span data-ttu-id="2e3bd-2611">例如，可以将模式的方法作为实例方法或扩展方法实现，因为这两个方法具有相同的调用语法，并且方法可以请求委托或表达式树，因为匿名函数可同时转换为两者。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2611">For example, the methods of the pattern can be implemented as instance methods or as extension methods because the two have the same invocation syntax, and the methods can request delegates or expression trees because anonymous functions are convertible to both.</span></span>

<span data-ttu-id="2e3bd-2612">`C<T>`下面显示了支持查询表达式模式的泛型类型的推荐形状。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2612">The recommended shape of a generic type `C<T>` that supports the query expression pattern is shown below.</span></span> <span data-ttu-id="2e3bd-2613">使用泛型类型来说明参数和结果类型之间的正确关系，但也可以实现非泛型类型的模式。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2613">A generic type is used in order to illustrate the proper relationships between parameter and result types, but it is possible to implement the pattern for non-generic types as well.</span></span>

```csharp
delegate R Func<T1,R>(T1 arg1);

delegate R Func<T1,T2,R>(T1 arg1, T2 arg2);

class C
{
    public C<T> Cast<T>();
}

class C<T> : C
{
    public C<T> Where(Func<T,bool> predicate);

    public C<U> Select<U>(Func<T,U> selector);

    public C<V> SelectMany<U,V>(Func<T,C<U>> selector,
        Func<T,U,V> resultSelector);

    public C<V> Join<U,K,V>(C<U> inner, Func<T,K> outerKeySelector,
        Func<U,K> innerKeySelector, Func<T,U,V> resultSelector);

    public C<V> GroupJoin<U,K,V>(C<U> inner, Func<T,K> outerKeySelector,
        Func<U,K> innerKeySelector, Func<T,C<U>,V> resultSelector);

    public O<T> OrderBy<K>(Func<T,K> keySelector);

    public O<T> OrderByDescending<K>(Func<T,K> keySelector);

    public C<G<K,T>> GroupBy<K>(Func<T,K> keySelector);

    public C<G<K,E>> GroupBy<K,E>(Func<T,K> keySelector,
        Func<T,E> elementSelector);
}

class O<T> : C<T>
{
    public O<T> ThenBy<K>(Func<T,K> keySelector);

    public O<T> ThenByDescending<K>(Func<T,K> keySelector);
}

class G<K,T> : C<T>
{
    public K Key { get; }
}
```

<span data-ttu-id="2e3bd-2614">上面的方法使用泛型委托类型 `Func<T1,R>` 和 `Func<T1,T2,R>` ，但它们可以同样地使用参数和结果类型中具有相同关系的其他委托或表达式树类型。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2614">The methods above use the generic delegate types `Func<T1,R>` and `Func<T1,T2,R>`, but they could equally well have used other delegate or expression tree types with the same relationships in parameter and result types.</span></span>

<span data-ttu-id="2e3bd-2615">请注意和之间建议的关系， `C<T>` `O<T>` 确保 `ThenBy` 和 `ThenByDescending` 方法仅可用于或的结果 `OrderBy` `OrderByDescending` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2615">Notice the recommended relationship between `C<T>` and `O<T>` which ensures that the `ThenBy` and `ThenByDescending` methods are available only on the result of an `OrderBy` or `OrderByDescending`.</span></span> <span data-ttu-id="2e3bd-2616">另请注意，结果的建议形状为序列的 `GroupBy` 序列，其中每个内部序列都有一个附加 `Key` 属性。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2616">Also notice the recommended shape of the result of `GroupBy` -- a sequence of sequences, where each inner sequence has an additional `Key` property.</span></span>

<span data-ttu-id="2e3bd-2617">`System.Linq`命名空间为实现接口的任何类型提供查询运算符模式的实现 `System.Collections.Generic.IEnumerable<T>` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2617">The `System.Linq` namespace provides an implementation of the query operator pattern for any type that implements the `System.Collections.Generic.IEnumerable<T>` interface.</span></span>

## <a name="assignment-operators"></a><span data-ttu-id="2e3bd-2618">赋值运算符</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2618">Assignment operators</span></span>

<span data-ttu-id="2e3bd-2619">赋值运算符将新值分配给变量、属性、事件或索引器元素。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2619">The assignment operators assign a new value to a variable, a property, an event, or an indexer element.</span></span>

```antlr
assignment
    : unary_expression assignment_operator expression
    ;

assignment_operator
    : '='
    | '+='
    | '-='
    | '*='
    | '/='
    | '%='
    | '&='
    | '|='
    | '^='
    | '<<='
    | right_shift_assignment
    ;
```

<span data-ttu-id="2e3bd-2620">赋值运算的左操作数必须是分类为变量、属性访问、索引器访问或事件访问的表达式。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2620">The left operand of an assignment must be an expression classified as a variable, a property access, an indexer access, or an event access.</span></span>

<span data-ttu-id="2e3bd-2621">`=`运算符称为 ***简单赋值运算符***。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2621">The `=` operator is called the ***simple assignment operator***.</span></span> <span data-ttu-id="2e3bd-2622">它将右操作数的值分配给左操作数给定的变量、属性或索引器元素。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2622">It assigns the value of the right operand to the variable, property, or indexer element given by the left operand.</span></span> <span data-ttu-id="2e3bd-2623">简单赋值运算符的左操作数不能是事件访问 (，只 [需) 类似于字段的事件](classes.md#field-like-events) 中所述。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2623">The left operand of the simple assignment operator may not be an event access (except as described in [Field-like events](classes.md#field-like-events)).</span></span> <span data-ttu-id="2e3bd-2624">简单赋值运算符在 [简单赋值](expressions.md#simple-assignment)中进行了介绍。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2624">The simple assignment operator is described in [Simple assignment](expressions.md#simple-assignment).</span></span>

<span data-ttu-id="2e3bd-2625">运算符以外的赋值运算符 `=` 称为 ***复合赋值运算符***。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2625">The assignment operators other than the `=` operator are called the ***compound assignment operators***.</span></span> <span data-ttu-id="2e3bd-2626">这些运算符对两个操作数执行指定的运算，然后将结果值分配给左操作数给定的变量、属性或索引器元素。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2626">These operators perform the indicated operation on the two operands, and then assign the resulting value to the variable, property, or indexer element given by the left operand.</span></span> <span data-ttu-id="2e3bd-2627">复合赋值运算符在 [复合赋值](expressions.md#compound-assignment)中介绍。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2627">The compound assignment operators are described in [Compound assignment](expressions.md#compound-assignment).</span></span>

<span data-ttu-id="2e3bd-2628">`+=` `-=` 使用事件访问表达式作为左操作数的和运算符称为 *事件赋值运算符*。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2628">The `+=` and `-=` operators with an event access expression as the left operand are called the *event assignment operators*.</span></span> <span data-ttu-id="2e3bd-2629">其他赋值运算符对于作为左操作数的事件访问无效。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2629">No other assignment operator is valid with an event access as the left operand.</span></span> <span data-ttu-id="2e3bd-2630">事件赋值运算符在 [事件分配](expressions.md#event-assignment)中介绍。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2630">The event assignment operators are described in [Event assignment](expressions.md#event-assignment).</span></span>

<span data-ttu-id="2e3bd-2631">赋值运算符是右结合运算符，这意味着运算从右到左分组。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2631">The assignment operators are right-associative, meaning that operations are grouped from right to left.</span></span> <span data-ttu-id="2e3bd-2632">例如，窗体的表达式的 `a = b = c` 计算结果为 `a = (b = c)` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2632">For example, an expression of the form `a = b = c` is evaluated as `a = (b = c)`.</span></span>

### <a name="simple-assignment"></a><span data-ttu-id="2e3bd-2633">简单赋值</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2633">Simple assignment</span></span>

<span data-ttu-id="2e3bd-2634">`=`运算符称为简单赋值运算符。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2634">The `=` operator is called the simple assignment operator.</span></span>

<span data-ttu-id="2e3bd-2635">如果简单赋值的左操作数为形式 `E.P` 或 `E[Ei]` `E` 具有编译时类型的 `dynamic` ，则分配将动态绑定 ([动态绑定](expressions.md#dynamic-binding)) 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2635">If the left operand of a simple assignment is of the form `E.P` or `E[Ei]` where `E` has the compile-time type `dynamic`, then the assignment is dynamically bound ([Dynamic binding](expressions.md#dynamic-binding)).</span></span> <span data-ttu-id="2e3bd-2636">在这种情况下，赋值表达式的编译时类型是 `dynamic` ，下面所述的解决方法将在运行时根据的运行时类型发生 `E` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2636">In this case the compile-time type of the assignment expression is `dynamic`, and the resolution described below will take place at run-time based on the run-time type of `E`.</span></span>

<span data-ttu-id="2e3bd-2637">在简单赋值中，右操作数必须是可隐式转换为左操作数类型的表达式。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2637">In a simple assignment, the right operand must be an expression that is implicitly convertible to the type of the left operand.</span></span> <span data-ttu-id="2e3bd-2638">操作将右操作数的值分配给左操作数给定的变量、属性或索引器元素。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2638">The operation assigns the value of the right operand to the variable, property, or indexer element given by the left operand.</span></span>

<span data-ttu-id="2e3bd-2639">简单赋值表达式的结果是赋给左操作数的值。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2639">The result of a simple assignment expression is the value assigned to the left operand.</span></span> <span data-ttu-id="2e3bd-2640">结果与左操作数的类型相同，并且始终归类为值。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2640">The result has the same type as the left operand and is always classified as a value.</span></span>

<span data-ttu-id="2e3bd-2641">如果左操作数是属性或索引器访问，则属性或索引器必须具有 `set` 访问器。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2641">If the left operand is a property or indexer access, the property or indexer must have a `set` accessor.</span></span> <span data-ttu-id="2e3bd-2642">如果不是这种情况，则会发生绑定时错误。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2642">If this is not the case, a binding-time error occurs.</span></span>

<span data-ttu-id="2e3bd-2643">简单分配窗体的运行时处理 `x = y` 包含以下步骤：</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2643">The run-time processing of a simple assignment of the form `x = y` consists of the following steps:</span></span>

*  <span data-ttu-id="2e3bd-2644">如果 `x` 归类为变量：</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2644">If `x` is classified as a variable:</span></span>
   * <span data-ttu-id="2e3bd-2645">`x` 计算以生成变量。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2645">`x` is evaluated to produce the variable.</span></span>
   * <span data-ttu-id="2e3bd-2646">`y``x`通过隐式转换 ([隐](conversions.md#implicit-conversions)式转换) 来计算并转换为的类型。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2646">`y` is evaluated and, if required, converted to the type of `x` through an implicit conversion ([Implicit conversions](conversions.md#implicit-conversions)).</span></span>
   * <span data-ttu-id="2e3bd-2647">如果给定的变量 `x` 是 *reference_type* 的数组元素，则执行运行时检查，以确保为计算的值与作为 `y` 元素的数组实例兼容 `x` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2647">If the variable given by `x` is an array element of a *reference_type*, a run-time check is performed to ensure that the value computed for `y` is compatible with the array instance of which `x` is an element.</span></span> <span data-ttu-id="2e3bd-2648">如果为，则检查成功 `y` `null` ; 或者，如果隐式引用转换 ([隐式引用](conversions.md#implicit-reference-conversions) 转换，) 存在从引用的实例的实际类型 `y` 到包含的数组实例的实际元素类型 `x` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2648">The check succeeds if `y` is `null`, or if an implicit reference conversion ([Implicit reference conversions](conversions.md#implicit-reference-conversions)) exists from the actual type of the instance referenced by `y` to the actual element type of the array instance containing `x`.</span></span> <span data-ttu-id="2e3bd-2649">否则，将会引发 `System.ArrayTypeMismatchException`。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2649">Otherwise, a `System.ArrayTypeMismatchException` is thrown.</span></span>
   * <span data-ttu-id="2e3bd-2650">的计算和转换导致的值存储在的 `y` 计算中给定的位置 `x` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2650">The value resulting from the evaluation and conversion of `y` is stored into the location given by the evaluation of `x`.</span></span>
*  <span data-ttu-id="2e3bd-2651">如果 `x` 归类为属性或索引器访问：</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2651">If `x` is classified as a property or indexer access:</span></span>
   * <span data-ttu-id="2e3bd-2652">如果) 为，则实例表达式 (如果是，则为 `x` `static` `x` ; 如果是与相关联的索引器访问) ，则 (参数列表 `x` ，并且在后续 `set` 访问器调用中使用结果。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2652">The instance expression (if `x` is not `static`) and the argument list (if `x` is an indexer access) associated with `x` are evaluated, and the results are used in the subsequent `set` accessor invocation.</span></span>
   * <span data-ttu-id="2e3bd-2653">`y``x`通过隐式转换 ([隐](conversions.md#implicit-conversions)式转换) 来计算并转换为的类型。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2653">`y` is evaluated and, if required, converted to the type of `x` through an implicit conversion ([Implicit conversions](conversions.md#implicit-conversions)).</span></span>
   * <span data-ttu-id="2e3bd-2654">`set`调用的访问器时，将 `x` 计算的值 `y` 作为其 `value` 参数。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2654">The `set` accessor of `x` is invoked with the value computed for `y` as its `value` argument.</span></span>

<span data-ttu-id="2e3bd-2655">数组协方差规则 ([数组协方](arrays.md#array-covariance) 差) 允许将数组类型的值 `A[]` 作为对数组类型的实例的引用 `B[]` ，前提是从到存在隐式引用转换 `B` `A` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2655">The array co-variance rules ([Array covariance](arrays.md#array-covariance)) permit a value of an array type `A[]` to be a reference to an instance of an array type `B[]`, provided an implicit reference conversion exists from `B` to `A`.</span></span> <span data-ttu-id="2e3bd-2656">由于这些规则，对 *reference_type* 的数组元素赋值需要运行时检查，以确保所赋的值与数组实例兼容。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2656">Because of these rules, assignment to an array element of a *reference_type* requires a run-time check to ensure that the value being assigned is compatible with the array instance.</span></span> <span data-ttu-id="2e3bd-2657">示例中</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2657">In the example</span></span>
```csharp
string[] sa = new string[10];
object[] oa = sa;

oa[0] = null;               // Ok
oa[1] = "Hello";            // Ok
oa[2] = new ArrayList();    // ArrayTypeMismatchException
```
<span data-ttu-id="2e3bd-2658">最后一个分配导致 `System.ArrayTypeMismatchException` 引发，因为的实例 `ArrayList` 无法存储在的元素中 `string[]` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2658">the last assignment causes a `System.ArrayTypeMismatchException` to be thrown because an instance of `ArrayList` cannot be stored in an element of a `string[]`.</span></span>

<span data-ttu-id="2e3bd-2659">如果在 *struct_type* 中声明的属性或索引器是赋值目标，则必须将与属性或索引器访问关联的实例表达式归类为变量。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2659">When a property or indexer declared in a *struct_type* is the target of an assignment, the instance expression associated with the property or indexer access must be classified as a variable.</span></span> <span data-ttu-id="2e3bd-2660">如果实例表达式归类为值，则会发生绑定时错误。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2660">If the instance expression is classified as a value, a binding-time error occurs.</span></span> <span data-ttu-id="2e3bd-2661">由于 [成员访问](expressions.md#member-access)，相同的规则也适用于字段。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2661">Because of [Member access](expressions.md#member-access), the same rule also applies to fields.</span></span>

<span data-ttu-id="2e3bd-2662">给定以下声明：</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2662">Given the declarations:</span></span>
```csharp
struct Point
{
    int x, y;

    public Point(int x, int y) {
        this.x = x;
        this.y = y;
    }

    public int X {
        get { return x; }
        set { x = value; }
    }

    public int Y {
        get { return y; }
        set { y = value; }
    }
}

struct Rectangle
{
    Point a, b;

    public Rectangle(Point a, Point b) {
        this.a = a;
        this.b = b;
    }

    public Point A {
        get { return a; }
        set { a = value; }
    }

    public Point B {
        get { return b; }
        set { b = value; }
    }
}
```
<span data-ttu-id="2e3bd-2663">示例中</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2663">in the example</span></span>
```csharp
Point p = new Point();
p.X = 100;
p.Y = 100;
Rectangle r = new Rectangle();
r.A = new Point(10, 10);
r.B = p;
```
<span data-ttu-id="2e3bd-2664">`p.X`允许对、、和的赋值， `p.Y` `r.A` `r.B` 因为 `p` 和 `r` 为变量。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2664">the assignments to `p.X`, `p.Y`, `r.A`, and `r.B` are permitted because `p` and `r` are variables.</span></span> <span data-ttu-id="2e3bd-2665">但在示例中</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2665">However, in the example</span></span>
```csharp
Rectangle r = new Rectangle();
r.A.X = 10;
r.A.Y = 10;
r.B.X = 100;
r.B.Y = 100;
```
<span data-ttu-id="2e3bd-2666">分配均无效，因为 `r.A` 和 `r.B` 不是变量。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2666">the assignments are all invalid, since `r.A` and `r.B` are not variables.</span></span>

### <a name="compound-assignment"></a><span data-ttu-id="2e3bd-2667">复合赋值</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2667">Compound assignment</span></span>

<span data-ttu-id="2e3bd-2668">如果复合赋值的左操作数为形式 `E.P` 或 `E[Ei]` `E` 具有编译时类型的 `dynamic` ，则分配将动态绑定 ([动态绑定](expressions.md#dynamic-binding)) 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2668">If the left operand of a compound assignment is of the form `E.P` or `E[Ei]` where `E` has the compile-time type `dynamic`, then the assignment is dynamically bound ([Dynamic binding](expressions.md#dynamic-binding)).</span></span> <span data-ttu-id="2e3bd-2669">在这种情况下，赋值表达式的编译时类型是 `dynamic` ，下面所述的解决方法将在运行时根据的运行时类型发生 `E` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2669">In this case the compile-time type of the assignment expression is `dynamic`, and the resolution described below will take place at run-time based on the run-time type of `E`.</span></span>

<span data-ttu-id="2e3bd-2670">`x op= y`通过将二元运算符重载决策应用 ([二元运算符重载](expressions.md#binary-operator-overload-resolution)决策来处理窗体的操作，) 与写入操作的方式相同 `x op y` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2670">An operation of the form `x op= y` is processed by applying binary operator overload resolution ([Binary operator overload resolution](expressions.md#binary-operator-overload-resolution)) as if the operation was written `x op y`.</span></span> <span data-ttu-id="2e3bd-2671">那么：</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2671">Then,</span></span>

*  <span data-ttu-id="2e3bd-2672">如果所选运算符的返回类型可隐式转换为的类型 `x` ，则运算计算结果为 `x = x op y` ， `x` 只计算一次。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2672">If the return type of the selected operator is implicitly convertible to the type of `x`, the operation is evaluated as `x = x op y`, except that `x` is evaluated only once.</span></span>
*  <span data-ttu-id="2e3bd-2673">否则，如果所选运算符为预定义运算符，则如果所选运算符的返回类型可显式转换为的类型 `x` ，并且如果可 `y` 隐式转换为的类型 `x` 或运算符为移位运算符，则运算将计算为 `x = (T)(x op y)` ，其中 `T` 是的类型 `x` ，但 `x` 只计算一次。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2673">Otherwise, if the selected operator is a predefined operator, if the return type of the selected operator is explicitly convertible to the type of `x`, and if `y` is implicitly convertible to the type of `x` or the operator is a shift operator, then the operation is evaluated as `x = (T)(x op y)`, where `T` is the type of `x`, except that `x` is evaluated only once.</span></span>
*  <span data-ttu-id="2e3bd-2674">否则，复合分配无效，并发生绑定时错误。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2674">Otherwise, the compound assignment is invalid, and a binding-time error occurs.</span></span>

<span data-ttu-id="2e3bd-2675">术语 "仅计算一次" 表示在的计算中 `x op y` ，的所有构成表达式的结果 `x` 都将暂时保存，然后在执行对的赋值时重用 `x` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2675">The term "evaluated only once" means that in the evaluation of `x op y`, the results of any constituent expressions of `x` are temporarily saved and then reused when performing the assignment to `x`.</span></span> <span data-ttu-id="2e3bd-2676">例如，在赋值中 `A()[B()] += C()` ，其中 `A` 是返回的方法 `int[]` ，并且 `B` 和是返回的方法 `C` `int` ，这些方法只会按顺序调用一次 `A` `B` `C` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2676">For example, in the assignment `A()[B()] += C()`, where `A` is a method returning `int[]`, and `B` and `C` are methods returning `int`, the methods are invoked only once, in the order `A`, `B`, `C`.</span></span>

<span data-ttu-id="2e3bd-2677">当复合赋值的左操作数是属性访问或索引器访问时，属性或索引器必须同时具有 `get` 访问器和访问器 `set` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2677">When the left operand of a compound assignment is a property access or indexer access, the property or indexer must have both a `get` accessor and a `set` accessor.</span></span> <span data-ttu-id="2e3bd-2678">如果不是这种情况，则会发生绑定时错误。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2678">If this is not the case, a binding-time error occurs.</span></span>

<span data-ttu-id="2e3bd-2679">上面的第二个规则允许 `x op= y` `x = (T)(x op y)` 在某些上下文中计算。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2679">The second rule above permits `x op= y` to be evaluated as `x = (T)(x op y)` in certain contexts.</span></span> <span data-ttu-id="2e3bd-2680">存在该规则，以便当左操作数的类型为 `sbyte` 、 `byte` 、 `short` 、 `ushort` 或时，可以将预定义运算符用作复合运算符 `char` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2680">The rule exists such that the predefined operators can be used as compound operators when the left operand is of type `sbyte`, `byte`, `short`, `ushort`, or `char`.</span></span> <span data-ttu-id="2e3bd-2681">即使两个参数都属于这些类型之一，预定义运算符也会生成类型的结果 `int` ，如 [二进制数值升级](expressions.md#binary-numeric-promotions)中所述。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2681">Even when both arguments are of one of those types, the predefined operators produce a result of type `int`, as described in [Binary numeric promotions](expressions.md#binary-numeric-promotions).</span></span> <span data-ttu-id="2e3bd-2682">因此，在没有强制转换的情况下，不能将结果赋给左操作数。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2682">Thus, without a cast it would not be possible to assign the result to the left operand.</span></span>

<span data-ttu-id="2e3bd-2683">对于预定义运算符，规则的直观效果只是 `x op= y` `x op y` 允许和都允许 `x = y` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2683">The intuitive effect of the rule for predefined operators is simply that `x op= y` is permitted if both of `x op y` and `x = y` are permitted.</span></span> <span data-ttu-id="2e3bd-2684">示例中</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2684">In the example</span></span>
```csharp
byte b = 0;
char ch = '\0';
int i = 0;

b += 1;             // Ok
b += 1000;          // Error, b = 1000 not permitted
b += i;             // Error, b = i not permitted
b += (byte)i;       // Ok

ch += 1;            // Error, ch = 1 not permitted
ch += (char)1;      // Ok
```
<span data-ttu-id="2e3bd-2685">每个错误的直观原因在于，相应的简单分配也会导致错误。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2685">the intuitive reason for each error is that a corresponding simple assignment would also have been an error.</span></span>

<span data-ttu-id="2e3bd-2686">这也意味着复合赋值操作支持提升的操作。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2686">This also means that compound assignment operations support lifted operations.</span></span> <span data-ttu-id="2e3bd-2687">示例中</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2687">In the example</span></span>
```csharp
int? i = 0;
i += 1;             // Ok
```
<span data-ttu-id="2e3bd-2688">使用提升运算符 `+(int?,int?)` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2688">the lifted operator `+(int?,int?)` is used.</span></span>

### <a name="event-assignment"></a><span data-ttu-id="2e3bd-2689">事件分配</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2689">Event assignment</span></span>

<span data-ttu-id="2e3bd-2690">如果 or 运算符的左操作数 `+=` `-=` 归类为事件访问，则表达式的计算方式如下：</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2690">If the left operand of a `+=` or `-=` operator is classified as an event access, then the expression is evaluated as follows:</span></span>

*  <span data-ttu-id="2e3bd-2691">计算事件访问的实例表达式（如果有）。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2691">The instance expression, if any, of the event access is evaluated.</span></span>
*  <span data-ttu-id="2e3bd-2692">`+=`计算或运算符的右操作数 `-=` ，并在需要时通过隐式转换转换为左操作数的类型 ([隐式](conversions.md#implicit-conversions)转换) 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2692">The right operand of the `+=` or `-=` operator is evaluated, and, if required, converted to the type of the left operand through an implicit conversion ([Implicit conversions](conversions.md#implicit-conversions)).</span></span>
*  <span data-ttu-id="2e3bd-2693">调用事件的事件访问器，其中包含右操作数（在计算后，如有必要）转换后的参数列表。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2693">An event accessor of the event is invoked, with argument list consisting of the right operand, after evaluation and, if necessary, conversion.</span></span> <span data-ttu-id="2e3bd-2694">如果运算符为 `+=` ，则 `add` 调用访问器; 如果运算符为，则 `-=` `remove` 调用访问器。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2694">If the operator was `+=`, the `add` accessor is invoked; if the operator was `-=`, the `remove` accessor is invoked.</span></span>

<span data-ttu-id="2e3bd-2695">事件赋值表达式不生成值。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2695">An event assignment expression does not yield a value.</span></span> <span data-ttu-id="2e3bd-2696">因此，事件分配表达式仅在) *statement_expression* ([表达式语句](statements.md#expression-statements) 的上下文中有效。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2696">Thus, an event assignment expression is valid only in the context of a *statement_expression* ([Expression statements](statements.md#expression-statements)).</span></span>

## <a name="expression"></a><span data-ttu-id="2e3bd-2697">表达式</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2697">Expression</span></span>

<span data-ttu-id="2e3bd-2698">*表达式* 可以是 *non_assignment_expression* 或 *赋值*。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2698">An *expression* is either a *non_assignment_expression* or an *assignment*.</span></span>

```antlr
expression
    : non_assignment_expression
    | assignment
    ;

non_assignment_expression
    : conditional_expression
    | lambda_expression
    | query_expression
    ;
```

## <a name="constant-expressions"></a><span data-ttu-id="2e3bd-2699">常量表达式</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2699">Constant expressions</span></span>

<span data-ttu-id="2e3bd-2700">*Constant_expression* 是可以在编译时完全计算的表达式。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2700">A *constant_expression* is an expression that can be fully evaluated at compile-time.</span></span>

```antlr
constant_expression
    : expression
    ;
```

<span data-ttu-id="2e3bd-2701">常数表达式必须是 `null` 文本或具有以下类型之一的值：、、、、、、、、、、、、、、 `sbyte` `byte` `short` `ushort` `int` `uint` `long` `ulong` `char` `float` `double` `decimal` `bool` `object` `string` 或任何枚举类型。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2701">A constant expression must be the `null` literal or a value with one of  the following types: `sbyte`, `byte`, `short`, `ushort`, `int`, `uint`, `long`, `ulong`, `char`, `float`, `double`, `decimal`, `bool`, `object`, `string`, or any enumeration type.</span></span> <span data-ttu-id="2e3bd-2702">常量表达式中仅允许使用以下构造：</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2702">Only the following constructs are permitted in constant expressions:</span></span>

*  <span data-ttu-id="2e3bd-2703">文本 (包括 `null` 文本) 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2703">Literals (including the `null` literal).</span></span>
*  <span data-ttu-id="2e3bd-2704">对 `const` 类和结构类型成员的引用。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2704">References to `const` members of class and struct types.</span></span>
*  <span data-ttu-id="2e3bd-2705">对枚举类型的成员的引用。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2705">References to members of enumeration types.</span></span>
*  <span data-ttu-id="2e3bd-2706">对 `const` 参数或局部变量的引用</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2706">References to `const` parameters or local variables</span></span>
*  <span data-ttu-id="2e3bd-2707">带括号的子表达式，它们本身就是常量表达式。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2707">Parenthesized sub-expressions, which are themselves constant expressions.</span></span>
*  <span data-ttu-id="2e3bd-2708">如果目标类型是上面列出的类型之一，则强制转换表达式。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2708">Cast expressions, provided the target type is one of the types listed above.</span></span>
*  <span data-ttu-id="2e3bd-2709">`checked` 和 `unchecked` 表达式</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2709">`checked` and `unchecked` expressions</span></span>
*  <span data-ttu-id="2e3bd-2710">默认值表达式</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2710">Default value expressions</span></span>
*  <span data-ttu-id="2e3bd-2711">Nameof 表达式</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2711">Nameof expressions</span></span>
*  <span data-ttu-id="2e3bd-2712">预定义的 `+` 、、 `-` `!` 和 `~` 一元运算符。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2712">The predefined `+`, `-`, `!`, and `~` unary operators.</span></span>
*  <span data-ttu-id="2e3bd-2713">预定义的、、、、、、、、、、、、、、、、、、、、、、、 `+` `-` `*` `/` `%` `<<` `>>` `&` `|` `^` `&&` `||` `==` `!=` `<` `>` `<=` 和 `>=` 二元运算符，前提是每个操作数都是上面列出的类型。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2713">The predefined `+`, `-`, `*`, `/`, `%`, `<<`, `>>`, `&`, `|`, `^`, `&&`, `||`, `==`, `!=`, `<`, `>`, `<=`, and `>=` binary operators, provided each operand is of a type listed above.</span></span>
*  <span data-ttu-id="2e3bd-2714">`?:`条件运算符。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2714">The `?:` conditional operator.</span></span>

<span data-ttu-id="2e3bd-2715">常数表达式中允许以下转换：</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2715">The following conversions are permitted in constant expressions:</span></span>

*  <span data-ttu-id="2e3bd-2716">标识转换</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2716">Identity conversions</span></span>
*  <span data-ttu-id="2e3bd-2717">数值转换</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2717">Numeric conversions</span></span>
*  <span data-ttu-id="2e3bd-2718">枚举转换</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2718">Enumeration conversions</span></span>
*  <span data-ttu-id="2e3bd-2719">常数表达式转换</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2719">Constant expression conversions</span></span>
*  <span data-ttu-id="2e3bd-2720">隐式和显式引用转换，前提是转换的源是计算结果为 null 值的常量表达式。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2720">Implicit and explicit reference conversions, provided that the source of the conversions is a constant expression that evaluates to the null value.</span></span>

<span data-ttu-id="2e3bd-2721">不允许在常数表达式中使用其他转换，包括非 null 值的装箱、取消装箱和隐式引用转换。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2721">Other conversions including boxing, unboxing and implicit reference conversions of non-null values are not permitted in constant expressions.</span></span> <span data-ttu-id="2e3bd-2722">例如：</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2722">For example:</span></span>
```csharp
class C {
    const object i = 5;         // error: boxing conversion not permitted
    const object str = "hello"; // error: implicit reference conversion
}
```
<span data-ttu-id="2e3bd-2723">i 的初始化是错误的，因为需要装箱转换。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2723">the initialization of i is an error because a boxing conversion is required.</span></span> <span data-ttu-id="2e3bd-2724">Str 的初始化是错误的，因为需要从非 null 值进行隐式引用转换。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2724">The initialization of str is an error because an implicit reference conversion from a non-null value is required.</span></span>

<span data-ttu-id="2e3bd-2725">只要表达式满足上面列出的要求，就会在编译时计算表达式。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2725">Whenever an expression fulfills the requirements listed above, the expression is evaluated at compile-time.</span></span> <span data-ttu-id="2e3bd-2726">即使表达式是包含非常量构造的更大的表达式的子表达式，也是如此。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2726">This is true even if the expression is a sub-expression of a larger expression that contains non-constant constructs.</span></span>

<span data-ttu-id="2e3bd-2727">常数表达式的编译时计算使用与非常量表达式的运行时计算相同的规则，只不过运行时计算会引发异常，编译时计算会导致发生编译时错误。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2727">The compile-time evaluation of constant expressions uses the same rules as run-time evaluation of non-constant expressions, except that where run-time evaluation would have thrown an exception, compile-time evaluation causes a compile-time error to occur.</span></span>

<span data-ttu-id="2e3bd-2728">除非将常量表达式显式放置在上下文中 `unchecked` ，否则在表达式的编译时计算过程中发生的溢出将始终导致编译时错误 ([常数表达式](expressions.md#constant-expressions)) 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2728">Unless a constant expression is explicitly placed in an `unchecked` context, overflows that occur in integral-type arithmetic operations and conversions during the compile-time evaluation of the expression always cause compile-time errors ([Constant expressions](expressions.md#constant-expressions)).</span></span>

<span data-ttu-id="2e3bd-2729">常数表达式出现在下面列出的上下文中。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2729">Constant expressions occur in the contexts listed below.</span></span> <span data-ttu-id="2e3bd-2730">在这些上下文中，如果表达式在编译时无法完全计算，则会发生编译时错误。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2730">In these contexts, a compile-time error occurs if an expression cannot be fully evaluated at compile-time.</span></span>

*  <span data-ttu-id="2e3bd-2731">常数声明 ([常量](classes.md#constants)) 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2731">Constant declarations ([Constants](classes.md#constants)).</span></span>
*  <span data-ttu-id="2e3bd-2732">枚举成员声明 (枚举 [成员](enums.md#enum-members)) 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2732">Enumeration member declarations ([Enum members](enums.md#enum-members)).</span></span>
*  <span data-ttu-id="2e3bd-2733">形参的默认实参列出 ([方法形参](classes.md#method-parameters)) </span><span class="sxs-lookup"><span data-stu-id="2e3bd-2733">Default arguments of formal parameter lists ([Method parameters](classes.md#method-parameters))</span></span>
*  <span data-ttu-id="2e3bd-2734">`case``switch` [switch 语句](statements.md#the-switch-statement) () 的语句的标签。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2734">`case` labels of a `switch` statement ([The switch statement](statements.md#the-switch-statement)).</span></span>
*  <span data-ttu-id="2e3bd-2735">`goto case`[goto 语句 (的](statements.md#the-goto-statement)语句) 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2735">`goto case` statements ([The goto statement](statements.md#the-goto-statement)).</span></span>
*  <span data-ttu-id="2e3bd-2736">数组创建表达式中的维度长度 ([数组创建](expressions.md#array-creation-expressions) 表达式) 包含初始值设定项。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2736">Dimension lengths in an array creation expression ([Array creation expressions](expressions.md#array-creation-expressions)) that includes an initializer.</span></span>
*  <span data-ttu-id="2e3bd-2737">特性 ([特性](attributes.md)) 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2737">Attributes ([Attributes](attributes.md)).</span></span>

<span data-ttu-id="2e3bd-2738">隐式常量表达式转换 ([隐式常量表达式转换](conversions.md#implicit-constant-expression-conversions)) 允许将类型的常量表达式 `int` 转换为 `sbyte` 、、、、 `byte` `short` `ushort` `uint` 或 `ulong` ，前提是常量表达式的值在目标类型的范围内。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2738">An implicit constant expression conversion ([Implicit constant expression conversions](conversions.md#implicit-constant-expression-conversions)) permits a constant expression of type `int` to be converted to `sbyte`, `byte`, `short`, `ushort`, `uint`, or `ulong`, provided the value of the constant expression is within the range of the destination type.</span></span>

## <a name="boolean-expressions"></a><span data-ttu-id="2e3bd-2739">布尔表达式</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2739">Boolean expressions</span></span>

<span data-ttu-id="2e3bd-2740">*Boolean_expression* 是一个表达式，该表达式生成类型为的结果， `bool` 在某些上下文中直接或通过的应用程序， `operator true` 如下所示。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2740">A *boolean_expression* is an expression that yields a result of type `bool`; either directly or through application of `operator true` in certain contexts as specified in the following.</span></span>

```antlr
boolean_expression
    : expression
    ;
```

<span data-ttu-id="2e3bd-2741">*If_statement* 的 ([if 语句](statements.md#the-if-statement)的控制条件表达式) ， *while_statement* ([while 语句](statements.md#the-while-statement)) ， *do_statement* [ (语句) 或](statements.md#the-do-statement) *for_statement (* [) 的](statements.md#the-for-statement) *boolean_expression。*</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2741">The controlling conditional expression of an *if_statement* ([The if statement](statements.md#the-if-statement)), *while_statement* ([The while statement](statements.md#the-while-statement)), *do_statement* ([The do statement](statements.md#the-do-statement)), or *for_statement* ([The for statement](statements.md#the-for-statement)) is a *boolean_expression*.</span></span> <span data-ttu-id="2e3bd-2742">`?:`运算符 ([条件运算符](expressions.md#conditional-operator)) 的控制条件表达式遵循与 *boolean_expression* 相同的规则，但出于运算符优先级的原因，将归类为 *conditional_or_expression*。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2742">The controlling conditional expression of the `?:` operator ([Conditional operator](expressions.md#conditional-operator)) follows the same rules as a *boolean_expression*, but for reasons of operator precedence is classified as a *conditional_or_expression*.</span></span>

<span data-ttu-id="2e3bd-2743">需要 *boolean_expression* 才能 `E` 生成类型的值 `bool` ，如下所示：</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2743">A *boolean_expression* `E` is required to be able to produce a value of type `bool`, as follows:</span></span>

*  <span data-ttu-id="2e3bd-2744">如果 `E` `bool` 在运行时隐式转换为，则应用隐式转换。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2744">If `E` is implicitly convertible to `bool` then at runtime that implicit conversion is applied.</span></span>
*  <span data-ttu-id="2e3bd-2745">否则，一元运算符重载决策 ([一元运算符重载决策](expressions.md#unary-operator-overload-resolution)) 用于查找上运算符的唯一最佳实现 `true` `E` ，并且该实现在运行时应用。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2745">Otherwise, unary operator overload resolution ([Unary operator overload resolution](expressions.md#unary-operator-overload-resolution)) is used to find a unique best implementation of operator `true` on `E`, and that implementation is applied at runtime.</span></span>
*  <span data-ttu-id="2e3bd-2746">如果找不到这样的运算符，则发生绑定时错误。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2746">If no such operator is found, a binding-time error occurs.</span></span>

<span data-ttu-id="2e3bd-2747">`DBBool`[数据库布尔类型](structs.md#database-boolean-type)中的结构类型提供了实现和的类型的示例 `operator true` `operator false` 。</span><span class="sxs-lookup"><span data-stu-id="2e3bd-2747">The `DBBool` struct type in [Database boolean type](structs.md#database-boolean-type) provides an example of a type that implements `operator true` and `operator false`.</span></span>

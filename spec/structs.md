---
ms.openlocfilehash: 6b35df4e00a00a23221dedca6c0424604971a2f5
ms.sourcegitcommit: f921de697a44d3ae827f168a8371d7dc3ca66f2d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/02/2021
ms.locfileid: "101641981"
---
# <a name="structs"></a>结构

结构与类相似，它们表示可以包含数据成员和函数成员的数据结构。 但是，与类不同的是，结构是值类型，不需要进行堆分配。 结构类型的变量直接包含结构的数据，而类类型的变量包含对数据的引用，后者称为对象。

结构对包含值语义的小型数据结构特别有用。 复数、坐标系中的点或字典中的键值对都是结构的典型示例。 这些数据结构的关键在于它们有少量的数据成员，它们不需要使用继承或引用标识，并且可以使用赋值语义方便地实现这些数据结构，赋值将复制值而不是引用。

如 [简单类型](types.md#simple-types)中所述，c # 提供的简单类型（如 `int` 、 `double` 和 `bool` ）实际上都是所有结构类型。 正如这些预定义类型是结构一样，还可以使用结构和运算符重载来实现 c # 语言的新 "基元" 类型。 本章末尾提供了此类类型的两个示例 ([结构示例](structs.md#struct-examples)) 。

## <a name="struct-declarations"></a>结构声明

*Struct_declaration* 是声明新结构 ([类型声明](namespaces.md#type-declarations)) *type_declaration* ：

```antlr
struct_declaration
    : attributes? struct_modifier* 'partial'? 'struct' identifier type_parameter_list?
      struct_interfaces? type_parameter_constraints_clause* struct_body ';'?
    ;
```

*Struct_declaration* 包含一组可选的 *特性* ([特性](attributes.md)) ，后跟一组可选的 *struct_modifier* s ([结构修饰符](structs.md#struct-modifiers)) ，后跟一个可选 `partial` 修饰符，后面跟一个关键字 `struct` 和一个命名该结构的 *标识符*，后跟一个可选的 *type_parameter_list* 规范 ([类型参数](classes.md#type-parameters)") ，后跟一个可选的 *struct_interfaces* 规范 ([Partial 修饰符](structs.md#partial-modifier)) ，后跟一个可选的 ) type_parameter_constraints_clause [类型参数约束](classes.md#type-parameter-constraints) (，后跟一个可选的 *)*[结构体](structs.md#struct-body)struct_body，后面可以跟一个分号。

### <a name="struct-modifiers"></a>结构修饰符

*Struct_declaration* 可以选择性地包含一系列结构修饰符：

```antlr
struct_modifier
    : 'new'
    | 'public'
    | 'protected'
    | 'internal'
    | 'private'
    | struct_modifier_unsafe
    ;
```

同一修饰符在结构声明中出现多次是编译时错误。

结构声明的修饰符与类声明 ([类](classes.md#class-declarations) 声明的修饰符具有相同的含义，) 。

### <a name="partial-modifier"></a>Partial 修饰符

`partial`修饰符指示此 *struct_declaration* 是分部类型声明。 在封闭命名空间或类型声明中具有相同名称的多个分部结构声明组合在一起，并遵循在 [部分类型](classes.md#partial-types)中指定的规则组成一个结构声明。

### <a name="struct-interfaces"></a>结构接口

结构声明可以包含 *struct_interfaces* 规范，在这种情况下，结构被称为直接实现给定的接口类型。

```antlr
struct_interfaces
    : ':' interface_type_list
    ;
```

[接口实现中进一步](interfaces.md#interface-implementations)讨论了接口实现。

### <a name="struct-body"></a>结构体

结构的 *struct_body* 定义结构的成员。

```antlr
struct_body
    : '{' struct_member_declaration* '}'
    ;
```

## <a name="struct-members"></a>结构成员

结构的成员由其 *struct_member_declaration* 引入的成员和从该类型继承的成员组成 `System.ValueType` 。

```antlr
struct_member_declaration
    : constant_declaration
    | field_declaration
    | method_declaration
    | property_declaration
    | event_declaration
    | indexer_declaration
    | operator_declaration
    | constructor_declaration
    | static_constructor_declaration
    | type_declaration
    | struct_member_declaration_unsafe
    ;
```

除了[类和结构差异](structs.md#class-and-struct-differences)中所述的区别外，通过[异步函数](classes.md#async-functions)在[类成员](classes.md#class-members)中提供的类成员的说明也适用于结构成员。

## <a name="class-and-struct-differences"></a>类和结构的区别

结构在几个重要方面不同于类：

*  结构是值类型 () 的值 [语义](structs.md#value-semantics) 。
*  所有结构类型都隐式继承自类， `System.ValueType` ([继承](structs.md#inheritance)) 。
*  对结构类型的变量赋值会创建 ([赋值](structs.md#assignment)) 分配的值的副本。
*  结构的默认值是通过将所有值类型字段设置为其默认值，并将所有引用类型字段设置为 `null` ([默认值](structs.md#default-values)) 生成的值。
*  装箱和取消装箱操作用于在结构类型和 `object` ([装箱和取消装箱](structs.md#boxing-and-unboxing)) 之间进行转换。
*  `this`[此访问](expressions.md#this-access))  (结构的含义不同。
*  结构的实例字段声明不允许包含变量初始值设定项 ([字段初始值设定](structs.md#field-initializers) 项) 。
*  不允许结构)  ([构造函数](structs.md#constructors) 声明无参数的实例构造函数。
*  不允许结构将析构函数声明 ([析构函数](structs.md#destructors)) 。

### <a name="value-semantics"></a>值语义

结构是值 [类型 (值类型) ](types.md#value-types) 并且称为具有值语义。 另一方面，类是引用类型 ([引用](types.md#reference-types) 类型) 并被认为具有引用语义。

结构类型的变量直接包含结构的数据，而类类型的变量包含对数据的引用，后者称为对象。 如果结构 `B` 包含类型为的实例字段 `A` 并且 `A` 是结构类型，则它是依赖于 `A` `B` 或从构造的类型的编译时错误 `B` 。 `X`  *  `Y` 如果 `X` 包含类型的实例字段，则结构直接依赖于 _ a 结构 `Y` 。 根据此定义，结构所依赖的完整结构集是 _ *_直接依赖于_** 关系的传递闭包。  例如
```csharp
struct Node
{
    int data;
    Node next; // error, Node directly depends on itself
}
```
是一个错误，因为 `Node` 包含自己的类型的实例字段。  另一个示例
```csharp
struct A { B b; }

struct B { C c; }

struct C { A a; }
```
是一个错误，因为每个类型 `A` 、 `B` 和相互 `C` 依赖。

对于类，两个变量可以引用同一对象，因此，对一个变量执行的运算可能会影响另一个变量所引用的对象。 使用结构，每个变量都有自己的数据副本 (除了 `ref` 和 `out` 参数变量) 以外，对一个变量执行的操作不可能影响另一个变量。 此外，由于结构不是引用类型，因此结构类型的值不能为 `null` 。

给定声明
```csharp
struct Point
{
    public int x, y;

    public Point(int x, int y) {
        this.x = x;
        this.y = y;
    }
}
```
代码片段
```csharp
Point a = new Point(10, 10);
Point b = a;
a.x = 100;
System.Console.WriteLine(b.x);
```
输出值 `10` 。 的赋值 `a` `b` 创建值的副本， `b` 因此不受赋值的影响 `a.x` 。 `Point`改为将其声明为类， `100` 因为 `a` 和 `b` 将引用相同的对象。

### <a name="inheritance"></a>继承

所有结构类型都隐式继承自类 `System.ValueType` ，后者又继承自类 `object` 。 结构声明可以指定实现接口的列表，但结构声明不可能指定基类。

结构类型永远不是抽象类型，并且始终隐式密封。 `abstract`因此， `sealed` 在结构声明中不允许使用和修饰符。

由于结构不支持继承，因此结构成员的声明的可访问性不能是 `protected` 或 `protected internal` 。

结构中的函数成员不能是 `abstract` 或 `virtual` ，且 `override` 修饰符只允许重写从继承的方法 `System.ValueType` 。

### <a name="assignment"></a>分配

对结构类型的变量赋值会创建要分配的值的副本。 这不同于对类类型的变量的赋值，后者复制引用，而不是由引用标识的对象。

与赋值类似，当结构作为值参数传递或作为函数成员的结果返回时，将创建结构的副本。 可以通过使用或参数对函数成员的引用来传递结构 `ref` `out` 。

当结构的属性或索引器是赋值的目标时，与属性或索引器访问关联的实例表达式必须归类为变量。 如果实例表达式归类为值，则会发生编译时错误。 这将在 [简单的赋值](expressions.md#simple-assignment)中更详细地介绍。

### <a name="default-values"></a>默认值

如 " [默认值](variables.md#default-values)" 中所述，在创建多个变量时，它们会自动初始化为其默认值。 对于类类型和其他引用类型的变量，此默认值为 `null` 。 但是，由于结构是值类型，因此， `null` 结构的默认值是通过将所有值类型字段设置为其默认值和所有引用类型字段而产生的值 `null` 。

引用 `Point` 上面声明的结构，示例
```csharp
Point[] a = new Point[100];
```
将数组中的每个初始化 `Point` 为通过将 `x` 和字段设置为零而产生的值 `y` 。

结构的默认值与 [结构 (默认构造函数](types.md#default-constructors)) 的默认构造函数返回的值相对应。 与类不同，结构不允许声明无参数的实例构造函数。 相反，每个结构都隐式包含一个无参数的实例构造函数，该构造函数始终返回通过将所有值类型字段设置为其默认值和所有引用类型字段而产生的值 `null` 。

结构应设计为将默认初始化状态视为有效状态。 示例中
```csharp
using System;

struct KeyValuePair
{
    string key;
    string value;

    public KeyValuePair(string key, string value) {
        if (key == null || value == null) throw new ArgumentException();
        this.key = key;
        this.value = value;
    }
}
```
用户定义实例构造函数仅在显式调用它的位置保护 null 值。 如果 `KeyValuePair` 变量服从默认值初始化，则 `key` 和 `value` 字段将为 null，并且该结构必须准备好处理此状态。

### <a name="boxing-and-unboxing"></a>装箱和取消装箱

类类型的值可以转换为类型 `object` 或由类实现的接口类型，只需在编译时将引用视为另一种类型。 同样，可以将类型的值 `object` 或接口类型的值转换回类类型，而无需更改引用 (但在这种情况下，需要运行时类型检查) 。

由于结构不是引用类型，因此对于结构类型，这些操作的实现方式有所不同。 当结构类型的值转换为类型 `object` 或结构实现的接口类型时，将发生装箱操作。 同样，如果类型的值 `object` 或接口类型的值转换回结构类型，则会发生取消装箱操作。 对类类型的相同操作的主要区别是装箱和取消装箱操作会将结构值复制到或传出装箱的实例。 因此，在装箱或取消装箱操作后，对取消装箱的结构所做的更改不会反映在已装箱的结构中。

当结构类型重写从 `System.Object` (（如、或) ）继承的虚方法时 `Equals` `GetHashCode` `ToString` ，通过该结构类型的实例调用虚拟方法不会导致装箱发生。 即使将结构用作类型参数，并且通过类型参数类型的实例进行调用，也是如此。 例如：
```csharp
using System;

struct Counter
{
    int value;

    public override string ToString() {
        value++;
        return value.ToString();
    }
}

class Program
{
    static void Test<T>() where T: new() {
        T x = new T();
        Console.WriteLine(x.ToString());
        Console.WriteLine(x.ToString());
        Console.WriteLine(x.ToString());
    }

    static void Main() {
        Test<Counter>();
    }
}
```

该程序的输出为：
```console
1
2
3
```

尽管对具有副作用的样式是不正确的 `ToString` ，但该示例演示了对的三个调用没有发生装箱 `x.ToString()` 。

同样，在访问受约束的类型参数上的成员时，不会隐式进行装箱。 例如，假设某个接口 `ICounter` 包含一个 `Increment` 可用于修改值的方法。 如果 `ICounter` 用作约束，则会 `Increment` 调用方法的实现，该方法具有对调用的变量的引用 `Increment` ，而不是装箱副本。

```csharp
using System;

interface ICounter
{
    void Increment();
}

struct Counter: ICounter
{
    int value;

    public override string ToString() {
        return value.ToString();
    }

    void ICounter.Increment() {
        value++;
    }
}

class Program
{
    static void Test<T>() where T: ICounter, new() {
        T x = new T();
        Console.WriteLine(x);
        x.Increment();                    // Modify x
        Console.WriteLine(x);
        ((ICounter)x).Increment();        // Modify boxed copy of x
        Console.WriteLine(x);
    }

    static void Main() {
        Test<Counter>();
    }
}
```

用于 `Increment` 修改变量中的值的第一次调用 `x` 。 这与对的第二次调用不等效 `Increment` ，后者修改的装箱副本中的值 `x` 。 因此，该程序的输出为：
```console
0
1
1
```

有关装箱和取消装箱的更多详细信息，请参阅 [装箱和取消装箱](types.md#boxing-and-unboxing)。

### <a name="meaning-of-this"></a>含义

在类的实例构造函数或实例函数成员内， `this` 分类为值。 因此，虽然 `this` 可用于引用调用了函数成员的实例，但不能 `this` 在类的函数成员中对赋值。

在结构的实例构造函数中， `this` 对应于结构 `out` 类型的参数，并在结构的实例函数成员内与 `this` `ref` 结构类型的参数对应。 在这两种情况下， `this` 都归类为变量，并且可以通过将 `this` 此函数作为或参数传递来修改调用了函数成员的整个结构 `ref` `out` 。

### <a name="field-initializers"></a>字段初始值设定项

如 " [默认值](structs.md#default-values)" 中所述，结构的默认值由将所有值类型字段设置为其默认值并将所有引用类型字段设置为而产生的值 `null` 。 出于此原因，结构不允许实例字段声明包含变量初始值设定项。 此限制仅适用于实例字段。 允许结构的静态字段包含变量初始值设定项。

示例
```csharp
struct Point
{
    public int x = 1;  // Error, initializer not permitted
    public int y = 1;  // Error, initializer not permitted
}
```
出现错误，因为实例字段声明包含变量初始值设定项。

### <a name="constructors"></a>构造函数

与类不同，结构不允许声明无参数的实例构造函数。 相反，每个结构都隐式包含一个无参数的实例构造函数，该构造函数始终返回将所有值类型字段设置为其默认值并将所有引用类型字段设置为 null ([默认构造函数](types.md#default-constructors)) 时得出的值。 结构可声明具有参数的实例构造函数。 例如
```csharp
struct Point
{
    int x, y;

    public Point(int x, int y) {
        this.x = x;
        this.y = y;
    }
}
```

给定上述声明，语句
```csharp
Point p1 = new Point();
Point p2 = new Point(0, 0);
```
两者均使用创建一个 `Point` `x` ，并 `y` 将其初始化为零。

不允许结构实例构造函数包含形式的构造函数初始值设定项 `base(...)` 。

如果结构实例构造函数未指定构造函数初始值设定项，则 `this` 变量与 `out` 结构类型的参数对应，与 `out` 参数类似，必须在 `this` 构造函数返回的每个位置 ([明确赋值](variables.md#definite-assignment)) 明确赋值。 如果结构实例构造函数指定构造函数初始值设定项，则 `this` 变量与结构类型的参数相对应，与 `ref` 参数类似 `ref` ， `this` 在进入构造函数主体时被视为已明确赋值。 请考虑下面的实例构造函数实现：
```csharp
struct Point
{
    int x, y;

    public int X {
        set { x = value; }
    }

    public int Y {
        set { y = value; }
    }

    public Point(int x, int y) {
        X = x;        // error, this is not yet definitely assigned
        Y = y;        // error, this is not yet definitely assigned
    }
}
```

不 (包括属性和) 的 set 访问器的实例成员函数 `X` ， `Y` 直到构造的结构的所有字段都已明确赋值。 唯一的例外涉及自动实现属性， ([自动实现](classes.md#automatically-implemented-properties)) 的属性。 明确的赋值规则 ([简单赋值表达式](variables.md#simple-assignment-expressions) ，) 在该结构类型的实例构造函数中明确分配对结构类型的自动属性的赋值：此类赋值被视为自动属性的隐藏支持字段的明确分配。 因此，可以使用以下内容：

```csharp
struct Point
{
    public int X { get; set; }
    public int Y { get; set; }

    public Point(int x, int y) {
        X = x;      // allowed, definitely assigns backing field
        Y = y;      // allowed, definitely assigns backing field
    }
```

### <a name="destructors"></a>析构函数

不允许结构声明析构函数。

### <a name="static-constructors"></a>静态构造函数

结构的静态构造函数遵循与类相同的大多数规则。 在应用程序域中发生以下事件的第一个事件时，将触发结构类型的静态构造函数的执行：

*  引用结构类型的静态成员。
*  将调用结构类型的显式声明的构造函数。

 (默认值) 结构类型的 [默认](structs.md#default-values) 值的创建不会触发静态构造函数。  (的一个示例是数组中元素的初始值。 ) 

## <a name="struct-examples"></a>结构示例

下面显示了两个使用 `struct` 类型创建类型的示例，这些类型可以与语言的预定义类型相似，但使用修改后的语义。

### <a name="database-integer-type"></a>数据库整数类型

`DBInt`下面的结构实现整数类型，该类型可以表示类型的完整值集 `int` ，另外还实现了指示未知值的附加状态。 具有这些特征的类型通常用于数据库。

```csharp
using System;

public struct DBInt
{
    // The Null member represents an unknown DBInt value.

    public static readonly DBInt Null = new DBInt();

    // When the defined field is true, this DBInt represents a known value
    // which is stored in the value field. When the defined field is false,
    // this DBInt represents an unknown value, and the value field is 0.

    int value;
    bool defined;

    // Private instance constructor. Creates a DBInt with a known value.

    DBInt(int value) {
        this.value = value;
        this.defined = true;
    }

    // The IsNull property is true if this DBInt represents an unknown value.

    public bool IsNull { get { return !defined; } }

    // The Value property is the known value of this DBInt, or 0 if this
    // DBInt represents an unknown value.

    public int Value { get { return value; } }

    // Implicit conversion from int to DBInt.

    public static implicit operator DBInt(int x) {
        return new DBInt(x);
    }

    // Explicit conversion from DBInt to int. Throws an exception if the
    // given DBInt represents an unknown value.

    public static explicit operator int(DBInt x) {
        if (!x.defined) throw new InvalidOperationException();
        return x.value;
    }

    public static DBInt operator +(DBInt x) {
        return x;
    }

    public static DBInt operator -(DBInt x) {
        return x.defined ? -x.value : Null;
    }

    public static DBInt operator +(DBInt x, DBInt y) {
        return x.defined && y.defined? x.value + y.value: Null;
    }

    public static DBInt operator -(DBInt x, DBInt y) {
        return x.defined && y.defined? x.value - y.value: Null;
    }

    public static DBInt operator *(DBInt x, DBInt y) {
        return x.defined && y.defined? x.value * y.value: Null;
    }

    public static DBInt operator /(DBInt x, DBInt y) {
        return x.defined && y.defined? x.value / y.value: Null;
    }

    public static DBInt operator %(DBInt x, DBInt y) {
        return x.defined && y.defined? x.value % y.value: Null;
    }

    public static DBBool operator ==(DBInt x, DBInt y) {
        return x.defined && y.defined? x.value == y.value: DBBool.Null;
    }

    public static DBBool operator !=(DBInt x, DBInt y) {
        return x.defined && y.defined? x.value != y.value: DBBool.Null;
    }

    public static DBBool operator >(DBInt x, DBInt y) {
        return x.defined && y.defined? x.value > y.value: DBBool.Null;
    }

    public static DBBool operator <(DBInt x, DBInt y) {
        return x.defined && y.defined? x.value < y.value: DBBool.Null;
    }

    public static DBBool operator >=(DBInt x, DBInt y) {
        return x.defined && y.defined? x.value >= y.value: DBBool.Null;
    }

    public static DBBool operator <=(DBInt x, DBInt y) {
        return x.defined && y.defined? x.value <= y.value: DBBool.Null;
    }

    public override bool Equals(object obj) {
        if (!(obj is DBInt)) return false;
        DBInt x = (DBInt)obj;
        return value == x.value && defined == x.defined;
    }

    public override int GetHashCode() {
        return value;
    }

    public override string ToString() {
        return defined? value.ToString(): "DBInt.Null";
    }
}
```

### <a name="database-boolean-type"></a>数据库布尔类型

`DBBool`下面的结构实现了三值逻辑类型。 此类型的可能值为 `DBBool.True` 、 `DBBool.False` 和 `DBBool.Null` ，其中 `Null` 成员指示未知值。 这三值逻辑类型通常用于数据库。

```csharp
using System;

public struct DBBool
{
    // The three possible DBBool values.

    public static readonly DBBool Null = new DBBool(0);
    public static readonly DBBool False = new DBBool(-1);
    public static readonly DBBool True = new DBBool(1);

    // Private field that stores -1, 0, 1 for False, Null, True.

    sbyte value;

    // Private instance constructor. The value parameter must be -1, 0, or 1.

    DBBool(int value) {
        this.value = (sbyte)value;
    }

    // Properties to examine the value of a DBBool. Return true if this
    // DBBool has the given value, false otherwise.

    public bool IsNull { get { return value == 0; } }

    public bool IsFalse { get { return value < 0; } }

    public bool IsTrue { get { return value > 0; } }

    // Implicit conversion from bool to DBBool. Maps true to DBBool.True and
    // false to DBBool.False.

    public static implicit operator DBBool(bool x) {
        return x? True: False;
    }

    // Explicit conversion from DBBool to bool. Throws an exception if the
    // given DBBool is Null, otherwise returns true or false.

    public static explicit operator bool(DBBool x) {
        if (x.value == 0) throw new InvalidOperationException();
        return x.value > 0;
    }

    // Equality operator. Returns Null if either operand is Null, otherwise
    // returns True or False.

    public static DBBool operator ==(DBBool x, DBBool y) {
        if (x.value == 0 || y.value == 0) return Null;
        return x.value == y.value? True: False;
    }

    // Inequality operator. Returns Null if either operand is Null, otherwise
    // returns True or False.

    public static DBBool operator !=(DBBool x, DBBool y) {
        if (x.value == 0 || y.value == 0) return Null;
        return x.value != y.value? True: False;
    }

    // Logical negation operator. Returns True if the operand is False, Null
    // if the operand is Null, or False if the operand is True.

    public static DBBool operator !(DBBool x) {
        return new DBBool(-x.value);
    }

    // Logical AND operator. Returns False if either operand is False,
    // otherwise Null if either operand is Null, otherwise True.

    public static DBBool operator &(DBBool x, DBBool y) {
        return new DBBool(x.value < y.value? x.value: y.value);
    }

    // Logical OR operator. Returns True if either operand is True, otherwise
    // Null if either operand is Null, otherwise False.

    public static DBBool operator |(DBBool x, DBBool y) {
        return new DBBool(x.value > y.value? x.value: y.value);
    }

    // Definitely true operator. Returns true if the operand is True, false
    // otherwise.

    public static bool operator true(DBBool x) {
        return x.value > 0;
    }

    // Definitely false operator. Returns true if the operand is False, false
    // otherwise.

    public static bool operator false(DBBool x) {
        return x.value < 0;
    }

    public override bool Equals(object obj) {
        if (!(obj is DBBool)) return false;
        return value == ((DBBool)obj).value;
    }

    public override int GetHashCode() {
        return value;
    }

    public override string ToString() {
        if (value > 0) return "DBBool.True";
        if (value < 0) return "DBBool.False";
        return "DBBool.Null";
    }
}
```

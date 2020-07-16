---
ms.openlocfilehash: eff00c4571054dcd07b235fe6d505838ee1dec94
ms.sourcegitcommit: e149d1e9c0cf666ae4bea1c3ceeac6171ef285d4
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/15/2020
ms.locfileid: "86403086"
---
# <a name="lexical-structure"></a>词法结构

## <a name="programs"></a>节目

C #***程序***由一个或多个***源文件***组成，该文件正式称为***编译单元***（[编译单元](namespaces.md#compilation-units)）。 源文件是 Unicode 字符的有序序列。 源文件与文件系统中的文件通常具有一对一的对应关系，但不需要此函件。 为获得最大的可移植性，建议使用 UTF-8 编码对文件系统中的文件进行编码。

从概念上讲，程序是使用三个步骤编译的：

1. 转换，将特定字符已知和编码方案中的文件转换为 Unicode 字符序列。
2. 词法分析，将 Unicode 输入字符流转换为标记流。
3. 语法分析，将令牌流转换为可执行代码。

## <a name="grammars"></a>语法

此规范提供使用两个语法的 c # 编程语言的语法。 ***词法语法***（[词法语法](lexical-structure.md#lexical-grammar)）定义 Unicode 字符如何合并为行终止符、空格、注释、标记和预处理指令。 ***句法文法***（[句法文法](lexical-structure.md#syntactic-grammar)）定义如何组合词汇语法生成的标记以形成 c # 程序。

### <a name="grammar-notation"></a>语法表示法

词法语法和句法语法以巴科斯-诺尔范式形式显示，使用 ANTLR 语法工具的表示法。

### <a name="lexical-grammar"></a>词法语法

C # 的词法语法显示在[词法分析](lexical-structure.md#lexical-analysis)、[标记](lexical-structure.md#tokens)和[预处理指令](lexical-structure.md#pre-processing-directives)中。 词法语法的终端符号是 Unicode 字符集的字符，并且词法语法指定如何将字符组合到一起形成标记（[标记](lexical-structure.md#tokens)）[、空白（空格）、](lexical-structure.md#white-space)注释（[注释](lexical-structure.md#comments)）和预处理指令（[预处理指令](lexical-structure.md#pre-processing-directives)）。

C # 程序中的每个源文件都必须符合词法文法的*输入*生产（[词法分析](lexical-structure.md#lexical-analysis)）。

### <a name="syntactic-grammar"></a>语法语法

C # 的句法语法在本章后面的章节和附录中提供。 语法语法的终端符号是由词法语法定义的标记，句法语法指定如何组合标记以形成 c # 程序。

C # 程序中的每个源文件都必须符合句法文法*compilation_unit*生产（[编译单元](namespaces.md#compilation-units)）。

## <a name="lexical-analysis"></a>词法分析

*输入*生产定义 c # 源文件的词法结构。 C # 程序中的每个源文件都必须符合此词法文法生产。

```antlr
input
    : input_section?
    ;

input_section
    : input_section_part+
    ;

input_section_part
    : input_element* new_line
    | pp_directive
    ;

input_element
    : whitespace
    | comment
    | token
    ;
```

五个基本元素构成 c # 源文件的词法结构：行终止符（[行终止符](lexical-structure.md#line-terminators)）、空白（[空格）、](lexical-structure.md#white-space)注释（[注释](lexical-structure.md#comments)）、标记（[标记](lexical-structure.md#tokens)）和预处理指令（[预处理指令](lexical-structure.md#pre-processing-directives)）。 在这些基本元素中，只有标记在 c # 程序的句法语法中非常重要（[句法语法](lexical-structure.md#syntactic-grammar)）。

C # 源文件的词法处理包括将文件缩减为一系列标记，后者成为句法分析的输入。 行终止符、空白和注释可用于分隔标记，预处理指令可能会导致跳过源文件的各个部分，否则，这些词法元素不会影响 c # 程序的语法结构。

在内插字符串文本（内[插字符串文本](lexical-structure.md#interpolated-string-literals)）的情况下，单个标记最初由词法分析生成，但被分解为多个输入元素，这些输入元素会反复进入词法分析状态，直到所有内插字符串文本均已解决。 然后，生成的令牌作为句法分析的输入。

当多个词法语法生产与源文件中的一系列字符匹配时，词法处理始终形成可能的最长词汇元素。 例如，字符序列 `//` 作为单行注释的开头处理，因为该词法元素比单个 `/` 标记长。

### <a name="line-terminators"></a>行终止符

行结束符将 c # 源文件中的字符分为多行。

```antlr
new_line
    : '<Carriage return character (U+000D)>'
    | '<Line feed character (U+000A)>'
    | '<Carriage return character (U+000D) followed by line feed character (U+000A)>'
    | '<Next line character (U+0085)>'
    | '<Line separator character (U+2028)>'
    | '<Paragraph separator character (U+2029)>'
    ;
```

为了与添加文件结尾标记的源代码编辑工具兼容，若要将源文件视为一系列正确终止的行，请按顺序将以下转换应用于 c # 程序中的每个源文件：

*  如果源文件的最后一个字符是 Control Z 字符（ `U+001A` ），则删除该字符。
*  如果源文件不 `U+000D` 是空的，并且源文件的最后一个字符不是回车符（ `U+000D` ）、换行符（ `U+000A` ）、行分隔符（ `U+2028` ）或段落分隔符（），则将回车符（）添加到源文件的末尾 `U+2029` 。

### <a name="comments"></a>注释

支持两种形式的注释：单行注释和分隔注释。 S 注释以字符 `//` 开头，并扩展到源行的末尾。 分隔注释以字符 `/*` 开头，以字符 `*/` 结尾。 分隔注释可能跨多行。

```antlr
comment
    : single_line_comment
    | delimited_comment
    ;

single_line_comment
    : '//' input_character*
    ;

input_character
    : '<Any Unicode character except a new_line_character>'
    ;

new_line_character
    : '<Carriage return character (U+000D)>'
    | '<Line feed character (U+000A)>'
    | '<Next line character (U+0085)>'
    | '<Line separator character (U+2028)>'
    | '<Paragraph separator character (U+2029)>'
    ;

delimited_comment
    : '/*' delimited_comment_section* asterisk+ '/'
    ;

delimited_comment_section
    : '/'
    | asterisk* not_slash_or_asterisk
    ;

asterisk
    : '*'
    ;

not_slash_or_asterisk
    : '<Any Unicode character except / or *>'
    ;
```

注释不嵌套。 字符序列 `/*` `*/` 在注释中没有特殊含义，并且 `//` 字符序列在 `//` `/*` 分隔注释中没有特殊含义。

在字符和字符串文本中不处理注释。

示例
```csharp
/* Hello, world program
   This program writes "hello, world" to the console
*/
class Hello
{
    static void Main() {
        System.Console.WriteLine("hello, world");
    }
}
```
包含分隔注释。

示例
```csharp
// Hello, world program
// This program writes "hello, world" to the console
//
class Hello // any name will do for this class
{
    static void Main() { // this method must be named "Main"
        System.Console.WriteLine("hello, world");
    }
}
```
显示若干单行注释。

### <a name="white-space"></a>空格

空格定义为带有 Unicode 类 Zs 的任何字符（包括空格字符）以及水平制表符、垂直制表符、换行符和换页符。

```antlr
whitespace
    : '<Any character with Unicode class Zs>'
    | '<Horizontal tab character (U+0009)>'
    | '<Vertical tab character (U+000B)>'
    | '<Form feed character (U+000C)>'
    ;
```

## <a name="tokens"></a>令牌

有多种类型的令牌：标识符、关键字、文本、运算符和标点符号。 空白和注释不是标记，不过它们充当标记的分隔符。

```antlr
token
    : identifier
    | keyword
    | integer_literal
    | real_literal
    | character_literal
    | string_literal
    | interpolated_string_literal
    | operator_or_punctuator
    ;
```

### <a name="unicode-character-escape-sequences"></a>Unicode 字符转义序列

Unicode 字符转义序列表示一个 Unicode 字符。 Unicode 字符转义序列在标识符（[标识符](lexical-structure.md#identifiers)）、字符文本（[字符文本](lexical-structure.md#character-literals)）和常规字符串文本（[字符串文字](lexical-structure.md#string-literals)）中进行处理。 不会在任何其他位置（例如，使用 operator、标点符号或关键字）处理 Unicode 字符转义。

```antlr
unicode_escape_sequence
    : '\\u' hex_digit hex_digit hex_digit hex_digit
    | '\\U' hex_digit hex_digit hex_digit hex_digit hex_digit hex_digit hex_digit hex_digit
    ;
```

Unicode 转义序列表示由 " `\u` " 或 "" 字符后面的十六进制数构成的单个 unicode 字符 `\U` 。 由于 c # 使用字符和字符串值中的 Unicode 码位的16位编码，字符文本中不允许使用 U + 10000 到 U + 10FFFF 范围内的 Unicode 字符，而是使用字符串文本中的 Unicode 代理项对来表示。 不支持0x10FFFF 以上的码位的 Unicode 字符。

不会执行多个转换。 例如，字符串文本 " `\u005Cu005C` " 等效于 " `\u005C` "，而不是 " `\` "。 Unicode 值 `\u005C` 为字符 " `\` "。

示例
```csharp
class Class1
{
    static void Test(bool \u0066) {
        char c = '\u0066';
        if (\u0066)
            System.Console.WriteLine(c.ToString());
    }        
}
```
显示了的几个用法 `\u0066` ，它是字母 "" 的转义序列 `f` 。 该程序等效于
```csharp
class Class1
{
    static void Test(bool f) {
        char c = 'f';
        if (f)
            System.Console.WriteLine(c.ToString());
    }        
}
```

### <a name="identifiers"></a>标识符

本节中给出的标识符规则完全与 Unicode 标准附录31推荐的规则相对应，只不过允许将下划线作为初始字符（如 C 编程语言中的传统）、标识符中允许使用 Unicode 转义序列，并允许将 " `@` " 字符作为前缀，以使关键字可用作标识符。

```antlr
identifier
    : available_identifier
    | '@' identifier_or_keyword
    ;

available_identifier
    : '<An identifier_or_keyword that is not a keyword>'
    ;

identifier_or_keyword
    : identifier_start_character identifier_part_character*
    ;

identifier_start_character
    : letter_character
    | '_'
    ;

identifier_part_character
    : letter_character
    | decimal_digit_character
    | connecting_character
    | combining_character
    | formatting_character
    ;

letter_character
    : '<A Unicode character of classes Lu, Ll, Lt, Lm, Lo, or Nl>'
    | '<A unicode_escape_sequence representing a character of classes Lu, Ll, Lt, Lm, Lo, or Nl>'
    ;

combining_character
    : '<A Unicode character of classes Mn or Mc>'
    | '<A unicode_escape_sequence representing a character of classes Mn or Mc>'
    ;

decimal_digit_character
    : '<A Unicode character of the class Nd>'
    | '<A unicode_escape_sequence representing a character of the class Nd>'
    ;

connecting_character
    : '<A Unicode character of the class Pc>'
    | '<A unicode_escape_sequence representing a character of the class Pc>'
    ;

formatting_character
    : '<A Unicode character of the class Cf>'
    | '<A unicode_escape_sequence representing a character of the class Cf>'
    ;
```

有关上面提到的 Unicode 字符类的信息，请参阅 Unicode 标准版本3.0，第4.5 节。

有效标识符的示例包括 " `identifier1` "、" `_identifier2` " 和 " `@if` "。

符合标准的程序中的标识符必须是 Unicode 范式 C 定义的规范格式，如 Unicode 标准附录15所定义。 如果遇到非范式规范的标识符，则该行为是实现定义的;但是，不需要诊断。

前缀 " `@` " 允许将关键字用作标识符，这在与其他编程语言交互时非常有用。 该字符 `@` 实际上不是标识符的一部分，因此标识符可能以其他语言显示为普通标识符，不含前缀。 带有前缀的标识符 `@` 称为***逐字标识符***。 `@`允许对不是关键字的标识符使用前缀，但强烈建议不要使用它作为样式。

示例：
```csharp
class @class
{
    public static void @static(bool @bool) {
        if (@bool)
            System.Console.WriteLine("true");
        else
            System.Console.WriteLine("false");
    }    
}

class Class1
{
    static void M() {
        cl\u0061ss.st\u0061tic(true);
    }
}
```
定义名为 "" 的类，该类具有名为 "" `class` 的静态方法 `static` ，该方法采用名为 "" 的参数 `bool` 。 请注意，由于关键字中不允许使用 Unicode 转义，因此标记 " `cl\u0061ss` " 是标识符，与 "" 具有相同的标识符 `@class` 。

如果两个标识符在应用以下转换后相同，则将其视为相同：

*  删除前缀 " `@` " （如果使用）。
*  每个*unicode_escape_sequence*都转换为其对应的 unicode 字符。
*  删除任何*formatting_character*。

包含两个连续下划线字符（ `U+005F` ）的标识符保留给实现使用。 例如，实现可能提供以两个下划线开头的扩展关键字。

### <a name="keywords"></a>关键字

***关键字***是类似于标识符的字符序列（保留），不能用作标识符，除非以 `@` 字符开头。

```antlr
keyword
    : 'abstract' | 'as'       | 'base'       | 'bool'      | 'break'
    | 'byte'     | 'case'     | 'catch'      | 'char'      | 'checked'
    | 'class'    | 'const'    | 'continue'   | 'decimal'   | 'default'
    | 'delegate' | 'do'       | 'double'     | 'else'      | 'enum'
    | 'event'    | 'explicit' | 'extern'     | 'false'     | 'finally'
    | 'fixed'    | 'float'    | 'for'        | 'foreach'   | 'goto'
    | 'if'       | 'implicit' | 'in'         | 'int'       | 'interface'
    | 'internal' | 'is'       | 'lock'       | 'long'      | 'namespace'
    | 'new'      | 'null'     | 'object'     | 'operator'  | 'out'
    | 'override' | 'params'   | 'private'    | 'protected' | 'public'
    | 'readonly' | 'ref'      | 'return'     | 'sbyte'     | 'sealed'
    | 'short'    | 'sizeof'   | 'stackalloc' | 'static'    | 'string'
    | 'struct'   | 'switch'   | 'this'       | 'throw'     | 'true'
    | 'try'      | 'typeof'   | 'uint'       | 'ulong'     | 'unchecked'
    | 'unsafe'   | 'ushort'   | 'using'      | 'virtual'   | 'void'
    | 'volatile' | 'while'
    ;
```

在语法中的某些位置，特定标识符具有特殊意义，但不是关键字。 此类标识符有时称为 "上下文关键字"。 例如，在属性声明中，" `get` " 和 " `set` " 标识符具有特殊意义（[取值函数](classes.md#accessors)）。 此位置不允许使用或以外的标识符 `get` `set` ，因此，此使用不会与使用这些字词作为标识符冲突。 在其他情况下，如 `var` 在隐式类型化局部变量声明（[局部变量声明](statements.md#local-variable-declarations)）中使用标识符 "" 时，上下文关键字可能与声明的名称冲突。 在这种情况下，声明的名称优先于将标识符用作上下文关键字。

### <a name="literals"></a>文字

文本是值的源代码表示形式。

```antlr
literal
    : boolean_literal
    | integer_literal
    | real_literal
    | character_literal
    | string_literal
    | null_literal
    ;
```

#### <a name="boolean-literals"></a>布尔值文字

有两个布尔文本值： `true` 和 `false` 。

```antlr
boolean_literal
    : 'true'
    | 'false'
    ;
```

*Boolean_literal*的类型为 `bool` 。

#### <a name="integer-literals"></a>整数文本

整数文本用于写入类型为、、和的值 `int` `uint` `long` `ulong` 。 整数文本具有两种可能的形式： decimal 和十六进制。

```antlr
integer_literal
    : decimal_integer_literal
    | hexadecimal_integer_literal
    ;

decimal_integer_literal
    : decimal_digit+ integer_type_suffix?
    ;

decimal_digit
    : '0' | '1' | '2' | '3' | '4' | '5' | '6' | '7' | '8' | '9'
    ;

integer_type_suffix
    : 'U' | 'u' | 'L' | 'l' | 'UL' | 'Ul' | 'uL' | 'ul' | 'LU' | 'Lu' | 'lU' | 'lu'
    ;

hexadecimal_integer_literal
    : '0x' hex_digit+ integer_type_suffix?
    | '0X' hex_digit+ integer_type_suffix?
    ;

hex_digit
    : '0' | '1' | '2' | '3' | '4' | '5' | '6' | '7' | '8' | '9'
    | 'A' | 'B' | 'C' | 'D' | 'E' | 'F' | 'a' | 'b' | 'c' | 'd' | 'e' | 'f';
```

确定整数文本的类型，如下所示：

*  如果文字没有后缀，则其值可以表示为以下类型的第一个类型： `int` 、 `uint` 、 `long` 和 `ulong` 。
*  如果文本的后缀为 `U` 或 `u` ，则它具有以下类型中可以表示其值的第一个类型： `uint` 、 `ulong` 。
*  如果文本的后缀为 `L` 或 `l` ，则它具有以下类型中可以表示其值的第一个类型： `long` 、 `ulong` 。
*  如果文本的后缀为 `UL` 、 `Ul` 、、、、、 `uL` `ul` `LU` `Lu` `lU` 或，则 `lu` 它属于类型 `ulong` 。

如果整数文本所表示的值超出了该类型的范围 `ulong` ，则会发生编译时错误。

作为样式，建议 `L` 在写入类型的文本时使用 "" 而不是 "" `l` `long` ，因为这样可以很容易地将字母 " `l` " 与数字 " `1` " 混淆。

若要允许尽可能小的 `int` 和 `long` 值写入为十进制整数文本，请满足以下两个规则：

* 当值为2147483648（2 ^ 31）且没有*integer_type_suffix*的*decimal_integer_literal*显示为紧跟一元减号运算符（[一元减号运算符](expressions.md#unary-minus-operator)）的标记时，结果为类型 `int` 值为-2147483648 （-2 ^ 31）的常量。 在所有其他情况下，这类*decimal_integer_literal*的类型为 `uint` 。
* 当*decimal_integer_literal*值为9223372036854775808（2 ^ 63）且没有*integer_type_suffix*或*integer_type_suffix*时 `L` ，或在 `l` 一元减号运算符（[一元减号运算符](expressions.md#unary-minus-operator)）之后立即显示标记时，结果为类型的常量，其 `long` 值为-9223372036854775808 （-2 ^ 63）。 在所有其他情况下，这类*decimal_integer_literal*的类型为 `ulong` 。

#### <a name="real-literals"></a>真实文本

真实文本用于写入类型为 `float` 、和的值 `double` `decimal` 。

```antlr
real_literal
    : decimal_digit+ '.' decimal_digit+ exponent_part? real_type_suffix?
    | '.' decimal_digit+ exponent_part? real_type_suffix?
    | decimal_digit+ exponent_part real_type_suffix?
    | decimal_digit+ real_type_suffix
    ;

exponent_part
    : 'e' sign? decimal_digit+
    | 'E' sign? decimal_digit+
    ;

sign
    : '+'
    | '-'
    ;

real_type_suffix
    : 'F' | 'f' | 'D' | 'd' | 'M' | 'm'
    ;
```

如果未指定*real_type_suffix* ，则真实文本的类型为 `double` 。 否则，实数类型后缀将确定真实文本的类型，如下所示：

*  用 `F` 或作为后缀的实文本 `f` 的类型为 `float` 。 例如，文本、、 `1f` `1.5f` `1e10f` 和 `123.456F` 都是类型 `float` 。
*  用 `D` 或作为后缀的实文本 `d` 的类型为 `double` 。 例如，文本、、 `1d` `1.5d` `1e10d` 和 `123.456D` 都是类型 `double` 。
*  用 `M` 或作为后缀的实文本 `m` 的类型为 `decimal` 。 例如，文本、、 `1m` `1.5m` `1e10m` 和 `123.456M` 都是类型 `decimal` 。 通过采用精确值将此文本转换为一个 `decimal` 值，并在必要时使用银行家舍入（[decimal 类型](types.md#the-decimal-type)）舍入为最接近的可表示值。 除非值舍入或值为零（在这种情况下，正负号为0），否则文本中的任何小数位数都将保留。 因此， `2.900m` 将分析文本，以形成带有符号 `0` 、系数 `2900` 和刻度的小数 `3` 。

如果指定的文本不能用指定的类型表示，则会发生编译时错误。

类型或的实字的值 `float` `double` 是通过使用 IEEE "舍入到最近的" 模式确定的。

请注意，在实际文本中，小数点后始终需要小数位数。 例如， `1.3F` 是一个真实文本，但 `1.F` 不是。

#### <a name="character-literals"></a>字符文本

字符文本表示单个字符，通常由引号中的字符组成，如中所示 `'a'` 。

注意： ANTLR 语法表示法会使以下混乱！ 在 ANTLR 中，当您编写时， `\'` 它代表一个引号 `'` 。 当你编写时， `\\` 它代表单个反斜杠 `\` 。 因此，字符文本的第一个规则意味着以单个单引号开始，然后是一个字符，然后是一个引号。 还有11个可能的简单转义序列 `\'` ：、、、 `\"` `\\` `\0` 、 `\a` 、 `\b` `\f` `\n` `\r` `\t` `\v` 、、、、、。

```antlr
character_literal
    : '\'' character '\''
    ;

character
    : single_character
    | simple_escape_sequence
    | hexadecimal_escape_sequence
    | unicode_escape_sequence
    ;

single_character
    : '<Any character except \' (U+0027), \\ (U+005C), and new_line_character>'
    ;

simple_escape_sequence
    : '\\\'' | '\\"' | '\\\\' | '\\0' | '\\a' | '\\b' | '\\f' | '\\n' | '\\r' | '\\t' | '\\v'
    ;

hexadecimal_escape_sequence
    : '\\x' hex_digit hex_digit? hex_digit? hex_digit?;
```

在字符中跟在反斜杠字符（）后面的字符 `\` 必须是下列字符之一： *character* `'` 、 `"` 、 `\` 、、 `0` `a` `b` `f` `n` `r` `t` `u` `U` `x` `v` 、、、、、、、、、。 否则，将发生编译时错误。

十六进制转义序列表示单个 Unicode 字符，其值由 "" 后面的十六进制数构成 `\x` 。

如果字符文本表示的值大于 `U+FFFF` ，则会发生编译时错误。

字符文本中的 Unicode 字符转义序列（[unicode 字符转义序列](lexical-structure.md#unicode-character-escape-sequences)）必须在到的范围内 `U+0000` `U+FFFF` 。

简单的转义序列表示 Unicode 字符编码，如下表中所述。


| __转义序列__ | __字符名称__ | __Unicode 编码__ |
|---------------------|--------------------|----------------------|
| `\'`                | 单引号       | `0x0027`             | 
| `\"`                | 双引号       | `0x0022`             | 
| `\\`                | 反斜杠          | `0x005C`             | 
| `\0`                | Null               | `0x0000`             | 
| `\a`                | 警报              | `0x0007`             | 
| `\b`                | Backspace          | `0x0008`             | 
| `\f`                | 换页          | `0x000C`             | 
| `\n`                | 换行           | `0x000A`             | 
| `\r`                | 回车    | `0x000D`             | 
| `\t`                | 水平制表符     | `0x0009`             | 
| `\v`                | 垂直制表符       | `0x000B`             | 

*Character_literal*的类型为 `char` 。

#### <a name="string-literals"></a>字符串文本

C # 支持两种形式的字符串文本：***常规字符串文本***和***原义字符串***。

正则字符串文字由零个或多个字符括在双引号中，如中所示， `"hello"` 并且可能包括简单转义序列（如 `\t` 制表符）和十六进制和 Unicode 转义序列。

原义字符串包含一个 `@` 字符，后跟一个双引号字符、零个或多个字符和一个右双引号字符。 一个简单的示例是 `@"hello"` 。 在原义字符串文本中，分隔符之间的字符按原义解释，唯一的例外是*quote_escape_sequence*。 具体而言，简单转义序列和十六进制和 Unicode 转义序列不会在原义字符串文本中处理。 原义字符串文本可以跨多个行。

```antlr
string_literal
    : regular_string_literal
    | verbatim_string_literal
    ;

regular_string_literal
    : '"' regular_string_literal_character* '"'
    ;

regular_string_literal_character
    : single_regular_string_literal_character
    | simple_escape_sequence
    | hexadecimal_escape_sequence
    | unicode_escape_sequence
    ;

single_regular_string_literal_character
    : '<Any character except " (U+0022), \\ (U+005C), and new_line_character>'
    ;

verbatim_string_literal
    : '@"' verbatim_string_literal_character* '"'
    ;

verbatim_string_literal_character
    : single_verbatim_string_literal_character
    | quote_escape_sequence
    ;

single_verbatim_string_literal_character
    : '<any character except ">'
    ;

quote_escape_sequence
    : '""'
    ;
```

在 regular_string_literal_character 中的反斜杠字符（ `\` ）后面的*regular_string_literal_character*字符必须是下列字符之一： `'` 、 `"` 、 `\` 、 `0` 、 `a` `b` `f` `n` `r` `t` `u` `U` `x` `v` 、、、、、、、、、。 否则，将发生编译时错误。

示例
```csharp
string a = "hello, world";                   // hello, world
string b = @"hello, world";                  // hello, world

string c = "hello \t world";                 // hello      world
string d = @"hello \t world";                // hello \t world

string e = "Joe said \"Hello\" to me";       // Joe said "Hello" to me
string f = @"Joe said ""Hello"" to me";      // Joe said "Hello" to me

string g = "\\\\server\\share\\file.txt";    // \\server\share\file.txt
string h = @"\\server\share\file.txt";       // \\server\share\file.txt

string i = "one\r\ntwo\r\nthree";
string j = @"one
two
three";
```
显示各种字符串文本。 最后一个字符串文字 `j` 是跨多行的逐字字符串。 引号之间的字符（包括空白字符，如换行符）会逐字保留。

由于十六进制转义序列可以具有可变数量的十六进制数字，因此字符串文本 `"\x123"` 包含一个具有十六进制值123的单个字符。 若要创建一个字符串，该字符串包含的字符的十六进制值为12，后跟字符3，则可以写 `"\x00123"` `"\x12" + "3"` 。

*String_literal*的类型为 `string` 。

每个字符串文本不一定会生成新的字符串实例。 如果两个或更多的字符串文本在同一程序中出现，则根据字符串相等运算符（[字符串相等运算符](expressions.md#string-equality-operators)）进行等效时，这些字符串将引用相同的字符串实例。 例如，生成的输出
```csharp
class Test
{
    static void Main() {
        object a = "hello";
        object b = "hello";
        System.Console.WriteLine(a == b);
    }
}
```
是 `True` 因为两个文本引用相同的字符串实例。

#### <a name="interpolated-string-literals"></a>内插字符串文本

内插字符串与字符串文本类似，但包含用 and 分隔的孔，其中的 `{` `}` 表达式可以出现。 在运行时，将对表达式进行计算，目的是将其文本窗体替换为发生该洞的位置的字符串。 字符串内插的语法和语义在节（内[插字符串](expressions.md#interpolated-strings)）中进行了介绍。

与字符串文本一样，内插字符串文本可以是正则为或是原义字符串。 内插正则字符串文本由 `$"` 和分隔 `"` ，并由和分隔逐字字符串。 `$@"` `"`

与其他文本一样，内插字符串的词法分析最初会根据下面的语法产生单个令牌。 但是，在句法分析之前，内插字符串的单个标记将被分解为包含该洞的字符串部分的多个标记，而洞中发生的输入元素会在词法上重新并非。 这反过来会生成更多的内插字符串文字，但如果词法上正确，最终将导致一系列标记，以便进行语法分析。

```antlr
interpolated_string_literal
    : '$' interpolated_regular_string_literal
    | '$' interpolated_verbatim_string_literal
    ;

interpolated_regular_string_literal
    : interpolated_regular_string_whole
    | interpolated_regular_string_start  interpolated_regular_string_literal_body interpolated_regular_string_end
    ;

interpolated_regular_string_literal_body
    : regular_balanced_text
    | interpolated_regular_string_literal_body interpolated_regular_string_mid regular_balanced_text
    ;

interpolated_regular_string_whole
    : '"' interpolated_regular_string_character* '"'
    ;

interpolated_regular_string_start
    : '"' interpolated_regular_string_character* '{'
    ;

interpolated_regular_string_mid
    : interpolation_format? '}' interpolated_regular_string_characters_after_brace? '{'
    ;

interpolated_regular_string_end
    : interpolation_format? '}' interpolated_regular_string_characters_after_brace? '"'
    ;

interpolated_regular_string_characters_after_brace
    : interpolated_regular_string_character_no_brace
    | interpolated_regular_string_characters_after_brace interpolated_regular_string_character
    ;

interpolated_regular_string_character
    : single_interpolated_regular_string_character
    | simple_escape_sequence
    | hexadecimal_escape_sequence
    | unicode_escape_sequence
    | open_brace_escape_sequence
    | close_brace_escape_sequence
    ;

interpolated_regular_string_character_no_brace
    : '<Any interpolated_regular_string_character except close_brace_escape_sequence and any hexadecimal_escape_sequence or unicode_escape_sequence designating } (U+007D)>'
    ;

single_interpolated_regular_string_character
    : '<Any character except \" (U+0022), \\ (U+005C), { (U+007B), } (U+007D), and new_line_character>'
    ;

open_brace_escape_sequence
    : '{{'
    ;

close_brace_escape_sequence
    : '}}'
    ;
    
regular_balanced_text
    : regular_balanced_text_part+
    ;

regular_balanced_text_part
    : single_regular_balanced_text_character
    | delimited_comment
    | '@' identifier_or_keyword
    | string_literal
    | interpolated_string_literal
    | '(' regular_balanced_text ')'
    | '[' regular_balanced_text ']'
    | '{' regular_balanced_text '}'
    ;
    
single_regular_balanced_text_character
    : '<Any character except / (U+002F), @ (U+0040), \" (U+0022), $ (U+0024), ( (U+0028), ) (U+0029), [ (U+005B), ] (U+005D), { (U+007B), } (U+007D) and new_line_character>'
    | '</ (U+002F), if not directly followed by / (U+002F) or * (U+002A)>'
    ;
    
interpolation_format
    : ':' interpolation_format_character+
    ;
    
interpolation_format_character
    : '<Any character except \" (U+0022), : (U+003A), { (U+007B) and } (U+007D)>'
    ;
    
interpolated_verbatim_string_literal
    : interpolated_verbatim_string_whole
    | interpolated_verbatim_string_start interpolated_verbatim_string_literal_body interpolated_verbatim_string_end
    ;

interpolated_verbatim_string_literal_body
    : verbatim_balanced_text
    | interpolated_verbatim_string_literal_body interpolated_verbatim_string_mid verbatim_balanced_text
    ;
    
interpolated_verbatim_string_whole
    : '@"' interpolated_verbatim_string_character* '"'
    ;
    
interpolated_verbatim_string_start
    : '@"' interpolated_verbatim_string_character* '{'
    ;
    
interpolated_verbatim_string_mid
    : interpolation_format? '}' interpolated_verbatim_string_characters_after_brace? '{'
    ;
    
interpolated_verbatim_string_end
    : interpolation_format? '}' interpolated_verbatim_string_characters_after_brace? '"'
    ;
    
interpolated_verbatim_string_characters_after_brace
    : interpolated_verbatim_string_character_no_brace
    | interpolated_verbatim_string_characters_after_brace interpolated_verbatim_string_character
    ;
    
interpolated_verbatim_string_character
    : single_interpolated_verbatim_string_character
    | quote_escape_sequence
    | open_brace_escape_sequence
    | close_brace_escape_sequence
    ;
    
interpolated_verbatim_string_character_no_brace
    : '<Any interpolated_verbatim_string_character except close_brace_escape_sequence>'
    ;
    
single_interpolated_verbatim_string_character
    : '<Any character except \" (U+0022), { (U+007B) and } (U+007D)>'
    ;
    
verbatim_balanced_text
    : verbatim_balanced_text_part+
    ;

verbatim_balanced_text_part
    : single_verbatim_balanced_text_character
    | comment
    | '@' identifier_or_keyword
    | string_literal
    | interpolated_string_literal
    | '(' verbatim_balanced_text ')'
    | '[' verbatim_balanced_text ']'
    | '{' verbatim_balanced_text '}'
    ;
    
single_verbatim_balanced_text_character
    : '<Any character except / (U+002F), @ (U+0040), \" (U+0022), $ (U+0024), ( (U+0028), ) (U+0029), [ (U+005B), ] (U+005D), { (U+007B) and } (U+007D)>'
    | '</ (U+002F), if not directly followed by / (U+002F) or * (U+002A)>'
    ;
```

*Interpolated_string_literal*令牌重新解释为多个令牌和其他输入元素，如下所示： *interpolated_string_literal*中出现的顺序：

* 以下各项分别作为单独的标记重新解释：前导 `$` 符号、 *interpolated_regular_string_whole*、 *interpolated_regular_string_start*、 *interpolated_regular_string_mid*、 *interpolated_regular_string_end*、 *interpolated_verbatim_string_whole*、 *interpolated_verbatim_string_start*、 *interpolated_verbatim_string_mid*和*interpolated_verbatim_string_end*。
* 在这些*regular_balanced_text*和*verbatim_balanced_text*之间发生的情况重新处理为*input_section* （[词法分析](lexical-structure.md#lexical-analysis)），并且重新解释为输入元素的结果序列。 这些转换可能会将内插字符串文本标记包含为重新解释。

语法分析会将令牌重新组合到*interpolated_string_expression* （内[插的字符串](expressions.md#interpolated-strings)）。

示例 TODO


#### <a name="the-null-literal"></a>Null 文本

```antlr
null_literal
    : 'null'
    ;
```

*Null_literal*可以隐式转换为引用类型或可以为 null 的类型。

### <a name="operators-and-punctuators"></a>运算符和标点符号

有多种运算符和标点符号。 表达式中使用运算符来描述涉及一个或多个操作数的操作。 例如，表达式 `a + b` 使用 `+` 运算符添加两个操作数 `a` 和 `b`。 标点符号用于分组和分隔。

```antlr
operator_or_punctuator
    : '{'  | '}'  | '['  | ']'  | '('   | ')'  | '.'  | ','  | ':'  | ';'
    | '+'  | '-'  | '*'  | '/'  | '%'   | '&'  | '|'  | '^'  | '!'  | '~'
    | '='  | '<'  | '>'  | '?'  | '??'  | '::' | '++' | '--' | '&&' | '||'
    | '->' | '==' | '!=' | '<=' | '>='  | '+=' | '-=' | '*=' | '/=' | '%='
    | '&=' | '|=' | '^=' | '<<' | '<<=' | '=>'
    ;

right_shift
    : '>>'
    ;

right_shift_assignment
    : '>>='
    ;
```

*Right_shift*和*right_shift_assignment*生产中的竖线用于指示，与句法语法中的其他生产不同，标记之间不允许任何类型的字符（甚至不允许使用空格）。 将对这些生产进行特殊处理，以便能够正确地处理*type_parameter_list*（[类型参数](classes.md#type-parameters)）。

## <a name="pre-processing-directives"></a>预处理指令

预处理指令提供按条件跳过源文件部分的功能，报告错误和警告条件，以及描述源代码的不同区域。 术语 "预处理指令" 仅用于与 C 和 c + + 编程语言的一致性。 在 c # 中，没有单独的预处理步骤;预处理指令作为词法分析阶段的一部分进行处理。

```antlr
pp_directive
    : pp_declaration
    | pp_conditional
    | pp_line
    | pp_diagnostic
    | pp_region
    | pp_pragma
    ;
```

以下预处理指令可用：

*  `#define`和 `#undef` ，分别用于定义和取消定义条件编译符号（[声明指令](lexical-structure.md#declaration-directives)）。
*  `#if`、 `#elif` 、 `#else` 和 `#endif` ，用于有条件地跳过源代码的各个部分（[条件编译指令](lexical-structure.md#conditional-compilation-directives)）。
*  `#line`，用于控制发出的错误和警告的行号（[行指令](lexical-structure.md#line-directives)）。
*  `#error`和 `#warning` ，分别用于发出错误和警告（[诊断指令](lexical-structure.md#diagnostic-directives)）。
*  `#region`和 `#endregion` ，用于显式标记源代码的各个部分（[Region 指令](lexical-structure.md#region-directives)）。
*  `#pragma`，用于指定编译器的可选上下文信息（[杂注指令](lexical-structure.md#pragma-directives)）。

预处理指令始终占用一行单独的源代码，并始终以 `#` 字符和预处理指令名称开头。 空格可能出现在字符和 `#` `#` 指令名称之间。

包含、、、、、、或指令的源行 `#define` `#undef` `#if` `#elif` `#else` `#endif` `#line` `#endregion` 可以以单行注释结束。 `/* */`在包含预处理指令的源行上不允许使用带分隔符的注释（注释的样式）。

预处理指令不是标记，不是 c # 语法语法的一部分。 但是，预处理指令可用于包含或排除标记序列，并以这种方式影响 c # 程序的含义。 例如，编译后，程序：
```csharp
#define A
#undef B

class C
{
#if A
    void F() {}
#else
    void G() {}
#endif

#if B
    void H() {}
#else
    void I() {}
#endif
}
```
生成与程序完全相同的标记序列：
```csharp
class C
{
    void F() {}
    void I() {}
}
```

因此，在语义上，这两个程序在语法上非常不同，它们是相同的。

### <a name="conditional-compilation-symbols"></a>“条件编译符”号

、、和指令提供的条件编译功能 `#if` `#elif` `#else` `#endif` 通过预处理表达式（[预处理表达式](lexical-structure.md#pre-processing-expressions)）和条件编译符号进行控制。

```antlr
conditional_symbol
    : '<Any identifier_or_keyword except true or false>'
    ;
```

条件编译符号有两种可能的状态：***已定义***或***未定义***。 在源文件的词法处理开始时，不定义条件编译符号，除非它已由外部机制（例如命令行编译器选项）显式定义。 `#define`处理指令时，该指令中名为的条件编译符号将在该源文件中进行定义。 在 `#undef` 处理同一符号的指令之前，或在到达源文件末尾之前，该符号保持为已定义。 这意味着， `#define` `#undef` 一个源文件中的和指令对同一程序中的其他源文件不起作用。

在预处理表达式中引用时，定义的条件编译符号具有布尔值 `true` ，未定义的条件编译符号具有布尔值 `false` 。 在预处理表达式中引用条件编译符号之前，不需要显式声明它们。 相反，未声明的符号只是未定义的，因此具有值 `false` 。

条件编译符号的命名空间是不同的，并且独立于 c # 程序中的所有其他命名实体。 条件编译符号只能在 `#define` 和 `#undef` 指令以及预处理表达式中引用。

### <a name="pre-processing-expressions"></a>预处理表达式

预处理表达式可以出现在 `#if` 和指令中 `#elif` 。 `!` `==` `!=` `&&` `||` 预处理表达式中允许使用运算符、、和，括号可用于分组。

```antlr
pp_expression
    : whitespace? pp_or_expression whitespace?
    ;

pp_or_expression
    : pp_and_expression
    | pp_or_expression whitespace? '||' whitespace? pp_and_expression
    ;

pp_and_expression
    : pp_equality_expression
    | pp_and_expression whitespace? '&&' whitespace? pp_equality_expression
    ;

pp_equality_expression
    : pp_unary_expression
    | pp_equality_expression whitespace? '==' whitespace? pp_unary_expression
    | pp_equality_expression whitespace? '!=' whitespace? pp_unary_expression
    ;

pp_unary_expression
    : pp_primary_expression
    | '!' whitespace? pp_unary_expression
    ;

pp_primary_expression
    : 'true'
    | 'false'
    | conditional_symbol
    | '(' whitespace? pp_expression whitespace? ')'
    ;
```

在预处理表达式中引用时，定义的条件编译符号具有布尔值 `true` ，未定义的条件编译符号具有布尔值 `false` 。

预处理表达式的计算始终产生布尔值。 预处理表达式的计算规则与常量表达式的计算规则相同（[常数](expressions.md#constant-expressions)表达式），只不过只能引用的用户定义实体是条件编译符号。

### <a name="declaration-directives"></a>声明指令

声明指令用于定义或取消定义条件编译符号。

```antlr
pp_declaration
    : whitespace? '#' whitespace? 'define' whitespace conditional_symbol pp_new_line
    | whitespace? '#' whitespace? 'undef' whitespace conditional_symbol pp_new_line
    ;

pp_new_line
    : whitespace? single_line_comment? new_line
    ;
```

指令的处理 `#define` 会使给定的条件编译符号成为定义，并从跟在指令后面的源行开始。 同样，处理 `#undef` 指令会使给定的条件编译符号变成未定义的，从该指令后面的源行开始。

`#define` `#undef` 源文件中的任何和指令必须出现在源文件中的第一个*标记*（[标记](lexical-structure.md#tokens)）之前; 否则，将发生编译时错误。 在直观的术语中， `#define` 和 `#undef` 指令必须位于源文件中的任何 "真实代码" 之前。

示例：
```csharp
#define Enterprise

#if Professional || Enterprise
    #define Advanced
#endif

namespace Megacorp.Data
{
    #if Advanced
    class PivotTable {...}
    #endif
}
```
有效，因为 `#define` 指令位于源文件中的第一个标记（ `namespace` 关键字）之前。

下面的示例会导致编译时错误，因为它会 `#define` 跟随真实代码：
```csharp
#define A
namespace N
{
    #define B
    #if B
    class Class1 {}
    #endif
}
```

`#define`可以定义已定义的条件编译符号，而不会 `#undef` 对该符号进行任何干预。 下面的示例定义条件编译符号 `A` ，然后再次定义它。
```csharp
#define A
#define A
```

`#undef`可能 "取消定义" 未定义的条件编译符号。 下面的示例定义条件编译符号 `A` ，然后将其取消定义两次; 虽然第二个 `#undef` 不起作用，但仍有效。
```csharp
#define A
#undef A
#undef A
```

### <a name="conditional-compilation-directives"></a>条件编译指令

条件编译指令用于有条件地包含或排除源文件的某些部分。

```antlr
pp_conditional
    : pp_if_section pp_elif_section* pp_else_section? pp_endif
    ;

pp_if_section
    : whitespace? '#' whitespace? 'if' whitespace pp_expression pp_new_line conditional_section?
    ;

pp_elif_section
    : whitespace? '#' whitespace? 'elif' whitespace pp_expression pp_new_line conditional_section?
    ;

pp_else_section:
    | whitespace? '#' whitespace? 'else' pp_new_line conditional_section?
    ;

pp_endif
    : whitespace? '#' whitespace? 'endif' pp_new_line
    ;

conditional_section
    : input_section
    | skipped_section
    ;

skipped_section
    : skipped_section_part+
    ;

skipped_section_part
    : skipped_characters? new_line
    | pp_directive
    ;

skipped_characters
    : whitespace? not_number_sign input_character*
    ;

not_number_sign
    : '<Any input_character except #>'
    ;
```

如语法所示，必须按顺序（按顺序）、 `#if` 指令、零个或多个 `#elif` 指令、零个或一个 `#else` 指令以及指令来写入条件编译指令 `#endif` 。 在指令与源代码的条件部分之间。 每个部分都由前面的指令控制。 条件部分本身可能包含嵌套的条件编译指令，前提是这些指令构成了完整的集。

*Pp_conditional*最多为常规词法处理选择一个包含的*conditional_section*：

*  和指令的*pp_expression*按 `#if` `#elif` 顺序进行计算，直到有一个结果 `true` 。 如果表达式产生了 `true` ，则选择相应指令的*conditional_section* 。
*  如果所有*pp_expression*均 `false` 为 yield，并且 `#else` 存在指令，则选择指令的*conditional_section* `#else` 。
*  否则，不会选择任何*conditional_section* 。

选定的*conditional_section*（如果有）将作为正常*input_section*进行处理：节中包含的源代码必须符合词法语法;标记是从节中的源代码生成的;部分中的和预处理指令具有指定的效果。

剩余的*conditional_section*（如果有）将作为*skipped_section*s 进行处理：除预处理指令以外，部分中的源代码无需遵守词法语法;不会从该部分中的源代码生成任何标记;部分中的和预处理指令必须在词法上是正确的，但不会进行处理。 在作为*skipped_section*进行处理的*conditional_section*中，任何嵌套的*conditional_section*（包含在嵌套 `#if` ... `#endif` 和 `#region` ... 构造中 `#endregion` ）也作为*skipped_section*处理。

下面的示例说明了条件编译指令如何嵌套：
```csharp
#define Debug       // Debugging on
#undef Trace        // Tracing off

class PurchaseTransaction
{
    void Commit() {
        #if Debug
            CheckConsistency();
            #if Trace
                WriteToLog(this.ToString());
            #endif
        #endif
        CommitHelper();
    }
}
```

除预处理指令外，跳过的源代码不受词法分析的限制。 例如，尽管部分中出现未终止的注释，以下内容仍有效 `#else` ：
```csharp
#define Debug        // Debugging on

class PurchaseTransaction
{
    void Commit() {
        #if Debug
            CheckConsistency();
        #else
            /* Do something else
        #endif
    }
}
```

但请注意，即使在源代码中跳过的部分，预处理指令也需要在词法上正确。

当预处理指令出现在多行输入元素中时，不会对其进行处理。 例如，程序：
```csharp
class Hello
{
    static void Main() {
        System.Console.WriteLine(@"hello, 
#if Debug
        world
#else
        Nebraska
#endif
        ");
    }
}
```
输出结果为：
```console
hello,
#if Debug
        world
#else
        Nebraska
#endif
```

在特殊情况下，处理的预处理指令集可能取决于*pp_expression*的计算。 示例：
```csharp
#if X
    /*
#else
    /* */ class Q { }
#endif
```
`class` `Q` `{` `}` 不管是否 `X` 定义了，始终会生成相同的令牌流（）。 如果 `X` 定义了，则仅处理的指令为 `#if` 和 `#endif` ，因为有多行注释。 如果 `X` 未定义，则三个指令（ `#if` 、 `#else` 、 `#endif` ）是指令集的一部分。

### <a name="diagnostic-directives"></a>诊断指令

诊断指令用于显式生成错误和警告消息，其报告方式与其他编译时错误和警告的方式相同。

```antlr
pp_diagnostic
    : whitespace? '#' whitespace? 'error' pp_message
    | whitespace? '#' whitespace? 'warning' pp_message
    ;

pp_message
    : new_line
    | whitespace input_character* new_line
    ;
```

示例：
```csharp
#warning Code review needed before check-in

#if Debug && Retail
    #error A build can't be both debug and retail
#endif

class Test {...}
```
始终产生警告（"签入前需要代码评审"），并且如果同时定义了条件符号和，则生成编译时错误（"A build 不能同时为调试和零售"） `Debug` `Retail` 。 请注意， *pp_message*可以包含任意文本;具体而言，它不需要包含格式正确的标记，如单词中的单引号所示 `can't` 。

### <a name="region-directives"></a>区域指令

区域指令用于显式标记源代码区域。

```antlr
pp_region
    : pp_start_region conditional_section? pp_end_region
    ;

pp_start_region
    : whitespace? '#' whitespace? 'region' pp_message
    ;

pp_end_region
    : whitespace? '#' whitespace? 'endregion' pp_message
    ;
```

无语义含义附加到区域;区域旨在供程序员或自动工具用来标记源代码的一部分。 或指令中指定的 `#region` 消息 `#endregion` 同样没有语义含义; 它仅用于标识区域。 匹配 `#region` 的和 `#endregion` 指令可能具有不同的*pp_message*。

区域的词法处理：
```csharp
#region
...
#endregion
```
完全对应于格式为的条件编译指令的词法处理：
```csharp
#if true
...
#endif
```

### <a name="line-directives"></a>行指令

行指令可用于更改编译器在输出（如警告和错误）中报告的行号和源文件名，以及由调用方信息特性（[调用方信息特性](attributes.md#caller-info-attributes)）使用的行号。

行指令最常用于从其他某些文本输入生成 c # 源代码的元编程工具。

```antlr
pp_line
    : whitespace? '#' whitespace? 'line' whitespace line_indicator pp_new_line
    ;

line_indicator
    : decimal_digit+ whitespace file_name
    | decimal_digit+
    | 'default'
    | 'hidden'
    ;

file_name
    : '"' file_name_character+ '"'
    ;

file_name_character
    : '<Any input_character except ">'
    ;
```

当不 `#line` 存在任何指令时，编译器会在其输出中报告真实的行号和源文件名。 当处理 `#line` 包含不是的*line_indicator*的指令时，编译器会将 `default` 指令后的行视为具有给定的行号（和文件名，如果指定）。

`#line default`指令反转所有前面 #line 指令的作用。 编译器会报告后续行的真实行信息，就像未 `#line` 处理过指令一样。

`#line hidden`指令对错误消息中报告的文件和行号没有影响，但会影响源级别调试。 调试时， `#line hidden` 指令和后续指令之间的所有行 `#line` （即 not `#line hidden` ）都没有行号信息。 单步执行调试器中的代码时，将完全跳过这些行。

请注意，在不处理转义字符的情况下， *file_name*与正则字符串文字不同;" `\` " 字符只是在*file_name*中指定普通反斜杠字符。

### <a name="pragma-directives"></a>Pragma 指令

`#pragma`预处理指令用于指定编译器的可选上下文信息。 指令中提供的信息 `#pragma` 永远不会更改程序语义。

```antlr
pp_pragma
    : whitespace? '#' whitespace? 'pragma' whitespace pragma_body pp_new_line
    ;

pragma_body
    : pragma_warning_body
    ;
```

C # 提供了 `#pragma` 控制编译器警告的指令。 将来版本的语言可能包含其他 `#pragma` 指令。 为了确保与其他 c # 编译器的互操作性，Microsoft c # 编译器不会发出未知指令的编译错误 `#pragma` ; 因此，此类指令将生成警告。

#### <a name="pragma-warning"></a>Pragma warning

`#pragma warning`指令用于在编译后续程序文本期间禁用或还原所有或一组特定的警告消息。

```antlr
pragma_warning_body
    : 'warning' whitespace warning_action
    | 'warning' whitespace warning_action whitespace warning_list
    ;

warning_action
    : 'disable'
    | 'restore'
    ;

warning_list
    : decimal_digit+ (whitespace? ',' whitespace? decimal_digit+)*
    ;
```

`#pragma warning`省略警告列表的指令将影响所有警告。 `#pragma warning`包含警告列表的指令只影响列表中指定的那些警告。

`#pragma warning disable`指令禁用所有或给定的一组警告。

`#pragma warning restore`指令将所有或给定的警告集还原到编译单元开头处生效的状态。 请注意，如果在外部禁用了特定警告，则 `#pragma warning restore` 将不会重新启用该警告。

下面的示例演示如何使用 `#pragma warning` Microsoft c # 编译器中的警告编号来暂时禁用引用过时成员时所报告的警告。
```csharp
using System;

class Program
{
    [Obsolete]
    static void Foo() {}

    static void Main() {
#pragma warning disable 612
    Foo();
#pragma warning restore 612
    }
}
```

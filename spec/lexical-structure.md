---
ms.openlocfilehash: eff00c4571054dcd07b235fe6d505838ee1dec94
ms.sourcegitcommit: e149d1e9c0cf666ae4bea1c3ceeac6171ef285d4
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/15/2020
ms.locfileid: "86403086"
---
# <a name="lexical-structure"></a><span data-ttu-id="cf567-101">词法结构</span><span class="sxs-lookup"><span data-stu-id="cf567-101">Lexical structure</span></span>

## <a name="programs"></a><span data-ttu-id="cf567-102">节目</span><span class="sxs-lookup"><span data-stu-id="cf567-102">Programs</span></span>

<span data-ttu-id="cf567-103">C #***程序***由一个或多个***源文件***组成，该文件正式称为***编译单元***（[编译单元](namespaces.md#compilation-units)）。</span><span class="sxs-lookup"><span data-stu-id="cf567-103">A C# ***program*** consists of one or more ***source files***, known formally as ***compilation units*** ([Compilation units](namespaces.md#compilation-units)).</span></span> <span data-ttu-id="cf567-104">源文件是 Unicode 字符的有序序列。</span><span class="sxs-lookup"><span data-stu-id="cf567-104">A source file is an ordered sequence of Unicode characters.</span></span> <span data-ttu-id="cf567-105">源文件与文件系统中的文件通常具有一对一的对应关系，但不需要此函件。</span><span class="sxs-lookup"><span data-stu-id="cf567-105">Source files typically have a one-to-one correspondence with files in a file system, but this correspondence is not required.</span></span> <span data-ttu-id="cf567-106">为获得最大的可移植性，建议使用 UTF-8 编码对文件系统中的文件进行编码。</span><span class="sxs-lookup"><span data-stu-id="cf567-106">For maximal portability, it is recommended that files in a file system be encoded with the UTF-8 encoding.</span></span>

<span data-ttu-id="cf567-107">从概念上讲，程序是使用三个步骤编译的：</span><span class="sxs-lookup"><span data-stu-id="cf567-107">Conceptually speaking, a program is compiled using three steps:</span></span>

1. <span data-ttu-id="cf567-108">转换，将特定字符已知和编码方案中的文件转换为 Unicode 字符序列。</span><span class="sxs-lookup"><span data-stu-id="cf567-108">Transformation, which converts a file from a particular character repertoire and encoding scheme into a sequence of Unicode characters.</span></span>
2. <span data-ttu-id="cf567-109">词法分析，将 Unicode 输入字符流转换为标记流。</span><span class="sxs-lookup"><span data-stu-id="cf567-109">Lexical analysis, which translates a stream of Unicode input characters into a stream of tokens.</span></span>
3. <span data-ttu-id="cf567-110">语法分析，将令牌流转换为可执行代码。</span><span class="sxs-lookup"><span data-stu-id="cf567-110">Syntactic analysis, which translates the stream of tokens into executable code.</span></span>

## <a name="grammars"></a><span data-ttu-id="cf567-111">语法</span><span class="sxs-lookup"><span data-stu-id="cf567-111">Grammars</span></span>

<span data-ttu-id="cf567-112">此规范提供使用两个语法的 c # 编程语言的语法。</span><span class="sxs-lookup"><span data-stu-id="cf567-112">This specification presents the syntax of the C# programming language using two grammars.</span></span> <span data-ttu-id="cf567-113">***词法语法***（[词法语法](lexical-structure.md#lexical-grammar)）定义 Unicode 字符如何合并为行终止符、空格、注释、标记和预处理指令。</span><span class="sxs-lookup"><span data-stu-id="cf567-113">The ***lexical grammar*** ([Lexical grammar](lexical-structure.md#lexical-grammar)) defines how Unicode characters are combined to form line terminators, white space, comments, tokens, and pre-processing directives.</span></span> <span data-ttu-id="cf567-114">***句法文法***（[句法文法](lexical-structure.md#syntactic-grammar)）定义如何组合词汇语法生成的标记以形成 c # 程序。</span><span class="sxs-lookup"><span data-stu-id="cf567-114">The ***syntactic grammar*** ([Syntactic grammar](lexical-structure.md#syntactic-grammar)) defines how the tokens resulting from the lexical grammar are combined to form C# programs.</span></span>

### <a name="grammar-notation"></a><span data-ttu-id="cf567-115">语法表示法</span><span class="sxs-lookup"><span data-stu-id="cf567-115">Grammar notation</span></span>

<span data-ttu-id="cf567-116">词法语法和句法语法以巴科斯-诺尔范式形式显示，使用 ANTLR 语法工具的表示法。</span><span class="sxs-lookup"><span data-stu-id="cf567-116">The lexical and syntactic grammars are presented in Backus-Naur form using the notation of the ANTLR grammar tool.</span></span>

### <a name="lexical-grammar"></a><span data-ttu-id="cf567-117">词法语法</span><span class="sxs-lookup"><span data-stu-id="cf567-117">Lexical grammar</span></span>

<span data-ttu-id="cf567-118">C # 的词法语法显示在[词法分析](lexical-structure.md#lexical-analysis)、[标记](lexical-structure.md#tokens)和[预处理指令](lexical-structure.md#pre-processing-directives)中。</span><span class="sxs-lookup"><span data-stu-id="cf567-118">The lexical grammar of C# is presented in [Lexical analysis](lexical-structure.md#lexical-analysis), [Tokens](lexical-structure.md#tokens), and [Pre-processing directives](lexical-structure.md#pre-processing-directives).</span></span> <span data-ttu-id="cf567-119">词法语法的终端符号是 Unicode 字符集的字符，并且词法语法指定如何将字符组合到一起形成标记（[标记](lexical-structure.md#tokens)）[、空白（空格）、](lexical-structure.md#white-space)注释（[注释](lexical-structure.md#comments)）和预处理指令（[预处理指令](lexical-structure.md#pre-processing-directives)）。</span><span class="sxs-lookup"><span data-stu-id="cf567-119">The terminal symbols of the lexical grammar are the characters of the Unicode character set, and the lexical grammar specifies how characters are combined to form tokens ([Tokens](lexical-structure.md#tokens)), white space ([White space](lexical-structure.md#white-space)), comments ([Comments](lexical-structure.md#comments)), and pre-processing directives ([Pre-processing directives](lexical-structure.md#pre-processing-directives)).</span></span>

<span data-ttu-id="cf567-120">C # 程序中的每个源文件都必须符合词法文法的*输入*生产（[词法分析](lexical-structure.md#lexical-analysis)）。</span><span class="sxs-lookup"><span data-stu-id="cf567-120">Every source file in a C# program must conform to the *input* production of the lexical grammar ([Lexical analysis](lexical-structure.md#lexical-analysis)).</span></span>

### <a name="syntactic-grammar"></a><span data-ttu-id="cf567-121">语法语法</span><span class="sxs-lookup"><span data-stu-id="cf567-121">Syntactic grammar</span></span>

<span data-ttu-id="cf567-122">C # 的句法语法在本章后面的章节和附录中提供。</span><span class="sxs-lookup"><span data-stu-id="cf567-122">The syntactic grammar of C# is presented in the chapters and appendices that follow this chapter.</span></span> <span data-ttu-id="cf567-123">语法语法的终端符号是由词法语法定义的标记，句法语法指定如何组合标记以形成 c # 程序。</span><span class="sxs-lookup"><span data-stu-id="cf567-123">The terminal symbols of the syntactic grammar are the tokens defined by the lexical grammar, and the syntactic grammar specifies how tokens are combined to form C# programs.</span></span>

<span data-ttu-id="cf567-124">C # 程序中的每个源文件都必须符合句法文法*compilation_unit*生产（[编译单元](namespaces.md#compilation-units)）。</span><span class="sxs-lookup"><span data-stu-id="cf567-124">Every source file in a C# program must conform to the *compilation_unit* production of the syntactic grammar ([Compilation units](namespaces.md#compilation-units)).</span></span>

## <a name="lexical-analysis"></a><span data-ttu-id="cf567-125">词法分析</span><span class="sxs-lookup"><span data-stu-id="cf567-125">Lexical analysis</span></span>

<span data-ttu-id="cf567-126">*输入*生产定义 c # 源文件的词法结构。</span><span class="sxs-lookup"><span data-stu-id="cf567-126">The *input* production defines the lexical structure of a C# source file.</span></span> <span data-ttu-id="cf567-127">C # 程序中的每个源文件都必须符合此词法文法生产。</span><span class="sxs-lookup"><span data-stu-id="cf567-127">Each source file in a C# program must conform to this lexical grammar production.</span></span>

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

<span data-ttu-id="cf567-128">五个基本元素构成 c # 源文件的词法结构：行终止符（[行终止符](lexical-structure.md#line-terminators)）、空白（[空格）、](lexical-structure.md#white-space)注释（[注释](lexical-structure.md#comments)）、标记（[标记](lexical-structure.md#tokens)）和预处理指令（[预处理指令](lexical-structure.md#pre-processing-directives)）。</span><span class="sxs-lookup"><span data-stu-id="cf567-128">Five basic elements make up the lexical structure of a C# source file: Line terminators ([Line terminators](lexical-structure.md#line-terminators)), white space ([White space](lexical-structure.md#white-space)), comments ([Comments](lexical-structure.md#comments)), tokens ([Tokens](lexical-structure.md#tokens)), and pre-processing directives ([Pre-processing directives](lexical-structure.md#pre-processing-directives)).</span></span> <span data-ttu-id="cf567-129">在这些基本元素中，只有标记在 c # 程序的句法语法中非常重要（[句法语法](lexical-structure.md#syntactic-grammar)）。</span><span class="sxs-lookup"><span data-stu-id="cf567-129">Of these basic elements, only tokens are significant in the syntactic grammar of a C# program ([Syntactic grammar](lexical-structure.md#syntactic-grammar)).</span></span>

<span data-ttu-id="cf567-130">C # 源文件的词法处理包括将文件缩减为一系列标记，后者成为句法分析的输入。</span><span class="sxs-lookup"><span data-stu-id="cf567-130">The lexical processing of a C# source file consists of reducing the file into a sequence of tokens which becomes the input to the syntactic analysis.</span></span> <span data-ttu-id="cf567-131">行终止符、空白和注释可用于分隔标记，预处理指令可能会导致跳过源文件的各个部分，否则，这些词法元素不会影响 c # 程序的语法结构。</span><span class="sxs-lookup"><span data-stu-id="cf567-131">Line terminators, white space, and comments can serve to separate tokens, and pre-processing directives can cause sections of the source file to be skipped, but otherwise these lexical elements have no impact on the syntactic structure of a C# program.</span></span>

<span data-ttu-id="cf567-132">在内插字符串文本（内[插字符串文本](lexical-structure.md#interpolated-string-literals)）的情况下，单个标记最初由词法分析生成，但被分解为多个输入元素，这些输入元素会反复进入词法分析状态，直到所有内插字符串文本均已解决。</span><span class="sxs-lookup"><span data-stu-id="cf567-132">In the case of interpolated string literals ([Interpolated string literals](lexical-structure.md#interpolated-string-literals)) a single token is initially produced by lexical analysis, but is broken up into several input elements which are repeatedly subjected to lexical analysis until all interpolated string literals have been resolved.</span></span> <span data-ttu-id="cf567-133">然后，生成的令牌作为句法分析的输入。</span><span class="sxs-lookup"><span data-stu-id="cf567-133">The resulting tokens then serve as input to the syntactic analysis.</span></span>

<span data-ttu-id="cf567-134">当多个词法语法生产与源文件中的一系列字符匹配时，词法处理始终形成可能的最长词汇元素。</span><span class="sxs-lookup"><span data-stu-id="cf567-134">When several lexical grammar productions match a sequence of characters in a source file, the lexical processing always forms the longest possible lexical element.</span></span> <span data-ttu-id="cf567-135">例如，字符序列 `//` 作为单行注释的开头处理，因为该词法元素比单个 `/` 标记长。</span><span class="sxs-lookup"><span data-stu-id="cf567-135">For example, the character sequence `//` is processed as the beginning of a single-line comment because that lexical element is longer than a single `/` token.</span></span>

### <a name="line-terminators"></a><span data-ttu-id="cf567-136">行终止符</span><span class="sxs-lookup"><span data-stu-id="cf567-136">Line terminators</span></span>

<span data-ttu-id="cf567-137">行结束符将 c # 源文件中的字符分为多行。</span><span class="sxs-lookup"><span data-stu-id="cf567-137">Line terminators divide the characters of a C# source file into lines.</span></span>

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

<span data-ttu-id="cf567-138">为了与添加文件结尾标记的源代码编辑工具兼容，若要将源文件视为一系列正确终止的行，请按顺序将以下转换应用于 c # 程序中的每个源文件：</span><span class="sxs-lookup"><span data-stu-id="cf567-138">For compatibility with source code editing tools that add end-of-file markers, and to enable a source file to be viewed as a sequence of properly terminated lines, the following transformations are applied, in order, to every source file in a C# program:</span></span>

*  <span data-ttu-id="cf567-139">如果源文件的最后一个字符是 Control Z 字符（ `U+001A` ），则删除该字符。</span><span class="sxs-lookup"><span data-stu-id="cf567-139">If the last character of the source file is a Control-Z character (`U+001A`), this character is deleted.</span></span>
*  <span data-ttu-id="cf567-140">如果源文件不 `U+000D` 是空的，并且源文件的最后一个字符不是回车符（ `U+000D` ）、换行符（ `U+000A` ）、行分隔符（ `U+2028` ）或段落分隔符（），则将回车符（）添加到源文件的末尾 `U+2029` 。</span><span class="sxs-lookup"><span data-stu-id="cf567-140">A carriage-return character (`U+000D`) is added to the end of the source file if that source file is non-empty and if the last character of the source file is not a carriage return (`U+000D`), a line feed (`U+000A`), a line separator (`U+2028`), or a paragraph separator (`U+2029`).</span></span>

### <a name="comments"></a><span data-ttu-id="cf567-141">注释</span><span class="sxs-lookup"><span data-stu-id="cf567-141">Comments</span></span>

<span data-ttu-id="cf567-142">支持两种形式的注释：单行注释和分隔注释。</span><span class="sxs-lookup"><span data-stu-id="cf567-142">Two forms of comments are supported: single-line comments and delimited comments.</span></span> <span data-ttu-id="cf567-143">S 注释以字符 `//` 开头，并扩展到源行的末尾。</span><span class="sxs-lookup"><span data-stu-id="cf567-143">***Single-line comments*** start with the characters `//` and extend to the end of the source line.</span></span> <span data-ttu-id="cf567-144">分隔注释以字符 `/*` 开头，以字符 `*/` 结尾。</span><span class="sxs-lookup"><span data-stu-id="cf567-144">***Delimited comments*** start with the characters `/*` and end with the characters `*/`.</span></span> <span data-ttu-id="cf567-145">分隔注释可能跨多行。</span><span class="sxs-lookup"><span data-stu-id="cf567-145">Delimited comments may span multiple lines.</span></span>

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

<span data-ttu-id="cf567-146">注释不嵌套。</span><span class="sxs-lookup"><span data-stu-id="cf567-146">Comments do not nest.</span></span> <span data-ttu-id="cf567-147">字符序列 `/*` `*/` 在注释中没有特殊含义，并且 `//` 字符序列在 `//` `/*` 分隔注释中没有特殊含义。</span><span class="sxs-lookup"><span data-stu-id="cf567-147">The character sequences `/*` and `*/` have no special meaning within a `//` comment, and the character sequences `//` and `/*` have no special meaning within a delimited comment.</span></span>

<span data-ttu-id="cf567-148">在字符和字符串文本中不处理注释。</span><span class="sxs-lookup"><span data-stu-id="cf567-148">Comments are not processed within character and string literals.</span></span>

<span data-ttu-id="cf567-149">示例</span><span class="sxs-lookup"><span data-stu-id="cf567-149">The example</span></span>
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
<span data-ttu-id="cf567-150">包含分隔注释。</span><span class="sxs-lookup"><span data-stu-id="cf567-150">includes a delimited comment.</span></span>

<span data-ttu-id="cf567-151">示例</span><span class="sxs-lookup"><span data-stu-id="cf567-151">The example</span></span>
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
<span data-ttu-id="cf567-152">显示若干单行注释。</span><span class="sxs-lookup"><span data-stu-id="cf567-152">shows several single-line comments.</span></span>

### <a name="white-space"></a><span data-ttu-id="cf567-153">空格</span><span class="sxs-lookup"><span data-stu-id="cf567-153">White space</span></span>

<span data-ttu-id="cf567-154">空格定义为带有 Unicode 类 Zs 的任何字符（包括空格字符）以及水平制表符、垂直制表符、换行符和换页符。</span><span class="sxs-lookup"><span data-stu-id="cf567-154">White space is defined as any character with Unicode class Zs (which includes the space character) as well as the horizontal tab character, the vertical tab character, and the form feed character.</span></span>

```antlr
whitespace
    : '<Any character with Unicode class Zs>'
    | '<Horizontal tab character (U+0009)>'
    | '<Vertical tab character (U+000B)>'
    | '<Form feed character (U+000C)>'
    ;
```

## <a name="tokens"></a><span data-ttu-id="cf567-155">令牌</span><span class="sxs-lookup"><span data-stu-id="cf567-155">Tokens</span></span>

<span data-ttu-id="cf567-156">有多种类型的令牌：标识符、关键字、文本、运算符和标点符号。</span><span class="sxs-lookup"><span data-stu-id="cf567-156">There are several kinds of tokens: identifiers, keywords, literals, operators, and punctuators.</span></span> <span data-ttu-id="cf567-157">空白和注释不是标记，不过它们充当标记的分隔符。</span><span class="sxs-lookup"><span data-stu-id="cf567-157">White space and comments are not tokens, though they act as separators for tokens.</span></span>

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

### <a name="unicode-character-escape-sequences"></a><span data-ttu-id="cf567-158">Unicode 字符转义序列</span><span class="sxs-lookup"><span data-stu-id="cf567-158">Unicode character escape sequences</span></span>

<span data-ttu-id="cf567-159">Unicode 字符转义序列表示一个 Unicode 字符。</span><span class="sxs-lookup"><span data-stu-id="cf567-159">A Unicode character escape sequence represents a Unicode character.</span></span> <span data-ttu-id="cf567-160">Unicode 字符转义序列在标识符（[标识符](lexical-structure.md#identifiers)）、字符文本（[字符文本](lexical-structure.md#character-literals)）和常规字符串文本（[字符串文字](lexical-structure.md#string-literals)）中进行处理。</span><span class="sxs-lookup"><span data-stu-id="cf567-160">Unicode character escape sequences are processed in identifiers ([Identifiers](lexical-structure.md#identifiers)), character literals ([Character literals](lexical-structure.md#character-literals)), and regular string literals ([String literals](lexical-structure.md#string-literals)).</span></span> <span data-ttu-id="cf567-161">不会在任何其他位置（例如，使用 operator、标点符号或关键字）处理 Unicode 字符转义。</span><span class="sxs-lookup"><span data-stu-id="cf567-161">A Unicode character escape is not processed in any other location (for example, to form an operator, punctuator, or keyword).</span></span>

```antlr
unicode_escape_sequence
    : '\\u' hex_digit hex_digit hex_digit hex_digit
    | '\\U' hex_digit hex_digit hex_digit hex_digit hex_digit hex_digit hex_digit hex_digit
    ;
```

<span data-ttu-id="cf567-162">Unicode 转义序列表示由 " `\u` " 或 "" 字符后面的十六进制数构成的单个 unicode 字符 `\U` 。</span><span class="sxs-lookup"><span data-stu-id="cf567-162">A Unicode escape sequence represents the single Unicode character formed by the hexadecimal number following the "`\u`" or "`\U`" characters.</span></span> <span data-ttu-id="cf567-163">由于 c # 使用字符和字符串值中的 Unicode 码位的16位编码，字符文本中不允许使用 U + 10000 到 U + 10FFFF 范围内的 Unicode 字符，而是使用字符串文本中的 Unicode 代理项对来表示。</span><span class="sxs-lookup"><span data-stu-id="cf567-163">Since C# uses a 16-bit encoding of Unicode code points in characters and string values, a Unicode character in the range U+10000 to U+10FFFF is not permitted in a character literal and is represented using a Unicode surrogate pair in a string literal.</span></span> <span data-ttu-id="cf567-164">不支持0x10FFFF 以上的码位的 Unicode 字符。</span><span class="sxs-lookup"><span data-stu-id="cf567-164">Unicode characters with code points above 0x10FFFF are not supported.</span></span>

<span data-ttu-id="cf567-165">不会执行多个转换。</span><span class="sxs-lookup"><span data-stu-id="cf567-165">Multiple translations are not performed.</span></span> <span data-ttu-id="cf567-166">例如，字符串文本 " `\u005Cu005C` " 等效于 " `\u005C` "，而不是 " `\` "。</span><span class="sxs-lookup"><span data-stu-id="cf567-166">For instance, the string literal "`\u005Cu005C`" is equivalent to "`\u005C`" rather than "`\`".</span></span> <span data-ttu-id="cf567-167">Unicode 值 `\u005C` 为字符 " `\` "。</span><span class="sxs-lookup"><span data-stu-id="cf567-167">The Unicode value `\u005C` is the character "`\`".</span></span>

<span data-ttu-id="cf567-168">示例</span><span class="sxs-lookup"><span data-stu-id="cf567-168">The example</span></span>
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
<span data-ttu-id="cf567-169">显示了的几个用法 `\u0066` ，它是字母 "" 的转义序列 `f` 。</span><span class="sxs-lookup"><span data-stu-id="cf567-169">shows several uses of `\u0066`, which is the escape sequence for the letter "`f`".</span></span> <span data-ttu-id="cf567-170">该程序等效于</span><span class="sxs-lookup"><span data-stu-id="cf567-170">The program is equivalent to</span></span>
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

### <a name="identifiers"></a><span data-ttu-id="cf567-171">标识符</span><span class="sxs-lookup"><span data-stu-id="cf567-171">Identifiers</span></span>

<span data-ttu-id="cf567-172">本节中给出的标识符规则完全与 Unicode 标准附录31推荐的规则相对应，只不过允许将下划线作为初始字符（如 C 编程语言中的传统）、标识符中允许使用 Unicode 转义序列，并允许将 " `@` " 字符作为前缀，以使关键字可用作标识符。</span><span class="sxs-lookup"><span data-stu-id="cf567-172">The rules for identifiers given in this section correspond exactly to those recommended by the Unicode Standard Annex 31, except that underscore is allowed as an initial character (as is traditional in the C programming language), Unicode escape sequences are permitted in identifiers, and the "`@`" character is allowed as a prefix to enable keywords to be used as identifiers.</span></span>

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

<span data-ttu-id="cf567-173">有关上面提到的 Unicode 字符类的信息，请参阅 Unicode 标准版本3.0，第4.5 节。</span><span class="sxs-lookup"><span data-stu-id="cf567-173">For information on the Unicode character classes mentioned above, see The Unicode Standard, Version 3.0, section 4.5.</span></span>

<span data-ttu-id="cf567-174">有效标识符的示例包括 " `identifier1` "、" `_identifier2` " 和 " `@if` "。</span><span class="sxs-lookup"><span data-stu-id="cf567-174">Examples of valid identifiers include "`identifier1`", "`_identifier2`", and "`@if`".</span></span>

<span data-ttu-id="cf567-175">符合标准的程序中的标识符必须是 Unicode 范式 C 定义的规范格式，如 Unicode 标准附录15所定义。</span><span class="sxs-lookup"><span data-stu-id="cf567-175">An identifier in a conforming program must be in the canonical format defined by Unicode Normalization Form C, as defined by Unicode Standard Annex 15.</span></span> <span data-ttu-id="cf567-176">如果遇到非范式规范的标识符，则该行为是实现定义的;但是，不需要诊断。</span><span class="sxs-lookup"><span data-stu-id="cf567-176">The behavior when encountering an identifier not in Normalization Form C is implementation-defined; however, a diagnostic is not required.</span></span>

<span data-ttu-id="cf567-177">前缀 " `@` " 允许将关键字用作标识符，这在与其他编程语言交互时非常有用。</span><span class="sxs-lookup"><span data-stu-id="cf567-177">The prefix "`@`" enables the use of keywords as identifiers, which is useful when interfacing with other programming languages.</span></span> <span data-ttu-id="cf567-178">该字符 `@` 实际上不是标识符的一部分，因此标识符可能以其他语言显示为普通标识符，不含前缀。</span><span class="sxs-lookup"><span data-stu-id="cf567-178">The character `@` is not actually part of the identifier, so the identifier might be seen in other languages as a normal identifier, without the prefix.</span></span> <span data-ttu-id="cf567-179">带有前缀的标识符 `@` 称为***逐字标识符***。</span><span class="sxs-lookup"><span data-stu-id="cf567-179">An identifier with an `@` prefix is called a ***verbatim identifier***.</span></span> <span data-ttu-id="cf567-180">`@`允许对不是关键字的标识符使用前缀，但强烈建议不要使用它作为样式。</span><span class="sxs-lookup"><span data-stu-id="cf567-180">Use of the `@` prefix for identifiers that are not keywords is permitted, but strongly discouraged as a matter of style.</span></span>

<span data-ttu-id="cf567-181">示例：</span><span class="sxs-lookup"><span data-stu-id="cf567-181">The example:</span></span>
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
<span data-ttu-id="cf567-182">定义名为 "" 的类，该类具有名为 "" `class` 的静态方法 `static` ，该方法采用名为 "" 的参数 `bool` 。</span><span class="sxs-lookup"><span data-stu-id="cf567-182">defines a class named "`class`" with a static method named "`static`" that takes a parameter named "`bool`".</span></span> <span data-ttu-id="cf567-183">请注意，由于关键字中不允许使用 Unicode 转义，因此标记 " `cl\u0061ss` " 是标识符，与 "" 具有相同的标识符 `@class` 。</span><span class="sxs-lookup"><span data-stu-id="cf567-183">Note that since Unicode escapes are not permitted in keywords, the token "`cl\u0061ss`" is an identifier, and is the same identifier as "`@class`".</span></span>

<span data-ttu-id="cf567-184">如果两个标识符在应用以下转换后相同，则将其视为相同：</span><span class="sxs-lookup"><span data-stu-id="cf567-184">Two identifiers are considered the same if they are identical after the following transformations are applied, in order:</span></span>

*  <span data-ttu-id="cf567-185">删除前缀 " `@` " （如果使用）。</span><span class="sxs-lookup"><span data-stu-id="cf567-185">The prefix "`@`", if used, is removed.</span></span>
*  <span data-ttu-id="cf567-186">每个*unicode_escape_sequence*都转换为其对应的 unicode 字符。</span><span class="sxs-lookup"><span data-stu-id="cf567-186">Each *unicode_escape_sequence* is transformed into its corresponding Unicode character.</span></span>
*  <span data-ttu-id="cf567-187">删除任何*formatting_character*。</span><span class="sxs-lookup"><span data-stu-id="cf567-187">Any *formatting_character*s are removed.</span></span>

<span data-ttu-id="cf567-188">包含两个连续下划线字符（ `U+005F` ）的标识符保留给实现使用。</span><span class="sxs-lookup"><span data-stu-id="cf567-188">Identifiers containing two consecutive underscore characters (`U+005F`) are reserved for use by the implementation.</span></span> <span data-ttu-id="cf567-189">例如，实现可能提供以两个下划线开头的扩展关键字。</span><span class="sxs-lookup"><span data-stu-id="cf567-189">For example, an implementation might provide extended keywords that begin with two underscores.</span></span>

### <a name="keywords"></a><span data-ttu-id="cf567-190">关键字</span><span class="sxs-lookup"><span data-stu-id="cf567-190">Keywords</span></span>

<span data-ttu-id="cf567-191">***关键字***是类似于标识符的字符序列（保留），不能用作标识符，除非以 `@` 字符开头。</span><span class="sxs-lookup"><span data-stu-id="cf567-191">A ***keyword*** is an identifier-like sequence of characters that is reserved, and cannot be used as an identifier except when prefaced by the `@` character.</span></span>

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

<span data-ttu-id="cf567-192">在语法中的某些位置，特定标识符具有特殊意义，但不是关键字。</span><span class="sxs-lookup"><span data-stu-id="cf567-192">In some places in the grammar, specific identifiers have special meaning, but are not keywords.</span></span> <span data-ttu-id="cf567-193">此类标识符有时称为 "上下文关键字"。</span><span class="sxs-lookup"><span data-stu-id="cf567-193">Such identifiers are sometimes referred to as "contextual keywords".</span></span> <span data-ttu-id="cf567-194">例如，在属性声明中，" `get` " 和 " `set` " 标识符具有特殊意义（[取值函数](classes.md#accessors)）。</span><span class="sxs-lookup"><span data-stu-id="cf567-194">For example, within a property declaration, the "`get`" and "`set`" identifiers have special meaning ([Accessors](classes.md#accessors)).</span></span> <span data-ttu-id="cf567-195">此位置不允许使用或以外的标识符 `get` `set` ，因此，此使用不会与使用这些字词作为标识符冲突。</span><span class="sxs-lookup"><span data-stu-id="cf567-195">An identifier other than `get` or `set` is never permitted in these locations, so this use does not conflict with a use of these words as identifiers.</span></span> <span data-ttu-id="cf567-196">在其他情况下，如 `var` 在隐式类型化局部变量声明（[局部变量声明](statements.md#local-variable-declarations)）中使用标识符 "" 时，上下文关键字可能与声明的名称冲突。</span><span class="sxs-lookup"><span data-stu-id="cf567-196">In other cases, such as with the identifier "`var`" in implicitly typed local variable declarations ([Local variable declarations](statements.md#local-variable-declarations)), a contextual keyword can conflict with declared names.</span></span> <span data-ttu-id="cf567-197">在这种情况下，声明的名称优先于将标识符用作上下文关键字。</span><span class="sxs-lookup"><span data-stu-id="cf567-197">In such cases, the declared name takes precedence over the use of the identifier as a contextual keyword.</span></span>

### <a name="literals"></a><span data-ttu-id="cf567-198">文字</span><span class="sxs-lookup"><span data-stu-id="cf567-198">Literals</span></span>

<span data-ttu-id="cf567-199">文本是值的源代码表示形式。</span><span class="sxs-lookup"><span data-stu-id="cf567-199">A ***literal*** is a source code representation of a value.</span></span>

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

#### <a name="boolean-literals"></a><span data-ttu-id="cf567-200">布尔值文字</span><span class="sxs-lookup"><span data-stu-id="cf567-200">Boolean literals</span></span>

<span data-ttu-id="cf567-201">有两个布尔文本值： `true` 和 `false` 。</span><span class="sxs-lookup"><span data-stu-id="cf567-201">There are two boolean literal values: `true` and `false`.</span></span>

```antlr
boolean_literal
    : 'true'
    | 'false'
    ;
```

<span data-ttu-id="cf567-202">*Boolean_literal*的类型为 `bool` 。</span><span class="sxs-lookup"><span data-stu-id="cf567-202">The type of a *boolean_literal* is `bool`.</span></span>

#### <a name="integer-literals"></a><span data-ttu-id="cf567-203">整数文本</span><span class="sxs-lookup"><span data-stu-id="cf567-203">Integer literals</span></span>

<span data-ttu-id="cf567-204">整数文本用于写入类型为、、和的值 `int` `uint` `long` `ulong` 。</span><span class="sxs-lookup"><span data-stu-id="cf567-204">Integer literals are used to write values of types `int`, `uint`, `long`, and `ulong`.</span></span> <span data-ttu-id="cf567-205">整数文本具有两种可能的形式： decimal 和十六进制。</span><span class="sxs-lookup"><span data-stu-id="cf567-205">Integer literals have two possible forms: decimal and hexadecimal.</span></span>

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

<span data-ttu-id="cf567-206">确定整数文本的类型，如下所示：</span><span class="sxs-lookup"><span data-stu-id="cf567-206">The type of an integer literal is determined as follows:</span></span>

*  <span data-ttu-id="cf567-207">如果文字没有后缀，则其值可以表示为以下类型的第一个类型： `int` 、 `uint` 、 `long` 和 `ulong` 。</span><span class="sxs-lookup"><span data-stu-id="cf567-207">If the literal has no suffix, it has the first of these types in which its value can be represented: `int`, `uint`, `long`, `ulong`.</span></span>
*  <span data-ttu-id="cf567-208">如果文本的后缀为 `U` 或 `u` ，则它具有以下类型中可以表示其值的第一个类型： `uint` 、 `ulong` 。</span><span class="sxs-lookup"><span data-stu-id="cf567-208">If the literal is suffixed by `U` or `u`, it has the first of these types in which its value can be represented: `uint`, `ulong`.</span></span>
*  <span data-ttu-id="cf567-209">如果文本的后缀为 `L` 或 `l` ，则它具有以下类型中可以表示其值的第一个类型： `long` 、 `ulong` 。</span><span class="sxs-lookup"><span data-stu-id="cf567-209">If the literal is suffixed by `L` or `l`, it has the first of these types in which its value can be represented: `long`, `ulong`.</span></span>
*  <span data-ttu-id="cf567-210">如果文本的后缀为 `UL` 、 `Ul` 、、、、、 `uL` `ul` `LU` `Lu` `lU` 或，则 `lu` 它属于类型 `ulong` 。</span><span class="sxs-lookup"><span data-stu-id="cf567-210">If the literal is suffixed by `UL`, `Ul`, `uL`, `ul`, `LU`, `Lu`, `lU`, or `lu`, it is of type `ulong`.</span></span>

<span data-ttu-id="cf567-211">如果整数文本所表示的值超出了该类型的范围 `ulong` ，则会发生编译时错误。</span><span class="sxs-lookup"><span data-stu-id="cf567-211">If the value represented by an integer literal is outside the range of the `ulong` type, a compile-time error occurs.</span></span>

<span data-ttu-id="cf567-212">作为样式，建议 `L` 在写入类型的文本时使用 "" 而不是 "" `l` `long` ，因为这样可以很容易地将字母 " `l` " 与数字 " `1` " 混淆。</span><span class="sxs-lookup"><span data-stu-id="cf567-212">As a matter of style, it is suggested that "`L`" be used instead of "`l`" when writing literals of type `long`, since it is easy to confuse the letter "`l`" with the digit "`1`".</span></span>

<span data-ttu-id="cf567-213">若要允许尽可能小的 `int` 和 `long` 值写入为十进制整数文本，请满足以下两个规则：</span><span class="sxs-lookup"><span data-stu-id="cf567-213">To permit the smallest possible `int` and `long` values to be written as decimal integer literals, the following two rules exist:</span></span>

* <span data-ttu-id="cf567-214">当值为2147483648（2 ^ 31）且没有*integer_type_suffix*的*decimal_integer_literal*显示为紧跟一元减号运算符（[一元减号运算符](expressions.md#unary-minus-operator)）的标记时，结果为类型 `int` 值为-2147483648 （-2 ^ 31）的常量。</span><span class="sxs-lookup"><span data-stu-id="cf567-214">When a *decimal_integer_literal* with the value 2147483648 (2^31) and no *integer_type_suffix* appears as the token immediately following a unary minus operator token ([Unary minus operator](expressions.md#unary-minus-operator)), the result is a constant of type `int` with the value -2147483648 (-2^31).</span></span> <span data-ttu-id="cf567-215">在所有其他情况下，这类*decimal_integer_literal*的类型为 `uint` 。</span><span class="sxs-lookup"><span data-stu-id="cf567-215">In all other situations, such a *decimal_integer_literal* is of type `uint`.</span></span>
* <span data-ttu-id="cf567-216">当*decimal_integer_literal*值为9223372036854775808（2 ^ 63）且没有*integer_type_suffix*或*integer_type_suffix*时 `L` ，或在 `l` 一元减号运算符（[一元减号运算符](expressions.md#unary-minus-operator)）之后立即显示标记时，结果为类型的常量，其 `long` 值为-9223372036854775808 （-2 ^ 63）。</span><span class="sxs-lookup"><span data-stu-id="cf567-216">When a *decimal_integer_literal* with the value 9223372036854775808 (2^63) and no *integer_type_suffix* or the *integer_type_suffix* `L` or `l` appears as the token immediately following a unary minus operator token ([Unary minus operator](expressions.md#unary-minus-operator)), the result is a constant of type `long` with the value -9223372036854775808 (-2^63).</span></span> <span data-ttu-id="cf567-217">在所有其他情况下，这类*decimal_integer_literal*的类型为 `ulong` 。</span><span class="sxs-lookup"><span data-stu-id="cf567-217">In all other situations, such a *decimal_integer_literal* is of type `ulong`.</span></span>

#### <a name="real-literals"></a><span data-ttu-id="cf567-218">真实文本</span><span class="sxs-lookup"><span data-stu-id="cf567-218">Real literals</span></span>

<span data-ttu-id="cf567-219">真实文本用于写入类型为 `float` 、和的值 `double` `decimal` 。</span><span class="sxs-lookup"><span data-stu-id="cf567-219">Real literals are used to write values of types `float`, `double`, and `decimal`.</span></span>

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

<span data-ttu-id="cf567-220">如果未指定*real_type_suffix* ，则真实文本的类型为 `double` 。</span><span class="sxs-lookup"><span data-stu-id="cf567-220">If no *real_type_suffix* is specified, the type of the real literal is `double`.</span></span> <span data-ttu-id="cf567-221">否则，实数类型后缀将确定真实文本的类型，如下所示：</span><span class="sxs-lookup"><span data-stu-id="cf567-221">Otherwise, the real type suffix determines the type of the real literal, as follows:</span></span>

*  <span data-ttu-id="cf567-222">用 `F` 或作为后缀的实文本 `f` 的类型为 `float` 。</span><span class="sxs-lookup"><span data-stu-id="cf567-222">A real literal suffixed by `F` or `f` is of type `float`.</span></span> <span data-ttu-id="cf567-223">例如，文本、、 `1f` `1.5f` `1e10f` 和 `123.456F` 都是类型 `float` 。</span><span class="sxs-lookup"><span data-stu-id="cf567-223">For example, the literals `1f`, `1.5f`, `1e10f`, and `123.456F` are all of type `float`.</span></span>
*  <span data-ttu-id="cf567-224">用 `D` 或作为后缀的实文本 `d` 的类型为 `double` 。</span><span class="sxs-lookup"><span data-stu-id="cf567-224">A real literal suffixed by `D` or `d` is of type `double`.</span></span> <span data-ttu-id="cf567-225">例如，文本、、 `1d` `1.5d` `1e10d` 和 `123.456D` 都是类型 `double` 。</span><span class="sxs-lookup"><span data-stu-id="cf567-225">For example, the literals `1d`, `1.5d`, `1e10d`, and `123.456D` are all of type `double`.</span></span>
*  <span data-ttu-id="cf567-226">用 `M` 或作为后缀的实文本 `m` 的类型为 `decimal` 。</span><span class="sxs-lookup"><span data-stu-id="cf567-226">A real literal suffixed by `M` or `m` is of type `decimal`.</span></span> <span data-ttu-id="cf567-227">例如，文本、、 `1m` `1.5m` `1e10m` 和 `123.456M` 都是类型 `decimal` 。</span><span class="sxs-lookup"><span data-stu-id="cf567-227">For example, the literals `1m`, `1.5m`, `1e10m`, and `123.456M` are all of type `decimal`.</span></span> <span data-ttu-id="cf567-228">通过采用精确值将此文本转换为一个 `decimal` 值，并在必要时使用银行家舍入（[decimal 类型](types.md#the-decimal-type)）舍入为最接近的可表示值。</span><span class="sxs-lookup"><span data-stu-id="cf567-228">This literal is converted to a `decimal` value by taking the exact value, and, if necessary, rounding to the nearest representable value using banker's rounding ([The decimal type](types.md#the-decimal-type)).</span></span> <span data-ttu-id="cf567-229">除非值舍入或值为零（在这种情况下，正负号为0），否则文本中的任何小数位数都将保留。</span><span class="sxs-lookup"><span data-stu-id="cf567-229">Any scale apparent in the literal is preserved unless the value is rounded or the value is zero (in which latter case the sign and scale will be 0).</span></span> <span data-ttu-id="cf567-230">因此， `2.900m` 将分析文本，以形成带有符号 `0` 、系数 `2900` 和刻度的小数 `3` 。</span><span class="sxs-lookup"><span data-stu-id="cf567-230">Hence, the literal `2.900m` will be parsed to form the decimal with sign `0`, coefficient `2900`, and scale `3`.</span></span>

<span data-ttu-id="cf567-231">如果指定的文本不能用指定的类型表示，则会发生编译时错误。</span><span class="sxs-lookup"><span data-stu-id="cf567-231">If the specified literal cannot be represented in the indicated type, a compile-time error occurs.</span></span>

<span data-ttu-id="cf567-232">类型或的实字的值 `float` `double` 是通过使用 IEEE "舍入到最近的" 模式确定的。</span><span class="sxs-lookup"><span data-stu-id="cf567-232">The value of a real literal of type `float` or `double` is determined by using the IEEE "round to nearest" mode.</span></span>

<span data-ttu-id="cf567-233">请注意，在实际文本中，小数点后始终需要小数位数。</span><span class="sxs-lookup"><span data-stu-id="cf567-233">Note that in a real literal, decimal digits are always required after the decimal point.</span></span> <span data-ttu-id="cf567-234">例如， `1.3F` 是一个真实文本，但 `1.F` 不是。</span><span class="sxs-lookup"><span data-stu-id="cf567-234">For example, `1.3F` is a real literal but `1.F` is not.</span></span>

#### <a name="character-literals"></a><span data-ttu-id="cf567-235">字符文本</span><span class="sxs-lookup"><span data-stu-id="cf567-235">Character literals</span></span>

<span data-ttu-id="cf567-236">字符文本表示单个字符，通常由引号中的字符组成，如中所示 `'a'` 。</span><span class="sxs-lookup"><span data-stu-id="cf567-236">A character literal represents a single character, and usually consists of a character in quotes, as in `'a'`.</span></span>

<span data-ttu-id="cf567-237">注意： ANTLR 语法表示法会使以下混乱！</span><span class="sxs-lookup"><span data-stu-id="cf567-237">Note: The ANTLR grammar notation makes the following confusing!</span></span> <span data-ttu-id="cf567-238">在 ANTLR 中，当您编写时， `\'` 它代表一个引号 `'` 。</span><span class="sxs-lookup"><span data-stu-id="cf567-238">In ANTLR, when you write `\'` it stands for a single quote `'`.</span></span> <span data-ttu-id="cf567-239">当你编写时， `\\` 它代表单个反斜杠 `\` 。</span><span class="sxs-lookup"><span data-stu-id="cf567-239">And when you write `\\` it stands for a single backslash `\`.</span></span> <span data-ttu-id="cf567-240">因此，字符文本的第一个规则意味着以单个单引号开始，然后是一个字符，然后是一个引号。</span><span class="sxs-lookup"><span data-stu-id="cf567-240">Therefore the first rule for a character literal means it starts with a single quote, then a character, then a single quote.</span></span> <span data-ttu-id="cf567-241">还有11个可能的简单转义序列 `\'` ：、、、 `\"` `\\` `\0` 、 `\a` 、 `\b` `\f` `\n` `\r` `\t` `\v` 、、、、、。</span><span class="sxs-lookup"><span data-stu-id="cf567-241">And the eleven possible simple escape sequences are `\'`, `\"`, `\\`, `\0`, `\a`, `\b`, `\f`, `\n`, `\r`, `\t`, `\v`.</span></span>

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

<span data-ttu-id="cf567-242">在字符中跟在反斜杠字符（）后面的字符 `\` 必须是下列字符之一： *character* `'` 、 `"` 、 `\` 、、 `0` `a` `b` `f` `n` `r` `t` `u` `U` `x` `v` 、、、、、、、、、。</span><span class="sxs-lookup"><span data-stu-id="cf567-242">A character that follows a backslash character (`\`) in a *character* must be one of the following characters: `'`, `"`, `\`, `0`, `a`, `b`, `f`, `n`, `r`, `t`, `u`, `U`, `x`, `v`.</span></span> <span data-ttu-id="cf567-243">否则，将发生编译时错误。</span><span class="sxs-lookup"><span data-stu-id="cf567-243">Otherwise, a compile-time error occurs.</span></span>

<span data-ttu-id="cf567-244">十六进制转义序列表示单个 Unicode 字符，其值由 "" 后面的十六进制数构成 `\x` 。</span><span class="sxs-lookup"><span data-stu-id="cf567-244">A hexadecimal escape sequence represents a single Unicode character, with the value formed by the hexadecimal number following "`\x`".</span></span>

<span data-ttu-id="cf567-245">如果字符文本表示的值大于 `U+FFFF` ，则会发生编译时错误。</span><span class="sxs-lookup"><span data-stu-id="cf567-245">If the value represented by a character literal is greater than `U+FFFF`, a compile-time error occurs.</span></span>

<span data-ttu-id="cf567-246">字符文本中的 Unicode 字符转义序列（[unicode 字符转义序列](lexical-structure.md#unicode-character-escape-sequences)）必须在到的范围内 `U+0000` `U+FFFF` 。</span><span class="sxs-lookup"><span data-stu-id="cf567-246">A Unicode character escape sequence ([Unicode character escape sequences](lexical-structure.md#unicode-character-escape-sequences)) in a character literal must be in the range `U+0000` to `U+FFFF`.</span></span>

<span data-ttu-id="cf567-247">简单的转义序列表示 Unicode 字符编码，如下表中所述。</span><span class="sxs-lookup"><span data-stu-id="cf567-247">A simple escape sequence represents a Unicode character encoding, as described in the table below.</span></span>


| <span data-ttu-id="cf567-248">__转义序列__</span><span class="sxs-lookup"><span data-stu-id="cf567-248">__Escape sequence__</span></span> | <span data-ttu-id="cf567-249">__字符名称__</span><span class="sxs-lookup"><span data-stu-id="cf567-249">__Character name__</span></span> | <span data-ttu-id="cf567-250">__Unicode 编码__</span><span class="sxs-lookup"><span data-stu-id="cf567-250">__Unicode encoding__</span></span> |
|---------------------|--------------------|----------------------|
| `\'`                | <span data-ttu-id="cf567-251">单引号</span><span class="sxs-lookup"><span data-stu-id="cf567-251">Single quote</span></span>       | `0x0027`             | 
| `\"`                | <span data-ttu-id="cf567-252">双引号</span><span class="sxs-lookup"><span data-stu-id="cf567-252">Double quote</span></span>       | `0x0022`             | 
| `\\`                | <span data-ttu-id="cf567-253">反斜杠</span><span class="sxs-lookup"><span data-stu-id="cf567-253">Backslash</span></span>          | `0x005C`             | 
| `\0`                | <span data-ttu-id="cf567-254">Null</span><span class="sxs-lookup"><span data-stu-id="cf567-254">Null</span></span>               | `0x0000`             | 
| `\a`                | <span data-ttu-id="cf567-255">警报</span><span class="sxs-lookup"><span data-stu-id="cf567-255">Alert</span></span>              | `0x0007`             | 
| `\b`                | <span data-ttu-id="cf567-256">Backspace</span><span class="sxs-lookup"><span data-stu-id="cf567-256">Backspace</span></span>          | `0x0008`             | 
| `\f`                | <span data-ttu-id="cf567-257">换页</span><span class="sxs-lookup"><span data-stu-id="cf567-257">Form feed</span></span>          | `0x000C`             | 
| `\n`                | <span data-ttu-id="cf567-258">换行</span><span class="sxs-lookup"><span data-stu-id="cf567-258">New line</span></span>           | `0x000A`             | 
| `\r`                | <span data-ttu-id="cf567-259">回车</span><span class="sxs-lookup"><span data-stu-id="cf567-259">Carriage return</span></span>    | `0x000D`             | 
| `\t`                | <span data-ttu-id="cf567-260">水平制表符</span><span class="sxs-lookup"><span data-stu-id="cf567-260">Horizontal tab</span></span>     | `0x0009`             | 
| `\v`                | <span data-ttu-id="cf567-261">垂直制表符</span><span class="sxs-lookup"><span data-stu-id="cf567-261">Vertical tab</span></span>       | `0x000B`             | 

<span data-ttu-id="cf567-262">*Character_literal*的类型为 `char` 。</span><span class="sxs-lookup"><span data-stu-id="cf567-262">The type of a *character_literal* is `char`.</span></span>

#### <a name="string-literals"></a><span data-ttu-id="cf567-263">字符串文本</span><span class="sxs-lookup"><span data-stu-id="cf567-263">String literals</span></span>

<span data-ttu-id="cf567-264">C # 支持两种形式的字符串文本：***常规字符串文本***和***原义字符串***。</span><span class="sxs-lookup"><span data-stu-id="cf567-264">C# supports two forms of string literals: ***regular string literals*** and ***verbatim string literals***.</span></span>

<span data-ttu-id="cf567-265">正则字符串文字由零个或多个字符括在双引号中，如中所示， `"hello"` 并且可能包括简单转义序列（如 `\t` 制表符）和十六进制和 Unicode 转义序列。</span><span class="sxs-lookup"><span data-stu-id="cf567-265">A regular string literal consists of zero or more characters enclosed in double quotes, as in `"hello"`, and may include both simple escape sequences (such as `\t` for the tab character), and hexadecimal and Unicode escape sequences.</span></span>

<span data-ttu-id="cf567-266">原义字符串包含一个 `@` 字符，后跟一个双引号字符、零个或多个字符和一个右双引号字符。</span><span class="sxs-lookup"><span data-stu-id="cf567-266">A verbatim string literal consists of an `@` character followed by a double-quote character, zero or more characters, and a closing double-quote character.</span></span> <span data-ttu-id="cf567-267">一个简单的示例是 `@"hello"` 。</span><span class="sxs-lookup"><span data-stu-id="cf567-267">A simple example is `@"hello"`.</span></span> <span data-ttu-id="cf567-268">在原义字符串文本中，分隔符之间的字符按原义解释，唯一的例外是*quote_escape_sequence*。</span><span class="sxs-lookup"><span data-stu-id="cf567-268">In a verbatim string literal, the characters between the delimiters are interpreted verbatim, the only exception being a *quote_escape_sequence*.</span></span> <span data-ttu-id="cf567-269">具体而言，简单转义序列和十六进制和 Unicode 转义序列不会在原义字符串文本中处理。</span><span class="sxs-lookup"><span data-stu-id="cf567-269">In particular, simple escape sequences, and hexadecimal and Unicode escape sequences are not processed in verbatim string literals.</span></span> <span data-ttu-id="cf567-270">原义字符串文本可以跨多个行。</span><span class="sxs-lookup"><span data-stu-id="cf567-270">A verbatim string literal may span multiple lines.</span></span>

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

<span data-ttu-id="cf567-271">在 regular_string_literal_character 中的反斜杠字符（ `\` ）后面的*regular_string_literal_character*字符必须是下列字符之一： `'` 、 `"` 、 `\` 、 `0` 、 `a` `b` `f` `n` `r` `t` `u` `U` `x` `v` 、、、、、、、、、。</span><span class="sxs-lookup"><span data-stu-id="cf567-271">A character that follows a backslash character (`\`) in a *regular_string_literal_character* must be one of the following characters: `'`, `"`, `\`, `0`, `a`, `b`, `f`, `n`, `r`, `t`, `u`, `U`, `x`, `v`.</span></span> <span data-ttu-id="cf567-272">否则，将发生编译时错误。</span><span class="sxs-lookup"><span data-stu-id="cf567-272">Otherwise, a compile-time error occurs.</span></span>

<span data-ttu-id="cf567-273">示例</span><span class="sxs-lookup"><span data-stu-id="cf567-273">The example</span></span>
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
<span data-ttu-id="cf567-274">显示各种字符串文本。</span><span class="sxs-lookup"><span data-stu-id="cf567-274">shows a variety of string literals.</span></span> <span data-ttu-id="cf567-275">最后一个字符串文字 `j` 是跨多行的逐字字符串。</span><span class="sxs-lookup"><span data-stu-id="cf567-275">The last string literal, `j`, is a verbatim string literal that spans multiple lines.</span></span> <span data-ttu-id="cf567-276">引号之间的字符（包括空白字符，如换行符）会逐字保留。</span><span class="sxs-lookup"><span data-stu-id="cf567-276">The characters between the quotation marks, including white space such as new line characters, are preserved verbatim.</span></span>

<span data-ttu-id="cf567-277">由于十六进制转义序列可以具有可变数量的十六进制数字，因此字符串文本 `"\x123"` 包含一个具有十六进制值123的单个字符。</span><span class="sxs-lookup"><span data-stu-id="cf567-277">Since a hexadecimal escape sequence can have a variable number of hex digits, the string literal `"\x123"` contains a single character with hex value 123.</span></span> <span data-ttu-id="cf567-278">若要创建一个字符串，该字符串包含的字符的十六进制值为12，后跟字符3，则可以写 `"\x00123"` `"\x12" + "3"` 。</span><span class="sxs-lookup"><span data-stu-id="cf567-278">To create a string containing the character with hex value 12 followed by the character 3, one could write `"\x00123"` or `"\x12" + "3"` instead.</span></span>

<span data-ttu-id="cf567-279">*String_literal*的类型为 `string` 。</span><span class="sxs-lookup"><span data-stu-id="cf567-279">The type of a *string_literal* is `string`.</span></span>

<span data-ttu-id="cf567-280">每个字符串文本不一定会生成新的字符串实例。</span><span class="sxs-lookup"><span data-stu-id="cf567-280">Each string literal does not necessarily result in a new string instance.</span></span> <span data-ttu-id="cf567-281">如果两个或更多的字符串文本在同一程序中出现，则根据字符串相等运算符（[字符串相等运算符](expressions.md#string-equality-operators)）进行等效时，这些字符串将引用相同的字符串实例。</span><span class="sxs-lookup"><span data-stu-id="cf567-281">When two or more string literals that are equivalent according to the string equality operator ([String equality operators](expressions.md#string-equality-operators)) appear in the same program, these string literals refer to the same string instance.</span></span> <span data-ttu-id="cf567-282">例如，生成的输出</span><span class="sxs-lookup"><span data-stu-id="cf567-282">For instance, the output produced by</span></span>
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
<span data-ttu-id="cf567-283">是 `True` 因为两个文本引用相同的字符串实例。</span><span class="sxs-lookup"><span data-stu-id="cf567-283">is `True` because the two literals refer to the same string instance.</span></span>

#### <a name="interpolated-string-literals"></a><span data-ttu-id="cf567-284">内插字符串文本</span><span class="sxs-lookup"><span data-stu-id="cf567-284">Interpolated string literals</span></span>

<span data-ttu-id="cf567-285">内插字符串与字符串文本类似，但包含用 and 分隔的孔，其中的 `{` `}` 表达式可以出现。</span><span class="sxs-lookup"><span data-stu-id="cf567-285">Interpolated string literals are similar to string literals, but contain holes delimited by `{` and `}`, wherein expressions can occur.</span></span> <span data-ttu-id="cf567-286">在运行时，将对表达式进行计算，目的是将其文本窗体替换为发生该洞的位置的字符串。</span><span class="sxs-lookup"><span data-stu-id="cf567-286">At runtime, the expressions are evaluated with the purpose of having their textual forms substituted into the string at the place where the hole occurs.</span></span> <span data-ttu-id="cf567-287">字符串内插的语法和语义在节（内[插字符串](expressions.md#interpolated-strings)）中进行了介绍。</span><span class="sxs-lookup"><span data-stu-id="cf567-287">The syntax and semantics of string interpolation are described in section ([Interpolated strings](expressions.md#interpolated-strings)).</span></span>

<span data-ttu-id="cf567-288">与字符串文本一样，内插字符串文本可以是正则为或是原义字符串。</span><span class="sxs-lookup"><span data-stu-id="cf567-288">Like string literals, interpolated string literals can be either regular or verbatim.</span></span> <span data-ttu-id="cf567-289">内插正则字符串文本由 `$"` 和分隔 `"` ，并由和分隔逐字字符串。 `$@"` `"`</span><span class="sxs-lookup"><span data-stu-id="cf567-289">Interpolated regular string literals are delimited by `$"` and `"`, and interpolated verbatim string literals are delimited by `$@"` and `"`.</span></span>

<span data-ttu-id="cf567-290">与其他文本一样，内插字符串的词法分析最初会根据下面的语法产生单个令牌。</span><span class="sxs-lookup"><span data-stu-id="cf567-290">Like other literals, lexical analysis of an interpolated string literal initially results in a single token, as per the grammar below.</span></span> <span data-ttu-id="cf567-291">但是，在句法分析之前，内插字符串的单个标记将被分解为包含该洞的字符串部分的多个标记，而洞中发生的输入元素会在词法上重新并非。</span><span class="sxs-lookup"><span data-stu-id="cf567-291">However, before syntactic analysis, the single token of an interpolated string literal is broken into several tokens for the parts of the string enclosing the holes, and the input elements occurring in the holes are lexically analysed again.</span></span> <span data-ttu-id="cf567-292">这反过来会生成更多的内插字符串文字，但如果词法上正确，最终将导致一系列标记，以便进行语法分析。</span><span class="sxs-lookup"><span data-stu-id="cf567-292">This may in turn produce more interpolated string literals to be processed, but, if lexically correct, will eventually lead to a sequence of tokens for syntactic analysis to process.</span></span>

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

<span data-ttu-id="cf567-293">*Interpolated_string_literal*令牌重新解释为多个令牌和其他输入元素，如下所示： *interpolated_string_literal*中出现的顺序：</span><span class="sxs-lookup"><span data-stu-id="cf567-293">An *interpolated_string_literal* token is reinterpreted as multiple tokens and other input elements as follows, in order of occurrence in the *interpolated_string_literal*:</span></span>

* <span data-ttu-id="cf567-294">以下各项分别作为单独的标记重新解释：前导 `$` 符号、 *interpolated_regular_string_whole*、 *interpolated_regular_string_start*、 *interpolated_regular_string_mid*、 *interpolated_regular_string_end*、 *interpolated_verbatim_string_whole*、 *interpolated_verbatim_string_start*、 *interpolated_verbatim_string_mid*和*interpolated_verbatim_string_end*。</span><span class="sxs-lookup"><span data-stu-id="cf567-294">Occurrences of the following are reinterpreted as separate individual tokens: the leading `$` sign, *interpolated_regular_string_whole*, *interpolated_regular_string_start*, *interpolated_regular_string_mid*, *interpolated_regular_string_end*, *interpolated_verbatim_string_whole*, *interpolated_verbatim_string_start*, *interpolated_verbatim_string_mid* and *interpolated_verbatim_string_end*.</span></span>
* <span data-ttu-id="cf567-295">在这些*regular_balanced_text*和*verbatim_balanced_text*之间发生的情况重新处理为*input_section* （[词法分析](lexical-structure.md#lexical-analysis)），并且重新解释为输入元素的结果序列。</span><span class="sxs-lookup"><span data-stu-id="cf567-295">Occurrences of *regular_balanced_text* and *verbatim_balanced_text* between these are reprocessed as an *input_section* ([Lexical analysis](lexical-structure.md#lexical-analysis)) and are reinterpreted as the resulting sequence of input elements.</span></span> <span data-ttu-id="cf567-296">这些转换可能会将内插字符串文本标记包含为重新解释。</span><span class="sxs-lookup"><span data-stu-id="cf567-296">These may in turn include interpolated string literal tokens to be reinterpreted.</span></span>

<span data-ttu-id="cf567-297">语法分析会将令牌重新组合到*interpolated_string_expression* （内[插的字符串](expressions.md#interpolated-strings)）。</span><span class="sxs-lookup"><span data-stu-id="cf567-297">Syntactic analysis will recombine the tokens into an *interpolated_string_expression* ([Interpolated strings](expressions.md#interpolated-strings)).</span></span>

<span data-ttu-id="cf567-298">示例 TODO</span><span class="sxs-lookup"><span data-stu-id="cf567-298">Examples TODO</span></span>


#### <a name="the-null-literal"></a><span data-ttu-id="cf567-299">Null 文本</span><span class="sxs-lookup"><span data-stu-id="cf567-299">The null literal</span></span>

```antlr
null_literal
    : 'null'
    ;
```

<span data-ttu-id="cf567-300">*Null_literal*可以隐式转换为引用类型或可以为 null 的类型。</span><span class="sxs-lookup"><span data-stu-id="cf567-300">The  *null_literal* can be implicitly converted to a reference type or nullable type.</span></span>

### <a name="operators-and-punctuators"></a><span data-ttu-id="cf567-301">运算符和标点符号</span><span class="sxs-lookup"><span data-stu-id="cf567-301">Operators and punctuators</span></span>

<span data-ttu-id="cf567-302">有多种运算符和标点符号。</span><span class="sxs-lookup"><span data-stu-id="cf567-302">There are several kinds of operators and punctuators.</span></span> <span data-ttu-id="cf567-303">表达式中使用运算符来描述涉及一个或多个操作数的操作。</span><span class="sxs-lookup"><span data-stu-id="cf567-303">Operators are used in expressions to describe operations involving one or more operands.</span></span> <span data-ttu-id="cf567-304">例如，表达式 `a + b` 使用 `+` 运算符添加两个操作数 `a` 和 `b`。</span><span class="sxs-lookup"><span data-stu-id="cf567-304">For example, the expression `a + b` uses the `+` operator to add the two operands `a` and `b`.</span></span> <span data-ttu-id="cf567-305">标点符号用于分组和分隔。</span><span class="sxs-lookup"><span data-stu-id="cf567-305">Punctuators are for grouping and separating.</span></span>

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

<span data-ttu-id="cf567-306">*Right_shift*和*right_shift_assignment*生产中的竖线用于指示，与句法语法中的其他生产不同，标记之间不允许任何类型的字符（甚至不允许使用空格）。</span><span class="sxs-lookup"><span data-stu-id="cf567-306">The vertical bar in the *right_shift* and *right_shift_assignment* productions are used to indicate that, unlike other productions in the syntactic grammar, no characters of any kind (not even whitespace) are allowed between the tokens.</span></span> <span data-ttu-id="cf567-307">将对这些生产进行特殊处理，以便能够正确地处理*type_parameter_list*（[类型参数](classes.md#type-parameters)）。</span><span class="sxs-lookup"><span data-stu-id="cf567-307">These productions are treated specially in order to enable the correct  handling of *type_parameter_list*s ([Type parameters](classes.md#type-parameters)).</span></span>

## <a name="pre-processing-directives"></a><span data-ttu-id="cf567-308">预处理指令</span><span class="sxs-lookup"><span data-stu-id="cf567-308">Pre-processing directives</span></span>

<span data-ttu-id="cf567-309">预处理指令提供按条件跳过源文件部分的功能，报告错误和警告条件，以及描述源代码的不同区域。</span><span class="sxs-lookup"><span data-stu-id="cf567-309">The pre-processing directives provide the ability to conditionally skip sections of source files, to report error and warning conditions, and to delineate distinct regions of source code.</span></span> <span data-ttu-id="cf567-310">术语 "预处理指令" 仅用于与 C 和 c + + 编程语言的一致性。</span><span class="sxs-lookup"><span data-stu-id="cf567-310">The term "pre-processing directives" is used only for consistency with the C and C++ programming languages.</span></span> <span data-ttu-id="cf567-311">在 c # 中，没有单独的预处理步骤;预处理指令作为词法分析阶段的一部分进行处理。</span><span class="sxs-lookup"><span data-stu-id="cf567-311">In C#, there is no separate pre-processing step; pre-processing directives are processed as part of the lexical analysis phase.</span></span>

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

<span data-ttu-id="cf567-312">以下预处理指令可用：</span><span class="sxs-lookup"><span data-stu-id="cf567-312">The following pre-processing directives are available:</span></span>

*  <span data-ttu-id="cf567-313">`#define`和 `#undef` ，分别用于定义和取消定义条件编译符号（[声明指令](lexical-structure.md#declaration-directives)）。</span><span class="sxs-lookup"><span data-stu-id="cf567-313">`#define` and `#undef`, which are used to define and undefine, respectively, conditional compilation symbols ([Declaration directives](lexical-structure.md#declaration-directives)).</span></span>
*  <span data-ttu-id="cf567-314">`#if`、 `#elif` 、 `#else` 和 `#endif` ，用于有条件地跳过源代码的各个部分（[条件编译指令](lexical-structure.md#conditional-compilation-directives)）。</span><span class="sxs-lookup"><span data-stu-id="cf567-314">`#if`, `#elif`, `#else`, and `#endif`, which are used to conditionally skip sections of source code ([Conditional compilation directives](lexical-structure.md#conditional-compilation-directives)).</span></span>
*  <span data-ttu-id="cf567-315">`#line`，用于控制发出的错误和警告的行号（[行指令](lexical-structure.md#line-directives)）。</span><span class="sxs-lookup"><span data-stu-id="cf567-315">`#line`, which is used to control line numbers emitted for errors and warnings ([Line directives](lexical-structure.md#line-directives)).</span></span>
*  <span data-ttu-id="cf567-316">`#error`和 `#warning` ，分别用于发出错误和警告（[诊断指令](lexical-structure.md#diagnostic-directives)）。</span><span class="sxs-lookup"><span data-stu-id="cf567-316">`#error` and `#warning`, which are used to issue errors and warnings, respectively ([Diagnostic directives](lexical-structure.md#diagnostic-directives)).</span></span>
*  <span data-ttu-id="cf567-317">`#region`和 `#endregion` ，用于显式标记源代码的各个部分（[Region 指令](lexical-structure.md#region-directives)）。</span><span class="sxs-lookup"><span data-stu-id="cf567-317">`#region` and `#endregion`, which are used to explicitly mark sections of source code ([Region directives](lexical-structure.md#region-directives)).</span></span>
*  <span data-ttu-id="cf567-318">`#pragma`，用于指定编译器的可选上下文信息（[杂注指令](lexical-structure.md#pragma-directives)）。</span><span class="sxs-lookup"><span data-stu-id="cf567-318">`#pragma`, which is used to specify optional contextual information to the compiler ([Pragma directives](lexical-structure.md#pragma-directives)).</span></span>

<span data-ttu-id="cf567-319">预处理指令始终占用一行单独的源代码，并始终以 `#` 字符和预处理指令名称开头。</span><span class="sxs-lookup"><span data-stu-id="cf567-319">A pre-processing directive always occupies a separate line of source code and always begins with a `#` character and a pre-processing directive name.</span></span> <span data-ttu-id="cf567-320">空格可能出现在字符和 `#` `#` 指令名称之间。</span><span class="sxs-lookup"><span data-stu-id="cf567-320">White space may occur before the `#` character and between the `#` character and the directive name.</span></span>

<span data-ttu-id="cf567-321">包含、、、、、、或指令的源行 `#define` `#undef` `#if` `#elif` `#else` `#endif` `#line` `#endregion` 可以以单行注释结束。</span><span class="sxs-lookup"><span data-stu-id="cf567-321">A source line containing a `#define`, `#undef`, `#if`, `#elif`, `#else`, `#endif`, `#line`, or `#endregion` directive may end with a single-line comment.</span></span> <span data-ttu-id="cf567-322">`/* */`在包含预处理指令的源行上不允许使用带分隔符的注释（注释的样式）。</span><span class="sxs-lookup"><span data-stu-id="cf567-322">Delimited comments (the `/* */` style of comments) are not permitted on source lines containing pre-processing directives.</span></span>

<span data-ttu-id="cf567-323">预处理指令不是标记，不是 c # 语法语法的一部分。</span><span class="sxs-lookup"><span data-stu-id="cf567-323">Pre-processing directives are not tokens and are not part of the syntactic grammar of C#.</span></span> <span data-ttu-id="cf567-324">但是，预处理指令可用于包含或排除标记序列，并以这种方式影响 c # 程序的含义。</span><span class="sxs-lookup"><span data-stu-id="cf567-324">However, pre-processing directives can be used to include or exclude sequences of tokens and can in that way affect the meaning of a C# program.</span></span> <span data-ttu-id="cf567-325">例如，编译后，程序：</span><span class="sxs-lookup"><span data-stu-id="cf567-325">For example, when compiled, the program:</span></span>
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
<span data-ttu-id="cf567-326">生成与程序完全相同的标记序列：</span><span class="sxs-lookup"><span data-stu-id="cf567-326">results in the exact same sequence of tokens as the program:</span></span>
```csharp
class C
{
    void F() {}
    void I() {}
}
```

<span data-ttu-id="cf567-327">因此，在语义上，这两个程序在语法上非常不同，它们是相同的。</span><span class="sxs-lookup"><span data-stu-id="cf567-327">Thus, whereas lexically, the two programs are quite different, syntactically, they are identical.</span></span>

### <a name="conditional-compilation-symbols"></a><span data-ttu-id="cf567-328">“条件编译符”号</span><span class="sxs-lookup"><span data-stu-id="cf567-328">Conditional compilation symbols</span></span>

<span data-ttu-id="cf567-329">、、和指令提供的条件编译功能 `#if` `#elif` `#else` `#endif` 通过预处理表达式（[预处理表达式](lexical-structure.md#pre-processing-expressions)）和条件编译符号进行控制。</span><span class="sxs-lookup"><span data-stu-id="cf567-329">The conditional compilation functionality provided by the `#if`, `#elif`, `#else`, and `#endif` directives is controlled through pre-processing expressions ([Pre-processing expressions](lexical-structure.md#pre-processing-expressions)) and conditional compilation symbols.</span></span>

```antlr
conditional_symbol
    : '<Any identifier_or_keyword except true or false>'
    ;
```

<span data-ttu-id="cf567-330">条件编译符号有两种可能的状态：***已定义***或***未定义***。</span><span class="sxs-lookup"><span data-stu-id="cf567-330">A conditional compilation symbol has two possible states: ***defined*** or ***undefined***.</span></span> <span data-ttu-id="cf567-331">在源文件的词法处理开始时，不定义条件编译符号，除非它已由外部机制（例如命令行编译器选项）显式定义。</span><span class="sxs-lookup"><span data-stu-id="cf567-331">At the beginning of the lexical processing of a source file, a conditional compilation symbol is undefined unless it has been explicitly defined by an external mechanism (such as a command-line compiler option).</span></span> <span data-ttu-id="cf567-332">`#define`处理指令时，该指令中名为的条件编译符号将在该源文件中进行定义。</span><span class="sxs-lookup"><span data-stu-id="cf567-332">When a `#define` directive is processed, the conditional compilation symbol named in that directive becomes defined in that source file.</span></span> <span data-ttu-id="cf567-333">在 `#undef` 处理同一符号的指令之前，或在到达源文件末尾之前，该符号保持为已定义。</span><span class="sxs-lookup"><span data-stu-id="cf567-333">The symbol remains defined until an `#undef` directive for that same symbol is processed, or until the end of the source file is reached.</span></span> <span data-ttu-id="cf567-334">这意味着， `#define` `#undef` 一个源文件中的和指令对同一程序中的其他源文件不起作用。</span><span class="sxs-lookup"><span data-stu-id="cf567-334">An implication of this is that `#define` and `#undef` directives in one source file have no effect on other source files in the same program.</span></span>

<span data-ttu-id="cf567-335">在预处理表达式中引用时，定义的条件编译符号具有布尔值 `true` ，未定义的条件编译符号具有布尔值 `false` 。</span><span class="sxs-lookup"><span data-stu-id="cf567-335">When referenced in a pre-processing expression, a defined conditional compilation symbol has the boolean value `true`, and an undefined conditional compilation symbol has the boolean value `false`.</span></span> <span data-ttu-id="cf567-336">在预处理表达式中引用条件编译符号之前，不需要显式声明它们。</span><span class="sxs-lookup"><span data-stu-id="cf567-336">There is no requirement that conditional compilation symbols be explicitly declared before they are referenced in pre-processing expressions.</span></span> <span data-ttu-id="cf567-337">相反，未声明的符号只是未定义的，因此具有值 `false` 。</span><span class="sxs-lookup"><span data-stu-id="cf567-337">Instead, undeclared symbols are simply undefined and thus have the value `false`.</span></span>

<span data-ttu-id="cf567-338">条件编译符号的命名空间是不同的，并且独立于 c # 程序中的所有其他命名实体。</span><span class="sxs-lookup"><span data-stu-id="cf567-338">The name space for conditional compilation symbols is distinct and separate from all other named entities in a C# program.</span></span> <span data-ttu-id="cf567-339">条件编译符号只能在 `#define` 和 `#undef` 指令以及预处理表达式中引用。</span><span class="sxs-lookup"><span data-stu-id="cf567-339">Conditional compilation symbols can only be referenced in `#define` and `#undef` directives and in pre-processing expressions.</span></span>

### <a name="pre-processing-expressions"></a><span data-ttu-id="cf567-340">预处理表达式</span><span class="sxs-lookup"><span data-stu-id="cf567-340">Pre-processing expressions</span></span>

<span data-ttu-id="cf567-341">预处理表达式可以出现在 `#if` 和指令中 `#elif` 。</span><span class="sxs-lookup"><span data-stu-id="cf567-341">Pre-processing expressions can occur in `#if` and `#elif` directives.</span></span> <span data-ttu-id="cf567-342">`!` `==` `!=` `&&` `||` 预处理表达式中允许使用运算符、、和，括号可用于分组。</span><span class="sxs-lookup"><span data-stu-id="cf567-342">The operators `!`, `==`, `!=`, `&&` and `||` are permitted in pre-processing expressions, and parentheses may be used for grouping.</span></span>

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

<span data-ttu-id="cf567-343">在预处理表达式中引用时，定义的条件编译符号具有布尔值 `true` ，未定义的条件编译符号具有布尔值 `false` 。</span><span class="sxs-lookup"><span data-stu-id="cf567-343">When referenced in a pre-processing expression, a defined conditional compilation symbol has the boolean value `true`, and an undefined conditional compilation symbol has the boolean value `false`.</span></span>

<span data-ttu-id="cf567-344">预处理表达式的计算始终产生布尔值。</span><span class="sxs-lookup"><span data-stu-id="cf567-344">Evaluation of a pre-processing expression always yields a boolean value.</span></span> <span data-ttu-id="cf567-345">预处理表达式的计算规则与常量表达式的计算规则相同（[常数](expressions.md#constant-expressions)表达式），只不过只能引用的用户定义实体是条件编译符号。</span><span class="sxs-lookup"><span data-stu-id="cf567-345">The rules of evaluation for a pre-processing expression are the same as those for a constant expression ([Constant expressions](expressions.md#constant-expressions)), except that the only user-defined entities that can be referenced are conditional compilation symbols.</span></span>

### <a name="declaration-directives"></a><span data-ttu-id="cf567-346">声明指令</span><span class="sxs-lookup"><span data-stu-id="cf567-346">Declaration directives</span></span>

<span data-ttu-id="cf567-347">声明指令用于定义或取消定义条件编译符号。</span><span class="sxs-lookup"><span data-stu-id="cf567-347">The declaration directives are used to define or undefine conditional compilation symbols.</span></span>

```antlr
pp_declaration
    : whitespace? '#' whitespace? 'define' whitespace conditional_symbol pp_new_line
    | whitespace? '#' whitespace? 'undef' whitespace conditional_symbol pp_new_line
    ;

pp_new_line
    : whitespace? single_line_comment? new_line
    ;
```

<span data-ttu-id="cf567-348">指令的处理 `#define` 会使给定的条件编译符号成为定义，并从跟在指令后面的源行开始。</span><span class="sxs-lookup"><span data-stu-id="cf567-348">The processing of a `#define` directive causes the given conditional compilation symbol to become defined, starting with the source line that follows the directive.</span></span> <span data-ttu-id="cf567-349">同样，处理 `#undef` 指令会使给定的条件编译符号变成未定义的，从该指令后面的源行开始。</span><span class="sxs-lookup"><span data-stu-id="cf567-349">Likewise, the processing of an `#undef` directive causes the given conditional compilation symbol to become undefined, starting with the source line that follows the directive.</span></span>

<span data-ttu-id="cf567-350">`#define` `#undef` 源文件中的任何和指令必须出现在源文件中的第一个*标记*（[标记](lexical-structure.md#tokens)）之前; 否则，将发生编译时错误。</span><span class="sxs-lookup"><span data-stu-id="cf567-350">Any `#define` and `#undef` directives in a source file must occur before the first *token* ([Tokens](lexical-structure.md#tokens)) in the source file; otherwise a compile-time error occurs.</span></span> <span data-ttu-id="cf567-351">在直观的术语中， `#define` 和 `#undef` 指令必须位于源文件中的任何 "真实代码" 之前。</span><span class="sxs-lookup"><span data-stu-id="cf567-351">In intuitive terms, `#define` and `#undef` directives must precede any "real code" in the source file.</span></span>

<span data-ttu-id="cf567-352">示例：</span><span class="sxs-lookup"><span data-stu-id="cf567-352">The example:</span></span>
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
<span data-ttu-id="cf567-353">有效，因为 `#define` 指令位于源文件中的第一个标记（ `namespace` 关键字）之前。</span><span class="sxs-lookup"><span data-stu-id="cf567-353">is valid because the `#define` directives precede the first token (the `namespace` keyword) in the source file.</span></span>

<span data-ttu-id="cf567-354">下面的示例会导致编译时错误，因为它会 `#define` 跟随真实代码：</span><span class="sxs-lookup"><span data-stu-id="cf567-354">The following example results in a compile-time error because a `#define` follows real code:</span></span>
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

<span data-ttu-id="cf567-355">`#define`可以定义已定义的条件编译符号，而不会 `#undef` 对该符号进行任何干预。</span><span class="sxs-lookup"><span data-stu-id="cf567-355">A `#define` may define a conditional compilation symbol that is already defined, without there being any intervening `#undef` for that symbol.</span></span> <span data-ttu-id="cf567-356">下面的示例定义条件编译符号 `A` ，然后再次定义它。</span><span class="sxs-lookup"><span data-stu-id="cf567-356">The example below defines a conditional compilation symbol `A` and then defines it again.</span></span>
```csharp
#define A
#define A
```

<span data-ttu-id="cf567-357">`#undef`可能 "取消定义" 未定义的条件编译符号。</span><span class="sxs-lookup"><span data-stu-id="cf567-357">A `#undef` may "undefine" a conditional compilation symbol that is not defined.</span></span> <span data-ttu-id="cf567-358">下面的示例定义条件编译符号 `A` ，然后将其取消定义两次; 虽然第二个 `#undef` 不起作用，但仍有效。</span><span class="sxs-lookup"><span data-stu-id="cf567-358">The example below defines a conditional compilation symbol `A` and then undefines it twice; although the second `#undef` has no effect, it is still valid.</span></span>
```csharp
#define A
#undef A
#undef A
```

### <a name="conditional-compilation-directives"></a><span data-ttu-id="cf567-359">条件编译指令</span><span class="sxs-lookup"><span data-stu-id="cf567-359">Conditional compilation directives</span></span>

<span data-ttu-id="cf567-360">条件编译指令用于有条件地包含或排除源文件的某些部分。</span><span class="sxs-lookup"><span data-stu-id="cf567-360">The conditional compilation directives are used to conditionally include or exclude portions of a source file.</span></span>

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

<span data-ttu-id="cf567-361">如语法所示，必须按顺序（按顺序）、 `#if` 指令、零个或多个 `#elif` 指令、零个或一个 `#else` 指令以及指令来写入条件编译指令 `#endif` 。</span><span class="sxs-lookup"><span data-stu-id="cf567-361">As indicated by the syntax, conditional compilation directives must be written as sets consisting of, in order, an `#if` directive, zero or more `#elif` directives, zero or one `#else` directive, and an `#endif` directive.</span></span> <span data-ttu-id="cf567-362">在指令与源代码的条件部分之间。</span><span class="sxs-lookup"><span data-stu-id="cf567-362">Between the directives are conditional sections of source code.</span></span> <span data-ttu-id="cf567-363">每个部分都由前面的指令控制。</span><span class="sxs-lookup"><span data-stu-id="cf567-363">Each section is controlled by the immediately preceding directive.</span></span> <span data-ttu-id="cf567-364">条件部分本身可能包含嵌套的条件编译指令，前提是这些指令构成了完整的集。</span><span class="sxs-lookup"><span data-stu-id="cf567-364">A conditional section may itself contain nested conditional compilation directives provided these directives form complete sets.</span></span>

<span data-ttu-id="cf567-365">*Pp_conditional*最多为常规词法处理选择一个包含的*conditional_section*：</span><span class="sxs-lookup"><span data-stu-id="cf567-365">A *pp_conditional* selects at most one of the contained *conditional_section*s for normal lexical processing:</span></span>

*  <span data-ttu-id="cf567-366">和指令的*pp_expression*按 `#if` `#elif` 顺序进行计算，直到有一个结果 `true` 。</span><span class="sxs-lookup"><span data-stu-id="cf567-366">The *pp_expression*s of the `#if` and `#elif` directives are evaluated in order until one yields `true`.</span></span> <span data-ttu-id="cf567-367">如果表达式产生了 `true` ，则选择相应指令的*conditional_section* 。</span><span class="sxs-lookup"><span data-stu-id="cf567-367">If an expression yields `true`, the *conditional_section* of the corresponding directive is selected.</span></span>
*  <span data-ttu-id="cf567-368">如果所有*pp_expression*均 `false` 为 yield，并且 `#else` 存在指令，则选择指令的*conditional_section* `#else` 。</span><span class="sxs-lookup"><span data-stu-id="cf567-368">If all *pp_expression*s yield `false`, and if an `#else` directive is present, the *conditional_section* of the `#else` directive is selected.</span></span>
*  <span data-ttu-id="cf567-369">否则，不会选择任何*conditional_section* 。</span><span class="sxs-lookup"><span data-stu-id="cf567-369">Otherwise, no *conditional_section* is selected.</span></span>

<span data-ttu-id="cf567-370">选定的*conditional_section*（如果有）将作为正常*input_section*进行处理：节中包含的源代码必须符合词法语法;标记是从节中的源代码生成的;部分中的和预处理指令具有指定的效果。</span><span class="sxs-lookup"><span data-stu-id="cf567-370">The selected *conditional_section*, if any, is processed as a normal *input_section*: the source code contained in the section must adhere to the lexical grammar; tokens are generated from the source code in the section; and pre-processing directives in the section have the prescribed effects.</span></span>

<span data-ttu-id="cf567-371">剩余的*conditional_section*（如果有）将作为*skipped_section*s 进行处理：除预处理指令以外，部分中的源代码无需遵守词法语法;不会从该部分中的源代码生成任何标记;部分中的和预处理指令必须在词法上是正确的，但不会进行处理。</span><span class="sxs-lookup"><span data-stu-id="cf567-371">The remaining *conditional_section*s, if any, are processed as *skipped_section*s: except for pre-processing directives, the source code in the section need not adhere to the lexical grammar; no tokens are generated from the source code in the section; and pre-processing directives in the section must be lexically correct but are not otherwise processed.</span></span> <span data-ttu-id="cf567-372">在作为*skipped_section*进行处理的*conditional_section*中，任何嵌套的*conditional_section*（包含在嵌套 `#if` ... `#endif` 和 `#region` ... 构造中 `#endregion` ）也作为*skipped_section*处理。</span><span class="sxs-lookup"><span data-stu-id="cf567-372">Within a *conditional_section* that is being processed as a *skipped_section*, any nested *conditional_section*s (contained in nested `#if`...`#endif` and `#region`...`#endregion` constructs) are also processed as *skipped_section*s.</span></span>

<span data-ttu-id="cf567-373">下面的示例说明了条件编译指令如何嵌套：</span><span class="sxs-lookup"><span data-stu-id="cf567-373">The following example illustrates how conditional compilation directives can nest:</span></span>
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

<span data-ttu-id="cf567-374">除预处理指令外，跳过的源代码不受词法分析的限制。</span><span class="sxs-lookup"><span data-stu-id="cf567-374">Except for pre-processing directives, skipped source code is not subject to lexical analysis.</span></span> <span data-ttu-id="cf567-375">例如，尽管部分中出现未终止的注释，以下内容仍有效 `#else` ：</span><span class="sxs-lookup"><span data-stu-id="cf567-375">For example, the following is valid despite the unterminated comment in the `#else` section:</span></span>
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

<span data-ttu-id="cf567-376">但请注意，即使在源代码中跳过的部分，预处理指令也需要在词法上正确。</span><span class="sxs-lookup"><span data-stu-id="cf567-376">Note, however, that pre-processing directives are required to be lexically correct even in skipped sections of source code.</span></span>

<span data-ttu-id="cf567-377">当预处理指令出现在多行输入元素中时，不会对其进行处理。</span><span class="sxs-lookup"><span data-stu-id="cf567-377">Pre-processing directives are not processed when they appear inside multi-line input elements.</span></span> <span data-ttu-id="cf567-378">例如，程序：</span><span class="sxs-lookup"><span data-stu-id="cf567-378">For example, the program:</span></span>
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
<span data-ttu-id="cf567-379">输出结果为：</span><span class="sxs-lookup"><span data-stu-id="cf567-379">results in the output:</span></span>
```console
hello,
#if Debug
        world
#else
        Nebraska
#endif
```

<span data-ttu-id="cf567-380">在特殊情况下，处理的预处理指令集可能取决于*pp_expression*的计算。</span><span class="sxs-lookup"><span data-stu-id="cf567-380">In peculiar cases, the set of pre-processing directives that is processed might depend on the evaluation of the *pp_expression*.</span></span> <span data-ttu-id="cf567-381">示例：</span><span class="sxs-lookup"><span data-stu-id="cf567-381">The example:</span></span>
```csharp
#if X
    /*
#else
    /* */ class Q { }
#endif
```
<span data-ttu-id="cf567-382">`class` `Q` `{` `}` 不管是否 `X` 定义了，始终会生成相同的令牌流（）。</span><span class="sxs-lookup"><span data-stu-id="cf567-382">always produces the same token stream (`class` `Q` `{` `}`), regardless of whether or not `X` is defined.</span></span> <span data-ttu-id="cf567-383">如果 `X` 定义了，则仅处理的指令为 `#if` 和 `#endif` ，因为有多行注释。</span><span class="sxs-lookup"><span data-stu-id="cf567-383">If `X` is defined, the only processed directives are `#if` and `#endif`, due to the multi-line comment.</span></span> <span data-ttu-id="cf567-384">如果 `X` 未定义，则三个指令（ `#if` 、 `#else` 、 `#endif` ）是指令集的一部分。</span><span class="sxs-lookup"><span data-stu-id="cf567-384">If `X` is undefined, then three directives (`#if`, `#else`, `#endif`) are part of the directive set.</span></span>

### <a name="diagnostic-directives"></a><span data-ttu-id="cf567-385">诊断指令</span><span class="sxs-lookup"><span data-stu-id="cf567-385">Diagnostic directives</span></span>

<span data-ttu-id="cf567-386">诊断指令用于显式生成错误和警告消息，其报告方式与其他编译时错误和警告的方式相同。</span><span class="sxs-lookup"><span data-stu-id="cf567-386">The diagnostic directives are used to explicitly generate error and warning messages that are reported in the same way as other compile-time errors and warnings.</span></span>

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

<span data-ttu-id="cf567-387">示例：</span><span class="sxs-lookup"><span data-stu-id="cf567-387">The example:</span></span>
```csharp
#warning Code review needed before check-in

#if Debug && Retail
    #error A build can't be both debug and retail
#endif

class Test {...}
```
<span data-ttu-id="cf567-388">始终产生警告（"签入前需要代码评审"），并且如果同时定义了条件符号和，则生成编译时错误（"A build 不能同时为调试和零售"） `Debug` `Retail` 。</span><span class="sxs-lookup"><span data-stu-id="cf567-388">always produces a warning ("Code review needed before check-in"), and produces a compile-time error ("A build can't be both debug and retail") if the conditional symbols `Debug` and `Retail` are both defined.</span></span> <span data-ttu-id="cf567-389">请注意， *pp_message*可以包含任意文本;具体而言，它不需要包含格式正确的标记，如单词中的单引号所示 `can't` 。</span><span class="sxs-lookup"><span data-stu-id="cf567-389">Note that a *pp_message* can contain arbitrary text; specifically, it need not contain well-formed tokens, as shown by the single quote in the word `can't`.</span></span>

### <a name="region-directives"></a><span data-ttu-id="cf567-390">区域指令</span><span class="sxs-lookup"><span data-stu-id="cf567-390">Region directives</span></span>

<span data-ttu-id="cf567-391">区域指令用于显式标记源代码区域。</span><span class="sxs-lookup"><span data-stu-id="cf567-391">The region directives are used to explicitly mark regions of source code.</span></span>

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

<span data-ttu-id="cf567-392">无语义含义附加到区域;区域旨在供程序员或自动工具用来标记源代码的一部分。</span><span class="sxs-lookup"><span data-stu-id="cf567-392">No semantic meaning is attached to a region; regions are intended for use by the programmer or by automated tools to mark a section of source code.</span></span> <span data-ttu-id="cf567-393">或指令中指定的 `#region` 消息 `#endregion` 同样没有语义含义; 它仅用于标识区域。</span><span class="sxs-lookup"><span data-stu-id="cf567-393">The message specified in a `#region` or `#endregion` directive likewise has no semantic meaning; it merely serves to identify the region.</span></span> <span data-ttu-id="cf567-394">匹配 `#region` 的和 `#endregion` 指令可能具有不同的*pp_message*。</span><span class="sxs-lookup"><span data-stu-id="cf567-394">Matching `#region` and `#endregion` directives may have different *pp_message*s.</span></span>

<span data-ttu-id="cf567-395">区域的词法处理：</span><span class="sxs-lookup"><span data-stu-id="cf567-395">The lexical processing of a region:</span></span>
```csharp
#region
...
#endregion
```
<span data-ttu-id="cf567-396">完全对应于格式为的条件编译指令的词法处理：</span><span class="sxs-lookup"><span data-stu-id="cf567-396">corresponds exactly to the lexical processing of a conditional compilation directive of the form:</span></span>
```csharp
#if true
...
#endif
```

### <a name="line-directives"></a><span data-ttu-id="cf567-397">行指令</span><span class="sxs-lookup"><span data-stu-id="cf567-397">Line directives</span></span>

<span data-ttu-id="cf567-398">行指令可用于更改编译器在输出（如警告和错误）中报告的行号和源文件名，以及由调用方信息特性（[调用方信息特性](attributes.md#caller-info-attributes)）使用的行号。</span><span class="sxs-lookup"><span data-stu-id="cf567-398">Line directives may be used to alter the line numbers and source file names that are reported by the compiler in output such as warnings and errors, and that are used by caller info attributes ([Caller info attributes](attributes.md#caller-info-attributes)).</span></span>

<span data-ttu-id="cf567-399">行指令最常用于从其他某些文本输入生成 c # 源代码的元编程工具。</span><span class="sxs-lookup"><span data-stu-id="cf567-399">Line directives are most commonly used in meta-programming tools that generate C# source code from some other text input.</span></span>

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

<span data-ttu-id="cf567-400">当不 `#line` 存在任何指令时，编译器会在其输出中报告真实的行号和源文件名。</span><span class="sxs-lookup"><span data-stu-id="cf567-400">When no `#line` directives are present, the compiler reports true line numbers and source file names in its output.</span></span> <span data-ttu-id="cf567-401">当处理 `#line` 包含不是的*line_indicator*的指令时，编译器会将 `default` 指令后的行视为具有给定的行号（和文件名，如果指定）。</span><span class="sxs-lookup"><span data-stu-id="cf567-401">When processing a `#line` directive that includes a *line_indicator* that is not `default`, the compiler treats the line after the directive as having the given line number (and file name, if specified).</span></span>

<span data-ttu-id="cf567-402">`#line default`指令反转所有前面 #line 指令的作用。</span><span class="sxs-lookup"><span data-stu-id="cf567-402">A `#line default` directive reverses the effect of all preceding #line directives.</span></span> <span data-ttu-id="cf567-403">编译器会报告后续行的真实行信息，就像未 `#line` 处理过指令一样。</span><span class="sxs-lookup"><span data-stu-id="cf567-403">The compiler reports true line information for subsequent lines, precisely as if no `#line` directives had been processed.</span></span>

<span data-ttu-id="cf567-404">`#line hidden`指令对错误消息中报告的文件和行号没有影响，但会影响源级别调试。</span><span class="sxs-lookup"><span data-stu-id="cf567-404">A `#line hidden` directive has no effect on the file and line numbers reported in error messages, but does affect source level debugging.</span></span> <span data-ttu-id="cf567-405">调试时， `#line hidden` 指令和后续指令之间的所有行 `#line` （即 not `#line hidden` ）都没有行号信息。</span><span class="sxs-lookup"><span data-stu-id="cf567-405">When debugging, all lines between a `#line hidden` directive and the subsequent `#line` directive (that is not `#line hidden`) have no line number information.</span></span> <span data-ttu-id="cf567-406">单步执行调试器中的代码时，将完全跳过这些行。</span><span class="sxs-lookup"><span data-stu-id="cf567-406">When stepping through code in the debugger, these lines will be skipped entirely.</span></span>

<span data-ttu-id="cf567-407">请注意，在不处理转义字符的情况下， *file_name*与正则字符串文字不同;" `\` " 字符只是在*file_name*中指定普通反斜杠字符。</span><span class="sxs-lookup"><span data-stu-id="cf567-407">Note that a *file_name* differs from a regular string literal in that escape characters are not processed; the "`\`" character simply designates an ordinary backslash character within a *file_name*.</span></span>

### <a name="pragma-directives"></a><span data-ttu-id="cf567-408">Pragma 指令</span><span class="sxs-lookup"><span data-stu-id="cf567-408">Pragma directives</span></span>

<span data-ttu-id="cf567-409">`#pragma`预处理指令用于指定编译器的可选上下文信息。</span><span class="sxs-lookup"><span data-stu-id="cf567-409">The `#pragma` preprocessing directive is used to specify optional contextual information to the compiler.</span></span> <span data-ttu-id="cf567-410">指令中提供的信息 `#pragma` 永远不会更改程序语义。</span><span class="sxs-lookup"><span data-stu-id="cf567-410">The information supplied in a `#pragma` directive will never change program semantics.</span></span>

```antlr
pp_pragma
    : whitespace? '#' whitespace? 'pragma' whitespace pragma_body pp_new_line
    ;

pragma_body
    : pragma_warning_body
    ;
```

<span data-ttu-id="cf567-411">C # 提供了 `#pragma` 控制编译器警告的指令。</span><span class="sxs-lookup"><span data-stu-id="cf567-411">C# provides `#pragma` directives to control compiler warnings.</span></span> <span data-ttu-id="cf567-412">将来版本的语言可能包含其他 `#pragma` 指令。</span><span class="sxs-lookup"><span data-stu-id="cf567-412">Future versions of the language may include additional `#pragma` directives.</span></span> <span data-ttu-id="cf567-413">为了确保与其他 c # 编译器的互操作性，Microsoft c # 编译器不会发出未知指令的编译错误 `#pragma` ; 因此，此类指令将生成警告。</span><span class="sxs-lookup"><span data-stu-id="cf567-413">To ensure interoperability with other C# compilers, the Microsoft C# compiler does not issue compilation errors for unknown `#pragma` directives; such directives do however generate warnings.</span></span>

#### <a name="pragma-warning"></a><span data-ttu-id="cf567-414">Pragma warning</span><span class="sxs-lookup"><span data-stu-id="cf567-414">Pragma warning</span></span>

<span data-ttu-id="cf567-415">`#pragma warning`指令用于在编译后续程序文本期间禁用或还原所有或一组特定的警告消息。</span><span class="sxs-lookup"><span data-stu-id="cf567-415">The `#pragma warning` directive is used to disable or restore all or a particular set of warning messages during compilation of the subsequent program text.</span></span>

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

<span data-ttu-id="cf567-416">`#pragma warning`省略警告列表的指令将影响所有警告。</span><span class="sxs-lookup"><span data-stu-id="cf567-416">A `#pragma warning` directive that omits the warning list affects all warnings.</span></span> <span data-ttu-id="cf567-417">`#pragma warning`包含警告列表的指令只影响列表中指定的那些警告。</span><span class="sxs-lookup"><span data-stu-id="cf567-417">A `#pragma warning` directive that includes a warning list affects only those warnings that are specified in the list.</span></span>

<span data-ttu-id="cf567-418">`#pragma warning disable`指令禁用所有或给定的一组警告。</span><span class="sxs-lookup"><span data-stu-id="cf567-418">A `#pragma warning disable` directive disables all or the given set of warnings.</span></span>

<span data-ttu-id="cf567-419">`#pragma warning restore`指令将所有或给定的警告集还原到编译单元开头处生效的状态。</span><span class="sxs-lookup"><span data-stu-id="cf567-419">A `#pragma warning restore` directive restores all or the given set of warnings to the state that was in effect at the beginning of the compilation unit.</span></span> <span data-ttu-id="cf567-420">请注意，如果在外部禁用了特定警告，则 `#pragma warning restore` 将不会重新启用该警告。</span><span class="sxs-lookup"><span data-stu-id="cf567-420">Note that if a particular warning was disabled externally, a `#pragma warning restore` (whether for all or the specific warning) will not re-enable that warning.</span></span>

<span data-ttu-id="cf567-421">下面的示例演示如何使用 `#pragma warning` Microsoft c # 编译器中的警告编号来暂时禁用引用过时成员时所报告的警告。</span><span class="sxs-lookup"><span data-stu-id="cf567-421">The following example shows use of `#pragma warning` to temporarily disable the warning reported when obsoleted members are referenced, using the warning number from the Microsoft C# compiler.</span></span>
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

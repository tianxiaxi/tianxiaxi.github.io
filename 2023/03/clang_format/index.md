# Clang format for C/C++/Java/JavaScript/Object-C


<a name="nhy9fv"></a>
# Clang-format简介
clang-format是一个代码格式化的工具，支持C/C++/Java/JavaScript/Objective-C/Protobuf等多种语言，并提供[LLVM](http://llvm.org/docs/CodingStandards.html)、[Google](http://google-styleguide.googlecode.com/svn/trunk/cppguide.xml)、[Visual Studio](https://code.visualstudio.com/docs/languages/cpp)（仅VSCode支持）、[Chromium](http://www.chromium.org/developers/coding-style)、[Mozilla](https://developer.mozilla.org/en-US/docs/Developer_Guide/Coding_Style)、[WebKit](http://www.webkit.org/coding/coding-style.html)等多种默认代码规范支持。

<a name="e0lebo"></a>
## Usage
```bash
$ clang-format -help
OVERVIEW: A tool to format C/C++/Java/JavaScript/Objective-C/Protobuf code.

If no arguments are specified, it formats the code from standard input
and writes the result to the standard output.
If <file>s are given, it reformats the files. If -i is specified
together with <file>s, the files are edited in-place. Otherwise, the
result is written to the standard output.

USAGE: clang-format [options] [<file> ...]

OPTIONS:

Clang-format options:

  -assume-filename=<string> - When reading from stdin, clang-format assumes this
                              filename to look for a style config file (with
                              -style=file) and to determine the language.
  -cursor=<uint>            - The position of the cursor when invoking
                              clang-format from an editor integration
  -dump-config              - Dump configuration options to stdout and exit.
                              Can be used with -style option.
  -fallback-style=<string>  - The name of the predefined style used as a
                              fallback in case clang-format is invoked with
                              -style=file, but can not find the .clang-format
                              file to use.
                              Use -fallback-style=none to skip formatting.
  -i                        - Inplace edit <file>s, if specified.
  -length=<uint>            - Format a range of this length (in bytes).
                              Multiple ranges can be formatted by specifying
                              several -offset and -length pairs.
                              When only a single -offset is specified without
                              -length, clang-format will format up to the end
                              of the file.
                              Can only be used with one input file.
  -lines=<string>           - <start line>:<end line> - format a range of
                              lines (both 1-based).
                              Multiple ranges can be formatted by specifying
                              several -lines arguments.
                              Can't be used with -offset and -length.
                              Can only be used with one input file.
  -offset=<uint>            - Format a range starting at this byte offset.
                              Multiple ranges can be formatted by specifying
                              several -offset and -length pairs.
                              Can only be used with one input file.
  -output-replacements-xml  - Output replacements as XML.
  -sort-includes            - Sort touched include lines
  -style=<string>           - Coding style, currently supports:
                                LLVM, Google, Chromium, Mozilla, WebKit.
                              Use -style=file to load style configuration from
                              .clang-format file located in one of the parent
                              directories of the source file (or current
                              directory for stdin).
                              Use -style="{key: value, ...}" to set specific
                              parameters, e.g.:
                                -style="{BasedOnStyle: llvm, IndentWidth: 8}"
  -verbose                  - If set, shows the list of processed files

Generic Options:

  -help                     - Display available options (-help-hidden for more)
  -help-list                - Display list of available options (-help-list-hidden for more)
  -version                  - Display the version of this program
```

- 创建.clang-format文件

```bash
clang-format -style=google -dump-config > .clang-format
```

<a name="fqfwgw"></a>
## Clang-Format配置介绍
Clang-Format 可以通过 -style="{key: value, ...}" 直接指定命令行参数的配置，也可以通过 -style=file 指定代码格式化配置文件；也可以从工作目录中读取`.clang-format` 或 `_clang-format`代码格式化配置文件。

`.clang-format`是一个YAML格式的配置文件：
```yaml
key1: value1
key2: value2
# A comment.
...
```

`.clang-format`文件可以同时配置多种语言的格式化配置参数，文件前面未指定的配置项为默认配置项，对所有语言都有效，但指定语言下相同的配置项将覆盖默认的配置项。clang-format工具可以自动识别代码文件的语言类型，也可以通过-assume-filename=指定语言。多语言的配置文件示例如下：
```yaml
---
# We'll use defaults from the LLVM style, but with 4 columns indentation.
BasedOnStyle: LLVM
IndentWidth: 4
---
Language: Cpp
# Force pointers to the type for C++.
DerivePointerAlignment: false
PointerAlignment: Left
---
Language: JavaScript
# Use 100 columns for JS.
ColumnLimit: 100
---
Language: Proto
# Don't format .proto files.
DisableFormat: true
...
```

当不期望clang-format格式化某一段代码时，可以采用下面的注释：
```cpp
int formatted_code;
// clang-format off
    void    unformatted_code  ;
// clang-format on
void formatted_code_again;
```
**注：**

- `// clang-format on` or `/* clang-format on */`等效

- `// clang-format off` or `/* clang-format off */`等效


<a name="eqwgsn"></a>
### Clang-Format 可选配置列表 (常用)

- **BasedOnStyle**_ (string）_

> 指定标准的基础代码规范式样，取值包括 [LLVM](http://llvm.org/docs/CodingStandards.html)、[Google](http://google-styleguide.googlecode.com/svn/trunk/cppguide.xml)、[Chromium](http://www.chromium.org/developers/coding-style)、[Mozilla](https://developer.mozilla.org/en-US/docs/Developer_Guide/Coding_Style)、[WebKit](http://www.webkit.org/coding/coding-style.html)等

- **AccessModifierOffset**_ (int)_

> 访问权限关键字的缩进，如public

- **AlignAfterOpenBracket**

> 括号中的参数是否需要对齐

- **AlignConsecutiveAssignments **_(bool)_

> 赋值操作时是否需要对齐，如当设置为true时，

> ```cpp
int aaaa = 12;
int b    = 23;
int ccc  = 23;
```

- **AlignConsecutiveDeclarations **_(bool)_

> 变量声明时是否需要对齐，如当设置为true时

> ```cpp
int         aaaa = 12;
float       b = 23;
std::string ccc = 23;
```

- **AlignEscapedNewlines**

> 使用backslashes(\)换行时(\)的设置方式，包括不对齐、左对齐、右对齐

- **AlignOperands **_(bool)_
> 表达式或者语句拆分为多行时的是否需要对齐
> ```cpp
int aaa = bbbbbbbbbbbbbbb +
          ccccccccccccccc;
```


- **AlignTrailingComments **_(bool)_

> 注释是否需要对齐

- **AllowAllParametersOfDeclarationOnNextLine **_(bool)_

> 如何函数声明时包含多行，每个参数是否需要单独列一行

- **AllowShortBlocksOnASingleLine **_(bool)_

> 简单语句是否需要写在一行。 E.g., this allows `if (a) { return; }` to be put on a single line.

- **AllowShortCaseLabelsOnASingleLine **_(bool)_

> case标签是否需要保持在一行

```cpp
// true:                                // false:
switch (a) {                    vs.     switch (a) {
case 1: x = 1; break;                   case 1:
case 2: return;                           x = 1;
}                                         break;
                                        case 2:
                                          return;
                                        }
```

- **AllowShortFunctionsOnASingleLine**

> 短函数是否可以保持在一行

- **AllowShortIfStatementsOnASingleLine **_(bool)_

> 简单 if 语句是否可以保持在一行

- **AllowShortLoopsOnASingleLine **_(bool)_

> 简单循环语句是否可以保持在一行

- **AlwaysBreakAfterReturnType**

> 数据返回值的可选格式

- **AlwaysBreakTemplateDeclarations**

> 模板声明时的换行式样

- **BinPackArguments **_(bool)_

> 函数调用时参数放在一行还是每个参数一行

- **BinPackParameters **_(bool)_

> 函数声明或定义时参数放在一行还是每个参数一行

- **BraceWrapping / BreakBeforeBraces**

> 大括号的使用方式

- **BreakBeforeTernaryOperators **_(bool)_

> 三元操作符时是否需要换行

```cpp
true:
veryVeryVeryVeryVeryVeryVeryVeryVeryVeryVeryLongDescription
    ? firstValue
    : SecondValueVeryVeryVeryVeryLong;

false:
veryVeryVeryVeryVeryVeryVeryVeryVeryVeryVeryLongDescription ?
    firstValue :
    SecondValueVeryVeryVeryVeryLong;
```

- **BreakConstructorInitializers**

> 构造函数成员初始化列表的的式样配置

- **BreakInheritanceList**

> 类多重继承时父类的式样配置

- **BreakStringLiterals **_(bool)_

> 是否允许对字符串进行格式化

- **ColumnLimit **_(unsigned)_

> The column limit.
> 
> A column limit of `0` means that there is no column limit. In this case, clang-format will respect the input’s line breaking decisions within statements unless they contradict other rules.

- **CompactNamespaces **_(bool)_

> 是否允许namespace打包到一行，或者每个namespace分行

```cpp
true:
namespace Foo { namespace Bar {
}}

false:
namespace Foo {
namespace Bar {
}
}
```

- **ConstructorInitializerAllOnOneLineOrOnePerLine**

> If the constructor initializers don’t fit on a line, put each initializer on its own line.

- **ConstructorInitializerIndentWidth** (`unsigned`)

> 构造函数成员初始化列表的缩进数量

- **ContinuationIndentWidth** (`unsigned`)

> 行缩进数量

- **Cpp11BracedListStyle** (`bool`)

> If `true`, format braced lists as best suited for C++11 braced lists.

```cpp
true:                                  false:
vector<int> x{1, 2, 3, 4};     vs.     vector<int> x{ 1, 2, 3, 4 };
vector<T> x{{}, {}, {}, {}};           vector<T> x{ {}, {}, {}, {} };
f(MyMap[{composite, key}]);            f(MyMap[{ composite, key }]);
new int[3]{1, 2, 3};                   new int[3]{ 1, 2, 3 };
```

- **DerivePointerAlignment** (`bool`)

> `&` and `*`的对齐方式

- **DisableFormat** (`bool`)

> 是否禁止代码格式化

- **FixNamespaceComments** (`bool`)

> 是否自动添加namespace的结束注释

```cpp
true:                                  false:
namespace a {                  vs.     namespace a {
foo();                                 foo();
} // namespace a;                      }
```

- **IncludeBlocks** (`IncludeBlocksStyle`)

> Dependent on the value, multiple `#include` blocks can be sorted as one and divided based on category.

- **IndentCaseLabels** (`bool`)

> case 语句是否需要缩进

```cpp
false:                                 true:
switch (fool) {                vs.     switch (fool) {
case 1:                                  case 1:
  bar();                                   bar();
  break;                                   break;
default:                                 default:
  plop();                                  plop();
}                                      }
```

- **IndentPPDirectives** (`PPDirectiveIndentStyle`)

> 预处理指令或宏的缩进方式

- **IndentWidth** (`unsigned`)

> 行缩进的字符数量

- **KeepEmptyLinesAtTheStartOfBlocks** (`bool`)

> 是否需要在代码段前保持空行

```cpp
true:                                  false:
if (foo) {                     vs.     if (foo) {
                                         bar();
  bar();                               }
}
```

- **Language** (`LanguageKind`)

> 指定语言类型

- `LK_None` (in configuration: `None`) Do not use.

- `LK_Cpp` (in configuration: `Cpp`) Should be used for C, C++.

- `LK_Java` (in configuration: `Java`) Should be used for Java.

- `LK_JavaScript` (in configuration: `JavaScript`) Should be used for JavaScript.

- `LK_ObjC` (in configuration: `ObjC`) Should be used for Objective-C, Objective-C++.

- `LK_Proto` (in configuration: `Proto`) Should be used for Protocol Buffers ([https://developers.google.com/protocol-buffers/](https://developers.google.com/protocol-buffers/)).

- `LK_TableGen` (in configuration: `TableGen`) Should be used for TableGen code.

- `LK_TextProto` (in configuration: `TextProto`) Should be used for Protocol Buffer messages in text format ([https://developers.google.com/protocol-buffers/](https://developers.google.com/protocol-buffers/)).

- **MaxEmptyLinesToKeep** (`unsigned`)

> The maximum number of consecutive empty lines to keep.

```cpp
MaxEmptyLinesToKeep: 1         vs.     MaxEmptyLinesToKeep: 0
int f() {                              int f() {
  int = 1;                                 int i = 1;
                                           i = foo();
  i = foo();                               return i;
                                       }
  return i;
}
```

- **NamespaceIndentation** (`NamespaceIndentationKind`)

> namespace 的缩进方式

- **PointerAlignment** (`PointerAlignmentStyle`)

> 指针与引用的对齐方式

- **ReflowComments** (`bool`)

> If `true`, clang-format will attempt to re-flow comments.

```cpp
false:
// veryVeryVeryVeryVeryVeryVeryVeryVeryVeryVeryLongComment with plenty of information
/* second veryVeryVeryVeryVeryVeryVeryVeryVeryVeryVeryLongComment with plenty of information */

true:
// veryVeryVeryVeryVeryVeryVeryVeryVeryVeryVeryLongComment with plenty of
// information
/* second veryVeryVeryVeryVeryVeryVeryVeryVeryVeryVeryLongComment with plenty of
 * information */
```

- **SortIncludes** (`bool`)

> 是否对 #include 的头文件进行排序

- **SortUsingDeclarations** (`bool`)

> 是否对声明进行排序

```cpp
false:                                 true:
using std::cout;               vs.     using std::cin;
using std::cin;                        using std::cout;
```

- **SpaceAfterCStyleCast** (`bool`)

> 是否对 C 语言风格的类型转换添加空格

```cpp
true:                                  false:
(int) i;                       vs.     (int)i;
```

- **SpaceAfterTemplateKeyword** (`bool`)

> template 关键字后是否需要添加空格

```cpp
true:                                  false:
template <int> void foo();     vs.     template<int> void foo();
```

- **SpaceBeforeAssignmentOperators** (`bool`)

> 赋值操作符前是否需要添加空格

```cpp
true:                                  false:
int a = 5;                     vs.     int a=5;
a += 42                                a+=42;
```

- **SpaceBeforeCpp11BracedList** (`bool`)

> C11风格的对象初始化时是否需要添加空格

```cpp
true:                                  false:
Foo foo { bar };               vs.     Foo foo{ bar };
Foo {};                                Foo{};
vector<int> { 1, 2, 3 };               vector<int>{ 1, 2, 3 };
new int[3] { 1, 2, 3 };                new int[3]{ 1, 2, 3 };
```

- **SpaceBeforeCtorInitializerColon** (`bool`)

> 构造函数成员初始化列表的冒号(:)前是否需要添加空格

```cpp
true:                                  false:
Foo::Foo() : a(a) {}                   Foo::Foo(): a(a) {}
```

- **SpaceBeforeInheritanceColon** (`bool`)

> 类继承时是否需要在冒号(:)前添加括号

```cpp
true:                                  false:
class Foo : Bar {}             vs.     class Foo: Bar {}
```

- **SpaceBeforeParens** (`SpaceBeforeParensOptions`)

> 括号前后的空格处理

- **SpaceBeforeRangeBasedForLoopColon** (`bool`)

> If `false`, spaces will be removed before range-based for loop colon.

```cpp
true:                                  false:
for (auto v : values) {}       vs.     for(auto v: values) {}
```

- **SpaceInEmptyParentheses** (`bool`)

> 空括号（）中是否需要添加空格

```cpp
true:                                false:
void f( ) {                    vs.   void f() {
  int x[] = {foo( ), bar( )};          int x[] = {foo(), bar()};
  if (true) {                          if (true) {
    f( );                                f();
  }                                    }
}                                    }
```

- **SpacesBeforeTrailingComments** (`unsigned`)

> 注释代码前需要添加的空格数量

```cpp
SpacesBeforeTrailingComments: 3
void f() {
  if (true) {   // foo1
    f();        // bar
  }             // foo
}
```

- **SpacesInAngles** (`bool`)

> <> 中是否要使用空格

```cpp
true:                                  false:
static_cast< int >(arg);       vs.     static_cast<int>(arg);
std::function< void(int) > fct;        std::function<void(int)> fct;
```

- **SpacesInCStyleCastParentheses** (`bool`)

> C语言风格的类型转换时是否需要在括号中添加空格

```cpp
true:                                  false:
x = ( int32 )y                 vs.     x = (int32)y
```

- **SpacesInContainerLiterals** (`bool`)

> 容器列表中的空格

```cpp
true:                                  false:
var arr = [ 1, 2, 3 ];         vs.     var arr = [1, 2, 3];
f({a : 1, b : 2, c : 3});              f({a: 1, b: 2, c: 3});
```

- **SpacesInParentheses** (`bool`)

> () 中的空格处理

```cpp
true:                                  false:
t f( Deleted & ) & = delete;   vs.     t f(Deleted &) & = delete;
```

- **SpacesInSquareBrackets** (`bool`)

> [与]中的空格处理

```cpp
true:                                  false:
int a[ 5 ];                    vs.     int a[5];
std::unique_ptr<int[]> foo() {} // Won't be affected
```

- **Standard** (`LanguageStandard`)

> C++标准的类型，Format compatible with this standard, e.g. use `A<A<int> >` instead of `A<A<int>>` for `LS_Cpp03`.

- `LS_Cpp03` (in configuration: `Cpp03`) Use C++03-compatible syntax.

- `LS_Cpp11` (in configuration: `Cpp11`) Use features of C++11, C++14 and C++1z (e.g. `A<A<int>>` instead of `A<A<int> >`).

- `LS_Auto` (in configuration: `Auto`) Automatic detection based on the input.

- **TabWidth** (`unsigned`)

> 设置tab stops的缩进量

- **UseTab** (`UseTabStyle`)

> 是否使用tab



<a name="p9ewlg"></a>
## .clang-format 配置文件示例
```yaml
# Style options please see https://clang.llvm.org/docs/ClangFormatStyleOptions.html

# The number of columns to use for indentation
IndentWidth: 4
---
Language:        Cpp
BasedOnStyle:  Google
# The extra indent or outdent of access modifiers, e.g. public
AccessModifierOffset: -1
# 括号中的参数是否需要对齐
AlignAfterOpenBracket: Align
# 连续的赋值语句是否需要在'='处对齐
AlignConsecutiveAssignments: true
# 连续声明的变更是否需要对齐
AlignConsecutiveDeclarations: false
# Options for aligning backslashes in escaped newlines
AlignEscapedNewlines: Left
# 操作数及三元操作符对齐
AlignOperands:   true
# 注释对齐
AlignTrailingComments: true
# 函数声明过长时是否每个参数作为一行处理
AllowAllParametersOfDeclarationOnNextLine: false
# Allows contracting simple braced statements to a single line
AllowShortBlocksOnASingleLine: false
# case关键字是否与语句放在一行
AllowShortCaseLabelsOnASingleLine: false
# 单语句或短函数是否可以放在一行
AllowShortFunctionsOnASingleLine: Inline
# IF 单语句代码是否可以放在一行
AllowShortIfStatementsOnASingleLine: true
# 单语句循环体是否可以与循环放在一行
AllowShortLoopsOnASingleLine: true
# AlwaysBreakAfterDefinitionReturnType 选项将被弃用
AlwaysBreakAfterDefinitionReturnType: None
# The function declaration return type breaking style to use
AlwaysBreakAfterReturnType: None
# 多行字符串是否需要另起一行
AlwaysBreakBeforeMultilineStrings: true
# 模板声明时是否需要另起一行
AlwaysBreakTemplateDeclarations: Yes
# 函数调用时参数打包对齐
BinPackArguments: true
# 函数声明或定义时是否需要参数打包对齐
BinPackParameters: false
# 大括号的处理行为
BraceWrapping:   
  AfterClass:      false
  AfterControlStatement: false
  AfterEnum:       false
  AfterFunction:   false
  AfterNamespace:  false
  AfterObjCDeclaration: false
  AfterStruct:     false
  AfterUnion:      false
  AfterExternBlock: false
  BeforeCatch:     false
  BeforeElse:      false
  IndentBraces:    false
  SplitEmptyFunction: true
  SplitEmptyRecord: true
  SplitEmptyNamespace: true
# The way to wrap binary operators
BreakBeforeBinaryOperators: None
# The brace breaking style to use
BreakBeforeBraces: Attach
BreakBeforeInheritanceComma: false
# The inheritance list style to use
BreakInheritanceList: AfterColon
# 三元操作号对齐
BreakBeforeTernaryOperators: true
BreakConstructorInitializersBeforeComma: false
BreakConstructorInitializers: BeforeColon
BreakAfterJavaFieldAnnotations: false
# Allow breaking string literals when formatting
BreakStringLiterals: true
# 允许每行字符最大长度
ColumnLimit:     120
CommentPragmas:  '^ IWYU pragma:'
# 是否允许多个namespace声明在同一行
CompactNamespaces: false
# If the constructor initializers don’t fit on a line, put each initializer on its own line
ConstructorInitializerAllOnOneLineOrOnePerLine: true
# The number of characters to use for indentation of constructor initializer lists as well as inheritance lists
ConstructorInitializerIndentWidth: 4
# Indent width for line continuations
ContinuationIndentWidth: 4
Cpp11BracedListStyle: true
DerivePointerAlignment: true
# 禁止代码格式化
DisableFormat:   false
ExperimentalAutoDetectBinPacking: false
# 是否自动添加namespace结束的注释
FixNamespaceComments: true
ForEachMacros:   
  - foreach
  - Q_FOREACH
  - BOOST_FOREACH
# Dependent on the value, multiple #include blocks can be sorted as one and divided based on category
IncludeBlocks:   Preserve
# Regular expressions denoting the different #include categories used for ordering #includes
IncludeCategories: 
  - Regex:           '^<ext/.*\.h>'
    Priority:        2
  - Regex:           '^<.*\.h>'
    Priority:        1
  - Regex:           '^<.*'
    Priority:        2
  - Regex:           '.*'
    Priority:        3
IncludeIsMainRegex: '([-_](test|unittest))?$'
# case关键字是否需要缩进
IndentCaseLabels: true
# 预处理指定或宏是否需要缩进
IndentPPDirectives: None
# 当函数声明的返回值与函数名不在一行时，函数名是否需要缩进
IndentWrappedFunctionNames: false
JavaScriptQuotes: Leave
JavaScriptWrapImports: true
# 是否在代码段前添加空格
KeepEmptyLinesAtTheStartOfBlocks: false
MacroBlockBegin: ''
MacroBlockEnd:   ''
# 允许最大的空行数量
MaxEmptyLinesToKeep: 1
# namespace中的子namespace与代码是否需要缩进
NamespaceIndentation: None
ObjCBinPackProtocolList: Never
ObjCBlockIndentWidth: 2
ObjCSpaceAfterProperty: false
ObjCSpaceBeforeProtocolList: true
PenaltyBreakAssignment: 2
PenaltyBreakBeforeFirstCallParameter: 1
PenaltyBreakComment: 300
PenaltyBreakFirstLessLess: 120
PenaltyBreakString: 1000
PenaltyBreakTemplateDeclaration: 10
PenaltyExcessCharacter: 1000000
# 函数允许的最大长度(行)
PenaltyReturnTypeOnItsOwnLine: 200
# 指针与引用的对齐方式
PointerAlignment: Left
# Defines hints for detecting supported languages code blocks in raw strings
RawStringFormats: 
  - Language:        Cpp
    Delimiters:      
      - cc
      - CC
      - cpp
      - Cpp
      - CPP
      - 'c++'
      - 'C++'
    CanonicalDelimiter: ''
    BasedOnStyle:    google
  - Language:        TextProto
    Delimiters:      
      - pb
      - PB
      - proto
      - PROTO
    EnclosingFunctions: 
      - EqualsProto
      - EquivToProto
      - PARSE_PARTIAL_TEXT_PROTO
      - PARSE_TEST_PROTO
      - PARSE_TEXT_PROTO
      - ParseTextOrDie
      - ParseTextProtoOrDie
    CanonicalDelimiter: ''
    BasedOnStyle:    google
# 是否对注释重新排列
ReflowComments:  true
# 是否对 #include 的头文件进行排序
SortIncludes:    true
# 是否对using的namespace排序
SortUsingDeclarations: true
# C语言风格的类型转换的变量前是否添加空格
SpaceAfterCStyleCast: false
# 模板关键字后是否需要添加空格
SpaceAfterTemplateKeyword: true
# 赋值操作符前否是否需要添加空格
SpaceBeforeAssignmentOperators: true
# C11风格的对象初始化是否需要添加空格
SpaceBeforeCpp11BracedList: true
# 成员初始化列表的冒号前是否需要添加空格
SpaceBeforeCtorInitializerColon: true
# 类继承时冒号前是否添加空格
SpaceBeforeInheritanceColon: true
# 圆括号前后是否要添加空格
SpaceBeforeParens: ControlStatements
# range-based的循环中的冒号(:)前是否添加空格
SpaceBeforeRangeBasedForLoopColon: true
# 空括号()中是否需要添加空格
SpaceInEmptyParentheses: false
# 行注释前需要添加的空格数量
SpacesBeforeTrailingComments: 2
# <>中是否需要添加空格
SpacesInAngles:  false
# 容器列表中是否需要添加空格
SpacesInContainerLiterals: true
# C语言风格的类型转换时()中是否需要添加空格
SpacesInCStyleCastParentheses: false
# ()中是否需要添加空格
SpacesInParentheses: false
# []中是否需要添加空格
SpacesInSquareBrackets: false
# 自动识别C++标准版本
Standard:        Auto
UseTab:          Never
...
```

<a name="nktaoo"></a>
# 编辑器支持Clang-format介绍
<a name="nklwgc"></a>
## VSCode Coding Formating插件
VSCode的C/C++插件使用Clang-Format支持源代码格式化。

<a name="xxpksc"></a>
### 编辑器设置
```
editor.formatOnSave - 保存文档时自动对代码格式化
editor.formatOnType - 当用户输入分号（;）时对代码进行格式化
```

**注：** 当对代码进行格式化时，VSCode优先检查当前工作空间是否存在.clang-format文件，如果存在则采用此文件进行格式化，否则采用默认的C_Cpp.clang_format_fallbackStyle进行格式化。同时可以通过C_Cpp.clang_format_path指定本机指定版本的clang-format工具代码默认的版本。

<a name="fagmnp"></a>
## VIM集成

- 请参考 [https://clang.llvm.org/docs/ClangFormat.html#vim-integration](https://clang.llvm.org/docs/ClangFormat.html#vim-integration)

<a name="wtgqan"></a>
## Emacs集成

- 请参考 [https://clang.llvm.org/docs/ClangFormat.html#emacs-integration](https://clang.llvm.org/docs/ClangFormat.html#emacs-integration)


<a name="0lwgiz"></a>
# 扩展阅读

- [VSCode Code formatting介绍](https://code.visualstudio.com/docs/languages/cpp)

- [Clang-Format 使用说明](https://clang.llvm.org/docs/ClangFormat.html)

- [Clang-Format Style Options](https://clang.llvm.org/docs/ClangFormatStyleOptions.html)




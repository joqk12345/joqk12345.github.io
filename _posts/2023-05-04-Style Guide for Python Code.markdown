---
layout:     post
title:      "Style Guide for Python Code"
subtitle:   "PEP 8 - Python代码的风格指南"
date:       2023-05-04 22:11:00
author:     "Galaxies"
header-img: "img/post-bg-2015.jpg"
catalog: true
tags:
    - Python Enhancement Proposals
    - Python
    - Foolish Consistency
---


>
>  Python enhancement Proposals : https://peps.python.org/pep-0008/#code-lay-out  的学习
>  花了四个小时的时间将这篇文章读完，感觉其实是省时间的，在写python代码的时候，不需要去各种参考其他代码的写法，按照这规范来就好了，值得多读的一篇规范提案，他更教会了我们在写代码需要考虑更多的是如何阅读代码、如何调用代码、如何维护代码、如何呈现错误……
> “Yeah It's on. ”
>  
>  

## A Foolish Consistency is the Hobgoblin of Little Minds
愚蠢的一致性是小脑袋里的恶棍

Guido的一个重要见解是，代码被阅读的次数远远多于被书写的次数。这里提供的指南是为了提高代码的可读性，并使其在广泛的Python代码中保持一致。正如PEP 20所说，"可读性很重要"。
风格指南是关于一致性。与本风格指南保持一致是很重要的。在一个项目中的一致性更为重要。一个模块或功能内的一致性是最重要的。
然而，要知道什么时候应该不一致--有时风格指南的建议就是不适用。当有疑问时，使用你的最佳判断。看看其他的例子，决定什么看起来最好。而且，不要犹豫，要问

特别是：不要为了遵守这个PEP而破坏了向后的兼容性!

其他一些忽略某条准则的好理由：

1. 当应用该准则会使代码的可读性降低时，即使是对于那些习惯于阅读遵循该PEP的代码的人来说。
2. 为了与周围的代码保持一致，这些代码也会破坏它（也许是出于历史原因）--尽管这也是一个清理别人的烂摊子的机会（真正的XP风格）。
3. 因为有问题的代码是在引入该准则之前的，而且没有其他理由要修改该代码。
4. 当代码需要与不支持风格指南所推荐的特性的旧版本的 Python 保持兼容时。

## Code Lay-out（代码布局）

### Indentation(缩进)
Use 4 spaces per indentation level.
每个缩进级别使用4个空格。

Continuation lines should align wrapped elements either vertically using Python’s implicit line joining inside parentheses, brackets and braces, or using a hanging indent [1]. When using a hanging indent the following should be considered; there should be no arguments on the first line and further indentation should be used to clearly distinguish itself as a continuation line:

延续行的缩进应该使用Python的小括号、大括号和圆括号内的隐式连线，或者使用悬挂缩进[1]，将包裹的元素垂直对齐。
当使用悬挂缩进时，应该考虑；
  1. 第一行不应该有参数；
  2. 并且应该使用进一步的缩进来明确区分自己是一个延续行：

The closing brace/bracket/parenthesis on multiline constructs may either line up under the first non-whitespace character of the last line of list, as in:
多行结构上的结尾大括号/小括号/大括号可以排在最后一行列表的第一个非空格字符下，如：：
```
my_list = [
    1, 2, 3,
    4, 5, 6,
    ]
result = some_function_that_takes_arguments(
    'a', 'b', 'c',
    'd', 'e', 'f',
    )
```
or it may be lined up under the first character of the line that starts the multiline construct, as in:

或者它可以排在开始多行结构的一行的第一个字符下面，如：：
```
my_list = [
    1, 2, 3,
    4, 5, 6,
]
result = some_function_that_takes_arguments(
    'a', 'b', 'c',
    'd', 'e', 'f',
)
```

### Tabs or Spaces? 

空格是首选的缩进方法。
标签应该只用于与已经用标签缩进的代码保持一致。
Python不允许将制表符和空格混合在一起缩进。


### Maximum Line Length

将所有行数限制在最多79个字符。
对于结构限制较少的流动的长文本块（文档串或注释），行长应限制在72个字符。
限制所需的编辑器窗口宽度，可以让几个文件并排打开，在使用将两个版本呈现在相邻列的代码审查工具时，效果很好。

大多数工具中的默认包装破坏了代码的视觉结构，使其更加难以理解。选择这些限制是为了避免在窗口宽度设置为80的编辑器中出现包行，即使该工具在包行时在最后一列放置一个标记字形。一些基于网络的工具可能根本不提供动态行包装。

有些团队强烈希望有更长的行长。对于完全或主要由一个团队维护的代码，如果能在这个问题上达成一致，可以将行长限制增加到99个字符，但注释和文档字符串仍以72个字符包裹。

### Should a Line Break Before or After a Binary Operator?

几十年来，推荐的风格是在二进制运算符之后断开。但这在两个方面会损害可读性：运算符往往分散在屏幕的不同列中，而且每个运算符都被从其操作数上移到了前一行。在这里，眼睛不得不做额外的工作来分辨哪些项目是加法，哪些是减法：
为了解决这个可读性问题，数学家和他们的出版商遵循相反的惯例。唐纳德-克努斯在他的《计算机与排版》系列中解释了这一传统规则："虽然段落内的公式总是在二元运算和关系之后断开，但显示的公式总是在二元运算之前断开。

```
# Correct:
# easy to match operators with operands
income = (gross_wages
          + taxable_interest
          + (dividends - qualified_dividends)
          - ira_deduction
          - student_loan_interest)
```
在Python代码中，允许在二进制运算符之前或之后断开，只要惯例在本地是一致的。对于新的代码，建议采用Knuth的风格。

### Blank Lines(空行)

用两行空行包围顶级函数和类的定义。
类内的方法定义被一个空行所包围。
额外的空行可以被用来分隔相关的函数组（少用）。在一堆相关的单行字之间可以省略空行（例如，一组假的实现）。
在函数中少用空行，以表示逻辑部分。
Python 接受 control-L (即 ^L) 换页字符作为空白；许多工具将这些字符视为页面分隔符，所以你可以用它们来分隔文件中相关部分的页面。注意，一些编辑器和基于网络的代码查看器可能不会将 control-L 识别为换页字符，而会在其位置上显示另一个字形。

### Source File Encoding(源文件编码)

核心Python发行版中的代码应该总是使用UTF-8，并且不应该有编码声明。
在标准库中，非UTF-8编码应该只用于测试目的。少用非ASCII字符，最好只用来表示地方和人名。如果使用非ASCII字符作为数据，应避免像z̯̯͡a̧͎̺l̡͓̫g̹̲o̡̼̘和字节顺序标记等嘈杂的Unicode字符。
Python标准库中的所有标识符必须使用纯ASCII的标识符，并且在可行的情况下应该使用英文单词(在很多情况下，使用的缩写和技术术语都不是英文)。
我们鼓励有全球受众的开源项目采取类似的政策。

### Imports

* 导入包通常应在单独的行上：

* 导入包的生命总是放在文件的顶部，就在任何模块注释和文件说明之后，在模块全局和常量之前。
  1. 标准库导入。
  2. 相关的第三方导入。
  3. 本地应用程序/库的特定导入。 
  
* 推荐使用绝对导入，因为它们通常更具可读性，而且如果导入系统配置不正确（例如，当包内的目录最终出现在sys.path上时），它们往往表现得更好（或者至少给出更好的错误信息）：
  * 然而，显式相对导入是绝对导入的一个可接受的替代方法，特别是在处理复杂的包布局时，使用绝对导入会造成不必要的冗长：

* 当从一个含类的模块中导入一个类时，通常可以这样拼写

```
from myclass import MyClass
from foo.bar.yourclass import YourClass
```

* 应该避免通配符导入 (从 <module> 导入 *)，因为它们使名字空间中存在的名字不明确，使读者和许多自动化工具都感到困惑。通配符导入有一个可辩护的用例，那就是重新发布一个内部接口作为公共 API 的一部分 (例如，用一个可选的加速器模块的定义覆盖一个纯 Python 接口的实现，具体哪些定义将被覆盖并不事先知道)。

### Module Level Dunder Names(模块级的Dunder名称)

模块级的 "dunders"(即带有两个前导和两个尾部下划线的名字)，如 __all__, __author__, __version__，等等，应该放在模块文档串之后，但在任何导入语句之前，除了来自 __future__ 的导入。Python 规定未来的导入必须在模块中出现在任何其他代码之前，除了文档串：

```
"""This is the example module.

This module does stuff.
"""

from __future__ import barry_as_FLUFL

__all__ = ['a', 'b', 'c']
__version__ = '0.1'
__author__ = 'Cardinal Biggles'

import os
import sys
```

## String Quotes(字符串引文)

在 Python 中，单引号字符串和双引号字符串是一样的。本 PEP 并没有对此提出建议。选择一个规则并坚持下去。然而，当一个字符串包含单引号或双引号字符时，使用另一个，以避免字符串中的反斜线。它可以提高可读性。
对于三引号的字符串，总是使用双引号字符，以便与PEP 257中的文档字符串约定一致。

## Whitespace in Expressions and Statements(表达式和声明语句中的空格)

### Pet Peeves( 讨厌的人)
在以下情况下要避免不相干的空白：
* 紧靠小括号、大括号或圆括号内：

```
# Correct:
spam(ham[1], {eggs: 2})

# Wrong:
spam( ham[ 1 ], { eggs: 2 } )
```

* 在尾部的逗号和后面的闭合小括号之间
* 紧接在逗号、分号或冒号之前：
* 然而，在一个切片中，冒号的作用就像一个二元运算符，两边应该有相等的数量（把它当作优先级最低的运算符）。在一个扩展的切片中，两个冒号都必须应用相同数量的间距。例外：当一个切片参数被省略时，空格被省略了：
* 紧挨着开始一个函数调用的参数列表的开放括号之前：
* 在一个赋值（或其他）运算符周围有一个以上的空格，以使其与另一个运算符对齐。
  
### Other Recommendations
* 避免在任何地方出现尾部空白。因为它通常是不可见的，它可能会引起混淆：例如，一个反斜杠后面跟着一个空格和一个换行符并不算作一个续行标记。一些编辑器不保留它，许多项目（如CPython本身）有预提交钩子，拒绝它。
* 在这些二进制运算符的两边总是用一个空格包围：赋值（=），增强赋值（+=，-=等），比较（==，<，>，！=，<>，<=，>=，在，不在，是，不是），布尔运算（和，或，不）。
* 如果使用了不同优先级的运算符，可以考虑在优先级最低的运算符周围添加空白。使用你自己的判断；然而，永远不要使用一个以上的空格，并且在二元运算符的两边总是有相同数量的空格：
* 函数注释应该使用正常的冒号规则，如果有的话，在->箭头周围总是有空格。(关于函数注释的更多信息，请参见下面的函数注释）：
* 当用于表示一个关键字参数时，或用于表示一个未注释的函数参数的默认值时，不要在=号周围使用空格：
* 通常不鼓励使用复合语句（同一行的多个语句）：
* 虽然有时把一个带有小主体的if/for/while放在同一行是可以的，但对于多条款的语句，千万不要这样做。也要避免折叠这样的长行!


## When to Use Trailing Commas
* 后面的逗号通常是可选的，但在制作一个元素的元组时，它们是必须的。为了清楚起见，建议用（技术上多余的）小括号包围后者：
* 当尾随逗号是多余的，当使用版本控制系统时，当一个值、参数或导入项目的列表预计会随着时间的推移而扩展时，尾随逗号往往是有帮助的。其模式是将每个值（等）单独放在一行，总是加上一个尾部逗号，然后在下一行加上封闭的小括号/括号/括号。然而，将尾部逗号与闭合分隔符放在同一行中是没有意义的（除了上述单子图元的情况）：

## Comments
* 与代码相抵触的注释比没有注释更糟糕。当代码发生变化时，一定要优先保持注释的更新！
* 注释应该是完整的句子。第一个词应该大写，除非它是一个以小写字母开头的标识符（永远不要改变标识符的大小写！）。
* 块状注释一般由一个或多个完整的句子组成的段落组成，每个句子以句号结束。
* 在多句评论中，除了最后一句话之外，在句子结束的句号后应使用一个或两个空格。
* 确保你的评论清晰明了，容易被其他使用你所写语言的人理解。
* 来自非英语国家的Python编码员：请用英语写评论，除非你有120%的把握，代码永远不会被不懂你语言的人阅读。

### Block Comments
块状注释通常适用于它们后面的一些（或全部）代码，并缩进到与该代码相同的水平。区块注释的每一行都以#和一个空格开始（除非是注释内的缩进文本）。
块状注释内的段落由包含一个单行的#
### Inline Comments
尽量少用内联评论。
内联注释是指与语句在同一行的注释。内联评论应该与语句至少隔开两个空格。它们应该以#和一个空格开始。
内联评论是不必要的，事实上，如果它们说明了明显的问题，就会分散注意力。不要这样做：
但是有时候他又是有用的
```
x = x + 1                 # Compensate for border
```

### Documentation Strings
* 编写好的文档字符串（又称 "docstrings"）的惯例在PEP 257中得到了永生。
* 为所有的公共模块、函数、类和方法编写文档说明。对于非公开的方法，不需要写文件说明，但你应该有一个注释来描述该方法的作用。这个注释应该出现在def行的后面。
* 对于单行的文件串，请将结尾的""保持在同一行。

## Naming Conventions(命名规范)

Python库的命名规则有点乱、 所以，我们永远不会让这个问题完全一致--尽管如此，这里是目前推荐的命名标准。 新的模块和包（包括第三方框架）应该按照这些标准来编写，但如果现有的库有不同的风格，内部的一致性是首选。

### Overriding Principle(最重要的原则)

作为API的公共部分，对用户可见的名称应该遵循反映用法而不是实现的惯例。（Names that are visible to the user as public parts of the API should follow conventions that reflect usage rather than implementation.）

comment： 命名首先服务的用户是调用者，而不是实现者。

### Descriptive: Naming Styles(描述性的： 命名方式)

有很多不同的命名方式。能够认识到使用的是什么命名方式，这有助于独立于它们的用途。
通常有以下几种命名方式的区分：
  * b (single lowercase letter)
  * B (single uppercase letter)
  * lowercase
  * lower_case_with_underscores
  * UPPERCASE
  * UPPER_CASE_WITH_UNDERSCORES
  * CapitalizedWords (or CapWords, or CamelCase – so named because of the bumpy look of its letters [4]). This is also sometimes known as StudlyCaps.
    * note: 当在CapWords中使用首字母时，要将首字母的所有字母大写。因此，HTTPServerError比HttpServerError好。
  * mixedCase (differs from CapitalizedWords by initial lowercase character!)
  * Capitalized_Words_With_Underscores(ugly!)
还有一种风格是使用简短的独特前缀，将相关的名字组合在一起。这在Python中用得不多，但为了完整起见，还是要提到。 例如，os.stat()函数返回一个元组，其项目传统上有st_mode、st_size、st_mtime等名称。(这样做是为了强调与POSIX系统调用结构字段的对应关系，这有助于熟悉该结构的程序员）。
X11库在其所有的公共函数中都使用了一个领先的X。在Python中，这种风格通常被认为是不必要的，因为属性和方法名的前缀是一个对象，而函数名的前缀是一个模块名。
此外，还可以识别以下使用前导或尾部下划线的特殊形式（这些通常可以与任何大小写惯例相结合）：
* _single_leading_underscore: 弱的 "内部使用 "指标。例如，from M import * 不会导入名称以下划线开头的对象。
* single_trailing_underscore_:按惯例使用，以避免与Python关键字冲突。
  ```
  tkinter.Toplevel(master, class_='ClassName')
  ```
* __double_leading_underscore: when naming a class attribute, invokes name mangling (inside class FooBar, __boo becomes _FooBar__boo; see below)
* __double_leading_and_trailing_underscore__: 存在在用户控制的命名空间中的 "神奇 "对象或属性。例如： __init__, __import__ or __file__. 不要发明他，只需要按照文档中注明的使用就行.

### Prescriptive: Naming Conventions (规定性的： 命名规则)
1. Names to Avoid
  不要使用字符'l'（小写字母el）、'O'（大写字母oh）或'I'（大写字母eye）作为单字符变量名。
  在某些字体中，这些字符与数字1和0无法区分。当想使用'l'时，请使用'L'代替。
2. ASCII Compatibility（ASCII兼容性）
  标准库中使用的标识符必须是ASCII兼容的，如PEP 3131的政策部分所述。
3. Package and Module Names
   模块应该有简短的、全小写的名字。如果能提高可读性，可以在模块名称中使用下划线。Python 包也应该有短的、全小写的名字，尽管不鼓励使用下划线。
   当一个用 C 或 C++ 编写的扩展模块有一个附带的 Python 模块，它提供了一个更高级别的（例如更多的面向对象的）接口，C/C++ 模块有一个前导下划线（例如 _socket）。
   note：模块，英文为Modules，本质上是一个Python程序，以.py作为文件后缀。任何py文件都可以作为一个模块。
4. Class Names
   1. 类名通常应使用CapWords惯例。
   2. 在接口被记录下来并主要作为可调用的情况下，可以使用函数的命名惯例来代替。
   3. 请注意，对于内建程序的名称有一个单独的约定：大多数内建程序的名称都是单字（或两个字并列），CapWords约定只用于异常名称和内建程序常量。
5. Type Variable Names
   1. PEP 484中引入的类型变量的名称通常应该使用CapWords，更倾向于使用短名称： 建议在用于声明协变或反变行为的变量上添加后缀_co或_contra：
6. Exception Names
   1. 因为异常应该是类，所以类的命名惯例在此适用。然而，你应该在你的异常名称上使用后缀 "Error"（如果该异常实际上是一个错误）。
7. Global Variable Names
   1. 让我们希望这些变量只在一个模块内使用。
   2. 惯例与函数的惯例大致相同。
   3. 被设计为通过 from M import * 使用的模块应该使用 __all__ 机制来防止导出模块变量，或者使用较早的惯例，在这些球状物前加一个下划线 (你可能想这样做来表示这些球状物是 "模块非公开的"。) 。
  note: 防止导出模块，阻止变量在模块间的污染
8. Function and Variable Names
   1. 函数名称应使用小写，必要时用下划线隔开，以提高可读性。
   2. 变量名称遵循与函数名称相同的惯例。
   3. mixedCase只允许在已经是主流风格的情况下使用（例如threading.py），以保持向后兼容。
9.  Function and Method Arguments (函数和方法参数)
    1.  始终使用self作为实例方法的第一个参数。
    2.  总是使用cls作为类方法的第一个参数。
    3.  如果一个函数参数的名称与一个保留的关键字相冲突，一般来说，最好是在后面加上一个下划线，而不是使用缩写或拼写错误。因此class_比clss好。(也许更好的是通过使用同义词来避免这种冲突)。
    4. 
10. Method Names and Instance Variables
    1.  使用函数命名规则：小写字母，必要时用下划线隔开单词，以提高可读性。
    2.  只对非公开的方法和实例变量使用一个前导下划线。
    3.  为了避免与子类发生名称冲突，使用两个前导下划线来调用 Python 的名称处理规则。
    4.  Python 将这些名字与类的名字混在一起： 如果类 Foo 有一个名为 __a 的属性，它不能被 Foo.__a 访问。(一个执着的用户仍然可以通过调用 Foo._Foo__a 来获得访问权。)一般来说，双引号只应该被用来避免与设计为子类的类中的属性发生名称冲突。
    5.  注意：关于__名称的使用有一些争议（见下文）。
11. Constants
    1.  常量通常定义在模块层面，用大写字母书写，用下划线隔开。例如，MAX_OVERFLOW和TOTAL。
12. Designing for Inheritance
    1.  始终决定一个类的方法和实例变量（统称："属性"）应该是公共的还是非公共的。如果有疑问，就选择非公开；以后公开比把公开属性变成非公开更容易。
    2.  公共属性是那些你期望你的类的无关客户使用的属性，你的承诺是避免向后不兼容的变化。非公开属性是那些不打算被第三方使用的属性；你不保证非公开属性不会改变，甚至被删除。我们在这里不使用 "私有 "一词，因为在 Python 中没有任何属性是真正的私有的 (不需要一般的不必要的工作)。
        1. note: 公开，意味着承诺
    3.  另一类属性是 "子类API "的一部分（在其他语言中通常称为 "保护"）。有些类被设计成可以被继承，以扩展或修改该类行为的各个方面。当设计这样一个类时，要注意明确决定哪些属性是公共的，哪些是子类API的一部分，哪些是真正只能由基类使用的。
    4.  考虑到这一点，下面是Pythonic准则：
        1.  公共属性不应该有前导下划线。
        2.  如果你的公共属性名称与一个保留的关键字相冲突，请在你的属性名称后面加上一个下划线。这比缩写或破坏性的拼写更可取。
        3.  对于简单的公共数据属性，最好是只公开属性名称，而不使用复杂的访问器/混合器方法。
        4.  请记住，如果你发现一个简单的数据属性需要增长功能行为，Python提供了一个方便的途径来实现未来的增强。在这种情况下，使用属性将功能实现隐藏在简单的数据属性访问语法后面。
            1.  尽量保持功能行为无副作用，尽管诸如缓存之类的副作用一般是可以的。
            2.  避免将属性用于计算昂贵的操作；属性符号使调用者相信访问是（相对）便宜的。
        5.  如果你的类打算被子类化，并且你有不想让子类使用的属性，可以考虑用双头下划线和无尾下划线来命名它们。这将调用Python的名称混合算法，类的名称将被混合成属性名称。这有助于在子类无意中包含相同名称的属性时避免属性名称的碰撞。
            1.   注意，只有简单的类名被用在混杂的名称中，所以如果一个子类同时选择了相同的类名和属性名，你仍然可以得到名称的碰撞。
            2.   名称分解会使某些使用，如调试和 __getattr__() ，变得不那么方便。然而，名字杂交的算法有很好的文件记录，并且很容易手动执行。
            3.   不是每个人都喜欢混用名字。尽量在避免意外的名字冲突与高级调用者的潜在使用之间取得平衡。

13.   Public and Internal Interfaces(公开的和内部接口)
      1.    任何向后兼容的保证只适用于公共接口。因此，重要的是，用户要能够明确区分公共和内部接口。
      2.    有文档记载的接口被认为是公共的，除非文档中明确声明它们是临时的或内部的接口，不受通常的向后兼容性保证。所有没有记录的接口应该被认为是内部的。
      3.    为了更好地支持自省，模块应该使用 __all__ 属性明确地声明其公共 API 中的名称。将 __all__ 设置为一个空列表，表示该模块没有公共 API。
      4.    即使适当地设置了 __all__，内部接口 (包、模块、类、函数、属性或其它名称) 仍然应该在前面加上一个单一的下划线。
      5.    如果任何包含命名空间（包、模块或类）的接口也被认为是内部的。
      6.    导入的名字应该总是被认为是一个实现细节。其他模块不能依赖对这些导入名称的间接访问，除非它们是包含模块的 API 中明确记录的部分，例如 os.path 或包的 __init__ 模块，它暴露了子模块的功能。
 
## Programming Recommendations

*  代码的编写方式不应不利于Python的其他实现（PyPy、Jython、IronPython、Cython、Psyco，等等）。
*  当用丰富的比较实现排序操作时，最好是实现所有的六个操作(__eq__, __ne__, __lt__, __le__, __gt__, __ge__)，而不是依靠其他代码只行使一个特定的比较。
*  始终使用 def 语句而不是赋值语句，将 lambda 表达式直接绑定到一个
```
# Correct:
def f(x): return 2*x

# Wrong:
f = lambda x: 2*x
```
* 从Exception而不是BaseException派生异常。从BaseException直接继承是为那些捕获异常几乎都是错误的事情而保留的。
  * 根据捕捉异常的代码可能需要的区别来设计异常层次，而不是根据引发异常的位置。旨在回答 "什么地方出错了 "的问题。
  * 以编程的方式，而不是只说明 "发生了一个问题",请参阅PEP 3151，以了解在内置异常层次结构中吸取的这一教训的例子)
  * 类的命名规则适用于此，不过如果异常是一个错误，你应该在异常类中添加后缀 "Error"。用于非本地流控制或其他形式的信号的非错误异常不需要特殊后缀。
* 适当地使用异常链。raise X from Y应该被用来表示明确的替换，而不丢失原来的跟踪。
  * 当故意替换一个内部异常时（使用 raise X from None），确保相关的细节被转移到新的异常中（比如在将 KeyError 转换为 AttributeError 时保留属性名称，或者在新的异常消息中嵌入原始异常的文本）。
* 在捕获异常时，尽可能地提及具体的异常，而不是使用赤裸裸的except:子句  
  ```
    try:
      import platform_specific_module
  except ImportError:
      platform_specific_module = None
    ```
  A bare except: 这个原因会捕捉SystemExit和KeyboardInterrupt异常，使得用Control-C中断程序更加困难，并且可以掩盖其他问题。如果你想捕捉所有提示程序错误的异常,使用except Exception:（裸except等同于except BaseException:）。
  一个好的经验法则是将裸 "除 "字句的使用限制在两种情况下：
  1. 如果异常处理程序将打印出来或记录回溯；至少用户会意识到发生了错误。
  2. 如果代码需要做一些清理工作，但又让异常通过 raise 向上传播，那么 try...finally 可以是处理这种情况的更好方法。
* 当捕捉操作系统错误时，更喜欢Python 3.3中引入的显式异常层次结构，而不是自省errno值。
* 此外，对于所有的try/except子句，将try子句限制在绝对必要的最小代码量。同样，这也避免了掩盖错误：
* 当一个资源是某段代码的局部时，使用with语句来确保它在使用后被及时可靠地清理掉。try/finally语句也是可以接受的。
* 当上下文管理程序做一些除获取和释放资源以外的事情时，应该通过单独的函数或方法进行调用：
* 在返回语句中要保持一致。在一个函数中，要么所有的返回语句都应该返回一个表达式，要么都不应该。如果任何返回语句都返回一个表达式，任何没有返回值的返回语句都应该明确说明为返回无，并且在函数的结尾应该有一个明确的返回语句（如果可以达到）：
* 使用''.startwith()和''.endswith()而不是字符串切片来检查前缀或后缀。
* 对象类型比较应该总是使用isinstance()而不是直接比较类型：
  ```
    # Correct:
    if isinstance(obj, int):

    # Wrong:
    if type(obj) is type(1):
  ```
* 对于序列（字符串、列表、图元），使用空序列为假的事实。
    ```
    # Correct:
    if not seq:
    if seq:
    # Wrong:
    if len(seq):
    if not len(seq):
    ```
* 不要写那些依赖大量尾部空白的字符串字面。这样的尾部空白在视觉上是无法区分的，一些编辑器（或最近的reindent.py）会修剪它们。
* 不要用==来比较布尔值是真还是假。
* 不鼓励在try...finally的finally套件中使用流控制语句return/break/continue，流控制语句会跳出finally套件。这是因为这些语句将隐含地取消任何正在通过Final套件传播的活动异常：

```
# Wrong:
def foo():
    try:
        1 / 0
    finally:
        return 42
```

### Function Annotations
随着PEP 484的接受，函数注释的风格规则已经改变。
* 函数注释应该使用PEP 484语法（在上一节中有一些注释的格式建议）。
* Python标准库在采用这种注解时应该是保守的，但对于新代码和大的重构来说，允许使用它们。
* 对于想要对函数注解进行不同的使用的代码，建议放一个形式的注释：
  * 靠近文件的顶部；这告诉类型检查器忽略所有的注释。(在PEP 484中可以找到禁用类型检查器投诉的更精细的方法)。
* 与linters一样，类型检查器是可选的、独立的工具。默认情况下，Python 解释器不应该因为类型检查而发出任何消息，也不应该根据注解来改变它们的行为。
* 不想使用类型检查器的用户可以自由地忽略它们。
### Variable Annotations
PEP 526引入了变量注释。对它们的风格建议与上述关于函数注释的建议相似：
* 模块级变量、类和实例变量以及局部变量的注释应该在冒号后面有一个空格。
* 冒号前不应该有空格。
* 如果一个赋值有一个右侧，那么等号的两边应该正好有一个空格：
* 尽管PEP 526被接受用于Python 3.6，但变量注释语法是所有Python版本上存根文件的首选语法(详情见PEP 484)。
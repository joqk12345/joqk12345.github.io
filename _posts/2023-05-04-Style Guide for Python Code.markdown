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







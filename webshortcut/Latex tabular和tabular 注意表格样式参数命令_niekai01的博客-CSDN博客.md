# Latex tabular和tabular* 注意表格样式参数命令_niekai01的博客-CSDN博客
![](https://csdnimg.cn/release/blogv2/dist/pc/img/reprint.png)

[niekai01](https://blog.csdn.net/niekai01) 2017-01-19 19:19:19 ![](https://csdnimg.cn/release/blogv2/dist/pc/img/articleReadEyes.png)
 53856 ![](https://csdnimg.cn/release/blogv2/dist/pc/img/tobarCollect.png)
 收藏  28 

一、表格环境的定义  
环境 tabular 和 tabular\*是生成表格的基本工具，其定义 (语法) 如下：  

\\begin{tabular}\[位置]{列} 行\\end{tabular}

\\begin{tabular\*}{宽度}\[位置]{列} 行\\end{tabular\*}

tabular 环境可以用来排版带有横线和竖线的表格，LATEX 自动确定表格的宽度；tabular\*环境与 tabular 环境类似，只是可以用参数指定表格的整体宽度，另外列参数必须在第一列后面的某个地方包含一个合适的表达式 (见下面说明)。

通常，为了使表格在页面上居中，要利用 center 环境:  

\\begin{center} 表格\\end{center}

二、表格环境参数格式  
2.1 位置可选参数  
该参数表示表格相对于外部文本行基线的位置, 又称为垂直定位参数，有三种情况:  
t: 表格顶部与当前外部文本行的基线重合  
b: 表格底部与当前外部文本行的基线重合  
缺省 (不使用): 表格按照外部文本行的基线垂直居中

2.2 列必选参数  
该参数表明表格的格式，故又称为列格式参数。在这个参数中，对每一列必须有一个相应的格式符号，另外还可能包含相应于表格左右边界和列间距的其它项。列格式符号可以取下列值：  
l: 列中文本左对齐  
r: 列中文本右对齐  
c: 列中文本居中  
pf 宽度 g: 指定列的文本宽度，宽度由宽度参数给出, 列中文本按该宽度自动换行  
｜: 画一条竖直线  
｜｜: 画二条紧相邻的竖直线

三、 表格文本行中的命令  
表格中的每一水平行都由\\结束。这些行由一组彼此之间用 & 符号分开的列条目组成。因此每一行应具有与列定义中列中相同数目的列条目，其中有些条目可以是空白的。  
3.1 \\tabularnewline 命令  
\\tabularnewline 命令用于强制一表格行的结束，而\\除了可以结束整个一行表格内容外，还可以在单个列的内容中实现换行.

3.2 \\ hline 命令  
这条命令只能位于第一行前面或紧接在行结束命令\\的后面，表示在刚结束的那一行画一根水平的直线。如果这条命令位于表格的开头，那么就会在表格顶部画一横线，横线的宽度与表格的宽度相同. 放在一起的两条水平\\hline 命令就会画出两条间隔很小的水平线.

3.3 \\cline{n-m} 命令  
这条命令的放置同\\hline 命令，并且在一行中可以出现多次。该命令从第 n 列的左边开始，画一条到第 m 列右边结束的水平线.

3.4 \\ vline 命令  
该命令画一条竖直线，其高度等于其所在行的行高。用这种命令，可以得到那些不是贯穿整个表格的竖直线.

3.5 \\multicolumn{数}{列}{文本} 命令  
这条命令只能位于一行的开始或者一个列分隔符 (&) 的后面，它把接下来的数个列合并成一个列处理，其内容为文本。该列的总宽度等于合并前各个列的宽度之和加上列间距之和。列参数的含义与 tabular 环境中列参数相似。

3.6 @表达式：@文本  
@表达式在出现两列中间的每一行上插入文本，同时去掉原来在这两列间自动插入的  
空白。我们有下面的几点为变通：  
1. 如果我们需要继续使用空白，必须在 @表达式的文本参数中包含\\hspace{} 命令。  
2. 如果希望某两个特定列之间的间隔与缺省的标准间隔不同，可以在表格环境的行参数中相应的位置上放上 @{\\hsapce{宽度}} 控制，此时该处列间间隔将变成宽度。  
3. @表达式中使用\\extracolsep{宽度} 控制，使后面所有列间间隔在原来标准间隔的基础上增加宽度大小。  
4. 在 tabular\*环境中。必须使用 @{\\extracolsep\\fill} 命令，使后面所有列间距可以伸展到预定义的表格宽度。  
5. 一个表格即使左右边界没有竖线或其他表征符号，相应的位置与后面 (前面) 的列之间也会插入等于标准列间隔一半的空白。如果不希望有这些空白，可以在行参数开始或结束处使用 @{}表达式。

四、表格样式参数命令  
在表格的生成中，LATEX 要利用许多样式参数，来设置其标准值。我们也可以在导言区或某一环境中用\\setlength 命令改变这些值。  
x4.1 \\tabcolsep 命令  
用于 tabular 或 tabular\*环境，表示两列间标准间隔的一半大小  
x4.2 \\arrayrulewidth 命令  
代表表格中水平线与垂直线的宽度  
x4.3 \\doublerulesep 命令  
代表表格中使用垂直竖线时两根竖线间的距离  
x4.4 \\arraystretch 命令  
代表表格中行间距的缩放比例因子 (缺省的标准值为 1)

> [http://blog.sina.com.cn/s/blog_53a8a4710100x4c1.html](http://blog.sina.com.cn/s/blog_53a8a4710100x4c1.html) 
>  [https://blog.csdn.net/niekai01/article/details/54618916?utm_medium=distribute.pc_relevant.none-task-blog-2%7Edefault%7EBlogCommendFromMachineLearnPai2%7Edefault-2.base&depth_1-utm_source=distribute.pc_relevant.none-task-blog-2%7Edefault%7EBlogCommendFromMachineLearnPai2%7Edefault-2.base](https://blog.csdn.net/niekai01/article/details/54618916?utm_medium=distribute.pc_relevant.none-task-blog-2%7Edefault%7EBlogCommendFromMachineLearnPai2%7Edefault-2.base&depth_1-utm_source=distribute.pc_relevant.none-task-blog-2%7Edefault%7EBlogCommendFromMachineLearnPai2%7Edefault-2.base)

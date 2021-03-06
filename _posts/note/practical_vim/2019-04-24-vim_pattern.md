---
bg: 'vim.png'
layout: post
title:  "vim模式"
crawlertitle: "vim"
summary: ""
date:   2019-04-24 09:26:00 +0700
categories: posts
tags: 'vim'
author: 宋天
---

Vim的查找替换的匹配模式

##### 查找
- :set ingorecase设置全局忽略大小写，影响配合和自动补全
- \c忽略大小写，\C大小写敏感
- :set smartcase在字符串全为小写时忽略大小写，字符串含有一个大写时大小写敏感
- vim正则表达式更接近POSIX，而不是Perl
- 正则中中括号`[]`不需要转义（escape）；小括号`()`两个都需要转义；大括号`{}`只需要转义前面的就可以了,都转义也可以。
- \v very magic 可以在正则中不需要转义特殊功能字符,和Perl规则一致
- \V very nomagic 除了\有特殊含义之外，其他的字符都无特殊含义，如.*等
- 用小括号()引住的内容会被存起来，后面可以用/n引用到，n对应小括号的出现的位置，/0代表整个匹配的内容,使用%不让小括号去捕获内容,当括号中有括号时，按左括号出现的顺序排序
- *往下查找光标下的单词（整个单词），#往上查找，g*往下查找光标下的单词（不一定非得整个单词）,g#向上查找
- escape(@u,'/\'),使用escape函数将u寄存器中的内容的/前面增加\转义,更加通用的方式`escape(@u,getcmdtype().'\')`


| 常用字符含义(:h /character-classes) | 作用                                                            | 相反 | 作用       |
| ----------------------------------- | --------------------------------------------------------------- | ---- | ---------- |
| \s                                  | 空白字符，空格和Tab                                             | \S   | 非空白字符 |
| \d                                  | 数字                                                            | \D   | 非数字     |
| \x                                  | 十六进制 [0-9a-fA-F]                                            | \X   | 非十六进制 |
| \o                                  | 八进制 [0-7]                                                    | \O   | 非八进制   |
| \w                                  | 数字，字母和下划线 [0-9a-zA-Z_]                                 | \W   | 相反       |
| \h                                  | 字母和下划线[a-zA-z_]                                           | \H   | 相反       |
| \a                                  | 字母 [a-zA-Z]                                                   | \A   | 相反       |
| \l                                  | 小写字母 [a-z]                                                  | \L   | 相反       |
| \u                                  | 大写字母 [A-Z]                                                  | \U   | 相反       |
| \e                                  | Esc                                                             | -    | -          |
| \t                                  | Tab                                                             | -    | -          |
| \r                                  | CR                                                              | -    | -          |
| \\_                                 | \\_s 空格或者换行，s可以由上面的字母替换                        | -    | -          |
| <>                                  | 单词的边界                                                      | -    | -          |
| \zs \ze                             | 匹配(match)的边界，表明了需要查找的部分,但任要符合模式(pattern) | -    | -          |


| 命令         | 作用                                                 |
| ------------ | ---------------------------------------------------- |
| /            | 向后查找                                             |
| ?            | 向前查找                                             |
| n            | 重复上一次查找                                       |
| N            | 反向上一次查找                                       |
| /CR          | 向下重复上一次查找                                   |
| ?CR          | 向上重复上一次查找                                   |
| /{content}/e | 查找指定内容，并将光标放到指定内容的后面             |
| //e          | 向下查找上一次内容并移动光标，可用于补救             |
| gn           | ---vim7.4 进入虚拟模式选中下个匹配，一般配合operator |
| gN           | ---vim7.4 进入虚拟模式选中上个匹配，一般配合operator |

- :set hls 设置高亮， :set nohls 关闭高亮， :nohls 这次关闭高亮
- nnoremap <silent> <C-l> :<C-u>nohlsearch<CR><C-l>  设置C-L清除高亮,C-L通常用来清除屏幕信息
- :set incsearch 设置在输入搜索关键字的同时会在文档中查找，并高亮给予反馈,对于需要查找整个单词的，这时不必手动打全，只用`C-rC-w`就可以将剩下的字符补全
- 可以先查找，捕获查找，在替换中引用，善于利用<up>和q/一步步完善正则表达式
- %s///gn 统计次数，搜索域为空默认为使用当前的模式，g为每行的所有匹配，n为不实际执行替换，显示匹配的个数
- :vimgrep //g %   %代表当前文件  搜索域为空代表使用当前的模式，统计次数并显示当前是第几个，cnext和cprev可以在匹配中移动就好像n和N

##### 替换

`:[range]substitute/{pat-tern}/{string}/[flags]`

| 替换域字符       | 作用                                           |
| ---------------- | ---------------------------------------------- |
| `\r`             | 插入回车                                       |
| `\t`             | 插入tab                                        |
| `\\`             | 代表一个反斜杠                                 |
| `\1`             | 插入匹配域中第一个匹配，由括号括起来为一个匹配 |
| `\2`             | 插入第二个匹配，同理，直到`\9`                 |
| `\0` `&`         | 插入整个匹配域                                 |
| `~`              | 使用上一个触发替换的字符串                     |
| `\={Vim Script}` | 使用vim脚本的结果                              |
| `C-r{register}`  | 使用寄存器里面的内容                           |

| flag | 作用                                                                           |
| ---- | ------------------------------------------------------------------------------ |
| g    | 匹配一行的所有出现情况，作为横向（%是ex命令range,作为纵向）                    |
| c    | 在替换每个内容时确认，替换，跳过，退出，替换退出，替换剩下所有退出，翻页等功能 |


- 替换时将搜索域留空默认使用当前匹配模式
- `:let @/=string` 相当于/string（除了在查找历史里面没用记录）
- `:let @a=string` 相当于将string放入寄存器a
- `:%s//\=@a/g`   `\=`执行vim脚本，@从寄存器中取值，这三步相当于一次替换
- `g&`相当于`:%s//~/&`   对所有行以同样的标识（flag）重复上次的替换，&上次的标识
- 可以对选中区域的内容实现替换
- &相当于:s ，执行上次替换，可以使用&&保持上次的flag
- 可以利用子匹配(submatch)重新排序
- 在vim脚本中必须使用submacht()方法引用子匹配
- `%s/\v(<dog>|<man>)/\={"dog":"man","man":"dog"}[submatch(1)]/g`将dog和man单词交换（希望多年后我还能看懂）
- `:vimgrep //g **/*`  查找当前目录及以下匹配模式的文件，`:copen`可以浏览匹配到的文件列表(quickfix)，并且可以通过回车和分裂窗口进行切换
- `:cfdo` 对quickfix中的文件进行统一修改，之前需先设置`:set hidden`不保存可以切换到其他文件
- `|` 在vim中代表将命令按顺序执行，在shell中代表管道符

##### 全局命令
`:[range] global[!] /{pattern}/ [cmd]`


- 默认范围（range）是%,这一点与通常的命令不同，通常命令范围默认都是针对行的
- 模式（pattern）是查找和替换中的一致，可以留空
- 命令（cmd）可以是global的命令之外的任何命令,默认是print
- `global!`和`vglobal`将作用于不匹配的模式
- `g/re/p`是这个命令的缩写模式，也是grep命令的来源
- `v/re/d` 仅留下匹配的行，vglobal
- `g/re/y A`  将内容追加到寄存器a中，可以存多行内容存到寄存器中，而小写a是覆盖，多行内容只会存最后一行，可以在执行之前`qaq`清空寄存器a的内容
- `g/re/t$` 将匹配内容复制到行尾
- `:sort` 对指定范围内的行首按照字母表排序
- `:g/{/ .+1,/}/-1 sort` 这个命令里面的内容都学过，功能是?
- `g/{start}/ .,{finish}` [cmd] 通用模式
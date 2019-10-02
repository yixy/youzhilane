
## 1. awk简介 ##

样式扫描和处理语言。

awk逐行扫描输入文件，从第一行到最后一行，寻找匹配的特定模式的行，并在这些行上进行相应的操作。如果没有指定匹配模式，则会对所有行进行操作。如果没有指定处理操作，则把匹配的行显示到标准输出。

语法格式如下：

```
awk [options] 'commands' filenames
awk [options] -f awk-script-file filenames
```

## 2. options ##

```
-F 定义输入字段(列)的分隔符，默认分隔符是空格或制表符tab
```

## 3. command ##

command可以分为三段：行处理前（实际发生在读文件之前），行处理（包含匹配模式和操作），行处理后（所有的行处理之后）。

> 模式匹配pattern支持正则表达式，使用形如`/`regcontent`/`的方式来指定

```
BEGIN{} pattern{action} END{}

#例子
$ awk 'BEGIN{print "---BEGIN---"} NR<=5{print "OK"} END{print "---END---"}' /etc/passwd
---BEGIN---
OK
OK
OK
OK
OK
---END---
```

## 4. 内置变量 ##

awk中，记录即行，字段即列。

* FS：field separator，awk在行处理时的字段（列）分隔符，默认分隔符是空格或制表符tab
* OFS：output field separator，awk行处理输出时的字段（列）分隔符，默认是空格。若输出多列并采用逗号分隔列时，实际是采用OFS分隔。
* $0：整条记录
* $1-$100：第1到100个字段（列）
* NR：已经读出的记录数，就是行号，从1开始，如果有多个文件话，这个值也是不断累加中。
* FNR：当前记录数，与NR不同的是，这个值会是各个文件自己的行号
* NF：当前行的字段数目
* $NF：最后一个字段
* RS：record separator，默认为\n
* ORS：output record separator，默认为\n
* FILENAME：当前输入文件的名字

## 5. 模式匹配pattern ##

模式可以是比较表达式、正则表达式。

> 模式中支持执行算术运算，awk都将按浮点数方式执行算术运算。算术运算包含：`+ - * / % ^`
> 模式中支持执行逻辑运算：与或非（`&& || !`）

比较表达式。使用比较运算符（<、<=、==、!=、>=、>）比较数字或字符串（用`"`括起来）。

```
#数字
awk '$3==0' /etc/passwd
#字符串
awk '$3=="0"' /etc/passwd
```

正则表达式。

```
#整行，匹配bob的记录
awk '/bob/' text.txt
awk '$0 ~ /bob/' text.txt
#整行，不匹配bob的记录
awk '!/bob/' text.txt
awk '$0 !~ /bob/' text.txt
#字段
awk '$1 ~ /bob/' text.txt
awk '$1 !~ /bob/' text.txt
```

awk范围模式。先匹配从第一个模式的首次出现的行到第二个模式的首次出现的行及其之间的内容，然后匹配从第一个模式的下一次出现到第二个的下一次出现，以此类推。如果匹配到第一个模式而没有发现第二个模式， awk 就将显示从第一个模式首次出现的行到文件末尾之间的所有行。

```
$ awk '/Tom/,/Suzanne/' filename
```

## 6. 操作action ##

可以在action中使用条件语句，循环语句，print(f)语句。

> action中支持执行算术运算，awk都将按浮点数方式执行算术运算。算术运算包含：`+ - * / % ^`
> action中支持执行逻辑运算：与或非（`&& || !`）

条件语句。

```
$ awk '$1=="example"{if($3>300){print $3">300"} else if($3<300 && $3>100){print "300>"$3">100"} else{print "nothing"}}' test.txt
```

循环语句。

```
$ awk '$1=="example"{for(i=0;i<10;i++){print $3,i}}' test.txt
#注意，printf 与 print 的区别: printf 不自动打印换行符, print 则自动打印。
```

action中可以进行输出（print）或 格式化输出（printf）。注意print或printf中非变量都需要用引号`"`括起来，否则不起作用。注意，printf 与 print 的区别: printf 不自动打印换行符, print 则自动打印。

```
#若使用逗号，则print使用OFS作为分隔
awk '{print $1,$NF}' test.txt
#指定print使用:作为分隔
awk '{print $1":"$NF}' test.txt
#多个字段分隔符
$ awk 'BEGIN{FS="[ :\t]"} {print $5}' test.txt
```

## 7. match 函数 ##

action中可以使用awk提供的内置函数。

```

```

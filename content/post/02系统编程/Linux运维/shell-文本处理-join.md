
将两个文件里指定列同样的行连接起来，即依照两个文件里共有的某一列，将相应的行拼接成一行。

```
$ cat a
1 sldkfj
2 sfds
3 s
4 sfsag
5 safgs
8 asljf

$ cat b
1 a
2 b
3 c
4 d
5 e
6 f
7 g

#默认显示匹配的
$ join a b
1 sldkfj a
2 sfds b
3 s c
4 sfsag d
5 safgs e

#`-v`显示不匹配
$ join -v 1 -v 2 a b
6 f
7 g
8 asljf

#`-a`显示匹配的和不匹配的
$ join -a 1 -a 2 a b
1 sldkfj a
2 sfds b
3 s c
4 sfsag d
5 safgs e
6 f
7 g
8 asljf
```
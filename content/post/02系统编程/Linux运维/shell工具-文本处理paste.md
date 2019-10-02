

paste默认采用tab分隔，可以通过`-d`指定分隔字符。

```
$ cat a
1
2
3
4
5

$ cat b
a
b
c
d
e
f
g

$ paste -d"=" a b
1=a
2=b
3=c
4=d
5=e
=f
=g
```

`grep`用于查找文件里的内容。

> grep 代表 `g/re/p`，是Ken Thompson根据ed来命名的。Its name comes from the ed command g/re/p (globally search a regular expression and print) 

```
#查询结果显示行号并高亮显示
grep -n --color  "root" /etc/passwd

#反向查找
grep -v "root" /etc/passwd

#以文本方式搜索
grep -a "root" /etc/passwd

#计算并显示找到的符合行的次数
grep -c "root" /etc/passwd

#忽略大小写
grep -i "root" /etc/passwd
```

在匹配模式串中使用正则表达式。`egrep`使用里扩展的正则表达式，`grep -E`相当于`egrep`命令。

```
#示例文件
$ cat test.txt
slkdjf.slksjdfn.234.sldkfj.23
slkjdf.234.rw0jfldkjf
234.23423.423423.432
234.223.43.432
234234234
234.423.423.423.
234.423.423.423.3245
.234.423.423.423

$ grep "^[0-9]\+$" test.txt
234234234

$ grep "^\([0-9]\+\.\)\{3\}[0-9]\+$" test.txt
234.23423.423423.432
234.223.43.432

$ grep -E "^([0-9]+\.){3}[0-9]+$" test.txt
234.23423.423423.432
234.223.43.432

$ grep -E "^([0-9]{0,3}\.){3}[0-9]{0,3}$" test.txt
234.223.43.432
```
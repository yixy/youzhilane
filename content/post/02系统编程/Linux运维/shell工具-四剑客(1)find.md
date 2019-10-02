
`find`用于查找文件。

```
#按名称查找
find /home -name a.txt
find /home -name "*.txt"
#忽略大小写
find /home -iname "*.txt"
#反向查找
find . ! -name "*.txt"

#按名称（正则表达式）查找
find /home -regex '.*\.txt'
#忽略大小写
find /home -iregex '.*\.txt'

#按类型查找目录
find /home -type dir
#按类型查找文件
find /home -type file

#1天以内的文件
find /home -mtime -1
#30天以前的文件
find /home -mtime +30

#大于10KB的文件
find /home -size +10k
#小于5MB的文件
find /home -size -5M

#按权限查询
find /home -perm 755

#将查询出来的文件传给exec后的命令来执行。-exec  参数后面跟的是command命令，它的终止是以;为结束标志的，所以这句命令后面的分号是不可缺少的，考虑到各个系统中分号会有不同的意义，所以前面加反斜杠。{}   花括号代表前面find查找出来的文件名。
find . -type f -exec ls -l {} \;
```

使用`stat`可以可以查看文件的atime、ctime、mtime。atime是指access time，即文件被读取或者执行的时间，修改文件是不会改变access time的。ctime即change time文件状态改变时间，指文件的i结点被修改的时间，如通过chmod修改文件属性，ctime就会被修改。mtime即modify time，指文件内容被修改的时间。


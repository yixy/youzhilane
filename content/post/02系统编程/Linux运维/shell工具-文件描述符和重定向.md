
文件描述符用来追踪已打开的文件，是与打开的文件或数据流相关的整数。最常见的文件描述符是stdin stdout stderr。文件描述符0/1/2是系统预留的。 
* 0 — stdin，标准输入 
* 1 — stdout，标准输出 
* 2 — stderr，标准错误


两个重定向符 > 和 >>。 
* > ：文件中的内容会先清空，再写入内容。 
* >>：将文本追加到目标文件中。

$ echo "Hello world" > tmp.txt
$ echo "Hello world" >> tmp.txt


使用重定向操作符时，重定向的内容不会出现在终端，而是直接被导入文件。默认使用标准输出。（>等同于 1>）如果想使用特定的文件描述符，必须将描述符置于操作符前。 
当一个命令发生错误并退出时，会返回一个非0的退出状态；成功运行返回数字0。退出状态可通过状态变量 $?获得。

$ ls + 2> out.txt #错误重定向
$ cmd 2>stderr.txt 1> stdout.txt #stderr重定向到一个文件，stdout重定向到另一个文件
$ cmd > output.txt 2>&1 #将stderr转换成stdout，使得stderr和stdout都重定向到同一个文件中
$ cmd &> out.txt #作用同上
$ cmd 2> /dev/null #将stderr重定向到/dev/null


/dev/null是一个特殊的设备文件，该文件收到任何数据都会被丢弃。


当对stderr或stdout进行重定向时，重定向的文本将传入文件，就不能将输出通过管道(|)传给接下来的命令，而这些命令是通过stdin来接收文本的。 
如果既要将数据重定向到文件，又要提供一份重定向的数据副本作为后续命令的stdin，可以使用tee来实现。 
$ command | tee FILE1 FILE2 
$ cat a* | tee out.txt | cat -n


cat > 1.txt <<eof
1
2
3
4
5
eof

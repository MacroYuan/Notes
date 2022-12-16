Linux命令——grep

grep全称是global regular exprission print，在Linux中跟awk类似是用于匹配的命令

命令格式

```shell
grep [option] PATTERN [FILE...]
```

- -c：打印匹配的个数

- -h：显示匹配行，但不显示文件名

- -i：忽略匹配的情况

- -l：只显示匹配的文件名

- -n：显示匹配的行及其行号

- -v：打印所有不匹配的行

- -e exp：指定表达式，可多次使用

- -f file：从文件中提起匹配串，每行一个

- -E：将PATTERN视为正则式匹配

- -w：匹配整个单词

- -o：仅打印匹配行匹配的部分

- -A n：打印匹配行及其后n行

- -B n：打印匹配行及其前n行

- -C n：打印匹配行及其前后n行



eg.查找某个目录下匹配的文件名

```shell
ls -l | grep employee
```

在文件中寻找多个字符串

```shell
grep "string" filename1 filename2
grep "string" file*
```

打印匹配次数

```shell
grep -c "string" filename
```



忽略大小写

- -i

- -y

- --ignore-case



正常表达式

https://github.com/MacroYuan/Notes/blob/main/%E5%89%8D%E7%AB%AF%E4%BB%8E%E5%85%A5%E9%97%A8%E5%BC%80%E5%A7%8B/%E6%AD%A3%E5%88%99%E5%BC%8F.md

grep中加上”“就可以匹配正则表达式了



上色

- --color





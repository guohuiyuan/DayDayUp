## 常见的坑
- 声明变量的时候,赋值要和值紧紧相连

```
a="你好"
echo ${a}
```

- 最好用${a}而不是$a,引用变量,为了增加可读性,清楚地把${a}和字符分开
- 字符串带变量要用""引用,而不能用''

```
a="你好"
echo "${a}"
echo '${a}'
```

结果是分别是 你好,${a}
- 命令的结果作为变量,最好用a=$(pwd),而不是a=`pwd`,因为前者支持嵌套

```
a=$(pwd)
echo ${a}
a=`pwd`
echo ${a}
```
- dirname是获得当前文件的路径,basename是去目录部分的最后的文件名或目录

| path     | dirname | basename |
| -------- | ------- | -------- |
| /usr/lib | /usr    | lib      |
| /usr/    | /       | usr      |
| usr      | .       | usr      |
| /        | /       | /        |
| .        | .       | .        |
| ..       | .       | ..       |

## 记录常用的命令
1. cat
**cat一般用于打印文件内容**

- cat <<EOF,多行字符串输入

```
cat > a.txt <<EOF
This is a test file
EOF
```

- cat <<-EOF, (-)的作用是去除文本里所有行的tab前缀,如果EOF也要缩进的时候,EOF前面也要加"tab"

```
cat >a.txt <<-EOF
<tab>Line1
<tab><tab>Line2
<tab><tab>Line3<tab>Field<tab>
<tab>EOF
```

- cat <<'EOF',加引号,变量失效

```
VAR="value"

cat >a.txt <<EOF
variabe is "${VAR}"
EOF
```

结果为 variabe is "value"

```
VAR="value"

cat >a.txt <<'EOF'
variabe is "${VAR}"
EOF
```

结果为 variabe is "${VAR}"

2. grep
**grep一般用于查找文本内容**

- grep

```
cat > a.txt <<EOF
This is a test file
hello world
hello,nihao
EOF
cat a.txt|grep hello
```

结果为

```
hello world
hello,nihao
```

- grep -v 反向查找

```
cat > a.txt <<EOF
This is a test file
hello world
hello,nihao
EOF
cat a.txt|grep -v hello
```

结果为

```
This is a test file
```

3. awk
**awk一般用来分割文本,获得文本某一段信息**



4. if语句

    if [ -f file ] ：如果文件存在
    if [ -d … ] ：如果目录存在
    if [ -s file ] ：如果文件存在且非空
    if [ -r file ] ：如果文件存在且可读
    if [ -w file ] ：如果文件存在且可写
    if [ -x file ] ： 如果文件存在且可执行

5. makefile
Q: makefile报错：missing separator (did you mean TAB instead of 8 spaces?). Stop.
A: 因为makefile中，书写命令时，必须要在命令开头敲一个Tab键，而不能说用8个空格（space）来代替Tab，虽然看起来样子是一样的，但是它们不会生效为真正地makefile命令！（可以看到直接复制的话[命令行](https://so.csdn.net/so/search?q=命令行&spm=1001.2101.3001.7020)都是白色的，表示不生效的意思！）

6. string

```
var="http://www.aaa.com/123.htm"
echo ${var#*//}
echo ${var##*/}
echo ${var%/*}
echo ${var%%/*}
echo ${var:0:5}
echo ${var:7}
echo ${var:0-7:3}
echo ${var:0-7}
```

结果

```
www.aaa.com/123.htm
123.htm
http://www.aaa.com
http:
http:
www.aaa.com/123.htm
123
123.htm
```

| 格式                       | 说明                                                         |
| -------------------------- | ------------------------------------------------------------ |
| ${string: start :length}   | 从 string 字符串的左边第 start 个字符开始，向右截取 length 个字符。 |
| ${string: start}           | 从 string 字符串的左边第 start 个字符开始截取，直到最后。    |
| ${string: 0-start :length} | 从 string 字符串的右边第 start 个字符开始，向右截取 length 个字符。 |
| ${string: 0-start}         | 从 string 字符串的右边第 start 个字符开始截取，直到最后。    |
| ${string#*chars}           | 从 string 字符串第一次出现 *chars 的位置开始，截取 *chars 右边的所有字符。 |
| ${string##*chars}          | 从 string 字符串最后一次出现 *chars 的位置开始，截取 *chars 右边的所有字符。 |
| ${string%chars*}           | 从 string 字符串第一次出现 *chars 的位置开始，截取 *chars 左边的所有字符。 |
| ${string%%chars*}          | 从 string 字符串最后一次出现 *chars 的位置开始，截取 *chars 左边的所有字符。 |

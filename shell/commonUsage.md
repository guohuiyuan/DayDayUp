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

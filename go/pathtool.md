# GOPATH/bin下的可执行文件的用法

<!-- GFM-TOC -->
- [GOPATH/bin下的可执行文件的用法](#gopathbin下的可执行文件的用法)
	- [vscode debug](#vscode-debug)
	- [dlv](#dlv)
		- [记一次调试的实操](#记一次调试的实操)
<!-- GFM-TOC -->
## vscode debug
launch.json

```
{
    // 使用 IntelliSense 了解相关属性。 
    // 悬停以查看现有属性的描述。
    // 欲了解更多信息，请访问: https://go.microsoft.com/fwlink/?linkid=830387
    "version": "0.2.0",
    "configurations": [
        {
            "name": "base",
            "type": "go",
            "request": "launch",
            "mode": "auto",
            "program": "${workspaceFolder}/cmd/base",
            "args": [
                "-conf",
                "../../local/dev/config.yaml"
            ]
        }
    ]
}
```

program是main包的目录
${workspaceFolder}是vscode环境[变量](https://zhuanlan.zhihu.com/p/186026657)


## dlv
实际上vscode已经集成了dlv的功能，我们并不需要自己去调dlv
go的调试工具
[go dlv 结合调试流程熟悉dlv命令的使用](https://zhuanlan.zhihu.com/p/478303635)
[GO delve(dlv)调试工具笔记及实操](https://zhuanlan.zhihu.com/p/425645473)

### 记一次调试的实操
因为在linux上查找问题, 所以只能用命令行

试过用vscode远程连接调, 把服务器调崩了, vscode插件安装服务器好像非常消耗内存

```
ps -ef|grep zbp # 找到进程的pid

dlv attach 进程id # 调试进程

b plugin\bilibili\bilibili.go:171 # 设置断点

c # 运行到断点

n # 下一行

step # 进入函数里面

```
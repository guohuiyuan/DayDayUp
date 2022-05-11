<!--
 * @Description: 
 * @Author: Alone
 * @Date: 2022-05-10 22:32:20
 * @LastEditors: Alone
 * @LastEditTime: 2022-05-10 23:00:30
-->
# vscode搭建go开发环境

1. 安装包下载地址: `https://golang.google.cn/` 
2. 安装go工具包时，直接下一步即可，注意安装路径中不要出现中文。
3. 在命令行输入`go version` 出现下图中画面即为安装成功

![](https://i0.hdslb.com/bfs/album/9e4342ed61b6cdc8db4d458306c39dcebff6fd90.png)

4. 安装好后会自动配置好Path环境变量和GOPATH环境变量,下面为GO开发时必须配置的环境变量

![](https://img-blog.csdnimg.cn/img_convert/fc7ade2ed942d8f5cd11e9e078d40468.png)

还需要向环境变量中配置GOROOT变量。

![](https://i0.hdslb.com/bfs/album/5dd1e425a1e0b3df1763f8f37e38c2da7e8b7b82.png)

所有的代码都要存放在GoPath目录下。

我此处还配置了GOBIN,路径为项目目录下的bin目录。

5. 设置代理加快访问速度：

cmd中输入下面代码：

```
GO111MODULE=on #使用Go的模块代理；  1.11版本。 on/off/auto  开启module
GOPROXY=https://goproxy.cn,direct #下载第三方包 配置代理(七牛云)
```

6. 在vscode中插件搜索Go进行安装，安装好后重启vscode
7. 手动安装go相关包：ctrl+shift+p -> go install/update tools
8. hello world示例代码：

```go
package main

import (
	"fmt"
)

func main() {
	fmt.Println("hello world")
}
```

集成终端中运行:`go run hello.go` 会输出hello world，此时环境搭建成功。

9. gopath下3个目录的作用：

- bin:go install 在编译项目时，生成的可执行文件会放到此目录
- pkg：go install 在编译项目时，生成的包文件会放到此目录
- src ：以后所有项目都要放在这个目录

上面的示例程序也是放在src文件夹中的。

## 参考

1. [实战：windows上基于vscode的golang环境搭建-2022.4.28(成功测试)](https://blog.csdn.net/weixin_39246554/article/details/124507926)
2. [手把手教你vscode配置golang开发环境的步骤](https://www.jb51.net/article/207157.htm)
3. [windows系统go的环境搭建(保姆式教程)](https://blog.csdn.net/ohmygodes/article/details/122646716)
4. [解决go项目编译报错go.mod: no such file or directory](https://blog.csdn.net/HYZX_9987/article/details/122922902)
5. [Golang的开始“Hello World”](https://juejin.cn/post/7038198669220266014)

[在搭环境的时候顺带解决了一下windows中Antimalware Service Executable占用内存过高的问题](https://blog.csdn.net/qq_41371349/article/details/106628372)
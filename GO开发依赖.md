开发依赖——使用第三库，包括依赖包的下载、更新、版本管理等等
## Go语言的常见构建模式
程序的构建是Go编译器会将代码、依赖包、配置文件等相关的东西都进行打包、编译、链接、最终形成一个可执行文件。而这个过程就需要Go编译器和依赖管理工具的支撑<br />![cd986fe068ef427cb987019cf4b8e0c6~tplv-k3u1fbpfcp-zoom-in-crop-mark_4536_0_0_0.webp](https://cdn.nlark.com/yuque/0/2023/webp/1462585/1683590347309-568fb795-63a1-470a-80ac-ed861a3a594e.webp#averageHue=%231f1f1f&clientId=u6eca097f-9072-4&from=ui&id=u3af040d2&originHeight=1235&originWidth=2892&originalType=binary&ratio=1.25&rotation=0&showTitle=false&size=74238&status=done&style=none&taskId=u40445ca1-cbb6-4652-ba93-4a687291193&title=)
## 模块
“模块”——多个Go包（package）的集合，将这些包作为一个整体一起进行版本控制<br />“模块”——记录了正确的依赖关系和依赖需求，在构建时会很便利<br />项目工程仓库、模块和包之间的关系：

- 项目工程仓库可以包含一个或者多个Go模块
- 每个模块可以包括一个或者多个Go包
- 每个包由单个目录中的一个或者多个Go源文件组成

模块必须有版本号
## 如何使用模块-go modules
### go.mod文件
```go
go mod init hello
```
执行上述命令，就在当前文件目录下创建一个go.mod文件，以定义Go模块本身
### 了解go.mod文件
```go
module hello

go 1.20
```
第一行，module指令，告诉Go你的模块的名字，这样当它在包中寻找import路径时，它就知道不要在其他地方寻找该包<br />go 1.20——告诉模块所针对的语言版本
```go
package main

import "fmt"

func main() {
    fmt.Println("Hello, Modules!")
}
```
package main package被命名为main，通常情况下，你可以用任何方式来命名包，当Go看到一个包被命名为main ，它就知道这个包应该被认为是一个二进制文件，应该被编译成一个可执行文件，而不是一个旨在用于其他程序的库。<br />main函数  是这个包中运行的第一个程序，为程序入口
## 向模块添加包
### 在一个同一个目录下
![捕获.PNG](https://cdn.nlark.com/yuque/0/2023/png/1462585/1683619535048-06a5b491-efed-4bd4-b12d-2bac8cdb7f34.png#averageHue=%23dbb585&clientId=u6eca097f-9072-4&from=ui&id=ub6bf49f8&originHeight=182&originWidth=317&originalType=binary&ratio=1.25&rotation=0&showTitle=false&size=7041&status=done&style=none&taskId=u09d56002-b982-4ba4-9873-d387357a7aa&title=)<br />目录结构中main文件夹中包括了mypackage包，他们在同一个目录下，这种情况下如何设置开发依赖？mypackage中不需要设置go.mod文件
```go
package main

import (
	"fmt"

	"hello/mypackage"
    //hello 是main模块的名字
)

func main(){
	mypackage.PrintMy()
}
```
### 不在同一个目录下
![捕获.PNG](https://cdn.nlark.com/yuque/0/2023/png/1462585/1683620137375-4e1f7e96-ae32-4b1f-a193-731a39b67cb8.png#averageHue=%23262424&clientId=u6eca097f-9072-4&from=ui&id=ud76b5de4&originHeight=449&originWidth=328&originalType=binary&ratio=1.25&rotation=0&showTitle=false&size=14184&status=done&style=none&taskId=uf07b152a-355d-407b-852d-d6b458350bf&title=)

- 导入包的时候，需要依赖的包也要modules初始化，即拥有一个自己的go.mod文件，内容如下：
```go
module demo

go 1.20
```

- 然后在main中导入依赖时，因为不在同一个目录中，所以需要使用require导入依赖，但是require是从远程仓库中拉取的，所以使用repalce替换拉取路径，从本地拉取
```go
module hello

go 1.20

require (
	demo v0.0.0
	test v0.0.0
)

replace (
	demo => ../pkg/demo
	test => ../pkg/test
)

```

- 上述步骤设置完成之后就可以使用import导入依赖了
```go
package main

import (
	
	"demo"

	_ "test"
)


func main(){
	demo.Text()

}
```

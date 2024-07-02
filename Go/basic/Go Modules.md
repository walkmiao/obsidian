# Go Modules

[TOC]

## 工作区和GO环境变量介绍

- **Go Workspace**

  Go工作区是Go 管理源文件、已编译的二进制文件 、用于稍后更快编译的缓存对象的一种方式。

  典型并且也被建议的情况是仅有一个Go工作区,尽管同时拥有多个工作区的情况是很有可能的。

  **GOPATH**变量对于一个工作区来说扮演着`root folder`的角色。

- **GOPATH**

  如果你是从其他编程语言转而学习GO，那么你可能在文件系统任意的放置你的源代码。

  但是Go tools对Go 源代码有着明确的位置要求。

  GOPATH是工作区的根目录，包含以下的目录：

  - src    放置源代码的目录诸如`.go`,`.c`,`.g`文件。不要与`$GOROOT`下的代码搞混淆了

  - pkg   放置已编译好的包的目录 如`.a`文件

  - bin    `go install`编译后二进制可执行文件将会存放在此目录。建议也将此目录也加入到系统的`$PATH`下，方便执行。

    如果未设置 你可能需要这样执行你已编译好的程序

    ```
    $GOPATH/bin/myapp
    ```

    当`$GOPATH/bin`加入到`$PATH`变量后就可以直接执行

    ```
    myapp
    ```

    

  像环境变量`PATH`一样，`GOPATH`变量是一个以`:`分隔的目录列表（在Windows下是`;`分隔符）

  Go 会在`GOPATH`变量设置的目录中寻找包。

  `go get`工具会将下载的包存放在`GOPAH`变量中的第一个目录

  **从GO 1.8开始，如果`GOPATH`变量未在系统中设置，它的默认值在Unix/Linux将是`$HOME/go`**,在Windows中则是`%USERPROFILE%/go`

  一些工具会假定`GOPATH`变量仅仅包含一个目录。

- **GOBIN**

  环境变量GOBIN是 `go install` 和 `go get`工具在构建`main packages`后放置二进制执行文件的目录。

  一般来说它会被设置成为 在系统`PATH`变量中的一个目录，这个安装后的执行文件能够被直接执行。

  如果没有设置GOBIN变量，那么默认其位置将是`$GOPATH/bin`目录

- **GOROOT**

  环境变量GOROOT是Go的安装目录。

  `$GOROOT千万不要和$GOPATH设置一样的目录`

  `$GOROOT`是Go标准库代码、编译器以及工具链所处的目录--它并不是我们的源代码。

  在Windows中GOROOT的默认值为`c:/go`,在Unix和Linux中为`/usr/local/go`。

  一般情况下我们需要将`$GOROOT/bin`配置到环境变量`$PATH`中，这样我们就能随处使用GO工具链。

  

  Linux设置GOROOT

  ```shell
  export GOROOT=~/go
  export PATH=$PATH:$GOROOT/bin
  ```

  

##  什么是GO MODULES

**Go Modules**机制从go版本`1.11`正式引入，在此之前Golang使用`GOROOT`和`GOPATH`来决定pkg包的位置。

### 模块

模块是一些相关的包组合在一起的版本化的单元。

每个模块本身都具有依赖其他

### 语义导入版本控制

所有的模块都必须遵循语义化版本规则：

![semantic import versioning](https://xuanwo.io/2019/05/27/go-modules/impver.png)

![](https://xuanwo.io/2019/05/27/go-modules/impver.png)

当主版本号大于等于`v2`时,这个模块的import path必须在尾部价格`/vN`(N是版本)

- 在go.mod 文件中   `module github.com/user/mod/v2`
- 在其他mod中需要require此模块的时候 `require github.com/user/v2 v2.0.0` 
- 在其他mod中import的时候 `import "github.com/user/mod/v2/mypkg"`

## 更新Module

- `go get -u`

  更新到最新的patch版本，比如我们从v1.0.0来更新，将更新到v1.0.1。如果有更高的版本v1.1.0将会优先获取v1.1.0，但不会更新到v2.0.0这种major版本

- `go get -u=patch`

  只更新最新的patch版本，如果从v1.0.0更新的话 只更新到v1.0.1，即使有更高的V1.1.0也不会去更新

- `go get package@version`

  更新到指定版本，比如`go get github.com/user/mod@v1.1.0`

## github的tags与module的关系

- **module major版本**

模块名称如果只是major版本譬如`github.com/walkmiao/mymod`、`github.com/walkmiao/mymod/v2`、`github.com/walkmiao/mymod/v3`等这样的形式，那么提交至远程仓库可以不用添加git tag，那么go mod会按照**Module 名称版本**比如`github.com/walkmiao/mymod/v3` 以这样的格式初始化版本号`vN.0.0-日期-commit号的前12位`

比如`v3.0.0-20190928152123-8706fe0badc3`

也就是说major版本是按照go mod中的mod 名称来寻找版本的，取决于模块名称是否末尾携带版本标识`github.com/walkmiao/mymod/vN`。与`git tag`是没有关系的

- **module major版本后的版本迭代**

如果在major之后需要迭代patch版本比如从v3.0.0到v3.0.1，则必须在提交模块代码时添加`git tag`，那么其他模块在`require`指定模块版本的时候先去仓库里寻找带有mod 版本的`git tag 集合`,从这些集合中找到符合require指定版本的模块。

> 这里我为了做测试，初始mod的版本就设置为v3. 正常情况下是初始的mod名称是没有版本号，在之后的版本迭代才会出现诸如v1.0.1 v2.0.0



## Go Module 命令

- `go mod -modcache`

  删除`$GOPATH/pkg/mod`下的module

- `go list -m all`

  列出当前模块依赖的所有模块

- `go list -m -json all`

  以json格式列出当前模块依赖的所有模块

- `go list -u -m all`

  列出当前模块依赖中可升级的模块

- `go get -u`

  升级所有依赖至最新版本

- `go get -u=patch`

  升级所有依赖至最新的修订版本

- `go mod tidy`

  清理未使用/生效的依赖
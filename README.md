# Dubbo Go Think
尝试着去熟悉 `dubbo-go` 的源码,同步进行熟悉的有 [Docker 使用](https://github.com/felix9ia/docker-study)
## 参考
[dubbo-go](https://github.com/apache/dubbo-go.git)
[dubbo-go-samples](https://github.com/apache/dubbo-go-samples.git)

[Dubbo-go 源码笔记](https://developer.aliyun.com/article/777450?spm=a2c6h.14164896.0.0.2235607ejLGQY1)

## 解读

源码的阅读顺序思路

先运行

- 运行基础的 `hello world` 源码
- 查看配置文件
- 开启各种依赖服务(如: zk, consul)
- 开启 `server` 服务端
- 通过 `client` 调用服务端
- 打印完整请求日志和回包

运行成功后

- 了解框架的设计模型
- 从配置文件解析开始, 自顶向下的阅读框架的调用栈
- 从 `server` 端的配置文件解析阅读到  `server`端的监听启动
- 从 `client`端的配置文件解析阅读到一次 `invoker Call` 调用

#### 环境

安装 `zookeeper` 

```shell
brew info zookeeper
brew install zookeeper
```

#### Hello world

目录组织

```shell
├── app # 源码
├── assembly # 可选的针对特定系统环境的 build 脚本
│   ├── bin
│   ├── common
│   ├── linux
│   ├── mac
│   └── windows
└── profiles # 配置文件	
    ├── dev
    ├── release
    └── test
```

分别切换到`server`和`client`下的`app`目录下,需要配置环境变量`CONF_PROVIDER_FILE_PATH` 和 `APP_LOG_CONF_FILE`, 当然也可以编写环境变量的配置脚本`builddev.sh`

```shell
 export CONF_PROVIDER_FILE_PATH="../profiles/dev/server.yml" 
 export APP_LOG_CONF_FILE="../profiles/dev/log.yml"
 export GOPROXY="http://goproxy.io"
 go run .
```

先运行`server`, 后运行`client`之后

`server`端会打印出:

```shell
[2020-11-23/23:52:33 main.(*UserProvider).GetUser: user.go: 51] req:[]interface {}{"A001"}
```

`client`端会收到如下响应:

```shell
[2020-11-23/23:42:59 main.main: client.go: 64] response result: &{A001 Alex Stocks 18 2020-11-23 23:42:59.442 +0800 CST}

```

这样表示成功运行.

#### 关键源码

`server`端的`user.go`包含以下的部分

- `init`函数

  服务端代码中最先被执行的部分

- 用户自定义的传输结构体

  用户数据的传输,示例中是`User`

- `rpc_service` 函数

  供`client`端调用的自定义 `rpc` 函数`GetUser`

下面是`dubbo`的官方设计图

![](https://ucc.alicdn.com/pic/developer-ecology/6b8f963c51b143deb25b3ba3e41d9b63.png)


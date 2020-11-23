# Dubbo go think
尝试着去熟悉 `dubbo-go` 的源码
## 参考
[dubbo-go](https://github.com/apache/dubbo-go.git)  
[dubbo-go-samples](https://github.com/apache/dubbo-go-samples.git)

[Dubbo-go 源码笔记](https://developer.aliyun.com/article/777450?spm=a2c6h.14164896.0.0.2235607ejLGQY1)

## 解读

源码的阅读方式

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

[Docker 使用](https://github.com/felix9ia/docker-study)

```shell
brew info zookeeper
brew install zookeeper
# 或者
docker search zookeeper
docker pull zookeeper


```



#### Hello world

目录组织

```
├── app								# 源码
├── assembly					# 可选的针对特定系统环境的 `build` 脚本
│   ├── bin
│   ├── common
│   ├── linux
│   ├── mac
│   └── windows
└── profiles						# 配置文件	
    ├── dev
    ├── release
    └── test
```

切换到`app`目录下


# 组件框架
`OlivOS`基于并行多组件的消息队列通信机制进行开发，基本架构如下。

```

                        *=================================*
                        |                                 | -------------- ...
                        |                                 |
                        |                                 | -------------- ...
     *--control_queue-> |      OlivOS.BootAPI.Entity      |
     |                  |                                 | -------------*
     |                  |                                 |              |
     |                  |                                 | -----------* |
...  |                  *=================================*            | |
 |   |                                                                 | |
 | <-+---logger_queue-- *=================================*            | |
 |   |                  | OlivOS.pluginAPI.shallow        | <-rx_queue-* |
 |   | <---tx_queue---- *=================================*              |
 |   |                                                                   |
 | <-+---logger_queue-- *=================================*              |
 |   |                  | OlivOS.nativeWinUIAPI.dock      | <-rx_queue---*
 |   | <---tx_queue---- *=================================*
 |   |
 | <-+---logger_queue-- *=================================*
 |   |                  | OlivOS.dodoLinkServerAPI.server | <-rx_queue---- ...
 |   | <---tx_queue---- *=================================*
 |   |
 | <-+---logger_queue-- *=================================*
 |   |                  | OlivOS.flaskServerAPI.server    | <-rx_queue---- ...
 |   | <---tx_queue---- *=================================*
 |   |
 |   |                   ... ...
 |   |
 *---+-----rx_queue---> *=================================*
     |                  | OlivOS.diagnoseAPI.logger       |
     | <---tx_queue---- *=================================*
     |
    ...

```

其中，OlivOS的各种功能组件都将以`OlivOS.**API`的方式提供给`OlivOS`进行调用，这些组件将在`OlivOS`启动时被加载，并基于预先配置的元数据进行实例化。  

各类不同的功能分布在不同的组件中，这些组件可以以进程或线程的方式运行，他们都有一个共同的父类`OlivOS.API.Proc_templet`，这个父类将为组件提供一些基础的功能，比如：  

+ 基础的启动与停止
+ 基础的日志记录
+ 基础的控制消息队列通信

其中有一些比较关键的组件承担了更重要的功能，例如：  

+ `OlivOS.pluginAPI.shallow`：插件加载器，负责加载插件并提供插件的调用接口
+ `OlivOS.nativeWinUIAPI.dock`：原生窗口管理器，负责管理原生窗口的生命周期
+ `OlivOS.diagnoseAPI.logger`：日志记录器，负责记录日志并提供日志的调用接口

这些核心框架都分别具有一个专属接收消息队列、一个公共控制消息发送队列以及一个公共日志消息发送队列，这些消息队列将在`OlivOS`启动时被实例化。  

特别的，`OlivOS.diagnoseAPI.logger`的接收队列就是公共日志消息发送队列，并且它的日志接口被重写过，将不会经过日志队列，这样做的目的是为了避免日志记录器的消息队列被阻塞和节约资源。  

此外的大部分插件间的通信需要通过公共控制消息发送队列进行，通信支持一对一和一对多，路由过程最终是由`OlivOS.bootAPI.Entity`中的主循环完成的，所有的消息都会被主循环接收，然后转发到符合条件的模块所对应的专属接收消息队列中去。  

各个组件分别具有一个类型名称和一个实际名称，类型名称由元数据和源代码指定，实际名称由元数据指定，实际名称将作为组件的唯一标识符，用于组件间的通信。所以对于一些需要多次启动的组件，实际名称还会加上一些后缀，公共控制消息发送队列的路由将依据这些后缀实现部分的路由，例如：  

+ `={bothash}`：指定该模块所对应的bot的hash
+ `+{subname}`：指定该模块所对应的子模块名称
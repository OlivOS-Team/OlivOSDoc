# 文件结构
本项目的文件结构如下, 将分别介绍各个目录对应的功能
```
.
│  main.py
├─conf
├─hook
├─OlivOS
│  │  __init__.py
│  │  hook.py
│  ├─adapter
│  ├─core
│  │  ├─boot
│  │  ├─core
│  │  ├─info
│  │  ├─inlineData
│  │  ├─L10N
│  │  └─web
│  ├─libBooter
│  ├─nativeGUI
│  ├─thirdPartyModule
│  └─userModule
├─plugin
│  ├─app
│  ├─conf
│  └─data
├─resource
└─script
```

## main.py
`main.py`是OlivOS的程序入口，它将完成`OlivOS`的实例化并启动OlivOS。  

## OlivOS
这里是`OlivOS`的主要部分，其中实现了发布版本`OlivOS`的大部分功能，在这个目录下以库的方式提供调用。  

## OlivOS/adapter
`OlivOS/adapter`目录下存放了一些适配器，这些适配器用于为`OlivOS`实现对接各种平台的协议支持。  

通常，一个完整的协议包含一个`**SDK.py`与一个`**API.py`，例如`OlivOS/adapter/telegram/telegramSDK.py`与`OlivOS/adapter/telegram/telegramPollServerAPI.py`。  

`**SDK.py`用于实现对应协议的SDK，其中将包含各种参考官方文档进行编程的对象与算法。  

`**API.py`用于实现协议的运行时，并提供给`OlivOS`进行调用的`API`，通常会提供一个`OlivOS组件`；原则上`**API.py`要确保尽量使用`**SDK.py`中的方法，不同适配器间要避免交叉调用。  

其中`**SDK.py`通常以`{协议名}SDK.py`的方式命名，如：  

+ `OlivOS/adapter/telegram/telegramSDK.py`
+ `OlivOS/adapter/discord/discordSDK.py`
+ ...

`**API.py`通常以`{协议名}{连接方式}ServerAPI.py`的方式命名，连接方式通常用于直观表明协议的主要连接方式，目前基于`websocket`的会使用`Link`，基于`长轮询`的会使用`Poll`，如：  

+ `OlivOS/adapter/telegram/telegramPollServerAPI.py`
+ `OlivOS/adapter/discord/discordLinkServerAPI.py`
+ ...

部分早期的模块不遵守这个命名规则，比如`OlivOS/adapter/onebotV11/flaskServerAPI.py`。  

## OlivOS/core
`OlivOS/core`目录下为`OlivOS`的核心组件，这些组件将为`OlivOS`提供核心功能。

### OlivOS/core/boot
`OlivOS/core/boot`目录下为`OlivOS`的启动实例相关组件，`OlivOS`启动时调用的接口直接来自于此。  

### OlivOS/core/core
`OlivOS/core/boot`目录下为`OlivOS`的核心组件，一些核心的功能诸如核心对象、插件加载器、账号管理机、消息抽象语法树、元数据、日志模块，都会放在这里。  

### OlivOS/core/info
`OlivOS/core/info`目录下为`OlivOS`的全局信息，诸如版本信息、版本兼容性、资源文件源，等信息。  

### OlivOS/core/inlineData
`OlivOS/core/inlineData`目录下为`OlivOS`的内联数据，这些数据将在`OlivOS`启动时被加载，通常是一些静态数据，比如软件图标文件数据。  

### OlivOS/core/L10N
`OlivOS/core/L10N`目录下为`OlivOS`的本地化组件，`L10N`是`localization`的缩写，这些组件将为`OlivOS`提供本地化支持。  

### OlivOS/core/web
`OlivOS/core/web`目录下为`OlivOS`的网络支持库，这些组件将为`OlivOS`提供诸如资源文件更新、代理识别等，网络相关服务支持。  

## OlivOS/libBooter
`OlivOS/libBooter`目录下为`OlivOS`的依赖二进制库的启动器组件，这些组件将为`OlivOS`提供诸如`go-cqgttp`、`walle-q`等二进制文件的挂载启动功能。  

## OlivOS/nativeGUI
`OlivOS/nativeGUI`目录下为`OlivOS`的Win平台原生GUI组件。  

## OlivOS/thirdPartyModule
`OlivOS/thirdPartyModule`目录下为`OlivOS`的第三方模块，原则上，不基于Pypi包管理且需要引入的第三方模块都要在这里引入。  

## OlivOS/userModule
`OlivOS/userModule`目录下为`OlivOS`的社区支持模块，这部分是社区贡献的非核心功能性模块。  

## plugin
`plugin`目录下为`OlivOS`的插件目录。  

### plugin/app
`plugin/app`目录下为`OlivOS`的插件加载目录，在这里的插件将被加载。

### plugin/conf
`plugin/conf`目录下为`OlivOS`的插件配置目录，存放插件的配置文件。

### plugin/data
`plugin/data`目录下为`OlivOS`的插件数据目录，存放插件的数据文件。

## script
`script`目录下存放了一些脚本文件，这些脚本文件可以帮助开发者快速进行一些操作，例如`start.bat`可以帮助开发者快速启动OlivOS实例。  

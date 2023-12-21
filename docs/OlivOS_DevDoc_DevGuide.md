# 开发指引
本文档旨在为开发者提供开发指引，帮助开发者快速上手OlivOS的开发。


## 文件结构
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

### main.py
`main.py`是OlivOS的程序入口，它将完成`OlivOS`的实例化并启动OlivOS。  

### OlivOS
这里是`OlivOS`的主要部分，其中实现了发布版本`OlivOS`的大部分功能，在这个目录下以库的方式提供调用。  

### OlivOS/adapter
`OlivOS/adapter`目录下存放了一些适配器，这些适配器用于为`OlivOS`实现对接各种平台的协议支持。  

通常，一个完整的协议包含一个`**SDK.py`与一个`**API.py`，例如`OlivOS/adapter/telegram/telegramSDK.py`与`OlivOS/adapter/telegram/telegramPollServerAPI.py`。  

`**SDK.py`用于实现对应协议的SDK，其中将包含各种参考官方文档进行编程的对象与算法。  

`**API.py`用于实现协议的运行时，并提供给`OlivOS`进行调用的`API`，通常会提供一个`OlivOS组件`；原则上`**API.py`要确保尽量使用`**SDK.py`中的方法，不同适配器间要避免交叉调用。  

其中`**SDK.py`通常以`{协议名}SDK.py`的方式命名，如：  

+ `OlivOS/adapter/telegram/telegramSDK.py`
+ `OlivOS/adapter/discord/discordSDK.py`
+ ...

`**API.py`通常以`{协议名}{连接方式}ServerAPI.py`的方式命名，连接方式通常取决于协议的连接方式，目前基于`websocket`的会使用`Link`，基于`长轮询`的会使用`Poll`，如：  

+ `OlivOS/adapter/telegram/telegramPollServerAPI.py`
+ `OlivOS/adapter/discord/discordLinkServerAPI.py`
+ ...


### OlivOS/core
`OlivOS/core`目录下存放了`OlivOS`的核心组件，这些组件将为`OlivOS`提供核心功能。

### script
`script`目录下存放了一些脚本文件，这些脚本文件可以帮助开发者快速进行一些操作，例如`start.bat`可以帮助开发者快速启动OlivOS实例。  














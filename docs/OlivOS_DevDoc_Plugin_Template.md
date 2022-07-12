# 插件模板

## 插件模板

### 综述

请配合[插件模板](https://github.com/OlivOS-Team/OlivOSPluginTemplate)进行阅读

OlivOS插件通过`importlib`进行动态加载，加载后将具有与OlivOS同级的命名空间，命名空间之间符合Python的覆盖机制，这意味着你可以对其他插件甚至OlivOS自身进行注入式开发。

在源码开发模式下，插件是一个被放置于`plugin/app`目录下的文件夹（Python模块）  
一个通过OlivOS的插件托盘的检查并被正常加载的插件应当具有以下文件

| 文件名称 | 功能 |
|:--:|:---|
| `app.json` | 自述文件 |
| `__init__.py` | 加载入口 |
| `main.py` | 事件回调入口 |

### 自述文件

本文件的内容用于向OlivOS插件托盘表明插件自身相关的信息，其内容应当参考[app.json](https://github.com/OlivOS-Team/OlivOSPluginTemplate/blob/main/OlivOSPluginTemplate/app.json)

具体通常如下

```json
{
    "name" : "OlivOS插件默认模板",
    "author" : "OlivOS-Team",
    "namespace" : "OlivOSPluginTemplate",
    "priority" : 30000,
    "svn" : 1,
    "compatible_svn" : 94,
    "message_mode" : "olivos_string",
    "support" : [
        {
            "sdk" : "onebot",
            "platform" : "qq",
            "model" : "all"
        },
        {
            "sdk" : "all",
            "platform" : "all",
            "model" : "all"
        }
    ]
}
```

其中包含的条目有如下作用

| 文件名称 | 类型 | 描述 |
|:--:|:--:|:---|
| name | string | 插件名称 |
| author | string | 插件作者 |
| namespace | string | 命名空间 |
| priority | number(int) | 优先级，值越小等级越高 |
| svn | number(int) | 版本号<br/>由插件自行递增 |
| compatible_svn | number(int) | 插件接口版本号<br/>表明了当前插件基于哪个版本的OlivOS设计 |
| message_mode | string | 插件消息模式 |
| support | list | 插件支持平台开关 |

`support`字段中包含的结构体应当包含如下

| 文件名称 | 类型 | 描述 |
|:--:|:--:|:---|
| sdk | string | 插件平台SDK |
| platform | string | 插件平台 |
| model | string | 插件平台模式 |


### 加载入口

该文件是Python模块加载时的标准文件，你应当在本文件中编写相关代码以确定加载步骤中的命名空间关系，以及其他操作


### 事件回调入口

该文件提供了插件命名空间下的`main`空间，这是插件后续业务加载所需的主要空间，详情请参考[框架事件综述](OlivOS_DevDoc_Event.md)


### 插件打包发布格式 | OPK（OlivOS Plugin Package）

OPK（OlivOS Plugin Package）是一种由仑质提出的，适用于OlivOS的单文件插件格式，其本质是源码状态的OlivOS插件目录的**zip**格式压缩包，并在完成压缩后修改后缀名`.zip`为`.opk`，这是一种通用的打包形式，参考了`Office`与`Java`等项目的设计。  
其详细打包过程请参考[OlivOS插件模板的CI过程](https://github.com/OlivOS-Team/OlivOSPluginTemplate/blob/main/.github/workflows/ci.yml)。


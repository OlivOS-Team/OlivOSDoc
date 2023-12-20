# 账号管理

## 账号系统介绍

### 介绍
总的来说，`OlivOS`是一个可以同时支持多平台与多账号的交互栈框架，对于`OlivOS`来说，每一个账号都是一个独立的连接实例，你可以通过配置来对其进行分配。

## 账号配置文件

### 介绍
账号配置文件通常为`conf/account.json`，这是一个`JSON`格式的配置文件，你可以通过修改这个文件来对账号进行配置，但是在`Win`平台上，你还可以用启动时会自动启动的账号编辑器来进行管理。  

### 文件格式
```json
{
    "account": [
        {
            "id": 114514,
            "password": "THISISNOTYOURPASSWORD",
            "sdk_type": "kaiheila_link",
            "platform_type": "kaiheila",
            "model_type": "default",
            "server": {
                "auto": true,
                "type": "websocket",
                "host": "THISISNOTYOURHOST",
                "port": 2333,
                "access_token": "THISISNOTYOURTOKEN"
            },
            "extends": {},
            "debug": false
        }
    ]
}
```

| 名称 | 类型 | 解释 | 缺省 |
|:---|:--:|:---|:--:|
| id | number<br/>string | 机器人ID，通常用于机器人进行登录和自我辨识 | - |
| password | string | 机器人密码，通常用于登录，有时也用于储存应用密钥 | - |
| sdk_type | string | 消息源所基于SDK<br/>例如`qq`、`telegram`、`kaiheila` | - |
| platform_type | string | 消息源平台实际平台<br/>例如`onebot`、`telegram_poll`、`kaiheila_link` | - |
| model_type | string | 消息源所基于模块模式<br/>例如`default` | - |
| server | object | 服务器信息 | - |
| server.auto | bool | 服务器配置模式 | - |
| server.type | string | 服务对接类型 | - |
| server.host | string | 服务器地址 | - |
| server.port | number | 服务器接口 | - |
| server.access_token | string | 服务器鉴权TOKEN | - |
| extends | object | 扩展字段，某些账号类型会使用 | - |



这些条目并不是大多数时候都在所有平台都有用，许多平台只需要一些特定的条目，后文将会对这些对应情况进行说明。  
你需要关注的是`sdk_type`、`platform_type`、`model_type`这些条目，它们将直接决定`OlivOS`对待该条账号配置的模式。  


## 账号类型条目对应
以下内容将会以`account`条目下数组中的成员为例进行说明。  
其中`固定配置`代表了只有在这些配置项被配置的时候才会被指定为该种连接，而`所需配置`代表了在这种连接模式下要配置的最小规模，没有被列举的条目则表示可选，通常也不会发挥作用。  
通常，这些组合方式已经被配置为了`OlivOS`各种账号类型，你可以在账号编辑器中选择它们。  


### onebotV11/Http

这种连接方式适配了OnebotV11协议的Http接口，这种接口通常用于连接`go-cqhttp`、`OpenShamrock`等协议端。  
OlivOS同样提供了对于这其中部分协议端的更进一步封装，这部分会在后文中提及，此处的连接方式是最为通用的连接方式。  

**固定配置**

| 名称 | 配置 |
|:---|:---|
| sdk_type | "qq" |
| platform_type | "onebot" |
| model_type | "default" |
| server.auto | false |
| server.type | "post" |

**所需配置**

| 名称 | 填写说明 |
|:---|:---|
| id | 填写机器人账号ID |
| server.host | 需要填写协议端的地址 |
| server.port | 需要填写协议端的端口 |
| server.access_token | 视情况填写，但最好不要空置 |


### onebotV11/Http/Shamrock

此连接方式是`onebotV11/Http`的变体，它适配了`OpenShamrock`协议端，由于`OpenShamrock`未能完全实现`onebotV11`，且由于其基于的安卓Java原生中的`orjson`存在扩展后的JSON格式，所以有了本账号类型，以适配各种`OpenShamrock`独有的情形。

**固定配置**

| 名称 | 配置 |
|:---|:---|
| sdk_type | "qq" |
| platform_type | "onebot" |
| model_type | "shamrock_default" |
| server.auto | false |
| server.type | "post" |

**所需配置**

| 名称 | 填写说明 |
|:---|:---|
| id | 填写机器人账号ID |
| server.host | 需要填写协议端的地址 |
| server.port | 需要填写协议端的端口 |
| server.access_token | 视情况填写，但最好不要空置 |


### onebotV12/正向WS

这种连接方式适配了通用的`OnebotV12协议`的`正向Websocket`，这种接口通常用于连接`Walle-q`、`ComWeChatBot`等协议端。  
OlivOS同样提供了对于这其中部分协议端的更进一步封装，这部分会在后文中提及，此处的连接方式是最为通用的连接方式。  

**固定配置**

| 名称 | 配置 |
|:---|:---|
| sdk_type | "qq" |
| platform_type | "onebot" |
| model_type | "onebotV12" |
| server.auto | false |
| server.type | "websocket" |

**所需配置**

| 名称 | 填写说明 |
|:---|:---|
| id | 填写机器人账号ID |
| server.host | 需要填写协议端的地址 |
| server.port | 需要填写协议端的端口 |
| server.access_token | 视情况填写，但最好不要空置 |

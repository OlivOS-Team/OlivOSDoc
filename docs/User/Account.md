# 账号管理

## 账号系统介绍

总的来说，`OlivOS`是一个可以同时支持多平台与多账号的交互栈框架，对于`OlivOS`来说，每一个账号都是一个独立的连接实例，你可以通过配置来对其进行分配。

## 账号配置文件

账号配置文件通常为`conf/account.json`，这是一个`JSON`格式的配置文件，你可以通过修改这个文件来对账号进行配置，但是在`Win`平台上，你还可以用启动时会自动启动的账号编辑器来进行管理，账号编辑器将会按照后文中账号类型与条目对应的关系来自动帮你填写必要的信息，并为你提供填写其余部分的输入框，并帮助你检查是否填写妥当。  

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
需要注意的是，这种对接模式会生成以下两个通信：  

+ 一个`OlivOS`向`协议端`方向的`正向HTTP`（`协议端`作为服务器），这个方向的监听端口由`协议端`开启，需要在账号信息中配置  
+ 一个`协议端`向`OlivOS`方向的`反向HTTP`（`OlivOS`作为服务器），这个方向的的监听端口由`OlivOS`开启  

如果需要指定配置则需要使用`conf/basic.json`配置文件进行配置，默认为`55001`，如果被占用则会自行寻找一个闲置端口，请关注日志  
你需要在`协议端`配置`http://<ip>:<port>/OlivOSMsgApi/<platform>/<sdk>/<model>`的上报地址  
如，`http://127.0.0.1:55001/OlivOSMsgApi/qq/onebot/default`  

**固定配置**

| 名称 | 配置 |
|:---|:---|
| platform_type | "qq" |
| sdk_type | "onebot" |
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

此方法对接`Shamrock`需要使用其自带的`OnebotV11`的功能，其中的：  

+ `回调HTTP地址`对应`反向HTTP`地址  
+ `主动HTTP端口`对应`正向HTTP`端口  

![001](/User/img/User_Account_001.png)

**固定配置**

| 名称 | 配置 |
|:---|:---|
| platform_type | "qq" |
| sdk_type | "onebot" |
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
| platform_type | "qq"<br/>"wechat" |
| sdk_type | "onebot" |
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


### RED协议

这种连接方式适配了`Chronocat`的`RED`协议，协议其实只是`QQNT`中hook数据的简单封装，OlivOS对此进行了适配。  

**固定配置**

| 名称 | 配置 |
|:---|:---|
| platform_type | "qq" |
| sdk_type | "onebot" |
| model_type | "red" |
| server.auto | false |
| server.type | "websocket" |

**所需配置**

| 名称 | 填写说明 |
|:---|:---|
| id | 填写机器人账号ID |
| server.host | 需要填写协议端的WS地址 |
| server.port | 需要填写协议端的WS端口 |
| server.access_token | 视情况填写，但最好不要空置 |
| extends.http-path | 需要填写后续计划支持的协议端的HTTP地址<br/>But……Chronocat停止维护了<br/>所以这东西现在没用 |


### OPQBot/正向WS

这种连接方式适配了`OPQBot`的正向Websocket协议。  
请注意，OPQBot（原IOTQQ）基于远程协议端实现，可以基本理解为，在远程服务器上为你的账号分配一个QQ虚拟机，而OlivOS对接的OPQBot只是一个连接这个虚拟机的客户端，本地几乎没有什么东西，它需要将你的登录凭证上交至远端的OPQBot官方（与OlivOS无关的第三方），且OPQBot为闭源框架，该过程无法监督，所以你的账号几乎一定会面临安全风险（比如框架跑路后可能报废）；对于这类第三方的协议框架对接方式，如果出现这种情况，**OlivOS官方将不会对此负责，也无法做出任何补偿**，请**仔细斟酌**后再考虑使用该种登录方案，如果无法接受，**OlivOS将同样为你提供其它更安全的登陆方式，OPQBot只是OlivOS已经对接的20多种对接方式中的一种**。  

对接此方法需要一个`Token`，你需要加入官方群进行`Token`申请和每日`签到`续签这个`Token`，这是`OPQ`官方要求的避免滥用的必要措施。  

**固定配置**

| 名称 | 配置 |
|:---|:---|
| platform_type | "qq" |
| sdk_type | "onebot" |
| model_type | "opqbot_default" |
| server.auto | false |
| server.type | "websocket" |

**所需配置**

| 名称 | 填写说明 |
|:---|:---|
| id | 填写机器人账号ID |
| server.host | 需要填写协议端的WS地址 |
| server.port | 需要填写协议端的WS端口 |


### QQ/GoCq
这种连接方式会由`OlivOS`自动接管`go-cqhttp`的启动流程。  

**固定配置**

| 名称 | 配置 |
|:---|:---|
| platform_type | "qq" |
| sdk_type | "onebot" |
| model_type | 详见可变配置 |
| server.auto | true |
| server.type | "post" |

**可变配置**

| 账号类型 | model_type |
|:---|:---|
| QQ/GoCq/默认 | "gocqhttp_show" |
| QQ/GoCq/安卓手机 | "gocqhttp_show_Android_Phone" |
| QQ/GoCq/安卓平板 | "gocqhttp_show_Android_Pad" |
| QQ/GoCq/安卓手表 | "gocqhttp_show_Android_Watch" |
| QQ/GoCq/iPad | "gocqhttp_show_iPad" |
| QQ/GoCq/iMac | "gocqhttp_show_iMac" |
| QQ/GoCq/旧 | "gocqhttp_show_old" |

**所需配置**

| 名称 | 填写说明 |
|:---|:---|
| id | 填写机器人账号ID |
| password | 填写机器人账号密码 |


### QQ/WQ
这种连接方式会由`OlivOS`自动接管`walle-q`的启动流程。  

**固定配置**

| 名称 | 配置 |
|:---|:---|
| platform_type | "qq" |
| sdk_type | "onebot" |
| model_type | 详见可变配置 |
| server.auto | true |
| server.type | "websocket" |

**可变配置**

| 账号类型 | model_type |
|:---|:---|
| QQ/Wq/默认 | "walleq_show" |
| QQ/Wq/安卓手机 | "walleq_show_Android_Phone" |
| QQ/Wq/安卓平板 | "walleq_show_Android_Pad" |
| QQ/Wq/安卓手表 | "walleq_show_Android_Watch" |
| QQ/Wq/iPad | "walleq_show_iPad" |
| QQ/Wq/iMac | "walleq_show_iMac" |
| QQ/Wq/旧 | "walleq_show_old" |

**所需配置**

| 名称 | 填写说明 |
|:---|:---|
| id | 填写机器人账号ID |
| password | 填写机器人账号密码 |


### 微信/ComWeChat
这种连接方式会由`OlivOS`自动接管`ComWeChatBot`的启动流程。  

**固定配置**

| 名称 | 配置 |
|:---|:---|
| platform_type | "wechat" |
| sdk_type | "onebot" |
| model_type | "ComWeChatBotClient" |
| server.auto | true |
| server.type | "websocket" |

**所需配置**

| 名称 | 填写说明 |
|:---|:---|
| id | 填写机器人账号ID |
| password | 填写机器人账号密码 |


### QQ/OPQ
这种连接方式会由`OlivOS`自动接管`OPQBot`的启动流程。  
请注意，OPQBot（原IOTQQ）基于远程协议端实现，可以基本理解为，在远程服务器上为你的账号分配一个QQ虚拟机，而OlivOS对接的OPQBot只是一个连接这个虚拟机的客户端，本地几乎没有什么东西，它需要将你的登录凭证上交至远端的OPQBot官方（与OlivOS无关的第三方），且OPQBot为闭源框架，该过程无法监督，所以你的账号几乎一定会面临安全风险（比如框架跑路后可能报废）；对于这类第三方的协议框架对接方式，如果出现这种情况，**OlivOS官方将不会对此负责，也无法做出任何补偿**，请**仔细斟酌**后再考虑使用该种登录方案，如果无法接受，**OlivOS将同样为你提供其它更安全的登陆方式，OPQBot只是OlivOS已经对接的20多种对接方式中的一种**。  

对接此方法需要一个`Token`，你需要通过官方渠道获取`Token`申请和每日`签到`续签这个`Token`，这是`OPQ`官方要求的避免滥用的必要措施。  
详情请参考[说明](/User/OPQ)  

**固定配置**

| 名称 | 配置 |
|:---|:---|
| platform_type | "qq" |
| sdk_type | "onebot" |
| model_type | 详见可变配置 |
| server.auto | true |
| server.type | "websocket" |

**可变配置**

| 账号类型 | model_type |
|:---|:---|
| QQ/OPQ/默认 | "opqbot_auto" |
| QQ/OPQ/指定端口 | "opqbot_port" |
| QQ/OPQ/指定端口/旧 | "opqbot_port_old" |

**所需配置**

| 名称 | 填写说明 |
|:---|:---|
| id | 填写机器人账号ID |
| server.port | 需要填写协议端的WS端口<br/>`QQ/OPQ/默认`中可随意填 |
| server.access_token | 填写从`OPQBot`官方获取的Token |


### KOOK
这种连接方式对接了KOOK开放平台的官方机器人接口。  
KOOK官方的相关文档可以在[这里](https://developer.kookapp.cn/)找到。  
其中消息兼容模式将会以纯文本的方式发送消息，这可以解决某些用户遇到的权限不足的问题。  

**固定配置**

| 名称 | 配置 |
|:---|:---|
| platform_type | "kaiheila" |
| sdk_type | "kaiheila_link" |
| model_type | 详见可变配置 |
| server.auto | true |
| server.type | "websocket" |

**可变配置**

| 账号类型 | model_type |
|:---|:---|
| KOOK | "default" |
| KOOK/消息兼容 | "text" |

**所需配置**

| 名称 | 填写说明 |
|:---|:---|
| server.access_token | 填写从KOOK官方获取的Token |


### 米游社/大别野
这种连接方式对接了米游社大别野开放平台的官方机器人接口。  
米游社大别野相关的文档可以在[这里](https://webstatic.mihoyo.com/vila/bot/doc/)找到。  

**固定配置**

| 名称 | 配置 |
|:---|:---|
| platform_type | "mhyVila" |
| sdk_type | "mhyVila_link" |
| model_type | 详见可变配置 |
| server.auto | true |
| server.type | "websocket" |

**可变配置**

| 账号类型 | model_type |
|:---|:---|
| 米游社/大别野/公域 | "public" |
| 米游社/大别野/私域 | "private" |
| 米游社/大别野/沙盒 | "sandbox" |

**所需配置**

| 名称 | 填写说明 |
|:---|:---|
| id | 填写从米游社官方获取的Bot_Id |
| password | 填写从米游社官方获取的Secret |
| server.access_token | 填写从米游社官方获取的Pub_Key |
| server.port | 机器人所应用的别野号<br/>只有沙盒模式下需要填写特定别野号<br/>上架后可以填写为`0` |


### B站直播间
这种连接方式对接了B站直播间的弹幕系统。  
`B站直播间/游客`将会以游客方式对接，这种方式下将会只能浏览弹幕，不能发送消息。  
`B站直播间/登录`将会进行二维码登录，这种方式可以正常发送消息，但由于弹幕20字数的限制，将会把消息分割发送。  

**固定配置**

| 名称 | 配置 |
|:---|:---|
| platform_type | "biliLive" |
| sdk_type | "biliLive_link" |
| model_type | 详见可变配置 |
| server.auto | true |
| server.type | "websocket" |

**可变配置**

| 账号类型 | model_type |
|:---|:---|
| B站直播间/游客 | "default" |
| B站直播间/登录 | "login" |

**所需配置**

| 名称 | 填写说明 |
|:---|:---|
| server.access_token | 填写所要对接的直播间ID |


### QQ官方/V1
这种连接方式对接了QQ官方的开放平台机器人接口。  
QQ官方开放平台的文档可以在[这里](https://bot.q.qq.com/wiki/)找到。  
所采用的机器人接口为V1版本，这种接口已经被官方升级为V2，但由于其向下兼容性较好，仍然可以使用，所以仍然保留。  

**固定配置**

| 名称 | 配置 |
|:---|:---|
| platform_type | "qqGuild" |
| sdk_type | "qqGuild_link" |
| model_type | 详见可变配置 |
| server.auto | true |
| server.type | "websocket" |

**可变配置**

| 账号类型 | model_type |
|:---|:---|
| QQ官方/公域/V1 | "public" |
| QQ官方/私域/V1 | "private" |

**所需配置**

| 名称 | 填写说明 |
|:---|:---|
| id | 填写从QQ官方获取的AppID |
| server.access_token | 填写从QQ官方获取的机器人令牌 |


### QQ官方/V2
这种连接方式对接了QQ官方的开放平台机器人接口。  
QQ官方开放平台的文档可以在[这里](https://bot.q.qq.com/wiki/)找到。  
所采用的机器人接口为V2版本，一些新的功能例如`QQ群官方机器人`需要这个版本的接口对接。  
基于文档，不同的账号类型对应不同的`intents`，你可以在[这里](https://bot.q.qq.com/wiki/develop/api-v2/dev-prepare/interface-framework/event-emit.html#websocket-%E6%96%B9%E5%BC%8F)查看相关说明。  
其对应关系如下：  

| 账号类型 | intents |
|:---|:---|
| QQ官方/公域/V2 | GUILDS<br/>DIRECT_MESSAGE<br/>PUBLIC_GUILD_MESSAGES<br/>PUBLIC_QQ_MESSAGES |
| QQ官方/公域/V2/纯频道 | GUILDS<br/>DIRECT_MESSAGE<br/>PUBLIC_GUILD_MESSAGES |
| QQ官方/公域/V2/指定intents | 指定 |
| QQ官方/私域/V2 | GUILDS<br/>DIRECT_MESSAGE<br/>GUILD_MESSAGES |
| QQ官方/私域/V2/指定intents | 指定 |

其中，`QQ官方/公域/V2/指定intents`和`QQ官方/私域/V2/指定intents`可以直接指定`intents`，这可以帮助你更好的规划Bot的部署。  

**固定配置**

| 名称 | 配置 |
|:---|:---|
| platform_type | "qqGuild" |
| sdk_type | "qqGuildv2_link" |
| model_type | 详见可变配置 |
| server.auto | true |
| server.type | "websocket" |

**可变配置**

| 账号类型 | model_type |
|:---|:---|
| QQ官方/公域/V2 | "public" |
| QQ官方/公域/V2/纯频道 | "public_guild_only" |
| QQ官方/公域/V2/指定intents | "public_intents" |
| QQ官方/私域/V2 | "private" |
| QQ官方/私域/V2/指定intents | "private_intents" |

**所需配置**

| 名称 | 填写说明 |
|:---|:---|
| id | 填写从QQ官方获取的AppID |
| server.access_token | 填写从QQ官方获取的AppSecret |


### Telegram
这种连接方式对接了Telegram官方的开放平台机器人接口。  
Telegram官方的相关文档可以在[这里](https://core.telegram.org/bots/api)找到。  
根据相关[文档](https://core.telegram.org/bots/features#botfather)，Telegram的机器人需要通过一个官方机器人[@botfather](https://t.me/botfather)来创建。  

**固定配置**

| 名称 | 配置 |
|:---|:---|
| platform_type | "telegram" |
| sdk_type | "telegram_poll" |
| model_type | "default" |
| server.auto | true |
| server.type | "post" |

**所需配置**

你会从官方获取到一个格式类似如下的字符串，它与`id`、`server.access_token`字段的对应关系如下。  


```
11451419198100:xxxxxxxxxxxxxxxxxxxxxxxxxx
|<----id---->|                           
|<---------server.access_token--------->|
```

| 名称 | 填写说明 |
|:---|:---|
| id | 填写从Telegram官方获取的ID |
| server.access_token | 填写从Telegram官方获取的TOKEN |


### Discord
这种连接方式对接了Discord官方的开放平台机器人接口。  
Discord官方的相关文档可以在[这里](https://discord.com/developers/docs/intro)找到。  

**固定配置**

| 名称 | 配置 |
|:---|:---|
| platform_type | "discord" |
| sdk_type | "discord_link" |
| model_type | "default" |
| server.auto | true |
| server.type | "websocket" |

**所需配置**

| 名称 | 填写说明 |
|:---|:---|
| server.access_token | 填写从Discord官方获取的TOKEN |


### 渡渡语音/Dodo
这种连接方式对接了Dodo官方的开放平台机器人接口。  
Dodo官方的相关文档可以在[这里](https://open.imdodo.com/)找到。  
提供了`V1`、`V2`两个版本接口的对接，两个版本的接口在功能上没有区别，但是在ID对应关系上有一些区别，虽然Dodo官方提供了映射对应的接口，但是这个接口与`V1`版本接口的生命周期一致，并不能永久的保证配合`V2`接口的使用，因而这实际上是官方自上而下的破坏性更新，`OlivOS`在此特别区分两个版本。  

**固定配置**

| 名称 | 配置 |
|:---|:---|
| platform_type | "dodo" |
| sdk_type | "dodo_link" |
| model_type | 详见可变配置 |
| server.auto | true |
| server.type | "websocket" |

**可变配置**

| 账号类型 | model_type |
|:---|:---|
| 渡渡语音/Dodo/V1 | "v1" |
| 渡渡语音/Dodo/V2 | "default" |

**所需配置**

| 名称 | 填写说明 |
|:---|:---|
| id | 填写从Dodo官方获取的BotID |
| server.access_token | 填写从Dodo官方获取的Bot私钥 |


### Fanbook
这种连接方式对接了Fanbook官方的开放平台机器人接口。  
Fanbook官方的相关文档可以在[这里](https://open.fanbook.mobi/document/manage/doc/)找到。  

**固定配置**

| 名称 | 配置 |
|:---|:---|
| platform_type | "fanbook" |
| sdk_type | "fanbook_poll" |
| model_type | "default" |
| server.auto | true |
| server.type | "post" |

**所需配置**

| 名称 | 填写说明 |
|:---|:---|
| server.access_token | 填写从Fanbook官方获取的Token |


### 钉钉
这种连接方式支持了钉钉官方开放平台的机器人接口。  

**固定配置**

| 名称 | 配置 |
|:---|:---|
| platform_type | "dingtalk" |
| sdk_type | "dingtalk_link" |
| model_type | "default" |
| server.auto | true |
| server.type | "websocket" |

**所需配置**

| 名称 | 填写说明 |
|:---|:---|
| id | 钉钉官方开放平台的机器人账号的Robot Code |


### Hack.Chat
这种连接方式对接了Hack.Chat聊天协议。  

**固定配置**

| 名称 | 配置 |
|:---|:---|
| platform_type | "hackChat" |
| sdk_type | "hackChat_link" |
| model_type | 详见可变配置 |
| server.auto | true |
| server.type | "websocket" |

**可变配置**

| 账号类型 | model_type |
|:---|:---|
| Hack.Chat | "default" |
| Hack.Chat/私有 | "private" |

**所需配置**

| 名称 | 填写说明 |
|:---|:---|
| id | 所要连接的房间名称 |
| server.access_token | 连入房间的Bot名称 |
| password | 连入房间的Bot的pass鉴权<br/>这是Hack.Chat协议的扩展内容<br/>这可以有效避免被人顶替 |
| extends.ws_path | 私有的Websocket服务器地址<br/>这个字段只在`Hack.Chat/私有`需要被配置 |


### 虚拟终端
这种连接方式支持你打开一个本地的虚拟聊天终端，你可以任意与Bot对话的身份与场景，主要可以用于插件的调试。  

**固定配置**

| 名称 | 配置 |
|:---|:---|
| platform_type | "terminal" |
| sdk_type | "terminal_link" |
| model_type | "default" |
| server.auto | true |
| server.type | "websocket" |

**所需配置**

| 名称 | 填写说明 |
|:---|:---|
| id | 虚拟的Bot账号ID |


### 接口终端（不成熟）
这种连接方式支持你打开一个本地的虚拟聊天终端，不同之处在于这个终端的交互途径是HTTP接口，这可以用于一些网络应用的实现。  
不过该功能目前遇到了一些性能瓶颈，所以后续打算用Websocket的方式重写。  

你将可以通过`POST`发送以下`json`表单来触发消息  
```json
{
    "type": "message",
    "message_type": "group_message",
    "group_id": "77777777",
    "user_id": "66666666",
    "message": ".rd",
    "sender": {
        "user_id": "66666666",
        "nickname": "质子"
    }
}
```


**固定配置**

| 名称 | 配置 |
|:---|:---|
| platform_type | "terminal" |
| sdk_type | "terminal_link" |
| model_type | "postapi" |
| server.auto | true |
| server.type | "post" |

**所需配置**

| 名称 | 填写说明 |
|:---|:---|
| id | 虚拟的Bot账号ID |
| server.port | 服务开放的端口 |


### FF14终端
这种连接方式支持你打开一个本地的虚拟聊天终端，但它同时有一套完整的基于以下回路对接最终幻想14（FINAL FANTASY XIV）的技术支持。  

```
FF14 -> ACT -> Triggernometry -> OlivOS -> 鲇鱼精邮差 -> ACT -> FF14
```

相关的介绍在[这里](https://forum.olivos.run/d/352-olivadice14trpg)  

**固定配置**

| 名称 | 配置 |
|:---|:---|
| platform_type | "terminal" |
| sdk_type | "terminal_link" |
| model_type | "ff14" |
| server.auto | true |
| server.type | "post" |

**所需配置**

| 名称 | 填写说明 |
|:---|:---|
| id | 虚拟的Bot账号ID |
| server.port | Triggernometry中使用的端口 |
| server.access_token | 鲇鱼精邮差的监听端口 |


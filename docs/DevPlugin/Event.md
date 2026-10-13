# 框架事件

## 框架事件综述

### 说明
框架事件由框架进行触发，其触发条件各异，对应不同应用场景

#### 参数规范

触发事件时将会传入参数如下：

| 参数 | 类型 | 解释 | 缺省 |
|:--:|:--:|:---|:--:|
| plugin_event | OlivOS.API.Event | 应用相关事件的主要数据获取和与接口交互对象 | None |
| Proc | OlivOS.pluginAPI.shallow | 当前插件所处的插件托盘组件 | - |

##### plugin_event
其中，`plugin_event`在一些事件中将固定传入`None`，这与这些事件的应用特性相关，例如`初始化事件`。其余场景下，`plugin_event.data`依照事件类型存在区别，将在每个事件的`数据`一栏具体说明。  

同时，对于`plugin_event`类有如下成员  

| 成员 | 类型 | 解释 | 缺省 |
|:---|:---|:---|:--:|
| bot_info | OlivOS.API.bot_info_T | 机器人信息 | - |
| platform | dict | 消息源平台信息 | - |
| platform['platform'] | str | 消息源平台实际平台<br/>例如`qq`、`telegram` | - |
| platform['sdk'] | str | 消息源所基于SDK<br/>例如`onebot`、`telegram_poll` | - |
| platform['model'] | str | 消息源所基于模块模式<br/>例如`default` | - |
| base_info | dict | 内容仅供内部使用，不推荐 | {} |
| data | - | 此处略，以下详述 | - |

###### bot_info_T 类型说明

`bot_info_T` 是机器人信息对象，包含以下成员：

| 成员 | 类型 | 说明 |
|:---|:---|:---|
| id | int | 机器人ID（默认-1） |
| password | str | 密码（默认空字符串） |
| platform | dict | 平台信息字典，包含：<br>- sdk: SDK类型（如'onebot', 'qqGuild_link'等）<br>- platform: 平台名称<br>- model: 选用的模型 |
| hash | str | 机器人的哈希值 |
| post_info | post_info_T对象 | 连接信息对象（详见下方post_info_T说明） |
| extends | dict | 扩展信息字典（可用于存储额外的自定义信息） |
| debug_mode | bool | 调试模式标志（默认False） |

###### post_info_T 类型说明

`post_info_T` 是连接信息对象，包含以下成员：

| 成员 | 类型 | 说明 |
|:---|:---|:---|
| auto | bool | 是否自动 |
| type | str | 服务器类型（默认'post'） |
| host | str | 主机地址 |
| port | int | 端口号（默认-1） |
| access_token | str/None | 访问令牌 |

##### Proc
`Proc`传入的是整个插件加载器的进程对象，通常情况下它在插件的整个生命周期中都不会发生改变，所以你大可以将其赋值到插件内的某个全局变量上以确保随时可以使用，有一些常用参数是你可以用到的：

| 成员 | 类型 | 解释 | 缺省 |
|:---|:---|:---|:--:|
| Proc_data | dict | 进程数据信息 | - |
| Proc_data['bot_info_dict'] | list | 登录账号信息<br/>其中key为bot的hash<br/>value为类型为OlivOS.API.bot_info_T的机器人信息 | - |

调用接口请参考[插件接口 -> 进程接口](/OlivOS_DevDoc_Plugin_API/#_3)章节。


#### 调用入口

事件触发时将会尝试寻找插件命名空间下的`main.Event`中的相关方法。  
- 例如

对于插件的命名空间为`OlivaDiceCore`的插件，在触发`私聊消息`事件时，则会调用的命名空间为`OlivaDiceCore.main.Event.private_message`的方法。


## 消息事件

### 私聊消息事件

#### 原型
```python
class Event(object):
    def private_message(plugin_event, Proc):
        #plugin to do
        pass
```

#### 描述
该事件会在收到私聊消息这类一对一场景消息时被调用  

#### 数据
| 成员 | 类型 | 解释 | 缺省 |
|:--:|:--:|:---|:--:|
| sub_type | str | 子类型 | - |
| user_id | ID | 发送者的用户索引 | - |
| sender | USER | 发送者的相关信息 | - |
| message | MSG | 依照插件所制定的类型转换后的消息内容 | - |
| message_sdk | MSG | 由平台所接收的消息格式 | - |
| message_id | ID | 消息索引 | - |
| raw_message | MSG | 依照插件所制定的类型转换后的消息源内容 | - |
| raw_message_sdk | MSG | 由平台所接收的消息源格式 | - |


### 群聊消息事件

#### 原型
```python
class Event(object):
    def group_message(plugin_event, Proc):
        #plugin to do
        pass
```



#### 描述
该事件会在收到群聊消息、频道消息等多人聊天场景消息时被调用

#### 数据
| 成员 | 类型 | 解释 | 缺省 |
|:--:|:--:|:---|:--:|
| sub_type | str | 子类型 | - |
| host_id | ID | 发送上层群的索引<br/>依平台区别通常为频道一类，不存在时缺省 | None |
| group_id | ID | 发送群的索引 | - |
| user_id | ID | 发送者的用户索引 | - |
| sender | USER | 发送者的相关信息 | - |
| message | MSG | 依照插件所制定的类型转换后的消息内容 | - |
| message_sdk | MSG | 由平台所接收的消息格式 | - |
| message_id | ID | 消息索引 | - |
| raw_message | MSG | 依照插件所制定的类型转换后的消息源内容 | - |
| raw_message_sdk | MSG | 由平台所接收的消息源格式 | - |



## 通知事件

<a id="button-click"></a>

### 按钮点击事件

#### 原型

```python
class Event(object):
    def button_click(plugin_event, Proc):
        button_id = plugin_event.data.button_id
        button_data = plugin_event.data.button_data
        # 按钮标识与业务数据由发送按钮的插件约定。
```

#### 描述

QQ 官方机器人（`qqGuildv2_link`）的回调按钮（按钮 `action.type=1`）触发
`INTERACTION_CREATE`，其中互动 `type=11` 时投递此通知。支持 QQ 群聊、单聊和频道。
指令按钮（`action.type=2`）实际发送的文字仍走原来的消息事件。
按钮的 `action.type` 与收到的互动 `type` 属于不同字段。

框架在后台安排 `PUT /interactions/{interaction_id}` 应答请求，请求体为 `code=0`。
安排请求不代表 QQ 已收到应答，网络失败或超时仍可能导致客户端显示操作失败。
插件只负责业务处理；自动应答也不表示插件业务已成功。同一互动只能应答一次，插件不要重复应答。
同一机器人收到相同互动 ID 时，在现有去重缓存有效期内只投递一次，最多安排一次自动应答。
自动应答在每个插件进程内最多使用 8 个工作线程、32 个排队任务；队列已满或线程启动失败时
不阻塞业务事件投递，跳过该次自动应答并记录警告。排队超过 5 秒的任务会被丢弃，避免继续发送积压的旧应答。
快捷菜单（互动 `type=12`）也按相同限制安排自动应答，并留在 SDK 未映射事件缓存中；
其他互动类型不投递为按钮事件，也不会自动应答。

#### 数据

| 成员 | 类型 | 解释 | 缺省 |
|:--:|:--:|:---|:--:|
| user_id | ID | 点击者索引：群成员 OpenID、单聊用户 OpenID 或频道用户 ID | None |
| group_id | ID | QQ 群 OpenID 或频道的子频道 ID；单聊为 None | None |
| host_id | ID | 频道 ID；QQ 群聊和单聊为 None | None |
| button_id | str | 发送按钮时设置的 id，平台可能不提供 | None |
| button_data | str | 发送按钮时设置的 data 原文，不转换为 CQ/OP 消息、不反转义 | None |
| message_id | ID | 按钮所在消息的 ID，平台可能不提供 | None |
| extend | dict | 平台上下文与原始事件，详见下表 | {} |

| extend 字段 | 说明 |
|:---|:---|
| chat_type | `qq_group`、`qq_private` 或 `guild_channel`；未知场景为 None |
| chat_id | 对应发送目标的索引；未知场景为 None |
| interaction_id | 官方事件体 `d.id`，用于互动应答 |
| event_id | 同 `interaction_id`，供 QQ 群/单聊的被动消息发送使用 |
| qq_auto_ack | True 表示框架已接受自动应答任务，不保证请求最终发出或成功；False 表示队列已满或线程启动失败，任务未被接受 |
| qq_event_type | `INTERACTION_CREATE` |
| qq_event_data | 完整官方事件体 `d`；其他官方顶层字段另有 `qq_` 前缀入口 |
| qq_payload_id | 外层事件包的 id，可能带事件名前缀，应答时不要使用它 |

未提供的字段保持 None。插件应先核对按钮标识和业务数据，再执行相应操作。
此事件不含 `message`，不受插件 `message_mode` 影响，仍遵循插件平台过滤、优先级和 `set_block()`。

发送业务处理结果可使用已有的 [QQ 专属发送接口](API.md#qqguildv2-send-message)：

```python
class Event(object):
    def button_click(plugin_event, Proc):
        if plugin_event.data.button_id != 'confirm':
            return
        context = plugin_event.data.extend
        chat_type, chat_id = context.get('chat_type'), context.get('chat_id')
        if chat_type is None or chat_id is None:
            return
        plugin_event.indeAPI.send_message(
            chat_type, chat_id, '已收到按钮操作'
        )
```

QQ 群聊和单聊在同一会话发送时会自动使用互动 `d.id` 作为被动发送标识；
频道按原来的频道发送路径处理。通用 `reply()` 尚未支持此事件。
使用自定义 intents 的账号需要订阅 `INTERACTION (1 << 26)`；普通账号模型已默认订阅。
官方载荷与应答要求见 [互动事件](https://bot.q.qq.com/wiki/develop/api-v2/autogen/event/interaction_create.html)
和 [互动事件响应](https://bot.q.qq.com/wiki/develop/api-v2/autogen/api/interactions_interaction_id.put.html)。

### 群文件上传事件

#### 原型
```python
class Event(object):
    def group_file_upload(plugin_event, Proc):
        #plugin to do
        pass
```

#### 描述
该事件会在将文件上传至群聊、频道等多人聊天场景时被调用  

#### 数据
| 成员 | 类型 | 解释 | 缺省 |
|:--:|:--:|:---|:--:|
| group_id | ID | 发送群的索引 | - |
| user_id | ID | 发送者的用户索引 | - |
| file | dict | 文件信息 | - |
| file['id'] | ID | 文件索引 | - |
| file['name'] | str | 由平台所接收的消息格式 | - |
| file['size'] | int | 文件大小 | - |
| file['busid'] | int | 暂时无用 | - |

### 群管理变更事件

#### 原型
```python
class Event(object):
    def group_admin(plugin_event, Proc):
        #plugin to do
        pass
```

#### 描述
该事件会在群聊、频道等多人聊天场景中管理员发生增减时被调用  

#### 数据
| 成员 | 类型 | 解释 | 缺省 |
|:--:|:--:|:---|:--:|
| group_id | ID | 发送群的索引 | - |
| user_id | ID | 被操作者的用户索引 | - |
| action | str | 表明动作<br/>`set`时表明新增群管理<br/>`unset`时表明移除群管理 | - |


### 群成员减少事件

#### 原型
```python
class Event(object):
    def group_member_decrease(plugin_event, Proc):
        #plugin to do
        pass
```

#### 描述
该事件会在群聊、频道等多人聊天场景中群成员减少时被调用  

#### 数据
| 成员 | 类型 | 解释 | 缺省 |
|:--:|:--:|:---|:--:|
| group_id | ID | 发送群的索引 | - |
| operator_id | ID | 操作者的用户索引 | - |
| user_id | ID | 被操作者的用户索引 | - |
| action | str | 表明动作<br/>`leave`时表明主动离开<br/>`kick`时表明被踢出<br/>`kick_me`时表明被踢出的是机器人本身 | - |


### 群成员增加事件

#### 原型
```python
class Event(object):
    def group_member_increase(plugin_event, Proc):
        #plugin to do
        pass
```

#### 描述
该事件会在群聊、频道等多人聊天场景中群成员增加时被调用  

#### 数据
| 成员 | 类型 | 解释 | 缺省 |
|:--:|:--:|:---|:--:|
| group_id | ID | 发送群的索引 | - |
| operator_id | ID | 操作者的用户索引 | - |
| user_id | ID | 被操作者的用户索引 | - |
| action | str | 表明动作<br/>`approve`时表明被同意放行加入<br/>`invite`时表明被邀请加入 | - |


### 群成员禁言事件

#### 原型
```python
class Event(object):
    def group_ban(plugin_event, Proc):
        #plugin to do
        pass
```

#### 描述
该事件会在群聊、频道等多人聊天场景中群成员被禁言时被调用  

#### 数据
| 成员 | 类型 | 解释 | 缺省 |
|:--:|:--:|:---|:--:|
| group_id | ID | 发送群的索引 | - |
| operator_id | ID | 操作者的用户索引 | - |
| user_id | ID | 被操作者的用户索引 | - |
| duration | int | 被禁言的时长，`0`表示解除禁言 | - |


### 好友添加事件

#### 原型
```python
class Event(object):
    def friend_add(plugin_event, Proc):
        #plugin to do
        pass
```

#### 描述
该事件会在有新好友被添加时被调用  

#### 数据
| 成员 | 类型 | 解释 | 缺省 |
|:--:|:--:|:---|:--:|
| user_id | ID | 该好友的用户索引 | - |


### 群消息撤回事件

#### 原型
```python
class Event(object):
    def group_message_recall(plugin_event, Proc):
        #plugin to do
        pass
```

#### 描述
该事件会在有群消息被撤回时调用  

#### 数据
| 成员 | 类型 | 解释 | 缺省 |
|:--:|:--:|:---|:--:|
| group_id | ID | 该群索引 | - |
| operator_id | ID | 操作者的用户索引 | - |
| user_id | ID | 该用户的用户索引 | - |
| message_id | ID | 被撤回的消息索引 | - |


### 私聊消息撤回事件

#### 原型
```python
class Event(object):
    def private_message_recall(plugin_event, Proc):
        #plugin to do
        pass
```

#### 描述
该事件会在有私聊消息被撤回时调用  

#### 数据
| 成员 | 类型 | 解释 | 缺省 |
|:--:|:--:|:---|:--:|
| user_id | ID | 该用户的用户索引 | - |
| message_id | ID | 被撤回的消息索引 | - |


### 戳一戳事件

#### 原型
```python
class Event(object):
    def poke(plugin_event, Proc):
        #plugin to do
        pass
```

#### 描述
该事件会在有戳一戳发生时调用  

#### 数据
| 成员 | 类型 | 解释 | 缺省 |
|:--:|:--:|:---|:--:|
| group_id | ID | 该群索引，当发生在私聊中时为`None` | - |
| user_id | ID | 操作者的用户索引 | - |
| target_id | ID | 被操作者的用户索引 | - |


### 群荣誉变更事件

#### 原型
```python
class Event(object):
    def group_honor(plugin_event, Proc):
        #plugin to do
        pass
```

#### 描述
该事件会在有群成员的荣誉发生变化时调用  

#### 数据
| 成员 | 类型 | 解释 | 缺省 |
|:--:|:--:|:---|:--:|
| group_id | ID | 该群索引，当发生在私聊中时为`None` | - |
| user_id | ID | 操作者的用户索引 | - |
| type | str | `talkative`、`performer`、`emotion`<br/>分别对应龙王、群聊之火、快乐源泉 | - |


## 请求事件


### 好友添加请求事件

#### 原型
```python
class Event(object):
    def friend_add_request(plugin_event, Proc):
        #plugin to do
        pass
```

#### 描述
该事件会在有加好友请求时调用  

#### 数据
| 成员 | 类型 | 解释 | 缺省 |
|:--:|:--:|:---|:--:|
| user_id | ID | 用户索引 | - |
| comment | str | 验证信息 | - |
| flag | str | 请求 flag，在调用处理请求的 API 时需要传入 | - |


### 加群请求事件

#### 原型
```python
class Event(object):
    def group_add_request(plugin_event, Proc):
        #plugin to do
        pass
```

#### 描述
该事件会在有加群请求时调用  

#### 数据
| 成员 | 类型 | 解释 | 缺省 |
|:--:|:--:|:---|:--:|
| group_id | ID | 该群索引 | - |
| user_id | ID | 用户索引 | - |
| comment | str | 验证信息 | - |
| flag | str | 请求 flag，在调用处理请求的 API 时需要传入 | - |


### 加群邀请事件

#### 原型
```python
class Event(object):
    def group_invite_request(plugin_event, Proc):
        #plugin to do
        pass
```

#### 描述
该事件会在有加群邀请时调用  

#### 数据
| 成员 | 类型 | 解释 | 缺省 |
|:--:|:--:|:---|:--:|
| group_id | ID | 该群索引 | - |
| user_id | ID | 用户索引 | - |
| comment | str | 验证信息 | - |
| flag | str | 请求 flag，在调用处理请求的 API 时需要传入 | - |



## 元事件
这些事件中`plugin_event`未说明的默认只传递`None`


### 初始化事件

#### 原型
```python
class Event(object):
    def init(plugin_event, Proc):
        #plugin to do
        pass
```

#### 描述
该事件在会在当前插件完成`import`流程后立即被调用。  
**这意味着其不会按照优先级顺序被调用。**


### 初始化后事件

#### 原型
```python
class Event(object):
    def init_after(plugin_event, Proc):
        #plugin to do
        pass
```

#### 描述
该事件在会在完成所有插件的`import`流程后立即被依次调用。  
**这意味着其一定会按照优先级顺序被调用。**


### 存储事件

#### 原型
```python
class Event(object):
    def save(plugin_event, Proc):
        #plugin to do
        pass
```

#### 描述
该事件在会在插件加载器认为需要让插件执行存储操作时被调用，通常为插件即将被重载时。  
**若为全局插件重载，则其一定会按照优先级顺序被调用。**


### 菜单事件

#### 原型
```python
class Event(object):
    def menu(plugin_event, Proc):
        #plugin to do
        pass
```

#### 描述
该事件在会在插件的菜单被点击时调用。  
需要注意的是，该事件的`plugin_event.bot_info`为`None`。

#### 数据
| 成员 | 类型 | 解释 | 缺省 |
|:--:|:--:|:---|:--:|
| namespace | str | 触发菜单的插件命名空间 | - |
| event | str | 触发菜单的插件事件字段<br/>于插件的`自述文件(app.json)`中指定 | - |
| webui | dict | 仅网页消息桥接请求附带，包含 `request_id`、`payload` 和宿主管理的 `session` | 无此属性 |
| payload | JSON 可序列化数据 | 仅网页消息桥接请求附带，等于 `webui['payload']` | 无此属性 |

WebUI 的插件页面交互也复用 `Event.menu`，不是一个名为 `webui` 的新事件回调。此时 `event` 由网页的 `olivos:plugin_event` 消息指定，不要求同时注册到 `menu_config`。

使用 `getattr(plugin_event.data, 'webui', None)` 区分网页交互和普通菜单点击，先检查 `plugin_event.data.namespace` 是否为自己的插件。普通菜单点击（包括 WebUI 插件列表中的菜单按钮）没有网页请求上下文。

网页事件仍然没有机器人上下文，`plugin_event.bot_info` 为 `None`。需要回包时，使用原事件调用 `plugin_event.send('webui', request_id, payload)`，不要使用 `reply()`。`session` 是宿主管理的会话信息，不应写入日志或返回给页面。

完整示例见 [WebUI 页面开发](WebUI.md)。

**该事件一定会按照优先级顺序被调用。**

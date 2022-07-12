# 框架

## 框架事件综述

### 说明
框架事件由框架进行触发，其触发条件各异，对应不同应用场景

#### 参数规范

触发事件时将会传入参数如下：

| 参数 | 类型 | 解释 | 缺省 |
|:--:|:--:|:---|:--:|
| plugin_event | OlivOS.API.Event | 应用相关事件的主要数据获取和与接口交互对象 | None |
| Proc | OlivOS.pluginAPI.shallow | 当前插件所处的插件托盘组件 | - |

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
| event | str | 触发菜单的插件事件字段 | - |

**若为全局插件重载，则其一定会按照优先级顺序被调用。**

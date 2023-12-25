# 插件接口

## 基础类型

### RES | 返回值

当你调用一个插件接口时，如果这个插件接口存在返回值，则会返回这个类，否则则为`None`  
当返回该类时，`data`将是该接口返回的具体业务数据，根据接口不同将会有所查别，以下的文档中各插件接口的`返回值`条目将会只列出其返回值分别对应的`data`条目的内容，但实际返回将是该类  
`active`将在必要时表示返回值是否可信，可以理解为对于插件接口调用成功性的标志  
其本身为`dict`类型  

| 参数 | 类型 | 解释 | 缺省 |
|:--:|:--:|:---|:--:|
| active | bool | 插件接口调用成功性的标志 | False |
| data | - | 该函数的具体返回值 | - |

### ID | 身份索引

独立的直接类型，可能为`int`或者`str`

### USER | 用户

`dict`类型，键值如下

| 参数 | 类型 | 解释 | 缺省 |
|:--:|:--:|:---|:--:|
| name | str | 用户昵称 | None |
| id | ID | 用户ID | -1 |

### GROUP | 群组

`dict`类型，键值如下

| 参数 | 类型 | 解释 | 缺省 |
|:--:|:--:|:---|:--:|
| name | str | 群组名称 | None |
| id | ID | 群组ID | -1 |
| memo | str | 群组描述 | None |
| max_member_count | ID | 群组人数上限 | -1 |

### GROUPUSER | 群组用户

`dict`类型，键值如下

| 参数 | 类型 | 解释 | 缺省 |
|:--:|:--:|:---|:--:|
| name | str | 用户名称 | None |
| id | ID | 用户ID | -1 |
| group_id | ID | 群组ID | -1 |

## 进程接口

### 重载插件
```python
Proc.set_restart()
```
`Proc.set_restart()`接口可以重启整个插件加载器，并重新加载整个插件。

### 插件列表
```python
Proc.get_plugin_list()
```
`Proc.get_plugin_list()`接口可以获得一个由插件的`namespace`填充的`list`，这可以让你知道当前的`OlivOS`上存在哪些插件。

## 插件接口

### 发送消息

```python
plugin_event.send(send_type, target_id, message)
```
用于发送基本消息

#### 函数原型

| 参数 | 类型 | 解释 | 缺省 |
|:--:|:--:|:---|:--:|
| send_type | str | 用于指定发送目标的类型 | - |
| target_id | ID | 发送目标的ID | - |
| message | MSG | 所需要发送的消息 | - |
| host_id  | ID | 发送目标的所属HOST ID | None |

#### 参数规范
- send_type

| 内容 | 解释 |
|:--:|:--:|
| private | 私聊消息 |
| group | 群聊消息 |


### 回复消息
```python
plugin_event.reply(message)
```
用于快速原路回复消息

#### 函数原型

| 参数 | 类型 | 解释 | 缺省 |
|:--:|:--:|:---|:--:|
| message | MSG | 所需要发送的消息 | - |


### 阻塞后续插件
```python
plugin_event.set_block()
```
用于就此阻塞丢弃该消息事件而不传递给后续插件处理

#### 函数原型

| 参数 | 类型 | 解释 | 缺省 |
|:--:|:--:|:---|:--:|
| enable | bool | 阻塞状态 | True |


### 撤回消息
```python
plugin_event.delete_msg(message_id)
```
用于撤回指定消息（以管理员权限）

#### 函数原型

| 参数 | 类型 | 解释 | 缺省 |
|:--:|:--:|:---|:--:|
| message_id | ID | 需要撤回的消息ID | - |


### 获取消息
```python
plugin_event.get_msg(message_id)
```
用于获取指定消息详情

#### 函数原型

| 参数 | 类型 | 解释 | 缺省 |
|:--:|:--:|:---|:--:|
| message_id | ID | 需要查询的消息ID | - |

#### 返回值
| 参数 | 类型 | 解释 | 缺省 |
|:--:|:--:|:---|:--:|
| message_id | ID | 所查询的消息ID | None |
| id | ID | 所查询的消息的实际ID | -1 |
| sender | USER | 发送者信息 | - |
| time | int | 消息时间戳 | -1 |
| message | MSG | 消息内容 | None |
| raw_message | MSG | 消息原生内容 | None |

> 注：`message`与`raw_message`后续将会修改为`MSG`，当前为`str`


### 发送赞
```python
plugin_event.send_like(user_id)
```
用于发送赞

#### 函数原型

| 参数 | 类型 | 解释 | 缺省 |
|:--:|:--:|:---|:--:|
| user_id | ID | 点赞对象ID | - |
| times | int | 点赞次数 | 1 |


### 踢出群成员
```python
plugin_event.set_group_kick(group_id, user_id)
```
用于踢出群成员

#### 函数原型

| 参数 | 类型 | 解释 | 缺省 |
|:--:|:--:|:---|:--:|
| group_id | ID | 群对象ID | - |
| user_id | ID | 群成员对象ID | - |
| rehect_add_request | bool | 是否拉黑对象 | False |


### 禁言群成员
```python
plugin_event.set_group_ban(group_id, user_id)
```
用于禁言群成员

#### 函数原型

| 参数 | 类型 | 解释 | 缺省 |
|:--:|:--:|:---|:--:|
| group_id | ID | 群对象ID | - |
| user_id | ID | 群成员对象ID | - |
| duration | int | 禁言时长/秒 | 1800 |

> 注：`duration`为`0`时表示解除禁言


### 禁言本群
```python
plugin_event.set_group_whole_ban(group_id, enable)
```
用于禁言本群

#### 函数原型

| 参数 | 类型 | 解释 | 缺省 |
|:--:|:--:|:---|:--:|
| group_id | ID | 群对象ID | - |
| enable | bool | 禁言状态 | - |


### 设置群管理员
```python
plugin_event.set_group_admin(group_id, user_id, enable)
```
用于设置群管理员

#### 函数原型

| 参数 | 类型 | 解释 | 缺省 |
|:--:|:--:|:---|:--:|
| group_id | ID | 群对象ID | - |
| user_id | ID | 群成员对象ID | - |
| enable | bool | 管理员状态 | - |


### 设置群成员名片
```python
plugin_event.set_group_card(group_id, user_id, card)
```
用于设置群成员名片

#### 函数原型

| 参数 | 类型 | 解释 | 缺省 |
|:--:|:--:|:---|:--:|
| group_id | ID | 群对象ID | - |
| user_id | ID | 群成员对象ID | - |
| card | str | 新的群名片 | - |


### 设置群名
```python
plugin_event.set_group_name(group_id, group_name)
```
用于设置群名

#### 函数原型

| 参数 | 类型 | 解释 | 缺省 |
|:--:|:--:|:---|:--:|
| group_id | ID | 群对象ID | - |
| group_name | str | 新的群名 | - |


### 退出群
```python
plugin_event.set_group_leave(group_id)
```
用于退出群

#### 函数原型

| 参数 | 类型 | 解释 | 缺省 |
|:--:|:--:|:---|:--:|
| group_id | ID | 群对象ID | - |
| is_dismiss | bool | 当为群主时是否解散该群 | False |


### 设置群成员特殊头衔
```python
plugin_event.set_group_special_title(group_id, user_id, special_title, duration)
```
用于设置群成员特殊头衔

#### 函数原型

| 参数 | 类型 | 解释 | 缺省 |
|:--:|:--:|:---|:--:|
| group_id | ID | 群对象ID | - |
| user_id | ID | 群成员对象ID | - |
| special_title | str | 专属头衔，不填或空字符串表示删除专属头衔 | - |
| duration | int | 专属头衔有效期/秒，`-1`表示永久 | - |


### 处理好友请求
```python
plugin_event.set_friend_add_request(flag, approve, remark)
```
用于处理好友请求

#### 函数原型

| 参数 | 类型 | 解释 | 缺省 |
|:--:|:--:|:---|:--:|
| flag | ID | 加好友请求的`flag`（需从事件的数据中获得） | - |
| approve | bool | 是否同意请求 | - |
| remark | str | 添加后的好友备注（仅在同意时有效） | - |


### 处理群请求
```python
plugin_event.set_group_add_request(flag, sub_type, approve, reason)
```
用于处理群请求

#### 函数原型

| 参数 | 类型 | 解释 | 缺省 |
|:--:|:--:|:---|:--:|
| flag | ID | 加群请求的`flag`（需从事件的数据中获得） | - |
| sub_type | str | 请求类型（需要和事件中的`sub_type`字段相符） | - |
| approve | bool | 是否同意请求／邀请 | - |
| reason | str | 拒绝理由（仅在拒绝时有效） | - |

#### 参数规范
- sub_type

| 内容 | 解释 |
|:--:|:--:|
| add | 加群请求 |
| invite | 加群邀请 |


### 获取登录账号信息
```python
plugin_event.get_login_info()
```
用于获取登录账号信息

#### 返回值
| 参数 | 类型 | 解释 | 缺省 |
|:--:|:--:|:---|:--:|
| - | USER | 整个返回值为单个[USER](#user--用户)类型 | - |


### 获取陌生人信息
```python
plugin_event.get_stranger_info(user_id)
```
用于获取陌生人信息

#### 函数原型

| 参数 | 类型 | 解释 | 缺省 |
|:--:|:--:|:---|:--:|
| user_id | ID | 陌生人对象ID | - |

#### 返回值
| 参数 | 类型 | 解释 | 缺省 |
|:--:|:--:|:---|:--:|
| - | USER | 整个返回值为单个[USER](#user--用户)类型 | - |


### 获取好友列表
```python
plugin_event.get_friend_list()
```
用于获取好友列表

#### 返回值
| 参数 | 类型 | 解释 | 缺省 |
|:--:|:--:|:---|:--:|
| - | list | 整个返回值为[USER](#user--用户)类型的列表 | [] |


### 获取群信息
```python
plugin_event.get_group_info()
```
用于获取群信息

#### 返回值
| 参数 | 类型 | 解释 | 缺省 |
|:--:|:--:|:---|:--:|
| - | GROUP | 整个返回值为[GROUP](#group--群组)类型 | - |


### 获取群列表
```python
plugin_event.get_group_list()
```
用于获取群列表

#### 返回值
| 参数 | 类型 | 解释 | 缺省 |
|:--:|:--:|:---|:--:|
| - | list | 整个返回值为[GROUP](#group--群组)类型的列表 | [] |


### 获取群成员信息
```python
plugin_event.get_group_member_info()
```
用于获取群信息

#### 返回值
| 参数 | 类型 | 解释 | 缺省 |
|:--:|:--:|:---|:--:|
| - | GROUPUSER | 整个返回值为[GROUPUSER](#groupuser--群组用户)类型 | - |


### 获取群成员列表
```python
plugin_event.get_group_member_list()
```
用于获取群列表

#### 返回值
| 参数 | 类型 | 解释 | 缺省 |
|:--:|:--:|:---|:--:|
| - | list | 整个返回值为[GROUPUSER](#groupuser--群组用户)类型的列表 | [] |


## 举例
以下为一则在`私聊消息`事件中调用`获取陌生人信息`接口的例子
```python
#main.py
class Event(object):
    def private_message(plugin_event, Proc):
        res = plugin_event.get_stranger_info(114514)
        Proc.log(2, str(res))
```
则将会有如下日志输出
```python
[2021-12-22 01:28:12] - [INFO] - {'active': True, 'data': {'name': '野兽前辈', 'id': 114514}}
```
即为，返回值内容为：
```python
{
    'active': True,
    'data': {
        'name': '野兽前辈',
        'id': 114514
    }
}
```

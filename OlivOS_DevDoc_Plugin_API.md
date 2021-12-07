# 插件

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
| sender | SENDER | 发送者信息 | - |
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

> 注：`duration`为`0`时表示解除禁言


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

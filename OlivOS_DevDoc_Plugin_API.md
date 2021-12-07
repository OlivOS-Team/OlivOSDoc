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
| sender | SENDER | 所查询的消息ID | - |
| time | ID | 所查询的消息的实际ID | -1 |
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

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

### 用户配置数据库
```python
Proc.database
```
`Proc.database`是一个内置的配置数据库，具体内容详见[用户配置数据库](/DevPlugin/UserModule/#_3)

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


### 检查是否可以发送图片
```python
plugin_event.can_send_image()
```
用于检查是否可以发送图片

#### 返回值
| 参数 | 类型 | 解释 | 缺省 |
|:--:|:--:|:---|:--:|
| yes | bool | 是否可以发送图片 | - |


### 检查是否可以发送语音
```python
plugin_event.can_send_record()
```
用于检查是否可以发送语音

#### 返回值
| 参数 | 类型 | 解释 | 缺省 |
|:--:|:--:|:---|:--:|
| yes | bool | 是否可以发送语音 | - |


### 获取运行状态
```python
plugin_event.get_status()
```
用于获取OneBot运行状态

#### 返回值
| 参数 | 类型 | 解释 | 缺省 |
|:--:|:--:|:---|:--:|
| online | bool | 当前QQ在线状态 | - |
| good | bool | 状态符合预期 | - |


### 获取版本信息
```python
plugin_event.get_version_info()
```
用于获取OneBot版本信息

#### 返回值
| 参数 | 类型 | 解释 | 缺省 |
|:--:|:--:|:---|:--:|
| app_name | str | 应用标识 | - |
| app_version | str | 应用版本 | - |
| protocol_version | str | OneBot协议版本 | - |

---

> **注意：以下接口仅在 `OlivOS 0.11.71 优美的陇烟双剑` 版本以上可用，且并非所有协议所有平台都支持，请注意兼容。**

## 消息相关接口

### 获取频道列表
```python
plugin_event.get_host_list()
```
用于获取频道列表

#### 返回值
| 参数 | 类型 | 解释 | 缺省 |
|:--:|:--:|:---|:--:|
| - | list | 频道列表 | [] |

> 支持平台：KOOK/黑盒语音


### 获取频道信息
```python
plugin_event.get_host_info(host_id)
```
用于获取频道信息

#### 函数原型
| 参数 | 类型 | 解释 | 缺省 |
|:--:|:--:|:---|:--:|
| host_id | ID | 频道ID | - |

#### 返回值
| 参数 | 类型 | 解释 | 缺省 |
|:--:|:--:|:---|:--:|
| - | - | 频道信息 | - |

> 支持平台：KOOK/黑盒语音

### 获取合并转发消息
```python
plugin_event.get_forward_msg(message_id)
```
用于获取合并转发消息内容

> 支持平台：OneBotV11
> 
> 支持协议：go-cqhttp、Lagrange、NapCat、LLOneBot

#### 函数原型
| 参数 | 类型 | 解释 | 缺省 |
|:--:|:--:|:---|:--:|
| message_id | ID | 合并转发消息ID | - |

#### 返回值
| 参数 | 类型 | 解释 | 缺省 |
|:--:|:--:|:---|:--:|
| messages | list | 消息列表 | [] |


### 发送群合并转发消息
```python
plugin_event.send_group_forward_msg(group_id, messages)
```
用于发送群合并转发消息

> 支持平台：OneBotV11
> 
> 支持协议：go-cqhttp、Lagrange、NapCat、LLOneBot

#### 函数原型
| 参数 | 类型 | 解释 | 缺省 |
|:--:|:--:|:---|:--:|
| group_id | ID | 群ID | - |
| messages | list | 消息节点列表 | - |


### 发送私聊合并转发消息
```python
plugin_event.send_private_forward_msg(user_id, messages)
```
用于发送私聊合并转发消息

> 支持平台：OneBotV11
> 
> 支持协议：go-cqhttp、Lagrange、NapCat、LLOneBot

#### 函数原型
| 参数 | 类型 | 解释 | 缺省 |
|:--:|:--:|:---|:--:|
| user_id | ID | 用户ID | - |
| messages | list | 消息节点列表 | - |


### 获取精华消息列表
```python
plugin_event.get_essence_msg_list(group_id)
```
用于获取群精华消息列表

> 支持平台：OneBotV11
> 
> 支持协议：Lagrange、NapCat、LLOneBot

#### 函数原型
| 参数 | 类型 | 解释 | 缺省 |
|:--:|:--:|:---|:--:|
| group_id | ID | 群ID | - |

#### 返回值
返回值为列表，列表中每项包含以下字段：

| 字段 | 类型 | 解释 |
|:--:|:--:|:---|
| sender_id | int | 发送者QQ号 |
| sender_nick | str | 发送者昵称 |
| sender_time | int | 发送时间戳 |
| operator_id | int | 操作者QQ号 |
| operator_nick | str | 操作者昵称 |
| operator_time | int | 操作时间戳 |
| message_id | int | 消息ID |
| message | str | 消息内容 |
| wording | str | 精华消息说明 |
| extra | dict | 平台特有扩展数据 |

**extra 字段说明：**

extra 字段包含平台特有的扩展数据，不同平台返回的字段不同：

| 平台 | 字段 | 类型 | 解释 |
|:--:|:--:|:--:|:---|
| NapCat | msg_seq | int | 消息序号 |
| NapCat | msg_random | int | 消息随机数 |
| NapCat | content | list | 消息内容列表 |
| Lagrange | content | list | 消息内容列表 |


### 设置精华消息
```python
plugin_event.set_essence_msg(message_id)
```
用于设置精华消息

> 支持平台：OneBotV11
> 
> 支持协议：go-cqhttp、Lagrange、NapCat、LLOneBot

#### 函数原型
| 参数 | 类型 | 解释 | 缺省 |
|:--:|:--:|:---|:--:|
| message_id | ID | 消息ID | - |


### 移出精华消息
```python
plugin_event.delete_essence_msg(message_id)
```
用于移出精华消息

> 支持平台：OneBotV11
> 
> 支持协议：go-cqhttp、Lagrange、NapCat、LLOneBot

#### 函数原型
| 参数 | 类型 | 解释 | 缺省 |
|:--:|:--:|:---|:--:|
| message_id | ID | 消息ID | - |


### 消息表情回应
```python
plugin_event.set_msg_emoji_like(message_id, emoji_id, is_set=True, group_id=None)
```
用于给消息添加或取消表情回应，统合了所有主流协议的接口实现

> 支持平台：OneBotV11
> 
> 支持协议：Lagrange、NapCat、LLOneBot

#### 函数原型
| 参数 | 类型 | 解释 | 缺省 |
|:--:|:--:|:---|:--:|
| message_id | ID | 消息ID | - |
| emoji_id | ID | 表情ID（Lagrange使用字符串code，NapCat和LLOneBot使用整数ID） | - |
| is_set | bool | True为添加，False为取消 | True |
| group_id | ID | 群ID（Lagrange必需） | None |

> 注：
> - Lagrange 平台必须提供 group_id 参数，使用 set_group_reaction 接口
> - NapCat 使用 set_msg_emoji_like 接口，emoji_id为整数
> - LLOneBot 根据is_set使用 set_msg_emoji_like 或 unset_msg_emoji_like 接口

### 群戳一戳
```python
plugin_event.group_poke(group_id, user_id)
```
用于在群内戳一戳某人

> 支持平台：OneBotV11
> 
> 支持协议：Lagrange、NapCat、LLOneBot

#### 函数原型
| 参数 | 类型 | 解释 | 缺省 |
|:--:|:--:|:---|:--:|
| group_id | ID | 群ID | - |
| user_id | ID | 用户ID | - |


### 好友戳一戳
```python
plugin_event.friend_poke(user_id)
```
用于戳一戳好友

> 支持平台：OneBotV11
> 
> 支持协议：Lagrange、NapCat、LLOneBot

#### 函数原型
| 参数 | 类型 | 解释 | 缺省 |
|:--:|:--:|:---|:--:|
| user_id | ID | 用户ID | - |


### 群打卡
```python
plugin_event.send_group_sign(group_id)
```
用于群打卡

> 支持平台：OneBotV11
> 
> 支持协议：NapCat、LLOneBot

#### 函数原型
| 参数 | 类型 | 解释 | 缺省 |
|:--:|:--:|:---|:--:|
| group_id | ID | 群ID | - |


### 获取群公告
```python
plugin_event.get_group_notice(group_id)
```
用于获取群公告列表

> 支持平台：OneBotV11
> 
> 支持协议：go-cqhttp、Lagrange、NapCat、LLOneBot

#### 函数原型
| 参数 | 类型 | 解释 | 缺省 |
|:--:|:--:|:---|:--:|
| group_id | ID | 群ID | - |

#### 返回值
返回值为列表，列表中每项包含以下字段：

| 字段 | 类型 | 解释 |
|:--:|:--:|:---|
| sender_id | int | 发送者QQ号 |
| publish_time | int | 发布时间戳 |
| message | dict | 公告内容(字典格式) |
| notice_id | str/int | 公告ID |
| extra | dict | 扩展信息 |

**平台差异说明：**
- **NapCat/Lagrange**: `notice_id` 为字符串类型，`extra.notice_id_type` = `'string'`
- **其他平台**: `notice_id` 为整数类型，`extra.notice_id_type` = `'int'`


### 发送群公告
```python
plugin_event.send_group_notice(group_id, content, image=None, **kwargs)
```
用于发送群公告

> 支持平台：OneBotV11
> 
> 支持协议：go-cqhttp、Lagrange、NapCat、LLOneBot

#### 函数原型
| 参数 | 类型 | 解释 | 缺省 |
|:--:|:--:|:---|:--:|
| group_id | ID | 群ID | - |
| content | str | 公告内容 | - |
| image | str | 公告图片URL | None |
| **kwargs | dict | NapCat 额外参数 | - |

**NapCat 额外参数 (kwargs)：**

| 参数 | 类型 | 解释 | 缺省 |
|:--:|:--:|:---|:--:|
| pinned | int | 是否置顶(0-不置顶, 1-置顶) | - |
| type | int | 公告类型 | - |
| confirm_required | int | 是否需要确认(0-不需要, 1-需要) | - |
| is_show_edit_card | int | 是否显示编辑卡片 | - |
| tip_window_type | int | 提示窗口类型 | - |


### 删除群公告
```python
plugin_event.del_group_notice(group_id, notice_id)
```
用于删除群公告

> 支持平台：OneBotV11
> 
> 支持协议：Lagrange、NapCat

#### 函数原型
| 参数 | 类型 | 解释 | 缺省 |
|:--:|:--:|:---|:--:|
| group_id | ID | 群ID | - |
| notice_id | str | 公告ID | - |


### 上传群文件
```python
plugin_event.upload_group_file(group_id, file, name='', folder_id=None)
```
用于上传群文件

> 支持平台：OneBotV11
> 
> 支持协议：go-cqhttp、Lagrange、NapCat、LLOneBot

#### 函数原型
| 参数 | 类型 | 解释 | 缺省 |
|:--:|:--:|:---|:--:|
| group_id | ID | 群ID | - |
| file | str | 本地文件路径 | - |
| name | str | 储存名称 | '' |
| folder_id | str | 父目录ID | None |


### 删除群文件
```python
plugin_event.delete_group_file(group_id, file_id, name=None)
```
用于删除群文件

> 支持平台：OneBotV11
> 
> 支持协议：go-cqhttp、Lagrange、NapCat、LLOneBot

#### 函数原型
| 参数 | 类型 | 解释 | 缺省 |
|:--:|:--:|:---|:--:|
| group_id | ID | 群ID | - |
| file_id | str | 文件ID | - |
| name | str | 文件名（未使用） | None |


### 创建群文件夹
```python
plugin_event.create_group_file_folder(group_id, name, parent_id='/')
```
用于创建群文件夹

> 支持平台：OneBotV11
> 
> 支持协议：go-cqhttp、Lagrange、NapCat、LLOneBot

#### 函数原型
| 参数 | 类型 | 解释 | 缺省 |
|:--:|:--:|:---|:--:|
| group_id | ID | 群ID | - |
| name | str | 文件夹名称 | - |
| parent_id | str | 父目录ID | '/' |


### 删除群文件夹
```python
plugin_event.delete_group_folder(group_id, folder_id)
```
用于删除群文件夹

> 支持平台：OneBotV11
> 
> 支持协议：go-cqhttp、Lagrange、NapCat、LLOneBot

#### 函数原型
| 参数 | 类型 | 解释 | 缺省 |
|:--:|:--:|:---|:--:|
| group_id | ID | 群ID | - |
| folder_id | str | 文件夹ID | - |


### 获取群文件系统信息
```python
plugin_event.get_group_file_system_info(group_id)
```
用于获取群文件系统信息

> 支持平台：OneBotV11
> 
> 支持协议：go-cqhttp、Lagrange、NapCat、LLOneBot

#### 函数原型
| 参数 | 类型 | 解释 | 缺省 |
|:--:|:--:|:---|:--:|
| group_id | ID | 群ID | - |

#### 返回值
| 参数 | 类型 | 解释 | 缺省 |
|:--:|:--:|:---|:--:|
| file_count | int | 文件数量 | 0 |
| limit_count | int | 文件数量限制 | 0 |
| used_space | int | 已使用空间 | 0 |
| total_space | int | 总空间 | 0 |


### 获取群根目录文件列表
```python
plugin_event.get_group_root_files(group_id, file_count=None)
```
用于获取群根目录文件列表

> 支持平台：OneBotV11
> 
> 支持协议：go-cqhttp、Lagrange、NapCat、LLOneBot（file_count参数仅NapCat支持）

#### 函数原型
| 参数 | 类型 | 解释 | 缺省 |
|:--:|:--:|:---|:--:|
| group_id | ID | 群ID | - |
| file_count | int | 获取文件数量（NapCat支持） | None |

#### 返回值
| 参数 | 类型 | 解释 | 缺省 |
|:--:|:--:|:---|:--:|
| files | list | 文件列表 | [] |
| folders | list | 文件夹列表 | [] |


### 获取群子目录文件列表
```python
plugin_event.get_group_files_by_folder(group_id, folder_id, file_count=None)
```
用于获取群子目录文件列表

> 支持平台：OneBotV11
> 
> 支持协议：go-cqhttp、Lagrange、NapCat、LLOneBot（file_count参数仅NapCat支持）

#### 函数原型
| 参数 | 类型 | 解释 | 缺省 |
|:--:|:--:|:---|:--:|
| group_id | ID | 群ID | - |
| folder_id | str | 文件夹ID | - |
| file_count | int | 获取文件数量（NapCat支持） | None |

#### 返回值
| 参数 | 类型 | 解释 | 缺省 |
|:--:|:--:|:---|:--:|
| files | list | 文件列表 | [] |
| folders | list | 文件夹列表 | [] |


### 获取群文件下载链接
```python
plugin_event.get_group_file_url(group_id, file_id, busid)
```
用于获取群文件下载链接

> 支持平台：OneBotV11
> 
> 支持协议：go-cqhttp、Lagrange、NapCat、LLOneBot

#### 函数原型
| 参数 | 类型 | 解释 | 缺省 |
|:--:|:--:|:---|:--:|
| group_id | ID | 群ID | - |
| file_id | str | 文件ID | - |
| busid | int | 文件类型 | - |

#### 返回值
| 参数 | 类型 | 解释 | 缺省 |
|:--:|:--:|:---|:--:|
| url | str | 文件下载URL | '' |


### 上传私聊文件
```python
plugin_event.upload_private_file(user_id, file, name)
```
用于上传私聊文件

> 支持平台：OneBotV11
> 
> 支持协议：go-cqhttp、Lagrange、NapCat、LLOneBot

#### 函数原型
| 参数 | 类型 | 解释 | 缺省 |
|:--:|:--:|:---|:--:|
| user_id | ID | 用户ID | - |
| file | str | 本地文件路径 | - |
| name | str | 文件名称 | - |


### 重命名群文件夹
```python
plugin_event.rename_group_file_folder(group_id, folder_id, new_folder_name)
```
用于重命名群文件夹

> 支持平台：OneBotV11
> 
> 支持协议：LLOneBot、Lagrange

#### 函数原型
| 参数 | 类型 | 解释 | 缺省 |
|:--:|:--:|:---|:--:|
| group_id | ID | 群ID | - |
| folder_id | str | 文件夹ID | - |
| new_folder_name | str | 新文件夹名称 | - |


### 重命名群文件
```python
plugin_event.rename_group_file(group_id, file_id, current_parent_directory, new_name)
```
用于重命名群文件

> 支持平台：OneBotV11
> 
> 支持协议：NapCat

#### 函数原型
| 参数 | 类型 | 解释 | 缺省 |
|:--:|:--:|:---|:--:|
| group_id | ID | 群ID | - |
| file_id | str | 文件ID | - |
| current_parent_directory | str | 当前父目录路径 | - |
| new_name | str | 新文件名 | - |


### 群文件转永久
```python
plugin_event.set_group_file_forever(group_id, file_id)
```
用于将群文件转为永久保存

> 支持平台：OneBotV11
> 
> 支持协议：NapCat、LLOneBot

#### 函数原型
| 参数 | 类型 | 解释 | 缺省 |
|:--:|:--:|:---|:--:|
| group_id | ID | 群ID | - |
| file_id | str | 文件ID | - |


### 获取已过滤的加群请求
```python
plugin_event.get_group_ignore_add_request(group_id=None)
```
用于获取已过滤的加群通知

> 支持平台：OneBotV11
> 
> 支持协议：NapCat（LLOneBot请使用`get_group_system_msg`）

#### 函数原型
| 参数 | 类型 | 解释 | 缺省 |
|:--:|:--:|:---|:--:|
| group_id | ID | 群ID（可选，用于筛选特定群） | None |

#### 返回值
返回值为列表，列表中每项包含以下字段：

| 字段 | 类型 | 解释 |
|:--:|:--:|:---|
| request_id | int | 请求ID |
| invitor_uin | int | 邀请者QQ号 |
| invitor_nick | str | 邀请者昵称 |
| group_id | int | 群号 |
| group_name | str | 群名称 |
| checked | bool | 是否已处理 |
| actor | int | 操作者QQ号 |
| requester_nick | str | 请求者昵称 |
| message | str | 请求消息 |


### 获取被过滤的好友请求
```python
plugin_event.get_doubt_friends_add_request(count=50)
```
用于获取被过滤的好友请求

> 支持平台：OneBotV11
> 
> 支持协议：NapCat、LLOneBot

#### 函数原型
| 参数 | 类型 | 解释 | 缺省 |
|:--:|:--:|:---|:--:|
| count | int | 获取数量 | 50 |

#### 返回值
返回值为列表，列表中每项包含以下字段：

| 字段 | 类型 | 解释 |
|:--:|:--:|:---|
| flag | str | 请求标识 |
| uin | str | 请求者QQ号 |
| nick | str | 请求者昵称 |
| source | str | 请求来源 |
| reason | str | 验证消息 |
| msg | str | 附加消息 |
| group_code | str | 来源群号 |
| time | str | 请求时间 |
| type | str | 请求类型 |
| extra | dict | 平台特有扩展数据 |


### 处理被过滤的好友请求
```python
plugin_event.set_doubt_friends_add_request(flag, approve=True)
```
用于处理被过滤的好友请求

> 支持平台：OneBotV11
> 
> 支持协议：NapCat、LLOneBot

#### 函数原型
| 参数 | 类型 | 解释 | 缺省 |
|:--:|:--:|:---|:--:|
| flag | str | 请求标识 | - |
| approve | bool | 是否同意（仅NapCat使用） | True |

> 注：LLOneBot 不使用 approve 参数


### 获取群系统消息
```python
plugin_event.get_group_system_msg(count=50)
```
用于获取群系统消息（包括加群申请和邀请）

> 支持平台：OneBotV11
> 
> 支持协议：NapCat、LLOneBot

#### 函数原型
| 参数 | 类型 | 解释 | 缺省 |
|:--:|:--:|:---|:--:|
| count | int | 获取数量（仅NapCat支持） | 50 |

#### 返回值
| 参数 | 类型 | 解释 | 缺省 |
|:--:|:--:|:---|:--:|
| invited_requests | list | 邀请加群申请列表 | [] |
| join_requests | list | 加群申请列表 | [] |

> 注：
> - LLOneBot 使用 GET 方法，不支持 count 参数
> - NapCat 使用 POST 方法，支持 count 参数
> - LLOneBot 的已过滤请求也在此接口查看


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

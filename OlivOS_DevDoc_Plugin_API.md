# 插件

## 插件接口

### 发送消息

```python
plugin_event.send(send_type, target_id, message)
```

#### 说明
用于发送基本消息

| 参数 | 类型 | 解释 | 缺省 |
|:--:|:--:|:---|:--:|
| send_type | str | 用于指定发送目标的类型 | - |
| target_id | ID | 发送目标的ID | - |
| message | MSG | 所需要发送的消息 | - |
| host_id  | ID | 发送目标的所属HOST ID | None |

- send_type

| 内容 | 解释 |
|:--:|:--:|
| private | 私聊消息 |
| group | 群聊消息 |

### 回复消息
```python
plugin_event.reply(message)
```

#### 说明
用于快速原路回复消息

| 参数 | 类型 | 解释 | 缺省 |
|:--:|:--:|:---|:--:|
| message | MSG | 所需要发送的消息 | - |

### 阻塞后续插件
```python
plugin_event.set_block()
```

#### 说明
用于就此阻塞丢弃该消息事件而不传递给后续插件处理

| 参数 | 类型 | 解释 | 缺省 |
|:--:|:--:|:---|:--:|
| enable | bool | 阻塞状态 | True |

# 开发技巧

## 主动调用插件接口

此案例现在已经内置在[插件模板](https://github.com/OlivOS-Team/OlivOSPluginTemplate)中，如果你没有什么额外想法，可以直接使用

### 在现版本的OlivOS中如何主动发消息
首先，OlivOS目前的接口调用需要基于 [OlivOS.API.Event](https://github.com/OlivOS-Team/OlivOS/blob/0d69a9ca63fa7130227891b447885819dc845a01/OlivOS/API.py#L89) 这个类来进行调用，形如你可以在文档中看到的那样：  
```python
plugin_event.send(send_type, target_id, message)
```
这个类的构造通常由收消息的前置组件所提供的`sdk_event`和插件托盘进程的`日志函数`共同完成，这意味着你需要首先去获得它们。  

#### 日志函数
`日志函数`由插件托盘进程提供，它几乎在每个[框架事件](/OlivOS_DevDoc_Event)中都会被作为[入参](/OlivOS_DevDoc_Event)提供。
以如下事件举例
```python
class Event(object):
    def private_message(plugin_event, Proc):
        #plugin to do
        pass
```
则，入参`Proc`即为插件托盘进程，它有如下函数为`日志函数`
```python
Proc.log(log_level, log_message, log_segment = [])
```
当你不需要直接使用它时，你不需要知道这些参数的意义是什么。

#### sdk_event
`sdk_event`是一类直接来自前置协议端的事件封包，其本质是一类由sdk自行定义的类，在插件托盘的 [OlivOS.API.Event](https://github.com/OlivOS-Team/OlivOS/blob/0d69a9ca63fa7130227891b447885819dc845a01/OlivOS/API.py#L89) 构造时，需要传入这些事件，并根据这些类的类型去调用对应的sdk自行实现并提供给OlivOS的插件事件组装函数，在这个过程中，`sdk_event`中包含的各类信息（事件本身的消息，本机的账号信息等）都会被统一转换为OlivOS插件所能接受的事件类型。  

而在主动调用的场景下，就需要自行基于`账号信息`生成一个虚假的`sdk_event`，并以此构造一个能用于OlivOS插件接口调用的插件事件。OlivOS已经内置了一个这样的专门用于这种场景的虚假`sdk_event`，它就是 [OlivOS.contentAPI.fake_sdk_event](https://github.com/OlivOS-Team/OlivOS/blob/0d69a9ca63fa7130227891b447885819dc845a01/OlivOS/contentAPI.py#L213) ，其构造需要提供一个`账号信息`，其类型为 [OlivOS.API.bot_info_T](https://github.com/OlivOS-Team/OlivOS/blob/0d69a9ca63fa7130227891b447885819dc845a01/OlivOS/API.py#L44) ，这个结构体在`Proc.Proc_data['bot_info_dict']`的字典中存储，其key为每个账号对应的hash。  

例如，假设你的账号的hash为`3cfede0d58a99a0fe71846310e9cac47`，那么可以有如下代码  
```python
bot_hash = '3cfede0d58a99a0fe71846310e9cac47'
bot_info = Proc.Proc_data['bot_info_dict'][bot_hash]
print(bot_info.id)
print(bot_info.platform)
print(bot_info.hash)
```
则应当会输出类似如下
```python
123456789
{'sdk': 'onebot', 'platform': 'qq', 'model': 'default'}
3cfede0d58a99a0fe71846310e9cac47
```

#### 综上
如果你需要直接调用接口，可以参考如下代码。  
```python
pluginName = '你的插件名称'
botHash = '3cfede0d58a99a0fe71846310e9cac47'
plugin_event = OlivOS.API.Event(
    OlivOS.contentAPI.fake_sdk_event(
        bot_info = Proc.Proc_data['bot_info_dict'][botHash],
        fakename = pluginName
    ),
    Proc.log
)
plugin_event.send(send_type, target_id, message)
```
或是一步到位  
```python
pluginName = '你的插件名称'
botHash = '3cfede0d58a99a0fe71846310e9cac47'
OlivOS.API.Event(
    OlivOS.contentAPI.fake_sdk_event(
        bot_info = Proc.Proc_data['bot_info_dict'][botHash],
        fakename = pluginName
    ),
    Proc.log
).send(send_type, target_id, message)
```
你也可以参考这个大量利用了主动调用的插件案例 [OlivOSOnebotV11](https://github.com/lunzhiPenxil/OlivOSOnebotV11/blob/4ec7bb610d3508860e93777d1d98a12e73d1bdfc/OlivOSOnebotV11/eventRouter.py#L227)



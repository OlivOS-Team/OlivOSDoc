# 框架

## 框架事件

### 说明
框架事件由框架进行触发，其触发条件各异，对应不同应用场景

#### 参数规范

触发事件时将会传入参数如下：

| 参数 | 类型 | 解释 | 缺省 |
|:--:|:--:|:---|:--:|
| plugin_event | OlivOS.API.Event | 应用相关事件的主要数据获取和与接口交互对象 | None |
| Proc | OlivOS.pluginAPI.shallow | 当前插件所处的插件托盘组件 | - |

其中，`plugin_event`在一些事件中将固定传入`None`，这与这些事件的应用特性相关，例如`初始化事件`  

#### 调用入口

事件触发时将会尝试寻找插件命名空间下的`main.Event`中的相关方法。  
- 例如

对于插件的命名空间为`OlivaDiceCore`的插件，在触发`私聊消息`事件时，则会调用的命名空间为`OlivaDiceCore.main.Event.private_message`的方法。


### 元事件

#### 初始化事件
```python
class Event(object):
    def init(plugin_event, Proc):
        #plugin to do
        pass
```

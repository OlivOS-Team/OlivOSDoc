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

其中，`plugin_event`在一些事件中将固定传入`None`，这与这些事件的应用特性相关，例如`初始化事件`  

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
该事件会在收到私聊消息时被调用


## 元事件
这些事件中`plugin_event`当前只传递`None`


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


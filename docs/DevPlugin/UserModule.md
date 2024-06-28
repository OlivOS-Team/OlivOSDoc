# 用户模块接口

这里提供了所有位于 `OlivOS/userModule` 内文件的相关文档。
`userModule` 中是一些由 `OlivOS` 开发组和插件开发者提供的，与插件编写相关的底层功能。这些功能与框架本身的实现关联性较低，作为用户 API 为插件编写提供方便

## 内容列表
| 名称 | 文件名 | 主导作者 | 大致功能 |
| --- | --- | --- | --- |
| 用户配置数据库 | [UserConfDB.py](https://github.com/OlivOS-Team/OlivOS/blob/main/OlivOS/userModule/UserConfDB.py) | [raininboat](https://github.com/raininboat) | 用户配置文件数据库 |
| IO流包 | [IOStream.py](https://github.com/OlivOS-Team/OlivOS/blob/main/OlivOS/userModule/IOStream.py) | [Dr.Amber](https://github.com/Amber-Keter) | 通过io流形式处理消息 |

---

## 用户配置数据库
`UserConfDB` 是一个简易的插件级别配置数据库，一方面可以用于各种轻量级插件的配置信息直接存取；另一方面，在专用命名空间 unity 中，可以方便的实现各个插件之间的配置互通。


### 主要特点
 - 基于手动传入 namespace 进行变量命名空间管理，当 namespace 为当前插件自身名称时，存储的变量名称不用担心和其他插件的重名，可作为私有配置使用
 - 当 namespace 留空时，自动设置为统一常量 unity ，存储的插件配置可以被所有插件共享。通过这个方式，可以更加方便的实现前置和后继插件业务逻辑分离（如同一个权限插件可以提供给所有后续插件）
 - 后续可以进行一定的倡议，将诸如用户权限固定为统一的配置项名称，方便不同作者的插件可以使用统一的权限管理

**注意**：这个数据库是设计为适合`每次`存取`单个用户/群组`的`一个配置项`，如在某个群聊中该插件是否打开、某个用户是否具有某种权限等。但是如果是每次需要存取一大堆数据（比如抽卡记录、人物卡信息），虽然理论上可以通过存储为json等形式实现，但是会造成大量频繁io写入，故建议还是插件自己独立实现存取过程。

### 数据库接口
数据库依托于 `插件托盘` 运行，具体接口如下

#### get_user_config | 读取对应用户配置项
~~~python
conf =  Proc.database.get_user_config(namespace, key, platform, user_id, default_value=None, pkl=False)
~~~
用于获取某个用户的某项配置

##### 传入参数
| 参数 | 类型 | 解释 | 缺省值 |
| --- | --- | --- | --- |
| namespace | str \| None | 配置项命名空间 | - |
| key | str | 具体存储的配置项名称 | - |
| platform | str | 用户所在平台名称 | - |
| user_id | str \| int | 用户 ID | - |
| default_value | Any | 当该配置项不存在时的默认返回值 | None |
| pkl | Bool | 是否采用 pickle 进行序列化和反序列化 | False |

**注意:**
 - `namespace` 当配置项希望被其他插件共同使用（如是否为管理员等权限），则填写 `None`, **_不可留空不填_**；

    否则此处应当填写当前插件 `app.json` 中的命名空间
 - `pkl` 设置和读取某个配置项时，`pkl` 值**必须相同**！
 - 当存取 `sqlite3` 原生支持的数据结构（**字符串**、**数字**等）时，`pkl`填`False`;

    当存取的数据为**容器**（列表、字典等）或**自定义类**等结构时，`pkl`填`True`，以将其序列化/反序列化后存取

##### 返回值
当数据库中**存在**该配置项时，返回对应配置项内容（原类型），如果**不存在**则返回 `default_value` 中的值



#### get_group_config | 读取对应群组配置项
~~~python
conf = Proc.database.get_group_config(namespace, key, platform, group_id, host_id, default_value=None, pkl=False)
~~~
用于获取某个群聊的某项配置

##### 传入参数
| 参数 | 类型 | 解释 | 缺省值 |
| --- | --- | --- | --- |
| namespace | str \| None | 配置项命名空间 | - |
| key | str | 具体存储的配置项名称 | - |
| platform | str | 群组所在平台名称 | - |
| user_id | str \| int | 群组 ID | - |
| host_id | None\|str \| int | 群组更多层级 | None |
| default_value | Any | 当该配置项不存在时的默认返回值 | None |
| pkl | Bool | 是否采用 pickle 进行序列化和反序列化 | False |

**注意:**
 - `namespace` 当配置项希望被其他插件共同使用（如是否为管理员等权限），则填写 `None`, **_不可留空不填_**；

    否则此处应当填写当前插件 `app.json` 中的命名空间
 - `pkl` 设置和读取某个配置项时，`pkl` 值**必须相同**！
 - 当存取 `sqlite3` 原生支持的数据结构（**字符串**、**数字**等）时，`pkl`填`False`;

    当存取的数据为**容器**（列表、字典等）或**自定义类**等结构时，`pkl`填`True`，以将其序列化/反序列化后存取
 - 如果所在平台有多个层级，则填入 `host_id`，默认为 `None`

##### 返回值
当数据库中**存在**该配置项时，返回对应配置项内容（原类型），如果**不存在**则返回 `default_value` 中的值

#### get_basic_config | 读取插件自身配置项
~~~python
conf =  Proc.database.get_basic_config(namespace, key, default_value=None, pkl=False)
~~~
用于获取插件自身的配置项，即与群组用户均无关的全局配置（如插件配置信息的版本控制）

##### 传入参数
| 参数 | 类型 | 解释 | 缺省值 |
| --- | --- | --- | --- |
| namespace | str \| None | 配置项命名空间 | - |
| key | str | 具体存储的配置项名称 | - |
| default_value | Any | 当该配置项不存在时的默认返回值 | None |
| pkl | Bool | 是否采用 pickle 进行序列化和反序列化 | False |

**注意:**
 - `namespace` 当配置项希望被其他插件共同使用（如是否为管理员等权限），则填写 `None`, **_不可留空不填_**；

    否则此处应当填写当前插件 `app.json` 中的命名空间
 - `pkl` 设置和读取某个配置项时，`pkl` 值**必须相同**！
 - 当存取 `sqlite3` 原生支持的数据结构（**字符串**、**数字**等）时，`pkl`填`False`;

    当存取的数据为**容器**（列表、字典等）或**自定义类**等结构时，`pkl`填`True`，以将其序列化/反序列化后存取

##### 返回值
当数据库中**存在**该配置项时，返回对应配置项内容（原类型），如果**不存在**则返回 `default_value` 中的值


#### get_user_config_from_event | 读取消息事件对应用户的配置项
~~~python
conf =  Proc.database.get_user_config_from_event(namespace, key, plugin_event, default_value=None, pkl=False)
~~~
读取消息事件对应用户的某项配置

##### 传入参数
| 参数 | 类型 | 解释 | 缺省值 |
| --- | --- | --- | --- |
| namespace | str \| None | 配置项命名空间 | - |
| key | str | 具体存储的配置项名称 | - |
| plugin_event | EVENT | OlivOS plugin_event | - |
| default_value | Any | 当该配置项不存在时的默认返回值 | None |
| pkl | Bool | 是否采用 pickle 进行序列化和反序列化 | False |

**注意:**
 - `namespace` 当配置项希望被其他插件共同使用（如是否为管理员等权限），则填写 `None`, **_不可留空不填_**；

    否则此处应当填写当前插件 `app.json` 中的命名空间
 - `pkl` 设置和读取某个配置项时，`pkl` 值**必须相同**！
 - 当存取 `sqlite3` 原生支持的数据结构（**字符串**、**数字**等）时，`pkl`填`False`;

    当存取的数据为**容器**（列表、字典等）或**自定义类**等结构时，`pkl`填`True`，以将其序列化/反序列化后存取
 - `plugin_event` 中直接传入插件被调用时的 `plugin_event`

##### 返回值
当数据库中**存在**该配置项时，返回对应配置项内容（原类型），如果**不存在**则返回 `default_value` 中的值



#### get_group_config_from_event | 读取消息事件对应群组的配置项
~~~python
conf = Proc.database.get_group_config_from_event(namespace, key, plugin_event, default_value=None, pkl=False)
~~~
用于获取消息事件对应群组的某项配置

##### 传入参数
| 参数 | 类型 | 解释 | 缺省值 |
| --- | --- | --- | --- |
| namespace | str \| None | 配置项命名空间 | - |
| key | str | 具体存储的配置项名称 | - |
| plugin_event | EVENT | OlivOS plugin_event | - |
| default_value | Any | 当该配置项不存在时的默认返回值 | None |
| pkl | Bool | 是否采用 pickle 进行序列化和反序列化 | False |

**注意:**
 - `namespace` 当配置项希望被其他插件共同使用（如是否为管理员等权限），则填写 `None`, **_不可留空不填_**；

    否则此处应当填写当前插件 `app.json` 中的命名空间
 - `pkl` 设置和读取某个配置项时，`pkl` 值**必须相同**！
 - 当存取 `sqlite3` 原生支持的数据结构（**字符串**、**数字**等）时，`pkl`填`False`;

    当存取的数据为**容器**（列表、字典等）或**自定义类**等结构时，`pkl`填`True`，以将其序列化/反序列化后存取
 - `plugin_event` 中直接传入插件被调用时的 `plugin_event`

##### 返回值
当数据库中**存在**该配置项时，返回对应配置项内容（原类型），如果**不存在**则返回 `default_value` 中的值


#### set_user_config | 设置对应用户配置项
~~~python

conf = Proc.database.set_user_config(namespace, key, value, platform, user_id, pkl=False)
~~~
用于设置某个用户的某项配置

##### 传入参数
| 参数 | 类型 | 解释 | 缺省值 |
| --- | --- | --- | --- |
| namespace | str \| None | 配置项命名空间 | - |
| key | str | 具体存储的配置项名称 | - |
| value | str | 具体存储的配置项名称 | - |
| platform | str | 用户所在平台名称 | - |
| user_id | str \| int | 用户 ID | - |
| pkl | Bool | 是否采用 pickle 进行序列化和反序列化 | False |

**注意:**
 - `namespace` 当配置项希望被其他插件共同使用（如是否为管理员等权限），则填写 `None`, **_不可留空不填_**；

    否则此处应当填写当前插件 `app.json` 中的命名空间
 - `pkl` 设置和读取某个配置项时，`pkl` 值**必须相同**！
 - 当存取 `sqlite3` 原生支持的数据结构（**字符串**、**数字**等）时，`pkl`填`False`;

    当存取的数据为**容器**（列表、字典等）或**自定义类**等结构时，`pkl`填`True`，以将其序列化/反序列化后存取

##### 返回值
目前恒为 `True`


#### set_group_config | 设置对应群组配置项
~~~python
conf = Proc.database.set_group_config(namespace, key, value, platform, group_id, host_id, pkl=False)
~~~
用于设置某个群聊的某项配置

##### 传入参数
| 参数 | 类型 | 解释 | 缺省值 |
| --- | --- | --- | --- |
| namespace | str \| None | 配置项命名空间 | - |
| key | str | 具体存储的配置项名称 | - |
| value | str | 具体存储的配置项名称 | - |
| platform | str | 群聊所在平台名称 | - |
| group_id | str \| int | 群组 ID | - |
| host_id | None\|str \| int | 群组更多层级 | None |
| pkl | Bool | 是否采用 pickle 进行序列化和反序列化 | False |

**注意:**
 - `namespace` 当配置项希望被其他插件共同使用（如是否为管理员等权限），则填写 `None`, **_不可留空不填_**；

    否则此处应当填写当前插件 `app.json` 中的命名空间
 - `pkl` 设置和读取某个配置项时，`pkl` 值**必须相同**！
 - 当存取 `sqlite3` 原生支持的数据结构（**字符串**、**数字**等）时，`pkl`填`False`;

    当存取的数据为**容器**（列表、字典等）或**自定义类**等结构时，`pkl`填`True`，以将其序列化/反序列化后存取
 - 如果所在平台有多个层级，则填入 `host_id`，默认为 `None`

##### 返回值
目前恒为 `True`



#### set_basic_config | 读取插件自身配置项
~~~python
conf = Proc.database.set_basic_config(namespace, key, value, pkl=False)
~~~
用于设置插件自身的配置项，即与群组用户均无关的全局配置（如插件配置信息的版本控制）

##### 传入参数
| 参数 | 类型 | 解释 | 缺省值 |
| --- | --- | --- | --- |
| namespace | str \| None | 配置项命名空间 | - |
| key | str | 具体存储的配置项名称 | - |
| value | str | 具体存储的配置项名称 | - |
| pkl | Bool | 是否采用 pickle 进行序列化和反序列化 | False |

**注意:**
 - `namespace` 当配置项希望被其他插件共同使用（如是否为管理员等权限），则填写 `None`, **_不可留空不填_**；

    否则此处应当填写当前插件 `app.json` 中的命名空间
 - `pkl` 设置和读取某个配置项时，`pkl` 值**必须相同**！
 - 当存取 `sqlite3` 原生支持的数据结构（**字符串**、**数字**等）时，`pkl`填`False`;

    当存取的数据为**容器**（列表、字典等）或**自定义类**等结构时，`pkl`填`True`，以将其序列化/反序列化后存取

##### 返回值
目前恒为 `True`



#### set_user_config_from_event | 设置消息事件对应用户的配置项
~~~python

conf = Proc.database.set_user_config_from_event(namespace, key, value, plugin_event, pkl=False)
~~~
用于设置消息事件对应用户的某项配置

##### 传入参数
| 参数 | 类型 | 解释 | 缺省值 |
| --- | --- | --- | --- |
| namespace | str \| None | 配置项命名空间 | - |
| key | str | 具体存储的配置项名称 | - |
| value | str | 具体存储的配置项名称 | - |
| plugin_event | EVENT | OlivOS plugin_event | - |
| pkl | Bool | 是否采用 pickle 进行序列化和反序列化 | False |

**注意:**
 - `namespace` 当配置项希望被其他插件共同使用（如是否为管理员等权限），则填写 `None`, **_不可留空不填_**；

    否则此处应当填写当前插件 `app.json` 中的命名空间
 - `pkl` 设置和读取某个配置项时，`pkl` 值**必须相同**！
 - 当存取 `sqlite3` 原生支持的数据结构（**字符串**、**数字**等）时，`pkl`填`False`;

    当存取的数据为**容器**（列表、字典等）或**自定义类**等结构时，`pkl`填`True`，以将其序列化/反序列化后存取
 - `plugin_event` 中直接传入插件被调用时的 `plugin_event`

##### 返回值
目前恒为 `True`


#### set_group_config_from_event | 设置消息事件对应群组的配置项
~~~python

conf = Proc.database.set_group_config_from_event(namespace, key, value, plugin_event, pkl=False)
~~~
用于设置消息事件对应群组的某项配置

##### 传入参数
| 参数 | 类型 | 解释 | 缺省值 |
| --- | --- | --- | --- |
| namespace | str \| None | 配置项命名空间 | - |
| key | str | 具体存储的配置项名称 | - |
| value | str | 具体存储的配置项名称 | - |
| plugin_event | EVENT | OlivOS plugin_event | - |
| pkl | Bool | 是否采用 pickle 进行序列化和反序列化 | False |

**注意:**
 - `namespace` 当配置项希望被其他插件共同使用（如是否为管理员等权限），则填写 `None`, **_不可留空不填_**；

    否则此处应当填写当前插件 `app.json` 中的命名空间
 - `pkl` 设置和读取某个配置项时，`pkl` 值**必须相同**！
 - 当存取 `sqlite3` 原生支持的数据结构（**字符串**、**数字**等）时，`pkl`填`False`;

    当存取的数据为**容器**（列表、字典等）或**自定义类**等结构时，`pkl`填`True`，以将其序列化/反序列化后存取
 - `plugin_event` 中直接传入插件被调用时的 `plugin_event`

##### 返回值
目前恒为 `True`

---

## IO流包
~~~
IO流包 by Dr.Amber
问题反馈请加QQ：1761089294
Email：amberketer@outlook.com


函数：IO_construct(self:API.Event,input_list:'list|tuple',plugin_name,default_mode:int = 2,default_max_time:int = 30,default_func:'function|None' = None)
入参               说明             默认值  默认值说明
self            -> 传入Event对象
input_list      -> 输入流列表
plugin_name     -> 插件名
mode            -> 匹配模式         2       全局模式
default_max_time-> 默认等候时间     30      30秒
default_func    -> 默认函数         None    无

mode：标识信息匹配模式
1   -> 群聊模式，接收同一群聊的所有信息
2   -> 全局模式，接收同一用户的群私聊信息
3   -> 群聊指定模式，接收同一群聊同一用户的信息

input_list：输入流集，元素为输入流单元

input_order：输入流单元，格式如下
{
    're':'此处为正则表达式',
    'max_time':'此处为数字',
    'function':'此处为函数'
}
键值对说明：
re：      标识输入流单元信息匹配格式，为正则表达式

max_time：标识输入流最高等待时间，单位为秒（second）

function：标识数据处理函数，为用户自定义函数，需自己编写。
~~~

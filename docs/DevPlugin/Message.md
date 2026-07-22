# 消息格式

## 综述

OlivOS 中插件所能收到的消息格式取决于插件开发者在 `app.json` 中所配置的 `message_mode` 值，通常有如下可配置的选项

| 值 | 描述 |
|:--:|:---|
| `old_string` | CQ码，CoolQ 时代的字符串消息，适合兼容旧时代 CoolQ、OnebotV11 的项目 |
| `olivos_string` | OlivOS 字符串消息，适合侧重文本消息的轻量消息处理 |
| `olivos_para` | OlivOS 消息段格式，适合需要处理复杂多模消息的场景 |

### CQ 码 - old_string

> CoolQ / 酷Q 是一款在QQ机器人爱好者中拥有很高知名度的第三方机器人框架，曾一度是该领域的常用工具。它于 2020年8月停止运营，目前已经无法使用。

> 其中的 HTTP API 插件经过多个版本的迭代，在当时基本成为了 CoolQ 原生支持以外的主流扩展支持方式，并以此形成了一个较大的应用端生态。

> 在 CoolQ 停止运营后，基于 MiraiGo 的 CQ-HttpApi 作为接替者承接了所有原先依赖 HTTP API 的应用端生态，并一度成为事实标准，在此前提下原先的 HTTP API 插件开发者主导制定了 OnebotV11 标准，所以其中 CQ 码因此被单独剥离出来作为标准的一部分而保留下来。

CQ 码是指 CoolQ 中特殊消息类型的文本格式, 这是它的基本语法：
```
[CQ:类型,参数=值,参数=值]
```

在 QQ 中, 一个消息由多个部分构成, 例如一段文本, 一个图片, at 某人的一个部分. CQ 中定义了与这些消息相符的 CQ 码, 以方便用户使用.

例如, 下面是由一个 at 部分和一个文本部分构成的合法 CQ 消息串
```
[CQ:at,qq=114514]早上好啊
```

例如qq号为114514的人昵称为"小明", 那么上述消息串在QQ中的渲染是这样的：
```
@小明 早上好啊
```

> 注意, CQ 码中不应该有多余的空格, 请不要在任何逗号后或前添加空格, 它会被识别为参数或参数值的一部分.

### OP 码 - olivos_string

OP 码是 OlivOS 中对于 CQ 码提出的替代方案，旨在剥离 CoolQ 标记以及 QQ 平台特征，整体和 CQ 码构造大致类似：

```
[OP:类型,参数=值,参数=值]
```

但是在一些细节上会剥离特征明显的印记，例如
```
[OP:at,id=114514]早上好啊
```

### OlivOS 消息段 - olivos_para

OlivOS 消息段是 OlivOS 内部用于消息格式转换以及交换的复杂数据类型，这种数据类型可以进行更复杂的更高阶的消息操作，但和 OlivOS 核心绑定更深

请注意，以下示意只是为了便于理解而用 json 格式做的近似演示，实际插件编程中，你需要调用实际的数据结构

不同类型的消息在 OlivOS 中对应了继承自 `OlivOS.messageAPI.PARA_templet` 的不同数据结构实现，用 json 序列化示意如下：

```json
{
    "type": "at",
    "data": {
        "id": "114514",
        "name": "仑质"
    }
}
```
它们最终会作为一个序列被封装在一个实例化的 `OlivOS.messageAPI.Message_templet` 中，示意如下：

```json
{
    "data": [
        {
            "type": "at",
            "data": {
                "id": "114514",
                "name": "仑质"
            }
        },
        {
            "type": "text",
            "data": {
                "text": "哼哼哼啊啊啊啊啊啊"
            }
        },
        {
            "type": "image",
            "data": {
                "file": "张口闭眼男.gif"
            }
        }
    ]
}
```

以上消息最终将被渲染为
> @仑质 哼哼哼啊啊啊啊啊啊

> ![张口闭眼男.gif](img/senpai.png)


### 转义

OP 码和 CQ 码由字符 `[` 起始, 以 `]` 结束, 并且以 `,` 分割各个参数, 如果你的 CQ 码中, 参数值包括了这些字符, 那么它们应该被使用 HTML 特殊字符的编码方式进行转义.

| 字符  | 对应实体转义序列 |
|:---:|----------|
| `&` | `&amp;`  |
| `[` | `&#91;`  |
| `]` | `&#93;`  |
| `,` | `&#44;`  |


## 消息类型

下面列出了现在存在的消息类型，由于OP 码和 CQ 码只存在细微差别，所以下面只在存在区别时单独列出 CQ 码

### @某人

```json
{
    "type": "at",
    "data": {
        "id": "10001000",
        "name": "昵称"
    }
}
```

```
[OP:at,id=10001000]
[OP:at,id=123,name=昵称]
[OP:at,id=all]
```

```
[CQ:at,qq=10001000]
[CQ:at,qq=123,name=昵称]
[CQ:at,qq=all]
```

| 参数名 | 收 | 发 | 可能的值 | 说明 |
| --- | --- | --- | --- | --- |
| `qq` | ✓ | ✓ | QQ 号、`all` | @的 QQ 号, `all` 表示全体成员 |
| `name` | | ✓ | 字符串 | 当在群中找不到此QQ号的名称时才会生效 |

### QQ 表情

Type: `face`

```json
{
    "type": "face",
    "data": {
        "id": "123"
    }
}
```

```
[OP:face,id=123]
```

参数 : 

| 参数名 | 收 | 发 | 可能的值 | 说明 |
| --- | --- | --- | --- | --- |
| `id` | ✓ | ✓ | - | QQ 表情 ID |

### 图片

Type : `image`

范围: **发送/接收**

参数:

| 参数名   | 可能的值         | 说明                                                            |
| ------- | --------------- | --------------------------------------------------------------- |
| `file`  | -               | 图片文件名                                                      |
| `type`  | `flash`, `show` | 图片类型, `flash` 表示闪照, `show` 表示秀图, 默认普通图片       |
| `subType`| -               | 图片子类型, 只出现在群聊.                                             |
| `url`   | -               | 图片 URL                                                        |
| `cache` | `0` `1`         | 只在通过网络 URL 发送时有效, 表示是否使用已缓存的文件, 默认 `1` |
| `id`    | -               | 发送秀图时的特效id, 默认为40000                                 |
| `c`     | `2` `3`         | 通过网络下载图片时的线程数, 默认单线程. (在资源不支持并发时会自动处理)|

可用的特效ID:

| id    | 类型 |
| ----- | ---- |
| 40000 | 普通 |
| 40001 | 幻影 |
| 40002 | 抖动 |
| 40003 | 生日 |
| 40004 | 爱你 |
| 40005 | 征友 |

子类型列表:

| value | 说明 |
| ----- | ---- |
| 0     | 正常图片                                  |
| 1     | 表情包, 在客户端会被分类到表情包图片并缩放显示 |
| 2     | 热图                                      |
| 3     | 斗图                                      |
| 4     | 智图?                                     |
| 7     | 贴图                                      |
| 8     | 自拍                                      |
| 9     | 贴图广告?                                  |
| 10    | 有待测试                                   |
| 13    | 热搜图                                    |

发送时，`file` 参数支持：

- 绝对路径，例如 `file:///C:\\Users\Alice\Pictures\1.png`，格式使用 [file URI](https://tools.ietf.org/html/rfc8089)
- 网络 URL，例如 `https://www.baidu.com/img/PCtm_d9c8750bed0b3c7d089fa7d55720d6cf.png`
- Base64 编码，例如 `base64://iVBORw0KGgoAAAANSUhEUgAAABQAAAAVCAIAAADJt1n/AAAAKElEQVQ4EWPk5+RmIBcwkasRpG9UM4mhNxpgowFGMARGEwnBIEJVAAAdBgBNAZf+QAAAAABJRU5ErkJggg==`

示例: `[OP:image,file=http://baidu.com/1.jpg,type=show,id=40004]`

### 回复

Type : `reply`

范围: **发送/接收**

参数:

| 参数名 | 类型 | 说明                                  |
| ------ | ---- | ------------------------------------- |
| `id`   | int  | 回复时所引用的消息id, 必须为本群消息. |
| `text`   | string  | 自定义回复的信息 |
| `qq`   | int64  | 自定义回复时的自定义QQ, 如果使用自定义信息必须指定. |
| `time`   | int64  | 自定义回复时的时间, 格式为Unix时间 |
| `seq` | int64  | 起始消息序号, 可通过 `get_msg` 获得 |

示例: 
```
[OP:reply,id=123456]
``` 

自定义回复示例: 
```
[OP:reply,text=Hello World,qq=10086,time=3376656000,seq=5123]
```

### 语音

```json
{
    "type": "record",
    "data": {
        "file": "http://baidu.com/1.mp3"
    }
}
```

```
[OP:record,file=http://baidu.com/1.mp3]
```

| 参数名 | 收 | 发 | 可能的值 | 说明 |
| --- | --- | --- | --- | --- |
| `file` | ✓ | ✓<sup>[1]</sup> | - | 语音文件名 |
| `magic` | ✓ | ✓ | `0` `1` | 发送时可选, 默认 `0`, 设置为 `1` 表示变声 |
| `url` | ✓ |  | - | 语音 URL |
| `cache` |  | ✓ | `0` `1` | 只在通过网络 URL 发送时有效, 表示是否使用已缓存的文件, 默认 `1` |
| `proxy` |  | ✓ | `0` `1` | 只在通过网络 URL 发送时有效, 表示是否通过代理下载文件 ( 需通过环境变量或配置文件配置代理 ) , 默认 `1` |
| `timeout` |  | ✓ | - | 只在通过网络 URL 发送时有效, 单位秒, 表示下载网络文件的超时时间 , 默认不超时|

[1] 发送时, `file` 参数除了支持使用收到的语音文件名直接发送外, 还支持其它形式, 参考 [图片](#_6)。

### 短视频


```json
{
    "type": "video",
    "data": {
        "file": "http://baidu.com/1.mp4"
    }
}
```

```
[OP:video,file=http://baidu.com/1.mp4]
```

| 参数名     | 类型     | 可能的值    | 说明                                     |
|---------|--------|---------|----------------------------------------|
| `file`  | string | -       | 视频地址, 支持http和file发送                    |
| `cover` | string | -       | 视频封面, 支持http, file和base64发送, 格式必须为jpg  |
| `c`     | int    | `2` `3` | 通过网络下载视频时的线程数, 默认单线程. (在资源不支持并发时会自动处理) |

### 文件

```json
{
    "type": "file",
    "data": {
        "file": "1.json",
        "path": null,
        "url": null,
        "name": "1.json",
        "size": null
    }
}
```

```
[OP:file,file=1.json,name=1.json]
```

| 参数名 | 收 | 发 | 可能的值 | 说明 |
| --- | --- | --- | --- | --- |
| `file` | ✓ | ✓ | - | 接收时为文件 ID 或文件资源；发送时可为文件路径或 URL |
| `path` | ✓ | ✓ | - | 文件路径 |
| `url` | ✓ | ✓ | - | 文件 URL |
| `name` | ✓ | ✓ | - | 文件名；部分平台可能不支持指定发送后的显示名称 |
| `size` | ✓ |  | - | 文件大小 |

发送时按 `path`、`url`、`file` 的顺序选择第一个有效的文件资源。

文件资源支持：

- 相对路径或文件名，例如 `1.json`，将从 OlivOS 运行目录下的 `data/files` 目录读取
- 绝对路径，例如 `C:\Users\Alice\Documents\1.json`
- [file URI](https://tools.ietf.org/html/rfc8089)，例如 `file:///C:/Users/Alice/Documents/1.json`
- 网络 URL，例如 `https://example.com/1.json`
- Base64 编码，例如 `base64://eyJrZXkiOiAidmFsdWUifQ==`

示例：

```
[OP:file,url=1.json,name=1.json]
```

### 猜拳魔法表情


```json
{
    "type": "rps",
    "data": {}
}
```

```
[OP:rps]
```

### 掷骰子魔法表情

```json
{
    "type": "dice",
    "data": {}
}
```

```
[OP:dice]
```

### 窗口抖动（戳一戳） <Badge text="发"/>

```json
{
    "type": "shake",
    "data": {}
}
```

```
[OP:shake]
```

### 匿名发消息 <Badge text="发"/>

```json
{
    "type": "anonymous",
    "data": {}
}
```

```
[OP:anonymous]
```

| 参数名 | 收 | 发 | 可能的值 | 说明 |
| --- | --- | --- | --- | --- |
| `ignore` |  | ✓ | `0` `1` | 可选, 表示无法匿名时是否继续发送 |

### 链接分享

```json
{
    "type": "share",
    "data": {
        "url": "http://baidu.com",
        "title": "百度"
    }
}
```

```
[OP:share,url=http://baidu.com,title=百度]
```

| 参数名 | 收 | 发 | 可能的值 | 说明 |
| --- | --- | --- | --- | --- |
| `url` | ✓ | ✓ | - | URL |
| `title` | ✓ | ✓ | - | 标题 |
| `content` | ✓ | ✓ | - | 发送时可选, 内容描述 |
| `image` | ✓ | ✓ | - | 发送时可选, 图片 URL |

### 推荐好友/群

```
[OP:contact,type=qq,id=10001000]
[OP:contact,type=group,id=100100]
```

| 参数名 | 收 | 发 | 可能的值 | 说明 |
| --- | --- | --- | --- | --- |
| `type` | ✓ | ✓ | `qq`/`group` | 推荐好友/群 |
| `id` | ✓ | ✓ | - | 被推荐的 QQ （群）号 |

### 位置

```json
{
    "type": "location",
    "data": {
        "lat": "39.8969426",
        "lon": "116.3109099"
    }
}
```

```
[OP:location,lat=39.8969426,lon=116.3109099]
```

| 参数名 | 收 | 发 | 可能的值 | 说明 |
| --- | --- | --- | --- | --- |
| `lat` | ✓ | ✓ | - | 纬度 |
| `lon` | ✓ | ✓ | - | 经度 |
| `title` | ✓ | ✓ | - | 发送时可选, 标题 |
| `content` | ✓ | ✓ | - | 发送时可选, 内容描述 |

### 音乐分享 <Badge text="发"/>

```json
{
    "type": "music",
    "data": {
        "type": "163",
        "id": "28949129"
    }
}
```

```
[OP:music,type=163,id=28949129]
```

| 参数名 | 收 | 发 | 可能的值 | 说明 |
| --- | --- | --- | --- | --- |
| `type` |  | ✓ | `qq` `163` `xm` | 分别表示使用 QQ 音乐、网易云音乐、虾米音乐 |
| `id` |  | ✓ | - | 歌曲 ID |

### 音乐自定义分享 <Badge text="发"/>

```json
{
    "type": "music",
    "data": {
        "type": "custom",
        "url": "http://baidu.com",
        "audio": "http://baidu.com/1.mp3",
        "title": "音乐标题"
    }
}
```

```
[OP:music,type=custom,url=http://baidu.com,audio=http://baidu.com/1.mp3,title=音乐标题]
```

| 参数名 | 收 | 发 | 可能的值 | 说明 |
| --- | --- | --- | --- | --- |
| `type` |  | ✓ | `custom` | 表示音乐自定义分享 |
| `url` |  | ✓ | - | 点击后跳转目标 URL |
| `audio` |  | ✓ | - | 音乐 URL |
| `title` |  | ✓ | - | 标题 |
| `content` |  | ✓ | - | 发送时可选, 内容描述 |
| `image` |  | ✓ | - | 发送时可选, 图片 URL |

### 红包 <Badge text="收"/>

Type: `redbag`

参数:

| 参数名  | 类型   | 说明        |
| ------- | ------ | ----------- |
| `title` | string | 祝福语/口令 |

示例: `[OP:redbag,title=恭喜发财]`

### 戳一戳 <Badge text="发"/>

Type: `poke`

范围: **仅群聊**

参数:

| 参数名 | 类型  | 说明         |
| ------ | ----- | ------------ |
| `qq`   | int64 | 需要戳的成员 |

示例: `[OP:poke,qq=123456]`

### 礼物 <Badge text="发"/>

Type: `gift`

范围: **仅群聊,接收的时候不是 CQ 码**

参数 :

| 参数名 | 类型  | 说明           |
| ------ | ----- | -------------- |
| `qq`   | int64 | 接收礼物的成员 |
| `id`   | int   | 礼物的类型     |

目前支持的礼物 ID :

| id   | 类型       |
| ---- | ---------- |
| 0    | 甜 Wink    |
| 1    | 快乐肥宅水 |
| 2    | 幸运手链   |
| 3    | 卡布奇诺   |
| 4    | 猫咪手表   |
| 5    | 绒绒手套   |
| 6    | 彩虹糖果   |
| 7    | 坚强       |
| 8    | 告白话筒   |
| 9    | 牵你的手   |
| 10   | 可爱猫咪   |
| 11   | 神秘面具   |
| 12   | 我超忙的   |
| 13   | 爱心口罩   |

示例: `[OP:gift,qq=123456,id=8]`

### 合并转发 <Badge text="收"/>

Type: `forward`

参数:

| 参数名 | 类型   | 说明                                                         |
| ------ | ------ | ------------------------------------------------------------ |
| `id`   | string | 合并转发ID, 需要通过 `/get_forward_msg` API获取转发的具体内容 |

示例: `[OP:forward,id=xxxx]`

### 合并转发消息节点 <Badge text="发"/>

Type: `node`

参数:

| 参数名    | 类型    | 说明           | 特殊说明                                                     |
| --------- | ------- | -------------- | ------------------------------------------------------------ |
| `id`      | int32   | 转发消息id     | 直接引用他人的消息合并转发,  实际查看顺序为原消息发送顺序 **与下面的自定义消息二选一** |
| `name`    | string  | 发送者显示名字 | 用于自定义消息 (自定义消息并合并转发, 实际查看顺序为自定义消息段顺序) |
| `uin`     | int64   | 发送者QQ号     | 用于自定义消息                                               |
| `content` | message | 具体消息       | 用于自定义消息 **不支持转发套娃**            |
| `seq`     | message | 具体消息       | 用于自定义消息                                                                         |

特殊说明: **此接口支持较为复杂，目前 OlivOS 没有进行剥离平台特征的处理，且需要使用单独的API `send_group_forward_msg` 发送, 并且由于消息段较为复杂, 仅支持Array形式入参。 如果引用消息和自定义消息同时出现, 实际查看顺序将取消息段顺序.  另外按 [Onebot v11](https://github.com/botuniverse/onebot-11/blob/master/message/array.md) 文档说明, `data` 应全为字符串, 但由于需要接收`message` 类型的消息, 所以 *仅限此Type的content字段* 支持Array套娃**

示例:

直接引用消息合并转发:

````json
[
	{
		"type": "node",
		"data": {
			"id": "123"
		}
	},
	{
		"type": "node",
		"data": {
			"id": "456"
		}
	}
]
````

自定义消息合并转发:

````json
[
	{
		"type": "node",
		"data": {
			"name": "消息发送者A",
			"uin": "10086",
			"content": [
				{
					"type": "text",
					"data": {
						"text": "测试消息1"
					}
				}
			]
		}
	},
	{
		"type": "node",
		"data": {
			"name": "消息发送者B",
			"uin": "10087",
			"content": "[OP:image,file=xxxxx]测试消息2"
		}
	}
]
````

引用自定义混合合并转发:

````json
[
    {
        "type": "node",
        "data": {
            "name": "自定义发送者",
            "uin": "10086",
            "content": "我是自定义消息",
            "seq": "5123",
            "time": "3376656000"
        }
    },
    {
        "type": "node",
        "data": {
            "id": "123"
        }
    }
]
````

### XML 消息

Type: `xml`

范围: **发送 / 接收**

参数:

| 参数名  | 类型   | 说明                                      |
| ------- | ------ | ----------------------------------------- |
| `data`  | string | xml内容, xml中的value部分, 记得实体化处理 |
| `resid` | int32  | 可能为空, 或空字符串                      |

示例: `[OP:xml,data=xxxx]`

##### 一些 xml 样例

> ps:重要 : xml 中的 value 部分, 记得 html 实体化处理后, 再打加入到 CQ 码中

##### QQ 音乐

```xml
<?xml version='1.0' encoding='UTF-8' standalone='yes' ?><msg serviceID="2" templateID="1" action="web" brief="&#91;分享&#93; 十年" sourceMsgId="0" url="https://i.y.qq.com/v8/playsong.html?_wv=1&amp;songid=4830342&amp;souce=qqshare&amp;source=qqshare&amp;ADTAG=qqshare" flag="0" adverSign="0" multiMsgFlag="0" ><item layout="2"><audio cover="http://imgcache.qq.com/music/photo/album_500/26/500_albumpic_89526_0.jpg" src="http://ws.stream.qqmusic.qq.com/C400003mAan70zUy5O.m4a?guid=1535153710&amp;vkey=D5315B8C0603653592AD4879A8A3742177F59D582A7A86546E24DD7F282C3ACF81526C76E293E57EA1E42CF19881C561275D919233333ADE&amp;uin=&amp;fromtag=3" /><title>十年</title><summary>陈奕迅</summary></item><source name="QQ音乐" icon="https://i.gtimg.cn/open/app_icon/01/07/98/56/1101079856_100_m.png" url="http://web.p.qq.com/qqmpmobile/aio/app.html?id=1101079856" action="app"  a_actionData="com.tencent.qqmusic" i_actionData="tencent1101079856://" appid="1101079856" /></msg>
```
##### 网易音乐
```xml
<?xml version='1.0' encoding='UTF-8' standalone='yes' ?><msg serviceID="2" templateID="1" action="web" brief="&#91;分享&#93; 十年" sourceMsgId="0" url="http://music.163.com/m/song/409650368" flag="0" adverSign="0" multiMsgFlag="0" ><item layout="2"><audio cover="http://p2.music.126.net/g-Qgb9ibk9Wp_0HWra0xQQ==/16636710440565853.jpg?param=90y90" src="https://music.163.com/song/media/outer/url?id=409650368.mp3" /><title>十年</title><summary>黄梦之</summary></item><source name="网易云音乐" icon="https://pic.rmb.bdstatic.com/911423bee2bef937975b29b265d737b3.png" url="http://web.p.qq.com/qqmpmobile/aio/app.html?id=1101079856" action="app" a_actionData="com.netease.cloudmusic" i_actionData="tencent100495085://" appid="100495085" /></msg>
```

##### 卡片消息 1
```xml
<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
<msg serviceID="1">
<item><title>生死8秒！女司机高速急刹, 他一个操作救下一车性命</title></item>
<source name="官方认证消息" icon="https://qzs.qq.com/ac/qzone_v5/client/auth_icon.png" action="" appid="-1" />
</msg>
```

##### 卡片消息 2
```xml
<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
<msg serviceID="1">
<item layout="4">
<title>test title</title>
<picture cover="http://url.cn/5CEwIUy"/>
</item>
</msg>
```

### JSON 消息

Type: `json`

范围: **发送/接收**

参数:

| 参数名  | 类型   | 说明                                            |
| ------- | ------ | ----------------------------------------------- |
| `data`  | string | json内容, json的所有字符串记得实体化处理        |
| `resid` | int32  | 默认不填为0, 走小程序通道, 填了走富文本通道发送 |

json中的字符串需要进行转义 : 

>","=> `&#44;`

>"&"=> `&amp;`

>"["=> `&#91;`

>"]"=> `&#93;`

否则无法正确得到解析

示例 json 的 CQ 码 : 
```test
[OP:json,data={"app":"com.tencent.miniapp"&#44;"desc":""&#44;"view":"notification"&#44;"ver":"0.0.0.1"&#44;"prompt":"&#91;应用&#93;"&#44;"appID":""&#44;"sourceName":""&#44;"actionData":""&#44;"actionData_A":""&#44;"sourceUrl":""&#44;"meta":{"notification":{"appInfo":{"appName":"全国疫情数据统计"&#44;"appType":4&#44;"appid":1109659848&#44;"iconUrl":"http:\/\/gchat.qpic.cn\/gchatpic_new\/719328335\/-2010394141-6383A777BEB79B70B31CE250142D740F\/0"}&#44;"data":&#91;{"title":"确诊"&#44;"value":"80932"}&#44;{"title":"今日确诊"&#44;"value":"28"}&#44;{"title":"疑似"&#44;"value":"72"}&#44;{"title":"今日疑似"&#44;"value":"5"}&#44;{"title":"治愈"&#44;"value":"60197"}&#44;{"title":"今日治愈"&#44;"value":"1513"}&#44;{"title":"死亡"&#44;"value":"3140"}&#44;{"title":"今**亡"&#44;"value":"17"}&#93;&#44;"title":"中国加油, 武汉加油"&#44;"button":&#91;{"name":"病毒 : SARS-CoV-2, 其导致疾病命名 COVID-19"&#44;"action":""}&#44;{"name":"传染源 : 新冠肺炎的患者。无症状感染者也可能成为传染源。"&#44;"action":""}&#93;&#44;"emphasis_keyword":""}}&#44;"text":""&#44;"sourceAd":""}]
```


### cardimage <Badge text="发"/>
一种xml的图片消息（装逼大图）

Type: `cardimage`

参数:

| 参数名      | 类型   | 说明                                  |
| ----------- | ------ | ------------------------------------- |
| `file`      | string | 和image的file字段对齐, 支持也是一样的 |
| `minwidth`  | int64  | 默认不填为400, 最小width              |
| `minheight` | int64  | 默认不填为400, 最小height             |
| `maxwidth`  | int64  | 默认不填为500, 最大width              |
| `maxheight` | int64  | 默认不填为1000, 最大height            |
| `source`    | string | 分享来源的名称, 可以留空              |
| `icon`      | string | 分享来源的icon图标url, 可以留空       |


示例cardimage 的cq码 : 
```test
[OP:cardimage,file=https://i.pixiv.cat/img-master/img/2020/03/25/00/00/08/80334602_p0_master1200.jpg]
```

### 文本转语音 <Badge text="发"/>

Type: `tts`

范围: **仅群聊**

参数:

| 参数名 | 类型   | 说明 |
| ------ | ------ | ---- |
| `text` | string | 内容 |

示例: `[OP:tts,text=这是一条测试消息]`

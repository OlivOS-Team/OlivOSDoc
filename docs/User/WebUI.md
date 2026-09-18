# WebUI

WebUI 是 OlivOS 自带的浏览器管理界面，可管理账号、查看日志和终端、管理插件，并打开插件提供的网页。需要使用包含 WebUI 功能的 OlivOS 核心。

## 打开与登录

1. 启动 OlivOS，等待日志出现 `WebUI 已启动`。默认地址是 `http://127.0.0.1:20480`，日志也会显示实际监听地址和认证文件路径。
2. Windows 可以从右下角托盘选择“打开 WebUI”；Linux、macOS 或无桌面环境可使用浏览器访问日志中的地址。
3. 打开 OlivOS 工作目录下的 `conf/webui_token.txt`，在登录页输入其中的令牌。

登录成功后，令牌保存在当前浏览器的本地存储中；刷新或重新打开同一地址时会自动登录。点击“退出登录”或缓存令牌认证失效时会清除缓存。浏览器禁用本地存储时仍可手动登录，但无法记住登录。

认证文件不存在时，WebUI 会自动生成令牌并写入该文件；后续启动继续使用此文件。核心不会自动读取或迁移旧的 `data/webui_token`、`data/webui_token.txt`。不要把令牌写入插件代码、网页地址或日志。

`127.0.0.1` 指运行 OlivOS 的机器。远程服务器推荐通过 SSH 转发访问，例如在自己的电脑运行：

```bash
ssh -L 20480:127.0.0.1:20480 user@server
```

然后在本机访问 `http://127.0.0.1:20480`。`user@server` 替换为自己的 SSH 登录地址。

## 页面

| 页面 | 内容 |
| --- | --- |
| 仪表盘 | 核心版本、账号数量、连接状态及未知状态的账号明细、运行时长、更新检查、重载插件、社区论坛与退出 |
| 账号 | 查看基本信息、连接状态及 `bot_hash`，新增、编辑、删除、启停账号，保存并应用配置 |
| 日志 | 按级别筛选日志、自动滚动和实时日志 |
| 终端 | 已启动协议端的终端输出、输入和登录二维码 |
| 插件 | 插件列表、路径、菜单及插件重载 |
| 插件页面 | 插件通过 `webui_config` 注册的内嵌页面或外部链接 |

终端只显示已经启动并被核心接入的协议端，不是任意系统命令终端。账号“在线”状态也不等于“已启用”：没有可用连接状态时，界面会明确提示。

QQ 官方 V2 Webhook 不建立 WebSocket 长连接，其在线状态按对应回调服务的监听和定期探活结果统计，表示本地回调服务可用，不代表外网回调地址或平台权限已经验证通过。

账号页中的编辑先保存在网页草稿，点击“保存并应用”才写入账号文件。该操作不会重启整个 OlivOS，但默认会停止并重建 `account_update` 配置中的账号连接组件，可能影响多个账号并导致短暂断线重连；主进程、插件进程和 WebUI 继续运行。成功提示表示配置已保存并发起应用，不表示全部连接已经恢复。

已有账号配置保存前会生成同目录的 `.webui-backup` 文件，例如 `conf/account.json.webui-backup`。备份也可能包含账号凭据，应按原配置文件同样管理。

## 外观

登录页及登录后的侧栏均可选择“浅色”“深色”或“跟随系统”。选择会保存在当前浏览器；浏览器禁用本地存储时，仍可切换本页外观，但刷新后可能恢复默认值。插件页面使用自己的网页样式。

## 内置默认值与用户配置

WebUI 默认启用，不需要在 `conf/config.json` 中预置 `OlivOS_webUI`。组件定义及默认参数内置于核心 `OlivOS/core/boot/bootDataAPI.py`，服务端也会补齐未指定的参数。

`conf/config.json` 仅用于用户主动设置的覆盖项，随项目提供的文件不重复写入这组默认值。正常使用无需修改；只有需要更换端口、监听地址或关闭 WebUI 时，才按核心的配置覆盖机制修改 `models.OlivOS_webUI` 下对应的用户设置，并重启 OlivOS 生效。

| 内置字段 | 默认值 | 说明 |
| --- | --- | --- |
| enable | `true` | 启动 WebUI |
| server.host | `127.0.0.1` | 默认只允许本机访问 |
| server.port | `20480` | HTTP 与 WebSocket 共用的监听端口 |
| server.token_path | `./conf/webui_token.txt` | 认证文件路径，相对 OlivOS 工作目录 |
| server.static_path | `./data/webui/static` | 核心静态资源释放目录，启动时由内嵌资源更新 |
| server.buffer_limit | `128` | 内存中的日志、终端和事件缓冲条数 |

### 需要修改时再添加

如果需要修改 WebUI，在现有 `conf/config.json` 中合并下面的配置，再修改所需的值。示例列出的是默认值，**不是启用 WebUI 的必填配置**；不要用此片段覆盖文件中的其他用户设置，已有 `models` 时将 `OlivOS_webUI` 加入该对象即可。

```json
{
    "models": {
        "OlivOS_webUI": {
            "enable": true,
            "server": {
                "auto": false,
                "type": "http",
                "host": "127.0.0.1",
                "port": 20480,
                "token_path": "./conf/webui_token.txt",
                "static_path": "./data/webui/static",
                "buffer_limit": 128
            }
        }
    }
}
```

例如，更换监听端口时修改 `port`；关闭 WebUI 时将 `enable` 改为 `false`。修改后重启 OlivOS。保持默认行为时，可以完全省略 `OlivOS_webUI`，由核心使用内置配置。

不要把 `static_path` 当作持久修改前端源码的目录。开发核心界面应修改 `OlivOS/webUI/static/`，再运行 `script/embed_webui.py` 更新内嵌资源。插件网页保存在插件自己的 `webui/` 目录，与核心资源打包无关。

若需要关闭原生 Windows 界面并使用 WebUI，可以将 `system.proc_mode` 设置为 `webUI`。该模式跳过原生账号确认窗口及托盘窗口，通过浏览器管理；WebUI 的 `enable` 仍需开启。默认模式保留原生 Windows 界面的流程。

## 插件页面

插件作者需要提供网页并注册入口，核心不会把 Python 设置项或原生 GUI 自动转换为网页。普通插件菜单仍能在“插件”页调用；带原生窗口的菜单不一定适合无桌面环境。

开发入口见 [WebUI 页面开发](../DevPlugin/WebUI.md) 和 [官方插件模板](https://github.com/OlivOS-Team/OlivOSPluginTemplate)。

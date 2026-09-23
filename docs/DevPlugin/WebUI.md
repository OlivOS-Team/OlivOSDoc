# WebUI 页面开发

插件自行提供 HTML、CSS 和 JavaScript，OlivOS 负责入口注册、静态页面挂载、登录认证，以及网页与 Python 事件之间的消息转发。页面不会由 `app.json` 或 Python 原生 GUI 自动生成，也不需要插件再启动一个 Web 服务。

建议从 [OlivOSPluginTemplate](https://github.com/OlivOS-Team/OlivOSPluginTemplate) 的 `webui/index.html` 开始。模板沿用官方原生插件结构，只有 OlivOS 依赖，不要求 Node.js、npm 或前端框架。

## 目录与注册

**页面路径由 `app.json` 中的 `webui_config.path` 决定，目录名称不固定。** 以下沿用模板的 `webui/` 目录举例，也支持自定目录名或插件根目录下的单个 HTML 文件。文件夹和 OPK 部署使用同一套路径规则。

```text
OlivOSPluginTemplate/
├── app.json
├── __init__.py
├── main.py
└── webui/
    ├── index.html
    └── assets/
        ├── app.js
        └── style.css
```

在 `app.json` 中添加以下字段，保留插件原有的其他字段。文件必须是 **UTF-8 无 BOM**。

```json
{
    "webui_config": [
        {
            "title": "插件模板",
            "type": "iframe",
            "path": "webui/index.html"
        }
    ]
}
```

| 字段 | 说明 |
| --- | --- |
| webui_config | 可选列表，允许注册多个页面 |
| title | 必填字符串，在侧栏“插件页面”中显示 |
| type | `iframe` 或 `link` |
| path | `iframe` 必填字符串，每个条目只填写一个路径；相对于插件根目录，使用 `/` 分隔，可指向文件（如 `webui/index.html`、`panel.html`）或包含 `index.html` 的目录（如 `webui/`） |
| url | `link` 必填，使用 `http://` 或 `https://` 地址 |

例如，也可以在列表中添加 `{"title": "文档", "type": "link", "url": "https://docs.olivos.run/"}`，在新标签页打开外部网站。外部链接不会获得内嵌插件页面的消息桥接能力。

**注册多个路径时，在 `webui_config` 中添加多个条目，每个条目各写一个 `path`。** 单个 `path` 不支持数组，也不能用逗号拼接多个路径。例如：

```json
{
    "webui_config": [
        {
            "title": "插件首页",
            "type": "iframe",
            "path": "webui/index.html"
        },
        {
            "title": "插件设置",
            "type": "iframe",
            "path": "webui/settings/index.html"
        },
        {
            "title": "独立面板",
            "type": "iframe",
            "path": "panel.html"
        }
    ]
}
```

以上配置会在侧栏的同一个插件分组下添加三个子入口。分组按 `namespace` 区分，标题使用插件名称，每个子入口显示自己的 `title`；外部链接也归入所属插件。点击插件名称可展开或收起子入口，收起分组不会关闭已打开的页面。

只有一个有效入口时，点击插件直接打开该页面，不显示额外的子入口层级；入口的 `title` 与插件名称不同时，会作为补充说明显示。存在多个有效入口时，点击插件名称只展开或收起，不自动打开页面；计数包括内嵌页面和外部链接。

插件名称相同时，分组标题下会补充 `namespace`，不同命名空间的插件仍各自独立。同一分组内的页面标题相同时，子入口会补充对应的 `path`（外部链接显示 `url`），各页面仍可分别打开。

资源范围取所有入口的并集：此例包含整个 `webui/` 目录及根目录下的 `panel.html`；`webui/settings/` 已包含在 `webui/` 中。页面用到的静态资源无需逐个注册为页面，但必须在这些资源范围内。

宿主从插件元数据补全 `namespace`，页面无需自行填写。修改 `app.json` 后需重载插件。已有的 `path: "webui/index.html"` 可直接使用，无需新增配置字段或编写资源恢复代码。

资源范围由注册路径自动确定：

- 路径指向子目录中的文件时，提供**该文件所在目录及其全部子目录**。例如 `webui/index.html` 对应整个 `webui/`；仅注册 `webui/pages/settings.html` 时，范围是 `webui/pages/`。
- 路径指向目录时，使用该目录的 `index.html` 为入口，提供整个目录。末尾的 `/` 可省略。
- 路径指向插件根目录下的文件时，只提供该文件，不公开整个插件根目录。适合把 CSS 和 JavaScript 内嵌在 HTML 中的单文件页面。
- 多个入口的资源范围取并集。页面依赖的脚本、样式、图片等应位于这些范围内；复杂页面建议在专用目录中组织入口和资源。

`path` 不接受绝对路径、`..`、隐藏路径、反斜杠或特殊路径字符。宿主不提供 `app.json`、Python 源码和字节码，也不跟随符号链接或 Windows 目录联接。**资源目录中的其他普通文件会公开给已登录的 WebUI 用户**，不要在其中保存私有配置或业务数据。

本地入口 URL 为 `/plugin/<namespace>/<包内相对路径>`，保留路径中的目录层级。访问需要已经登录 WebUI。应从侧栏入口打开页面：单独打开文件或直接访问这个地址不会提供宿主的消息转发桥。

注意区分包内路径、注册路径与浏览器 URL：

| 包内文件（相对于插件根目录） | `webui_config` 中的 `path` | 浏览器 URL |
| --- | --- | --- |
| `webui/index.html` | `webui/` 或 `webui/index.html` | `/plugin/<namespace>/webui/index.html` |
| `webui/pages/settings.html` | `webui/pages/settings.html` | `/plugin/<namespace>/webui/pages/settings.html` |
| `webui/assets/app.js` | 注册 `webui/index.html` 后无需单独注册 | `/plugin/<namespace>/webui/assets/app.js` |
| `panel.html` | `panel.html` | `/plugin/<namespace>/panel.html` |

例如，`webui/index.html` 引用脚本写 `src="./assets/app.js"`，引用样式写 `href="./assets/style.css"`。HTML 中的相对引用按文件位置解析，不应重复添加自身的目录名。若同时注册了 `webui/index.html`，`webui/pages/settings.html` 也可通过 `../assets/app.js` 引用同一脚本，因为整个 `webui/` 都在资源范围内。HTML 中的相对引用与注册字段 `path` 禁止包含 `..` 是两个不同的规则。

宿主直接按注册路径生成入口 URL，不根据目录名添加或省略路径层级，插件应使用相对资源引用。

## 网页发送请求

网页调用 `window.parent.postMessage`，消息格式如下：

```javascript
const requestId = `${Date.now()}-1`;

window.parent.postMessage(
  {
    type: 'olivos:plugin_event',
    event: 'OlivOSPluginTemplate_WebUI_Echo',
    request_id: requestId,
    payload: { text: '你好，OlivOS' },
  },
  '*',
);
```

| 字段 | 说明 |
| --- | --- |
| type | 固定为 `olivos:plugin_event` |
| event | 业务事件名，1 至 256 字符，由插件自己定义 |
| request_id | 用于匹配回包的字符串，不超过 128 字符；每次请求应使用不同的非空值 |
| payload | JSON 可序列化的业务数据，建议使用对象 |

示例只发送一次请求。正式页面应像模板一样增加序号、保存待处理请求、设置超时，并处理发送失败和回包格式错误。宿主最多保留 128 个待回包的页面请求；单个 HTTP 请求体上限为 1 MiB，业务数据应尽量精简。

宿主会根据当前 iframe 确定插件命名空间，并添加认证令牌和浏览器会话信息，然后将请求提交到 `/api/plugin_event`。插件页面不需要也不应该拿到 token 或 session，不能绕过消息桥直接调用核心 REST API。

## Python 处理与回包

网页请求复用 `Event.menu`，不是新的事件类型。`plugin_event.bot_info` 为 `None`，先判断命名空间，再识别网页上下文：

```python
class Event(object):
    def menu(plugin_event, Proc):
        if plugin_event.data.namespace != 'OlivOSPluginTemplate':
            return

        context = getattr(plugin_event.data, 'webui', None)
        if not isinstance(context, dict):
            # 在这里保留原有 menu_config 菜单处理逻辑。
            return

        payload = getattr(plugin_event.data, 'payload', None)
        if plugin_event.data.event != 'OlivOSPluginTemplate_WebUI_Echo':
            response = {'ok': False, 'error': '未知的 WebUI 事件'}
        elif not isinstance(payload, dict) or not isinstance(payload.get('text'), str):
            response = {'ok': False, 'error': '消息必须为文本'}
        elif not payload['text'].strip() or len(payload['text']) > 200:
            response = {'ok': False, 'error': '请输入 1 至 200 字的消息'}
        else:
            response = {'ok': True, 'text': payload['text']}

        plugin_event.send('webui', context['request_id'], response)
```

`plugin_event.data.webui` 包含 `request_id`、`payload` 和宿主管理的 `session`；`plugin_event.data.payload` 是 `payload` 的快捷入口。普通菜单点击没有这两个属性，应使用 `getattr` 判断。网页业务事件名不要求加入 `menu_config`。

`send('webui', request_id, response)` 必须使用当前事件自己的请求 ID 和命名空间，返回值为 `True` 时仅表示回包已入队。回包数据只能包含 JSON 可序列化值；不要直接返回 Python 对象、二进制、账号凭据或整个事件对象。核心负责将回包送回对应浏览器，不需要插件处理会话认证。

回包使用原始事件的 `send`，不是 `reply()`，也不是构造一个没有网页上下文的机器人事件。模板用 `ok`、`text`、`error` 作为业务字段，这些字段由插件定义，不是核心固定的响应格式。

## 网页接收回包

```javascript
window.addEventListener('message', (event) => {
  if (event.source !== window.parent) return;
  const data = event.data;
  if (!data || data.type !== 'olivos:plugin_reply' || data.request_id !== requestId) return;

  const output = document.getElementById('reply');
  if (typeof data.error === 'string') {
    output.textContent = data.error;
    return;
  }
  const payload = data.payload;
  output.textContent = payload?.ok === true ? payload.text : payload?.error || '处理失败';
});
```

HTML 中需有对应的输出节点，例如 `<output id="reply"></output>`。实际页面应先注册接收监听，再发送请求。

核心回包为 `{type: 'olivos:plugin_reply', request_id, payload}`。如果宿主提交 HTTP 请求时失败，会返回顶层 `error` 字符串；插件业务失败则由插件自行组织 `payload`，两者应分别处理。模板还会检查业务回包的数据类型。

接收时必须检查 `event.source === window.parent`、消息类型和待处理的请求 ID。渲染文本使用 `textContent`，不要把回包直接写入 `innerHTML`。切换页面、退出登录或会话失效后，不保证旧请求仍能被展示；插件不应把超时当作业务一定未执行，涉及写操作时需自行处理重复提交。

## 沙箱与资源

本地页面运行在带 `sandbox` 的 iframe 中，同时受两套机制约束：iframe 的 `sandbox` 属性与核心下发的 CSP `sandbox` 指令。**浏览器取更严格的那个**，因此它们使用同一份能力列表。

已放行的常规浏览器能力：

| 放行的 token | 对应能力 |
| --- | --- |
| `allow-scripts` | 执行 JavaScript |
| `allow-forms` | 原生表单提交 |
| `allow-modals` | `alert` / `confirm` / `prompt` |
| `allow-downloads` | 下载（含 blob 与 `<a download>` 导出） |
| `allow-popups`、`allow-popups-to-escape-sandbox` | `window.open` 与 `target="_blank"`，且弹窗不继承沙箱 |
| `allow-pointer-lock`、`allow-orientation-lock`、`allow-presentation` | 指针锁定、屏幕方向锁、投屏 |
| `allow-top-navigation-by-user-activation` | 用户点击触发的整页跳转 |
| `allow-storage-access-by-user-activation` | Storage Access API |

刻意**不**放行两个：

- 没有 `allow-same-origin`，网页是独立的 opaque origin。不能访问父页面 DOM、父页面存储或认证数据，也不能依赖 iframe 自己的 `localStorage`。
- 没有 `allow-top-navigation`，页面不能自行把宿主 WebUI 整页导航走。

其余限制：

- `connect-src 'none'` 禁止网页直接发起 `fetch`、XHR 或 WebSocket 请求。需要读写插件数据时，通过消息桥交给 Python 处理，并在 Python 中校验输入、处理文件或网络异常。
- 默认不允许外部 CDN 脚本和样式。模板将 HTML、CSS 和 JavaScript 放在同一个可读的 `index.html` 中，避免构建工具和额外资源请求。复杂前端应在实际沙箱中验证模块加载与资源访问。
- 宿主不会向页面发布 token、session 或专用主题同步事件。页面维护自己的样式；模板使用 `prefers-color-scheme` 适配浏览器提供的深浅配色。

打开外部文档或教程页面时使用 `<a href="..." target="_blank" rel="noopener">`；弹窗脱离沙箱，外部站点按正常源运行。下载应由用户手势触发（点击），不要尝试在页面加载时自动导出。

沙箱消息发送中使用 `'*'`，并不表示向任意窗口广播：消息发给指定的 `window.parent`，回包也发给指定的 iframe 窗口。宿主验证当前 iframe 的 `source` 和 opaque origin，并绑定插件命名空间；页面仍需检查回包来源与请求 ID。

## 页面保活与可见性

打开过的插件页面会**留在 DOM 中保活**：切到日志、终端等其他页面再切回来不会重新加载，表单内容、滚动位置与页面内状态都会保留。

- 保活数量有上限（默认 10 个，见[用户文档的 `server.plugin_page_cache`](../User/WebUI.md#内置默认值与用户配置)），超出时按最久未使用淘汰，被淘汰的页面下次进入会重新加载。
- 侧栏每个条目右侧的 `×` 只关闭该页面，标题栏的 `×` 一次关闭全部存活页面。
- **被切走（隐藏）的页面仍在后台运行**，定时器与轮询不会自行停止。宿主会向页面发送可见性通知，页面应据此暂停后台工作：

````js
// 声明状态：即使是普通脚本也要显式声明，模块或严格模式下未声明赋值会抛 ReferenceError
let visible = true;

// 让轮询循环真正观察这个状态，而不是只改一个变量
setInterval(() => {
    if (!visible) return;          // 页面在后台时跳过这一轮
    sendRequest('state', { bot_hash: botHash });
}, 5000);

window.addEventListener('message', (event) => {
    if (event.source !== window.parent) return;
    const data = event.data;
    if (!data || data.type !== 'olivos:plugin_visibility') return;
    visible = data.visible;        // false 时暂停轮询，true 时恢复
});
````

关键点是第二段：通知本身只改变状态，**必须由轮询循环去读取它**才会真正停发请求。若定时器已经跑起来，也可以在收到通知时 `clearInterval` / 重新 `setInterval`，效果相同。

不处理这条通知也能正常工作，但页面在后台会继续按原节奏发请求，容易造成不必要的平台调用与日志噪音。新建页面加载完成时宿主也会补发一次，因此页面无需自己猜测初始可见性。

浏览器**刷新会销毁全部保活页面**，只按当前停留的插件页面重建一个；这是最彻底的手工回收方式。

## OPK 打包与静态资源

`.opk` 是将插件目录内的文件压缩为 ZIP 后改后缀得到的包，包根目录应直接包含 `app.json`、`__init__.py` 和声明的页面文件或目录，不要再套一层插件目录。

**打包时必须包含 `webui_config.path` 指向的入口，以及资源范围内的全部静态资源。** 例如 `webui/index.html` 对应的包内结构应包含 `webui/index.html` 和它引用的 `webui/assets/`。单文件页面可直接打包为包根目录下的 `panel.html`。

宿主将 OPK 解包，并按 `app.json` 中的 `namespace` 整理到 `plugin/tmp/<namespace>/`，再导入插件。即使 OPK 放在 `plugin/app/` 的子目录中，或包名与命名空间不同，也会从整理后的目录提取资源，挂载到下面的独立缓存。

**网页资源的释放与清理由宿主管理。** 生命周期如下：

1. OlivOS 启动时清理上次运行留下的 `plugin/cache/webui/` 缓存，随后加载插件并重新释放资源。
2. OPK 导入并完成 `init` 后，宿主按注册路径将网页资源复制到 `plugin/cache/webui/<namespace>/<generation>/`，保留包内相对路径。缓存目录名中的 `webui` 是宿主用途分类，不限制插件包内的资源目录名。
3. 资源复制完成后，完整清理 `plugin/tmp/`，不再在其中保留网页。`init_after` 在此之后执行，插件不应依赖解包目录仍然存在。
4. 插件重载会生成新的资源缓存；WebUI 切换挂载后回收旧缓存。正在发送的文件会在响应关闭后再次尝试清理。卸载插件也会回收其已挂载缓存，包内已删除的旧资源不会混入新页面。

源码文件夹插件直接提供自身资源范围内的文件，不复制到 OPK 缓存。外部 `link` 页面不产生静态资源缓存。



## 常见问题

| 现象 | 检查项 |
| --- | --- |
| 侧栏没有插件入口 | 插件是否加载成功；`webui_config` 是否为列表；`type`、`title`、`path` 是否有效；修改后是否重载 |
| 页面 401 | 浏览器是否已登录、会话是否失效；重新登录后从侧栏打开 |
| 页面 404 | 包内是否包含注册入口及其资源；路径、文件名和大小写是否一致；目录入口是否包含 `index.html`；资源是否超出注册范围；仅 OPK 失败时检查核心版本和资源释放日志 |
| 页面可见但请求超时 | 是否从宿主侧栏打开；事件名和命名空间是否一致；插件是否回包；请求 ID 是否匹配 |
| 普通菜单报属性错误 | 使用 `getattr` 检查 `webui` 和 `payload`，不要假定每个菜单事件都来自网页 |
| 原生 GUI 菜单不能用 | 原生 GUI 不会自动变成网页，需要自行编写页面和 Python 处理逻辑 |

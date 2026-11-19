# 插件定时任务

需要定期刷新缓存、每天清理数据或在指定时间执行一次维护操作时，从 `OlivOS` 导入 `scheduleAPI`。定时任务由插件加载器统一运行，不需要在插件中创建定时线程，也不需要在 `app.json` 里注册事件。此接口要求包含 `scheduleAPI` 的 OlivOS 核心；旧版核心不会识别这些声明。

## 在 `main.Event` 声明任务

示例的单次任务按需启用：在启动 OlivOS 前，将示例自己的环境变量 `EXAMPLE_MAINTENANCE_AT` 设置为**晚于插件加载时刻**的本地日期和时间（例如使用 `YYYY-MM-DDTHH:MM:SS` 格式，不带时区或 UTC 偏移）。这里的本地时间按[宿主调度时区](../User/WebUI.md#plugin-schedule-timezone)解释；没有设置该变量时，只注册两个周期任务。

```python
import os
from datetime import datetime

from OlivOS import scheduleAPI

maintenance_at = os.getenv('EXAMPLE_MAINTENANCE_AT')


class Event:
    @staticmethod
    @scheduleAPI.interval(id='refresh_cache', minutes=30, run_on_start=True)
    def refresh_cache(Proc):
        Proc.log(2, '刷新缓存')

    @staticmethod
    @scheduleAPI.cron(id='daily_cleanup', hour=4, minute=0)
    def cleanup(Proc):
        Proc.log(2, '清理过期数据')

    if maintenance_at:
        @staticmethod
        @scheduleAPI.date(id='maintenance', run_at=datetime.fromisoformat(maintenance_at))
        def maintenance(Proc):
            Proc.log(2, '执行一次维护')
```

`@staticmethod` 放外层。回调是同步函数，只接收 `Proc`；上面的日志调用可替换为插件自己的维护操作。`id` 由 1–64 个 ASCII 字母、数字或 `_.-` 组成，在本插件命名空间内唯一。不同插件可以使用同一个 ID。同一插件重复声明一个 ID 时，冲突的任务不会启用。

插件加载器在全体插件的 `init_after` 阶段结束后注册任务；`init` 或 `init_after` 失败的插件不启动其定时任务。任务按插件声明注册，不按机器人账号重复注册。回调没有收到消息时的 `plugin_event` 或隐式账号；要主动发消息，请显式选择账号并使用[主动调用接口](Note.md)。

## 选触发方式

| 声明 | 何时运行 |
| --- | --- |
| `interval(id, seconds=…, minutes=…, hours=…, run_on_start=False)` | 按固定间隔触发，合计至少一秒。默认等第一个间隔；`run_on_start=True` 让新一代加载器激活后立即触发一次，包括正常重载后。时区和夏令时不改变间隔长度。 |
| `cron(id, hour=…, minute=…, day_of_week=…)` | 按[宿主调度时区](../User/WebUI.md#plugin-schedule-timezone)的本地时间触发，秒固定为 0。星期用 `mon`、`mon-fri` 或 `mon,wed,fri` 等名称。夏令时切换按核心所用 APScheduler 版本的 Cron 规则处理。 |
| `date(id, run_at=…)` | `run_at` 只接受 `tzinfo=None` 的 `datetime`。加载器用宿主调度时区解释这个本地时刻；过期时不补跑，夏令时跳过或重复的本地时刻会被拒绝。 |

插件不传 `timezone=`。例如，`cron(hour=4, minute=0)` 在每台 OlivOS 实例上都表示该实例调度时区的每天 04:00；用户在 WebUI 改时区并成功重载后，下一次执行的 UTC 时刻可能变化。

## 执行、失败与重载

- 同一任务正在排队或执行时，新的触发会跳过，不排队补跑。等待过久的事件也会丢弃，不保证每个周期都执行。任务异常记录在框架日志里，后续周期仍可执行。
- 定时任务可能与其他任务或消息回调并发。访问共享状态时由插件负责同步，对网络请求设置超时，不要在回调里写无限循环。
- 正常重载、更新和停止会先停止接收定时任务，再等待已开始的回调退出，之后才执行 `save`。排空超时时，本次操作取消；旧任务退出后，调度器按未来的计划继续运行。强制退出不保证任务收尾。
- 任务计划保存在内存中，不会补发停机期间漏掉的触发，也不提供跨重载的恰好一次保证。对必须送达的提醒或其他有副作用的操作，插件需要自行保存业务状态并避免重复执行。

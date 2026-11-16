# yxy_ai_memory

保存项目已完成的工作、证据范围和上下文交接，便于后续代理先恢复记忆，再按用户的新要求行动。

更新时间：**2026-11-16，Asia/Shanghai**。

| 项目 | 当前状态 | 文档 |
|---|---|---|
| VU13P 四线 BLE 广播 / DL16 | FPGA 与固件已恢复；CLI 严格解码 4/4 完整包通过；原生 GUI 已视觉确认前八组 / 前导码 AA；用户理解后要求停止，全部 GUI 操作已结束 | [完整进度与交接](projects/vu13p-ble-dl16/PROGRESS.md) · [原生 GUI 人工核对指南](projects/vu13p-ble-dl16/MANUAL_CHECK.md) |

**新上下文先读交接文档。当前没有待自动继续的硬件任务，不要自行重新采集、烧写、连接 JTAG/UART、操作 GUI 或重新连接 RIGOL 示波器。** 原生 GUI 新采集的整包去白化和 CRC 尚未验证，不能把 CLI 的完整解码结论写成原生 GUI 的验证结果。

仓库只收录 Markdown 和两张真实波形图片。原始采样、日志、ATKDL 工程、bitstream、固件与本地交付 ZIP 保留在原机器；位置和校验值见[完整进度与交接](projects/vu13p-ble-dl16/PROGRESS.md)。不收录 API Key、口令或其他凭据。

# yxy_ai_memory

保存项目已完成的工作、证据范围和上下文交接，便于后续代理先恢复记忆，再按用户的新要求行动。

更新时间：**2026-11-16，Asia/Shanghai**。

## 项目索引

| 项目 | 当前状态 | 文档 |
|---|---|---|
| 公司 VPS 代理部署与客户端接入 | 美国入站及 NODE04 链路有历史实测；手机订阅、同事侧接入和重启持久化未全部验收；本次仅脱敏归档，不自动操作线上服务 | [进展与证据边界](projects/vps-proxy-deploy/PROGRESS.md) · [人工复核](projects/vps-proxy-deploy/MANUAL_CHECK.md) · [本机材料与 SHA-256](projects/vps-proxy-deploy/LOCAL_EVIDENCE.md) |
| VU13P 四线 BLE 广播 / DL16 | FPGA 与固件已恢复；CLI 严格解码 4/4 完整包通过；原生 GUI 已视觉确认前八组 / 前导码 AA；用户理解后要求停止，全部 GUI 操作已结束 | [完整进度与交接](projects/vu13p-ble-dl16/PROGRESS.md) · [原生 GUI 人工核对指南](projects/vu13p-ble-dl16/MANUAL_CHECK.md) |

**新上下文先读交接文档。当前没有待自动继续的硬件任务，不要自行重新采集、烧写、连接 JTAG/UART、操作 GUI 或重新连接 RIGOL 示波器。** 原生 GUI 新采集的整包去白化和 CRC 尚未验证，不能把 CLI 的完整解码结论写成原生 GUI 的验证结果。

## 怎么新增一个项目

1. 读 [`AGENTS.md`](AGENTS.md)，按其中的目录、分支和提交约定执行。
2. 只写这一个项目自己的目录：`projects/<项目简称>/`（英文小写加连字符）。
3. 在本文的项目索引表里加一行，**只加自己那行**，不要重排其他行。
4. 需要给其他对话用的提示词，见 [`docs/NEW_CONVERSATION_PROMPT.md`](docs/NEW_CONVERSATION_PROMPT.md)。

不同项目用不同目录互不干扰，默认都提交到 `main`；只在内容可能大改、需要先审阅时才用 `topic/<项目简称>` 短分支，合并后删除。

## 收录范围

仓库只收录 Markdown 和少量真实证据图片。原始采样、日志、ATKDL 工程、bitstream、固件与本地交付 ZIP 保留在原机器；位置和校验值见各项目的 `PROGRESS.md`。不收录 API Key、口令或其他凭据。

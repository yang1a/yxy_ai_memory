# yxy_ai_memory

保存项目已完成的工作、证据范围和上下文交接，便于后续代理先恢复记忆，再按用户的新要求行动。

更新时间：**2026-11-17，Asia/Shanghai**。

## 项目索引

| 项目 | 当前状态 | 文档 |
|---|---|---|
| 公司 VPS 代理部署与客户端接入 | 2026-11-17：已换公网地址、端口收敛到 443、加 Cloudflare CDN 备用入口、NODE04 改走本机 Xray、Sub2API 独立身份；已定位并修复"面板恒为 0 B/离线"的统计缺陷。手机订阅、35 人扩容、流量上限、凭据轮换未完成 | [进展与证据边界](projects/vps-proxy-deploy/PROGRESS.md) · [人工复核](projects/vps-proxy-deploy/MANUAL_CHECK.md) · [本机材料与 SHA-256](projects/vps-proxy-deploy/LOCAL_EVIDENCE.md) |
| VU13P 四线蓝牙 / DL16 | 2026-11-24：双板 BLE、BR、EDR 11 项首轮和冻结版回归均通过，发布包已校验归档。此前 DL16 的 4/4 包严格解码记录保留 | [进度与交接](projects/vu13p-ble-dl16/PROGRESS.md) · [双板实测与源码对照](projects/vu13p-ble-dl16/REGRESSION_20261124.md) · [原生 GUI 指南](projects/vu13p-ble-dl16/MANUAL_CHECK.md) |

**新上下文先读交接文档。当前没有待自动继续的硬件任务，不要自行重新采集、烧写、连接 JTAG/UART、操作 GUI 或重新连接 RIGOL 示波器。** 原生 GUI 新采集的整包去白化和 CRC 尚未验证，不能把 CLI 的完整解码结论写成原生 GUI 的验证结果。

## 怎么新增一个项目

1. 读 [`AGENTS.md`](AGENTS.md)，按其中的目录、分支和提交约定执行。
2. 只写这一个项目自己的目录：`projects/<项目简称>/`（英文小写加连字符）。
3. 在本文的项目索引表里加一行，**只加自己那行**，不要重排其他行。
4. 需要给其他对话用的提示词，见 [`docs/NEW_CONVERSATION_PROMPT.md`](docs/NEW_CONVERSATION_PROMPT.md)。

不同项目用不同目录互不干扰，默认都提交到 `main`；只在内容可能大改、需要先审阅时才用 `topic/<项目简称>` 短分支，合并后删除。

## 收录范围

仓库只收录 Markdown 和少量真实证据图片。原始采样、日志、ATKDL 工程、bitstream、固件与本地交付 ZIP 保留在原机器；位置和校验值见各项目的 `PROGRESS.md`。不收录 API Key、口令或其他凭据。

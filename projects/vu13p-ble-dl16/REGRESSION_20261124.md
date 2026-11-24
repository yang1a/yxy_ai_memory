# VU13P 双板 BLE、BR 和 EDR 回归

2026-11-24，Asia/Shanghai。运行编号 `20261123_2121`。首轮 11 项均通过。冻结 bit、LTX 和各项 A/B 固件后，从第 1 项到第 11 项又全部实测通过。两板读回 `DONE=1`。每项固件在启动前分别完成独立的整份 `verifybin`。原始证据和发布包已回传 Linux，当前处于交付归档状态。

## 双板实测

| 序号 | 用例目录 | 最终结果 |
|---:|---|---|
| 1 | `1m` 固定 LE 1M | 30 ms。B 接收通知，A 校验回写，B 收到成功应答。 |
| 2 | `2m` LE 2M | 30 ms。A、B 均确认 TX/RX 为 2M 后发出首条通知，通知、回写和应答通过。 |
| 3 | `br` BR 1 Mb/s | A、B 分别发起 L2CAP Echo，校验回送的 `NOVA-BR-A` 和 `NOVA-BR-B`。 |
| 4 | `edr2` EDR 2-DH1 | 双向 Echo 通过，实测目标包型，CRC、FEC 和 guard 错误均为 0。 |
| 5 | `edr3` EDR 3-DH1 | 双向 Echo 通过，实测目标包型，CRC、FEC 和 guard 错误均为 0。 |
| 6 | `s2` Coded S=2 短包 | 30 ms，45 槽。通知及回写均为 `rate=3 ci=1 stat=0000`，业务闭环。 |
| 7 | `s8` Coded S=8 短包 | 60 ms，93 槽。通知及回写均为 `rate=2 ci=0 stat=0000`，业务闭环。 |
| 8 | `stress2` S=2 压测 | 1,000 组连续收发，10 次重连，每次重连再交换 1 组，共 1,010 组。 |
| 9 | `stress8` S=8 压测 | 1,000 组连续收发，10 次重连，每次重连再交换 1 组，共 1,010 组。 |
| 10 | `long2` S=2 长包 | 双向 100 字节载荷，100 组。A、B 各校验 400 个接收分片。 |
| 11 | `long8` S=8 长包 | 双向 100 字节载荷，100 组。A、B 各校验 400 个接收分片。 |

两档短包的 A 端 UART 中，`[WRITE_RX]` 与 `[RX_CI] op=12 handle=0015 len=20 seq=0 stat=0000` 的业务序号相同。B 端通知对应 `op=1b handle=0012 len=8 seq=0`。两档压测的序号缺口、重复交付、CRC 失败、ACK 超时与 CI 未解析均为 0。两档长包使用 ATT MTU 247、LL 数据长度上限 27 字节，每方向每组有 4 个 LL 分片。正常断开记录为 A `0x13`、B `0x16`。

本轮证据覆盖板间 QSFP0 四线 VPHY 有线链路。Coded 编码以业务序号匹配接收端的 CI。无线射频性能和蓝牙认证需要独立测试。

## 故障与修复

- S=2 压测初次运行时，中央端先自动切到 2M，Coded PHY 请求返回 `-5`。明确配置两端的自动 PHY 策略后重建，原用例重测通过，再完成冻结版全项回归。Zephyr 本地提交 `e71c81f9c744173b91aed37bac0144fc3d89430e`。
- BR 回归中，A 板曾无法暂停 CPU，也遇到 J-Link 探针弹窗超时。A 用同一 bit 重新配置，最终使用 J-Link 7.82b 完成加载与独立校验，BR 双向 Echo 通过。B 使用 9.24a。失败记录随交付证据保存。
- Nova 验收记录提交 `e01c3d68259462d0d6e2c86f8ffdbf8224854fc8`。

## 固定 LE 1M 源码与日志对照

下表地址来自实际板测的 ELF 指令。A 源文件是 `zephyr-1m/samples/bluetooth/hci_ceva_btdm_dualmode_acceptor/src/main.c`，B 源文件是 `zephyr-1m/samples/bluetooth/hci_ceva_btdm_dualmode_pager/src/main.c`。Linux 源码根目录为 `/home/user007/z_nova_vu13p_coded_20261121/zephyr-1m`，构建基线 `f147a531658d`，B 的 30 ms 配置变更记录为 `9d7bf6fb399`。

| 板卡和事件 | UART 行 | 源码位置 | 执行地址 |
|---|---:|---|---|
| A 连接成功 | 31 | `connected()` 中 `main.c:121` | `0x10001626` |
| B 连接成功 | 32 | `connected()` 中 `main.c:258` | `0x100015F4` |
| A 发送通知 | 35 | `main.c:107` | `0x100016E2` |
| B 校验通知 | 46 | `main.c:151` | `0x10001BDC` |
| B 提交写请求 | 49 | `main.c:127` | `0x10001B3A` |
| A 校验回写 | 39 | `main.c:74` | `0x1000177C` |
| A 发送 ATT 写应答 | — | `subsys/bluetooth/host/att.c:2196` | `0x1000784A` |
| B 收到成功应答 | 51 | `main.c:92` | `0x10001998` |

B 的 `create connection err=0` 对应成功提交连接请求。两端打印 `[BLE] Connected` 对应 Host 回调报告 LE 连接成功。A 发出的通知是 `00 00 00 00 00 56 4F 4E`，B 写回 `01 00 00 00 01 56 4F 4E`。这两组载荷按固件的两个小端 32 位字段 `seq` 和 `stamp` 还原。B 在源码第 145 行比较通知的 8 字节，A 在源码第 67 行比较回写的 8 字节。B 收到 `WRITE_ACK err=0` 后发起断开。

## 发布与证据

Linux 归档目录为 `/home/user007/z_nova_vu13p_coded_20261121/results/windows/ble_coded_full_regression/20261123_2121/`。三档 CI 串口复测原始证据位于 `/home/user007/z_nova_vu13p_coded_20261121/results/windows/ble_coded_ci_uart_20261123/`。该复测独立通过固定 1M、S=2、S=8，S=2 的 A 端 `timeout_list` 地址为 `0x10046B8C`。

| 发布文件 | SHA-256 |
|---|---|
| `vu13p_ble_coded_full_regression_20261123_2121.zip` | `09710bbe5842f363acfc5723e609098e980bb7baeaac658ff028cff370a2b489` |
| `fpga/chip_englishbreakfast_vu13p.bit` | `5ac32619bbc11e42781d5158fc669d75f4aa4c646b08a68a4db44a1faa15fb93` |
| `fpga/chip_englishbreakfast_vu13p.ltx` | `e7cc0bb2f046db0fd285848394fd7368a10cfaac675c9e443d43db49c99dbb38` |

Linux 上已独立解包并校验 `SHA256SUMS` 中的 1,387 项，包内 bit、LTX 和 22 份固件与最终板测记录一致。包内 `RESULTS_CN.md` 是逐项汇总，`first` 是首轮记录，`final` 是最终回归记录，`firmware/<用例>/<A 或 B>/` 是对应固件和配置。查最终 1M 的原文可打开 `final/1m/A_uart/uart_a.txt` 和 `final/1m/B_uart/uart_b.txt`。

Windows 桌面解包副本的 `firmware/源码与编译配置索引.md` 记录 22 份固件的实际 `main.c`、编译配置和 Linux 构建目录。索引通过各构建的 `CMakeCache.txt` 和 `compile_commands.json` 整理，并核对了包内各板 BIN、ELF 和 `.config` 的 SHA-256。

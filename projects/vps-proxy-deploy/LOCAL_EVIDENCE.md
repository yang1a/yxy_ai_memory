# 本机材料、校验值与离线复算

日期：2026-11-16（Asia/Shanghai）。本轮只读取本机材料、核对仓库和计算哈希，没有重新采样线上网络。

## 1. 来源与保密原则

- 本节列出的文件均为**现存本机材料**，本轮用 `Get-FileHash -Algorithm SHA256` 实际计算，不是从聊天猜测。
- 哈希定位的是此次读取的字节版本，不证明文件创建时刻、当时测试全路径或服务器现状。
- 部分 YAML、脚本和旧手册带真实代理身份、订阅信息或面板入口；**只记录位置与哈希，不把正文、完整 URL、数据库或原始日志复制进仓库**。
- 微信来源的旧配置还存在一份；为避免公开私人微信标识，未写出微信账号目录。其内容与下表旧 `vps-us.yaml` 哈希相同。需要时由所有者从微信文件入口定位。
- 项目文件位置：`E:\Users\24242\Documents\deepseek-harness\default-workspace\yxy_ai_memory\projects\vps-proxy-deploy`。

## 2. 文件清单（E1：本轮已核验）

| 本机绝对路径 | SHA-256 | 用途与限制 |
|---|---|---|
| `E:\Users\24242\Documents\deepseek-harness\default-workspace\VPS-VPN部署手册.md` | `0DD1ABC019B8B179518C11159D59F03BB9F0F419E356CD2D8BE5CD848A2AA41B` | 早期操作手册，739 行；含真实接入配置和不准确论断，**不可直接执行或发布**。用户要求删除“关闭密码登录”小节后，本轮内容确认该节已不存在。 |
| `C:\Temp\jptest.txt` | `CD71EFD347503CCD74165C3854302AD9B18142C1B97C2133103CBCE8BCE08A72` | 日本 SSH 隧道测试结果；ping 段没有统计，不补造。 |
| `C:\Temp\speedtest_both.txt` | `2D04B36D3D5FC57966C7268CE5F5591077934C373407ED605A012C442C4E1741` | 美日 TCP 20 次与 VLESS 20 MB 下载 3 轮的本机输出；无完整日期头。 |
| `C:\Temp\jptest.ps1` | `203010A5BE45360B8E43C5C99E615CFBC5B8B9DB8BD27DA76F15F26AAE9A3C65` | 当时测试脚本；硬编码网关、端口与临时路由，缺少完善异常清理。仅供审阅，不直接重跑。 |
| `C:\Temp\speedtest_both.ps1` | `4627E05BDEEBC27E8216E3159F27BBF4FD1D1A1898DFC0ECF982ABF9F3A5FB9F` | 当时双节点脚本；**含真实客户端凭据**，不能提交；“retrans”字段实际只数 >500 ms。 |
| `C:\Users\24242\Desktop\vps-us.yaml` | `4A7B9D2E3AD7DC729084BF8E7FCEEA44014B8569AE8A106CE7865170C5BEA61B` | 旧美国单节点，旧 SNI，不能直接当当前有效配置。 |
| `C:\Users\24242\Desktop\jp-tokyo-clash.yaml` | `2812D2FAD142022522FB941C6E4C59B6F57312A448C595CF3423FE71329DB4C9` | 历史日本节点；所有权/退款未确认，不继续连接。 |
| `C:\Users\24242\Desktop\friends-clash.yaml` | `1FE31E754FEE02ACB6E16DCA2CFAEB015305A2BDE90AC334ADE7F8EBD0DE938B` | 历史 friends 单节点；当前凭据与 SNI 是否仍一致需所有者核验。 |
| `C:\Users\24242\Desktop\配置-最新.yaml` | `9B49B817FD451F98B02D47EDDD6EED3A1A337502A8916C68D83161B71B9F029A` | 2026-11-16 生成的公司+friends 配置；只是文件已生成，不代表手机已导入。名称“最新”不等于现在线上一定有效。 |

注意：Windows 桌面可能重定向。历史创建英文 Edge 快捷方式时返回过 `E:\桌面`；不要假定 `C:\Users\24242\Desktop` 就是用户眼前的桌面。

## 3. 哈希复算命令（本机，只读）

```powershell
Get-FileHash -Algorithm SHA256 -LiteralPath 'C:\Temp\speedtest_both.txt'
Get-FileHash -Algorithm SHA256 -LiteralPath 'C:\Users\24242\Desktop\配置-最新.yaml'
Get-FileHash -Algorithm SHA256 -LiteralPath 'E:\Users\24242\Documents\deepseek-harness\default-workspace\VPS-VPN部署手册.md'
```

若哈希不一致，先定位文件是否已修改；不要把新版本的结果反填到旧测试。

## 4. 双节点数字离线复算（不连接任何主机）

以下代码只解析现存日志，计算 connect 时间和下载摘要，不跑脚本、不改路由、不启动代理：

```powershell
$path = 'C:\Temp\speedtest_both.txt'
$section = ''
$tcp = @{ US = @(); JP = @() }
$downloads = @{ US = @(); JP = @() }

foreach ($line in Get-Content -LiteralPath $path) {
    if ($line -match '^===== US RackNerd TCP') { $section = 'US-TCP'; continue }
    if ($line -match '^===== JP HostDare TCP') { $section = 'JP-TCP'; continue }
    if ($line -match '^===== US RackNerd end-to-end') { $section = 'US-DL'; continue }
    if ($line -match '^===== JP HostDare end-to-end') { $section = 'JP-DL'; continue }

    if ($section -match '^(US|JP)-TCP$') {
        $key = $Matches[1]
        if ($line -match '^\s+OK\s+(\d+) ms$') {
            $tcp[$key] += [int]$Matches[1]
        }
    } elseif ($section -match '^(US|JP)-DL$') {
        $key = $Matches[1]
        if ($line -match '^\s+run\d+: (\d+)\|([\d.]+)\|(\d+)') {
            $downloads[$key] += [pscustomobject]@{
                BytesPerSecond = [long]$Matches[1]
                Seconds = [double]::Parse($Matches[2], [Globalization.CultureInfo]::InvariantCulture)
                HTTP = [int]$Matches[3]
            }
        }
    }
}

foreach ($key in @('US', 'JP')) {
    $values = $tcp[$key]
    $m = $values | Measure-Object -Minimum -Maximum -Average
    $slow = @($values | Where-Object { $_ -gt 500 }).Count
    Write-Output "$key n=$($values.Count) min=$($m.Minimum) max=$($m.Maximum) avg=$($m.Average) slow_gt_500ms=$slow"
    $downloads[$key] | Format-Table -AutoSize
}
```

本轮严格复算结果：美国 n=20、均值 **269.75 ms**、>500 ms **2** 次；日本 n=20、均值 **628.45 ms**、>500 ms **7** 次。

该复算不会把样本数当长期成功率，也不会将慢连接推成 SYN 重传。下载部分需同时看 HTTP、字节量和测试对象。

## 5. 远端续接位置（仅作定位，不代表此次核验）

| 角色 | 历史位置 / 参数 | 下一轮需确认什么 |
|---|---|---|
| US_VPS | `/etc/x-ui/x-ui.db`；运行生成配置 `/usr/local/x-ui/bin/config.json` | 面板版本、启用入站、生成配置与客户端参数对应关系。文件含凭据，不应全文输出/提交。 |
| US_VPS | 公司 TCP 2053；friends TCP 22537；管理 54321；订阅 2096 | UFW 当前规则、证书有效期、订阅是否仍明文。 |
| US_VPS | 历史备份 `/root/x-ui.db.bak.2026-11-12-0254`、`/root/x-ui.db.bak.2026-11-14-0907`、`/root/x-ui.db.bak.2026-11-14-0918`、`/root/x-ui.db.bak.2026-11-16-1011` | 这些是会话中打印过的路径；本轮没有读文件或算哈希。回滚前必须判断是否会覆盖后来的客户改动。 |
| NODE04 | `/etc/mihomo/config.yaml`；`/etc/systemd/system/mihomo.service` | 规则组、持久化、自启动与当前出站；原第三方订阅不得泄露。 |
| NODE04 | `/home/user705/.config/systemd/user/socks-tunnel.service` | 用户 manager 与 linger；不能只 `su - user705` 就假定能访问其 D-Bus。 |
| NODE04 | `/home/user705/.config/environment.d/10-proxy.conf` 与用户 shell 配置 | 哪些程序继承、哪些不支持 SOCKS，NO_PROXY 范围是否合适。 |
| NODE04 | `/root/apply-tun.py`；历史目录 `/root/mihomo-backup-2026-11-10-1704` | 历史脚本不保证幂等；不要当作安全的一键恢复工具。 |
| NODE04 | `/etc/systemd/system/iprule-lan-fix.service`（仅建议过） | 文件是否真实存在、服务是否 enabled、重启后规则是否仍在，尚未确认。 |
| Windows | `E:\PRO_APP\vpn_v2\v2rayN-windows-64`；`C:\Program Files\Clash Verge` | 哪个实例实际运行、是否管理员启动、实际配置路径与 core 版本。 |

没有远端材料 SHA-256：本次仅文档归档授权，不进行新线上读取；不编造空缺校验值。

## 6. 截图与日志

本对话存在许多真实截图，但包含邮箱、面板入口、订阅 token、UUID、密码或客户端识别信息。本次**不提交任何原始截图及日志**，因此没有 `assets/` 目录。

证据来源在 PROGRESS 中区分为历史工具输出、用户终端文本和视觉确认。将来若需关键图片，应取得真实原图、做必要脱敏并复核，再单独放入本项目的 `assets/`。不要画一张示意图冒充实测截图。

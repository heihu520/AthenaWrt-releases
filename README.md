# AthenaWrt Releases

**AthenaWrt** 是面向 **京东云 RE-CS-02（雅典娜 / IPQ6018 / ARM64 / eMMC）** 定制的 ImmortalWrt 固件发布仓库。本仓库仅用于托管签名固件镜像、更新清单与可选插件安装包，源码在私有仓库维护。

---

## 快速下载

| 发布渠道 | 当前版本 | 内容说明 | 入口 |
|---|---|---|---|
| **`channel-stable`** | **AthenaWrt `1.0.11`** | 完整固件（`lan10` / `lan192`）、`SHA256SUMS` 与 `usign` 签名清单 | [前往下载](https://github.com/heihu520/AthenaWrt-releases/releases/tag/channel-stable) |
| **`athenaclaw-latest`** | **AthenaClaw `v0.10.0`** | 独立可选 AI 运维智能体插件（ARM64 单二进制，按需安装，**不预置进固件**） | [前往下载](https://github.com/heihu520/AthenaWrt-releases/releases/tag/athenaclaw-latest) |

---

## 固件核心特性

- **Vantage 现代控制台界面（`v1.2.2`）**：默认内置 Vantage 主题与仪表盘（不含 Argon），全站自制页面（服务控制、DNS 容灾、访问排查、应用与数据更新、系统更新、Dufs、上网记录）统一跟随深浅色主题。
- **定制仪表盘三磁贴**：
  - **OpenClash 磁贴**：实时展示当前节点、延迟、国内外出口 IP，基于 mihomo 内核 `/proxies/<node>/delay` 接口，支持 16 项自定义服务测速；
  - **AdGuard Home 磁贴**：下游查询、拦截率、缓存命中率与活跃设备数；
  - **DNS 链路磁贴**：当前链路模式、容灾状态、监听端口与查询速率。
- **签名更新 & 轻量热更新**：
  - **完整版在线升级**：内置 `usign` 公钥校验清单与镜像 SHA-256，升级前自动备份配置至 `/mnt/data/athenawrt-backup/`（保留最近 3 份），并通过 `keep.d` 自动保留 OpenClash、AdGuard Home、Tailscale、ksmbd、上网记录、MetaCubeXD 面板及已装 AthenaClaw 插件；
  - **轻量增量热更新（Hotfix）**：前端页面与管理脚本支持几十 KB 签名补丁包秒级免重启应用，异常自动回滚，支持历史版本一键回退。
- **网络与服务治理**：
  - 内置 OpenClash（mihomo + MetaCubeXD）、AdGuard Home、Tailscale（含直连出网规则、路由自愈与管理端口防火墙自愈）、Dufs（含 `/mnt/data` 安全保护）、ksmbd、Netdata、nlbwmon、Wake-on-LAN、eMMC 健康检查与三频 Wi-Fi 校准固件；
  - 支持 DNS 链路一键切换、`Redir-Host 混合 / Fake-IP` 切换、DNS 自动容灾降级与访问排查分页分析。

---

## 镜像变体与选型

| 变体 | 默认 LAN 地址 | 适用场景 |
|---|---|---|
| `lan10` | `10.0.0.1` | 10 段主路由环境 |
| `lan192` | `192.168.10.1` | 192.168 段常规路由环境 |

每个变体提供两种镜像文件：
- **`*-factory.bin`**：首次刷机 / 底层重刷用完整镜像；
- **`*-sysupgrade.bin`**：已有系统升级用镜像（校验设备型号 `jdcloud,re-cs-02`）。

> **⚠️ 刷机与升级提示**
> 1. 京东云 RE-CS-02 **没有备用系统分区**，升级失败无法自动回滚，请务必在升级前先下载配置备份。
> 2. 已运行 AthenaWrt 的设备，建议直接进入 **系统 → AthenaWrt 更新 → 检查更新** 在线升级；升级完成后如页面仍显示旧版本号，请**强制刷新浏览器缓存**（`Ctrl+Shift+R` 或手机端清站点缓存）。
> 3. 5G Wi-Fi 建议保持固件默认的非 DFS 固定信道（`radio0=149`、`radio2=36`），避免自动选到 DFS 信道导致开机雷达检测期间约 1 分钟搜不到信号。

---

## 可选插件：AthenaClaw AI 运维智能体

**AthenaClaw** 是专为 AthenaWrt 打造的轻量级 Go ARM64 单二进制 AI 运维助手（常驻内存约 8 MiB，零外部依赖，**不会预装在固件中**）：

- **双端交互**：支持 LuCI 顶栏「智能体」网页端（实时流式输出 + 「⏹ 停止」中止 + 多会话管理）与 Telegram Bot（流式编辑、快捷菜单与远程告警）。
- **安全排障**：内置 14 个并行只读运维工具（系统总览、服务状态、日志、网络诊断、OpenClash/AdGuard 排查、Wi-Fi 状态、端口/防火墙、eMMC 健康等）与 4 个写操作（重启服务、切节点、应用热更新、受控 Shell），所有写操作均强制 5 分钟二次确认并自动备份 `/etc/config` 与记录审计日志。
- **长期记忆与 Skill 技能**：数据存放在 `/mnt/data/athenaclaw`，支持自动上下文压缩、记忆提炼去重、`SKILL.md` 外部导入以及 Watchdog 故障告警与恢复闭环。

### 安装与升级方式
1. **推荐（图形界面一键安装）**：在路由器进入 **系统 → AthenaWrt 更新**，点击「AthenaClaw 智能体插件」行的 **安装插件 / 更新插件** 即可（安装完成后刷新或重新登录 LuCI 即可在顶栏看到「智能体」入口）。
2. **插件内自更新**：已安装用户可直接在 **智能体 → 设置 → 检查更新** 自动校验 SHA-256 并一键升级。

---

## 校验说明

每次发布均附带 `SHA256SUMS-lan10`、`SHA256SUMS-lan192`（固件）及 `athenaclaw-<ver>-arm64.tar.gz.sha256`（插件）。下载后可执行：

```sh
sha256sum -c SHA256SUMS-lan10
```

确认全部为 `OK` 后再进行刷机或安装。

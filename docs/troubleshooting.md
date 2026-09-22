# v2rayA troubleshooting for Ubuntu / Debian

本文提供分层诊断路径，不提供未经验证的一键修复。命令输出可能包含 IP、域名、
文件路径或其他环境信息；公开前请清理订阅 URL、密码、UUID、token、节点地址和
公网 IP。

> [!WARNING]
> 本文未在全新 Ubuntu 22.04 或 Ubuntu 24.04 上完成 2026 年端到端验证。
> 路径、unit 名称、端口与行为可能随安装方式和版本变化。

## 1. Record the environment

先记录环境，避免把不同发行版、架构、软件来源或核心实现的问题混在一起。

```bash
cat /etc/os-release
dpkg --print-architecture
v2raya --version
```

如果 `v2raya --version` 不可用，也应记录 `apt policy v2raya` 的输出以及实际安装方式。
同时注明使用的是随包提供的核心、V2Ray core、Xray-core，还是手动安装的其他核心。

## 2. Check the service

```bash
systemctl status v2raya --no-pager
journalctl -u v2raya -e --no-pager
```

关注第一条明确错误，而不仅是最后一行。区分以下状态：unit 不存在、启动失败、
启动后退出、持续重启、服务运行但核心未启动。不要在未读日志前反复重装。

如需分享日志，先复制到本地文本并人工脱敏。不要粘贴完整配置文件。

## 3. Check the core

确认日志所指向的核心二进制文件：

- 是否实际存在且具有执行权限；
- 架构是否与系统匹配；
- 版本是否与当前 v2rayA 兼容；
- systemd 服务用户是否有权读取或执行；
- 手动安装路径是否与 v2rayA 的配置一致。

不要仅因为存在名为 `v2ray` 或 `xray` 的文件，就认为核心已被 v2rayA 正确发现。

## 4. Check the Web UI

先确认服务运行，再检查实际监听状态：

```bash
sudo ss -lntp
```

历史教程使用 `http://localhost:2017`，但应以实际监听结果和当前配置为准。

- 本机无法访问：检查服务日志、监听地址、端口冲突和本机防火墙；
- 远程无法访问：区分 loopback 监听、主机防火墙、云安全组和路由问题；
- 首次初始化：不要把未设置管理员账号的界面暴露到不受信任网络或公网。

## 5. Check geoip / geosite rule data

若日志明确报告 `geoip.dat` 或 `geosite.dat` 缺失：

1. 记录日志中的预期路径和准确错误；
2. 确认文件是否存在、大小是否合理、服务用户是否可读；
3. 确认文件来自所用版本认可的可信来源；
4. 核对安装包是否本应提供这些文件，还是首次启动时下载；
5. 检查网络、DNS、证书和代理是否阻止下载。

不要从未知镜像下载规则文件，也不要在不确认预期路径和版本的情况下到处复制文件。

## 6. Service works, but traffic does not

把问题拆分为不同层面：

1. **节点/核心层**：核心能否启动，节点是否可用，系统时间是否正确；
2. **应用代理层**：应用是否连接到实际监听的 SOCKS5/HTTP 地址与端口；
3. **DNS 层**：域名解析使用哪个 resolver，是否发生污染、超时或回环；
4. **路由层**：默认路由、策略路由及局域网绕过规则是否符合预期；
5. **防火墙层**：nftables/iptables 规则是否冲突；
6. **透明代理层**：所选模式、TUN、转发、权限及内核能力是否满足要求。

每次只改变一个变量并记录结果。若关闭透明代理后网络恢复，应先收集路由、DNS 与
防火墙状态，再决定下一步；不要直接执行来源不明的规则清空命令。

## 7. Information for a useful issue

提交 Issue 时至少提供：

- OS、Ubuntu/Debian 版本、架构；
- v2rayA 版本、核心类型与版本、安装方式；
- 脱敏后的 `systemctl status v2raya`；
- 脱敏后的相关 `journalctl -u v2raya` 日志；
- 期望行为、实际行为、可复现步骤；
- 是否启用透明代理，以及问题发生前最后一次配置变更。

请使用仓库的 [Bug report 模板](../.github/ISSUE_TEMPLATE/bug_report.yml)。

## 8. Upstream references

- [v2rayA documentation](https://v2raya.org/docs/)
- [v2rayA repository](https://github.com/v2rayA/v2rayA)
- [v2rayA issues](https://github.com/v2rayA/v2rayA/issues)

若问题属于 v2rayA 本体或核心，请先搜索对应上游项目。向上游报告时同样需要脱敏。

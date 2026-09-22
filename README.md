# v2rayA Ubuntu Guide

> Ubuntu / Debian 下 v2rayA 安装、基础配置与故障排查实战指南。
>
> A practical v2rayA installation and troubleshooting guide for Ubuntu/Debian.

> [!WARNING]
> **验证状态 / Validation status**
>
> 本指南包含最初在较早 Ubuntu / v2rayA 环境中记录和使用的安装步骤。截至
> **2026 年 9 月**，完整安装流程尚未在全新 Ubuntu 22.04 或 Ubuntu 24.04
> 环境中重新验证。软件源地址、安装脚本、软件包、端口或上游行为可能已经变化。
>
> This guide contains installation steps originally documented for earlier
> Ubuntu/v2rayA environments. As of **September 2026**, the complete workflow
> has **not been re-validated from scratch on Ubuntu 22.04 or Ubuntu 24.04**.
> Repository URLs, install scripts, packages, ports, and upstream behavior may
> have changed.
>
> 在生产系统使用前，请对照 [v2rayA 官方文档](https://v2raya.org/docs/)
> 和 [v2rayA 官方仓库](https://github.com/v2rayA/v2rayA) 核对。后续维护将逐步补充
> 可复现的版本验证记录。

## Project overview

这是一个由社区独立维护的非官方文档仓库，面向 Ubuntu / Debian 用户，聚焦
v2rayA 的安装、基础配置和故障诊断。v2rayA 是管理代理核心与路由配置的 Web
客户端；V2Ray core 与 Xray-core 是不同的上游核心项目。本仓库不是 v2rayA、
V2Ray 或 Xray 的官方项目，也不提供这些项目的软件包或支持承诺。

历史教程最初记录于 **Ubuntu 20.04（2023 年）**。以下命令仅为保存历史价值，
不代表它们在当前系统上仍然有效。

## Quick navigation

- [Prerequisites](#1-prerequisites)
- [Install core](#2-install-core)
- [Install v2rayA](#3-install-v2raya)
- [Start / enable service](#4-start--enable-service)
- [Access Web UI](#5-access-web-ui)
- [Basic configuration](#6-basic-configuration)
- [Verify installation](#7-verify-installation)
- [Troubleshooting](#8-troubleshooting)
- [Upgrade](#9-upgrade)
- [Uninstall](#10-uninstall)
- [Upstream projects](#11-upstream-projects)
- [Contributing](#12-contributing)
- [License / attribution](#13-license--attribution)

## 1. Prerequisites

开始前请确认：

- 使用 Ubuntu 或 Debian，且拥有 `sudo` 权限；
- 知道系统版本与架构：`cat /etc/os-release`、`dpkg --print-architecture`；
- 已备份现有网络、DNS、防火墙和代理配置；
- 不在命令、Issue 或截图中泄露订阅 URL、节点密码、UUID、token 等敏感信息。

## 2. Install core

> [!CAUTION]
> 本节是 **2023 年历史流程，Pending re-validation**。管道执行远程脚本前应先
> 独立下载、检查内容及来源。这里保留原命令，不表示推荐当前继续使用。

历史教程将 V2Ray core 作为 v2rayA 的依赖，并记录了以下安装命令：

```bash
curl -Ls https://mirrors.v2raya.org/go.sh | sudo bash
```

选择核心及安装方式前，请查阅当前上游资料：

- [v2rayA 官方安装说明](https://v2raya.org/docs/prologue/installation/)
- [V2Ray 官方安装脚本](https://github.com/v2fly/fhs-install-v2ray)
- [Xray 官方安装脚本](https://github.com/XTLS/Xray-install)

不要同时假定 V2Ray core 与 Xray-core 的安装位置、软件包名称和兼容性完全相同。

## 3. Install v2rayA

> [!CAUTION]
> 以下 APT key 路径、软件源地址与软件包组合均为历史记录，尚未在 Ubuntu
> 22.04 / 24.04 上重新验证。执行前应与当前官方文档比较；本轮治理没有替换命令。

历史步骤 1：添加公钥。

```bash
wget -qO - https://apt.v2raya.org/key/public-key.asc | sudo tee /etc/apt/trusted.gpg.d/v2raya.asc
```

历史步骤 2：添加软件源并刷新索引。

```bash
echo "deb https://apt.v2raya.org/ v2raya main" | sudo tee /etc/apt/sources.list.d/v2raya.list
sudo apt update
```

历史步骤 3：安装 v2rayA。

```bash
sudo apt install v2raya
```

上述历史命令没有证明代理核心已正确安装、版本匹配或规则数据已就绪。

## 4. Start / enable service

历史教程使用以下命令启动服务：

```bash
sudo systemctl start v2raya.service
```

如需开机自动启动，请先依据当前软件包与上游说明确认 unit 名称和行为，再决定是否
执行 `systemctl enable`。不要把“包安装成功”等同于“服务已运行且代理可用”。

## 5. Access Web UI

历史环境通过以下地址访问 Web UI：

<http://localhost:2017>

端口 `2017` 及监听地址需要以本机实际服务状态和当前上游文档为准。若从其他设备
访问，先评估防火墙与未初始化管理界面的暴露风险；不要直接把管理端口开放到公网。

![v2rayA Web UI 登录界面](docs/v2raya2.png)

历史界面首次进入时需要创建管理员账号。旧教程还记录了以下密码重置命令，但当前
版本是否仍支持该参数尚未重新验证：

```bash
sudo v2raya --reset-password
```

## 6. Basic configuration

历史操作路径如下，仅用于帮助识别旧版界面：

1. 在 Web UI 中导入订阅或手动添加节点；
2. 更新订阅并选择节点；
3. 启动核心；
4. 按需设置应用代理或透明代理。

![旧版 v2rayA 节点列表](docs/v2raya3.png)

![旧版 v2rayA 运行状态](docs/v2raya4.png)

历史教程称核心可能提供 `20170`（SOCKS5）、`20171`（HTTP）和 `20172`
（带路由规则的 HTTP）端口。这些端口、监听范围和用途尚未在当前版本验证，请以
实际配置、监听状态和上游文档为准。

![旧版 v2rayA 代理配置界面](docs/v2raya5.png)

透明代理会涉及 DNS、路由、防火墙以及 nftables/iptables/TUN 等系统组件。
启用前应先了解回滚方法；不要将旧版截图视为当前版本的推荐配置。

## 7. Verify installation

本节只提供诊断检查，不构成安装流程已通过验证的声明。

```bash
systemctl status v2raya --no-pager
journalctl -u v2raya -e --no-pager
```

还应分别确认：

- v2rayA 服务是否处于运行状态；
- 所需核心是否存在、可执行且与 v2rayA 兼容；
- Web UI 是否在预期地址监听；
- `geoip.dat` / `geosite.dat` 等规则数据是否存在且可读；
- 应用层代理、DNS、路由和透明代理分别是否按预期工作。

更完整的分层检查见 [Troubleshooting](docs/troubleshooting.md)。

## 8. Troubleshooting

遇到 `geoip.dat` / `geosite.dat` 缺失、服务异常、Web UI 无法打开或安装后无法
联网时，请先收集事实，再修改网络配置：

- [故障排查指南](docs/troubleshooting.md)
- [提交 Bug report](https://github.com/ningmoon/v2raya-ubuntu-guide/issues/new?template=bug_report.yml)

公开日志前务必移除订阅 URL、节点地址、密码、UUID、token、公网 IP 及其他隐私信息。

## 9. Upgrade

本仓库尚未验证当前 APT 源、跨版本升级路径、核心与 v2rayA 的兼容矩阵。升级前请：

1. 阅读 v2rayA 的当前 release notes 与官方安装文档；
2. 备份配置并记录已安装版本、软件来源和核心类型；
3. 确认有可执行的回滚方案；
4. 在非生产环境先行验证。

此处不提供未经验证的一键升级命令。

## 10. Uninstall

卸载可能涉及软件包、服务、APT source、key、规则数据、配置以及由透明代理写入的
网络状态。由于不同安装方式的文件范围不同，本仓库暂不提供未经验证的一键清理命令。
请先确认当初的安装方式，并参考对应上游卸载说明；保留诊断日志前仍需清理敏感信息。

## 11. Upstream projects

- [v2rayA](https://github.com/v2rayA/v2rayA) — Web 客户端与服务端项目；
- [v2rayA documentation](https://github.com/v2rayA/v2raya.github.io) — 官方文档源码；
- [V2Ray core / V2Fly](https://github.com/v2fly/v2ray-core) — 独立的代理核心项目；
- [Xray-core](https://github.com/XTLS/Xray-core) — 独立的代理核心项目。

本仓库与上述项目没有官方隶属或背书关系。各项目的名称、商标、代码与文档归其
各自权利人所有，并分别受其自身许可证约束。

## 12. Contributing

欢迎补充可复现的 Ubuntu / Debian 版本验证记录和诊断路径。修改安装步骤时，请说明
实际测试的系统版本、架构、v2rayA 版本、核心、安装方式和测试日期。详见
[CONTRIBUTING.md](CONTRIBUTING.md)。变更记录见 [CHANGELOG.md](CHANGELOG.md)。

## 13. License / attribution

除特别注明外，本仓库原创文档由 `ningmoon` 以
[CC BY-SA 4.0](LICENSE) 许可。

README 的部分历史安装说明来自或改编自
[v2rayA 官方文档](https://github.com/v2rayA/v2raya.github.io)，其非代码内容同样采用
CC BY-SA 4.0；本仓库保留来源说明并以相同方式共享修改内容。第三方软件、代码、
商标、截图以及链接内容不因收录于本仓库而自动改用本仓库许可证。详见 [LICENSE](LICENSE)。

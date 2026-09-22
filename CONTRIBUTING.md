# Contributing

感谢你帮助改进这份社区指南。仓库重点是可复现、可审计的 Ubuntu / Debian 文档，
不追求堆叠未经验证的安装方式。

## Installation changes

修改安装、升级或卸载步骤时，请同时说明：

- 实际测试的 Ubuntu/Debian 版本与架构；
- v2rayA 版本、核心类型与版本；
- 安装方式与测试日期；
- 从干净环境开始的关键前置条件；
- 验证了哪些结果，哪些仍未验证。

若没有实际验证，请明确标注 proposal 或 pending validation，不要使用 “verified”、
“tested” 或类似表述。

## Troubleshooting changes

优先贡献诊断路径、可观察信号和安全的回滚思路。避免未经验证的一键修复，尤其是会
清空防火墙、路由、DNS 或删除配置的命令。

## Privacy and security

不得提交真实的订阅 URL、节点密码、UUID、token、私钥、账号、公网 IP 或其他敏感
配置。日志、截图与示例必须先脱敏。管道执行远程脚本的示例应说明来源和风险。

## Attribution and license

引用或改编上游资料时请链接到具体来源，注明改编关系，并遵守其许可证。贡献到本仓库
的原创文档将按 [CC BY-SA 4.0](LICENSE) 发布；第三方材料仍受原许可证约束。

提交前请运行：

```bash
git diff --check
```

并人工检查所有相对链接、命令、版本声明与隐私信息。

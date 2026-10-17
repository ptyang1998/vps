# VPS 个人代理部署与维护技能

一个可复用的 Codex 技能，用于自有 VPS 的代理部署、Windows / Android / iPhone / Mac 接入、DNS 分流、延迟诊断、BBR 对照测试，以及恢复和跨设备交接。

以 **Xray + VLESS + TCP + 标准 TLS** 为一条可选实施路径。已有可用服务时优先接入和核验，协议选择仍以用户需求、网络和客户端能力为准。系统代理和 TUN 的覆盖范围分别验收。

本仓库提供通用技能和方法文档，不包含任何真实部署记录、私人账户、服务器地址、节点 UUID、密码、令牌、私钥、订阅链接或可连接的配置。示例中的大写占位符需要在使用者自己的私密环境中填写。

## 使用

将 [skills/vps-proxy-workflow](skills/vps-proxy-workflow) 文件夹完整放入 Codex 的技能目录，例如 `~/.codex/skills/`，然后在支持技能的会话中调用：

```text
使用 $vps-proxy-workflow，帮我把现有 VPS 接入这台电脑。
先检查本机客户端、实际代理端口和生效配置，备份后再修改。
```

也可以要求只诊断延迟、准备手机配置、撤销特定规则，或整理脱敏交接文档。安装本技能不会自动连接服务器或更改设备设置。

## 文件导航

| 文件 | 内容 |
| --- | --- |
| [SKILL.md](skills/vps-proxy-workflow/SKILL.md) | 技能入口、任务范围、实施原则与验收要求 |
| [服务器部署](skills/vps-proxy-workflow/references/server.md) | 协议选择、服务管理、TLS、续期与授权收尾 |
| [客户端与分流](skills/vps-proxy-workflow/references/clients.md) | 本机导入、DNS、固定节点规则、手机接入与冲突排查 |
| [性能与延迟](skills/vps-proxy-workflow/references/diagnostics.md) | 分层测量、统一延迟、BBR A/B 与流量配额解释 |
| [维护与交接](skills/vps-proxy-workflow/references/maintenance.md) | 恢复、公开发布检查、可选应用维护与时区设置 |

## 一次完整实施应交付什么

1. 修改前的本地私密备份，以及明确的恢复入口。
2. 与当前环境匹配的服务器或客户端配置，凭据通过私密渠道交付。
3. 实际运行配置、TLS、出口、分流和目标应用的验收结果。
4. 若做优化，保留相同条件下的前后样本、失败记录和结论边界。
5. 区分“已验证”“历史记录”“仅准备文件”和“待设备实机验证”的交接文档。

本技能是按环境执行的工作流程，不是一键部署脚本。它不会预置应用专属路由、自动清理登录数据、修改时区或发布实际配置；这些工作需要属于用户当前任务。

## Shadowrocket 分流配置

[配置文件](configs/shadowrocket-cn-direct.conf)可供 iPhone、iPad 上的 Shadowrocket 从 URL 下载。打开文件的 Raw 页面复制直链，在 Shadowrocket 的“配置”页添加 URL；不要将它作为节点订阅导入。

这份文件只包含规则，不包含节点凭据。先私密导入自己的节点，再在首页选中该节点；`PROXY` 使用首页当前选中的代理。此配置不会新建或固定任何服务器节点。

分流逻辑：

- 局域网、常见国内域名集、`.cn` 域名及中国大陆 IP 直连。
- 其他请求按 `FINAL,PROXY` 使用当前节点。
- DNS 使用系统设置，保留 iOS 系统兼容旁路；这不是严格的全流量接管或泄漏防护配置。

国内域名数据引用 [blackmatrix7 的 China_Domain.list](https://github.com/blackmatrix7/ios_rule_script/blob/master/rule/Shadowrocket/China/China_Domain.list)，以 `DOMAIN-SET` 加载；IP 分类使用客户端的 GeoIP 数据库。分类可能有遗漏，不能保证所有页面的全部资源都会直连。

使用前备份手机原配置，作为独立新配置下载并选择“使用配置”，然后将首页“全局路由”设为“配置”。下载配置和远程域名集时可先连接已有节点；确认远程列表加载成功。此操作不合并旧配置中的自定义规则、模块或 DNS，原配置仍可切回；已有模块也可能优先匹配，需要在手机核验。

验收时分别打开国内和海外网站，检查连接记录的规则及最终出站。代理链路的证书验证仍由节点设置负责，不需要安装 HTTPS 解密证书。规则导入不会直接提升跨网带宽，手机实测通过后才能确认具体视频效果。

# astrbot_plugin_touchgal - SAKURA.md

## 项目简介
为 AstrBot 机器人提供的 Galgame 搜索与下载插件，通过接入 TouchGal API（https://www.touchgal.us/） 实现游戏信息查询及资源获取。

## 技术栈
- 核心框架：AstrBot 插件系统（Python）
- 外部接口：TouchGal API
- 依赖管理：requirements.txt
- 配置管理：JSON Schema + 控制面板配置

## 架构设计与关键决策
- **命令格式**：以斜杠 / 开头，遵循 AstrBot 标准
- **配置入口**：统一通过 AstrBot 控制面板管理
- **展示名机制**：V1.4 支持插件展示名与目录名不同
- **HTTP 请求（重要）**：第三方 API 存在反爬校验，需添加浏览器请求头（User-Agent、Referer、Origin 等）方可正常访问。当前已发现上游要求携带 `x-requested-with: kun-fetch` 自定义头。

## 已知问题与注意事项
- **反爬时效性**：硬编码的 User-Agent（如 Chrome 120）会过时，后续可能导致 403 错误，需定期更新或引入动态 UA。
- **代码不一致**：`search_game` 已添加请求头，但 `get_downloads` 和 `download_and_convert_image` 方法尚未同步，存在潜在失效风险。
- **缺少基础设施**：项目未封装统一 HTTP 请求方法（如 `_request()`），headers、超时、重试逻辑分散在各方法中，易产生技术债务。
- **403 诊断清单**：遇到 403 时依次检查 User-Agent、Referer/Origin、自定义头（如 `x-requested-with`）、IP 封禁、Cookie/Token。
- **稳定性建议**：TouchGal API 无官方接入保障，建议监控后续失效情况，并考虑增加重试策略与降级机制。

## 审查中发现的重要模式
- **“救火式”编码**：仅针对当前报错的方法添加反爬头，未统一设计 —— 常见的技术债务来源。
- **增量审查盲区**：基于 diff 的审查易遗漏跨方法的代码不一致问题，需配合全量重审。
- **依赖脆弱性**：插件强依赖单一第三方 API，缺少降级或容错机制。
- **传递影响遗忘**：修复一个接口的 403 后，未主动检查同一域名的其他接口。

## 团队约定与规范
- 所有对外网络请求应通过统一私有方法（如 `_request()`）发出，集中管理 headers、session 和日志。
- 修复 403 后，必须验证同域名下至少 80% 的 API 方法（规则 `403-check-all-endpoints`）。
- User-Agent 中不出现具体版本号，保留 `Chrome/` 或使用 `python-requests` 默认 UA + 可配置补充头。
- 修改 API 相关代码后，需验证其他接口是否同样需要新增的请求头。
- 版本更新记录须维护在 README 的“更新日志”章节。
# astrbot_plugin_touchgal - SAKURA.md

## 项目简介
为 AstrBot 机器人提供的 Galgame 搜索与下载插件，通过接入 TouchGal API（https://www.touchgal.us/） 实现游戏信息查询及资源获取。

## 技术栈
- 核心框架：AstrBot 插件系统（Python）
- 外部接口：TouchGal API
- 依赖管理：requirements.txt
- 配置管理：JSON Schema + 控制面板配置

## 项目结构
- main.py：插件主逻辑，实现命令 /查询gal、/下载gal
- metadata.yaml：插件元数据（名称、版本、作者）
- _conf_schema.json：配置项定义（如 NSFW 开关）
- README.md：安装、配置、使用方法及版本记录
- requirements.txt：依赖列表
- LICENSE：许可证

## 架构设计与关键决策
- **命令格式**：以斜杠 / 开头，遵循 AstrBot 标准
- **配置入口**：统一通过 AstrBot 控制面板管理
- **展示名机制**：V1.4 支持插件展示名与目录名不同
- **HTTP 请求（重要）**：第三方 API 存在反爬校验，需添加浏览器请求头（User-Agent、Referer 等）方可正常访问

## 已知问题与注意事项
- **反爬时效性**：硬编码的 User-Agent（如 Chrome 120）会过时，后续可能导致 403 错误，需定期更新或引入动态 UA
- **代码不一致**：`search_game` 现已添加请求头，但 `get_downloads` 和 `download_and_convert_image` 方法尚未同步，存在潜在失效风险
- **缺少基础设施**：项目未封装统一 HTTP 请求方法，headers、超时、重试逻辑分散在各方法中，易产生技术债务
- **错误处理局限**：API 返回 403 时现有异常分支可能无法妥善处理（原按超时/网络异常设计）
- **稳定性建议**：TouchGal API 无官方接入保障，建议监控后续失效情况，并考虑增加重试策略

## 审查发现的重要模式
- **"救火式"编码**：仅针对当前报错的方法添加反爬头，未统一设计 —— 常见的技术债务来源
- **增量审查盲区**：基于 diff 的审查易遗漏跨方法的代码不一致问题，需配合全量重审
- **依赖脆弱性**：插件强依赖单一第三方 API，缺少降级或容错机制

## 团队约定与规范
- 所有对外网络请求应通过统一私有方法（如 `_request()`）发出，集中管理 headers、session 和日志
- 对于特定站点的反爬修复，评审时需主动检查是否存在"相同目标域名的其他请求"
- 版本更新记录须维护在 README 的"更新日志"章节
- 修改 API 相关代码后，需验证其他接口是否同样需要新增的请求头

## 累计反思次数
3
1. **项目简介**  
为 AstrBot 机器人提供的插件，通过接入 touchgal API 实现 Galgame 游戏的搜索与下载功能。

2. **技术栈**  
- 核心框架：AstrBot 插件系统（Python）  
- 外部接口：TouchGal API（https://www.touchgal.us/）  
- 依赖管理：requirements.txt  
- 配置管理：JSON Schema（_conf_schema.json）及控制面板配置  

3. **项目结构**  
- LICENSE：开源许可证文件  
- README.md：项目说明、安装/配置/使用方法及版本记录  
- _conf_schema.json：插件配置项定义（如 NSFW 开关）  
- main.py：插件主逻辑，实现命令 /查询gal 和 /下载gal  
- metadata.yaml：AstrBot 插件元数据（名称、版本、作者等）  
- requirements.txt：Python 依赖列表  

4. **开发约定**  
- 配置统一通过 AstrBot 控制面板管理（插件配置项）  
- 命令以斜杠 / 开头，遵循 AstrBot 标准命令格式  
- 错误处理包含对 API 返回字段（如 size）缺失的异常捕获  
- 版本更新记录需维护在 README 的“更新日志”章节  
- 展示名与插件目录名可不同（V1.4 引入插件展示名）
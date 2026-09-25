# Changelog

本项目的更新记录，日期格式 `YYYY-MM-DD`。

## [1.0.0] - 2026-09-25

首个公开版本。

### Added

- 收录 4 个社媒发布技能，均基于 `social-auto-upload` 的 `sau` CLI：
  - `douyin-upload` — 抖音登录 / cookie 校验 / 视频与图文发布
  - `kuaishou-upload` — 快手登录 / cookie 校验 / 视频与图文发布
  - `xiaohongshu-upload` — 小红书登录 / cookie 校验 / 视频与图文发布
  - `bilibili-upload` — B 站登录 / 账号校验 / 视频上传（自动准备 `biliup`）
- 每个技能包含 `SKILL.md`、`references/`（运行前提、CLI 契约、故障排查）、`scripts/examples/`（ps1 / sh / py 命令模板）、`locales/`
- `README.md`（安装、使用、安全说明）与 `.gitignore`

### Security

- 公开发布前对全部 36 个文件做了两轮敏感信息正则扫描（token / 密码 / cookie 值 / API key / 邮箱 / 手机号 / 盘符路径 / IP / 平台凭据字段），结果 0 命中，并人工抽读关键文档复核
- `.gitignore` 排除 cookie、二维码、虚拟环境、本地配置等敏感产物

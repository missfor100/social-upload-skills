# 上游依赖锁定（UPSTREAM）

本仓库的 4 个技能只是 `social-auto-upload` 的 `sau` CLI 的**文档与工作流封装**，不含业务代码。
技能文档里写的每一条命令契约，都对应上游某一固定状态；因此上游版本必须显式锁定、显式比对。

## 当前锁定（pin）

| 项 | 值 |
| --- | --- |
| 上游仓库 | <https://github.com/dreammis/social-auto-upload> |
| 锁定 commit | `0012d2c355f88f683cc38dde2a2db209e14091bc` |
| 提交日期 | 2026-09-03（`main`） |
| 提交说明 | Merge PR #278（kuaishou upload modal recovery） |
| 上游版本号 | `0.1.0`（`pyproject.toml`） |
| Python 要求 | `>=3.10,<3.13` |
| 关键依赖 | `patchright==1.58.2`、`qrcode==8.2`、`loguru==0.7.3` |
| 契约核验日期 | 2026-09-26（本机执行 `sau --help` 与各平台 `--help`，与 `references/cli-contract.md` 逐条比对一致） |

## 如何在自己机器上记录 pin

在上游仓库的本地 checkout 根目录执行：

```bash
git rev-parse HEAD        # 记录到本文件的表格里
git status -sb            # 确认工作区干净、未处于游离 HEAD
```

安装方式（推荐 editable 安装，便于对照源码排错）：

```bash
uv pip install -e .
```

## 升级上游的固定流程

1. 先记下旧 pin（本文件表格）与本地技能当前状态。
2. 拉取上游：`git fetch origin && git log --oneline <旧pin>..origin/main`，重点看
   `sau_cli.py`、`uploader/` 下改动——这两处直接改 CLI 契约与发布行为。
3. 升级安装后，执行 `sau --help` 与每个平台的 `sau <platform> --help`，
   与本仓库对应的 `references/cli-contract.md` **逐条比对**（子命令增删、参数改名、必填项变化）。
4. 比对不一致 → 先改技能文档，再验证真实发布流程；一致 → 更新本文件的 pin 与核验日期。
5. 在 `CHANGELOG.md` 记一笔（`Changed` / `Security` 视情况），提交并打 tag。

> 不要静默升级：上游是活跃仓库，CLI 契约与浏览器自动化实现都可能在任意提交里变更。

## 平台覆盖情况

上游 `sau` 顶层当前暴露 10 个平台子命令，本仓库收录 4 个：

| 平台 | 子命令 | 本仓库技能 | 状态 |
| --- | --- | --- | --- |
| 抖音 | `sau douyin` | `douyin-upload` | 已收录（login / check / upload-video / upload-note） |
| 快手 | `sau kuaishou` | `kuaishou-upload` | 已收录（同上） |
| 小红书 | `sau xiaohongshu` | `xiaohongshu-upload` | 已收录（同上） |
| 哔哩哔哩 | `sau bilibili` | `bilibili-upload` | 已收录（login / check / upload-video，无图文） |
| 腾讯视频号 | `sau tencent` | — | 未收录 |
| 支付宝生活号 | `sau alipay` | — | 未收录 |
| 微博 | `sau weibo` | — | 未收录 |
| 虎扑 | `sau hupu` | — | 未收录 |
| YouTube | `sau youtube` | — | 未收录 |
| 百家号 | `sau baijiahao` | — | 未收录 |

扩充新平台时的最小流程：

1. `sau <platform> --help` 及其每个子命令的 `--help`，把真实契约抄下来（不要凭想象写参数）。
2. 复制现有技能目录为模板，替换平台名，逐条改写 `SKILL.md` / `references/` / `scripts/examples/` / `locales/`。
3. 真机跑通 `login` → `check` → 发布一遍，再提交。
4. 更新本表与 README 的技能清单。

## 二维码口径

各技能对登录二维码的处置口径以 `README.md` 的「安全说明」为准：
**二维码本身即登录态凭证**，默认让用户在本机直接打开图片路径扫码；
只有在用户明确信任聊天通道时，才把图片经会话发送。

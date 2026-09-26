# 抖音 CLI 契约

这个 skill 默认假设当前环境已经安装并可调用 `sau` 命令。

## 命令列表

### 登录

```bash
sau douyin login --account <account>
```

- 必填参数:
  - `--account`
- 作用:
  - 启动抖音登录流程，为指定账号生成或刷新 cookie 文件
  - 如果登录过程中生成本地二维码图片，把该图片的本地路径交给用户，让用户在本机打开扫码
  - 二维码即登录态凭证，仅在用户明确信任聊天通道时，才把图片经会话发送（会途经模型服务商）
- 账号说明:
  - `--account` 传的是用户自定义的 `account_name`，不是固定只能叫 `creator`
  - 一个 `account_name` 对应一个账号文件，可用于多账号隔离和并发任务

### 校验 cookie

```bash
sau douyin check --account <account>
```

- 必填参数:
  - `--account`
- 预期输出:
  - `valid`：cookie 可用
  - `invalid`：cookie 缺失或已失效

### 上传视频

```bash
sau douyin upload-video \
  --account <account> \
  --file <video-path> \
  --title "<title>" \
  [--desc "<description>"] \
  [--tags tag1,tag2] \
  [--schedule "YYYY-MM-DD HH:MM"] \
  [--thumbnail <image-path>] \
  [--thumbnail-landscape <image-path>] \
  [--thumbnail-portrait <image-path>] \
  [--product-link <url>] \
  [--product-title "<title>"] \
  [--declaration "<exact-option-text>"] \
  [--collection "<collection-name>"] \
  [--debug] \
  [--headless | --headed]
```

- 必填参数:
  - `--account`
  - `--file`
  - `--title`
- 可选参数:
  - `--desc`
  - `--tags`
  - `--schedule`
  - `--thumbnail`（3:4 竖版封面）
  - `--thumbnail-landscape`（4:3 横版封面）
  - `--thumbnail-portrait`（3:4 竖版封面）
  - `--product-link`
  - `--product-title`
  - `--declaration`（抖音内容声明，必须与平台选项文案完全一致；不传则不设置）
  - `--collection`（合集名，必须是账号内已存在的合集）
  - `--debug`
  - `--headless`
  - `--headed`

### 上传图文

```bash
sau douyin upload-note \
  --account <account> \
  --images <image-1> [image-2 ...] \
  --title "<title>" \
  [--note "<content>"] \
  [--notef <file-path>] \
  [--tags tag1,tag2] \
  [--bgm "<music-name>"] \
  [--schedule "YYYY-MM-DD HH:MM"] \
  [--debug] \
  [--headless | --headed]
```

- 必填参数:
  - `--account`
  - `--images`
  - `--title`
- 可选参数:
  - `--note`（正文直接写在命令行）
  - `--notef`（从 txt / md 文件读取正文，与 `--note` 二选一）
  - `--tags`
  - `--bgm`（要搜索并选用的背景音乐名）
  - `--schedule`
  - `--debug`
  - `--headless`
  - `--headed`

## 发布策略

- 如果不传 `--schedule`，CLI 使用立即发布
- 如果传了 `--schedule`，CLI 自动切换为定时发布
- 时间格式为:

```text
YYYY-MM-DD HH:MM
```

## 额外说明

- `upload-video` 每次命令只支持一个视频文件
- `upload-note` 每次命令支持多张图片
- 视频描述字段统一使用 `--desc`
- 图文正文统一使用 `--note`
- `upload-note` 当前不支持 GIF
- `upload-note` 当前最多支持 35 张图片（上游 `douyin_uploader` 硬限制，超出直接报错）
- 封面比例：`--thumbnail` / `--thumbnail-portrait` 为 3:4 竖版，`--thumbnail-landscape` 为 4:3 横版
- `--declaration` 必须逐字匹配抖音页面上的声明选项文案，写错会导致设置失败

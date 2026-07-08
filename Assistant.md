# Assistant — AI / 维护者说明（国内站 gogogotoken.com）

本文件供 **后续 AI 协作者与运维** 阅读；用户可见文档在根目录 `*.mdx`，勿把内部配置写进 MDX。

## 仓库与站点

| 项 | 值 |
| --- | --- |
| 本仓库 | `gogogotoken-doc-cn` — Mintlify，部署为 **gogogotoken.com** 文档 |
| 海外文档仓库 | `gogogotoken-doc` — **gogogotoken.ai** 文档（独立 repo，勿混用域名） |
| 后端源码 | `gotoken-api` — Go 网关，relay/adaptor 决定计费分档 |
| 站点配置 | `docs.json`；页面在仓库根目录 |

## 海外 vs 国内定价（红线）

- **两套独立价目表**：国内对齐 **火山 ARK CNY**；海外对齐 **BytePlus USD**。
- **禁止**用汇率互相推导；改国内价须对照火山官方，改海外价须对照 BytePlus 官方。
- 仅 `doubao-seedance-2-0` / `doubao-seedance-2-0-fast` / `doubao-seedance-2-0-mini` 走 **token 计费**；`*-260128` 等别名仍按秒。
- **Kling（TokenHub）** 与 Seedance **独立**：模型 `kling-v2-5-turbo` / `kling-v2-6` / `kling-v3`，按上游积分结算，**禁止**写进 Seedance 价目表。

### 国内 Kling（TokenHub）— 用户文档 `kling-models.mdx`

| 模型 | ModelPrice（预扣上限 ¥） | max credits |
| --- | --- | --- |
| `kling-v2-5-turbo` | 5 | 5 |
| `kling-v2-6` | 12 | 12 |
| `kling-v3` | 45 | 45 |

- 零售价：**¥1 / 积分**（`yuan_per_credit = ModelPrice / maxCredits`）。
- 结算：上游 `final_unit_deduction` 精确积分，**不**在网关按秒复算。
- 渠道：type **50**，`base_url` = `https://tokenhub.tencentmaas.com`；`model_mapping` 如 `kling-v2-5-turbo` → `kl-video-v2-5-turbo`。
- 代码 SSOT：`gotoken-api/relay/channel/task/kling/tokenhub.go`（`tokenHubMaxCreditsByModel`）。
- 用户文档勿写渠道 ID、上游 sk、ModelRatio 内部键名。

### 国内（火山）— 用户文档 `video-models.mdx`

官方价（CNY/M output tokens，2026-06）：

| 模型 | 480/720 文生 | 480/720 媒体 | 1080 文生 | 1080 媒体 | fast 文生 | fast 媒体 | mini 文生 | mini 媒体 |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| 档位 | ¥46 | ¥28 | ¥51 | ¥31 | ¥37 | ¥22 | ¥23 | ¥14 |

mini **不支持 1080p**。

### 后台 ModelRatio（国内，baseline 档）

框架约定：`ModelRatio = 官方 baseline CNY/M ÷ 2`。

| 模型 | ModelRatio | 对应零售约 |
| --- | --- | --- |
| `doubao-seedance-2-0` | **25.5** | 1080p 文生 ≈ ¥51/M |
| `doubao-seedance-2-0-fast` | **18.5** | 文生 ≈ ¥37/M |
| `doubao-seedance-2-0-mini` | **11.5** | 文生 ≈ ¥23/M |

分辨率 / 媒体输入由代码 `variant_ratio` 自动乘算（`volcTokenBilledVariantRatio`），**不要**为每个分档单独写 ModelRatio。

代码 SSOT：`gotoken-api/relay/channel/task/doubao/constants.go`（`TokenBilledModelRatioBaseline(..., false)`）。

媒体输入判定：`hasMediaInputForBilling` — `images[]`、`metadata.content` 内 `image_url`/`video_url`/`audio_url`、**含 `asset://`**。

### 2026-06-27 状态说明

- **国内站**：本次 **未** 改 ModelRatio、**未** 部署海外 Seedance 2.0 价目对齐的那次 binary 变更；现有 CNY 价目与代码中 `volc*` 常量一致，**是对的**。
- **海外站**：新加坡已单独更新 ModelRatio（3.85 / 2.80）并部署；见 `gogogotoken-doc/Assistant.md`。

## 国内 Seedance 运维要点（内部）

| 项 | 说明 |
| --- | --- |
| 上游 | 火山 ARK 北京，`base_url` 含 `volces.com` / 非 `bytepluses.com` 时走国内价目 |
| 素材 API | `POST /v1/seedance/asset/*`；虚拟 AIGC 素材 vs 真人 KYC（H5）是不同流程 |
| 部署 | 见 `gotoken-api/docs/deployment-china.md` — IP **101.132.181.148** |

## 更新文档时的检查清单

1. 改价：先改 `gotoken-api` `volc*` 常量 + 单测，再更新国内 DB `options.ModelRatio` baseline 两项，再改 `video-models.mdx`。
2. **不要**把海外 USD 价写进国内 MDX，也不要用汇率换算填国内表。
3. 用户文档禁止出现：ModelRatio 数值、AK/SK、内部项目名、渠道 ID。

## 文档页面索引

| 路径 | 用途 |
| --- | --- |
| `video-models.mdx` | Seedance 模型 + CNY 价目表 |
| `seedance-token.mdx` | Token 计费 API |
| `video-generations.mdx` | 提交任务 |
| `video-tasks.mdx` | 轮询查询 |
| `assets.mdx` | 素材管理（CreateAsset ¥0.10/次） |
| `images.mdx` | Seedream 按张计费 |
| `chat-models.mdx` | Seed 对话 + 2.0-pro 分档 |
| `billing.mdx` | 全产品线计费汇总 |
| `video-tools.mdx` | 控制台工具说明 |
| `kling-models.mdx` | Kling 模型 + TokenHub 积分价目 |
| `kling-compatible.mdx` | Kling 文生/图生视频 API |
| `kling-tasks.mdx` | Kling 任务轮询 |

## 变更记录

| 日期 | 说明 |
| --- | --- |
| 2026-07-08 | 新增 Kling TokenHub 文档三页；修正 kling-tasks 查询响应为 TaskDto（大写 status）；aspect_ratio 默认 1:1；补充分组倍率/失败全额退款说明 |
| 2026-07-07 | 新增 mini / Seedream CNY 短模型名 / CreateAsset 计费 / chat-models 豆包对话页；修复 auth.mdx frontmatter |
| 2026-06-27 | 细化 video-models 分档表与「媒体输入」定义；补充 Assistant 与国内/海外价目分离说明 |

# BytePlus MediaKit CLI

[![License](https://img.shields.io/badge/License-Apache%202.0-blue.svg)](https://opensource.org/licenses/Apache-2.0)
[![Go Version](https://img.shields.io/badge/go-%3E%3D1.22-blue.svg)](https://go.dev/)
[![npm version](https://img.shields.io/npm/v/@byteplus/mediakit-cli.svg)](https://www.npmjs.com/package/@byteplus/mediakit-cli)

[English](./README.md) | [中文版](./README.zh.md)

BytePlus MediaKit 官方 CLI。对外命令为 `byteplus-mediakit-cli`。这是纯云端命令面，既能完成裁剪 / 拼接 / 加字幕 / 混音 / 提取音频等剪辑操作，也能调用画质增强、字幕擦除、高光智剪、场景切分等 AI 能力。目前已覆盖视频、剪辑、音频等多模态原子能力和 4 个 AI Agent [Skills](./skills/)。

[安装](#安装与快速开始) · [AI Agent Skills](#agent-skills) · [鉴权](#鉴权) · [命令结构](#命令结构) · [进阶用法](#进阶用法) · [许可证](#许可证)

## 为什么选 byteplus-mediakit-cli？

- **完备的能力矩阵**：横跨视频、剪辑、音频三大模态，从裁剪 / 拼接 / 加字幕等底层处理，到画质增强、字幕擦除、高光智剪、场景切分等上层理解，一条命令覆盖预处理到成片输出的全链路。
- **纯云端，同一命令面**：同一条 `<domain> <tool>` 命令走云端弹性算力，覆盖剪辑与 AI 原子能力。云端结果以 URL 返回；异步工具返回 `task_id` 供轮询。
- **面向 Agent 的 Skills**：随包装载 4 个 AI Agent Skills，Agent 可完成安装、鉴权、选工具、轮询异步任务，无需第二套命令面。
- **高性价比处理**：依托云端弹性资源调度，为大批量媒体任务提供有竞争力的价格，降低 AI 应用的整体运行成本。

## 功能

| 领域                | 能力                                                                                                                                                                                                                               | 运行 |
| ------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ---- |
| 🎬 **剪辑** (22)    | 视频加图片 · 视频加字幕 · 音频调速 · 视频调速 · 调整视频音量 · 视频添加滤镜 · 音频拼接 · 视频拼接 · 视频画面裁剪 · 视频截取动图 · 提取音频 · 音频声音淡入淡出 · 视频声音淡入淡出 · 视频画面翻转 · 视频高斯模糊 · 图片转视频 · 音频混合 · 视频加音频 · 视频画面旋转 · 视频画面拼接 · 音频裁剪 · 视频裁剪 | 云端 |
| 🎚️ **音频** (2)    | 人声背景音分离 · 音频转码                                                                                                                                                                                                          | 云端 |
| 🎥 **智能视频** (11)| 画质增强 · 视频画质增强极速版 · 精细化字幕擦除 · 视频抽帧 · 视频人脸打码 · 极智超清 · 视频转封装 · 场景切分 · 视频转码 · 视频插帧 · 高光智剪                                                                                         | 云端 |
| 🔧 **通用** (1)     | 异步任务查询                                                                                                                                                                                                                       | 云端 |

## 安装与快速开始

### 环境要求

开始之前，请确保具备以下条件：

- Node.js `>=18`（`npm` / `npx`）

### 快速开始（人类用户）

#### 安装

以下两种方式**任选其一**：

**方式一 — 一键安装：**

```bash
npx --registry=https://registry.npmjs.org/ @byteplus/mediakit-cli install -y
```

**方式二 — npm 全局安装：**

```bash
npm install -g @byteplus/mediakit-cli --registry=https://registry.npmjs.org/
```

本仓库仅开源 `skills/` 中的 Skills。CLI 通过 npm 包和 GitHub Release 预编译二进制分发。

#### 配置与使用

```bash
# 1. 设置云端 API Key
export MEDIAKIT_BYTEPLUS_API_KEY=<your-api-key>

# 2. 初始化配置
byteplus-mediakit-cli init

# 3. 环境自检（检查云端连通性、安装建议）
byteplus-mediakit-cli doctor

# 4. 云端 AI（异步）：将视频画质增强至 1080p，然后轮询获取最终结果
byteplus-mediakit-cli video enhance-video --video-url <url> --resolution 1080p
byteplus-mediakit-cli shared query-task --task-id <task_id> --poll-complete
```

### 快速开始（AI Agent）

> 以下步骤面向 AI Agent，全流程支持无人值守。

**第 1 步 — 安装**

```bash
npx --registry=https://registry.npmjs.org/ @byteplus/mediakit-cli install -y
```

**第 2 步 — 非交互式初始化**

```bash
# API Key 获取地址：https://console.byteplus.com/vodpaas/region:vodpaas+ap-southeast-1/ai-mediakit/settings
export MEDIAKIT_BYTEPLUS_API_KEY=<your-api-key>
byteplus-mediakit-cli init
```

**第 3 步 — 验证**

```bash
byteplus-mediakit-cli doctor
byteplus-mediakit-cli version
```

仅重装 Skills：

```bash
npx @byteplus/mediakit-cli install --skills-only -y
```

## Agent Skills

| Skill                       | 说明                                                                                                                                                                                                                              |
| --------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `byted-bp-mediakit-shared`  | 通用能力：云端鉴权与查询任务，其他 Skill 均需依赖                                                                                                                                                                                 |
| `byted-bp-mediakit-editing` | 剪辑：视频加图片、视频加字幕、音频调速、视频调速、调整视频音量、视频添加滤镜、音频拼接、视频拼接、视频画面裁剪、视频截取动图、提取音频、音频声音淡入淡出、视频声音淡入淡出、视频画面翻转、视频高斯模糊、图片转视频、音频混合、视频加音频、视频画面旋转、视频画面拼接、音频裁剪、视频裁剪 |
| `byted-bp-mediakit-audio`   | 音频：人声背景音分离、音频转码                                                                                                                                                                                                    |
| `byted-bp-mediakit-video`   | 智能视频：画质增强、视频画质增强极速版、精细化字幕擦除、视频抽帧、视频人脸打码、极智超清、视频转封装、场景切分、视频转码、视频插帧、高光智剪                                                                                     |

通过 Skill 执行真实云端业务命令或 `shared query-task` 时，必须在同一条命令行注入：

- `MEDIAKIT_BYTEPLUS_SURFACE=skill`
- `MEDIAKIT_BYTEPLUS_RUNTIME=<runtime>`

`<runtime>` 为当前 Agent 宿主（例如 `cursor`、`claude-code`）；无法可靠判断时使用 `unknown`。不要把它们当成 CLI flag 或业务参数。只读命令如 `--help`、`--schema`、`--domains`、`--version` 不需要注入。

```bash
MEDIAKIT_BYTEPLUS_API_KEY=<your-api-key> \
MEDIAKIT_BYTEPLUS_SURFACE=skill \
MEDIAKIT_BYTEPLUS_RUNTIME=<runtime> \
  byteplus-mediakit-cli video enhance-video --video-url <url>
```

## 鉴权

`byteplus-mediakit-cli` 采用极简鉴权：只需一个 API Key，无需 OAuth / STS / IAM 角色配置。鉴权值只从运行时环境变量读取。

```bash
export MEDIAKIT_BYTEPLUS_API_KEY=<your-api-key>
byteplus-mediakit-cli init
byteplus-mediakit-cli config show
```

| 环境变量                     | 说明                                                                                                                           |
| ---------------------------- | ------------------------------------------------------------------------------------------------------------------------------ |
| `MEDIAKIT_BYTEPLUS_API_KEY`  | 云端 API Key（[控制台获取](https://console.byteplus.com/vodpaas/region:vodpaas+ap-southeast-1/ai-mediakit/settings)）          |
| `MEDIAKIT_BYTEPLUS_ENDPOINT` | 可选的云端 API Endpoint 覆盖。默认：`https://mediakit.ap-southeast-1.bytepluses.com`                                           |
| `MEDIAKIT_BYTEPLUS_SURFACE`  | Skill 调用来源；Skill 驱动的调用使用 `skill`                                                                                   |
| `MEDIAKIT_BYTEPLUS_RUNTIME`  | 当前 Agent 宿主（例如 `cursor`、`claude-code`）；无法可靠判断时使用 `unknown`                                                  |

## 命令结构

```
byteplus-mediakit-cli <domain> <tool> [flags]
```

- **纯云端命令面**：全部业务工具走云端弹性算力，异步返回 `task_id`。没有 Local 模式，也没有 `--local` / `--cloud` 切换。
- **输出**：云端结果以 URL 返回。

系统命令：

| 命令                                                  | 说明                                                        |
| ----------------------------------------------------- | ----------------------------------------------------------- |
| `byteplus-mediakit-cli init`                          | 初始化云端配置（endpoint / runtime 标签）                   |
| `byteplus-mediakit-cli doctor`                        | 检查云端连通性与安装建议                                    |
| `byteplus-mediakit-cli config`                        | 查看 / 修改非密钥云端配置                                   |
| `byteplus-mediakit-cli version [--check]`             | 显示版本；`--check` 对比 npm 最新版                         |
| `byteplus-mediakit-cli update [--check]`              | 通过 `npm install -g` 更新 CLI 和 Skills；`--check` 只检查不安装 |
| `byteplus-mediakit-cli --domains`                     | 列出所有域                                                  |
| `byteplus-mediakit-cli --help-full`                   | 列出全部能力索引                                            |
| `byteplus-mediakit-cli <domain> <tool> --schema`      | 输出该能力的 JSON Schema（Mode / Async / 轮询命令等元信息） |
| `byteplus-mediakit-cli shared query-task --task-id X` | 查询异步任务；加 `--poll-complete` 轮询至终态               |

## 进阶用法

### Schema 自省

每个能力命令都支持 `--schema`，输出输入 / 输出 schema、Mode 与 Async 信息，供 Agent 动态发现工具能力：

```bash
byteplus-mediakit-cli video enhance-video --schema
byteplus-mediakit-cli editing trim-video --schema
```

## 许可证

`skills/` 中的 Skills 基于 **Apache License 2.0** 开源，详见各 Skill 的 LICENSE。CLI 的使用受根目录 LICENSE 所列条款约束。

该软件运行时会调用 BytePlus MediaKit 云端 API，使用这些 API 需要遵守如下协议：

- [BytePlus VOD 服务等级协议](https://docs.byteplus.com/en/docs/byteplus-vod/docs-byteplus-vod-service-level-agreement)
- [AI MediaKit 计费](https://docs.byteplus.com/en/docs/byteplus-vod/ai-mediakit-pricing)

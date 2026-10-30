# BytePlus MediaKit CLI

[![License](https://img.shields.io/badge/License-Apache%202.0-blue.svg)](https://opensource.org/licenses/Apache-2.0)
[![Go Version](https://img.shields.io/badge/go-%3E%3D1.22-blue.svg)](https://go.dev/)
[![npm version](https://img.shields.io/npm/v/@byteplus/mediakit-cli.svg)](https://www.npmjs.com/package/@byteplus/mediakit-cli)

[English](./README.md) | [中文版](./README.zh.md)

The official BytePlus MediaKit CLI. The public command is `byteplus-mediakit-cli`. This Cloud-only command surface covers editing operations such as trimming, concatenation, subtitling, mixing, and audio extraction, plus AI capabilities such as quality enhancement, subtitle erasure, highlight clipping, and scene segmentation. It already covers atomic capabilities across video, editing, and audio modalities plus 4 AI Agent [Skills](./skills/).

[Installation](#installation--quick-start) · [AI Agent Skills](#agent-skills) · [Authentication](#authentication) · [Command Structure](#command-structure) · [Advanced Usage](#advanced-usage) · [License](#license)

## Why choose byteplus-mediakit-cli?

- **Comprehensive capability matrix**: spans video, editing, and audio modalities, from low-level processing such as trimming / concatenation / subtitling to high-level understanding such as quality enhancement, subtitle erasure, highlight clipping, and scene segmentation — a single command covers the full pipeline from preprocessing to final output.
- **Cloud AI, one command surface**: the same `<domain> <tool>` command invokes Cloud elastic compute for editing and AI atomic capabilities. Cloud results are returned as URLs; async tools return a `task_id` for polling.
- **Agent-native Skills**: ships 4 AI Agent Skills so an Agent can install, authenticate, choose a tool, and poll async tasks without a second command surface.
- **Cost-effective processing**: leverages Cloud elastic resource scheduling to provide competitive pricing for large batches of media tasks, reducing overall operational cost for AI applications.

## Features

| Domain              | Capabilities                                                                                                                                                                                                                                                                                                                                                                  | Runtime |
| ------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------- |
| 🎬 **Editing** (22) | Video image overlay · Video subtitle overlay · Audio speed · Video speed · Video volume adjustment · Video filter · Audio concatenation · Video concatenation · Video crop · Animated image extraction · Audio extraction · Audio fade · Video audio fade · Video flip · Gaussian blur · Image to video · Audio mixing · Audio/video muxing · Video rotation · Video stitching · Audio trim · Video trim | Cloud   |
| 🎚️ **Audio** (2)    | Voice/background separation · Audio transcoding                                                                                                                                                                                                                                                                                                                               | Cloud   |
| 🎥 **Video AI** (11)| Quality enhancement · Fast enhancement · Fine-grained subtitle erasure · Frame extraction · Face blur · Low-bitrate HD transcoding · Remux · Scene segmentation · Transcoding · Frame interpolation · Highlight clipping                                                                                                                                                      | Cloud   |
| 🔧 **Common** (1)   | Async task query                                                                                                                                                                                                                                                                                                                                                              | Cloud   |

## Installation & Quick Start

### Requirements

Before you begin, make sure you have:

- Node.js `>=18` (`npm` / `npx`)

### Quick Start (Human Users)

#### Installation

Choose **one** of the following methods:

**Option 1 — one-click install:**

```bash
npx --registry=https://registry.npmjs.org/ @byteplus/mediakit-cli install -y
```

**Option 2 — global npm install:**

```bash
npm install -g @byteplus/mediakit-cli --registry=https://registry.npmjs.org/
```

This repository publishes the Skills in `skills/`. The CLI is distributed through npm and prebuilt GitHub Release binaries.

#### Configuration & Usage

```bash
# 1. Set the Cloud API Key
export MEDIAKIT_BYTEPLUS_API_KEY=<your-api-key>

# 2. Initialize configuration
byteplus-mediakit-cli init

# 3. Environment self-check (Cloud connectivity and install suggestions)
byteplus-mediakit-cli doctor

# 4. Cloud AI (async): enhance a video to 1080p, then poll for the final result
byteplus-mediakit-cli video enhance-video --video-url <url> --resolution 1080p
byteplus-mediakit-cli shared query-task --task-id <task_id> --poll-complete
```

### Quick Start (AI Agent)

> The following steps are designed for AI Agents and support fully unattended workflows.

**Step 1 — Install**

```bash
npx --registry=https://registry.npmjs.org/ @byteplus/mediakit-cli install -y
```

**Step 2 — Non-interactive initialization**

```bash
# Get an API Key at: https://console.byteplus.com/vodpaas/region:vodpaas+ap-southeast-1/ai-mediakit/settings
export MEDIAKIT_BYTEPLUS_API_KEY=<your-api-key>
byteplus-mediakit-cli init
```

**Step 3 — Verify**

```bash
byteplus-mediakit-cli doctor
byteplus-mediakit-cli version
```

Reinstall Skills only:

```bash
npx @byteplus/mediakit-cli install --skills-only -y
```

## Agent Skills

| Skill                         | Description                                                                                                                                                                                                                                                                                                                                 |
| ----------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `byted-bp-mediakit-shared`    | Common capabilities: Cloud auth and async task query — required by all other skills                                                                                                                                                                                                                                                         |
| `byted-bp-mediakit-editing`   | Editing: video image overlay, video subtitle overlay, audio speed, video speed, video volume adjust, video filter, audio concat, video concat, video crop, animated image extract, extract audio, audio fade in/out, video audio fade in/out, video flip, gaussian blur, image to video, audio mix, mux audio/video, video rotate, video stitch, audio trim, video trim |
| `byted-bp-mediakit-audio`     | Audio: voice / background separation, audio transcode                                                                                                                                                                                                                                                                                       |
| `byted-bp-mediakit-video`     | Video AI: quality enhancement, fast enhancement, fine-grained subtitle erasure, frame extract, face blur, low-bitrate HD transcoding, remux, scene segmentation, transcode, frame interpolation, highlight clipping                                                                                                                         |

When executing real Cloud business commands or `shared query-task` through a Skill, inject both values on the same command line:

- `MEDIAKIT_BYTEPLUS_SURFACE=skill`
- `MEDIAKIT_BYTEPLUS_RUNTIME=<runtime>`

`<runtime>` is the current Agent host (for example `cursor`, `claude-code`). Use `unknown` when the host cannot be determined reliably. Do not pass them as CLI flags or business parameters. Read-only commands such as `--help`, `--schema`, `--domains`, and `--version` do not require injection.

```bash
MEDIAKIT_BYTEPLUS_API_KEY=<your-api-key> \
MEDIAKIT_BYTEPLUS_SURFACE=skill \
MEDIAKIT_BYTEPLUS_RUNTIME=<runtime> \
  byteplus-mediakit-cli video enhance-video --video-url <url>
```

## Authentication

`byteplus-mediakit-cli` uses minimal authentication: just an API Key — no OAuth / STS / IAM role configuration required. Authentication values are accepted only from runtime environment variables.

```bash
export MEDIAKIT_BYTEPLUS_API_KEY=<your-api-key>
byteplus-mediakit-cli init
byteplus-mediakit-cli config show
```

| Environment variable           | Description                                                                                                                              |
| ------------------------------ | ---------------------------------------------------------------------------------------------------------------------------------------- |
| `MEDIAKIT_BYTEPLUS_API_KEY`    | Cloud API Key ([get it from the console](https://console.byteplus.com/vodpaas/region:vodpaas+ap-southeast-1/ai-mediakit/settings))       |
| `MEDIAKIT_BYTEPLUS_ENDPOINT`   | Optional Cloud API endpoint override. Default: `https://mediakit.ap-southeast-1.bytepluses.com`                                          |
| `MEDIAKIT_BYTEPLUS_SURFACE`    | Skill source; use `skill` for Skill-driven calls                                                                                         |
| `MEDIAKIT_BYTEPLUS_RUNTIME`    | Current Agent host (for example `cursor`, `claude-code`); use `unknown` when the host cannot be determined reliably                      |

## Command Structure

```
byteplus-mediakit-cli <domain> <tool> [flags]
```

- **Cloud-only command surface**: every business tool runs on Cloud elastic compute and asynchronously returns a `task_id`. There is no Local mode and no `--local` / `--cloud` switch.
- **Output**: Cloud results are returned as URLs.

System commands:

| Command                                               | Description                                                                              |
| ----------------------------------------------------- | ---------------------------------------------------------------------------------------- |
| `byteplus-mediakit-cli init`                          | Initialize Cloud configuration (endpoint / runtime label)                                |
| `byteplus-mediakit-cli doctor`                        | Check Cloud connectivity and install suggestions                                         |
| `byteplus-mediakit-cli config`                        | View / modify non-secret Cloud configuration                                             |
| `byteplus-mediakit-cli version [--check]`             | Show version; `--check` compares against the latest npm release                          |
| `byteplus-mediakit-cli update [--check]`              | Update the CLI and Skills via `npm install -g`; `--check` only checks without installing |
| `byteplus-mediakit-cli --domains`                     | List all domains                                                                         |
| `byteplus-mediakit-cli --help-full`                   | List the full capability index                                                           |
| `byteplus-mediakit-cli <domain> <tool> --schema`      | Output the JSON Schema for the capability (Mode / Async / polling command metadata)      |
| `byteplus-mediakit-cli shared query-task --task-id X` | Query an async task; add `--poll-complete` to poll until terminal state                  |

## Advanced Usage

### Schema Introspection

Every capability command supports `--schema`, which outputs the input / output schema plus Mode and Async information for Agents to discover tool capabilities dynamically:

```bash
byteplus-mediakit-cli video enhance-video --schema
byteplus-mediakit-cli editing trim-video --schema
```

## License

The Skills in `skills/` are published under **Apache License 2.0**; see each Skill’s LICENSE. CLI use is subject to the terms listed in the root LICENSE.

At runtime this software calls BytePlus MediaKit cloud APIs. Using those APIs is subject to the following agreements:

- [BytePlus VOD Service Level Agreement](https://docs.byteplus.com/en/docs/byteplus-vod/docs-byteplus-vod-service-level-agreement)
- [AI MediaKit Pricing](https://docs.byteplus.com/en/docs/byteplus-vod/ai-mediakit-pricing)

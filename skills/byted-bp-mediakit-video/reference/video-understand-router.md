# video-understand-router

## Capability

Analyze video frames and optionally audio to produce structured video understanding results, using automatic or manual analysis settings.

## Parameter rules

- Optional parameters may be filled only when the user specifies them, or when they can be determined accurately from user intent; do not invent values. Omit them when uncertain; ask the user if they are required to complete the task. Before submitting a business task, resolve required fields and required alternatives from the user's explicit requirements, authoritative prior results, or an unambiguous documented mapping. A media type, genre, or broad processing goal does not by itself supply missing user choices or selection criteria. If a required choice has no documented default and remains unresolved, ask a focused clarification, preserve the user's known constraints, and stop before business submission. Do not invent a custom criterion merely to make a required field present. For optional parameters, infer only values implementing a behavior the user actually requested or required by a declared parameter relation. Do not add extra processing, alignment, formatting, or tuning merely because it seems helpful.

## Cloud

### Command and lifecycle

- Command: `byteplus-mediakit-cli video video-understand-router`
- Lifecycle: async
- Return: returns `task_id`; query the terminal result next.

### Usage guide

- Object parameters (`--manual-option`) must be valid JSON. Prefer a UTF-8 file with `--manual-option @params.json`, or pass correctly escaped inline JSON; field names and nesting must match the “Enum/range” column and nested field descriptions in the Parameters table below.
- Do not pass comma-separated values or plain text for these parameters.
- Inline JSON needs shell escaping. A single-quoted shell argument cannot contain an unescaped apostrophe, such as in It's ready; use a JSON file for text containing apostrophes.
- Array parameters (`--prefer-endpoints`) must use comma-separated values wrapped in quotes, for example `--prefer-endpoints "value1,value2"`.
- A comma inside one array item must be quoted by CSV rules, including doubled embedded double quotes, or use a JSON array file with `--prefer-endpoints @params.json`. A single string URL may contain commas; this CSV rule applies only to array encoding.
- For one operation on an ordered collection, include all items in one `--prefer-endpoints` array argument in the user's order. Submit separate tasks only for independent operations requested by the user or a documented input limit.
- Array parameters (`--prefer-models`) must use comma-separated values wrapped in quotes, for example `--prefer-models "value1,value2"`.
- A comma inside one array item must be quoted by CSV rules, including doubled embedded double quotes, or use a JSON array file with `--prefer-models @params.json`. A single string URL may contain commas; this CSV rule applies only to array encoding.
- For one operation on an ordered collection, include all items in one `--prefer-models` array argument in the user's order. Submit separate tasks only for independent operations requested by the user or a documented input limit.
- Array parameters (`--video-urls`) must use comma-separated values wrapped in quotes, for example `--video-urls "url1,url2"`.
- A comma inside one array item must be quoted by CSV rules, including doubled embedded double quotes, or use a JSON array file with `--video-urls @params.json`. A single string URL may contain commas; this CSV rule applies only to array encoding.
- For one operation on an ordered collection, include all items in one `--video-urls` array argument in the user's order. Submit separate tasks only for independent operations requested by the user or a documented input limit.
- `{}` and `[{}]` illustrate JSON container shapes. Replace `<parameter_json>` in command templates with valid JSON matching the parameter table, including required fields. Empty objects or arrays are valid only when the Schema permits them, including a required object with no required child fields. Required means field presence; null, empty values, zero and false are checked against their own type and constraints. Child fields marked optional remain optional; do not infer required fields from illustrative prose. Do not submit placeholders.

### Example

```bash
byteplus-mediakit-cli video video-understand-router \
  --video-urls "url1,url2" \
  --prompt '<prompt>'
```

Replace placeholders with real user input only. Optional flags follow the parameter rules; do not invent URLs, files, enums, or business parameters.
Pass each scalar value as one shell argument. The quotes in these templates are illustrative: escape embedded quotes for the actual shell, or use an argument array when invoking a process. Never concatenate raw user input into shell code.

### Parameters

| Path | CLI flag | Type | Required | Default | Enum/range | Description |
| --- | --- | --- | --- | --- | --- | --- |
| `callback_args` | `--callback-args` | string | no | - | - | Custom callback argument returned unchanged when the task finishes, used to correlate business records; maximum 512 bytes. Pass only when the user provides this value; do not generate, infer, or fill it. |
| `callback_url` | `--callback-url` | string | no | - | - | Callback URL that receives this task result; when set it takes priority over the global callback URL and must start with http:// or https://. Pass only when the user provides this value; do not generate, infer, or fill it. |
| `client_token` | `--client-token` | string | no | - | - | Idempotency token; case-sensitive, at most 64 printable ASCII characters. Omit by default. Reuse the same token for retries of the same logical request. Do not mint a new token for each attempt. The CLI does not infer retry intent or auto-generate tokens. Omit by default on the first asynchronous submission. Use a user-provided value as-is when specified. A retry of the same uncertain submission must keep the original token policy: reuse its token if provided, or keep the field omitted if it was not provided. After a result exists, an explicitly requested new run must use a new token. Do not mint a different value for each retry attempt. Synchronous tasks do not support idempotency checks. The CLI runtime does not infer retry intent and does not auto-generate tokens. |
| `level` | `--level` | string | no | "Economy" | enum: ["Economy","Balanced","Quality"] | Analysis tier. Economy favors speed and bulk annotation; Balanced balances speed and quality for moderation or retrieval; Quality favors detailed understanding. Tiers select frame sampling and models to balance cost, speed and result quality. |
| `manual_option` | `--manual-option` | object | no | - | - | Manual overrides for tier-based analysis. Omission uses the level strategy in full. |
| `manual_option.fps` | - | number | no | 1.0 | min: 0.2; max: 5.0 | Uniform frame-sampling rate from 0.2 to 5 frames per second, default 1. |
| `manual_option.max_snapshot_number` | - | integer | no | 0 | max: 1000 | Maximum sampled frames from 0 to 1000, default zero. Zero uses the tier's frame count; a positive value overrides it. |
| `manual_option.need_audio` | - | boolean | no | false | - | Explicitly enable audio analysis when true, or disable it when false, the default. Automatic audio-keyword detection recognizes only Chinese prompt terms; English or other-language prompts mentioning speech, sound or music require this flag to analyze audio. |
| `prefer_endpoints` | `--prefer-endpoints` | array<string> | no | - | min items: 1; max items: 10 | Preferred ModelArk Endpoint IDs, up to ten. The selection strategy chooses among them. This preference takes priority over prefer_models. One preferred ModelArk Endpoint ID. |
| `prefer_models` | `--prefer-models` | array<string> | no | - | min items: 1; max items: 10 | Preferred ModelArk model IDs, up to ten, used together with the selection strategy. Available IDs are listed in the ModelArk console. One preferred ModelArk model ID. |
| `prompt` | `--prompt` | string | yes | - | min length: 1 | Natural-language instruction for video analysis, with at least one character. |
| `queue_id` | `--queue-id` | string | no | - | - | Target queue ID for the submitted task. Omit to use the system default queue. Pass only when the user provides this value; do not generate, infer, or fill it. |
| `video_urls` | `--video-urls` | array<string> | yes | - | min items: 1; max items: 10 | Up to ten videos to analyze, each no longer than two hours. Supports common video formats and public HTTP/HTTPS URLs. Each array item may be a local file path or public HTTP/HTTPS URL. The declared media input format also accepts vod:// and tos:// resource URIs. The CLI handles local file upload internally; pass the original input directly. One input video in the analysis sequence. CLI inputs may be local file paths or public HTTP/HTTPS URLs. The CLI handles local file upload internally; pass the original input directly. |

### Result

| Path | Type | Required | Mode | Description |
| --- | --- | --- | --- | --- |
| `request_id` | string | no | Cloud | Request id returned by the service; forwarded only when non-empty. Constraints: min length: 1. |
| `task_id` | string | yes | Cloud | Unique async task id used to query status and the terminal result. Constraints: min length: 1. |
| `task_type` | string | no | Cloud | Task type returned by the service; forwarded only when non-empty. Constraints: min length: 1. |
| `contents` | array<string> | no | Cloud terminal | Analysis results in the same order as the input video list. Analysis result for the corresponding input video. |
| `duration` | number | no | Cloud terminal | Sum of input video durations in seconds. |
| `token_usage` | object | no | Cloud terminal | Token use during model inference. |
| `token_usage.input_tokens` | integer | no | Cloud terminal | Number of input tokens. |
| `token_usage.output_tokens` | integer | no | Cloud terminal | Number of output tokens. |
| `token_usage.total_tokens` | integer | no | Cloud terminal | Total number of tokens. |

After a successful Cloud call, read the returned `task_id` and check its current status. This command queries once and returns; when the task is still running, query the same task again later until terminal. Do not resubmit the business task. Use `--poll-complete` only when continuous waiting is intended; it waits without a limit by default. Pass a polling timeout or attempt limit only when specified by the user. Interrupting the CLI stops waiting without canceling the Cloud task:

```bash
byteplus-mediakit-cli shared query-task --task-id '<task_id>'
```

After a synchronous business tool succeeds or an asynchronous task reaches completed, return its actual result and any output asset URLs to the user. Use the returned URLs as inputs to requested dependent steps. Download assets or perform additional local processing only when the user requests it, and retain the Cloud result URL alongside any downloaded file. Do not present task submission as completion. Do not replace a completed Cloud output with a locally recreated file from the original input, or infer permission for local recreation from a requested duration or quality target. If a returned asset's metadata or name conflicts with the requested constraints, probe that asset with an available metadata tool before claiming a mismatch or performing a correction. Corrective processing must consume the completed Cloud output, preserving its lineage; present the original Cloud result URL alongside any corrected output and explain any confirmed limitation. Never omit an available completed result URL in favor of an unfinished correction or a local fallback.

### Machine contract

These commands read live help/schema only and do not call Cloud:

```bash
byteplus-mediakit-cli video video-understand-router --help
byteplus-mediakit-cli video video-understand-router --schema
```

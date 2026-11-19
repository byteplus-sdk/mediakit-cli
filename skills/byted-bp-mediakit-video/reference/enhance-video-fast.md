# enhance-video-fast

## Capability

Uses a speed-first algorithm combining lightweight super-resolution and intelligent quality enhancement to balance efficiency and visual quality.

## Parameter rules

- Optional parameters may be filled only when the user specifies them, or when they can be determined accurately from user intent; do not invent values. Omit them when uncertain; ask the user if they are required to complete the task. Before submitting a business task, resolve required fields and required alternatives from the user's explicit requirements, authoritative prior results, or an unambiguous documented mapping. A media type, genre, or broad processing goal does not by itself supply missing user choices or selection criteria. If a required choice has no documented default and remains unresolved, ask a focused clarification, preserve the user's known constraints, and stop before business submission. Do not invent a custom criterion merely to make a required field present. For optional parameters, infer only values implementing a behavior the user actually requested or required by a declared parameter relation. Do not add extra processing, alignment, formatting, or tuning merely because it seems helpful.
- This capability is one of two documented enhancement paths: enhance-video (quality-first, including cinema-grade professional) and enhance-video-fast (speed-first). If the user only asks to enhance the video and does not choose quality/cinema-grade versus speed/low-latency, ask which path to use. Do not submit either tool until they choose. If you already asked and the user has not answered, stop; do not submit a guessed tool.

## Cloud

### Command and lifecycle

- Command: `byteplus-mediakit-cli video enhance-video-fast`
- Lifecycle: async
- Return: returns `task_id`; query the terminal result next.

### Parameters

| Path | CLI flag | Type | Required | Default | Enum/range | Description |
| --- | --- | --- | --- | --- | --- | --- |
| `bitrate` | `--bitrate` | integer | no | - | min: 10; max: 150000 | Target average bitrate from 10 to 150,000 kilobits per second. Overrides bitrate_level when supplied; omission derives the bitrate from level, resolution and frame rate. |
| `bitrate_level` | `--bitrate-level` | string | no | "medium" | enum: ["low","medium","high"] | This parameter is optional and controls the output video's average bitrate and affects visual quality and final file size. It supports low, medium, and high, and defaults to medium. The actual bitrate is automatically calculated using target resolution and frame rate. |
| `callback_args` | `--callback-args` | string | no | - | - | Custom callback argument returned unchanged when the task finishes, used to correlate business records; maximum 512 bytes. Pass only when the user provides this value; do not generate, infer, or fill it. |
| `callback_url` | `--callback-url` | string | no | - | - | Callback URL that receives this task result; when set it takes priority over the global callback URL and must start with http:// or https://. Pass only when the user provides this value; do not generate, infer, or fill it. |
| `client_token` | `--client-token` | string | no | - | - | Idempotency token; case-sensitive, at most 64 printable ASCII characters. Omit by default. Reuse the same token for retries of the same logical request. Do not mint a new token for each attempt. The CLI does not infer retry intent or auto-generate tokens. Omit by default on the first asynchronous submission. Use a user-provided value as-is when specified. A retry of the same uncertain submission must keep the original token policy: reuse its token if provided, or keep the field omitted if it was not provided. After a result exists, an explicitly requested new run must use a new token. Do not mint a different value for each retry attempt. Synchronous tasks do not support idempotency checks. The CLI runtime does not infer retry intent and does not auto-generate tokens. |
| `fps` | `--fps` | number | no | - | min: 15; max: 120 | This parameter is optional and sets the target frame rate, in fps. Its inclusive minimum is 15 and its inclusive maximum is 120. The target frame rate is recommended not to exceed 4x the source frame rate. If fps is omitted, the output retains the source video's frame rate. |
| `queue_id` | `--queue-id` | string | no | - | - | Target queue ID for the submitted task. Omit to use the system default queue. Pass only when the user provides this value; do not generate, infer, or fill it. |
| `resolution` | `--resolution` | string | no | - | enum: ["240p","360p","480p","540p","720p","1080p","2k","4k"] | This parameter is optional and sets the target resolution level, and it can upscale the video to a specified level. It supports 240p, 360p, 480p, 540p, 720p, 1080p, 2k, and 4k. This parameter and resolution_limit are mutually exclusive and cannot be configured together. |
| `resolution_limit` | `--resolution-limit` | integer | no | - | min: 128; max: 2160 | This parameter is optional and sets the target resolution's short-side pixel limit. Based on it, the system scales the video proportionally while preserving its original aspect ratio. It has an inclusive minimum of 128 and an inclusive maximum of 2160. This parameter and resolution are mutually exclusive and cannot be configured together. |
| `video_url` | `--video-url` | string | yes | - | min length: 1 | This required parameter is the URL of the video to be enhanced. Only publicly accessible HTTP or HTTPS video URLs are supported. Common supported video formats include mp4, flv, ts, avi, mov, wmv, and mkv. The input video's long side must be in the inclusive range 360 to 2560, and its short side must be in the inclusive range 360 to 1440. Input resolution up to 2K is supported. The input file size is recommended not to exceed 10 GB. CLI inputs may be local file paths or public HTTP/HTTPS URLs. The declared media input format also accepts vod:// and tos:// resource URIs. The CLI handles local file upload internally; pass the original input directly. |

### Example

```bash
byteplus-mediakit-cli video enhance-video-fast \
  --video-url '<video_url>'
```

Replace placeholders with real user input only. Optional flags follow the parameter rules; do not invent URLs, files, enums, or business parameters.
Pass each scalar value as one shell argument. The quotes in these templates are illustrative: escape embedded quotes for the actual shell, or use an argument array when invoking a process. Never concatenate raw user input into shell code.

### Result

| Path | Type | Required | Mode | Description |
| --- | --- | --- | --- | --- |
| `request_id` | string | no | Cloud | Request id returned by the service; forwarded only when non-empty. Constraints: min length: 1. |
| `task_id` | string | yes | Cloud | Unique async task id used to query status and the terminal result. Constraints: min length: 1. |
| `task_type` | string | no | Cloud | Task type returned by the service; forwarded only when non-empty. Constraints: min length: 1. |
| `duration` | number | no | Cloud terminal | The duration of the video. |
| `fps` | number | no | Cloud terminal | The frame rate of the video. |
| `resolution` | string | no | Cloud terminal | Supports 240p, 360p, 480p, 540p, 720p, 1080p, 2k, and 4k. |
| `video_url` | string | no | Cloud terminal | An HTTPS temporary download link for the result, valid for 24 hours by default. Please download the result within this validity period. |

After a successful Cloud call, read the returned `task_id` and check its current status. This command queries once and returns; when the task is still running, query the same task again later until terminal. Do not resubmit the business task. Use `--poll-complete` only when continuous waiting is intended; it waits without a limit by default. Pass a polling timeout or attempt limit only when specified by the user. Interrupting the CLI stops waiting without canceling the Cloud task:

```bash
byteplus-mediakit-cli shared query-task --task-id '<task_id>'
```

After a synchronous business tool succeeds or an asynchronous task reaches completed, return its actual result and any output asset URLs to the user. Use the returned URLs as inputs to requested dependent steps. Download assets or perform additional local processing only when the user requests it, and retain the Cloud result URL alongside any downloaded file. Do not present task submission as completion. Do not replace a completed Cloud output with a locally recreated file from the original input, or infer permission for local recreation from a requested duration or quality target. If a returned asset's metadata or name conflicts with the requested constraints, probe that asset with an available metadata tool before claiming a mismatch or performing a correction. Corrective processing must consume the completed Cloud output, preserving its lineage; present the original Cloud result URL alongside any corrected output and explain any confirmed limitation. Never omit an available completed result URL in favor of an unfinished correction or a local fallback.

### Machine contract

These commands read live help/schema only and do not call Cloud:

```bash
byteplus-mediakit-cli video enhance-video-fast --help
byteplus-mediakit-cli video enhance-video-fast --schema
```

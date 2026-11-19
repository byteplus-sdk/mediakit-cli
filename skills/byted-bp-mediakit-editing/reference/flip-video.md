# flip-video

## Capability

Mirrors a specified video vertically or horizontally.

## Parameter rules

- Optional parameters may be filled only when the user specifies them, or when they can be determined accurately from user intent; do not invent values. Omit them when uncertain; ask the user if they are required to complete the task. Before submitting a business task, resolve required fields and required alternatives from the user's explicit requirements, authoritative prior results, or an unambiguous documented mapping. A media type, genre, or broad processing goal does not by itself supply missing user choices or selection criteria. If a required choice has no documented default and remains unresolved, ask a focused clarification, preserve the user's known constraints, and stop before business submission. Do not invent a custom criterion merely to make a required field present. For optional parameters, infer only values implementing a behavior the user actually requested or required by a declared parameter relation. Do not add extra processing, alignment, formatting, or tuning merely because it seems helpful.

## Cloud

### Command and lifecycle

- Command: `byteplus-mediakit-cli editing flip-video`
- Lifecycle: async
- Return: returns `task_id`; query the terminal result next.

### Usage guide

- Boolean parameters (`--is-flip-horizontal`, `--is-flip-vertical`) must be written as `--flag=true` or `--flag=false`, or as bare `--flag` (equivalent to true). Do not pass a space-separated value such as `--flag true`, which the CLI treats as a positional argument.
- Pass a boolean explicitly when the user specifies true or false, including values equal to the default. Omit it when the user has not specified a value and no relation requires it.
- Requesting the main operation does not request every optional behavior. Do not enable an optional switch or fill its dependent defaults merely because input metadata differs or a changed output would be more convenient. Keep those fields omitted unless the user requested that separate behavior or a declared parameter relation requires their explicit presence.
- If the parameter relations require at least one boolean to be true, explicitly pass the intended true flag instead of omitting all boolean flags.

### Example

```bash
byteplus-mediakit-cli editing flip-video \
  --video-url '<video_url>' \
  --is-flip-vertical=true
```

Replace placeholders with real user input only. Optional flags follow the parameter rules; do not invent URLs, files, enums, or business parameters.
Pass each scalar value as one shell argument. The quotes in these templates are illustrative: escape embedded quotes for the actual shell, or use an argument array when invoking a process. Never concatenate raw user input into shell code.

### Parameters

| Path | CLI flag | Type | Required | Default | Enum/range | Description |
| --- | --- | --- | --- | --- | --- | --- |
| `callback_args` | `--callback-args` | string | no | - | - | Custom callback argument returned unchanged when the task finishes, used to correlate business records; maximum 512 bytes. Pass only when the user provides this value; do not generate, infer, or fill it. |
| `callback_url` | `--callback-url` | string | no | - | - | Callback URL that receives this task result; when set it takes priority over the global callback URL and must start with http:// or https://. Pass only when the user provides this value; do not generate, infer, or fill it. |
| `client_token` | `--client-token` | string | no | - | - | Idempotency token; case-sensitive, at most 64 printable ASCII characters. Omit by default. Reuse the same token for retries of the same logical request. Do not mint a new token for each attempt. The CLI does not infer retry intent or auto-generate tokens. Omit by default on the first asynchronous submission. Use a user-provided value as-is when specified. A retry of the same uncertain submission must keep the original token policy: reuse its token if provided, or keep the field omitted if it was not provided. After a result exists, an explicitly requested new run must use a new token. Do not mint a different value for each retry attempt. Synchronous tasks do not support idempotency checks. The CLI runtime does not infer retry intent and does not auto-generate tokens. |
| `is_flip_horizontal` | `--is-flip-horizontal` | boolean | no | false | - | Optional. Controls whether the video is flipped horizontally from left to right. Defaults to false. |
| `is_flip_vertical` | `--is-flip-vertical` | boolean | no | false | - | Optional. Controls whether the video is flipped vertically from top to bottom. Defaults to false. At least one of is_flip_vertical and is_flip_horizontal must be set to true. If neither is set to true, the processed video is identical to the source. When both are set to true, the effect is equivalent to rotating the video 180 degrees. |
| `queue_id` | `--queue-id` | string | no | - | - | Target queue ID for the submitted task. Omit to use the system default queue. Pass only when the user provides this value; do not generate, infer, or fill it. |
| `video_url` | `--video-url` | string | yes | - | min length: 1 | The URL of the video to be flipped. This parameter is required. Cloud requests use publicly accessible HTTP/HTTPS URLs. Supported inputs include common video formats such as mp4, flv, ts, avi, mov, wmv, and mkv. It is recommended that the input file size not exceed 10 GB. Input resolution up to 4K (3840×2160) is supported. CLI inputs may be local file paths or public HTTP/HTTPS URLs. The declared media input format also accepts vod:// and tos:// resource URIs. The CLI handles local file upload internally; pass the original input directly. |

### Parameter relations

- At least one of (is_flip_vertical=true) or (is_flip_horizontal=true) must hold.

### Result

| Path | Type | Required | Mode | Description |
| --- | --- | --- | --- | --- |
| `request_id` | string | no | Cloud | Request id returned by the service; forwarded only when non-empty. Constraints: min length: 1. |
| `task_id` | string | yes | Cloud | Unique async task id used to query status and the terminal result. Constraints: min length: 1. |
| `task_type` | string | no | Cloud | Task type returned by the service; forwarded only when non-empty. Constraints: min length: 1. |
| `duration` | number | no | Cloud terminal | The total duration of the output video, measured in seconds. |
| `resolution` | string | no | Cloud terminal | The resolution of the output video, which can be 240p, 360p, 480p, 540p, 720p, 1080p, 2k, or 4k. Constraints: enum: ["240p","360p","480p","540p","720p","1080p","2k","4k"]. |
| `video_url` | string | no | Cloud terminal | The HTTPS temporary download link for the generated flipped video file, which is in MP4 format. The link is valid for 24 hours by default, and the file should be downloaded and saved promptly. |

After a successful Cloud call, read the returned `task_id` and check its current status. This command queries once and returns; when the task is still running, query the same task again later until terminal. Do not resubmit the business task. Use `--poll-complete` only when continuous waiting is intended; it waits without a limit by default. Pass a polling timeout or attempt limit only when specified by the user. Interrupting the CLI stops waiting without canceling the Cloud task:

```bash
byteplus-mediakit-cli shared query-task --task-id '<task_id>'
```

After a synchronous business tool succeeds or an asynchronous task reaches completed, return its actual result and any output asset URLs to the user. Use the returned URLs as inputs to requested dependent steps. Download assets or perform additional local processing only when the user requests it, and retain the Cloud result URL alongside any downloaded file. Do not present task submission as completion. Do not replace a completed Cloud output with a locally recreated file from the original input, or infer permission for local recreation from a requested duration or quality target. If a returned asset's metadata or name conflicts with the requested constraints, probe that asset with an available metadata tool before claiming a mismatch or performing a correction. Corrective processing must consume the completed Cloud output, preserving its lineage; present the original Cloud result URL alongside any corrected output and explain any confirmed limitation. Never omit an available completed result URL in favor of an unfinished correction or a local fallback.

### Machine contract

These commands read live help/schema only and do not call Cloud:

```bash
byteplus-mediakit-cli editing flip-video --help
byteplus-mediakit-cli editing flip-video --schema
```

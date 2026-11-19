# gaussian-blur-video

## Capability

This tool can apply Gaussian blur to specific rectangular regions within defined time ranges. It can programmatically mask sensitive information or unwanted elements in a video. Common uses include obscuring faces, watermarks, or logos. The processed video is returned in MP4 format.

## Parameter rules

- Optional parameters may be filled only when the user specifies them, or when they can be determined accurately from user intent; do not invent values. Omit them when uncertain; ask the user if they are required to complete the task. Before submitting a business task, resolve required fields and required alternatives from the user's explicit requirements, authoritative prior results, or an unambiguous documented mapping. A media type, genre, or broad processing goal does not by itself supply missing user choices or selection criteria. If a required choice has no documented default and remains unresolved, ask a focused clarification, preserve the user's known constraints, and stop before business submission. Do not invent a custom criterion merely to make a required field present. For optional parameters, infer only values implementing a behavior the user actually requested or required by a declared parameter relation. Do not add extra processing, alignment, formatting, or tuning merely because it seems helpful.

## Cloud

### Command and lifecycle

- Command: `byteplus-mediakit-cli editing gaussian-blur-video`
- Lifecycle: async
- Return: returns `task_id`; query the terminal result next.

### Usage guide

- Array parameters (`--blur-regions`) must be valid JSON. Prefer a UTF-8 file with `--blur-regions @params.json`, or pass correctly escaped inline JSON; field names and nesting must match the “Enum/range” column and nested field descriptions in the Parameters table below.
- Do not pass comma-separated values or plain text for these parameters.
- Inline JSON needs shell escaping. A single-quoted shell argument cannot contain an unescaped apostrophe, such as in It's ready; use a JSON file for text containing apostrophes.
- `{}` and `[{}]` illustrate JSON container shapes. Replace `<parameter_json>` in command templates with valid JSON matching the parameter table, including required fields. Empty objects or arrays are valid only when the Schema permits them, including a required object with no required child fields. Required means field presence; null, empty values, zero and false are checked against their own type and constraints. Child fields marked optional remain optional; do not infer required fields from illustrative prose. Do not submit placeholders.

### Example

```bash
byteplus-mediakit-cli editing gaussian-blur-video \
  --video-url '<video_url>' \
  --blur-regions '<blur_regions_json>'
```

Replace placeholders with real user input only. Optional flags follow the parameter rules; do not invent URLs, files, enums, or business parameters.
Pass each scalar value as one shell argument. The quotes in these templates are illustrative: escape embedded quotes for the actual shell, or use an argument array when invoking a process. Never concatenate raw user input into shell code.

### Parameters

| Path | CLI flag | Type | Required | Default | Enum/range | Description |
| --- | --- | --- | --- | --- | --- | --- |
| `blur_regions` | `--blur-regions` | array<object> | yes | - | min items: 1; max items: 3 | This parameter is required and supports up to 3 configurations. |
| `blur_regions[].bottom_right_x` | - | integer | yes | - | min: 1 | This value is required. It is the horizontal X coordinate of the blur rectangle's bottom-right corner, in pixels, and it must be greater than top_left_x. |
| `blur_regions[].bottom_right_y` | - | integer | yes | - | min: 1 | The vertical Y coordinate of the blur rectangle's bottom-right corner, measured in pixels. This value is required, and it must be greater than top_left_y. |
| `blur_regions[].end_time` | - | number | yes | - | min: 0 | The end time of the blur effect, in seconds. This value is required and supports up to 3 decimal places. |
| `blur_regions[].start_time` | - | number | yes | - | min: 0 | The start time of the blur effect, in seconds. This value is required and supports up to 3 decimal places. |
| `blur_regions[].top_left_x` | - | integer | yes | - | min: 0 | The horizontal X coordinate of the blur rectangle's top-left corner, in pixels. This parameter is required. |
| `blur_regions[].top_left_y` | - | integer | yes | - | min: 0 | The vertical Y coordinate of the blur rectangle's top-left corner, in pixels. This parameter is required. |
| `callback_args` | `--callback-args` | string | no | - | - | Custom callback argument returned unchanged when the task finishes, used to correlate business records; maximum 512 bytes. Pass only when the user provides this value; do not generate, infer, or fill it. |
| `callback_url` | `--callback-url` | string | no | - | - | Callback URL that receives this task result; when set it takes priority over the global callback URL and must start with http:// or https://. Pass only when the user provides this value; do not generate, infer, or fill it. |
| `client_token` | `--client-token` | string | no | - | - | Idempotency token; case-sensitive, at most 64 printable ASCII characters. Omit by default. Reuse the same token for retries of the same logical request. Do not mint a new token for each attempt. The CLI does not infer retry intent or auto-generate tokens. Omit by default on the first asynchronous submission. Use a user-provided value as-is when specified. A retry of the same uncertain submission must keep the original token policy: reuse its token if provided, or keep the field omitted if it was not provided. After a result exists, an explicitly requested new run must use a new token. Do not mint a different value for each retry attempt. Synchronous tasks do not support idempotency checks. The CLI runtime does not infer retry intent and does not auto-generate tokens. |
| `queue_id` | `--queue-id` | string | no | - | - | Target queue ID for the submitted task. Omit to use the system default queue. Pass only when the user provides this value; do not generate, infer, or fill it. |
| `video_url` | `--video-url` | string | yes | - | min length: 1 | The URL of the video to be processed. This parameter is required. Cloud requests use publicly accessible HTTP/HTTPS URLs. Common supported video formats include mp4, flv, ts, avi, mov, wmv, and mkv. Input resolution up to 4K (3840×2160) is supported. CLI inputs may be local file paths or public HTTP/HTTPS URLs. The declared media input format also accepts vod:// and tos:// resource URIs. The CLI handles local file upload internally; pass the original input directly. |

### Parameter relations

- `$.blur_regions[].end_time` must be greater than `$.blur_regions[].start_time`.
- `$.blur_regions[].bottom_right_x` must be greater than `$.blur_regions[].top_left_x`.
- `$.blur_regions[].bottom_right_y` must be greater than `$.blur_regions[].top_left_y`.

### Result

| Path | Type | Required | Mode | Description |
| --- | --- | --- | --- | --- |
| `request_id` | string | no | Cloud | Request id returned by the service; forwarded only when non-empty. Constraints: min length: 1. |
| `task_id` | string | yes | Cloud | Unique async task id used to query status and the terminal result. Constraints: min length: 1. |
| `task_type` | string | no | Cloud | Task type returned by the service; forwarded only when non-empty. Constraints: min length: 1. |
| `duration` | number | no | Cloud terminal | The duration of the output video. |
| `resolution` | string | no | Cloud terminal | The resolution of the output video, such as 720p, 1080p, 2k, and 4k. |
| `video_url` | string | no | Cloud terminal | An HTTPS temporary download link for the output video, valid for 24 hours by default. |

After a successful Cloud call, read the returned `task_id` and check its current status. This command queries once and returns; when the task is still running, query the same task again later until terminal. Do not resubmit the business task. Use `--poll-complete` only when continuous waiting is intended; it waits without a limit by default. Pass a polling timeout or attempt limit only when specified by the user. Interrupting the CLI stops waiting without canceling the Cloud task:

```bash
byteplus-mediakit-cli shared query-task --task-id '<task_id>'
```

After a synchronous business tool succeeds or an asynchronous task reaches completed, return its actual result and any output asset URLs to the user. Use the returned URLs as inputs to requested dependent steps. Download assets or perform additional local processing only when the user requests it, and retain the Cloud result URL alongside any downloaded file. Do not present task submission as completion. Do not replace a completed Cloud output with a locally recreated file from the original input, or infer permission for local recreation from a requested duration or quality target. If a returned asset's metadata or name conflicts with the requested constraints, probe that asset with an available metadata tool before claiming a mismatch or performing a correction. Corrective processing must consume the completed Cloud output, preserving its lineage; present the original Cloud result URL alongside any corrected output and explain any confirmed limitation. Never omit an available completed result URL in favor of an unfinished correction or a local fallback.

### Machine contract

These commands read live help/schema only and do not call Cloud:

```bash
byteplus-mediakit-cli editing gaussian-blur-video --help
byteplus-mediakit-cli editing gaussian-blur-video --schema
```

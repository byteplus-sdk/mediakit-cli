# add-subtitle-to-video

## Capability

Burns a specified subtitle file or text content into video frames with custom styling and produces a new video with embedded subtitles.

## Parameter rules

- Optional parameters may be filled only when the user specifies them, or when they can be determined accurately from user intent; do not invent values. Omit them when uncertain; ask the user if they are required to complete the task. Before submitting a business task, resolve required fields and required alternatives from the user's explicit requirements, authoritative prior results, or an unambiguous documented mapping. A media type, genre, or broad processing goal does not by itself supply missing user choices or selection criteria. If a required choice has no documented default and remains unresolved, ask a focused clarification, preserve the user's known constraints, and stop before business submission. Do not invent a custom criterion merely to make a required field present. For optional parameters, infer only values implementing a behavior the user actually requested or required by a declared parameter relation. Do not add extra processing, alignment, formatting, or tuning merely because it seems helpful.
- Required input choice: subtitle_url or subtitles. If none of these alternatives has a value supplied or uniquely resolved from the user's explicit requirements and a documented mapping, ask for the missing choice and wait for the answer before any business call. Do not manufacture a value to satisfy the alternative. Media metadata, a genre label, or your own judgment does not establish the user's preferences or selection criteria. Preserve already supplied constraints while asking; do not run a metadata probe as a substitute for that answer.

## Cloud

### Command and lifecycle

- Command: `byteplus-mediakit-cli editing add-subtitle-to-video`
- Lifecycle: async
- Return: returns `task_id`; query the terminal result next.

### Usage guide

- Array parameters (`--subtitles`) must be valid JSON. Prefer a UTF-8 file with `--subtitles @params.json`, or pass correctly escaped inline JSON; field names and nesting must match the “Enum/range” column and nested field descriptions in the Parameters table below.
- Do not pass comma-separated values or plain text for these parameters.
- Inline JSON needs shell escaping. A single-quoted shell argument cannot contain an unescaped apostrophe, such as in It's ready; use a JSON file for text containing apostrophes.
- `{}` and `[{}]` illustrate JSON container shapes. Replace `<parameter_json>` in command templates with valid JSON matching the parameter table, including required fields. Empty objects or arrays are valid only when the Schema permits them, including a required object with no required child fields. Required means field presence; null, empty values, zero and false are checked against their own type and constraints. Child fields marked optional remain optional; do not infer required fields from illustrative prose. Do not submit placeholders.

### Example

```bash
byteplus-mediakit-cli editing add-subtitle-to-video \
  --video-url '<video_url>' \
  --subtitle-url '<subtitle_url>'
```

Replace placeholders with real user input only. Optional flags follow the parameter rules; do not invent URLs, files, enums, or business parameters.
Pass each scalar value as one shell argument. The quotes in these templates are illustrative: escape embedded quotes for the actual shell, or use an argument array when invoking a process. Never concatenate raw user input into shell code.

### Parameters

| Path | CLI flag | Type | Required | Default | Enum/range | Description |
| --- | --- | --- | --- | --- | --- | --- |
| `callback_args` | `--callback-args` | string | no | - | - | Custom callback argument returned unchanged when the task finishes, used to correlate business records; maximum 512 bytes. Pass only when the user provides this value; do not generate, infer, or fill it. |
| `callback_url` | `--callback-url` | string | no | - | - | Callback URL that receives this task result; when set it takes priority over the global callback URL and must start with http:// or https://. Pass only when the user provides this value; do not generate, infer, or fill it. |
| `client_token` | `--client-token` | string | no | - | - | Idempotency token; case-sensitive, at most 64 printable ASCII characters. Omit by default. Reuse the same token for retries of the same logical request. Do not mint a new token for each attempt. The CLI does not infer retry intent or auto-generate tokens. Omit by default on the first asynchronous submission. Use a user-provided value as-is when specified. A retry of the same uncertain submission must keep the original token policy: reuse its token if provided, or keep the field omitted if it was not provided. After a result exists, an explicitly requested new run must use a new token. Do not mint a different value for each retry attempt. Synchronous tasks do not support idempotency checks. The CLI runtime does not infer retry intent and does not auto-generate tokens. |
| `queue_id` | `--queue-id` | string | no | - | - | Target queue ID for the submitted task. Omit to use the system default queue. Pass only when the user provides this value; do not generate, infer, or fill it. |
| `subtitle_font_color` | `--subtitle-font-color` | string | no | "#FFFFFFFF" | - | The subtitle font color, specified in RGBA format. This setting is optional; the default is #FFFFFFFF, which is opaque white. |
| `subtitle_font_size` | `--subtitle-font-size` | integer | no | 50 | min: 1 | The subtitle font size, measured in pixels (px). It defaults to 50. This parameter is optional. |
| `subtitle_font_type` | `--subtitle-font-type` | string | no | "inter" | enum: ["inter","montserrat","oppo_sans","roboto","source_han_serif"] | Subtitle font, default Inter. Montserrat, OPPO Sans, Roboto and Source Han Serif are also available. |
| `subtitle_pos_preset` | `--subtitle-pos-preset` | string | no | "bottom_center" | enum: ["bottom_center","top_center","center","lower_third"] | The preset subtitle position. This parameter is optional. It defaults to bottom_center and can also be set to top_center, center, or lower_third. |
| `subtitle_url` | `--subtitle-url` | string | required in an alternative: subtitle_url or subtitles | - | - | The URL of the subtitle file. publicly accessible HTTP/HTTPS URLs are supported, and common supported subtitle formats include SRT, VTT, and ASS. This parameter is optional. When both subtitle_url and subtitles are present, the subtitle_url content takes priority. CLI inputs may be local file paths or public HTTP/HTTPS URLs. The CLI handles local file upload internally; pass the original input directly. |
| `subtitles` | `--subtitles` | array<object> | required in an alternative: subtitle_url or subtitles | - | min items: 1 | A list of subtitle objects, each containing subtitle text and start and end times. This parameter is optional. |
| `subtitles[].end_time` | - | number | yes | - | min: 0 | The time when the subtitle disappears, measured in seconds. This value is required. |
| `subtitles[].start_time` | - | number | yes | - | min: 0 | The time when the subtitle appears, measured in seconds. This field is required. |
| `subtitles[].subtitle_text` | - | string | yes | - | - | The text content of a single subtitle. This field is required. |
| `video_url` | `--video-url` | string | yes | - | min length: 1 | The URL of the video to which subtitles are added. publicly accessible HTTP/HTTPS URLs are supported as the video file source. Common supported video formats include mp4, flv, ts, avi, mov, wmv, and mkv. Input resolution up to 4K (3840×2160) is supported. It is recommended that one input video file not exceed 10 GB. This field is required. CLI inputs may be local file paths or public HTTP/HTTPS URLs. The declared media input format also accepts vod:// and tos:// resource URIs. The CLI handles local file upload internally; pass the original input directly. |

### Parameter relations

- At least one of (subtitle_url is provided) or (subtitles is provided) must hold.
- `$.subtitles[].end_time` must be greater than `$.subtitles[].start_time`.

### Result

| Path | Type | Required | Mode | Description |
| --- | --- | --- | --- | --- |
| `request_id` | string | no | Cloud | Request id returned by the service; forwarded only when non-empty. Constraints: min length: 1. |
| `task_id` | string | yes | Cloud | Unique async task id used to query status and the terminal result. Constraints: min length: 1. |
| `task_type` | string | no | Cloud | Task type returned by the service; forwarded only when non-empty. Constraints: min length: 1. |
| `duration` | number | no | Cloud terminal | The total duration of the output video, measured in seconds. |
| `resolution` | string | no | Cloud terminal | The resolution of the output video, which can be 240p, 360p, 480p, 540p, 720p, 1080p, 2k, or 4k. Constraints: enum: ["240p","360p","480p","540p","720p","1080p","2k","4k"]. |
| `video_url` | string | no | Cloud terminal | The download URL of the generated video with embedded subtitles. The generated video is in MP4 format. The URL is a temporary HTTPS download link that is valid for 24 hours by default. |

After a successful Cloud call, read the returned `task_id` and check its current status. This command queries once and returns; when the task is still running, query the same task again later until terminal. Do not resubmit the business task. Use `--poll-complete` only when continuous waiting is intended; it waits without a limit by default. Pass a polling timeout or attempt limit only when specified by the user. Interrupting the CLI stops waiting without canceling the Cloud task:

```bash
byteplus-mediakit-cli shared query-task --task-id '<task_id>'
```

After a synchronous business tool succeeds or an asynchronous task reaches completed, return its actual result and any output asset URLs to the user. Use the returned URLs as inputs to requested dependent steps. Download assets or perform additional local processing only when the user requests it, and retain the Cloud result URL alongside any downloaded file. Do not present task submission as completion. Do not replace a completed Cloud output with a locally recreated file from the original input, or infer permission for local recreation from a requested duration or quality target. If a returned asset's metadata or name conflicts with the requested constraints, probe that asset with an available metadata tool before claiming a mismatch or performing a correction. Corrective processing must consume the completed Cloud output, preserving its lineage; present the original Cloud result URL alongside any corrected output and explain any confirmed limitation. Never omit an available completed result URL in favor of an unfinished correction or a local fallback.

### Machine contract

These commands read live help/schema only and do not call Cloud:

```bash
byteplus-mediakit-cli editing add-subtitle-to-video --help
byteplus-mediakit-cli editing add-subtitle-to-video --schema
```

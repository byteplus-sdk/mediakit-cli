# erase-video-subtitle-pro

## Capability

This capability provides high-quality, seamless subtitle removal and restores the original picture to the greatest extent possible. Different queues are the primary way to allocate costs to specific projects.

## Parameter rules

- Optional parameters may be filled only when the user specifies them, or when they can be determined accurately from user intent; do not invent values. Omit them when uncertain; ask the user if they are required to complete the task. Before submitting a business task, resolve required fields and required alternatives from the user's explicit requirements, authoritative prior results, or an unambiguous documented mapping. A media type, genre, or broad processing goal does not by itself supply missing user choices or selection criteria. If a required choice has no documented default and remains unresolved, ask a focused clarification, preserve the user's known constraints, and stop before business submission. Do not invent a custom criterion merely to make a required field present. For optional parameters, infer only values implementing a behavior the user actually requested or required by a declared parameter relation. Do not add extra processing, alignment, formatting, or tuning merely because it seems helpful.

## Cloud

### Command and lifecycle

- Command: `byteplus-mediakit-cli video erase-video-subtitle-pro`
- Lifecycle: async
- Return: returns `task_id`; query the terminal result next.

### Usage guide

- JSON parameters (`--erase-ratio-location`, `--subtitle-filter`, `--time-segment-filter`) must be valid JSON. Prefer a UTF-8 file using `@params.json`, or pass correctly escaped inline JSON; field names and nesting must match the Parameters table below.
- Do not pass comma-separated values or plain text for these parameters.
- Inline JSON needs shell escaping. A single-quoted shell argument cannot contain an unescaped apostrophe, such as in It's ready; use a JSON file for text containing apostrophes.
- `{}` and `[{}]` illustrate JSON container shapes. Replace `<parameter_json>` in command templates with valid JSON matching the parameter table, including required fields. Empty objects or arrays are valid only when the Schema permits them, including a required object with no required child fields. Required means field presence; null, empty values, zero and false are checked against their own type and constraints. Child fields marked optional remain optional; do not infer required fields from illustrative prose. Do not submit placeholders.

### Example

```bash
byteplus-mediakit-cli video erase-video-subtitle-pro \
  --video-url '<video_url>'
```

Replace placeholders with real user input only. Optional flags follow the parameter rules; do not invent URLs, files, enums, or business parameters.
Pass each scalar value as one shell argument. The quotes in these templates are illustrative: escape embedded quotes for the actual shell, or use an argument array when invoking a process. Never concatenate raw user input into shell code.

### Parameters

| Path | CLI flag | Type | Required | Default | Enum/range | Description |
| --- | --- | --- | --- | --- | --- | --- |
| `callback_args` | `--callback-args` | string | no | - | - | Custom callback argument returned unchanged when the task finishes, used to correlate business records; maximum 512 bytes. Pass only when the user provides this value; do not generate, infer, or fill it. |
| `callback_url` | `--callback-url` | string | no | - | - | Callback URL that receives this task result; when set it takes priority over the global callback URL and must start with http:// or https://. Pass only when the user provides this value; do not generate, infer, or fill it. |
| `client_token` | `--client-token` | string | no | - | - | Idempotency token; case-sensitive, at most 64 printable ASCII characters. Omit by default. Reuse the same token for retries of the same logical request. Do not mint a new token for each attempt. The CLI does not infer retry intent or auto-generate tokens. Omit by default on the first asynchronous submission. Use a user-provided value as-is when specified. A retry of the same uncertain submission must keep the original token policy: reuse its token if provided, or keep the field omitted if it was not provided. After a result exists, an explicitly requested new run must use a new token. Do not mint a different value for each retry attempt. Synchronous tasks do not support idempotency checks. The CLI runtime does not infer retry intent and does not auto-generate tokens. |
| `erase_ratio_location` | `--erase-ratio-location` | array<object> | no | - | min items: 0; max items: 20 | Global erasure boxes, up to 20. In Subtitle mode, the effective region is their intersection with the lower half of the frame. Global boxes cannot be combined with per-segment boxes. Each Location object defines a rectangular erasure area. It uses normalized coordinates from top-left (0, 0) to bottom-right (1, 1). |
| `erase_ratio_location[].bottom_right_x` | - | number | yes | - | min: 0; max: 1 | bottom_right_x is required, ranges from 0 to 1 inclusive, and must be greater than top_left_x. It is the horizontal ratio of the box's bottom-right corner relative to the frame's left edge. |
| `erase_ratio_location[].bottom_right_y` | - | number | yes | - | min: 0; max: 1 | bottom_right_y is required, ranges from 0 to 1 inclusive, and must be greater than top_left_y. It is the vertical ratio of the box's bottom-right corner relative to the frame's top edge. |
| `erase_ratio_location[].top_left_x` | - | number | yes | - | min: 0; max: 1 | top_left_x is required and ranges from 0 to 1 inclusive. It is the horizontal ratio of the box's top-left corner relative to the frame's left edge. |
| `erase_ratio_location[].top_left_y` | - | number | yes | - | min: 0; max: 1 | This value is required and specifies the vertical ratio of the box's top-left corner relative to the frame's top edge. It can be set from a minimum of 0 to a maximum of 1. |
| `mode` | `--mode` | string | no | "Subtitle" | enum: ["Subtitle","Text"] | Select Subtitle to erase OCR-detected subtitles in the bottom half of the frame, or Text to detect subtitles and other text across the frame, excluding scene text such as signboards. Custom boxes restrict erasure to the relevant region. Defaults to Subtitle. |
| `model_version` | `--model-version` | string | no | "v5" | enum: ["v5","v5.6"] | Erasure version, default v5. V5 favors stable quality and faster processing. V5.6 reduces flicker and ripple artifacts and supports more languages, but takes noticeably longer; animated text or complex backgrounds may still leave incomplete erasure. |
| `output_encode_mode` | `--output-encode-mode` | string | no | "Quality" | enum: ["Quality","Size"] | This parameter is optional, supports Quality and Size, and defaults to Quality. Quality mode uses a higher bitrate for better quality, but the file may be larger. Size mode keeps output bitrate close to the source while ensuring a certain quality level. |
| `queue_id` | `--queue-id` | string | no | - | - | Target queue ID for the submitted task. Omit to use the system default queue. Pass only when the user provides this value; do not generate, infer, or fill it. |
| `subtitle_filter` | `--subtitle-filter` | object | no | - | - | Optional. Takes effect only when the mode is Subtitle, where it fine-tunes subtitle detection using text height and horizontal centering to help prevent accidental erasure of non-subtitle content such as titles or logos. If it is not specified, default values are used. |
| `subtitle_filter.center_offset_ratio` | - | number | no | 0.08 | min: 0; max: 1 | Optional. The maximum allowed ratio of the offset between the text's horizontal center and the video's horizontal center. Text exceeding this ratio is not treated as a subtitle. Ranges from a minimum of 0 to a maximum of 1, and defaults to 0.08. |
| `subtitle_filter.max_text_height_ratio` | - | number | no | 0.1 | min: 0; max: 1 | Optional. The maximum ratio of text height to video height. Text taller than this ratio is not treated as subtitles. Ranges from a minimum of 0 to a maximum of 1, and defaults to 0.1, or 10%. |
| `subtitle_filter.min_text_height_ratio` | - | number | no | - | min: 0; max: 1 | The minimum ratio of text height to video height. This field is optional and defaults to 0.01, or 1%. It can be set from a minimum value of 0 to a maximum value of 1. Text shorter than this ratio is not treated as subtitles. |
| `time_segment_filter` | `--time-segment-filter` | object | no | - | - | Include or exclude video intervals for erasure. Omission processes the whole video. |
| `time_segment_filter.mode` | - | string | yes | - | enum: ["skip","selected"] | This field is required. It supports selected and skip. selected erases subtitles only within the listed segments, while skip preserves the listed segments and erases the rest of the video. |
| `time_segment_filter.segments` | - | array<object> | yes | - | min items: 1 | Intervals to include or exclude according to the selected filter mode. One interval in the erasure time filter. |
| `time_segment_filter.segments[].end_time` | - | number | yes | - | min: 0 | Required. The end time of the segment, measured in seconds, and must be greater than start_time. |
| `time_segment_filter.segments[].erase_ratio_location` | - | array<object> | no | - | min items: 0; max items: 20 | Erasure boxes for this time segment, up to 20. Supported only in selected mode and cannot be combined with global boxes. Omit or use an empty array to keep the default behavior for this segment. One normalized erasure rectangle for this interval. |
| `time_segment_filter.segments[].erase_ratio_location[].bottom_right_x` | - | number | yes | - | min: 0; max: 1 | Right edge of the erasure box as a fraction of frame width, from 0 to 1. Must be greater than top_left_x. |
| `time_segment_filter.segments[].erase_ratio_location[].bottom_right_y` | - | number | yes | - | min: 0; max: 1 | Bottom edge of the erasure box as a fraction of frame height, from 0 to 1. Must be greater than top_left_y. |
| `time_segment_filter.segments[].erase_ratio_location[].top_left_x` | - | number | yes | - | min: 0; max: 1 | Left edge of the erasure box as a fraction of frame width, from 0 to 1. |
| `time_segment_filter.segments[].erase_ratio_location[].top_left_y` | - | number | yes | - | min: 0; max: 1 | Top edge of the erasure box as a fraction of frame height, from 0 to 1. |
| `time_segment_filter.segments[].start_time` | - | number | yes | - | min: 0 | Start time of the segment, measured in seconds. This value is required. |
| `video_url` | `--video-url` | string | yes | - | min length: 1 | The URL of the video whose subtitles are to be erased. This value is required. publicly accessible HTTP/HTTPS video URLs are supported. Supported mainstream video formats include mp4, flv, ts, avi, mov, wmv, and mkv. The input resolution supports up to 2K, and the output resolution supports up to 1080P. CLI inputs may be local file paths or public HTTP/HTTPS URLs. The declared media input format also accepts vod:// and tos:// resource URIs. The CLI handles local file upload internally; pass the original input directly. |

### Parameter relations

- Must not satisfy ($.time_segment_filter.segments[].erase_ratio_location is provided) when erase_ratio_location is provided.
- Must not satisfy ($.time_segment_filter.segments[].erase_ratio_location is provided) when time_segment_filter is provided and time_segment_filter.mode=skip.
- `$.erase_ratio_location[].bottom_right_x` must be greater than `$.erase_ratio_location[].top_left_x`.
- `$.erase_ratio_location[].bottom_right_y` must be greater than `$.erase_ratio_location[].top_left_y`.
- `$.time_segment_filter.segments[].end_time` must be greater than `$.time_segment_filter.segments[].start_time`.
- `$.time_segment_filter.segments[].erase_ratio_location[].bottom_right_x` must be greater than `$.time_segment_filter.segments[].erase_ratio_location[].top_left_x`.
- `$.time_segment_filter.segments[].erase_ratio_location[].bottom_right_y` must be greater than `$.time_segment_filter.segments[].erase_ratio_location[].top_left_y`.

### Result

| Path | Type | Required | Mode | Description |
| --- | --- | --- | --- | --- |
| `request_id` | string | no | Cloud | Request id returned by the service; forwarded only when non-empty. Constraints: min length: 1. |
| `task_id` | string | yes | Cloud | Unique async task id used to query status and the terminal result. Constraints: min length: 1. |
| `task_type` | string | no | Cloud | Task type returned by the service; forwarded only when non-empty. Constraints: min length: 1. |
| `duration` | number | no | Cloud terminal | The total duration of the output video, measured in seconds. |
| `video_url` | string | no | Cloud terminal | The download URL for the video with subtitles erased. The URL is valid for 24 hours. |

After a successful Cloud call, read the returned `task_id` and check its current status. This command queries once and returns; when the task is still running, query the same task again later until terminal. Do not resubmit the business task. Use `--poll-complete` only when continuous waiting is intended; it waits without a limit by default. Pass a polling timeout or attempt limit only when specified by the user. Interrupting the CLI stops waiting without canceling the Cloud task:

```bash
byteplus-mediakit-cli shared query-task --task-id '<task_id>'
```

After a synchronous business tool succeeds or an asynchronous task reaches completed, return its actual result and any output asset URLs to the user. Use the returned URLs as inputs to requested dependent steps. Download assets or perform additional local processing only when the user requests it, and retain the Cloud result URL alongside any downloaded file. Do not present task submission as completion. Do not replace a completed Cloud output with a locally recreated file from the original input, or infer permission for local recreation from a requested duration or quality target. If a returned asset's metadata or name conflicts with the requested constraints, probe that asset with an available metadata tool before claiming a mismatch or performing a correction. Corrective processing must consume the completed Cloud output, preserving its lineage; present the original Cloud result URL alongside any corrected output and explain any confirmed limitation. Never omit an available completed result URL in favor of an unfinished correction or a local fallback.

### Machine contract

These commands read live help/schema only and do not call Cloud:

```bash
byteplus-mediakit-cli video erase-video-subtitle-pro --help
byteplus-mediakit-cli video erase-video-subtitle-pro --schema
```

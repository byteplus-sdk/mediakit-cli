# extract-frames

## Capability

This capability can capture one or more still images from a video. The captured images can be used for video covers, preview thumbnails, sprite sheets, or as input for other video understanding tasks.

## Parameter rules

- Optional parameters may be filled only when the user specifies them, or when they can be determined accurately from user intent; do not invent values. Omit them when uncertain; ask the user if they are required to complete the task. Before submitting a business task, resolve required fields and required alternatives from the user's explicit requirements, authoritative prior results, or an unambiguous documented mapping. A media type, genre, or broad processing goal does not by itself supply missing user choices or selection criteria. If a required choice has no documented default and remains unresolved, ask a focused clarification, preserve the user's known constraints, and stop before business submission. Do not invent a custom criterion merely to make a required field present. For optional parameters, infer only values implementing a behavior the user actually requested or required by a declared parameter relation. Do not add extra processing, alignment, formatting, or tuning merely because it seems helpful.

## Cloud

### Command and lifecycle

- Command: `byteplus-mediakit-cli video extract-frames`
- Lifecycle: async
- Return: returns `task_id`; query the terminal result next.

### Usage guide

- JSON parameters (`--specified-frames`, `--specified-time`) must be valid JSON. Prefer a UTF-8 file using `@params.json`, or pass correctly escaped inline JSON; field names and nesting must match the Parameters table below.
- Do not pass comma-separated values or plain text for these parameters.
- Inline JSON needs shell escaping. A single-quoted shell argument cannot contain an unescaped apostrophe, such as in It's ready; use a JSON file for text containing apostrophes.
- `{}` and `[{}]` illustrate JSON container shapes. Replace `<parameter_json>` in command templates with valid JSON matching the parameter table, including required fields. Empty objects or arrays are valid only when the Schema permits them, including a required object with no required child fields. Required means field presence; null, empty values, zero and false are checked against their own type and constraints. Child fields marked optional remain optional; do not infer required fields from illustrative prose. Do not submit placeholders.
- Boolean parameters (`--enable-sprite`) must be written as `--flag=true` or `--flag=false`, or as bare `--flag` (equivalent to true). Do not pass a space-separated value such as `--flag true`, which the CLI treats as a positional argument.
- Pass a boolean explicitly when the user specifies true or false, including values equal to the default. Omit it when the user has not specified a value and no relation requires it.
- Requesting the main operation does not request every optional behavior. Do not enable an optional switch or fill its dependent defaults merely because input metadata differs or a changed output would be more convenient. Keep those fields omitted unless the user requested that separate behavior or a declared parameter relation requires their explicit presence.

### Example

```bash
byteplus-mediakit-cli video extract-frames \
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
| `enable_sprite` | `--enable-sprite` | boolean | no | false | - | Controls whether the captured images are combined, and can be set to false or true. false outputs multiple independent snapshots, while true outputs a sprite sheet containing all snapshots. It defaults to false. |
| `queue_id` | `--queue-id` | string | no | - | - | Target queue ID for the submitted task. Omit to use the system default queue. Pass only when the user provides this value; do not generate, infer, or fill it. |
| `scale_long` | `--scale-long` | integer | no | - | min: 0; max: 4096 | Sets the maximum pixel value of the output image's long side, with a minimum of 0 and a maximum of 4096. The output image's long side cannot exceed the original video's long side. When both scale_long and scale_short are set, scaling preserves aspect ratio while respecting both maxima. When enable_sprite is true, scale_long defines the long side of a single tile. |
| `scale_short` | `--scale-short` | integer | no | - | min: 0; max: 4096 | The maximum pixel value for the output image's short side. It can be set from a minimum of 0 to a maximum of 4096, and the output image's short side cannot exceed the original video's short side. When enable_sprite is true, this value defines the short side of a single tile. |
| `scene_change_threshold` | `--scene-change-threshold` | number | no | 0.1 | min: 0; max: 1 | Scene-change threshold between 0 and 1, inclusive, default 0.1. Smaller values detect more changes and can produce more snapshots. Applies in SceneChange mode. |
| `snapshot_limit` | `--snapshot-limit` | integer | no | - | min: 1; max: 1000 | The maximum number of snapshots to generate. It can be set from a minimum of 1 to a maximum of 1000, and the actual number of output snapshots may be smaller than snapshot_limit. It is effective only for TimeInterval or SceneChange. When sprite sheets are enabled, it limits the number of large sprite sheets, and in sprite-sheet mode the small-tile limit is snapshot_limit * sprite_rows * sprite_cols. |
| `snapshot_type` | `--snapshot-type` | string | no | "TimeInterval" | enum: ["TimeInterval","SpecifiedTime","SpecifiedFrames","SceneChange"] | Determines the frame-extraction strategy and can be set to TimeInterval, SpecifiedTime, SpecifiedFrames, or SceneChange, defaulting to TimeInterval. TimeInterval extracts frames at a fixed time interval and is used with time_interval. SpecifiedTime extracts frames at specified time points and is used with specified_time. SpecifiedFrames extracts frames at specified frame numbers and is used with specified_frames. SceneChange extracts frames on scene changes and is used with scene_change_threshold. |
| `specified_frames` | `--specified-frames` | array<integer> | no | - | min items: 1; max items: 2; element enum: [0,-1] | An optional list that supports up to 2 values. |
| `specified_time` | `--specified-time` | array<number> | no | - | min items: 1; max items: 1000; element min: 0 | An optional list that supports up to 1000 time points. |
| `sprite_cols` | `--sprite-cols` | integer | no | 10 | min: 1; max: 100 | The number of tiles along the sprite sheet's horizontal X-axis. It defaults to 10, with a minimum of 1 and a maximum of 100. A single side of the final sprite sheet should not exceed 16384 pixels. This setting takes effect only when enable_sprite is true. |
| `sprite_rows` | `--sprite-rows` | integer | no | 10 | min: 1; max: 100 | The number of rows of tiles along the sprite sheet's vertical Y-axis. It defaults to 10, with a minimum of 1 and a maximum of 100. Setting too many sprite rows or columns may cause the task to fail, and a single side of the final sprite sheet should not exceed 16384 pixels. This setting takes effect only when enable_sprite is true. |
| `time_interval` | `--time-interval` | number | no | 1 | min: 0.001 | Snapshot interval in seconds, at most three decimal places, at least 0.001 and default 1. Applies in TimeInterval mode. |
| `video_url` | `--video-url` | string | yes | - | min length: 1 | video_url is the URL of the video to be processed. Supported mainstream video formats include mp4, flv, ts, avi, mov, wmv, and mkv. Only publicly accessible HTTP or HTTPS video URLs are supported. CLI inputs may be local file paths or public HTTP/HTTPS URLs. The declared media input format also accepts vod:// and tos:// resource URIs. The CLI handles local file upload internally; pass the original input directly. |

### Result

| Path | Type | Required | Mode | Description |
| --- | --- | --- | --- | --- |
| `request_id` | string | no | Cloud | Request id returned by the service; forwarded only when non-empty. Constraints: min length: 1. |
| `task_id` | string | yes | Cloud | Unique async task id used to query status and the terminal result. Constraints: min length: 1. |
| `task_type` | string | no | Cloud | Task type returned by the service; forwarded only when non-empty. Constraints: min length: 1. |
| `snapshot_count` | integer | no | Cloud terminal | In sprite-sheet mode, snapshot_count is the number of small tiles in the sprite sheet. |
| `snapshots` | array<object> | no | Cloud terminal | Snapshot results, with information about each extracted image. |
| `snapshots[].image_url` | string | no | Cloud terminal | Snapshot download link. Download and save it within the 24-hour validity period. |

After a successful Cloud call, read the returned `task_id` and check its current status. This command queries once and returns; when the task is still running, query the same task again later until terminal. Do not resubmit the business task. Use `--poll-complete` only when continuous waiting is intended; it waits without a limit by default. Pass a polling timeout or attempt limit only when specified by the user. Interrupting the CLI stops waiting without canceling the Cloud task:

```bash
byteplus-mediakit-cli shared query-task --task-id '<task_id>'
```

After a synchronous business tool succeeds or an asynchronous task reaches completed, return its actual result and any output asset URLs to the user. Use the returned URLs as inputs to requested dependent steps. Download assets or perform additional local processing only when the user requests it, and retain the Cloud result URL alongside any downloaded file. Do not present task submission as completion. Do not replace a completed Cloud output with a locally recreated file from the original input, or infer permission for local recreation from a requested duration or quality target. If a returned asset's metadata or name conflicts with the requested constraints, probe that asset with an available metadata tool before claiming a mismatch or performing a correction. Corrective processing must consume the completed Cloud output, preserving its lineage; present the original Cloud result URL alongside any corrected output and explain any confirmed limitation. Never omit an available completed result URL in favor of an unfinished correction or a local fallback.

### Machine contract

These commands read live help/schema only and do not call Cloud:

```bash
byteplus-mediakit-cli video extract-frames --help
byteplus-mediakit-cli video extract-frames --schema
```

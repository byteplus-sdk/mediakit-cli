# extract-frames

## Capability

This capability can capture one or more still images from a video. The captured images can be used for video covers, preview thumbnails, sprite sheets, or as input for other video understanding tasks.

## Parameter rules

- Optional parameters may be filled only when the user specifies them, or when they can be determined accurately from user intent; do not invent values. Omit them when uncertain; ask the user if they are required to complete the task.

## Cloud

### Command and lifecycle

- Command: `byteplus-mediakit-cli video extract-frames`
- Lifecycle: async
- Return: returns `task_id`; query the terminal result next.

### Usage guide

- Object or object array parameters (`--specified-frames`, `--specified-time`) must be passed as valid JSON strings wrapped in single quotes; field names and nesting must match the Parameters table below.
- Do not pass comma-separated values or plain text for these parameters.
- Boolean parameters (`--enable-sprite`) must be written as `--flag=true` or `--flag=false`, or as bare `--flag` (equivalent to true). Do not pass a space-separated value such as `--flag true`, which the CLI treats as a positional argument.
- Omit boolean flags when they would repeat the default value.

### Example

```bash
MEDIAKIT_BYTEPLUS_SURFACE=skill MEDIAKIT_BYTEPLUS_RUNTIME=<runtime> \
  byteplus-mediakit-cli video extract-frames \
  --video-url <video_url>
```

Replace placeholders with real user input only. Optional flags follow the parameter rules; do not invent URLs, files, enums, or business parameters.

### Parameters

| Path | CLI flag | Type | Required | Default | Enum/range | Description |
| --- | --- | --- | --- | --- | --- | --- |
| `callback_args` | `--callback-args` | string | no | - | - | Custom callback argument returned unchanged when the task finishes, used to correlate business records; maximum 512 bytes. Pass only when the user provides this value; do not generate, infer, or fill it. |
| `callback_url` | `--callback-url` | string | no | - | - | Callback URL that receives this task result; when set it takes priority over the global callback URL and must start with http:// or https://. Pass only when the user provides this value; do not generate, infer, or fill it. |
| `client_token` | `--client-token` | string | no | - | - | Idempotency token; case-sensitive, at most 64 printable ASCII characters. Omit by default. Reuse the same token for retries of the same logical request. Do not mint a new token for each attempt. The CLI does not infer retry intent or auto-generate tokens. Omit by default. Use the user-provided value as-is when specified. When the user explicitly retries, the same logical request must reuse the same token. If a token already exists, reuse it; if earlier requests had no token, you may create one starting with this retry and keep reusing it, but that token does not provide retroactive idempotency for earlier requests. A change in business parameters is a new request and must not reuse the old token. Do not mint a different value for each attempt. The CLI runtime does not infer retry intent and does not auto-generate tokens. |
| `enable_sprite` | `--enable-sprite` | boolean | no | false | - | Controls whether the captured images are combined, and can be set to false or true. false outputs multiple independent snapshots, while true outputs a sprite sheet containing all snapshots. It defaults to false. |
| `queue_id` | `--queue-id` | string | no | - | - | Target queue ID for the submitted task. Omit to use the system default queue. Pass only when the user provides this value; do not generate, infer, or fill it. |
| `scale_long` | `--scale-long` | integer | no | - | min: 0; max: 4096 | Sets the maximum pixel value of the output image's long side, with a minimum of 0 and a maximum of 4096. The output image's long side cannot exceed the original video's long side. When both scale_long and scale_short are set, scaling preserves aspect ratio while respecting both maxima. When enable_sprite is true, scale_long defines the long side of a single tile. |
| `scale_short` | `--scale-short` | integer | no | - | min: 0; max: 4096 | The maximum pixel value for the output image's short side. It can be set from a minimum of 0 to a maximum of 4096, and the output image's short side cannot exceed the original video's short side. When enable_sprite is true, this value defines the short side of a single tile. |
| `scene_change_threshold` | `--scene-change-threshold` | number | no | 0.1 | min: 0; max: 1 | Controls the sensitivity of scene-change detection. A smaller value makes it more sensitive and may produce more snapshots. It defaults to 0.1, must be greater than 0, and must be less than 1. This value is effective only when snapshot_type is SceneChange. |
| `snapshot_limit` | `--snapshot-limit` | integer | no | - | min: 1; max: 1000 | The maximum number of snapshots to generate. It can be set from a minimum of 1 to a maximum of 1000, and the actual number of output snapshots may be smaller than snapshot_limit. It is effective only for TimeInterval or SceneChange. When sprite sheets are enabled, it limits the number of large sprite sheets, and in sprite-sheet mode the small-tile limit is snapshot_limit * sprite_rows * sprite_cols. |
| `snapshot_type` | `--snapshot-type` | string | no | "TimeInterval" | enum: ["TimeInterval","SpecifiedTime","SpecifiedFrames","SceneChange"] | Determines the frame-extraction strategy and can be set to TimeInterval, SpecifiedTime, SpecifiedFrames, or SceneChange, defaulting to TimeInterval. TimeInterval extracts frames at a fixed time interval and is used with time_interval. SpecifiedTime extracts frames at specified time points and is used with specified_time. SpecifiedFrames extracts frames at specified frame numbers and is used with specified_frames. SceneChange extracts frames on scene changes and is used with scene_change_threshold. |
| `specified_frames` | `--specified-frames` | array<integer> | no | - | min items: 1; max items: 2; element enum: [0,-1] | An optional list that supports up to 2 values. |
| `specified_time` | `--specified-time` | array<number> | no | - | min items: 1; max items: 1000; element min: 0 | An optional list that supports up to 1000 time points. |
| `sprite_cols` | `--sprite-cols` | integer | no | 10 | min: 1; max: 100 | The number of tiles along the sprite sheet's horizontal X-axis. It defaults to 10, with a minimum of 1 and a maximum of 100. A single side of the final sprite sheet should not exceed 16384 pixels. This setting takes effect only when enable_sprite is true. |
| `sprite_rows` | `--sprite-rows` | integer | no | 10 | min: 1; max: 100 | The number of rows of tiles along the sprite sheet's vertical Y-axis. It defaults to 10, with a minimum of 1 and a maximum of 100. Setting too many sprite rows or columns may cause the task to fail, and a single side of the final sprite sheet should not exceed 16384 pixels. This setting takes effect only when enable_sprite is true. |
| `time_interval` | `--time-interval` | number | no | 1 | min: 0.001 | The time interval between captured frames, measured in seconds. It defaults to 1, must be greater than 0.001, and supports up to 3 decimal places. This setting takes effect only when snapshot_type is TimeInterval. |
| `video_url` | `--video-url` | string | yes | - | - | video_url is the URL of the video to be processed. Supported mainstream video formats include mp4, flv, ts, avi, mov, wmv, and mkv. Only publicly accessible HTTP or HTTPS video URLs are supported. |

### Result

| Path | Type | Required | Mode | Description |
| --- | --- | --- | --- | --- |
| `request_id` | string | no | Cloud | Request id returned by the service; forwarded only when non-empty. |
| `task_id` | string | yes | Cloud | Unique async task id used to query status and the terminal result. |
| `task_type` | string | no | Cloud | Task type returned by the service; forwarded only when non-empty. |
| `result.snapshot_count` | integer | no | Cloud terminal | In sprite-sheet mode, snapshot_count is the number of small tiles in the sprite sheet. |
| `result.snapshots` | array<object> | no | Cloud terminal | snapshots is a list of snapshot entries. |
| `result.snapshots[].image_url` | string | no | Cloud terminal | When enable_sprite is true, image_url points to the combined large sprite sheet. The snapshot download URL is valid for 24 hours. |

After a successful Cloud call, read `task_id` and query the terminal result:

```bash
MEDIAKIT_BYTEPLUS_SURFACE=skill MEDIAKIT_BYTEPLUS_RUNTIME=<runtime> \
  byteplus-mediakit-cli shared query-task --task-id <task_id> --poll-complete
```

### Machine contract

These commands read live help/schema only and do not call Cloud:

```bash
byteplus-mediakit-cli video extract-frames --help
byteplus-mediakit-cli video extract-frames --schema
```

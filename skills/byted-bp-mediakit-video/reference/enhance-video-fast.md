# enhance-video-fast

## Capability

Uses a speed-first algorithm combining lightweight super-resolution and intelligent quality enhancement to balance efficiency and visual quality.

## Parameter rules

- Optional parameters may be filled only when the user specifies them, or when they can be determined accurately from user intent; do not invent values. Omit them when uncertain; ask the user if they are required to complete the task.
- This capability is the speed-first enhancement path. Route here only when the user names speed, fast, quickly, low-latency, or “quick enhance”. Unqualified “enhance” / “enhance quality” requests go to enhance-video instead. Do not ask the user to choose between the two paths.

## Cloud

### Command and lifecycle

- Command: `byteplus-mediakit-cli video enhance-video-fast`
- Lifecycle: async
- Return: returns `task_id`; query the terminal result next.

### Parameters

| Path | CLI flag | Type | Required | Default | Enum/range | Description |
| --- | --- | --- | --- | --- | --- | --- |
| `bitrate_level` | `--bitrate-level` | string | no | "medium" | enum: ["low","medium","high"] | This parameter is optional and controls the output video's average bitrate and affects visual quality and final file size. It supports low, medium, and high, and defaults to medium. The actual bitrate is automatically calculated using target resolution and frame rate. |
| `callback_args` | `--callback-args` | string | no | - | - | Custom callback argument returned unchanged when the task finishes, used to correlate business records; maximum 512 bytes. Pass only when the user provides this value; do not generate, infer, or fill it. |
| `callback_url` | `--callback-url` | string | no | - | - | Callback URL that receives this task result; when set it takes priority over the global callback URL and must start with http:// or https://. Pass only when the user provides this value; do not generate, infer, or fill it. |
| `client_token` | `--client-token` | string | no | - | - | Idempotency token; case-sensitive, at most 64 printable ASCII characters. Omit by default. Reuse the same token for retries of the same logical request. Do not mint a new token for each attempt. The CLI does not infer retry intent or auto-generate tokens. Omit by default. Use the user-provided value as-is when specified. When the user explicitly retries, the same logical request must reuse the same token. If a token already exists, reuse it; if earlier requests had no token, you may create one starting with this retry and keep reusing it, but that token does not provide retroactive idempotency for earlier requests. A change in business parameters is a new request and must not reuse the old token. Do not mint a different value for each attempt. The CLI runtime does not infer retry intent and does not auto-generate tokens. |
| `fps` | `--fps` | number | no | - | min: 15; max: 120 | This parameter is optional and sets the target frame rate, in fps. Its inclusive minimum is 15 and its inclusive maximum is 120. The target frame rate is recommended not to exceed 4x the source frame rate. If fps is omitted, the output retains the source video's frame rate. |
| `queue_id` | `--queue-id` | string | no | - | - | Target queue ID for the submitted task. Omit to use the system default queue. Pass only when the user provides this value; do not generate, infer, or fill it. |
| `resolution` | `--resolution` | string | no | - | enum: ["240p","360p","480p","540p","720p","1080p","2k","4k"] | This parameter is optional and sets the target resolution level, and it can upscale the video to a specified level. It supports 240p, 360p, 480p, 540p, 720p, 1080p, 2k, and 4k. This parameter and resolution_limit are mutually exclusive and cannot be configured together. |
| `resolution_limit` | `--resolution-limit` | integer | no | - | min: 128; max: 2160 | This parameter is optional and sets the target resolution's short-side pixel limit. Based on it, the system scales the video proportionally while preserving its original aspect ratio. It has an inclusive minimum of 128 and an inclusive maximum of 2160. This parameter and resolution are mutually exclusive and cannot be configured together. |
| `video_url` | `--video-url` | string | yes | - | - | This required parameter is the URL of the video to be enhanced. Only publicly accessible HTTP or HTTPS video URLs are supported. Common supported video formats include mp4, flv, ts, avi, mov, wmv, and mkv. The input video's long side must be in the inclusive range 360 to 2560, and its short side must be in the inclusive range 360 to 1440. Input resolution up to 2K is supported. The input file size is recommended not to exceed 10 GB. |

### Example

```bash
MEDIAKIT_BYTEPLUS_SURFACE=skill MEDIAKIT_BYTEPLUS_RUNTIME=<runtime> \
  byteplus-mediakit-cli video enhance-video-fast \
  --video-url <video_url>
```

Replace placeholders with real user input only. Optional flags follow the parameter rules; do not invent URLs, files, enums, or business parameters.

### Result

| Path | Type | Required | Mode | Description |
| --- | --- | --- | --- | --- |
| `request_id` | string | no | Cloud | Request id returned by the service; forwarded only when non-empty. |
| `task_id` | string | yes | Cloud | Unique async task id used to query status and the terminal result. |
| `task_type` | string | no | Cloud | Task type returned by the service; forwarded only when non-empty. |
| `result.duration` | number | no | Cloud terminal | The duration of the video. |
| `result.fps` | number | no | Cloud terminal | The frame rate of the video. |
| `result.resolution` | string | no | Cloud terminal | Supports 240p, 360p, 480p, 540p, 720p, 1080p, 2k, and 4k. |
| `result.video_url` | string | no | Cloud terminal | An HTTPS temporary download link for the result, valid for 24 hours by default. Please download the result within this validity period. |

After a successful Cloud call, read `task_id` and query the terminal result:

```bash
MEDIAKIT_BYTEPLUS_SURFACE=skill MEDIAKIT_BYTEPLUS_RUNTIME=<runtime> \
  byteplus-mediakit-cli shared query-task --task-id <task_id> --poll-complete
```

### Machine contract

These commands read live help/schema only and do not call Cloud:

```bash
byteplus-mediakit-cli video enhance-video-fast --help
byteplus-mediakit-cli video enhance-video-fast --schema
```

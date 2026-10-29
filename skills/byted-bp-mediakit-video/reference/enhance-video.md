# enhance-video

## Capability

Enhances video quality using AI algorithms for denoising, color enhancement, artifact removal, and super-resolution.

## Parameter rules

- Optional parameters may be filled only when the user specifies them, or when they can be determined accurately from user intent; do not invent values. Omit them when uncertain; ask the user if they are required to complete the task.
- This capability is the quality-first enhancement path. Route here when the user asks to enhance / improve image quality without naming speed, fast, quickly, or low-latency. Do not ask the user to choose between enhance-video and enhance-video-fast — default to this tool for unqualified enhance requests.

## Cloud

### Command and lifecycle

- Command: `byteplus-mediakit-cli video enhance-video`
- Lifecycle: async
- Return: returns `task_id`; query the terminal result next.

### Parameters

| Path | CLI flag | Type | Required | Default | Enum/range | Description |
| --- | --- | --- | --- | --- | --- | --- |
| `bit_depth` | `--bit-depth` | integer | no | 8 | enum: [8,10,12] | Sets the target bit depth, also known as color depth. This parameter is optional and defaults to 8. It takes effect only for the Professional version, which applies when tool_version is professional. At bit depth 8, each color channel provides 2⁸ (256) levels and output uses H.264. At bit depth 10, each color channel provides 2¹⁰ (1,024) levels and output uses H.265. At bit depth 12, each color channel provides 2¹² (4,096) levels. At bit depth 16, each color channel provides 2¹⁶ (65,536) levels and output uses H.265. For 16-bit depth, input duration must not exceed 40 seconds. 16-bit depth processing runs serially, one at a time, and may take longer. At 16-bit depth, the bitrate is system-determined, and bitrate and bitrate_level do not take effect. |
| `bitrate_level` | `--bitrate-level` | string | no | "medium" | enum: ["low","medium","high"] | Controls the average bitrate of the output video, affecting visual quality and final file size. This parameter is optional and defaults to medium. It can be set to low, medium, or high, where low selects a low bitrate, medium selects the recommended medium bitrate, and high selects a high bitrate. The actual bitrate is automatically calculated from the target resolution and frame rate. The documented low, medium, and high target output bitrates in Mbps are defined by output resolution and frame-rate bands from 15 through 120 fps. The actual output bitrate is typically 0.8 to 1.5 times the listed target because the system dynamically adjusts for content complexity. |
| `callback_args` | `--callback-args` | string | no | - | - | Custom callback argument returned unchanged when the task finishes, used to correlate business records; maximum 512 bytes. Pass only when the user provides this value; do not generate, infer, or fill it. |
| `callback_url` | `--callback-url` | string | no | - | - | Callback URL that receives this task result; when set it takes priority over the global callback URL and must start with http:// or https://. Pass only when the user provides this value; do not generate, infer, or fill it. |
| `client_token` | `--client-token` | string | no | - | - | Idempotency token; case-sensitive, at most 64 printable ASCII characters. Omit by default. Reuse the same token for retries of the same logical request. Do not mint a new token for each attempt. The CLI does not infer retry intent or auto-generate tokens. Omit by default. Use the user-provided value as-is when specified. When the user explicitly retries, the same logical request must reuse the same token. If a token already exists, reuse it; if earlier requests had no token, you may create one starting with this retry and keep reusing it, but that token does not provide retroactive idempotency for earlier requests. A change in business parameters is a new request and must not reuse the old token. Do not mint a different value for each attempt. The CLI runtime does not infer retry intent and does not auto-generate tokens. |
| `fps` | `--fps` | number | no | - | min: 15; max: 120 | Specifies the target frame rate, in frames per second. This parameter is optional; when it is not specified, the output preserves the source frame rate. The value ranges from a minimum of 15 to a maximum of 120, and we recommend that it not exceed 4 times the source frame rate. |
| `queue_id` | `--queue-id` | string | no | - | - | Target queue ID for the submitted task. Omit to use the system default queue. Pass only when the user provides this value; do not generate, infer, or fill it. |
| `resolution` | `--resolution` | string | no | - | enum: ["240p","360p","480p","540p","720p","1080p","2k","4k","8k"] | Specifies the target resolution level used to upscale the video. This parameter is optional and supports 240p, 360p, 480p, 540p, 720p, 1080p, 2k, 4k, or 8k. It cannot be configured together with resolution_limit, as the two are mutually exclusive. |
| `resolution_limit` | `--resolution-limit` | integer | no | - | min: 128; max: 4320 | Sets the short-side pixel limit for the target resolution. The system scales the video proportionally to this limit while preserving the original aspect ratio. This parameter is optional, with values ranging from a minimum of 128 to a maximum of 4320. It cannot be configured together with resolution, as the two are mutually exclusive. |
| `scene` | `--scene` | string | no | "common" | enum: ["common","ugc","short_series","aigc","old_film"] | Selects a preset enhancement template for a specific business scenario. This parameter is optional and defaults to common. It supports common for general-purpose content, ugc for UGC short video, short_series for short-form drama series, aigc for AI-generated content, and old_film for old film restoration. This setting takes effect only when tool_version is standard. |
| `tool_version` | `--tool-version` | string | no | "standard" | enum: ["standard","professional"] | Optional. It determines the enhancement algorithms' strength and scope and the billing. It defaults to standard and can be set to standard or professional. The standard version balances processing speed and video quality, includes over 10 frequently used enhancement algorithms, meets mainstream playback platform quality requirements, and suits video distribution quality improvement. The professional version provides ultimate quality enhancement with over 30 built-in algorithms, ensures shot-level visual effects, and suits cinema-grade video production. |
| `video_url` | `--video-url` | string | yes | - | - | The URL of the video to be enhanced. This parameter is required. It supports only publicly accessible HTTP/HTTPS URLs. Supported input video formats include mp4, flv, ts, avi, mov, wmv, and mkv. Input video resolution supports up to 2K. It is recommended that a single input file not exceed 10 GB. |

### Example

```bash
MEDIAKIT_BYTEPLUS_SURFACE=skill MEDIAKIT_BYTEPLUS_RUNTIME=<runtime> \
  byteplus-mediakit-cli video enhance-video \
  --video-url <video_url>
```

Replace placeholders with real user input only. Optional flags follow the parameter rules; do not invent URLs, files, enums, or business parameters.

### Result

| Path | Type | Required | Mode | Description |
| --- | --- | --- | --- | --- |
| `request_id` | string | no | Cloud | Request id returned by the service; forwarded only when non-empty. |
| `task_id` | string | yes | Cloud | Unique async task id used to query status and the terminal result. |
| `task_type` | string | no | Cloud | Task type returned by the service; forwarded only when non-empty. |
| `result.duration` | number | no | Cloud terminal | The total duration of the output video, in seconds. |
| `result.fps` | number | no | Cloud terminal | The frame rate of the output video. |
| `result.resolution` | string | no | Cloud terminal | The resolution of the output video. |
| `result.tool_version` | string | no | Cloud terminal | The tool version actually used for the task. |
| `result.video_url` | string | no | Cloud terminal | The download URL of the enhanced video. Please be sure to save it in time, as this URL remains valid for 24 hours. |

After a successful Cloud call, read `task_id` and query the terminal result:

```bash
MEDIAKIT_BYTEPLUS_SURFACE=skill MEDIAKIT_BYTEPLUS_RUNTIME=<runtime> \
  byteplus-mediakit-cli shared query-task --task-id <task_id> --poll-complete
```

### Machine contract

These commands read live help/schema only and do not call Cloud:

```bash
byteplus-mediakit-cli video enhance-video --help
byteplus-mediakit-cli video enhance-video --schema
```

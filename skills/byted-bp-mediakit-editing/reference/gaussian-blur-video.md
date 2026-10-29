# gaussian-blur-video

## Capability

This tool can apply Gaussian blur to specific rectangular regions within defined time ranges. It can programmatically mask sensitive information or unwanted elements in a video. Common uses include obscuring faces, watermarks, or logos. The processed video is returned in MP4 format.

## Parameter rules

- Optional parameters may be filled only when the user specifies them, or when they can be determined accurately from user intent; do not invent values. Omit them when uncertain; ask the user if they are required to complete the task.

## Cloud

### Command and lifecycle

- Command: `byteplus-mediakit-cli editing gaussian-blur-video`
- Lifecycle: async
- Return: returns `task_id`; query the terminal result next.

### Usage guide

- Object or object array parameters (`--blur-regions`) must be passed as a valid JSON string wrapped in single quotes, for example `--blur-regions '[{...}]'`; field names and nesting must match the “Enum/range” column and nested field descriptions in the Parameters table below.
- Do not pass comma-separated values or plain text for these parameters.

### Example

```bash
MEDIAKIT_BYTEPLUS_SURFACE=skill MEDIAKIT_BYTEPLUS_RUNTIME=<runtime> \
  byteplus-mediakit-cli editing gaussian-blur-video \
  --video-url <video_url> \
  --blur-regions <blur_regions>
```

Replace placeholders with real user input only. Optional flags follow the parameter rules; do not invent URLs, files, enums, or business parameters.

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
| `client_token` | `--client-token` | string | no | - | - | Idempotency token; case-sensitive, at most 64 printable ASCII characters. Omit by default. Reuse the same token for retries of the same logical request. Do not mint a new token for each attempt. The CLI does not infer retry intent or auto-generate tokens. Omit by default. Use the user-provided value as-is when specified. When the user explicitly retries, the same logical request must reuse the same token. If a token already exists, reuse it; if earlier requests had no token, you may create one starting with this retry and keep reusing it, but that token does not provide retroactive idempotency for earlier requests. A change in business parameters is a new request and must not reuse the old token. Do not mint a different value for each attempt. The CLI runtime does not infer retry intent and does not auto-generate tokens. |
| `queue_id` | `--queue-id` | string | no | - | - | Target queue ID for the submitted task. Omit to use the system default queue. Pass only when the user provides this value; do not generate, infer, or fill it. |
| `video_url` | `--video-url` | string | yes | - | - | The URL of the video to be processed. This parameter is required. Only publicly accessible HTTP/HTTPS URLs are supported as file sources. Common supported video formats include mp4, flv, ts, avi, mov, wmv, and mkv. Input resolution up to 4K (3840×2160) is supported. |

### Result

| Path | Type | Required | Mode | Description |
| --- | --- | --- | --- | --- |
| `request_id` | string | no | Cloud | Request id returned by the service; forwarded only when non-empty. |
| `task_id` | string | yes | Cloud | Unique async task id used to query status and the terminal result. |
| `task_type` | string | no | Cloud | Task type returned by the service; forwarded only when non-empty. |
| `result.duration` | number | no | Cloud terminal | The duration of the output video. |
| `result.resolution` | string | no | Cloud terminal | The resolution of the output video, such as 720p, 1080p, 2k, and 4k. |
| `result.video_url` | string | no | Cloud terminal | An HTTPS temporary download link for the output video, valid for 24 hours by default. |

After a successful Cloud call, read `task_id` and query the terminal result:

```bash
MEDIAKIT_BYTEPLUS_SURFACE=skill MEDIAKIT_BYTEPLUS_RUNTIME=<runtime> \
  byteplus-mediakit-cli shared query-task --task-id <task_id> --poll-complete
```

### Machine contract

These commands read live help/schema only and do not call Cloud:

```bash
byteplus-mediakit-cli editing gaussian-blur-video --help
byteplus-mediakit-cli editing gaussian-blur-video --schema
```

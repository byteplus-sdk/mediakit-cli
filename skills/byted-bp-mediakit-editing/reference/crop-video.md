# crop-video

## Capability

Crops a video to a specified rectangular region and can return a new video containing only that region.

## Parameter rules

- Optional parameters may be filled only when the user specifies them, or when they can be determined accurately from user intent; do not invent values. Omit them when uncertain; ask the user if they are required to complete the task.
- bottom_right_x/y must be greater than top_left_x/y. If the stated rectangle violates that contract, ask; do not swap, clamp, or submit. If you already asked and the user has not confirmed a valid rectangle, stop.

## Cloud

### Command and lifecycle

- Command: `byteplus-mediakit-cli editing crop-video`
- Lifecycle: async
- Return: returns `task_id`; query the terminal result next.

### Parameters

| Path | CLI flag | Type | Required | Default | Enum/range | Description |
| --- | --- | --- | --- | --- | --- | --- |
| `bottom_right_x` | `--bottom-right-x` | integer | yes | - | min: 1 | The X coordinate of the bottom-right corner of the crop rectangle, in pixels. This value is required and must be greater than top_left_x, and the difference between them must be at least 16 px. We recommend that the crop rectangle not exceed the original frame boundaries; if bottom_right_x exceeds the video width or bottom_right_y exceeds the video height, the corresponding cropped dimension is truncated to the frame boundary. |
| `bottom_right_y` | `--bottom-right-y` | integer | yes | - | min: 1 | The Y coordinate, in pixels, of the bottom-right corner of the crop rectangle. This parameter is required and must be greater than top_left_y, and the difference between bottom_right_y and top_left_y must be at least 16px. We recommend that the crop rectangle not exceed the original frame boundaries. If bottom_right_x exceeds the video width or bottom_right_y exceeds the video height, the corresponding cropped dimension is truncated to the frame boundary. |
| `callback_args` | `--callback-args` | string | no | - | - | Custom callback argument returned unchanged when the task finishes, used to correlate business records; maximum 512 bytes. Pass only when the user provides this value; do not generate, infer, or fill it. |
| `callback_url` | `--callback-url` | string | no | - | - | Callback URL that receives this task result; when set it takes priority over the global callback URL and must start with http:// or https://. Pass only when the user provides this value; do not generate, infer, or fill it. |
| `client_token` | `--client-token` | string | no | - | - | Idempotency token; case-sensitive, at most 64 printable ASCII characters. Omit by default. Reuse the same token for retries of the same logical request. Do not mint a new token for each attempt. The CLI does not infer retry intent or auto-generate tokens. Omit by default. Use the user-provided value as-is when specified. When the user explicitly retries, the same logical request must reuse the same token. If a token already exists, reuse it; if earlier requests had no token, you may create one starting with this retry and keep reusing it, but that token does not provide retroactive idempotency for earlier requests. A change in business parameters is a new request and must not reuse the old token. Do not mint a different value for each attempt. The CLI runtime does not infer retry intent and does not auto-generate tokens. |
| `queue_id` | `--queue-id` | string | no | - | - | Target queue ID for the submitted task. Omit to use the system default queue. Pass only when the user provides this value; do not generate, infer, or fill it. |
| `top_left_x` | `--top-left-x` | integer | yes | - | min: 0 | The horizontal X coordinate, in pixels, of the top-left corner of the crop rectangle. This parameter is required and must be a non-negative integer. |
| `top_left_y` | `--top-left-y` | integer | yes | - | min: 0 | The vertical Y coordinate, in pixels, of the top-left corner of the crop rectangle. This parameter is required and must be a non-negative integer. |
| `video_url` | `--video-url` | string | yes | - | - | The URL of the video to be cropped. This parameter is required. Only publicly accessible HTTP/HTTPS URLs are supported as file sources. Commonly supported video formats include mp4, flv, ts, avi, mov, wmv, and mkv. Input resolution up to 4K (3840×2160) is supported. It is recommended that the input file size not exceed 10 GB. |

### Example

```bash
MEDIAKIT_BYTEPLUS_SURFACE=skill MEDIAKIT_BYTEPLUS_RUNTIME=<runtime> \
  byteplus-mediakit-cli editing crop-video \
  --video-url <video_url> \
  --top-left-x <top_left_x> \
  --top-left-y <top_left_y> \
  --bottom-right-x <bottom_right_x> \
  --bottom-right-y <bottom_right_y>
```

Replace placeholders with real user input only. Optional flags follow the parameter rules; do not invent URLs, files, enums, or business parameters.

### Result

| Path | Type | Required | Mode | Description |
| --- | --- | --- | --- | --- |
| `request_id` | string | no | Cloud | Request id returned by the service; forwarded only when non-empty. |
| `task_id` | string | yes | Cloud | Unique async task id used to query status and the terminal result. |
| `task_type` | string | no | Cloud | Task type returned by the service; forwarded only when non-empty. |
| `result.duration` | number | no | Cloud terminal | The total duration of the output video, in seconds. |
| `result.resolution` | string | no | Cloud terminal | The resolution of the output video, which can be 240p, 360p, 480p, 540p, 720p, 1080p, 2k, or 4k. |
| `result.video_url` | string | no | Cloud terminal | The URL of the generated cropped video file in MP4 format, provided as an HTTPS temporary download link that is valid for 24 hours by default. |

After a successful Cloud call, read `task_id` and query the terminal result:

```bash
MEDIAKIT_BYTEPLUS_SURFACE=skill MEDIAKIT_BYTEPLUS_RUNTIME=<runtime> \
  byteplus-mediakit-cli shared query-task --task-id <task_id> --poll-complete
```

### Machine contract

These commands read live help/schema only and do not call Cloud:

```bash
byteplus-mediakit-cli editing crop-video --help
byteplus-mediakit-cli editing crop-video --schema
```

# flip-video

## Capability

Mirrors a specified video vertically or horizontally.

## Parameter rules

- Optional parameters may be filled only when the user specifies them, or when they can be determined accurately from user intent; do not invent values. Omit them when uncertain; ask the user if they are required to complete the task.

## Cloud

### Command and lifecycle

- Command: `byteplus-mediakit-cli editing flip-video`
- Lifecycle: async
- Return: returns `task_id`; query the terminal result next.

### Usage guide

- Boolean parameters (`--is-flip-horizontal`, `--is-flip-vertical`) must be written as `--flag=true` or `--flag=false`, or as bare `--flag` (equivalent to true). Do not pass a space-separated value such as `--flag true`, which the CLI treats as a positional argument.
- Omit boolean flags when they would repeat the default value.

### Example

```bash
MEDIAKIT_BYTEPLUS_SURFACE=skill MEDIAKIT_BYTEPLUS_RUNTIME=<runtime> \
  byteplus-mediakit-cli editing flip-video \
  --video-url <video_url>
```

Replace placeholders with real user input only. Optional flags follow the parameter rules; do not invent URLs, files, enums, or business parameters.

### Parameters

| Path | CLI flag | Type | Required | Default | Enum/range | Description |
| --- | --- | --- | --- | --- | --- | --- |
| `callback_args` | `--callback-args` | string | no | - | - | Custom callback argument returned unchanged when the task finishes, used to correlate business records; maximum 512 bytes. Pass only when the user provides this value; do not generate, infer, or fill it. |
| `callback_url` | `--callback-url` | string | no | - | - | Callback URL that receives this task result; when set it takes priority over the global callback URL and must start with http:// or https://. Pass only when the user provides this value; do not generate, infer, or fill it. |
| `client_token` | `--client-token` | string | no | - | - | Idempotency token; case-sensitive, at most 64 printable ASCII characters. Omit by default. Reuse the same token for retries of the same logical request. Do not mint a new token for each attempt. The CLI does not infer retry intent or auto-generate tokens. Omit by default. Use the user-provided value as-is when specified. When the user explicitly retries, the same logical request must reuse the same token. If a token already exists, reuse it; if earlier requests had no token, you may create one starting with this retry and keep reusing it, but that token does not provide retroactive idempotency for earlier requests. A change in business parameters is a new request and must not reuse the old token. Do not mint a different value for each attempt. The CLI runtime does not infer retry intent and does not auto-generate tokens. |
| `is_flip_horizontal` | `--is-flip-horizontal` | boolean | no | false | - | Optional. Controls whether the video is flipped horizontally from left to right. Defaults to false. |
| `is_flip_vertical` | `--is-flip-vertical` | boolean | no | false | - | Optional. Controls whether the video is flipped vertically from top to bottom. Defaults to false. At least one of is_flip_vertical and is_flip_horizontal must be set to true. If neither is set to true, the processed video is identical to the source. When both are set to true, the effect is equivalent to rotating the video 180 degrees. |
| `queue_id` | `--queue-id` | string | no | - | - | Target queue ID for the submitted task. Omit to use the system default queue. Pass only when the user provides this value; do not generate, infer, or fill it. |
| `video_url` | `--video-url` | string | yes | - | - | The URL of the video to be flipped. This parameter is required. Only publicly accessible HTTP/HTTPS URLs are supported as the file source. Supported inputs include common video formats such as mp4, flv, ts, avi, mov, wmv, and mkv. It is recommended that the input file size not exceed 10 GB. Input resolution up to 4K (3840×2160) is supported. |

### Result

| Path | Type | Required | Mode | Description |
| --- | --- | --- | --- | --- |
| `request_id` | string | no | Cloud | Request id returned by the service; forwarded only when non-empty. |
| `task_id` | string | yes | Cloud | Unique async task id used to query status and the terminal result. |
| `task_type` | string | no | Cloud | Task type returned by the service; forwarded only when non-empty. |
| `result.duration` | number | no | Cloud terminal | The total duration of the output video, measured in seconds. |
| `result.resolution` | string | no | Cloud terminal | The resolution of the output video, which can be 240p, 360p, 480p, 540p, 720p, 1080p, 2k, or 4k. |
| `result.video_url` | string | no | Cloud terminal | The HTTPS temporary download link for the generated flipped video file, which is in MP4 format. The link is valid for 24 hours by default, and the file should be downloaded and saved promptly. |

After a successful Cloud call, read `task_id` and query the terminal result:

```bash
MEDIAKIT_BYTEPLUS_SURFACE=skill MEDIAKIT_BYTEPLUS_RUNTIME=<runtime> \
  byteplus-mediakit-cli shared query-task --task-id <task_id> --poll-complete
```

### Machine contract

These commands read live help/schema only and do not call Cloud:

```bash
byteplus-mediakit-cli editing flip-video --help
byteplus-mediakit-cli editing flip-video --schema
```

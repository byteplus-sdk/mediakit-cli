# video-frame-interpolation

## Capability

Uses deep learning to intelligently synthesize new frames and increase the video's frame rate to a specified target. This is particularly effective for enhancing the viewing experience of low-frame-rate, fast-motion, or large-screen videos.

## Parameter rules

- Optional parameters may be filled only when the user specifies them, or when they can be determined accurately from user intent; do not invent values. Omit them when uncertain; ask the user if they are required to complete the task.

## Cloud

### Command and lifecycle

- Command: `byteplus-mediakit-cli video video-frame-interpolation`
- Lifecycle: async
- Return: returns `task_id`; query the terminal result next.

### Parameters

| Path | CLI flag | Type | Required | Default | Enum/range | Description |
| --- | --- | --- | --- | --- | --- | --- |
| `callback_args` | `--callback-args` | string | no | - | - | Custom callback argument returned unchanged when the task finishes, used to correlate business records; maximum 512 bytes. Pass only when the user provides this value; do not generate, infer, or fill it. |
| `callback_url` | `--callback-url` | string | no | - | - | Callback URL that receives this task result; when set it takes priority over the global callback URL and must start with http:// or https://. Pass only when the user provides this value; do not generate, infer, or fill it. |
| `client_token` | `--client-token` | string | no | - | - | Idempotency token; case-sensitive, at most 64 printable ASCII characters. Omit by default. Reuse the same token for retries of the same logical request. Do not mint a new token for each attempt. The CLI does not infer retry intent or auto-generate tokens. Omit by default. Use the user-provided value as-is when specified. When the user explicitly retries, the same logical request must reuse the same token. If a token already exists, reuse it; if earlier requests had no token, you may create one starting with this retry and keep reusing it, but that token does not provide retroactive idempotency for earlier requests. A change in business parameters is a new request and must not reuse the old token. Do not mint a different value for each attempt. The CLI runtime does not infer retry intent and does not auto-generate tokens. |
| `fps` | `--fps` | number | yes | - | min: 15; max: 120 | This parameter specifies the target output frame rate, measured in fps, and is required. The minimum value is 15 and the maximum value is 120. For a more natural interpolation result, the target frame rate is recommended not to exceed 4x the source frame rate. If the target frame rate is lower than the source frame rate, the system first performs intelligent interpolation and then downsamples to reach the final target frame rate. |
| `queue_id` | `--queue-id` | string | no | - | - | Target queue ID for the submitted task. Omit to use the system default queue. Pass only when the user provides this value; do not generate, infer, or fill it. |
| `video_url` | `--video-url` | string | yes | - | - | This parameter is the URL of the video to be interpolated and is required. Only publicly accessible HTTP/HTTPS URLs are supported. Common supported video formats include mp4, flv, ts, avi, mov, wmv, and mkv. |

### Example

```bash
MEDIAKIT_BYTEPLUS_SURFACE=skill MEDIAKIT_BYTEPLUS_RUNTIME=<runtime> \
  byteplus-mediakit-cli video video-frame-interpolation \
  --video-url <video_url> \
  --fps <fps>
```

Replace placeholders with real user input only. Optional flags follow the parameter rules; do not invent URLs, files, enums, or business parameters.

### Result

| Path | Type | Required | Mode | Description |
| --- | --- | --- | --- | --- |
| `request_id` | string | no | Cloud | Request id returned by the service; forwarded only when non-empty. |
| `task_id` | string | yes | Cloud | Unique async task id used to query status and the terminal result. |
| `task_type` | string | no | Cloud | Task type returned by the service; forwarded only when non-empty. |
| `result.duration` | number | no | Cloud terminal | The duration of the output video, measured in seconds, used for metering and billing. |
| `result.fps` | number | no | Cloud terminal | The frame rate of the output video, which matches the target frame rate specified at submission. |
| `result.resolution` | string | no | Cloud terminal | The resolution level of the output video. Interpolation does not change the resolution, so the output resolution stays the same as the source. Possible values are 240p, 360p, 480p, 540p, 720p, 1080p, 2k, 4k, and 8k. |
| `result.video_url` | string | no | Cloud terminal | The URL of the interpolated video file, provided as an HTTPS temporary download link that is valid for 24 hours by default. |

After a successful Cloud call, read `task_id` and query the terminal result:

```bash
MEDIAKIT_BYTEPLUS_SURFACE=skill MEDIAKIT_BYTEPLUS_RUNTIME=<runtime> \
  byteplus-mediakit-cli shared query-task --task-id <task_id> --poll-complete
```

### Machine contract

These commands read live help/schema only and do not call Cloud:

```bash
byteplus-mediakit-cli video video-frame-interpolation --help
byteplus-mediakit-cli video video-frame-interpolation --schema
```

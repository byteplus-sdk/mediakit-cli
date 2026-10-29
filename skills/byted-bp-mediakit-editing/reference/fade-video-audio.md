# fade-video-audio

## Capability

It modifies the audio component without altering the video stream. The processing provides a smoother listening experience. The output has the same resolution as the source. Using different queues is the primary way to allocate costs to specific projects.

## Parameter rules

- Optional parameters may be filled only when the user specifies them, or when they can be determined accurately from user intent; do not invent values. Omit them when uncertain; ask the user if they are required to complete the task.

## Cloud

### Command and lifecycle

- Command: `byteplus-mediakit-cli editing fade-video-audio`
- Lifecycle: async
- Return: returns `task_id`; query the terminal result next.

### Parameters

| Path | CLI flag | Type | Required | Default | Enum/range | Description |
| --- | --- | --- | --- | --- | --- | --- |
| `callback_args` | `--callback-args` | string | no | - | - | Custom callback argument returned unchanged when the task finishes, used to correlate business records; maximum 512 bytes. Pass only when the user provides this value; do not generate, infer, or fill it. |
| `callback_url` | `--callback-url` | string | no | - | - | Callback URL that receives this task result; when set it takes priority over the global callback URL and must start with http:// or https://. Pass only when the user provides this value; do not generate, infer, or fill it. |
| `client_token` | `--client-token` | string | no | - | - | Idempotency token; case-sensitive, at most 64 printable ASCII characters. Omit by default. Reuse the same token for retries of the same logical request. Do not mint a new token for each attempt. The CLI does not infer retry intent or auto-generate tokens. Omit by default. Use the user-provided value as-is when specified. When the user explicitly retries, the same logical request must reuse the same token. If a token already exists, reuse it; if earlier requests had no token, you may create one starting with this retry and keep reusing it, but that token does not provide retroactive idempotency for earlier requests. A change in business parameters is a new request and must not reuse the old token. Do not mint a different value for each attempt. The CLI runtime does not infer retry intent and does not auto-generate tokens. |
| `fade_in_duration` | `--fade-in-duration` | number | no | 1 | min: 0 | Controls the audio fade-in duration, in seconds. This parameter is optional and supports up to 3 decimal places. If omitted, its default value is 1, applying a 1-second fade-in. A value of 0 means no fade-in is applied. |
| `fade_out_duration` | `--fade-out-duration` | number | no | 1 | min: 0 | Optional. Controls the audio fade-out duration, measured in seconds, and supports up to 3 decimal places. If fade_out_duration is omitted, its default value is 1, applying a 1-second fade-out. A value of 0 means no fade-out is applied. |
| `queue_id` | `--queue-id` | string | no | - | - | Target queue ID for the submitted task. Omit to use the system default queue. Pass only when the user provides this value; do not generate, infer, or fill it. |
| `video_url` | `--video-url` | string | yes | - | - | The URL of the video to be processed. This parameter is required. Only publicly accessible HTTP/HTTPS URLs are supported as file sources. Common supported video formats include mp4, flv, ts, avi, mov, wmv, and mkv, and input resolution up to 4K (3840×2160) is supported. The input file size is recommended not to exceed 10 GB. |

### Example

```bash
MEDIAKIT_BYTEPLUS_SURFACE=skill MEDIAKIT_BYTEPLUS_RUNTIME=<runtime> \
  byteplus-mediakit-cli editing fade-video-audio \
  --video-url <video_url>
```

Replace placeholders with real user input only. Optional flags follow the parameter rules; do not invent URLs, files, enums, or business parameters.

### Result

| Path | Type | Required | Mode | Description |
| --- | --- | --- | --- | --- |
| `request_id` | string | no | Cloud | Request id returned by the service; forwarded only when non-empty. |
| `task_id` | string | yes | Cloud | Unique async task id used to query status and the terminal result. |
| `task_type` | string | no | Cloud | Task type returned by the service; forwarded only when non-empty. |
| `result.duration` | number | no | Cloud terminal | The total duration of the output video, measured in seconds. |
| `result.resolution` | string | no | Cloud terminal | The resolution of the output video, which is the same as the source video. Example values of the output resolution include 720p, 1080p, 2k, and 4k. |
| `result.video_url` | string | no | Cloud terminal | The URL of the processed video file, in MP4 format. It is an HTTPS temporary download link that is valid for 24 hours by default. The processed file should be downloaded and saved promptly. |

After a successful Cloud call, read `task_id` and query the terminal result:

```bash
MEDIAKIT_BYTEPLUS_SURFACE=skill MEDIAKIT_BYTEPLUS_RUNTIME=<runtime> \
  byteplus-mediakit-cli shared query-task --task-id <task_id> --poll-complete
```

### Machine contract

These commands read live help/schema only and do not call Cloud:

```bash
byteplus-mediakit-cli editing fade-video-audio --help
byteplus-mediakit-cli editing fade-video-audio --schema
```

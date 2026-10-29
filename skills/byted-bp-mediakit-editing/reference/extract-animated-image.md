# extract-animated-image

## Capability

Can create an animated image in GIF or WebP format from a specific segment of a video. It is ideal for generating cover animations, marketing materials, and short previews.

## Parameter rules

- Optional parameters may be filled only when the user specifies them, or when they can be determined accurately from user intent; do not invent values. Omit them when uncertain; ask the user if they are required to complete the task.
- Omit output_format/format unless the user names a documented format. Matching the source container or file extension is inventing. If the user names a documented format, pass it even when it equals the default. After a call succeeds or is intercepted, do not retry the same tool with an invented format.

## Cloud

### Command and lifecycle

- Command: `byteplus-mediakit-cli editing extract-animated-image`
- Lifecycle: async
- Return: returns `task_id`; query the terminal result next.

### Parameters

| Path | CLI flag | Type | Required | Default | Enum/range | Description |
| --- | --- | --- | --- | --- | --- | --- |
| `callback_args` | `--callback-args` | string | no | - | - | Custom callback argument returned unchanged when the task finishes, used to correlate business records; maximum 512 bytes. Pass only when the user provides this value; do not generate, infer, or fill it. |
| `callback_url` | `--callback-url` | string | no | - | - | Callback URL that receives this task result; when set it takes priority over the global callback URL and must start with http:// or https://. Pass only when the user provides this value; do not generate, infer, or fill it. |
| `client_token` | `--client-token` | string | no | - | - | Idempotency token; case-sensitive, at most 64 printable ASCII characters. Omit by default. Reuse the same token for retries of the same logical request. Do not mint a new token for each attempt. The CLI does not infer retry intent or auto-generate tokens. Omit by default. Use the user-provided value as-is when specified. When the user explicitly retries, the same logical request must reuse the same token. If a token already exists, reuse it; if earlier requests had no token, you may create one starting with this retry and keep reusing it, but that token does not provide retroactive idempotency for earlier requests. A change in business parameters is a new request and must not reuse the old token. Do not mint a different value for each attempt. The CLI runtime does not infer retry intent and does not auto-generate tokens. |
| `end_time` | `--end-time` | number | yes | - | min: 0 | The end time of the segment to extract, measured in seconds. It supports up to 3 decimal places. This parameter is required and must be greater than start_time. The output animated image can have a maximum duration of 60 seconds. |
| `output_format` | `--output-format` | string | no | "gif" | enum: ["gif","webp"] | Selects the output format of the animated image. This parameter supports gif and webp. It is optional and defaults to gif. |
| `queue_id` | `--queue-id` | string | no | - | - | Target queue ID for the submitted task. Omit to use the system default queue. Pass only when the user provides this value; do not generate, infer, or fill it. |
| `start_time` | `--start-time` | number | yes | 0 | min: 0 | The start time of the segment to extract, measured in seconds, and supports up to 3 decimal places. This field is required. When set to 0, which is the default value, extraction starts at the beginning of the video. |
| `video_url` | `--video-url` | string | yes | - | - | The URL of the video from which the animated image is extracted. This field is required and supports only publicly accessible HTTP or HTTPS URLs. Commonly supported video formats include mp4, flv, ts, avi, mov, wmv, and mkv, and the input video resolution is supported up to 4K (3840×2160). |

### Example

```bash
MEDIAKIT_BYTEPLUS_SURFACE=skill MEDIAKIT_BYTEPLUS_RUNTIME=<runtime> \
  byteplus-mediakit-cli editing extract-animated-image \
  --video-url <video_url> \
  --start-time <start_time> \
  --end-time <end_time>
```

Replace placeholders with real user input only. Optional flags follow the parameter rules; do not invent URLs, files, enums, or business parameters.

### Result

| Path | Type | Required | Mode | Description |
| --- | --- | --- | --- | --- |
| `request_id` | string | no | Cloud | Request id returned by the service; forwarded only when non-empty. |
| `task_id` | string | yes | Cloud | Unique async task id used to query status and the terminal result. |
| `task_type` | string | no | Cloud | Task type returned by the service; forwarded only when non-empty. |
| `result.duration` | number | no | Cloud terminal | The duration of the output animated image, in seconds, with up to 3 decimal places. It is calculated as min(end_time, video duration) - start_time. |
| `result.image_url` | string | no | Cloud terminal | The URL of the generated animated image file. This is an HTTPS temporary download link that is valid for 24 hours by default. The output animated image has a fixed frame rate of 15 fps. |
| `result.resolution` | string | no | Cloud terminal | The resolution of the output animated image. The maximum output animated image resolution is 480p. When the input video resolution is below 480p, the output animated image resolution matches the input resolution. |

After a successful Cloud call, read `task_id` and query the terminal result:

```bash
MEDIAKIT_BYTEPLUS_SURFACE=skill MEDIAKIT_BYTEPLUS_RUNTIME=<runtime> \
  byteplus-mediakit-cli shared query-task --task-id <task_id> --poll-complete
```

### Machine contract

These commands read live help/schema only and do not call Cloud:

```bash
byteplus-mediakit-cli editing extract-animated-image --help
byteplus-mediakit-cli editing extract-animated-image --schema
```

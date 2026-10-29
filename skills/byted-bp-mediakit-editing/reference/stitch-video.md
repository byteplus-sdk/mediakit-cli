# stitch-video

## Capability

Spatially combines the input videos horizontally or vertically to produce a single composited video, ideal for scenarios such as multi-view comparison and frame composition. The output video duration matches the longest input video, and shorter input videos are padded with a black screen after playback finishes. Output audio mixes the audio tracks of all input videos whose keep_audio value is true.

## Parameter rules

- Optional parameters may be filled only when the user specifies them, or when they can be determined accurately from user intent; do not invent values. Omit them when uncertain; ask the user if they are required to complete the task.
- The documented maximum number of input videos is 3. If the user provides more than 3 clips, clarify or refuse. Do not drop extras silently and do not submit more than 3 items.

## Cloud

### Command and lifecycle

- Command: `byteplus-mediakit-cli editing stitch-video`
- Lifecycle: async
- Return: returns `task_id`; query the terminal result next.

### Usage guide

- Object or object array parameters (`--videos`) must be passed as a valid JSON string wrapped in single quotes, for example `--videos '[{...}]'`; field names and nesting must match the “Enum/range” column and nested field descriptions in the Parameters table below.
- Do not pass comma-separated values or plain text for these parameters.

### Example

```bash
MEDIAKIT_BYTEPLUS_SURFACE=skill MEDIAKIT_BYTEPLUS_RUNTIME=<runtime> \
  byteplus-mediakit-cli editing stitch-video \
  --videos <videos> \
  --stitch-direction <stitch_direction>
```

Replace placeholders with real user input only. Optional flags follow the parameter rules; do not invent URLs, files, enums, or business parameters.

### Parameters

| Path | CLI flag | Type | Required | Default | Enum/range | Description |
| --- | --- | --- | --- | --- | --- | --- |
| `callback_args` | `--callback-args` | string | no | - | - | Custom callback argument returned unchanged when the task finishes, used to correlate business records; maximum 512 bytes. Pass only when the user provides this value; do not generate, infer, or fill it. |
| `callback_url` | `--callback-url` | string | no | - | - | Callback URL that receives this task result; when set it takes priority over the global callback URL and must start with http:// or https://. Pass only when the user provides this value; do not generate, infer, or fill it. |
| `client_token` | `--client-token` | string | no | - | - | Idempotency token; case-sensitive, at most 64 printable ASCII characters. Omit by default. Reuse the same token for retries of the same logical request. Do not mint a new token for each attempt. The CLI does not infer retry intent or auto-generate tokens. Omit by default. Use the user-provided value as-is when specified. When the user explicitly retries, the same logical request must reuse the same token. If a token already exists, reuse it; if earlier requests had no token, you may create one starting with this retry and keep reusing it, but that token does not provide retroactive idempotency for earlier requests. A change in business parameters is a new request and must not reuse the old token. Do not mint a different value for each attempt. The CLI runtime does not infer retry intent and does not auto-generate tokens. |
| `queue_id` | `--queue-id` | string | no | - | - | Target queue ID for the submitted task. Omit to use the system default queue. Pass only when the user provides this value; do not generate, infer, or fill it. |
| `stitch_direction` | `--stitch-direction` | string | yes | - | enum: ["horizontal","vertical"] | Required. Can be set to horizontal or vertical, where horizontal means side-by-side compositing and vertical means top-and-bottom compositing. |
| `videos` | `--videos` | array<object> | yes | - | min items: 2; max items: 3 | Required. Must contain at least 2 and at most 3 videos. The frame stitching order matches the order of the items. |
| `videos[].keep_audio` | - | boolean | no | true | - | Optional. Controls whether the video's audio is kept, and defaults to true. When set to false, that video's audio track is not mixed into the final output. |
| `videos[].video_url` | - | string | yes | - | - | Required. The URL of an input video to stitch. Only publicly accessible HTTP or HTTPS URLs are supported. Supported common video formats include mp4, flv, ts, avi, mov, wmv, and mkv. Input resolution up to 4K (3840×2160) is supported. The input file size is recommended not to exceed 10 GB. For better stitching results, input videos are recommended to use common aspect ratios such as 16:9, 9:16, 1:1, 4:3, or 3:4. |

### Result

| Path | Type | Required | Mode | Description |
| --- | --- | --- | --- | --- |
| `request_id` | string | no | Cloud | Request id returned by the service; forwarded only when non-empty. |
| `task_id` | string | yes | Cloud | Unique async task id used to query status and the terminal result. |
| `task_type` | string | no | Cloud | Task type returned by the service; forwarded only when non-empty. |
| `result.duration` | number | no | Cloud terminal | The total duration of the output video, measured in seconds. |
| `result.resolution` | string | no | Cloud terminal | The resolution of the output video. It can be 240p, 360p, 480p, 540p, 720p, 1080p, 2k, or 4k. |
| `result.video_url` | string | no | Cloud terminal | The URL of the generated stitched video file in MP4 format, provided as an HTTPS temporary download link that is valid for 24 hours by default. |

After a successful Cloud call, read `task_id` and query the terminal result:

```bash
MEDIAKIT_BYTEPLUS_SURFACE=skill MEDIAKIT_BYTEPLUS_RUNTIME=<runtime> \
  byteplus-mediakit-cli shared query-task --task-id <task_id> --poll-complete
```

### Machine contract

These commands read live help/schema only and do not call Cloud:

```bash
byteplus-mediakit-cli editing stitch-video --help
byteplus-mediakit-cli editing stitch-video --schema
```

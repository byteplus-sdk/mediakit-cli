# face-blur-video

## Capability

It is suitable for protecting facial privacy, including short-drama content anonymization and preprocessing reference material for generative video.

## Parameter rules

- Optional parameters may be filled only when the user specifies them, or when they can be determined accurately from user intent; do not invent values. Omit them when uncertain; ask the user if they are required to complete the task.
- Asking to blur or anonymize faces selects this capability; it does not select mask_mode=blur. Omit mask_mode, mask_strength, and upright_face_only unless the user names mosaic versus Gaussian blur, a strength, or all face orientations.

## Cloud

### Command and lifecycle

- Command: `byteplus-mediakit-cli video face-blur-video`
- Lifecycle: async
- Return: returns `task_id`; query the terminal result next.

### Usage guide

- Boolean parameters (`--upright-face-only`) must be written as `--flag=true` or `--flag=false`, or as bare `--flag` (equivalent to true). Do not pass a space-separated value such as `--flag true`, which the CLI treats as a positional argument.
- Omit boolean flags when they would repeat the default value.

### Example

```bash
MEDIAKIT_BYTEPLUS_SURFACE=skill MEDIAKIT_BYTEPLUS_RUNTIME=<runtime> \
  byteplus-mediakit-cli video face-blur-video \
  --video-url <video_url>
```

Replace placeholders with real user input only. Optional flags follow the parameter rules; do not invent URLs, files, enums, or business parameters.

### Parameters

| Path | CLI flag | Type | Required | Default | Enum/range | Description |
| --- | --- | --- | --- | --- | --- | --- |
| `callback_args` | `--callback-args` | string | no | - | - | Custom callback argument returned unchanged when the task finishes, used to correlate business records; maximum 512 bytes. Pass only when the user provides this value; do not generate, infer, or fill it. |
| `callback_url` | `--callback-url` | string | no | - | - | Callback URL that receives this task result; when set it takes priority over the global callback URL and must start with http:// or https://. Pass only when the user provides this value; do not generate, infer, or fill it. |
| `client_token` | `--client-token` | string | no | - | - | Idempotency token; case-sensitive, at most 64 printable ASCII characters. Omit by default. Reuse the same token for retries of the same logical request. Do not mint a new token for each attempt. The CLI does not infer retry intent or auto-generate tokens. Omit by default. Use the user-provided value as-is when specified. When the user explicitly retries, the same logical request must reuse the same token. If a token already exists, reuse it; if earlier requests had no token, you may create one starting with this retry and keep reusing it, but that token does not provide retroactive idempotency for earlier requests. A change in business parameters is a new request and must not reuse the old token. Do not mint a different value for each attempt. The CLI runtime does not infer retry intent and does not auto-generate tokens. |
| `face_box_expand` | `--face-box-expand` | number | no | 0.2 | min: 0; max: 1 | This optional parameter is the face bounding-box expansion ratio, by which the system expands the masking area outward from the detected face region. It must be greater than 0.0, has an inclusive maximum of 1.0, and defaults to 0.2. |
| `face_confidence` | `--face-confidence` | number | no | 0.35 | min: 0.1; max: 1 | This optional parameter is the confidence threshold for face detection. Detection results below this threshold are discarded, and a higher value makes detection more conservative by requiring higher face similarity. It ranges from an inclusive minimum of 0.1 to an inclusive maximum of 1.0, and defaults to 0.35. |
| `mask_mode` | `--mask-mode` | string | no | "mosaic" | enum: ["mosaic","blur"] | Optional. Selects the masking method. Supports mosaic for mosaic masking and blur for Gaussian blur. Defaults to mosaic. |
| `mask_strength` | `--mask-strength` | string | no | "medium" | enum: ["low","medium","high"] | Optional. Selects the masking strength. Supports low, medium, and high. Defaults to medium. |
| `queue_id` | `--queue-id` | string | no | - | - | Target queue ID for the submitted task. Omit to use the system default queue. Pass only when the user provides this value; do not generate, infer, or fill it. |
| `upright_face_only` | `--upright-face-only` | boolean | no | true | - | Optional. Specifies whether to detect only upright faces to optimize processing speed and controls rotation checks for non-upright faces. Can be set to true or false. When true, the system detects upright and slightly angled faces, including side faces, but not faces that are lying down or upside down, significantly reducing processing time. When false, the system attempts to detect faces in all orientations, widening detection at the cost of increased processing time. Defaults to true. |
| `video_url` | `--video-url` | string | yes | - | - | The URL of the video to be masked. This URL is required. Supported mainstream video formats include mp4, flv, ts, avi, mov, wmv, and mkv. Video duration must not exceed 10 minutes. Video resolution can be up to 4K, and 1080P is recommended. Video frame rate must be within 25 to 60. |

### Result

| Path | Type | Required | Mode | Description |
| --- | --- | --- | --- | --- |
| `request_id` | string | no | Cloud | Request id returned by the service; forwarded only when non-empty. |
| `task_id` | string | yes | Cloud | Unique async task id used to query status and the terminal result. |
| `task_type` | string | no | Cloud | Task type returned by the service; forwarded only when non-empty. |
| `result.duration` | number | no | Cloud terminal | The total duration of the output video, measured in seconds. |
| `result.video_url` | string | no | Cloud terminal | The download URL for the masked video file. The download URL is valid for 24 hours, and the output file should be saved promptly. |

After a successful Cloud call, read `task_id` and query the terminal result:

```bash
MEDIAKIT_BYTEPLUS_SURFACE=skill MEDIAKIT_BYTEPLUS_RUNTIME=<runtime> \
  byteplus-mediakit-cli shared query-task --task-id <task_id> --poll-complete
```

### Machine contract

These commands read live help/schema only and do not call Cloud:

```bash
byteplus-mediakit-cli video face-blur-video --help
byteplus-mediakit-cli video face-blur-video --schema
```

# concat-video

## Capability

Joins video clips in a specified order and can apply transition effects at the joins to create a seamless final video.

## Parameter rules

- Optional parameters may be filled only when the user specifies them, or when they can be determined accurately from user intent; do not invent values. Omit them when uncertain; ask the user if they are required to complete the task.
- transitions accepts only the documented numeric effect IDs. Map a documented Effect Name (Swap, RotateScaleFade, Ripple, Hexagonalize, GlitchDisplay, Flyeye, DreamZoom, Doorway, Cube, CrossWrap, ColorPhase, CircleCrop) to its ID from the Transition effects table, then pass that ID. Omit it for a hard cut. If the user names an effect that has no documented name-to-ID mapping, ask how to proceed; do not invent an ID and do not search the web for a mapping. If you already asked and the user has not answered, stop; do not pick the closest documented ID.

## Cloud

### Command and lifecycle

- Command: `byteplus-mediakit-cli editing concat-video`
- Lifecycle: async
- Return: returns `task_id`; query the terminal result next.

### Usage guide

- Array parameters (`--transitions`) must use comma-separated values wrapped in quotes, for example `--transitions "url1,url2"`.
- Individual file names or URLs must not contain commas (`,`); if that happens, ask the user for comma-free paths or URLs before calling.
- Array parameters (`--video-urls`) must use comma-separated values wrapped in quotes, for example `--video-urls "url1,url2"`.
- Individual file names or URLs must not contain commas (`,`); if that happens, ask the user for comma-free paths or URLs before calling.

### Example

```bash
MEDIAKIT_BYTEPLUS_SURFACE=skill MEDIAKIT_BYTEPLUS_RUNTIME=<runtime> \
  byteplus-mediakit-cli editing concat-video \
  --video-urls "url1,url2"
```

Replace placeholders with real user input only. Optional flags follow the parameter rules; do not invent URLs, files, enums, or business parameters.

### Parameters

| Path | CLI flag | Type | Required | Default | Enum/range | Description |
| --- | --- | --- | --- | --- | --- | --- |
| `callback_args` | `--callback-args` | string | no | - | - | Custom callback argument returned unchanged when the task finishes, used to correlate business records; maximum 512 bytes. Pass only when the user provides this value; do not generate, infer, or fill it. |
| `callback_url` | `--callback-url` | string | no | - | - | Callback URL that receives this task result; when set it takes priority over the global callback URL and must start with http:// or https://. Pass only when the user provides this value; do not generate, infer, or fill it. |
| `client_token` | `--client-token` | string | no | - | - | Idempotency token; case-sensitive, at most 64 printable ASCII characters. Omit by default. Reuse the same token for retries of the same logical request. Do not mint a new token for each attempt. The CLI does not infer retry intent or auto-generate tokens. Omit by default. Use the user-provided value as-is when specified. When the user explicitly retries, the same logical request must reuse the same token. If a token already exists, reuse it; if earlier requests had no token, you may create one starting with this retry and keep reusing it, but that token does not provide retroactive idempotency for earlier requests. A change in business parameters is a new request and must not reuse the old token. Do not mint a different value for each attempt. The CLI runtime does not infer retry intent and does not auto-generate tokens. |
| `queue_id` | `--queue-id` | string | no | - | - | Target queue ID for the submitted task. Omit to use the system default queue. Pass only when the user provides this value; do not generate, infer, or fill it. |
| `transitions` | `--transitions` | array<string> | no | - | element enum: ["1109556","1109558","1109560","1109568","1109572","1109574","1109576","1109578","1109584","1109586","1109588","1109594"] | This field is optional. It sets the list of transition effect IDs between videos. By default, no transitions are applied and joins use hard cuts. When there are at least two more videos than transitions, the system automatically cycles through the transitions. Transition effect ID. Effect Name: Swap, ID: 1109556. Effect Name: RotateScaleFade, ID: 1109558. Effect Name: Ripple, ID: 1109560. Effect Name: Hexagonalize, ID: 1109568. Effect Name: GlitchDisplay, ID: 1109572. Effect Name: Flyeye, ID: 1109574. Effect Name: DreamZoom, ID: 1109576. Effect Name: Doorway, ID: 1109578. Effect Name: Cube, ID: 1109584. Effect Name: CrossWrap, ID: 1109586. Effect Name: ColorPhase, ID: 1109588. Effect Name: CircleCrop, ID: 1109594. |
| `video_urls` | `--video-urls` | array<string> | yes | - | min items: 1; max items: 100 | The list of video URLs to be concatenated. This field is required. A single task supports at most 100 video URLs, and supports at least 1 video URL. If the input videos have inconsistent resolutions, the output resolution matches the first video in the list. Only a publicly accessible HTTP or HTTPS URL is supported. Supported video formats include mp4, flv, ts, avi, mov, wmv, and mkv. Input resolution up to 4K (3840×2160) is supported. It is recommended that each file not exceed 10 GB. Pass multiple values as comma-separated strings or by repeating the flag; do not pass a JSON array string. |

### Transition effects

Pass `--transitions` with the numeric IDs below. Do not pass Effect Name strings.

| Effect Name | ID |
| --- | --- |
| Swap | 1109556 |
| RotateScaleFade | 1109558 |
| Ripple | 1109560 |
| Hexagonalize | 1109568 |
| GlitchDisplay | 1109572 |
| Flyeye | 1109574 |
| DreamZoom | 1109576 |
| Doorway | 1109578 |
| Cube | 1109584 |
| CrossWrap | 1109586 |
| ColorPhase | 1109588 |
| CircleCrop | 1109594 |

### Result

| Path | Type | Required | Mode | Description |
| --- | --- | --- | --- | --- |
| `request_id` | string | no | Cloud | Request id returned by the service; forwarded only when non-empty. |
| `task_id` | string | yes | Cloud | Unique async task id used to query status and the terminal result. |
| `task_type` | string | no | Cloud | Task type returned by the service; forwarded only when non-empty. |
| `result.duration` | number | no | Cloud terminal | The total duration of the output video, measured in seconds. |
| `result.resolution` | string | no | Cloud terminal | The resolution of the output video. |
| `result.video_url` | string | no | Cloud terminal | The download URL of the generated concatenated video in MP4 format. The download URL is valid for 24 hours by default. |

After a successful Cloud call, read `task_id` and query the terminal result:

```bash
MEDIAKIT_BYTEPLUS_SURFACE=skill MEDIAKIT_BYTEPLUS_RUNTIME=<runtime> \
  byteplus-mediakit-cli shared query-task --task-id <task_id> --poll-complete
```

### Machine contract

These commands read live help/schema only and do not call Cloud:

```bash
byteplus-mediakit-cli editing concat-video --help
byteplus-mediakit-cli editing concat-video --schema
```

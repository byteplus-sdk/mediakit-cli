# image-to-video

## Capability

This image-to-video tool combines multiple images in order and can optionally configure transition animations and in-image animations to produce a dynamic video.

## Parameter rules

- Optional parameters may be filled only when the user specifies them, or when they can be determined accurately from user intent; do not invent values. Omit them when uncertain; ask the user if they are required to complete the task.
- transitions accepts only the documented numeric effect IDs. Map a documented Effect Name (Swap, RotateScaleFade, Ripple, Hexagonalize, GlitchDisplay, Flyeye, DreamZoom, Doorway, Cube, CrossWrap, ColorPhase, CircleCrop) to its ID from the Transition effects table, then pass that ID. Omit it for a hard cut. If the user names an effect that has no documented name-to-ID mapping, ask how to proceed; do not invent an ID and do not search the web for a mapping. If you already asked and the user has not answered, stop; do not pick the closest documented ID.

## Cloud

### Command and lifecycle

- Command: `byteplus-mediakit-cli editing image-to-video`
- Lifecycle: async
- Return: returns `task_id`; query the terminal result next.

### Usage guide

- Object or object array parameters (`--images`) must be passed as a valid JSON string wrapped in single quotes, for example `--images '[{...}]'`; field names and nesting must match the “Enum/range” column and nested field descriptions in the Parameters table below.
- Do not pass comma-separated values or plain text for these parameters.
- Array parameters (`--transitions`) must use comma-separated values wrapped in quotes, for example `--transitions "url1,url2"`.
- Individual file names or URLs must not contain commas (`,`); if that happens, ask the user for comma-free paths or URLs before calling.

### Example

```bash
MEDIAKIT_BYTEPLUS_SURFACE=skill MEDIAKIT_BYTEPLUS_RUNTIME=<runtime> \
  byteplus-mediakit-cli editing image-to-video \
  --images <images>
```

Replace placeholders with real user input only. Optional flags follow the parameter rules; do not invent URLs, files, enums, or business parameters.

### Parameters

| Path | CLI flag | Type | Required | Default | Enum/range | Description |
| --- | --- | --- | --- | --- | --- | --- |
| `callback_args` | `--callback-args` | string | no | - | - | Custom callback argument returned unchanged when the task finishes, used to correlate business records; maximum 512 bytes. Pass only when the user provides this value; do not generate, infer, or fill it. |
| `callback_url` | `--callback-url` | string | no | - | - | Callback URL that receives this task result; when set it takes priority over the global callback URL and must start with http:// or https://. Pass only when the user provides this value; do not generate, infer, or fill it. |
| `client_token` | `--client-token` | string | no | - | - | Idempotency token; case-sensitive, at most 64 printable ASCII characters. Omit by default. Reuse the same token for retries of the same logical request. Do not mint a new token for each attempt. The CLI does not infer retry intent or auto-generate tokens. Omit by default. Use the user-provided value as-is when specified. When the user explicitly retries, the same logical request must reuse the same token. If a token already exists, reuse it; if earlier requests had no token, you may create one starting with this retry and keep reusing it, but that token does not provide retroactive idempotency for earlier requests. A change in business parameters is a new request and must not reuse the old token. Do not mint a different value for each attempt. The CLI runtime does not infer retry intent and does not auto-generate tokens. |
| `images` | `--images` | array<object> | yes | - | min items: 1; max items: 100 | images is required. It specifies the list of image objects that define the content of each video frame. A single task supports at least 1 and at most 100 image objects. Each object in images represents an image and its animation effects. |
| `images[].animation_in` | - | number | no | - | - | This field is optional. It sets the animation start time relative to the start of the image segment. The value is measured in seconds and supports two decimal places. It defaults to 0. A value of 0 means the animation starts from the first frame in which the image is displayed. This field takes effect only after animation_type is set. |
| `images[].animation_out` | - | number | no | - | - | This field is optional. It sets the animation end time relative to the start of the image segment. The value is measured in seconds and supports two decimal places. It defaults to the image's duration value. The default makes the animation end on the last frame in which the image is displayed. This field takes effect only after animation_type is set. |
| `images[].animation_type` | - | string | no | - | - | This field is optional. It selects the in-image animation applied while the image is displayed. Supported values are move_up, move_down, move_left, move_right, zoom_in, and zoom_out. By default, no in-image animation is applied. |
| `images[].duration` | - | number | no | - | - | Controls the display duration of the image, in seconds. Optional, defaults to 3, and supports up to two decimal places. |
| `images[].image_url` | - | string | yes | - | - | The URL of the image. Required. Only publicly accessible HTTP or HTTPS image URLs are supported, and common static image formats such as jpg and png are supported. |
| `queue_id` | `--queue-id` | string | no | - | - | Target queue ID for the submitted task. Omit to use the system default queue. Pass only when the user provides this value; do not generate, infer, or fill it. |
| `transitions` | `--transitions` | array<string> | no | - | element enum: ["1109556","1109558","1109560","1109568","1109572","1109574","1109576","1109578","1109584","1109586","1109588","1109594"] | The list of transition effect IDs between images. Optional. By default, there are no transitions and images use hard cuts. If transitions contains fewer effects than the number of images minus 1, the system cycles through the listed effects. Transition effect ID. Effect Name: Swap, ID: 1109556. Effect Name: RotateScaleFade, ID: 1109558. Effect Name: Ripple, ID: 1109560. Effect Name: Hexagonalize, ID: 1109568. Effect Name: GlitchDisplay, ID: 1109572. Effect Name: Flyeye, ID: 1109574. Effect Name: DreamZoom, ID: 1109576. Effect Name: Doorway, ID: 1109578. Effect Name: Cube, ID: 1109584. Effect Name: CrossWrap, ID: 1109586. Effect Name: ColorPhase, ID: 1109588. Effect Name: CircleCrop, ID: 1109594. |

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
| `result.video_url` | string | no | Cloud terminal | The download URL of the generated video, provided as a temporary HTTPS download link. The generated video file is in MP4 format, and the link is valid for 24 hours by default. |

After a successful Cloud call, read `task_id` and query the terminal result:

```bash
MEDIAKIT_BYTEPLUS_SURFACE=skill MEDIAKIT_BYTEPLUS_RUNTIME=<runtime> \
  byteplus-mediakit-cli shared query-task --task-id <task_id> --poll-complete
```

### Machine contract

These commands read live help/schema only and do not call Cloud:

```bash
byteplus-mediakit-cli editing image-to-video --help
byteplus-mediakit-cli editing image-to-video --schema
```

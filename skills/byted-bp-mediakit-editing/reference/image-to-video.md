# image-to-video

## Capability

This image-to-video tool combines multiple images in order and can optionally configure transition animations and in-image animations to produce a dynamic video.

## Parameter rules

- Optional parameters may be filled only when the user specifies them, or when they can be determined accurately from user intent; do not invent values. Omit them when uncertain; ask the user if they are required to complete the task. Before submitting a business task, resolve required fields and required alternatives from the user's explicit requirements, authoritative prior results, or an unambiguous documented mapping. A media type, genre, or broad processing goal does not by itself supply missing user choices or selection criteria. If a required choice has no documented default and remains unresolved, ask a focused clarification, preserve the user's known constraints, and stop before business submission. Do not invent a custom criterion merely to make a required field present. For optional parameters, infer only values implementing a behavior the user actually requested or required by a declared parameter relation. Do not add extra processing, alignment, formatting, or tuning merely because it seems helpful.
- transitions accepts only the documented numeric effect IDs in the parameter table. Omit it for a hard cut. If the user names an effect that has no documented name-to-ID mapping, ask how to proceed; do not invent an ID and do not search the web for a mapping. If you already asked and the user has not answered, stop; do not pick the closest documented ID.

## Cloud

### Command and lifecycle

- Command: `byteplus-mediakit-cli editing image-to-video`
- Lifecycle: async
- Return: returns `task_id`; query the terminal result next.

### Usage guide

- Array parameters (`--images`) must be valid JSON. Prefer a UTF-8 file with `--images @params.json`, or pass correctly escaped inline JSON; field names and nesting must match the “Enum/range” column and nested field descriptions in the Parameters table below.
- Do not pass comma-separated values or plain text for these parameters.
- Inline JSON needs shell escaping. A single-quoted shell argument cannot contain an unescaped apostrophe, such as in It's ready; use a JSON file for text containing apostrophes.
- Array parameters (`--transitions`) must use comma-separated values wrapped in quotes, for example `--transitions "1109556,1109558"`.
- A comma inside one array item must be quoted by CSV rules, including doubled embedded double quotes, or use a JSON array file with `--transitions @params.json`. A single string URL may contain commas; this CSV rule applies only to array encoding.
- For one operation on an ordered collection, include all items in one `--transitions` array argument in the user's order. Submit separate tasks only for independent operations requested by the user or a documented input limit.
- `{}` and `[{}]` illustrate JSON container shapes. Replace `<parameter_json>` in command templates with valid JSON matching the parameter table, including required fields. Empty objects or arrays are valid only when the Schema permits them, including a required object with no required child fields. Required means field presence; null, empty values, zero and false are checked against their own type and constraints. Child fields marked optional remain optional; do not infer required fields from illustrative prose. Do not submit placeholders.

### Example

```bash
byteplus-mediakit-cli editing image-to-video \
  --images '<images_json>'
```

Replace placeholders with real user input only. Optional flags follow the parameter rules; do not invent URLs, files, enums, or business parameters.
Pass each scalar value as one shell argument. The quotes in these templates are illustrative: escape embedded quotes for the actual shell, or use an argument array when invoking a process. Never concatenate raw user input into shell code.

### Parameters

| Path | CLI flag | Type | Required | Default | Enum/range | Description |
| --- | --- | --- | --- | --- | --- | --- |
| `callback_args` | `--callback-args` | string | no | - | - | Custom callback argument returned unchanged when the task finishes, used to correlate business records; maximum 512 bytes. Pass only when the user provides this value; do not generate, infer, or fill it. |
| `callback_url` | `--callback-url` | string | no | - | - | Callback URL that receives this task result; when set it takes priority over the global callback URL and must start with http:// or https://. Pass only when the user provides this value; do not generate, infer, or fill it. |
| `client_token` | `--client-token` | string | no | - | - | Idempotency token; case-sensitive, at most 64 printable ASCII characters. Omit by default. Reuse the same token for retries of the same logical request. Do not mint a new token for each attempt. The CLI does not infer retry intent or auto-generate tokens. Omit by default on the first asynchronous submission. Use a user-provided value as-is when specified. A retry of the same uncertain submission must keep the original token policy: reuse its token if provided, or keep the field omitted if it was not provided. After a result exists, an explicitly requested new run must use a new token. Do not mint a different value for each retry attempt. Synchronous tasks do not support idempotency checks. The CLI runtime does not infer retry intent and does not auto-generate tokens. |
| `images` | `--images` | array<object> | yes | - | min items: 1; max items: 100 | images is required. It specifies the list of image objects that define the content of each video frame. A single task supports at least 1 and at most 100 image objects. Each object in images represents an image and its animation effects. |
| `images[].animation_in` | - | number | no | 0 | - | This field is optional. It sets the animation start time relative to the start of the image segment. The value is measured in seconds and supports two decimal places. It defaults to 0. A value of 0 means the animation starts from the first frame in which the image is displayed. This field takes effect only after animation_type is set. |
| `images[].animation_out` | - | number | no | - | - | This field is optional. It sets the animation end time relative to the start of the image segment. The value is measured in seconds and supports two decimal places. It defaults to the image's duration value. The default makes the animation end on the last frame in which the image is displayed. This field takes effect only after animation_type is set. |
| `images[].animation_type` | - | string | no | - | enum: ["move_up","move_down","move_left","move_right","zoom_in","zoom_out"] | This field is optional. It selects the in-image animation applied while the image is displayed. Supported values are move_up, move_down, move_left, move_right, zoom_in, and zoom_out. By default, no in-image animation is applied. |
| `images[].duration` | - | number | no | 3 | - | Controls the display duration of the image, in seconds. Optional, defaults to 3, and supports up to two decimal places. |
| `images[].image_url` | - | string | yes | - | min length: 1 | The URL of the image. Required. Only publicly accessible HTTP or HTTPS image URLs are supported, and common static image formats such as jpg and png are supported. CLI inputs may be local file paths or public HTTP/HTTPS URLs. The declared media input format also accepts tos:// resource URIs. The CLI handles local file upload internally; pass the original input directly. |
| `queue_id` | `--queue-id` | string | no | - | - | Target queue ID for the submitted task. Omit to use the system default queue. Pass only when the user provides this value; do not generate, infer, or fill it. |
| `transitions` | `--transitions` | array<string> | no | - | element enum: ["1109556","1109558","1109560","1109568","1109572","1109574","1109576","1109578","1109584","1109586","1109588","1109594"] | Transition IDs between consecutive images. Omission uses hard cuts. A shorter list repeats to cover the image boundaries. Use IDs from the supported transition catalog. |

### Result

| Path | Type | Required | Mode | Description |
| --- | --- | --- | --- | --- |
| `request_id` | string | no | Cloud | Request id returned by the service; forwarded only when non-empty. Constraints: min length: 1. |
| `task_id` | string | yes | Cloud | Unique async task id used to query status and the terminal result. Constraints: min length: 1. |
| `task_type` | string | no | Cloud | Task type returned by the service; forwarded only when non-empty. Constraints: min length: 1. |
| `duration` | number | no | Cloud terminal | The total duration of the output video, measured in seconds. |
| `resolution` | string | no | Cloud terminal | The resolution of the output video. |
| `video_url` | string | no | Cloud terminal | The download URL of the generated video, provided as a temporary HTTPS download link. The generated video file is in MP4 format, and the link is valid for 24 hours by default. |

After a successful Cloud call, read the returned `task_id` and check its current status. This command queries once and returns; when the task is still running, query the same task again later until terminal. Do not resubmit the business task. Use `--poll-complete` only when continuous waiting is intended; it waits without a limit by default. Pass a polling timeout or attempt limit only when specified by the user. Interrupting the CLI stops waiting without canceling the Cloud task:

```bash
byteplus-mediakit-cli shared query-task --task-id '<task_id>'
```

After a synchronous business tool succeeds or an asynchronous task reaches completed, return its actual result and any output asset URLs to the user. Use the returned URLs as inputs to requested dependent steps. Download assets or perform additional local processing only when the user requests it, and retain the Cloud result URL alongside any downloaded file. Do not present task submission as completion. Do not replace a completed Cloud output with a locally recreated file from the original input, or infer permission for local recreation from a requested duration or quality target. If a returned asset's metadata or name conflicts with the requested constraints, probe that asset with an available metadata tool before claiming a mismatch or performing a correction. Corrective processing must consume the completed Cloud output, preserving its lineage; present the original Cloud result URL alongside any corrected output and explain any confirmed limitation. Never omit an available completed result URL in favor of an unfinished correction or a local fallback.

### Machine contract

These commands read live help/schema only and do not call Cloud:

```bash
byteplus-mediakit-cli editing image-to-video --help
byteplus-mediakit-cli editing image-to-video --schema
```

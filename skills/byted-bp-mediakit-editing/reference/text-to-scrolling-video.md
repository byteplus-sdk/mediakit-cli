# text-to-scrolling-video

## Capability

Turn text and a background image into a vertical scrolling-text video, with optional background music and timing controls.

## Parameter rules

- Optional parameters may be filled only when the user specifies them, or when they can be determined accurately from user intent; do not invent values. Omit them when uncertain; ask the user if they are required to complete the task. Before submitting a business task, resolve required fields and required alternatives from the user's explicit requirements, authoritative prior results, or an unambiguous documented mapping. A media type, genre, or broad processing goal does not by itself supply missing user choices or selection criteria. If a required choice has no documented default and remains unresolved, ask a focused clarification, preserve the user's known constraints, and stop before business submission. Do not invent a custom criterion merely to make a required field present. For optional parameters, infer only values implementing a behavior the user actually requested or required by a declared parameter relation. Do not add extra processing, alignment, formatting, or tuning merely because it seems helpful.

## Cloud

### Command and lifecycle

- Command: `byteplus-mediakit-cli editing text-to-scrolling-video`
- Lifecycle: async
- Return: returns `task_id`; query the terminal result next.

### Parameters

| Path | CLI flag | Type | Required | Default | Enum/range | Description |
| --- | --- | --- | --- | --- | --- | --- |
| `audio_url` | `--audio-url` | string | no | - | - | Background audio that loops to cover the video duration. Supports common formats including MP3, M4A and WAV. The recommended maximum input file size is 10 GB; the API accepts public HTTP/HTTPS URLs. CLI inputs may be local file paths or public HTTP/HTTPS URLs. The declared media input format also accepts vod:// and tos:// resource URIs. The CLI handles local file upload internally; pass the original input directly. |
| `callback_args` | `--callback-args` | string | no | - | - | Custom callback argument returned unchanged when the task finishes, used to correlate business records; maximum 512 bytes. Pass only when the user provides this value; do not generate, infer, or fill it. |
| `callback_url` | `--callback-url` | string | no | - | - | Callback URL that receives this task result; when set it takes priority over the global callback URL and must start with http:// or https://. Pass only when the user provides this value; do not generate, infer, or fill it. |
| `client_token` | `--client-token` | string | no | - | - | Idempotency token; case-sensitive, at most 64 printable ASCII characters. Omit by default. Reuse the same token for retries of the same logical request. Do not mint a new token for each attempt. The CLI does not infer retry intent or auto-generate tokens. Omit by default on the first asynchronous submission. Use a user-provided value as-is when specified. A retry of the same uncertain submission must keep the original token policy: reuse its token if provided, or keep the field omitted if it was not provided. After a result exists, an explicitly requested new run must use a new token. Do not mint a different value for each retry attempt. Synchronous tasks do not support idempotency checks. The CLI runtime does not infer retry intent and does not auto-generate tokens. |
| `end_hold_duration` | `--end-hold-duration` | number | no | 2 | min: 0; max: 60 | Seconds to hold the text at its final position after scrolling. Range 0 to 60; default 2. |
| `font_color` | `--font-color` | string | no | "#1F1F1FFF" | pattern: "^#[0-9A-Fa-f]{8}$" | Text color in eight-digit RGBA hexadecimal form, RRGGBBAA. Defaults to opaque dark gray, |
| `font_type` | `--font-type` | string | no | "inter" | enum: ["inter","roboto","source_han_serif"] | Scrolling text font, chosen from inter, roboto and source_han_serif. The default is inter. |
| `image_url` | `--image-url` | string | yes | - | min length: 1 | Static JPG, PNG or WebP background image. Prefer a 9:16 image close to the chosen video resolution, with sufficient contrast against the text. Keep important content outside the top and bottom 10 percent, which are masked. The API accepts public HTTP/HTTPS URLs. CLI inputs may be local file paths or public HTTP/HTTPS URLs. The declared media input format also accepts tos:// resource URIs. The CLI handles local file upload internally; pass the original input directly. |
| `queue_id` | `--queue-id` | string | no | - | - | Target queue ID for the submitted task. Omit to use the system default queue. Pass only when the user provides this value; do not generate, infer, or fill it. |
| `resolution` | `--resolution` | string | no | "720p" | enum: ["360p","480p","720p","1080p"] | Vertical video resolution at a fixed 9:16 aspect ratio. Choose 360p, 480p, 720p or 1080p; the default is 720p. |
| `single_roll_duration` | `--single-roll-duration` | number | no | 3 | min: 0.5; max: 60 | Seconds for one page of text to scroll across the screen. Smaller values scroll faster. Range 0.5 to 60; default 3. |
| `start_hold_duration` | `--start-hold-duration` | number | no | 2 | min: 0; max: 60 | Seconds to hold the text at its initial position before scrolling. Range 0 to 60; default 2. |
| `text` | `--text` | string | yes | - | min length: 1 | Left-aligned text for the scrolling video. A newline forces a line break; other text wraps to the canvas width. Some characters require escaping; use // to display a single slash. |

### Example

```bash
byteplus-mediakit-cli editing text-to-scrolling-video \
  --text '<text>' \
  --image-url '<image_url>'
```

Replace placeholders with real user input only. Optional flags follow the parameter rules; do not invent URLs, files, enums, or business parameters.
Pass each scalar value as one shell argument. The quotes in these templates are illustrative: escape embedded quotes for the actual shell, or use an argument array when invoking a process. Never concatenate raw user input into shell code.

### Result

| Path | Type | Required | Mode | Description |
| --- | --- | --- | --- | --- |
| `request_id` | string | no | Cloud | Request id returned by the service; forwarded only when non-empty. Constraints: min length: 1. |
| `task_id` | string | yes | Cloud | Unique async task id used to query status and the terminal result. Constraints: min length: 1. |
| `task_type` | string | no | Cloud | Task type returned by the service; forwarded only when non-empty. Constraints: min length: 1. |
| `duration` | number | no | Cloud terminal | Duration of the generated scrolling video in seconds. |
| `resolution` | string | no | Cloud terminal | Resolution of the generated scrolling video. |
| `video_url` | string | no | Cloud terminal | Generated MP4 scrolling text video. Download and save it within the 24-hour validity period. |

After a successful Cloud call, read the returned `task_id` and check its current status. This command queries once and returns; when the task is still running, query the same task again later until terminal. Do not resubmit the business task. Use `--poll-complete` only when continuous waiting is intended; it waits without a limit by default. Pass a polling timeout or attempt limit only when specified by the user. Interrupting the CLI stops waiting without canceling the Cloud task:

```bash
byteplus-mediakit-cli shared query-task --task-id '<task_id>'
```

After a synchronous business tool succeeds or an asynchronous task reaches completed, return its actual result and any output asset URLs to the user. Use the returned URLs as inputs to requested dependent steps. Download assets or perform additional local processing only when the user requests it, and retain the Cloud result URL alongside any downloaded file. Do not present task submission as completion. Do not replace a completed Cloud output with a locally recreated file from the original input, or infer permission for local recreation from a requested duration or quality target. If a returned asset's metadata or name conflicts with the requested constraints, probe that asset with an available metadata tool before claiming a mismatch or performing a correction. Corrective processing must consume the completed Cloud output, preserving its lineage; present the original Cloud result URL alongside any corrected output and explain any confirmed limitation. Never omit an available completed result URL in favor of an unfinished correction or a local fallback.

### Machine contract

These commands read live help/schema only and do not call Cloud:

```bash
byteplus-mediakit-cli editing text-to-scrolling-video --help
byteplus-mediakit-cli editing text-to-scrolling-video --schema
```

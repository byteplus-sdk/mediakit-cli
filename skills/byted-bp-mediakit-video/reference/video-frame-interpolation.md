# video-frame-interpolation

## Capability

Uses deep learning to intelligently synthesize new frames and increase the video's frame rate to a specified target. This is particularly effective for enhancing the viewing experience of low-frame-rate, fast-motion, or large-screen videos.

## Parameter rules

- Optional parameters may be filled only when the user specifies them, or when they can be determined accurately from user intent; do not invent values. Omit them when uncertain; ask the user if they are required to complete the task. Before submitting a business task, resolve required fields and required alternatives from the user's explicit requirements, authoritative prior results, or an unambiguous documented mapping. A media type, genre, or broad processing goal does not by itself supply missing user choices or selection criteria. If a required choice has no documented default and remains unresolved, ask a focused clarification, preserve the user's known constraints, and stop before business submission. Do not invent a custom criterion merely to make a required field present. For optional parameters, infer only values implementing a behavior the user actually requested or required by a declared parameter relation. Do not add extra processing, alignment, formatting, or tuning merely because it seems helpful.

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
| `client_token` | `--client-token` | string | no | - | - | Idempotency token; case-sensitive, at most 64 printable ASCII characters. Omit by default. Reuse the same token for retries of the same logical request. Do not mint a new token for each attempt. The CLI does not infer retry intent or auto-generate tokens. Omit by default on the first asynchronous submission. Use a user-provided value as-is when specified. A retry of the same uncertain submission must keep the original token policy: reuse its token if provided, or keep the field omitted if it was not provided. After a result exists, an explicitly requested new run must use a new token. Do not mint a different value for each retry attempt. Synchronous tasks do not support idempotency checks. The CLI runtime does not infer retry intent and does not auto-generate tokens. |
| `fps` | `--fps` | number | yes | - | min: 15; max: 120 | This parameter specifies the target output frame rate, measured in fps, and is required. The minimum value is 15 and the maximum value is 120. For a more natural interpolation result, the target frame rate is recommended not to exceed 4x the source frame rate. If the target frame rate is lower than the source frame rate, the system first performs intelligent interpolation and then downsamples to reach the final target frame rate. |
| `queue_id` | `--queue-id` | string | no | - | - | Target queue ID for the submitted task. Omit to use the system default queue. Pass only when the user provides this value; do not generate, infer, or fill it. |
| `video_url` | `--video-url` | string | yes | - | min length: 1 | This parameter is the URL of the video to be interpolated and is required. Cloud requests use publicly accessible HTTP/HTTPS URLs. Common supported video formats include mp4, flv, ts, avi, mov, wmv, and mkv. CLI inputs may be local file paths or public HTTP/HTTPS URLs. The CLI handles local file upload internally; pass the original input directly. |

### Example

```bash
byteplus-mediakit-cli video video-frame-interpolation \
  --video-url '<video_url>' \
  --fps '<fps>'
```

Replace placeholders with real user input only. Optional flags follow the parameter rules; do not invent URLs, files, enums, or business parameters.
Pass each scalar value as one shell argument. The quotes in these templates are illustrative: escape embedded quotes for the actual shell, or use an argument array when invoking a process. Never concatenate raw user input into shell code.

### Result

| Path | Type | Required | Mode | Description |
| --- | --- | --- | --- | --- |
| `request_id` | string | no | Cloud | Request id returned by the service; forwarded only when non-empty. Constraints: min length: 1. |
| `task_id` | string | yes | Cloud | Unique async task id used to query status and the terminal result. Constraints: min length: 1. |
| `task_type` | string | no | Cloud | Task type returned by the service; forwarded only when non-empty. Constraints: min length: 1. |
| `duration` | number | no | Cloud terminal | The duration of the output video, measured in seconds, used for metering and billing. |
| `fps` | number | no | Cloud terminal | The frame rate of the output video, which matches the target frame rate specified at submission. |
| `resolution` | string | no | Cloud terminal | The resolution level of the output video. Interpolation does not change the resolution, so the output resolution stays the same as the source. Possible values are 240p, 360p, 480p, 540p, 720p, 1080p, 2k, 4k, and 8k. Constraints: enum: ["240p","360p","480p","540p","720p","1080p","2k","4k","8k"]. |
| `video_url` | string | no | Cloud terminal | The URL of the interpolated video file, provided as an HTTPS temporary download link that is valid for 24 hours by default. |

After a successful Cloud call, read the returned `task_id` and check its current status. This command queries once and returns; when the task is still running, query the same task again later until terminal. Do not resubmit the business task. Use `--poll-complete` only when continuous waiting is intended; it waits without a limit by default. Pass a polling timeout or attempt limit only when specified by the user. Interrupting the CLI stops waiting without canceling the Cloud task:

```bash
byteplus-mediakit-cli shared query-task --task-id '<task_id>'
```

After a synchronous business tool succeeds or an asynchronous task reaches completed, return its actual result and any output asset URLs to the user. Use the returned URLs as inputs to requested dependent steps. Download assets or perform additional local processing only when the user requests it, and retain the Cloud result URL alongside any downloaded file. Do not present task submission as completion. Do not replace a completed Cloud output with a locally recreated file from the original input, or infer permission for local recreation from a requested duration or quality target. If a returned asset's metadata or name conflicts with the requested constraints, probe that asset with an available metadata tool before claiming a mismatch or performing a correction. Corrective processing must consume the completed Cloud output, preserving its lineage; present the original Cloud result URL alongside any corrected output and explain any confirmed limitation. Never omit an available completed result URL in favor of an unfinished correction or a local fallback.

### Machine contract

These commands read live help/schema only and do not call Cloud:

```bash
byteplus-mediakit-cli video video-frame-interpolation --help
byteplus-mediakit-cli video video-frame-interpolation --schema
```

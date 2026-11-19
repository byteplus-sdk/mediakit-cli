# enhance-video-smoothness

## Capability

Detect and optionally repair periodic stutter and duplicate frames in a video. Detection can also run without producing a repaired video.

## Parameter rules

- Video stutter and duplicate-frame detection settings. Optional parameters may be filled only when the user specifies them, or when they can be determined accurately from user intent; do not invent values. Omit them when uncertain; ask the user if they are required to complete the task. Before submitting a business task, resolve required fields and required alternatives from the user's explicit requirements, authoritative prior results, or an unambiguous documented mapping. A media type, genre, or broad processing goal does not by itself supply missing user choices or selection criteria. If a required choice has no documented default and remains unresolved, ask a focused clarification, preserve the user's known constraints, and stop before business submission. Do not invent a custom criterion merely to make a required field present. For optional parameters, infer only values implementing a behavior the user actually requested or required by a declared parameter relation. Do not add extra processing, alignment, formatting, or tuning merely because it seems helpful.

## Cloud

### Command and lifecycle

- Command: `byteplus-mediakit-cli video enhance-video-smoothness`
- Lifecycle: async
- Return: returns `task_id`; query the terminal result next.

### Usage guide

- JSON parameters (`--duplicate-frame-detect`, `--periodic-stutter-detect`) must be valid JSON. Prefer a UTF-8 file using `@params.json`, or pass correctly escaped inline JSON; field names and nesting must match the Parameters table below.
- Do not pass comma-separated values or plain text for these parameters.
- Inline JSON needs shell escaping. A single-quoted shell argument cannot contain an unescaped apostrophe, such as in It's ready; use a JSON file for text containing apostrophes.
- `{}` and `[{}]` illustrate JSON container shapes. Replace `<parameter_json>` in command templates with valid JSON matching the parameter table, including required fields. Empty objects or arrays are valid only when the Schema permits them, including a required object with no required child fields. Required means field presence; null, empty values, zero and false are checked against their own type and constraints. Child fields marked optional remain optional; do not infer required fields from illustrative prose. Do not submit placeholders.

### Example

```bash
byteplus-mediakit-cli video enhance-video-smoothness \
  --video-url '<video_url>'
```

Replace placeholders with real user input only. Optional flags follow the parameter rules; do not invent URLs, files, enums, or business parameters.
Pass each scalar value as one shell argument. The quotes in these templates are illustrative: escape embedded quotes for the actual shell, or use an argument array when invoking a process. Never concatenate raw user input into shell code.

### Parameters

| Path | CLI flag | Type | Required | Default | Enum/range | Description |
| --- | --- | --- | --- | --- | --- | --- |
| `callback_args` | `--callback-args` | string | no | - | - | Custom callback argument returned unchanged when the task finishes, used to correlate business records; maximum 512 bytes. Pass only when the user provides this value; do not generate, infer, or fill it. |
| `callback_url` | `--callback-url` | string | no | - | - | Callback URL that receives this task result; when set it takes priority over the global callback URL and must start with http:// or https://. Pass only when the user provides this value; do not generate, infer, or fill it. |
| `client_token` | `--client-token` | string | no | - | - | Idempotency token; case-sensitive, at most 64 printable ASCII characters. Omit by default. Reuse the same token for retries of the same logical request. Do not mint a new token for each attempt. The CLI does not infer retry intent or auto-generate tokens. Omit by default on the first asynchronous submission. Use a user-provided value as-is when specified. A retry of the same uncertain submission must keep the original token policy: reuse its token if provided, or keep the field omitted if it was not provided. After a result exists, an explicitly requested new run must use a new token. Do not mint a different value for each retry attempt. Synchronous tasks do not support idempotency checks. The CLI runtime does not infer retry intent and does not auto-generate tokens. |
| `duplicate_frame_detect` | `--duplicate-frame-detect` | object | no | - | - | Duplicate-frame configuration. Omission enables detection and repair. |
| `duplicate_frame_detect.duplicate_frame_repair` | - | boolean | no | true | - | Detect and remove duplicate frames when true, the default. False performs detection alone. |
| `periodic_stutter_detect` | `--periodic-stutter-detect` | object | no | - | - | Periodic stutter configuration. Omission enables detection and repair. |
| `periodic_stutter_detect.align_source_fps` | - | boolean | no | false | - | Retain the source frame rate when true by removing an equal number of low-variation frames, preserving frame count and duration. Default false prioritizes repair quality and can increase frame rate and frame count. |
| `periodic_stutter_detect.insert_frame_indices` | - | array<integer> | no | - | min items: 1; unique items: true; element min: 0 | Extra zero-based positions at which to insert a frame during periodic-stutter repair. Provide at least one nonnegative index with no duplicates. An index of 80 inserts a frame between source frames 80 and 81. |
| `periodic_stutter_detect.periodic_stutter_repair` | - | boolean | no | true | - | Repair detected periodic stutter when true, the default. False performs detection alone. |
| `queue_id` | `--queue-id` | string | no | - | - | Target queue ID for the submitted task. Omit to use the system default queue. Pass only when the user provides this value; do not generate, infer, or fill it. |
| `video_url` | `--video-url` | string | yes | - | - | MP4, AVI or MOV video up to 4K. Detection with repair supports up to 35 seconds; detection alone has no duration limit. The API requires a public HTTP/HTTPS URL. CLI inputs may be local file paths or public HTTP/HTTPS URLs. The CLI handles local file upload internally; pass the original input directly. |

### Result

| Path | Type | Required | Mode | Description |
| --- | --- | --- | --- | --- |
| `request_id` | string | no | Cloud | Request id returned by the service; forwarded only when non-empty. Constraints: min length: 1. |
| `task_id` | string | yes | Cloud | Unique async task id used to query status and the terminal result. Constraints: min length: 1. |
| `task_type` | string | no | Cloud | Task type returned by the service; forwarded only when non-empty. Constraints: min length: 1. |
| `duplicate_frames_detail` | object | no | Cloud terminal | Duplicate-frame detection result, available whenever that detection is enabled, including when repair is skipped. |
| `duplicate_frames_detail.duplicate_frame_count` | integer | no | Cloud terminal | Frames detected as duplicates of their predecessors. Detection uses the intermediate video after successful periodic-stutter repair, otherwise the source video. Constraints: min: 0. |
| `duration` | number | no | Cloud terminal | Input video duration in seconds, used for metering and billing. Constraints: min: 0. |
| `inserted_frames_detail` | object | no | Cloud terminal | Periodic-stutter detection result, available whenever that detection is enabled, including when repair is skipped. |
| `inserted_frames_detail.inserted_frame_count` | integer | no | Cloud terminal | Frames the algorithm recommends inserting, excluding manually supplied positions. This detection count does not report how many frames were actually inserted. Constraints: min: 0. |
| `video_url` | string | no | Cloud terminal | Repaired video download link, valid for 24 hours. Its presence denotes detection-and-repair billing. Its absence denotes detection-only billing because repair was disabled, unnecessary or skipped after quality evaluation. |

After a successful Cloud call, read the returned `task_id` and check its current status. This command queries once and returns; when the task is still running, query the same task again later until terminal. Do not resubmit the business task. Use `--poll-complete` only when continuous waiting is intended; it waits without a limit by default. Pass a polling timeout or attempt limit only when specified by the user. Interrupting the CLI stops waiting without canceling the Cloud task:

```bash
byteplus-mediakit-cli shared query-task --task-id '<task_id>'
```

After a synchronous business tool succeeds or an asynchronous task reaches completed, return its actual result and any output asset URLs to the user. Use the returned URLs as inputs to requested dependent steps. Download assets or perform additional local processing only when the user requests it, and retain the Cloud result URL alongside any downloaded file. Do not present task submission as completion. Do not replace a completed Cloud output with a locally recreated file from the original input, or infer permission for local recreation from a requested duration or quality target. If a returned asset's metadata or name conflicts with the requested constraints, probe that asset with an available metadata tool before claiming a mismatch or performing a correction. Corrective processing must consume the completed Cloud output, preserving its lineage; present the original Cloud result URL alongside any corrected output and explain any confirmed limitation. Never omit an available completed result URL in favor of an unfinished correction or a local fallback.

### Machine contract

These commands read live help/schema only and do not call Cloud:

```bash
byteplus-mediakit-cli video enhance-video-smoothness --help
byteplus-mediakit-cli video enhance-video-smoothness --schema
```

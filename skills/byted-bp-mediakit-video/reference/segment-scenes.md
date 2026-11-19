# segment-scenes

## Capability

Automatically splits a video into multiple scene segments based on transitions and changes in visual content, and outputs each segment's timeline information and independent video file.

## Parameter rules

- Optional parameters may be filled only when the user specifies them, or when they can be determined accurately from user intent; do not invent values. Omit them when uncertain; ask the user if they are required to complete the task. Before submitting a business task, resolve required fields and required alternatives from the user's explicit requirements, authoritative prior results, or an unambiguous documented mapping. A media type, genre, or broad processing goal does not by itself supply missing user choices or selection criteria. If a required choice has no documented default and remains unresolved, ask a focused clarification, preserve the user's known constraints, and stop before business submission. Do not invent a custom criterion merely to make a required field present. For optional parameters, infer only values implementing a behavior the user actually requested or required by a declared parameter relation. Do not add extra processing, alignment, formatting, or tuning merely because it seems helpful.

## Cloud

### Command and lifecycle

- Command: `byteplus-mediakit-cli video segment-scenes`
- Lifecycle: async
- Return: returns `task_id`; query the terminal result next.

### Usage guide

- Boolean parameters (`--enable-clip-fade`, `--return-segment-videos`) must be written as `--flag=true` or `--flag=false`, or as bare `--flag` (equivalent to true). Do not pass a space-separated value such as `--flag true`, which the CLI treats as a positional argument.
- Pass a boolean explicitly when the user specifies true or false, including values equal to the default. Omit it when the user has not specified a value and no relation requires it.
- Requesting the main operation does not request every optional behavior. Do not enable an optional switch or fill its dependent defaults merely because input metadata differs or a changed output would be more convenient. Keep those fields omitted unless the user requested that separate behavior or a declared parameter relation requires their explicit presence.

### Example

```bash
byteplus-mediakit-cli video segment-scenes \
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
| `enable_clip_fade` | `--enable-clip-fade` | boolean | no | false | - | Controls whether detected fade-in/fade-out segments are output as independent clips. This parameter is optional and can be set to false or true, and defaults to false. When set to true, noticeable fade-in/fade-out transitions are split into independent clips. When set to false, fade transitions are not output separately and are merged into adjacent clips as part of the preceding or following scene. |
| `max_duration` | `--max-duration` | number | no | - | min: 0 | The maximum duration of a single clip, in seconds. This parameter is optional and defaults to 30. The value should be greater than or equal to min_duration. Segments longer than this maximum are forcibly split. |
| `min_duration` | `--min-duration` | number | no | - | min: 0 | The minimum duration of a single clip, in seconds. This parameter is optional and defaults to 3. It should be less than or equal to max_duration. Clips shorter than this duration are merged. |
| `return_segment_videos` | `--return-segment-videos` | boolean | no | true | - | Optional field that controls whether clip video files are generated and returned. It can be set to true or false, and the default value is true. When set to true, clip files are generated and each clip download URL is returned in segments[].segment_video_url. When set to false, clip files are not generated and only the start_time and end_time timeline is returned, without segment_video_url. Setting it to false is suitable when only scene timecodes are needed and clipping is handled by the caller, and it can reduce task processing time. |
| `segment_threshold` | `--segment-threshold` | number | no | 10 | min: 0; max: 100; less than: 100 | Scene-change threshold from 0 to below 100, default 10. Lower values produce more segments. With duration bounds also set, segmentation first uses the threshold and minimum duration, then recuts overlong segments to meet max_duration regardless of the threshold. |
| `video_url` | `--video-url` | string | yes | - | min length: 1 | The URL of the video to be processed. This parameter is required. Commonly supported video formats include mp4, flv, ts, avi, mov, wmv, and mkv. Cloud requests use publicly accessible HTTP/HTTPS URLs. A single video must not exceed 2 hours. CLI inputs may be local file paths or public HTTP/HTTPS URLs. The declared media input format also accepts vod:// and tos:// resource URIs. The CLI handles local file upload internally; pass the original input directly. |

### Result

| Path | Type | Required | Mode | Description |
| --- | --- | --- | --- | --- |
| `request_id` | string | no | Cloud | Request id returned by the service; forwarded only when non-empty. Constraints: min length: 1. |
| `task_id` | string | yes | Cloud | Unique async task id used to query status and the terminal result. Constraints: min length: 1. |
| `task_type` | string | no | Cloud | Task type returned by the service; forwarded only when non-empty. Constraints: min length: 1. |
| `duration` | number | no | Cloud terminal | The total duration of the input video, in seconds. |
| `segments` | array<object> | no | Cloud terminal | Scene segments with their timelines and other available metadata. |
| `segments[].end_time` | number | no | Cloud terminal | The end time of the clip, in seconds. |
| `segments[].segment_video_url` | string | no | Cloud terminal | MP4 clip download link, returned when return_segment_videos is true. Download and save it within the 24-hour validity period. |
| `segments[].start_time` | number | no | Cloud terminal | The start time of the clip, in seconds. |

After a successful Cloud call, read the returned `task_id` and check its current status. This command queries once and returns; when the task is still running, query the same task again later until terminal. Do not resubmit the business task. Use `--poll-complete` only when continuous waiting is intended; it waits without a limit by default. Pass a polling timeout or attempt limit only when specified by the user. Interrupting the CLI stops waiting without canceling the Cloud task:

```bash
byteplus-mediakit-cli shared query-task --task-id '<task_id>'
```

After a synchronous business tool succeeds or an asynchronous task reaches completed, return its actual result and any output asset URLs to the user. Use the returned URLs as inputs to requested dependent steps. Download assets or perform additional local processing only when the user requests it, and retain the Cloud result URL alongside any downloaded file. Do not present task submission as completion. Do not replace a completed Cloud output with a locally recreated file from the original input, or infer permission for local recreation from a requested duration or quality target. If a returned asset's metadata or name conflicts with the requested constraints, probe that asset with an available metadata tool before claiming a mismatch or performing a correction. Corrective processing must consume the completed Cloud output, preserving its lineage; present the original Cloud result URL alongside any corrected output and explain any confirmed limitation. Never omit an available completed result URL in favor of an unfinished correction or a local fallback.

### Machine contract

These commands read live help/schema only and do not call Cloud:

```bash
byteplus-mediakit-cli video segment-scenes --help
byteplus-mediakit-cli video segment-scenes --schema
```

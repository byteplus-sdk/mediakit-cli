# semantic-segment-pro

## Capability

Split a video into semantically complete segments using content-specific strategies, with optional shot, scene, sentence and segment-video results.

## Parameter rules

- Optional parameters may be filled only when the user specifies them, or when they can be determined accurately from user intent; do not invent values. Omit them when uncertain; ask the user if they are required to complete the task. Before submitting a business task, resolve required fields and required alternatives from the user's explicit requirements, authoritative prior results, or an unambiguous documented mapping. A media type, genre, or broad processing goal does not by itself supply missing user choices or selection criteria. If a required choice has no documented default and remains unresolved, ask a focused clarification, preserve the user's known constraints, and stop before business submission. Do not invent a custom criterion merely to make a required field present. For optional parameters, infer only values implementing a behavior the user actually requested or required by a declared parameter relation. Do not add extra processing, alignment, formatting, or tuning merely because it seems helpful.

## Cloud

### Command and lifecycle

- Command: `byteplus-mediakit-cli video semantic-segment-pro`
- Lifecycle: async
- Return: returns `task_id`; query the terminal result next.

### Usage guide

- Array parameters (`--include-detail-types`) must use comma-separated values wrapped in quotes, for example `--include-detail-types "shot_list,scene_list"`.
- A comma inside one array item must be quoted by CSV rules, including doubled embedded double quotes, or use a JSON array file with `--include-detail-types @params.json`. A single string URL may contain commas; this CSV rule applies only to array encoding.
- For one operation on an ordered collection, include all items in one `--include-detail-types` array argument in the user's order. Submit separate tasks only for independent operations requested by the user or a documented input limit.
- Boolean parameters (`--avoid-sentence-cutoff`, `--enable-scene-detection`, `--return-segment-videos`) must be written as `--flag=true` or `--flag=false`, or as bare `--flag` (equivalent to true). Do not pass a space-separated value such as `--flag true`, which the CLI treats as a positional argument.
- Pass a boolean explicitly when the user specifies true or false, including values equal to the default. Omit it when the user has not specified a value and no relation requires it.
- Requesting the main operation does not request every optional behavior. Do not enable an optional switch or fill its dependent defaults merely because input metadata differs or a changed output would be more convenient. Keep those fields omitted unless the user requested that separate behavior or a declared parameter relation requires their explicit presence.

### Example

```bash
byteplus-mediakit-cli video semantic-segment-pro \
  --video-url '<video_url>' \
  --strategy '<strategy>'
```

Replace placeholders with real user input only. Optional flags follow the parameter rules; do not invent URLs, files, enums, or business parameters.
Pass each scalar value as one shell argument. The quotes in these templates are illustrative: escape embedded quotes for the actual shell, or use an argument array when invoking a process. Never concatenate raw user input into shell code.

### Parameters

| Path | CLI flag | Type | Required | Default | Enum/range | Description |
| --- | --- | --- | --- | --- | --- | --- |
| `avoid_sentence_cutoff` | `--avoid-sentence-cutoff` | boolean | no | false | - | Try to cut at sentence boundaries in custom mode, default false. Depends on ASR or OCR and may have little effect without recognizable speech or subtitles. Other strategies ignore this flag. |
| `callback_args` | `--callback-args` | string | no | - | - | Custom callback argument returned unchanged when the task finishes, used to correlate business records; maximum 512 bytes. Pass only when the user provides this value; do not generate, infer, or fill it. |
| `callback_url` | `--callback-url` | string | no | - | - | Callback URL that receives this task result; when set it takes priority over the global callback URL and must start with http:// or https://. Pass only when the user provides this value; do not generate, infer, or fill it. |
| `client_token` | `--client-token` | string | no | - | - | Idempotency token; case-sensitive, at most 64 printable ASCII characters. Omit by default. Reuse the same token for retries of the same logical request. Do not mint a new token for each attempt. The CLI does not infer retry intent or auto-generate tokens. Omit by default on the first asynchronous submission. Use a user-provided value as-is when specified. A retry of the same uncertain submission must keep the original token policy: reuse its token if provided, or keep the field omitted if it was not provided. After a result exists, an explicitly requested new run must use a new token. Do not mint a different value for each retry attempt. Synchronous tasks do not support idempotency checks. The CLI runtime does not infer retry intent and does not auto-generate tokens. |
| `enable_scene_detection` | `--enable-scene-detection` | boolean | no | false | - | Detect and merge semantic scenes in custom mode. Defaults to false. Other strategies use their own configurations and ignore this flag. |
| `include_detail_types` | `--include-detail-types` | array<string> | no | [] | unique items: true; element enum: ["shot_list","scene_list","sentence_list"] | Optional result details, shot_list, scene_list and sentence_list. Defaults to an empty list; segments are always returned. Include shot_list alongside scenes or sentences to resolve their shot_ids to shot details. |
| `max_duration` | `--max-duration` | integer | no | 30 | min: 1; max: 600 | Maximum segment duration from 1 to 600 seconds, default 30. Must not be less than min_duration. |
| `min_duration` | `--min-duration` | integer | no | 3 | min: 1; max: 600 | Minimum segment duration from 1 to 600 seconds, default 3. Must not exceed max_duration. |
| `queue_id` | `--queue-id` | string | no | - | - | Target queue ID for the submitted task. Omit to use the system default queue. Pass only when the user provides this value; do not generate, infer, or fill it. |
| `return_segment_videos` | `--return-segment-videos` | boolean | no | false | - | Generate clips and return their links in segments when true. Defaults to false, which returns segment metadata alone. |
| `strategy` | `--strategy` | string | yes | - | enum: ["short_drama","movie_series","mini_game","custom"] | Required segmentation strategy. Short_drama combines OCR and ASR to protect dialogue; movie_series detects and merges scenes; mini_game filters static, low-quality and pop-up screen segments; custom lets you configure scene detection and sentence protection. |
| `video_url` | `--video-url` | string | yes | - | min length: 1 | Video to segment, limited to 3.5 hours. Supports common video formats and public HTTP/HTTPS URLs. CLI inputs may be local file paths or public HTTP/HTTPS URLs. The CLI handles local file upload internally; pass the original input directly. |

### Parameter relations

- `$.min_duration` must be less than or equal to `$.max_duration`.

### Result

| Path | Type | Required | Mode | Description |
| --- | --- | --- | --- | --- |
| `request_id` | string | no | Cloud | Request id returned by the service; forwarded only when non-empty. Constraints: min length: 1. |
| `task_id` | string | yes | Cloud | Unique async task id used to query status and the terminal result. Constraints: min length: 1. |
| `task_type` | string | no | Cloud | Task type returned by the service; forwarded only when non-empty. Constraints: min length: 1. |
| `duration` | number | no | Cloud terminal | Input video duration in seconds, used for metering and billing. |
| `scene_count` | integer | no | Cloud terminal | Number of semantic scenes, available when scene_list was requested and strategy is movie_series, or custom with scene detection enabled. |
| `scenes` | array<object> | no | Cloud terminal | Semantic scene details under the same request and strategy conditions as scene_count. |
| `segment_count` | integer | no | Cloud terminal | Number of semantic segments. |
| `segments` | array<object> | no | Cloud terminal | Semantic segments in chronological order, always returned. |
| `sentence_count` | integer | no | Cloud terminal | Number of sentences, available when requested, sentence detection is enabled and recognizable speech or subtitles exist. Mini_game does not return sentences. |
| `sentences` | array<object> | no | Cloud terminal | Sentence details under the same conditions as sentence_count. Mini_game does not return this list. |
| `shot_count` | integer | no | Cloud terminal | Number of shots, available when shot_list was requested and the workflow produced shot results. |
| `shots` | array<object> | no | Cloud terminal | Shot details, available when shot_list was requested and the workflow produced them. |

After a successful Cloud call, read the returned `task_id` and check its current status. This command queries once and returns; when the task is still running, query the same task again later until terminal. Do not resubmit the business task. Use `--poll-complete` only when continuous waiting is intended; it waits without a limit by default. Pass a polling timeout or attempt limit only when specified by the user. Interrupting the CLI stops waiting without canceling the Cloud task:

```bash
byteplus-mediakit-cli shared query-task --task-id '<task_id>'
```

After a synchronous business tool succeeds or an asynchronous task reaches completed, return its actual result and any output asset URLs to the user. Use the returned URLs as inputs to requested dependent steps. Download assets or perform additional local processing only when the user requests it, and retain the Cloud result URL alongside any downloaded file. Do not present task submission as completion. Do not replace a completed Cloud output with a locally recreated file from the original input, or infer permission for local recreation from a requested duration or quality target. If a returned asset's metadata or name conflicts with the requested constraints, probe that asset with an available metadata tool before claiming a mismatch or performing a correction. Corrective processing must consume the completed Cloud output, preserving its lineage; present the original Cloud result URL alongside any corrected output and explain any confirmed limitation. Never omit an available completed result URL in favor of an unfinished correction or a local fallback.

#### Semantic segment terminal fields

Read the terminal result arrays using the documented element fields below. These are result fields, not additional request parameters.

| Result array | Documented element fields | Availability |
| --- | --- | --- |
| `segments` | `index`, `start_ms`, `end_ms`, `start`, `end`, `shot_ids`, `text`, `video_url` | Always returned in chronological order. `text` is omitted for strategies without text, including `mini_game`. `video_url` is returned only when `return_segment_videos=true` and the segment video was successfully generated. |
| `scenes` | `start_ms`, `end_ms`, `start`, `end`, `shot_ids` | Same conditions as `scene_count`: `scene_list` must be requested, with `strategy=movie_series` or `strategy=custom` and `enable_scene_detection=true`. |
| `sentences` | `speaker_id`, `text`, `start_ms`, `end_ms`, `start`, `end`, `shot_ids` | Same conditions as `sentence_count`: `sentence_list` must be requested, sentence detection must be enabled, and recognizable speech or subtitles must exist. `mini_game` does not return this array. |
| `shots` | `id`, `start_ms`, `end_ms`, `start`, `end`, `frames`, `text` | Returned when `shot_list` is requested and the workflow produces shot results. `text` is omitted when there is no text. |

For segments, `index` starts at 0 and increases in order. `start_ms` and `end_ms` use milliseconds; `start` and `end` use `HH:MM:SS.mmm`. A shot's `frames` is `[start_frame, end_frame]`. Preserve the returned `shot_ids`, shot `id`, speaker information and frame ranges when using the detailed result. Return generated segment URLs only when they are present in the terminal response.

### Machine contract

These commands read live help/schema only and do not call Cloud:

```bash
byteplus-mediakit-cli video semantic-segment-pro --help
byteplus-mediakit-cli video semantic-segment-pro --schema
```

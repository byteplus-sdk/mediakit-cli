# video-highlights-llm

## Capability

Automatically generates highlight reels from video content.

## Parameter rules

- Optional parameters may be filled only when the user specifies them, or when they can be determined accurately from user intent; do not invent values. Omit them when uncertain; ask the user if they are required to complete the task. Before submitting a business task, resolve required fields and required alternatives from the user's explicit requirements, authoritative prior results, or an unambiguous documented mapping. A media type, genre, or broad processing goal does not by itself supply missing user choices or selection criteria. If a required choice has no documented default and remains unresolved, ask a focused clarification, preserve the user's known constraints, and stop before business submission. Do not invent a custom criterion merely to make a required field present. For optional parameters, infer only values implementing a behavior the user actually requested or required by a declared parameter relation. Do not add extra processing, alignment, formatting, or tuning merely because it seems helpful.
- Required input choice: scoring_prompt or preset. If none of these alternatives has a value supplied or uniquely resolved from the user's explicit requirements and a documented mapping, ask for the missing choice and wait for the answer before any business call. Do not manufacture a value to satisfy the alternative. Media metadata, a genre label, or your own judgment does not establish the user's preferences or selection criteria. Preserve already supplied constraints while asking; do not run a metadata probe as a substitute for that answer.

## Cloud

### Command and lifecycle

- Command: `byteplus-mediakit-cli video video-highlights-llm`
- Lifecycle: async
- Return: returns `task_id`; query the terminal result next.

### Usage guide

- JSON parameters (`--target-duration`, `--time-range`) must be valid JSON. Prefer a UTF-8 file using `@params.json`, or pass correctly escaped inline JSON; field names and nesting must match the Parameters table below.
- Do not pass comma-separated values or plain text for these parameters.
- Inline JSON needs shell escaping. A single-quoted shell argument cannot contain an unescaped apostrophe, such as in It's ready; use a JSON file for text containing apostrophes.
- Array parameters (`--video-urls`) must use comma-separated values wrapped in quotes, for example `--video-urls "url1,url2"`.
- A comma inside one array item must be quoted by CSV rules, including doubled embedded double quotes, or use a JSON array file with `--video-urls @params.json`. A single string URL may contain commas; this CSV rule applies only to array encoding.
- For one operation on an ordered collection, include all items in one `--video-urls` array argument in the user's order. Submit separate tasks only for independent operations requested by the user or a documented input limit.
- `{}` and `[{}]` illustrate JSON container shapes. Replace `<parameter_json>` in command templates with valid JSON matching the parameter table, including required fields. Empty objects or arrays are valid only when the Schema permits them, including a required object with no required child fields. Required means field presence; null, empty values, zero and false are checked against their own type and constraints. Child fields marked optional remain optional; do not infer required fields from illustrative prose. Do not submit placeholders.
- Boolean parameters (`--skip-video-output`) must be written as `--flag=true` or `--flag=false`, or as bare `--flag` (equivalent to true). Do not pass a space-separated value such as `--flag true`, which the CLI treats as a positional argument.
- Pass a boolean explicitly when the user specifies true or false, including values equal to the default. Omit it when the user has not specified a value and no relation requires it.
- Requesting the main operation does not request every optional behavior. Do not enable an optional switch or fill its dependent defaults merely because input metadata differs or a changed output would be more convenient. Keep those fields omitted unless the user requested that separate behavior or a declared parameter relation requires their explicit presence.

### Example

```bash
byteplus-mediakit-cli video video-highlights-llm \
  --video-urls "url1,url2" \
  --target-duration '<target_duration_json>' \
  --scoring-prompt '<scoring_prompt>'
```

Replace placeholders with real user input only. Optional flags follow the parameter rules; do not invent URLs, files, enums, or business parameters.
Pass each scalar value as one shell argument. The quotes in these templates are illustrative: escape embedded quotes for the actual shell, or use an argument array when invoking a process. Never concatenate raw user input into shell code.

### Parameters

| Path | CLI flag | Type | Required | Default | Enum/range | Description |
| --- | --- | --- | --- | --- | --- | --- |
| `analysis_prompt` | `--analysis-prompt` | string | no | - | max length: 5000 | Optional. Additional analysis instructions. Use natural language to specify extra information for the model to focus on and extract during video analysis. Maximum length is 5000 characters. |
| `callback_args` | `--callback-args` | string | no | - | - | Custom callback argument returned unchanged when the task finishes, used to correlate business records; maximum 512 bytes. Pass only when the user provides this value; do not generate, infer, or fill it. |
| `callback_url` | `--callback-url` | string | no | - | - | Callback URL that receives this task result; when set it takes priority over the global callback URL and must start with http:// or https://. Pass only when the user provides this value; do not generate, infer, or fill it. |
| `client_token` | `--client-token` | string | no | - | - | Idempotency token; case-sensitive, at most 64 printable ASCII characters. Omit by default. Reuse the same token for retries of the same logical request. Do not mint a new token for each attempt. The CLI does not infer retry intent or auto-generate tokens. Omit by default on the first asynchronous submission. Use a user-provided value as-is when specified. A retry of the same uncertain submission must keep the original token policy: reuse its token if provided, or keep the field omitted if it was not provided. After a result exists, an explicitly requested new run must use a new token. Do not mint a different value for each retry attempt. Synchronous tasks do not support idempotency checks. The CLI runtime does not infer retry intent and does not auto-generate tokens. |
| `output_language` | `--output-language` | string | no | "en-US" | enum: ["","zh-CN","en-US"] | The output language, which can be set to zh-CN or en-US. |
| `preset` | `--preset` | string | required in an alternative: scoring_prompt or preset | - | enum: ["football","basketball","automotive","finance","product_launch","ecommerce","knowledge_share"] | Optional. The scene preset to use, which applies pre-configured analysis and scoring logic tailored for the selected scene. You must provide either preset or scoring_prompt. Supported values are football, basketball, automotive, finance, product_launch, ecommerce, and knowledge_share. |
| `queue_id` | `--queue-id` | string | no | - | - | Target queue ID for the submitted task. Omit to use the system default queue. Pass only when the user provides this value; do not generate, infer, or fill it. |
| `scoring_prompt` | `--scoring-prompt` | string | required in an alternative: scoring_prompt or preset | - | max length: 5000 | Optional. Custom scoring criteria. Describe the characteristics of what you consider a highlight using natural language. The model will score and filter clips based on this description. This parameter is required if preset is not provided. Max length: 5000 characters. |
| `skip_video_output` | `--skip-video-output` | boolean | no | false | - | Optional. Whether to skip the final video generation and output only structured highlight data. When set to false, which is the default, the tool generates the highlight video and returns its download URL along with structured data. When set to true, it returns only structured data, such as segment information, without creating a video file. Set this to true when you only need the analysis results, which improves processing speed. |
| `target_duration` | `--target-duration` | array<number> | yes | - | min items: 1; max items: 5; element min: 1 | Required. Provide an Array of Numbers where each value is a target duration in seconds for the output highlight reels. A separate video is generated for each specified duration; for example, [30, 60] produces two videos, one about 30 seconds long and another about 60. You can specify up to 5 unique durations, and each value must be at least 1 and less than the total duration of all input videos. The actual output duration may vary. The minimum value is 1. |
| `time_range` | `--time-range` | object | no | - | - | Optional. The time range within the video to analyze, in milliseconds, expressed as a half-open interval. The system selects highlights only from within this window. This setting takes effect only when a single video is provided in video_urls. |
| `time_range.end_ms` | - | integer | yes | - | min: 1 | The end time of the range, in milliseconds. It must be greater than start_ms and must not exceed the total video duration. |
| `time_range.start_ms` | - | integer | yes | - | min: 0 | The start time of the range, in milliseconds. It must be greater than or equal to 0. |
| `video_urls` | `--video-urls` | array<string> | yes | - | min items: 1; max items: 100 | This field is required. It is provided as an Array of Strings and is a list of input video URLs that will be analyzed in order. Cloud requests use publicly accessible HTTP/HTTPS URLs. Common video formats are supported, such as mp4, flv, ts, avi, mov, wmv, and mkv. Each video must not exceed 3 hours, and the cumulative duration of all input videos must not exceed 15 hours. Each array item may be a local file path or public HTTP/HTTPS URL. The declared media input format also accepts vod:// and tos:// resource URIs. The CLI handles local file upload internally; pass the original input directly. Each entry is a single value provided through media-to-url handling, which turns a media input into a URL. CLI inputs may be local file paths or public HTTP/HTTPS URLs. The declared media input format also accepts vod:// and tos:// resource URIs. The CLI handles local file upload internally; pass the original input directly. |
| `voice_language` | `--voice-language` | string | no | - | enum: ["","zh-CN","en-US","ja-JP","id-ID","es-MX","pt-BR","de-DE","fr-FR","ko-KR","fil-PH","ms-MY","th-TH","ar-SA","it-IT","bn-BD","el-GR","nl-NL","ru-RU","tr-TR","vi-VN","pl-PL","ro-RO","ne-NP","uk-UA","yue-CN"] | Spoken language for an existing ASR workflow; this setting does not enable ASR itself. Empty uses automatic detection for Chinese, English and the listed Chinese dialects. Chinese and English have full support; other listed languages can be processed with no guaranteed recognition quality. |

### Parameter relations

- At least one of (scoring_prompt is provided) or (preset is provided) must hold.

### Result

| Path | Type | Required | Mode | Description |
| --- | --- | --- | --- | --- |
| `request_id` | string | no | Cloud | Request id returned by the service; forwarded only when non-empty. Constraints: min length: 1. |
| `task_id` | string | yes | Cloud | Unique async task id used to query status and the terminal result. Constraints: min length: 1. |
| `task_type` | string | no | Cloud | Task type returned by the service; forwarded only when non-empty. Constraints: min length: 1. |
| `duration` | number | no | Cloud terminal | The total duration of all generated highlight videos, in seconds. |
| `outputs` | array<object> | no | Cloud terminal | An Array of Objects, where each object represents one generated highlight video. |
| `outputs[].duration` | number | no | Cloud terminal | The actual duration of this generated highlight video, in seconds. |
| `outputs[].index` | integer | no | Cloud terminal | The 0-based index of the output, corresponding to the order in the target_duration request array. |
| `outputs[].segments` | array<object> | no | Cloud terminal | An Array of Objects containing detailed information about all the highlight clips that make up this video. |
| `outputs[].segments[].reason` | string | no | Cloud terminal | The model's explanation for why this clip was selected as a highlight. |
| `outputs[].segments[].segment_index` | integer | no | Cloud terminal | The 0-based sequence number of this clip within the current highlight video. |
| `outputs[].segments[].source` | string | no | Cloud terminal | The URL or identifier of the source video. |
| `outputs[].segments[].spans` | array<object> | no | Cloud terminal | An Array of Objects that lists the specific time intervals of this clip within the source video. |
| `outputs[].segments[].spans[].source_end_ms` | integer | no | Cloud terminal | The end time of the clip in the source video, in milliseconds. |
| `outputs[].segments[].spans[].source_start_ms` | integer | no | Cloud terminal | The start time of the clip in the source video, in milliseconds. |
| `outputs[].segments[].summary` | string | no | Cloud terminal | The content summary generated by the model for this clip. |
| `outputs[].segments[].title` | string | no | Cloud terminal | The title generated by the model for this clip. |
| `outputs[].segments[].video_index` | integer | no | Cloud terminal | The 0-based index of the source video in the video_urls request array. |
| `outputs[].target_duration` | number | no | Cloud terminal | The requested target duration, in seconds, for this highlight video. |
| `outputs[].video_url` | string | no | Cloud terminal | The URL for the generated highlight video. The URL is valid for 24 hours. This field is empty if skip_video_output is set to true. |
| `token_usage` | object | no | Cloud terminal | Statistics on the total Large Language Model token consumption for this task. |
| `token_usage.endpoint_token_usages` | array<object> | no | Cloud terminal | A detailed list of token usage, aggregated by the Ark endpoint actually called in this task, provided as an Array of Objects. |
| `token_usage.endpoint_token_usages[].audio_cached_tokens` | integer | no | Cloud terminal | The number of cached audio tokens consumed by this endpoint. |
| `token_usage.endpoint_token_usages[].audio_tokens` | integer | no | Cloud terminal | The number of audio tokens consumed by this endpoint. |
| `token_usage.endpoint_token_usages[].cached_tokens` | integer | no | Cloud terminal | The number of cached input tokens consumed by this endpoint. |
| `token_usage.endpoint_token_usages[].endpoint` | string | no | Cloud terminal | ID of the Ark model that was actually called. |
| `token_usage.endpoint_token_usages[].input_tokens` | integer | no | Cloud terminal | The number of input tokens consumed by this endpoint. |
| `token_usage.endpoint_token_usages[].model` | string | no | Cloud terminal | The model name corresponding to the endpoint. |
| `token_usage.endpoint_token_usages[].output_tokens` | integer | no | Cloud terminal | The number of output tokens generated by this endpoint. |
| `token_usage.endpoint_token_usages[].think_tokens` | integer | no | Cloud terminal | The number of tokens consumed during thinking by this endpoint. |
| `token_usage.total_audio_cached_tokens` | integer | no | Cloud terminal | Total number of cached tokens from audio input. |
| `token_usage.total_audio_tokens` | integer | no | Cloud terminal | Total number of tokens from audio input. |
| `token_usage.total_cached_tokens` | integer | no | Cloud terminal | Total number of input tokens that hit the context cache. |
| `token_usage.total_input_tokens` | integer | no | Cloud terminal | Total input tokens consumed by the task, including text, image, and audio. |
| `token_usage.total_output_tokens` | integer | no | Cloud terminal | Total output tokens generated by the task. |
| `token_usage.total_think_tokens` | integer | no | Cloud terminal | Total tokens consumed during the model's thinking process. Returns 0 if not supported by the model. |

After a successful Cloud call, read the returned `task_id` and check its current status. This command queries once and returns; when the task is still running, query the same task again later until terminal. Do not resubmit the business task. Use `--poll-complete` only when continuous waiting is intended; it waits without a limit by default. Pass a polling timeout or attempt limit only when specified by the user. Interrupting the CLI stops waiting without canceling the Cloud task:

```bash
byteplus-mediakit-cli shared query-task --task-id '<task_id>'
```

After a synchronous business tool succeeds or an asynchronous task reaches completed, return its actual result and any output asset URLs to the user. Use the returned URLs as inputs to requested dependent steps. Download assets or perform additional local processing only when the user requests it, and retain the Cloud result URL alongside any downloaded file. Do not present task submission as completion. Do not replace a completed Cloud output with a locally recreated file from the original input, or infer permission for local recreation from a requested duration or quality target. If a returned asset's metadata or name conflicts with the requested constraints, probe that asset with an available metadata tool before claiming a mismatch or performing a correction. Corrective processing must consume the completed Cloud output, preserving its lineage; present the original Cloud result URL alongside any corrected output and explain any confirmed limitation. Never omit an available completed result URL in favor of an unfinished correction or a local fallback.

#### Highlight output order

The terminal `outputs` array is returned in the same order as the request's `target_duration` array. Each output's `index` is 0-based and corresponds to that request order. Match each generated highlight video to its requested duration using this ordering and the returned `target_duration`; do not reorder outputs by their actual duration.

### Machine contract

These commands read live help/schema only and do not call Cloud:

```bash
byteplus-mediakit-cli video video-highlights-llm --help
byteplus-mediakit-cli video video-highlights-llm --schema
```

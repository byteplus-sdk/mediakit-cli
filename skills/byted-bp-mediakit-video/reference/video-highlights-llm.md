# video-highlights-llm

## Capability

Automatically generates highlight reels from video content.

## Parameter rules

- Optional parameters may be filled only when the user specifies them, or when they can be determined accurately from user intent; do not invent values. Omit them when uncertain; ask the user if they are required to complete the task.
- If a user-provided `scoring_prompt` or `analysis_prompt` exceeds the documented max length, refuse or ask them to shorten it. Do not silently truncate and submit.

## Cloud

### Command and lifecycle

- Command: `byteplus-mediakit-cli video video-highlights-llm`
- Lifecycle: async
- Return: returns `task_id`; query the terminal result next.

### Usage guide

- Object or object array parameters (`--target-duration`, `--time-range`) must be passed as valid JSON strings wrapped in single quotes; field names and nesting must match the Parameters table below.
- Do not pass comma-separated values or plain text for these parameters.
- Array parameters (`--video-urls`) must use comma-separated values wrapped in quotes, for example `--video-urls "url1,url2"`.
- Individual file names or URLs must not contain commas (`,`); if that happens, ask the user for comma-free paths or URLs before calling.
- Boolean parameters (`--skip-video-output`) must be written as `--flag=true` or `--flag=false`, or as bare `--flag` (equivalent to true). Do not pass a space-separated value such as `--flag true`, which the CLI treats as a positional argument.
- Omit boolean flags when they would repeat the default value.

### Example

```bash
MEDIAKIT_BYTEPLUS_SURFACE=skill MEDIAKIT_BYTEPLUS_RUNTIME=<runtime> \
  byteplus-mediakit-cli video video-highlights-llm \
  --video-urls "url1,url2" \
  --target-duration <target_duration> \
  --scoring-prompt <scoring_prompt>
```

Replace placeholders with real user input only. Optional flags follow the parameter rules; do not invent URLs, files, enums, or business parameters.

### Parameters

| Path | CLI flag | Type | Required | Default | Enum/range | Description |
| --- | --- | --- | --- | --- | --- | --- |
| `analysis_prompt` | `--analysis-prompt` | string | no | - | max length: 5000 | Optional. Additional analysis instructions. Use natural language to specify extra information for the model to focus on and extract during video analysis. Maximum length is 5000 characters. |
| `callback_args` | `--callback-args` | string | no | - | - | Custom callback argument returned unchanged when the task finishes, used to correlate business records; maximum 512 bytes. Pass only when the user provides this value; do not generate, infer, or fill it. |
| `callback_url` | `--callback-url` | string | no | - | - | Callback URL that receives this task result; when set it takes priority over the global callback URL and must start with http:// or https://. Pass only when the user provides this value; do not generate, infer, or fill it. |
| `client_token` | `--client-token` | string | no | - | - | Idempotency token; case-sensitive, at most 64 printable ASCII characters. Omit by default. Reuse the same token for retries of the same logical request. Do not mint a new token for each attempt. The CLI does not infer retry intent or auto-generate tokens. Omit by default. Use the user-provided value as-is when specified. When the user explicitly retries, the same logical request must reuse the same token. If a token already exists, reuse it; if earlier requests had no token, you may create one starting with this retry and keep reusing it, but that token does not provide retroactive idempotency for earlier requests. A change in business parameters is a new request and must not reuse the old token. Do not mint a different value for each attempt. The CLI runtime does not infer retry intent and does not auto-generate tokens. |
| `preset` | `--preset` | string | no | - | enum: ["football","basketball","automotive","finance","product_launch","ecommerce","knowledge_share","short_drama"] | Optional. The scene preset to use, which applies pre-configured analysis and scoring logic tailored for the selected scene. You must provide either preset or scoring_prompt. Supported values are football, basketball, automotive, finance, product_launch, ecommerce, and knowledge_share. |
| `queue_id` | `--queue-id` | string | no | - | - | Target queue ID for the submitted task. Omit to use the system default queue. Pass only when the user provides this value; do not generate, infer, or fill it. |
| `scoring_prompt` | `--scoring-prompt` | string | no | - | max length: 5000 | Optional. Custom scoring criteria. Describe the characteristics of what you consider a highlight using natural language. The model will score and filter clips based on this description. This parameter is required if preset is not provided. Max length: 5000 characters. |
| `skip_video_output` | `--skip-video-output` | boolean | no | false | - | Optional. Whether to skip the final video generation and output only structured highlight data. When set to false, which is the default, the tool generates the highlight video and returns its download URL along with structured data. When set to true, it returns only structured data, such as segment information, without creating a video file. Set this to true when you only need the analysis results, which improves processing speed. |
| `target_duration` | `--target-duration` | array<number> | yes | - | min items: 1; max items: 5; element min: 1 | Required. Provide an Array of Numbers where each value is a target duration in seconds for the output highlight reels. A separate video is generated for each specified duration; for example, [30, 60] produces two videos, one about 30 seconds long and another about 60. You can specify up to 5 unique durations, and each value must be at least 1 and less than the total duration of all input videos. The actual output duration may vary. The minimum value is 1. |
| `time_range` | `--time-range` | object | no | - | - | Optional. The time range within the video to analyze, in milliseconds, expressed as a half-open interval. The system selects highlights only from within this window. This setting takes effect only when a single video is provided in video_urls. |
| `time_range.end_ms` | - | integer | yes | - | min: 1 | The end time of the range, in milliseconds. It must be greater than start_ms and must not exceed the total video duration. |
| `time_range.start_ms` | - | integer | yes | - | min: 0 | The start time of the range, in milliseconds. It must be greater than or equal to 0. |
| `video_urls` | `--video-urls` | array<string> | yes | - | min items: 1; max items: 100 | This field is required. It is provided as an Array of Strings and is a list of input video URLs that will be analyzed in order. Only publicly accessible HTTP/HTTPS URLs are supported. Common video formats are supported, such as mp4, flv, ts, avi, mov, wmv, and mkv. Each video must not exceed 3 hours, and the cumulative duration of all input videos must not exceed 15 hours. Each entry is a single value provided through media-to-url handling, which turns a media input into a URL. Pass multiple values as comma-separated strings or by repeating the flag; do not pass a JSON array string. |

### Result

| Path | Type | Required | Mode | Description |
| --- | --- | --- | --- | --- |
| `request_id` | string | no | Cloud | Request id returned by the service; forwarded only when non-empty. |
| `task_id` | string | yes | Cloud | Unique async task id used to query status and the terminal result. |
| `task_type` | string | no | Cloud | Task type returned by the service; forwarded only when non-empty. |
| `result.duration` | number | no | Cloud terminal | The total duration (in seconds) of all generated output videos. |
| `result.outputs` | array<object> | no | Cloud terminal | An Array of Objects, where each object represents one generated highlight video. |
| `result.outputs[].duration` | number | no | Cloud terminal | The actual duration, in seconds, of the generated highlight video. |
| `result.outputs[].index` | integer | no | Cloud terminal | The 0-based index of the output, corresponding to the order in the target_duration request array. |
| `result.outputs[].segments` | array<object> | no | Cloud terminal | An Array of Objects containing detailed information about all the highlight clips that make up this video. |
| `result.outputs[].segments[].reason` | string | no | Cloud terminal | The model's explanation for why this clip was selected as a highlight. |
| `result.outputs[].segments[].segment_index` | integer | no | Cloud terminal | The 0-based sequence number of this clip within the current highlight video. |
| `result.outputs[].segments[].source` | string | no | Cloud terminal | The URL or identifier of the source video. |
| `result.outputs[].segments[].spans` | array<object> | no | Cloud terminal | An Array of Objects that lists the specific time intervals of this clip within the source video. |
| `result.outputs[].segments[].spans[].source_end_ms` | integer | no | Cloud terminal | The end time of the clip in the source video, in milliseconds. |
| `result.outputs[].segments[].spans[].source_start_ms` | integer | no | Cloud terminal | The start time of the clip in the source video, in milliseconds. |
| `result.outputs[].segments[].summary` | string | no | Cloud terminal | The content summary generated by the model for this clip. |
| `result.outputs[].segments[].title` | string | no | Cloud terminal | The title generated by the model for this clip. |
| `result.outputs[].segments[].video_index` | integer | no | Cloud terminal | The 0-based index of the source video in the video_urls request array. |
| `result.outputs[].target_duration` | number | no | Cloud terminal | The requested target duration, in seconds, for this highlight video. |
| `result.outputs[].video_url` | string | no | Cloud terminal | The URL for the generated highlight video. The URL is valid for 24 hours. This field is empty if skip_video_output is set to true. |
| `result.token_usage` | object | no | Cloud terminal | Statistics on the total Large Language Model token consumption for this task. |
| `result.token_usage.endpoint_token_usages` | array<object> | no | Cloud terminal | A detailed list of token usage, aggregated by the Ark endpoint actually called in this task, provided as an Array of Objects. |
| `result.token_usage.endpoint_token_usages[].audio_cached_tokens` | integer | no | Cloud terminal | The number of cached audio tokens consumed by this endpoint. |
| `result.token_usage.endpoint_token_usages[].audio_tokens` | integer | no | Cloud terminal | The number of audio tokens consumed by this endpoint. |
| `result.token_usage.endpoint_token_usages[].cached_tokens` | integer | no | Cloud terminal | The number of cached input tokens consumed by this endpoint. |
| `result.token_usage.endpoint_token_usages[].endpoint` | string | no | Cloud terminal | ID of the Ark model that was actually called. |
| `result.token_usage.endpoint_token_usages[].input_tokens` | integer | no | Cloud terminal | The number of input tokens consumed by this endpoint. |
| `result.token_usage.endpoint_token_usages[].layer_token_usages` | array<object> | no | Cloud terminal | The token usage breakdown for each layer. |
| `result.token_usage.endpoint_token_usages[].layer_token_usages[].audio_cached_tokens` | integer | no | Cloud terminal | The number of cached audio tokens. |
| `result.token_usage.endpoint_token_usages[].layer_token_usages[].audio_tokens` | integer | no | Cloud terminal | . |
| `result.token_usage.endpoint_token_usages[].layer_token_usages[].cached_tokens` | integer | no | Cloud terminal | . |
| `result.token_usage.endpoint_token_usages[].layer_token_usages[].input_tokens` | integer | no | Cloud terminal | . |
| `result.token_usage.endpoint_token_usages[].layer_token_usages[].output_tokens` | integer | no | Cloud terminal | The number of output tokens generated in this range. |
| `result.token_usage.endpoint_token_usages[].layer_token_usages[].prompt_token_range` | string | no | Cloud terminal | Prompt token range: 0~32000, 32000~128000, 128000~256000, or 256000+. |
| `result.token_usage.endpoint_token_usages[].layer_token_usages[].think_tokens` | integer | no | Cloud terminal | The number of thinking tokens consumed in this range. |
| `result.token_usage.endpoint_token_usages[].model` | string | no | Cloud terminal | The model name corresponding to the endpoint. |
| `result.token_usage.endpoint_token_usages[].output_tokens` | integer | no | Cloud terminal | The number of output tokens generated by this endpoint. |
| `result.token_usage.endpoint_token_usages[].think_tokens` | integer | no | Cloud terminal | The number of tokens consumed during thinking by this endpoint. |
| `result.token_usage.total_audio_cached_tokens` | integer | no | Cloud terminal | Total number of cached tokens from audio input. |
| `result.token_usage.total_audio_tokens` | integer | no | Cloud terminal | Total number of tokens from audio input. |
| `result.token_usage.total_cached_tokens` | integer | no | Cloud terminal | Total number of input tokens that hit the context cache. |
| `result.token_usage.total_input_tokens` | integer | no | Cloud terminal | Total input tokens consumed by the task, including text, image, and audio. |
| `result.token_usage.total_output_tokens` | integer | no | Cloud terminal | Total output tokens generated by the task. |
| `result.token_usage.total_think_tokens` | integer | no | Cloud terminal | Total tokens consumed during the model's thinking process. Returns 0 if not supported by the model. |

After a successful Cloud call, read `task_id` and query the terminal result:

```bash
MEDIAKIT_BYTEPLUS_SURFACE=skill MEDIAKIT_BYTEPLUS_RUNTIME=<runtime> \
  byteplus-mediakit-cli shared query-task --task-id <task_id> --poll-complete
```

### Machine contract

These commands read live help/schema only and do not call Cloud:

```bash
byteplus-mediakit-cli video video-highlights-llm --help
byteplus-mediakit-cli video video-highlights-llm --schema
```

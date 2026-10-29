# mix-audio

## Capability

Mixes multiple audio files, such as background music, sound effects, and voice, into one new audio file.

## Parameter rules

- Optional parameters may be filled only when the user specifies them, or when they can be determined accurately from user intent; do not invent values. Omit them when uncertain; ask the user if they are required to complete the task.

## Cloud

### Command and lifecycle

- Command: `byteplus-mediakit-cli editing mix-audio`
- Lifecycle: async
- Return: returns `task_id`; query the terminal result next.

### Usage guide

- Array parameters (`--audio-urls`) must use comma-separated values wrapped in quotes, for example `--audio-urls "url1,url2"`.
- Individual file names or URLs must not contain commas (`,`); if that happens, ask the user for comma-free paths or URLs before calling.

### Example

```bash
MEDIAKIT_BYTEPLUS_SURFACE=skill MEDIAKIT_BYTEPLUS_RUNTIME=<runtime> \
  byteplus-mediakit-cli editing mix-audio \
  --audio-urls "url1,url2"
```

Replace placeholders with real user input only. Optional flags follow the parameter rules; do not invent URLs, files, enums, or business parameters.

### Parameters

| Path | CLI flag | Type | Required | Default | Enum/range | Description |
| --- | --- | --- | --- | --- | --- | --- |
| `audio_urls` | `--audio-urls` | array<string> | yes | - | min items: 1; max items: 100 | The list of audio file URLs to be mixed. This field is required and must contain at least 1 item and no more than 100 items. Only publicly accessible HTTP or HTTPS URLs are supported. Common supported input audio formats include mp3, wav, and flac. A single input file is recommended not to exceed 10 GB. Pass multiple values as comma-separated strings or by repeating the flag; do not pass a JSON array string. |
| `callback_args` | `--callback-args` | string | no | - | - | Custom callback argument returned unchanged when the task finishes, used to correlate business records; maximum 512 bytes. Pass only when the user provides this value; do not generate, infer, or fill it. |
| `callback_url` | `--callback-url` | string | no | - | - | Callback URL that receives this task result; when set it takes priority over the global callback URL and must start with http:// or https://. Pass only when the user provides this value; do not generate, infer, or fill it. |
| `client_token` | `--client-token` | string | no | - | - | Idempotency token; case-sensitive, at most 64 printable ASCII characters. Omit by default. Reuse the same token for retries of the same logical request. Do not mint a new token for each attempt. The CLI does not infer retry intent or auto-generate tokens. Omit by default. Use the user-provided value as-is when specified. When the user explicitly retries, the same logical request must reuse the same token. If a token already exists, reuse it; if earlier requests had no token, you may create one starting with this retry and keep reusing it, but that token does not provide retroactive idempotency for earlier requests. A change in business parameters is a new request and must not reuse the old token. Do not mint a different value for each attempt. The CLI runtime does not infer retry intent and does not auto-generate tokens. |
| `format` | `--format` | string | no | "m4a" | enum: ["mp3","m4a","ogg","flac","wav"] | Specifies the output audio format. Optional; defaults to m4a. Supports mp3, m4a, ogg, flac, and wav. |
| `queue_id` | `--queue-id` | string | no | - | - | Target queue ID for the submitted task. Omit to use the system default queue. Pass only when the user provides this value; do not generate, infer, or fill it. |

### Result

| Path | Type | Required | Mode | Description |
| --- | --- | --- | --- | --- |
| `request_id` | string | no | Cloud | Request id returned by the service; forwarded only when non-empty. |
| `task_id` | string | yes | Cloud | Unique async task id used to query status and the terminal result. |
| `task_type` | string | no | Cloud | Task type returned by the service; forwarded only when non-empty. |
| `result.audio_url` | string | no | Cloud terminal | The download URL for the generated mixed audio file. It is an HTTPS temporary download link that is valid for 24 hours by default. |
| `result.duration` | number | no | Cloud terminal | The total duration of the output audio, measured in seconds. The output duration is based on the longest input audio file. |

After a successful Cloud call, read `task_id` and query the terminal result:

```bash
MEDIAKIT_BYTEPLUS_SURFACE=skill MEDIAKIT_BYTEPLUS_RUNTIME=<runtime> \
  byteplus-mediakit-cli shared query-task --task-id <task_id> --poll-complete
```

### Machine contract

These commands read live help/schema only and do not call Cloud:

```bash
byteplus-mediakit-cli editing mix-audio --help
byteplus-mediakit-cli editing mix-audio --schema
```

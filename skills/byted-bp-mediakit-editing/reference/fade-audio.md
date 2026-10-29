# fade-audio

## Capability

Applies a fade-in or fade-out effect to a standalone audio file. It processes an independent audio source and returns a new audio file with the specified volume transitions.

## Parameter rules

- Optional parameters may be filled only when the user specifies them, or when they can be determined accurately from user intent; do not invent values. Omit them when uncertain; ask the user if they are required to complete the task.

## Cloud

### Command and lifecycle

- Command: `byteplus-mediakit-cli editing fade-audio`
- Lifecycle: async
- Return: returns `task_id`; query the terminal result next.

### Parameters

| Path | CLI flag | Type | Required | Default | Enum/range | Description |
| --- | --- | --- | --- | --- | --- | --- |
| `audio_url` | `--audio-url` | string | yes | - | - | The URL of the audio to be processed. This parameter is required. Only publicly accessible HTTP/HTTPS URLs are supported as file sources. Supported common audio formats include mp3, m4a, and wav. It is recommended that the input file size not exceed 10 GB. |
| `callback_args` | `--callback-args` | string | no | - | - | Custom callback argument returned unchanged when the task finishes, used to correlate business records; maximum 512 bytes. Pass only when the user provides this value; do not generate, infer, or fill it. |
| `callback_url` | `--callback-url` | string | no | - | - | Callback URL that receives this task result; when set it takes priority over the global callback URL and must start with http:// or https://. Pass only when the user provides this value; do not generate, infer, or fill it. |
| `client_token` | `--client-token` | string | no | - | - | Idempotency token; case-sensitive, at most 64 printable ASCII characters. Omit by default. Reuse the same token for retries of the same logical request. Do not mint a new token for each attempt. The CLI does not infer retry intent or auto-generate tokens. Omit by default. Use the user-provided value as-is when specified. When the user explicitly retries, the same logical request must reuse the same token. If a token already exists, reuse it; if earlier requests had no token, you may create one starting with this retry and keep reusing it, but that token does not provide retroactive idempotency for earlier requests. A change in business parameters is a new request and must not reuse the old token. Do not mint a different value for each attempt. The CLI runtime does not infer retry intent and does not auto-generate tokens. |
| `fade_in_duration` | `--fade-in-duration` | number | no | 1 | min: 0 | Specifies the audio fade-in duration in seconds, and supports up to 3 decimal places. A value of 0 applies no fade-in. This parameter is optional. When it is omitted, its default value is 1 and a 1-second fade-in is applied. |
| `fade_out_duration` | `--fade-out-duration` | number | no | 1 | min: 0 | Specifies the audio fade-out duration in seconds. This parameter is optional and supports up to 3 decimal places. When omitted, it defaults to 1, applying a 1-second fade-in; a value of 0 applies no fade-in. |
| `format` | `--format` | string | no | "mp3" | enum: ["mp3","m4a","ogg","flac","wav"] | Specifies the output audio format. This parameter is optional and supports mp3, m4a, ogg, flac, and wav. |
| `queue_id` | `--queue-id` | string | no | - | - | Target queue ID for the submitted task. Omit to use the system default queue. Pass only when the user provides this value; do not generate, infer, or fill it. |

### Example

```bash
MEDIAKIT_BYTEPLUS_SURFACE=skill MEDIAKIT_BYTEPLUS_RUNTIME=<runtime> \
  byteplus-mediakit-cli editing fade-audio \
  --audio-url <audio_url>
```

Replace placeholders with real user input only. Optional flags follow the parameter rules; do not invent URLs, files, enums, or business parameters.

### Result

| Path | Type | Required | Mode | Description |
| --- | --- | --- | --- | --- |
| `request_id` | string | no | Cloud | Request id returned by the service; forwarded only when non-empty. |
| `task_id` | string | yes | Cloud | Unique async task id used to query status and the terminal result. |
| `task_type` | string | no | Cloud | Task type returned by the service; forwarded only when non-empty. |
| `result.audio_url` | string | no | Cloud terminal | The URL of the processed audio file. This is an HTTPS temporary download link that is valid for 24 hours by default. |
| `result.duration` | number | no | Cloud terminal | The total duration of the output audio, in seconds. |

After a successful Cloud call, read `task_id` and query the terminal result:

```bash
MEDIAKIT_BYTEPLUS_SURFACE=skill MEDIAKIT_BYTEPLUS_RUNTIME=<runtime> \
  byteplus-mediakit-cli shared query-task --task-id <task_id> --poll-complete
```

### Machine contract

These commands read live help/schema only and do not call Cloud:

```bash
byteplus-mediakit-cli editing fade-audio --help
byteplus-mediakit-cli editing fade-audio --schema
```

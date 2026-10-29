# adjust-audio-speed

## Capability

Adjusts the playback speed of an audio file to produce fast-forward or slow-motion effects while keeping the pitch natural.

## Parameter rules

- Optional parameters may be filled only when the user specifies them, or when they can be determined accurately from user intent; do not invent values. Omit them when uncertain; ask the user if they are required to complete the task.
- Omit output_format/format unless the user names a documented format. Matching the source container or file extension is inventing. If the user names a documented format, pass it even when it equals the default. After a call succeeds or is intercepted, do not retry the same tool with an invented format.
- If the requested speed is outside the documented range, refuse or offer the documented bound in one user-facing reply. Do not clamp silently, chain multiple speed calls to exceed the bound, or keep retrying until the command budget is exhausted.

## Cloud

### Command and lifecycle

- Command: `byteplus-mediakit-cli editing adjust-audio-speed`
- Lifecycle: async
- Return: returns `task_id`; query the terminal result next.

### Parameters

| Path | CLI flag | Type | Required | Default | Enum/range | Description |
| --- | --- | --- | --- | --- | --- | --- |
| `audio_url` | `--audio-url` | string | yes | - | - | This is the URL of the audio file to be sped up or slowed down, and it is required. Only publicly accessible HTTP/HTTPS URLs are supported as the file source. Commonly supported input audio formats include mp3, m4a, and wav. It is recommended that the input file size not exceed 10 GB. |
| `callback_args` | `--callback-args` | string | no | - | - | Custom callback argument returned unchanged when the task finishes, used to correlate business records; maximum 512 bytes. Pass only when the user provides this value; do not generate, infer, or fill it. |
| `callback_url` | `--callback-url` | string | no | - | - | Callback URL that receives this task result; when set it takes priority over the global callback URL and must start with http:// or https://. Pass only when the user provides this value; do not generate, infer, or fill it. |
| `client_token` | `--client-token` | string | no | - | - | Idempotency token; case-sensitive, at most 64 printable ASCII characters. Omit by default. Reuse the same token for retries of the same logical request. Do not mint a new token for each attempt. The CLI does not infer retry intent or auto-generate tokens. Omit by default. Use the user-provided value as-is when specified. When the user explicitly retries, the same logical request must reuse the same token. If a token already exists, reuse it; if earlier requests had no token, you may create one starting with this retry and keep reusing it, but that token does not provide retroactive idempotency for earlier requests. A change in business parameters is a new request and must not reuse the old token. Do not mint a different value for each attempt. The CLI runtime does not infer retry intent and does not auto-generate tokens. |
| `format` | `--format` | string | no | "m4a" | enum: ["mp3","m4a","ogg","flac","wav"] | This parameter specifies the output audio format and is optional. It supports mp3, m4a, ogg, flac, and wav. The default is m4a. |
| `queue_id` | `--queue-id` | string | no | - | - | Target queue ID for the submitted task. Omit to use the system default queue. Pass only when the user provides this value; do not generate, infer, or fill it. |
| `speed` | `--speed` | number | no | 1 | min: 0.1; max: 4 | The playback speed multiplier for the audio. The default is 1.0, which preserves the original speed. This parameter is optional and can be set from a minimum of 0.1 to a maximum of 4.0. A value greater than 1.0 produces fast-forward, and a value less than 1.0 produces slow-motion. |

### Example

```bash
MEDIAKIT_BYTEPLUS_SURFACE=skill MEDIAKIT_BYTEPLUS_RUNTIME=<runtime> \
  byteplus-mediakit-cli editing adjust-audio-speed \
  --audio-url <audio_url>
```

Replace placeholders with real user input only. Optional flags follow the parameter rules; do not invent URLs, files, enums, or business parameters.

### Result

| Path | Type | Required | Mode | Description |
| --- | --- | --- | --- | --- |
| `request_id` | string | no | Cloud | Request id returned by the service; forwarded only when non-empty. |
| `task_id` | string | yes | Cloud | Unique async task id used to query status and the terminal result. |
| `task_type` | string | no | Cloud | Task type returned by the service; forwarded only when non-empty. |
| `result.audio_url` | string | no | Cloud terminal | The download URL of the generated speed-adjusted audio file. This is an HTTPS temporary download link that is valid for 24 hours by default. |
| `result.duration` | number | no | Cloud terminal | The total duration of the output audio, in seconds. |

After a successful Cloud call, read `task_id` and query the terminal result:

```bash
MEDIAKIT_BYTEPLUS_SURFACE=skill MEDIAKIT_BYTEPLUS_RUNTIME=<runtime> \
  byteplus-mediakit-cli shared query-task --task-id <task_id> --poll-complete
```

### Machine contract

These commands read live help/schema only and do not call Cloud:

```bash
byteplus-mediakit-cli editing adjust-audio-speed --help
byteplus-mediakit-cli editing adjust-audio-speed --schema
```

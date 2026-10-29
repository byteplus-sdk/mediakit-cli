# separate-voice

## Capability

Separates the vocals and background audio from an audio or video file into two independent audio files.

## Parameter rules

- Optional parameters may be filled only when the user specifies them, or when they can be determined accurately from user intent; do not invent values. Omit them when uncertain; ask the user if they are required to complete the task.
- When a later step needs the isolated vocals, pass `result.voice_audio_url` as that tool's `audio_url`. Prefer the vocal track over `background_audio_url` unless the user asked for accompaniment.
- Omit output_format/format unless the user names a documented format. Matching the source container or file extension is inventing. If the user names a documented format, pass it even when it equals the default. After a call succeeds or is intercepted, do not retry the same tool with an invented format.

## Cloud

### Command and lifecycle

- Command: `byteplus-mediakit-cli audio separate-voice`
- Lifecycle: async
- Return: returns `task_id`; query the terminal result next.

### Parameters

| Path | CLI flag | Type | Required | Default | Enum/range | Description |
| --- | --- | --- | --- | --- | --- | --- |
| `audio_url` | `--audio-url` | string | no | - | - | The URL of the audio to be processed. It supports only publicly accessible HTTP or HTTPS URLs, and supports mainstream audio formats such as mp3, m4a, and wav. If both audio_url and video_url are provided, video_url takes priority. |
| `callback_args` | `--callback-args` | string | no | - | - | Custom callback argument returned unchanged when the task finishes, used to correlate business records; maximum 512 bytes. Pass only when the user provides this value; do not generate, infer, or fill it. |
| `callback_url` | `--callback-url` | string | no | - | - | Callback URL that receives this task result; when set it takes priority over the global callback URL and must start with http:// or https://. Pass only when the user provides this value; do not generate, infer, or fill it. |
| `client_token` | `--client-token` | string | no | - | - | Idempotency token; case-sensitive, at most 64 printable ASCII characters. Omit by default. Reuse the same token for retries of the same logical request. Do not mint a new token for each attempt. The CLI does not infer retry intent or auto-generate tokens. Omit by default. Use the user-provided value as-is when specified. When the user explicitly retries, the same logical request must reuse the same token. If a token already exists, reuse it; if earlier requests had no token, you may create one starting with this retry and keep reusing it, but that token does not provide retroactive idempotency for earlier requests. A change in business parameters is a new request and must not reuse the old token. Do not mint a different value for each attempt. The CLI runtime does not infer retry intent and does not auto-generate tokens. |
| `output_format` | `--output-format` | string | no | "mp3" | enum: ["aac","mp3","wav","m4a","flac"] | Specifies the output format of the separated audio. This parameter is optional and can be set to aac, mp3, wav, m4a, or flac. |
| `video_url` | `--video-url` | string | no | - | - | The URL of the video to be processed. Supports mainstream video formats such as mp4, flv, ts, avi, mov, wmv, and mkv. Supports only publicly accessible HTTP or HTTPS URLs. If both video_url and audio_url are present, video_url takes priority. |

### Example

```bash
MEDIAKIT_BYTEPLUS_SURFACE=skill MEDIAKIT_BYTEPLUS_RUNTIME=<runtime> \
  byteplus-mediakit-cli audio separate-voice
```

Replace placeholders with real user input only. Optional flags follow the parameter rules; do not invent URLs, files, enums, or business parameters.

### Result

| Path | Type | Required | Mode | Description |
| --- | --- | --- | --- | --- |
| `request_id` | string | no | Cloud | Request id returned by the service; forwarded only when non-empty. |
| `task_id` | string | yes | Cloud | Unique async task id used to query status and the terminal result. |
| `task_type` | string | no | Cloud | Task type returned by the service; forwarded only when non-empty. |
| `result.background_audio_url` | string | no | Cloud terminal | The download URL of the separated background track. The background track format matches the requested output_format, with AAC as the default. It is a temporary HTTPS download URL valid for 24 hours. It is returned only when scene is Audio or Music. |
| `result.duration` | number | no | Cloud terminal | The total duration of the input video or audio, measured in seconds. |
| `result.voice_audio_url` | string | no | Cloud terminal | The download URL of the separated vocal track. The vocal track format matches the requested output_format, with AAC as the default. This is a temporary HTTPS download URL valid for 24 hours. |

After a successful Cloud call, read `task_id` and query the terminal result:

```bash
MEDIAKIT_BYTEPLUS_SURFACE=skill MEDIAKIT_BYTEPLUS_RUNTIME=<runtime> \
  byteplus-mediakit-cli shared query-task --task-id <task_id> --poll-complete
```

### Machine contract

These commands read live help/schema only and do not call Cloud:

```bash
byteplus-mediakit-cli audio separate-voice --help
byteplus-mediakit-cli audio separate-voice --schema
```

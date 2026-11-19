# separate-voice

## Capability

Separates the vocals and background audio from an audio or video file into two independent audio files.

## Parameter rules

- Optional parameters may be filled only when the user specifies them, or when they can be determined accurately from user intent; do not invent values. Omit them when uncertain; ask the user if they are required to complete the task. Before submitting a business task, resolve required fields and required alternatives from the user's explicit requirements, authoritative prior results, or an unambiguous documented mapping. A media type, genre, or broad processing goal does not by itself supply missing user choices or selection criteria. If a required choice has no documented default and remains unresolved, ask a focused clarification, preserve the user's known constraints, and stop before business submission. Do not invent a custom criterion merely to make a required field present. For optional parameters, infer only values implementing a behavior the user actually requested or required by a declared parameter relation. Do not add extra processing, alignment, formatting, or tuning merely because it seems helpful.
- Required input choice: video_url or audio_url. If none of these alternatives has a value supplied or uniquely resolved from the user's explicit requirements and a documented mapping, ask for the missing choice and wait for the answer before any business call. Do not manufacture a value to satisfy the alternative. Media metadata, a genre label, or your own judgment does not establish the user's preferences or selection criteria. Preserve already supplied constraints while asking; do not run a metadata probe as a substitute for that answer.
- Omit `output_format` unless the user names a documented format or explicitly requires preserving the source format. For format preservation, use a documented original value when available; otherwise confirm the input format from reliable metadata and pass its supported value. Ask when the source format is unknown or unsupported. If the user names a documented format, pass it even when it equals the default. After a call succeeds or is intercepted, do not retry the same tool with an invented format.

## Cloud

### Command and lifecycle

- Command: `byteplus-mediakit-cli audio separate-voice`
- Lifecycle: async
- Return: returns `task_id`; query the terminal result next.

### Parameters

| Path | CLI flag | Type | Required | Default | Enum/range | Description |
| --- | --- | --- | --- | --- | --- | --- |
| `audio_url` | `--audio-url` | string | required in an alternative: video_url or audio_url | - | - | The URL of the audio to be processed. It supports only publicly accessible HTTP or HTTPS URLs, and supports mainstream audio formats such as mp3, m4a, and wav. If both audio_url and video_url are provided, video_url takes priority. CLI inputs may be local file paths or public HTTP/HTTPS URLs. The CLI handles local file upload internally; pass the original input directly. |
| `callback_args` | `--callback-args` | string | no | - | - | Custom callback argument returned unchanged when the task finishes, used to correlate business records; maximum 512 bytes. Pass only when the user provides this value; do not generate, infer, or fill it. |
| `callback_url` | `--callback-url` | string | no | - | - | Callback URL that receives this task result; when set it takes priority over the global callback URL and must start with http:// or https://. Pass only when the user provides this value; do not generate, infer, or fill it. |
| `client_token` | `--client-token` | string | no | - | - | Idempotency token; case-sensitive, at most 64 printable ASCII characters. Omit by default. Reuse the same token for retries of the same logical request. Do not mint a new token for each attempt. The CLI does not infer retry intent or auto-generate tokens. Omit by default on the first asynchronous submission. Use a user-provided value as-is when specified. A retry of the same uncertain submission must keep the original token policy: reuse its token if provided, or keep the field omitted if it was not provided. After a result exists, an explicitly requested new run must use a new token. Do not mint a different value for each retry attempt. Synchronous tasks do not support idempotency checks. The CLI runtime does not infer retry intent and does not auto-generate tokens. |
| `output_format` | `--output-format` | string | no | "mp3" | enum: ["aac","mp3","wav","m4a","flac"] | Encoding for the separated audio tracks, default MP3. AAC, WAV, M4A and FLAC are also available. |
| `scene` | `--scene` | string | no | "Audio" | enum: ["Audio","Drama","Music","Narrate","Game"] | Select the separation strategy for the recording. Audio is the default. Audio and Music return vocals and background; Drama, Narrate and Game return vocals, music and sound effects. |
| `video_url` | `--video-url` | string | required in an alternative: video_url or audio_url | - | - | The URL of the video to be processed. Supports mainstream video formats such as mp4, flv, ts, avi, mov, wmv, and mkv. Supports only publicly accessible HTTP or HTTPS URLs. If both video_url and audio_url are present, video_url takes priority. CLI inputs may be local file paths or public HTTP/HTTPS URLs. The declared media input format also accepts vod:// and tos:// resource URIs. The CLI handles local file upload internally; pass the original input directly. |

### Parameter relations

- At least one of (video_url is provided) or (audio_url is provided) must hold.

### Example

```bash
byteplus-mediakit-cli audio separate-voice \
  --video-url '<video_url>'
```

Replace placeholders with real user input only. Optional flags follow the parameter rules; do not invent URLs, files, enums, or business parameters.
Pass each scalar value as one shell argument. The quotes in these templates are illustrative: escape embedded quotes for the actual shell, or use an argument array when invoking a process. Never concatenate raw user input into shell code.

### Result

| Path | Type | Required | Mode | Description |
| --- | --- | --- | --- | --- |
| `request_id` | string | no | Cloud | Request id returned by the service; forwarded only when non-empty. Constraints: min length: 1. |
| `task_id` | string | yes | Cloud | Unique async task id used to query status and the terminal result. Constraints: min length: 1. |
| `task_type` | string | no | Cloud | Task type returned by the service; forwarded only when non-empty. Constraints: min length: 1. |
| `background_audio_url` | string | no | Cloud terminal | The download URL of the separated background track. The background track format matches the requested output_format, with MP3 as the default. It is a temporary HTTPS download URL valid for 24 hours. It is returned only when scene is Audio or Music. |
| `duration` | number | no | Cloud terminal | The total duration of the input video or audio, measured in seconds. |
| `music_audio_url` | string | no | Cloud terminal | Download link for the music track, returned for Drama, Narrate and Game. Its encoding follows output_format. Save the file within the 24-hour validity period. |
| `sfx_audio_url` | string | no | Cloud terminal | Download link for the ambient sounds and sound effects track, returned for Drama, Narrate and Game. Its encoding follows output_format. Save the file within the 24-hour validity period. |
| `voice_audio_url` | string | no | Cloud terminal | The download URL of the separated vocal track. The vocal track format matches the requested output_format, with MP3 as the default. This is a temporary HTTPS download URL valid for 24 hours. |

After a successful Cloud call, read the returned `task_id` and check its current status. This command queries once and returns; when the task is still running, query the same task again later until terminal. Do not resubmit the business task. Use `--poll-complete` only when continuous waiting is intended; it waits without a limit by default. Pass a polling timeout or attempt limit only when specified by the user. Interrupting the CLI stops waiting without canceling the Cloud task:

```bash
byteplus-mediakit-cli shared query-task --task-id '<task_id>'
```

After a synchronous business tool succeeds or an asynchronous task reaches completed, return its actual result and any output asset URLs to the user. Use the returned URLs as inputs to requested dependent steps. Download assets or perform additional local processing only when the user requests it, and retain the Cloud result URL alongside any downloaded file. Do not present task submission as completion. Do not replace a completed Cloud output with a locally recreated file from the original input, or infer permission for local recreation from a requested duration or quality target. If a returned asset's metadata or name conflicts with the requested constraints, probe that asset with an available metadata tool before claiming a mismatch or performing a correction. Corrective processing must consume the completed Cloud output, preserving its lineage; present the original Cloud result URL alongside any corrected output and explain any confirmed limitation. Never omit an available completed result URL in favor of an unfinished correction or a local fallback.

### Machine contract

These commands read live help/schema only and do not call Cloud:

```bash
byteplus-mediakit-cli audio separate-voice --help
byteplus-mediakit-cli audio separate-voice --schema
```

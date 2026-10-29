# mux-audio-video

## Capability

Combines an input audio stream and a video stream into a new video file.

## Parameter rules

- Optional parameters may be filled only when the user specifies them, or when they can be determined accurately from user intent; do not invent values. Omit them when uncertain; ask the user if they are required to complete the task.

## Cloud

### Command and lifecycle

- Command: `byteplus-mediakit-cli editing mux-audio-video`
- Lifecycle: async
- Return: returns `task_id`; query the terminal result next.

### Usage guide

- Boolean parameters (`--is-audio-reserve`, `--is-video-audio-sync`) must be written as `--flag=true` or `--flag=false`, or as bare `--flag` (equivalent to true). Do not pass a space-separated value such as `--flag true`, which the CLI treats as a positional argument.
- Omit boolean flags when they would repeat the default value.

### Example

```bash
MEDIAKIT_BYTEPLUS_SURFACE=skill MEDIAKIT_BYTEPLUS_RUNTIME=<runtime> \
  byteplus-mediakit-cli editing mux-audio-video \
  --video-url <video_url> \
  --audio-url <audio_url>
```

Replace placeholders with real user input only. Optional flags follow the parameter rules; do not invent URLs, files, enums, or business parameters.

### Parameters

| Path | CLI flag | Type | Required | Default | Enum/range | Description |
| --- | --- | --- | --- | --- | --- | --- |
| `audio_url` | `--audio-url` | string | yes | - | - | The URL of the input audio to merge. Only publicly accessible HTTP/HTTPS URLs are supported as the audio source. Commonly supported audio formats include mp3, m4a, and wav. A single input audio file is recommended not to exceed 10 GB. |
| `callback_args` | `--callback-args` | string | no | - | - | Custom callback argument returned unchanged when the task finishes, used to correlate business records; maximum 512 bytes. Pass only when the user provides this value; do not generate, infer, or fill it. |
| `callback_url` | `--callback-url` | string | no | - | - | Callback URL that receives this task result; when set it takes priority over the global callback URL and must start with http:// or https://. Pass only when the user provides this value; do not generate, infer, or fill it. |
| `client_token` | `--client-token` | string | no | - | - | Idempotency token; case-sensitive, at most 64 printable ASCII characters. Omit by default. Reuse the same token for retries of the same logical request. Do not mint a new token for each attempt. The CLI does not infer retry intent or auto-generate tokens. Omit by default. Use the user-provided value as-is when specified. When the user explicitly retries, the same logical request must reuse the same token. If a token already exists, reuse it; if earlier requests had no token, you may create one starting with this retry and keep reusing it, but that token does not provide retroactive idempotency for earlier requests. A change in business parameters is a new request and must not reuse the old token. Do not mint a different value for each attempt. The CLI runtime does not infer retry intent and does not auto-generate tokens. |
| `is_audio_reserve` | `--is-audio-reserve` | boolean | no | true | - | Controls whether to keep the video's original audio track. Can be set to true or false, and defaults to true. When set to true, the original audio is kept and the new audio is mixed with it. When set to false, the original audio track is not kept and is replaced by the new audio. |
| `is_video_audio_sync` | `--is-video-audio-sync` | boolean | no | false | - | Controls whether to align the audio and video durations. Accepts false or true, and defaults to false. When true, processing follows sync_mode and sync_method. When false, durations are not aligned and the merged video's duration is determined by the longer media stream. |
| `queue_id` | `--queue-id` | string | no | - | - | Target queue ID for the submitted task. Omit to use the system default queue. Pass only when the user provides this value; do not generate, infer, or fill it. |
| `sync_method` | `--sync-method` | string | no | "trim" | - | Specifies the method for duration alignment, and is effective only when is_video_audio_sync is true. Accepts trim or speed, and defaults to trim. trim removes content from the end of the longer stream to align it with the shorter stream, while speed changes speed so the two media streams have the same duration. |
| `sync_mode` | `--sync-mode` | string | no | "video" | - | Specifies the duration alignment basis, and is effective only when is_video_audio_sync is true. Accepts video or audio, and defaults to video. With video as the basis, longer audio is trimmed and shorter audio is processed according to sync_method. With audio as the basis, longer video is trimmed and shorter video is processed according to sync_method. |
| `video_url` | `--video-url` | string | yes | - | - | The URL of the input video. Only publicly accessible HTTP/HTTPS URLs are supported as the video source. Common supported video formats include mp4, flv, ts, avi, mov, wmv, and mkv. A single input video file is recommended not to exceed 10 GB. Input video resolution up to 4K (3840×2160) is supported. |

### Result

| Path | Type | Required | Mode | Description |
| --- | --- | --- | --- | --- |
| `request_id` | string | no | Cloud | Request id returned by the service; forwarded only when non-empty. |
| `task_id` | string | yes | Cloud | Unique async task id used to query status and the terminal result. |
| `task_type` | string | no | Cloud | Task type returned by the service; forwarded only when non-empty. |
| `result.duration` | number | no | Cloud terminal | The total duration of the output video, measured in seconds. |
| `result.resolution` | string | no | Cloud terminal | The resolution of the output video. |
| `result.video_url` | string | no | Cloud terminal | The download URL of the merged video file. The video file format is MP4. By default, the download URL is valid for 24 hours. |

After a successful Cloud call, read `task_id` and query the terminal result:

```bash
MEDIAKIT_BYTEPLUS_SURFACE=skill MEDIAKIT_BYTEPLUS_RUNTIME=<runtime> \
  byteplus-mediakit-cli shared query-task --task-id <task_id> --poll-complete
```

### Machine contract

These commands read live help/schema only and do not call Cloud:

```bash
byteplus-mediakit-cli editing mux-audio-video --help
byteplus-mediakit-cli editing mux-audio-video --schema
```

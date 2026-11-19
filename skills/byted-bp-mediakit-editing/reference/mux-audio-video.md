# mux-audio-video

## Capability

Combines an input audio stream and a video stream into a new video file.

## Parameter rules

- Optional parameters may be filled only when the user specifies them, or when they can be determined accurately from user intent; do not invent values. Omit them when uncertain; ask the user if they are required to complete the task. Before submitting a business task, resolve required fields and required alternatives from the user's explicit requirements, authoritative prior results, or an unambiguous documented mapping. A media type, genre, or broad processing goal does not by itself supply missing user choices or selection criteria. If a required choice has no documented default and remains unresolved, ask a focused clarification, preserve the user's known constraints, and stop before business submission. Do not invent a custom criterion merely to make a required field present. For optional parameters, infer only values implementing a behavior the user actually requested or required by a declared parameter relation. Do not add extra processing, alignment, formatting, or tuning merely because it seems helpful.

## Cloud

### Command and lifecycle

- Command: `byteplus-mediakit-cli editing mux-audio-video`
- Lifecycle: async
- Return: returns `task_id`; query the terminal result next.

### Usage guide

- Boolean parameters (`--is-audio-reserve`, `--is-video-audio-sync`) must be written as `--flag=true` or `--flag=false`, or as bare `--flag` (equivalent to true). Do not pass a space-separated value such as `--flag true`, which the CLI treats as a positional argument.
- Pass a boolean explicitly when the user specifies true or false, including values equal to the default. Omit it when the user has not specified a value and no relation requires it.
- Requesting the main operation does not request every optional behavior. Do not enable an optional switch or fill its dependent defaults merely because input metadata differs or a changed output would be more convenient. Keep those fields omitted unless the user requested that separate behavior or a declared parameter relation requires their explicit presence.

#### Soundtrack replacement and duration

Replacing or dropping the original soundtrack and aligning media durations are independent behaviors. A request to replace the soundtrack does not specify an output duration and does not ask to align the tracks.

When the user asks to drop the original track, pass `--is-audio-reserve=false`. If the user has not separately requested duration alignment, omit `--is-video-audio-sync`, `--sync-mode`, and `--sync-method`, even when a metadata probe shows that the audio and video have different lengths. Do not treat the source video's duration as an unstated target and do not trim or speed up the audio on that basis.

The documented default for `is_video_audio_sync` is false. With synchronization disabled, the output duration follows the longer media stream. Use that default when no duration constraint was supplied, return the actual Cloud result, and explain its measured duration if relevant. Do not change this default behavior merely to make the result match the shorter input. If the user explicitly requests alignment, resolve the requested duration basis and method from the documented parameter table.

For soundtrack replacement with the original track dropped and no duration request, the command needs only the two inputs and the original-track switch:

```bash
byteplus-mediakit-cli editing mux-audio-video \
  --video-url '<video_url>' \
  --audio-url '<audio_url>' \
  --is-audio-reserve=false
```

### Example

```bash
byteplus-mediakit-cli editing mux-audio-video \
  --video-url '<video_url>' \
  --audio-url '<audio_url>'
```

Replace placeholders with real user input only. Optional flags follow the parameter rules; do not invent URLs, files, enums, or business parameters.
Pass each scalar value as one shell argument. The quotes in these templates are illustrative: escape embedded quotes for the actual shell, or use an argument array when invoking a process. Never concatenate raw user input into shell code.

### Parameters

| Path | CLI flag | Type | Required | Default | Enum/range | Description |
| --- | --- | --- | --- | --- | --- | --- |
| `audio_url` | `--audio-url` | string | yes | - | min length: 1 | The URL of the input audio to merge. publicly accessible HTTP/HTTPS URLs are supported as the audio source. Commonly supported audio formats include mp3, m4a, and wav. A single input audio file is recommended not to exceed 10 GB. CLI inputs may be local file paths or public HTTP/HTTPS URLs. The declared media input format also accepts vod:// and tos:// resource URIs. The CLI handles local file upload internally; pass the original input directly. |
| `callback_args` | `--callback-args` | string | no | - | - | Custom callback argument returned unchanged when the task finishes, used to correlate business records; maximum 512 bytes. Pass only when the user provides this value; do not generate, infer, or fill it. |
| `callback_url` | `--callback-url` | string | no | - | - | Callback URL that receives this task result; when set it takes priority over the global callback URL and must start with http:// or https://. Pass only when the user provides this value; do not generate, infer, or fill it. |
| `client_token` | `--client-token` | string | no | - | - | Idempotency token; case-sensitive, at most 64 printable ASCII characters. Omit by default. Reuse the same token for retries of the same logical request. Do not mint a new token for each attempt. The CLI does not infer retry intent or auto-generate tokens. Omit by default on the first asynchronous submission. Use a user-provided value as-is when specified. A retry of the same uncertain submission must keep the original token policy: reuse its token if provided, or keep the field omitted if it was not provided. After a result exists, an explicitly requested new run must use a new token. Do not mint a different value for each retry attempt. Synchronous tasks do not support idempotency checks. The CLI runtime does not infer retry intent and does not auto-generate tokens. |
| `is_audio_reserve` | `--is-audio-reserve` | boolean | no | true | - | Controls whether to keep the video's original audio track. Can be set to true or false, and defaults to true. When set to true, the original audio is kept and the new audio is mixed with it. When set to false, the original audio track is not kept and is replaced by the new audio. |
| `is_video_audio_sync` | `--is-video-audio-sync` | boolean | no | false | - | Controls whether to align the audio and video durations. Accepts false or true, and defaults to false. When true, processing follows sync_mode and sync_method. When false, durations are not aligned and the merged video's duration is determined by the longer media stream. |
| `queue_id` | `--queue-id` | string | no | - | - | Target queue ID for the submitted task. Omit to use the system default queue. Pass only when the user provides this value; do not generate, infer, or fill it. |
| `sync_method` | `--sync-method` | string | no | "trim" | enum: ["trim","speed"] | Specifies the method for duration alignment, and is effective only when is_video_audio_sync is true. Accepts trim or speed, and defaults to trim. trim removes content from the end of the longer stream to align it with the shorter stream, while speed changes speed so the two media streams have the same duration. |
| `sync_mode` | `--sync-mode` | string | no | "video" | enum: ["video","audio"] | Specifies the duration alignment basis, and is effective only when is_video_audio_sync is true. Accepts video or audio, and defaults to video. With video as the basis, longer audio is trimmed and shorter audio is processed according to sync_method. With audio as the basis, longer video is trimmed and shorter video is processed according to sync_method. |
| `video_url` | `--video-url` | string | yes | - | min length: 1 | The URL of the input video. publicly accessible HTTP/HTTPS URLs are supported as the video source. Common supported video formats include mp4, flv, ts, avi, mov, wmv, and mkv. A single input video file is recommended not to exceed 10 GB. Input video resolution up to 4K (3840×2160) is supported. CLI inputs may be local file paths or public HTTP/HTTPS URLs. The declared media input format also accepts vod:// and tos:// resource URIs. The CLI handles local file upload internally; pass the original input directly. |

### Result

| Path | Type | Required | Mode | Description |
| --- | --- | --- | --- | --- |
| `request_id` | string | no | Cloud | Request id returned by the service; forwarded only when non-empty. Constraints: min length: 1. |
| `task_id` | string | yes | Cloud | Unique async task id used to query status and the terminal result. Constraints: min length: 1. |
| `task_type` | string | no | Cloud | Task type returned by the service; forwarded only when non-empty. Constraints: min length: 1. |
| `duration` | number | no | Cloud terminal | The total duration of the output video, measured in seconds. |
| `resolution` | string | no | Cloud terminal | The resolution of the output video. |
| `video_url` | string | no | Cloud terminal | The download URL of the merged video file. The video file format is MP4. By default, the download URL is valid for 24 hours. |

After a successful Cloud call, read the returned `task_id` and check its current status. This command queries once and returns; when the task is still running, query the same task again later until terminal. Do not resubmit the business task. Use `--poll-complete` only when continuous waiting is intended; it waits without a limit by default. Pass a polling timeout or attempt limit only when specified by the user. Interrupting the CLI stops waiting without canceling the Cloud task:

```bash
byteplus-mediakit-cli shared query-task --task-id '<task_id>'
```

After a synchronous business tool succeeds or an asynchronous task reaches completed, return its actual result and any output asset URLs to the user. Use the returned URLs as inputs to requested dependent steps. Download assets or perform additional local processing only when the user requests it, and retain the Cloud result URL alongside any downloaded file. Do not present task submission as completion. Do not replace a completed Cloud output with a locally recreated file from the original input, or infer permission for local recreation from a requested duration or quality target. If a returned asset's metadata or name conflicts with the requested constraints, probe that asset with an available metadata tool before claiming a mismatch or performing a correction. Corrective processing must consume the completed Cloud output, preserving its lineage; present the original Cloud result URL alongside any corrected output and explain any confirmed limitation. Never omit an available completed result URL in favor of an unfinished correction or a local fallback.

### Machine contract

These commands read live help/schema only and do not call Cloud:

```bash
byteplus-mediakit-cli editing mux-audio-video --help
byteplus-mediakit-cli editing mux-audio-video --schema
```

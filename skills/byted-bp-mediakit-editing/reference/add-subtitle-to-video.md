# add-subtitle-to-video

## Capability

Burns a specified subtitle file or text content into video frames with custom styling and produces a new video with embedded subtitles.

## Parameter rules

- Either subtitle_url or subtitles must be provided; when both are present, subtitle_url takes priority.
- When the user asks to add/burn subtitles but provides neither `subtitle_url` nor subtitle text/timestamps, ask for subtitle content before submitting any business call — including earlier chain steps such as erase-video-subtitle-pro. Do not invent placeholder subtitle text, and do not start the chain in parallel while waiting for the answer.
- Optional parameters may be filled only when the user specifies them, or when they can be determined accurately from user intent; do not invent values. Omit them when uncertain; ask the user if they are required to complete the task.
- Before setting subtitle_pos_preset or subtitle_font_size, probe video metadata if width and height are unknown. When the user does not specify a position, use bottom_center for landscape and lower_third for portrait.
- Font size must not exceed the maximum render height of the selected position preset (video height × height%); single-line character count × font size must not exceed the preset width (video width × width%).
- If the output is for short-video or micro-drama platforms, confirm with the user before setting the position and avoid platform chrome such as action bars, progress bars, and interactive controls.

## Cloud

### Command and lifecycle

- Command: `byteplus-mediakit-cli editing add-subtitle-to-video`
- Lifecycle: async
- Return: returns `task_id`; query the terminal result next.

### Usage guide

- Object or object array parameters (`--subtitles`) must be passed as a valid JSON string wrapped in single quotes, for example `--subtitles '[{...}]'`; field names and nesting must match the “Enum/range” column and nested field descriptions in the Parameters table below.
- Do not pass comma-separated values or plain text for these parameters.

### Example

```bash
MEDIAKIT_BYTEPLUS_SURFACE=skill MEDIAKIT_BYTEPLUS_RUNTIME=<runtime> \
  byteplus-mediakit-cli editing add-subtitle-to-video \
  --video-url <video_url>
```

Replace placeholders with real user input only. Optional flags follow the parameter rules; do not invent URLs, files, enums, or business parameters.

### Parameters

| Path | CLI flag | Type | Required | Default | Enum/range | Description |
| --- | --- | --- | --- | --- | --- | --- |
| `callback_args` | `--callback-args` | string | no | - | - | Custom callback argument returned unchanged when the task finishes, used to correlate business records; maximum 512 bytes. Pass only when the user provides this value; do not generate, infer, or fill it. |
| `callback_url` | `--callback-url` | string | no | - | - | Callback URL that receives this task result; when set it takes priority over the global callback URL and must start with http:// or https://. Pass only when the user provides this value; do not generate, infer, or fill it. |
| `client_token` | `--client-token` | string | no | - | - | Idempotency token; case-sensitive, at most 64 printable ASCII characters. Omit by default. Reuse the same token for retries of the same logical request. Do not mint a new token for each attempt. The CLI does not infer retry intent or auto-generate tokens. Omit by default. Use the user-provided value as-is when specified. When the user explicitly retries, the same logical request must reuse the same token. If a token already exists, reuse it; if earlier requests had no token, you may create one starting with this retry and keep reusing it, but that token does not provide retroactive idempotency for earlier requests. A change in business parameters is a new request and must not reuse the old token. Do not mint a different value for each attempt. The CLI runtime does not infer retry intent and does not auto-generate tokens. |
| `queue_id` | `--queue-id` | string | no | - | - | Target queue ID for the submitted task. Omit to use the system default queue. Pass only when the user provides this value; do not generate, infer, or fill it. |
| `subtitle_font_color` | `--subtitle-font-color` | string | no | "#FFFFFFFF" | - | The subtitle font color, specified in RGBA format. This setting is optional; the default is #FFFFFFFF, which is opaque white. |
| `subtitle_font_size` | `--subtitle-font-size` | integer | no | 50 | min: 1 | The subtitle font size, measured in pixels (px). It defaults to 50. This parameter is optional. |
| `subtitle_font_type` | `--subtitle-font-type` | string | no | "sy_black" | enum: ["sy_black","pm_zhengdao","zhanku_kuaile"] | The subtitle font. This parameter is optional. Supported fonts include inter, montserrat, oppo_sans, roboto, and source_han_serif. pm_zhengdao is bold and powerful with a strong presence, ideal for large headings and covers. sy_black is a classic, clean, versatile sans-serif suited to body text. zhanku_kuaile is rounded and lively with a handwritten feel, suitable for a relaxed, fun Vlog mood. |
| `subtitle_pos_preset` | `--subtitle-pos-preset` | string | no | "bottom_center" | enum: ["bottom_center","top_center","center","lower_third"] | The preset subtitle position. This parameter is optional. It defaults to bottom_center and can also be set to top_center, center, or lower_third. |
| `subtitle_url` | `--subtitle-url` | string | no | - | - | The URL of the subtitle file. Only publicly accessible HTTP/HTTPS URLs are supported, and common supported subtitle formats include SRT, VTT, and ASS. This parameter is optional. When both subtitle_url and subtitles are present, the subtitle_url content takes priority. |
| `subtitles` | `--subtitles` | array<object> | no | - | min items: 0 | A list of subtitle objects, each containing subtitle text and start and end times. This parameter is optional. |
| `subtitles[].end_time` | - | number | yes | - | min: 0 | The time when the subtitle disappears, measured in seconds. This value is required. |
| `subtitles[].start_time` | - | number | yes | - | min: 0 | The time when the subtitle appears, measured in seconds. This field is required. |
| `subtitles[].subtitle_text` | - | string | yes | - | - | The text content of a single subtitle. This field is required. |
| `video_url` | `--video-url` | string | yes | - | - | The URL of the video to which subtitles are added. Only publicly accessible HTTP/HTTPS URLs are supported as the video file source. Common supported video formats include mp4, flv, ts, avi, mov, wmv, and mkv. Input resolution up to 4K (3840×2160) is supported. It is recommended that one input video file not exceed 10 GB. This field is required. |

### Result

| Path | Type | Required | Mode | Description |
| --- | --- | --- | --- | --- |
| `request_id` | string | no | Cloud | Request id returned by the service; forwarded only when non-empty. |
| `task_id` | string | yes | Cloud | Unique async task id used to query status and the terminal result. |
| `task_type` | string | no | Cloud | Task type returned by the service; forwarded only when non-empty. |
| `result.duration` | number | no | Cloud terminal | The total duration of the output video, measured in seconds. |
| `result.resolution` | string | no | Cloud terminal | The resolution of the output video, which can be 240p, 360p, 480p, 540p, 720p, 1080p, 2k, or 4k. |
| `result.video_url` | string | no | Cloud terminal | The download URL of the generated video with embedded subtitles. The generated video is in MP4 format. The URL is a temporary HTTPS download link that is valid for 24 hours by default. |

After a successful Cloud call, read `task_id` and query the terminal result:

```bash
MEDIAKIT_BYTEPLUS_SURFACE=skill MEDIAKIT_BYTEPLUS_RUNTIME=<runtime> \
  byteplus-mediakit-cli shared query-task --task-id <task_id> --poll-complete
```

### Machine contract

These commands read live help/schema only and do not call Cloud:

```bash
byteplus-mediakit-cli editing add-subtitle-to-video --help
byteplus-mediakit-cli editing add-subtitle-to-video --schema
```

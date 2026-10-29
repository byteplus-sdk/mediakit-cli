# erase-video-subtitle-pro

## Capability

This capability provides high-quality, seamless subtitle removal and restores the original picture to the greatest extent possible. Different queues are the primary way to allocate costs to specific projects.

## Parameter rules

- Optional parameters may be filled only when the user specifies them, or when they can be determined accurately from user intent; do not invent values. Omit them when uncertain; ask the user if they are required to complete the task.
- When erase-video-subtitle-pro is only the first step of a larger chain that also needs add-subtitle (or another later required-no-default field) and that later content is still missing, ask first; do not submit erase while waiting.

## Cloud

### Command and lifecycle

- Command: `byteplus-mediakit-cli video erase-video-subtitle-pro`
- Lifecycle: async
- Return: returns `task_id`; query the terminal result next.

### Usage guide

- Object or object array parameters (`--erase-ratio-location`, `--subtitle-filter`, `--time-segment-filter`) must be passed as valid JSON strings wrapped in single quotes; field names and nesting must match the Parameters table below.
- Do not pass comma-separated values or plain text for these parameters.

### Example

```bash
MEDIAKIT_BYTEPLUS_SURFACE=skill MEDIAKIT_BYTEPLUS_RUNTIME=<runtime> \
  byteplus-mediakit-cli video erase-video-subtitle-pro \
  --video-url <video_url>
```

Replace placeholders with real user input only. Optional flags follow the parameter rules; do not invent URLs, files, enums, or business parameters.

### Parameters

| Path | CLI flag | Type | Required | Default | Enum/range | Description |
| --- | --- | --- | --- | --- | --- | --- |
| `callback_args` | `--callback-args` | string | no | - | - | Custom callback argument returned unchanged when the task finishes, used to correlate business records; maximum 512 bytes. Pass only when the user provides this value; do not generate, infer, or fill it. |
| `callback_url` | `--callback-url` | string | no | - | - | Callback URL that receives this task result; when set it takes priority over the global callback URL and must start with http:// or https://. Pass only when the user provides this value; do not generate, infer, or fill it. |
| `client_token` | `--client-token` | string | no | - | - | Idempotency token; case-sensitive, at most 64 printable ASCII characters. Omit by default. Reuse the same token for retries of the same logical request. Do not mint a new token for each attempt. The CLI does not infer retry intent or auto-generate tokens. Omit by default. Use the user-provided value as-is when specified. When the user explicitly retries, the same logical request must reuse the same token. If a token already exists, reuse it; if earlier requests had no token, you may create one starting with this retry and keep reusing it, but that token does not provide retroactive idempotency for earlier requests. A change in business parameters is a new request and must not reuse the old token. Do not mint a different value for each attempt. The CLI runtime does not infer retry intent and does not auto-generate tokens. |
| `erase_ratio_location` | `--erase-ratio-location` | array<object> | no | - | min items: 0; max items: 20 | This field is optional. When configured, it restricts text erasure to the specified rectangular regions and supports up to 20 erasure boxes. In Subtitle mode, the effective erasure region is the intersection of the custom boxes and the lower half of the frame. Each Location object defines a rectangular erasure area. It uses normalized coordinates from top-left (0, 0) to bottom-right (1, 1). |
| `erase_ratio_location[].bottom_right_x` | - | number | yes | - | min: 0; max: 1 | bottom_right_x is required, ranges from 0 to 1 inclusive, and must be greater than top_left_x. It is the horizontal ratio of the box's bottom-right corner relative to the frame's left edge. |
| `erase_ratio_location[].bottom_right_y` | - | number | yes | - | min: 0; max: 1 | bottom_right_y is required, ranges from 0 to 1 inclusive, and must be greater than top_left_y. It is the vertical ratio of the box's bottom-right corner relative to the frame's top edge. |
| `erase_ratio_location[].top_left_x` | - | number | yes | - | min: 0; max: 1 | top_left_x is required and ranges from 0 to 1 inclusive. It is the horizontal ratio of the box's top-left corner relative to the frame's left edge. |
| `erase_ratio_location[].top_left_y` | - | number | yes | - | min: 0; max: 1 | This value is required and specifies the vertical ratio of the box's top-left corner relative to the frame's top edge. It can be set from a minimum of 0 to a maximum of 1. |
| `mode` | `--mode` | string | no | "Subtitle" | enum: ["Subtitle","Text"] | This parameter is optional, supports Subtitle and Text, and defaults to Subtitle. Subtitle mode erases text that OCR detects as subtitles, and by default processes only subtitles in the lower half, the bottom 50%, of the video frame; in Subtitle mode, configuring erase_ratio_location limits erasure to the intersection of the custom boxes and the lower-half region. Text mode performs text detection over the entire video frame and erases subtitles and other OCR-detected text such as people names and place names, but excludes scene text such as signboards; in Text mode, configuring erase_ratio_location limits erasure to the specified boxes. |
| `output_encode_mode` | `--output-encode-mode` | string | no | "Quality" | enum: ["Quality","Size"] | This parameter is optional, supports Quality and Size, and defaults to Quality. Quality mode uses a higher bitrate for better quality, but the file may be larger. Size mode keeps output bitrate close to the source while ensuring a certain quality level. |
| `queue_id` | `--queue-id` | string | no | - | - | Target queue ID for the submitted task. Omit to use the system default queue. Pass only when the user provides this value; do not generate, infer, or fill it. |
| `subtitle_filter` | `--subtitle-filter` | object | no | - | - | Optional. Takes effect only when the mode is Subtitle, where it fine-tunes subtitle detection using text height and horizontal centering to help prevent accidental erasure of non-subtitle content such as titles or logos. If it is not specified, default values are used. |
| `subtitle_filter.center_offset_ratio` | - | number | no | - | min: 0; max: 1 | Optional. The maximum allowed ratio of the offset between the text's horizontal center and the video's horizontal center. Text exceeding this ratio is not treated as a subtitle. Ranges from a minimum of 0 to a maximum of 1, and defaults to 0.08. |
| `subtitle_filter.max_text_height_ratio` | - | number | no | - | min: 0; max: 1 | Optional. The maximum ratio of text height to video height. Text taller than this ratio is not treated as subtitles. Ranges from a minimum of 0 to a maximum of 1, and defaults to 0.1, or 10%. |
| `subtitle_filter.min_text_height_ratio` | - | number | no | - | min: 0; max: 1 | The minimum ratio of text height to video height. This field is optional and defaults to 0.01, or 1%. It can be set from a minimum value of 0 to a maximum value of 1. Text shorter than this ratio is not treated as subtitles. |
| `time_segment_filter` | `--time-segment-filter` | object | no | - | - | Restricts erasure to, or excludes, specified video time segments. This field is optional and is commonly used to process main content while preserving subtitles in opening or closing credits. When it is omitted, the entire video is processed. |
| `time_segment_filter.mode` | - | string | yes | - | enum: ["skip","selected"] | This field is required. It supports selected and skip. selected erases subtitles only within the listed segments, while skip preserves the listed segments and erases the rest of the video. |
| `time_segment_filter.segments` | - | array<object> | yes | - | min items: 1 | Required. The list of time segments. Each element defines a single time segment, representing one start-to-end time interval measured in seconds. |
| `time_segment_filter.segments[].end_time` | - | number | yes | - | min: 0 | Required. The end time of the segment, measured in seconds, and must be greater than start_time. |
| `time_segment_filter.segments[].start_time` | - | number | yes | - | min: 0 | Start time of the segment, measured in seconds. This value is required. |
| `video_url` | `--video-url` | string | yes | - | - | The URL of the video whose subtitles are to be erased. This value is required. Only publicly accessible HTTP/HTTPS video URLs are supported. Supported mainstream video formats include mp4, flv, ts, avi, mov, wmv, and mkv. The input resolution supports up to 2K, and the output resolution supports up to 1080P. |

### Result

| Path | Type | Required | Mode | Description |
| --- | --- | --- | --- | --- |
| `request_id` | string | no | Cloud | Request id returned by the service; forwarded only when non-empty. |
| `task_id` | string | yes | Cloud | Unique async task id used to query status and the terminal result. |
| `task_type` | string | no | Cloud | Task type returned by the service; forwarded only when non-empty. |
| `result.duration` | number | no | Cloud terminal | The total duration of the output video, measured in seconds. |
| `result.video_url` | string | no | Cloud terminal | The download URL for the video with subtitles erased. The URL is valid for 24 hours. |

After a successful Cloud call, read `task_id` and query the terminal result:

```bash
MEDIAKIT_BYTEPLUS_SURFACE=skill MEDIAKIT_BYTEPLUS_RUNTIME=<runtime> \
  byteplus-mediakit-cli shared query-task --task-id <task_id> --poll-complete
```

### Machine contract

These commands read live help/schema only and do not call Cloud:

```bash
byteplus-mediakit-cli video erase-video-subtitle-pro --help
byteplus-mediakit-cli video erase-video-subtitle-pro --schema
```

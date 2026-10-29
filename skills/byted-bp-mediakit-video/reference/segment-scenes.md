# segment-scenes

## Capability

Automatically splits a video into multiple scene segments based on transitions and changes in visual content, and outputs each segment's timeline information and independent video file.

## Parameter rules

- Optional parameters may be filled only when the user specifies them, or when they can be determined accurately from user intent; do not invent values. Omit them when uncertain; ask the user if they are required to complete the task.

## Cloud

### Command and lifecycle

- Command: `byteplus-mediakit-cli video segment-scenes`
- Lifecycle: async
- Return: returns `task_id`; query the terminal result next.

### Usage guide

- Boolean parameters (`--enable-clip-fade`, `--return-segment-videos`) must be written as `--flag=true` or `--flag=false`, or as bare `--flag` (equivalent to true). Do not pass a space-separated value such as `--flag true`, which the CLI treats as a positional argument.
- Omit boolean flags when they would repeat the default value.

### Example

```bash
MEDIAKIT_BYTEPLUS_SURFACE=skill MEDIAKIT_BYTEPLUS_RUNTIME=<runtime> \
  byteplus-mediakit-cli video segment-scenes \
  --video-url <video_url>
```

Replace placeholders with real user input only. Optional flags follow the parameter rules; do not invent URLs, files, enums, or business parameters.

### Parameters

| Path | CLI flag | Type | Required | Default | Enum/range | Description |
| --- | --- | --- | --- | --- | --- | --- |
| `callback_args` | `--callback-args` | string | no | - | - | Custom callback argument returned unchanged when the task finishes, used to correlate business records; maximum 512 bytes. Pass only when the user provides this value; do not generate, infer, or fill it. |
| `callback_url` | `--callback-url` | string | no | - | - | Callback URL that receives this task result; when set it takes priority over the global callback URL and must start with http:// or https://. Pass only when the user provides this value; do not generate, infer, or fill it. |
| `client_token` | `--client-token` | string | no | - | - | Idempotency token; case-sensitive, at most 64 printable ASCII characters. Omit by default. Reuse the same token for retries of the same logical request. Do not mint a new token for each attempt. The CLI does not infer retry intent or auto-generate tokens. Omit by default. Use the user-provided value as-is when specified. When the user explicitly retries, the same logical request must reuse the same token. If a token already exists, reuse it; if earlier requests had no token, you may create one starting with this retry and keep reusing it, but that token does not provide retroactive idempotency for earlier requests. A change in business parameters is a new request and must not reuse the old token. Do not mint a different value for each attempt. The CLI runtime does not infer retry intent and does not auto-generate tokens. |
| `enable_clip_fade` | `--enable-clip-fade` | boolean | no | false | - | Controls whether detected fade-in/fade-out segments are output as independent clips. This parameter is optional and can be set to false or true, and defaults to false. When set to true, noticeable fade-in/fade-out transitions are split into independent clips. When set to false, fade transitions are not output separately and are merged into adjacent clips as part of the preceding or following scene. |
| `max_duration` | `--max-duration` | number | no | - | min: 0 | The maximum duration of a single clip, in seconds. This parameter is optional and defaults to 30. The value should be greater than or equal to min_duration. Segments longer than this maximum are forcibly split. |
| `min_duration` | `--min-duration` | number | no | - | min: 0 | The minimum duration of a single clip, in seconds. This parameter is optional and defaults to 3. It should be less than or equal to max_duration. Clips shorter than this duration are merged. |
| `return_segment_videos` | `--return-segment-videos` | boolean | no | true | - | Optional field that controls whether clip video files are generated and returned. It can be set to true or false, and the default value is true. When set to true, clip files are generated and each clip download URL is returned in result.segments[].segment_video_url. When set to false, clip files are not generated and only the start_time and end_time timeline is returned, without segment_video_url. Setting it to false is suitable when only scene timecodes are needed and clipping is handled by the caller, and it can reduce task processing time. |
| `segment_threshold` | `--segment-threshold` | number | no | - | min: 0; max: 100 | segment_threshold is optional and sets the sensitivity threshold for scene segmentation. It supports a minimum value of 0 and must be less than 100, and defaults to 10. Lower values make the algorithm more sensitive to scene changes and result in more segments, while higher values make the algorithm more likely to treat minor changes as the same scene and result in fewer segments. When min_duration, max_duration, and segment_threshold are set together, the algorithm first segments based on segment_threshold and min_duration. After the first stage, a clip exceeding max_duration is segmented again without regard to segment_threshold so final durations do not exceed max_duration. |
| `video_url` | `--video-url` | string | yes | - | - | The URL of the video to be processed. This parameter is required. Commonly supported video formats include mp4, flv, ts, avi, mov, wmv, and mkv. Only publicly accessible HTTP/HTTPS URLs are supported as file sources. A single video must not exceed 2 hours. |

### Result

| Path | Type | Required | Mode | Description |
| --- | --- | --- | --- | --- |
| `request_id` | string | no | Cloud | Request id returned by the service; forwarded only when non-empty. |
| `task_id` | string | yes | Cloud | Unique async task id used to query status and the terminal result. |
| `task_type` | string | no | Cloud | Task type returned by the service; forwarded only when non-empty. |
| `result.duration` | number | no | Cloud terminal | The total duration of the input video, in seconds. |
| `result.segments` | array<object> | no | Cloud terminal | The list of clip information. |
| `result.segments[].end_time` | number | no | Cloud terminal | The end time of the clip, in seconds. |
| `result.segments[].segment_video_url` | string | no | Cloud terminal | The download URL for the clip's video file, in MP4 format. The URL is valid for 24 hours and is returned only when return_segment_videos is true. |
| `result.segments[].start_time` | number | no | Cloud terminal | The start time of the clip, in seconds. |

After a successful Cloud call, read `task_id` and query the terminal result:

```bash
MEDIAKIT_BYTEPLUS_SURFACE=skill MEDIAKIT_BYTEPLUS_RUNTIME=<runtime> \
  byteplus-mediakit-cli shared query-task --task-id <task_id> --poll-complete
```

### Machine contract

These commands read live help/schema only and do not call Cloud:

```bash
byteplus-mediakit-cli video segment-scenes --help
byteplus-mediakit-cli video segment-scenes --schema
```

# add-image-to-video

## Capability

Can programmatically add a logo, watermark, or other image to a video.

## Parameter rules

- Optional parameters may be filled only when the user specifies them, or when they can be determined accurately from user intent; do not invent values. Omit them when uncertain; ask the user if they are required to complete the task.

## Cloud

### Command and lifecycle

- Command: `byteplus-mediakit-cli editing add-image-to-video`
- Lifecycle: async
- Return: returns `task_id`; query the terminal result next.

### Parameters

| Path | CLI flag | Type | Required | Default | Enum/range | Description |
| --- | --- | --- | --- | --- | --- | --- |
| `callback_args` | `--callback-args` | string | no | - | - | Custom callback argument returned unchanged when the task finishes, used to correlate business records; maximum 512 bytes. Pass only when the user provides this value; do not generate, infer, or fill it. |
| `callback_url` | `--callback-url` | string | no | - | - | Callback URL that receives this task result; when set it takes priority over the global callback URL and must start with http:// or https://. Pass only when the user provides this value; do not generate, infer, or fill it. |
| `client_token` | `--client-token` | string | no | - | - | Idempotency token; case-sensitive, at most 64 printable ASCII characters. Omit by default. Reuse the same token for retries of the same logical request. Do not mint a new token for each attempt. The CLI does not infer retry intent or auto-generate tokens. Omit by default. Use the user-provided value as-is when specified. When the user explicitly retries, the same logical request must reuse the same token. If a token already exists, reuse it; if earlier requests had no token, you may create one starting with this retry and keep reusing it, but that token does not provide retroactive idempotency for earlier requests. A change in business parameters is a new request and must not reuse the old token. Do not mint a different value for each attempt. The CLI runtime does not infer retry intent and does not auto-generate tokens. |
| `end_time` | `--end-time` | number | no | - | min: 0 | Specifies in seconds when the image stops appearing. This parameter is optional and defaults to the end of the video. If the specified end time exceeds the original video duration, the output is extended to that time, and the extension is a black screen with the image still displayed. |
| `queue_id` | `--queue-id` | string | no | - | - | Target queue ID for the submitted task. Omit to use the system default queue. Pass only when the user provides this value; do not generate, infer, or fill it. |
| `start_time` | `--start-time` | number | no | - | min: 0 | Specifies in seconds when the image starts to appear. This parameter is optional and defaults to the start of the video. |
| `sub_image_height` | `--sub-image-height` | string | no | "5%" | pattern: "^(\\d{1,4}\|\\d{1,3}%)$" | Sets the image height. Supports a pixel value or a percentage relative to the video height. This parameter is optional and defaults to 5%. |
| `sub_image_pos_x` | `--sub-image-pos-x` | string | no | "85%" | pattern: "^(\\d{1,4}\|\\d{1,3}%)$" | Specifies the horizontal X-axis position of the image's top-left corner, using the video's top-left corner as origin "0". Supports a pixel value or a percentage. This parameter is optional and defaults to 85%. |
| `sub_image_pos_y` | `--sub-image-pos-y` | string | no | "90%" | pattern: "^(\\d{1,4}\|\\d{1,3}%)$" | Specifies the vertical Y-axis position of the image's top-left corner, using the video's top-left corner as origin "0". Supports a pixel value or a percentage. This parameter is optional and defaults to 90%. The default X and Y position values place the image in the bottom-right area of the video frame, suitable for a watermark. |
| `sub_image_url` | `--sub-image-url` | string | yes | - | - | Specifies the URL of the overlay image. This parameter is required, and only publicly accessible HTTP or HTTPS URLs are supported as the image source. PNG, JPG, and JPEG are recommended image formats, and using a PNG with an alpha channel is recommended for the best watermark effect. |
| `sub_image_width` | `--sub-image-width` | string | no | "10%" | pattern: "^(\\d{1,4}\|\\d{1,3}%)$" | The image width supports a pixel value or a percentage relative to the video width. This parameter is optional, and the default value is 10%. |
| `video_url` | `--video-url` | string | yes | - | - | Specifies the URL of the source video. This parameter is required, and only publicly accessible HTTP or HTTPS URLs are supported as the video file source. Common supported video formats include mp4, flv, ts, avi, mov, wmv, and mkv. Input resolution up to 4K (3840×2160) is supported. The input file size is recommended not to exceed 10 GB. |

### Example

```bash
MEDIAKIT_BYTEPLUS_SURFACE=skill MEDIAKIT_BYTEPLUS_RUNTIME=<runtime> \
  byteplus-mediakit-cli editing add-image-to-video \
  --video-url <video_url> \
  --sub-image-url <sub_image_url>
```

Replace placeholders with real user input only. Optional flags follow the parameter rules; do not invent URLs, files, enums, or business parameters.

### Result

| Path | Type | Required | Mode | Description |
| --- | --- | --- | --- | --- |
| `request_id` | string | no | Cloud | Request id returned by the service; forwarded only when non-empty. |
| `task_id` | string | yes | Cloud | Unique async task id used to query status and the terminal result. |
| `task_type` | string | no | Cloud | Task type returned by the service; forwarded only when non-empty. |
| `result.duration` | number | no | Cloud terminal | The total duration of the output video, in seconds. |
| `result.resolution` | string | no | Cloud terminal | The resolution of the output video, which can be 240p, 360p, 480p, 540p, 720p, 1080p, 2k, or 4k. |
| `result.video_url` | string | no | Cloud terminal | The URL of the generated video with the image overlaid, in MP4 format. The value is an HTTPS temporary download link, valid for 24 hours by default. |

After a successful Cloud call, read `task_id` and query the terminal result:

```bash
MEDIAKIT_BYTEPLUS_SURFACE=skill MEDIAKIT_BYTEPLUS_RUNTIME=<runtime> \
  byteplus-mediakit-cli shared query-task --task-id <task_id> --poll-complete
```

### Machine contract

These commands read live help/schema only and do not call Cloud:

```bash
byteplus-mediakit-cli editing add-image-to-video --help
byteplus-mediakit-cli editing add-image-to-video --schema
```

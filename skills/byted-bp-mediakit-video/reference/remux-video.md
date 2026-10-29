# remux-video

## Capability

Remuxing can quickly convert between mainstream container formats such as MP4, FLV, and MPEG-TS with no quality loss and at very low cost. It changes only the container format and does not re-encode or re-decode the audio/video bitstreams. This is used for VOD distribution adaptation, streaming segmentation and packaging, and multi-device compatibility.

## Parameter rules

- Optional parameters may be filled only when the user specifies them, or when they can be determined accurately from user intent; do not invent values. Omit them when uncertain; ask the user if they are required to complete the task.

## Cloud

### Command and lifecycle

- Command: `byteplus-mediakit-cli video remux-video`
- Lifecycle: async
- Return: returns `task_id`; query the terminal result next.

### Usage guide

- Object or object array parameters (`--metadata-add-tags`) must be passed as a valid JSON string wrapped in single quotes, for example `--metadata-add-tags '[{...}]'`; field names and nesting must match the “Enum/range” column and nested field descriptions in the Parameters table below.
- Do not pass comma-separated values or plain text for these parameters.
- Array parameters (`--metadata-keep-tags`) must use comma-separated values wrapped in quotes, for example `--metadata-keep-tags "url1,url2"`.
- Individual file names or URLs must not contain commas (`,`); if that happens, ask the user for comma-free paths or URLs before calling.

### Example

```bash
MEDIAKIT_BYTEPLUS_SURFACE=skill MEDIAKIT_BYTEPLUS_RUNTIME=<runtime> \
  byteplus-mediakit-cli video remux-video \
  --video-url <video_url> \
  --container-format <container_format>
```

Replace placeholders with real user input only. Optional flags follow the parameter rules; do not invent URLs, files, enums, or business parameters.

### Parameters

| Path | CLI flag | Type | Required | Default | Enum/range | Description |
| --- | --- | --- | --- | --- | --- | --- |
| `callback_args` | `--callback-args` | string | no | - | - | Custom callback argument returned unchanged when the task finishes, used to correlate business records; maximum 512 bytes. Pass only when the user provides this value; do not generate, infer, or fill it. |
| `callback_url` | `--callback-url` | string | no | - | - | Callback URL that receives this task result; when set it takes priority over the global callback URL and must start with http:// or https://. Pass only when the user provides this value; do not generate, infer, or fill it. |
| `client_token` | `--client-token` | string | no | - | - | Idempotency token; case-sensitive, at most 64 printable ASCII characters. Omit by default. Reuse the same token for retries of the same logical request. Do not mint a new token for each attempt. The CLI does not infer retry intent or auto-generate tokens. Omit by default. Use the user-provided value as-is when specified. When the user explicitly retries, the same logical request must reuse the same token. If a token already exists, reuse it; if earlier requests had no token, you may create one starting with this retry and keep reusing it, but that token does not provide retroactive idempotency for earlier requests. A change in business parameters is a new request and must not reuse the old token. Do not mint a different value for each attempt. The CLI runtime does not infer retry intent and does not auto-generate tokens. |
| `container_format` | `--container-format` | string | yes | "MP4" | enum: ["MP4","FLV","MPEGTS"] | This parameter specifies the target container format. It is required, supports MP4, FLV, and MPEGTS, and defaults to MP4. |
| `metadata_add_tags` | `--metadata-add-tags` | array<object> | no | - | - | This parameter is optional and is an Array of Objects that lists metadata tags to add to the output video. If an added tag has the same name as a kept tag, it overrides the source file's value. It does not take effect when container_format is MPEGTS. |
| `metadata_add_tags[].key` | - | string | no | - | - | The key of a metadata tag to add. |
| `metadata_add_tags[].value` | - | string | no | - | - | The value of a metadata tag to add. |
| `metadata_keep_tags` | `--metadata-keep-tags` | array<string> | no | - | - | Optional. A list of metadata tag keys to keep from the source video. By default, transcoding discards most metadata, such as title and artist, so use this field to specify the tag keys to keep. Provide the values as an Array of Strings. This field does not take effect when container_format is MPEGTS. Pass multiple values as comma-separated strings or by repeating the flag; do not pass a JSON array string. |
| `video_url` | `--video-url` | string | yes | - | - | video_url is required and is the URL of the video to be transcoded. Supported input formats include mainstream video formats such as mp4, flv, ts, avi, mov, wmv, and mkv. Only publicly accessible HTTP/HTTPS URLs are supported as file sources. |

### Result

| Path | Type | Required | Mode | Description |
| --- | --- | --- | --- | --- |
| `request_id` | string | no | Cloud | Request id returned by the service; forwarded only when non-empty. |
| `task_id` | string | yes | Cloud | Unique async task id used to query status and the terminal result. |
| `task_type` | string | no | Cloud | Task type returned by the service; forwarded only when non-empty. |
| `result.duration` | number | no | Cloud terminal | The duration of the output video, measured in seconds. |
| `result.video_url` | string | no | Cloud terminal | The download URL of the output video. This URL is valid for 24 hours by default; please download the video in time. |

After a successful Cloud call, read `task_id` and query the terminal result:

```bash
MEDIAKIT_BYTEPLUS_SURFACE=skill MEDIAKIT_BYTEPLUS_RUNTIME=<runtime> \
  byteplus-mediakit-cli shared query-task --task-id <task_id> --poll-complete
```

### Machine contract

These commands read live help/schema only and do not call Cloud:

```bash
byteplus-mediakit-cli video remux-video --help
byteplus-mediakit-cli video remux-video --schema
```

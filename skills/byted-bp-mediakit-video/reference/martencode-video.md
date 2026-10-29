# martencode-video

## Capability

Low-bitrate HD transcoding analyzes video scenes, motion, content, and textures and selects optimal encoding parameters. The task outputs video with better perceived quality at a relatively lower bitrate, reducing bandwidth costs while improving the viewing experience.

## Parameter rules

- Optional parameters may be filled only when the user specifies them, or when they can be determined accurately from user intent; do not invent values. Omit them when uncertain; ask the user if they are required to complete the task.
- Words such as low-bitrate HD select this capability; they do not license a numeric bitrate. Fill only nested video fields the user named, such as codec from H.264. If Cloud requires additional nested fields to submit, pass only the documented defaults from the parameter table. Never invent a non-default bitrate_kbps or any scale field. On retry, do not change fields the user did not name.

## Cloud

### Command and lifecycle

- Command: `byteplus-mediakit-cli video martencode-video`
- Lifecycle: async
- Return: returns `task_id`; query the terminal result next.

### Usage guide

- Object or object array parameters (`--audio`, `--metadata-add-tags`, `--video`) must be passed as valid JSON strings wrapped in single quotes; field names and nesting must match the Parameters table below.
- Do not pass comma-separated values or plain text for these parameters.
- Array parameters (`--metadata-keep-tags`) must use comma-separated values wrapped in quotes, for example `--metadata-keep-tags "url1,url2"`.
- Individual file names or URLs must not contain commas (`,`); if that happens, ask the user for comma-free paths or URLs before calling.

### Example

```bash
MEDIAKIT_BYTEPLUS_SURFACE=skill MEDIAKIT_BYTEPLUS_RUNTIME=<runtime> \
  byteplus-mediakit-cli video martencode-video \
  --video-url <video_url> \
  --container-format <container_format> \
  --video <video>
```

Replace placeholders with real user input only. Optional flags follow the parameter rules; do not invent URLs, files, enums, or business parameters.

### Parameters

| Path | CLI flag | Type | Required | Default | Enum/range | Description |
| --- | --- | --- | --- | --- | --- | --- |
| `audio` | `--audio` | object | no | - | - | The audio object defines audio transcoding parameters. If it is omitted, audio is encoded with the aac codec while following the source. |
| `audio.bitrate_kbps` | - | integer | yes | 128 | min: 10; max: 500 | Audio bitrate, measured in Kbps, ranging from 10 through 500, with a default value of 128. If this parameter is omitted, the output audio bitrate matches the source audio. |
| `audio.bitrate_mode` | - | string | yes | "cbr" | enum: ["cbr","cae"] | Audio bitrate mode. It supports cbr and cae, and defaults to cbr. cbr tries to keep every second at bitrate_kbps, making file size predictable for strict bandwidth-stability streaming; cbr is supported only when video.codec is h264. cae dynamically adjusts instantaneous bitrate by content complexity while targeting the bitrate_kbps whole-file average; cae is supported only when audio.codec is aac. |
| `audio.channels` | - | integer | no | 2 | enum: [1,2] | Audio channels. Supports 1 for mono and 2 for stereo, and defaults to 2. |
| `audio.codec` | - | string | yes | "aac" | enum: ["aac"] | Audio codec. Supports only aac, and defaults to aac. |
| `audio.sample_rate` | - | integer | yes | 44100 | enum: [8000,11025,12000,16000,22050,24000,32000,44100,48000,64000,88200,96000] | Sets the audio sample rate. The aac codec supports sample rates of 8000, 11025, 12000, 16000, 22050, 24000, 32000, 44100, 48000, 64000, 88200, and 96000 Hz. Defaults to 44100 Hz. |
| `audio.volume_integrated_loudness` | - | number | no | -12 | min: -70; max: -5 | Sets the integrated loudness of the audio, measured in LUFS and ranging from -70 through -5. Defaults to -12. |
| `audio.volume_loudness_range` | - | number | no | 7 | min: 1; max: 20 | Sets the loudness range of the audio, measured in LU and ranging from 1 through 20, and applies when volume_method is 2Pass. Defaults to 7. |
| `audio.volume_method` | - | string | no | - | enum: ["2Pass"] | Setting this to 2Pass enables two-pass loudness analysis and processing and activates the three loudness parameters. If omitted, volume is not processed. |
| `audio.volume_true_peak` | - | number | no | 0 | min: -9; max: 0 | The true peak is measured in dBTP and ranges from -9 through 0. It defaults to 0. |
| `callback_args` | `--callback-args` | string | no | - | - | Custom callback argument returned unchanged when the task finishes, used to correlate business records; maximum 512 bytes. Pass only when the user provides this value; do not generate, infer, or fill it. |
| `callback_url` | `--callback-url` | string | no | - | - | Callback URL that receives this task result; when set it takes priority over the global callback URL and must start with http:// or https://. Pass only when the user provides this value; do not generate, infer, or fill it. |
| `client_token` | `--client-token` | string | no | - | - | Idempotency token; case-sensitive, at most 64 printable ASCII characters. Omit by default. Reuse the same token for retries of the same logical request. Do not mint a new token for each attempt. The CLI does not infer retry intent or auto-generate tokens. Omit by default. Use the user-provided value as-is when specified. When the user explicitly retries, the same logical request must reuse the same token. If a token already exists, reuse it; if earlier requests had no token, you may create one starting with this retry and keep reusing it, but that token does not provide retroactive idempotency for earlier requests. A change in business parameters is a new request and must not reuse the old token. Do not mint a different value for each attempt. The CLI runtime does not infer retry intent and does not auto-generate tokens. |
| `container_format` | `--container-format` | string | yes | "MP4" | enum: ["MP4","FLV","MPEGTS"] | Selects the output container format. Supports MP4, FLV, and MPEGTS, and defaults to MP4. |
| `metadata_add_tags` | `--metadata-add-tags` | array<object> | no | - | - | Lists the metadata tags to add to the output video. An added tag overrides the source value when it has the same name as a kept tag. This parameter has no effect when container_format is MPEGTS. |
| `metadata_add_tags[].key` | - | string | no | - | - | The key of the metadata tag. |
| `metadata_add_tags[].value` | - | string | no | - | - | The value of the metadata tag. |
| `metadata_keep_tags` | `--metadata-keep-tags` | array<string> | no | - | - | Lists the metadata tag keys to keep from the source video. By default, transcoding discards most metadata, such as title and artist. This parameter has no effect when container_format is MPEGTS. Pass multiple values as comma-separated strings or by repeating the flag; do not pass a JSON array string. |
| `video` | `--video` | object | yes | - | - | Defines the video transcoding parameters and is required. |
| `video.bitrate_crf` | - | number | no | 25 | min: 0; max: 51 | This value defaults to 25 and ranges from 0 through 51. A smaller CRF value means higher quality and a larger file, and 0 means lossless. It applies only when bitrate_mode is crf. |
| `video.bitrate_kbps` | - | integer | yes | 2000 | min: 10; max: 50000 | Video bitrate in Kbps, ranging from 10 to 50000. This value is the maximum in crf mode, the average target in abr mode, and the constant target in cbr mode. Defaults to 2000. |
| `video.bitrate_mode` | - | string | yes | "crf" | enum: ["crf","abr","cbr"] | Supports crf, abr, and cbr. crf keeps overall visual quality at bitrate_crf while limiting instantaneous bitrate to bitrate_kbps and is recommended for most scenarios. abr targets the bitrate_kbps average and suits cases where file size must stay within a specific range. cbr targets bitrate_kbps each second and suits streaming with strict transmission-stability requirements. Defaults to crf. |
| `video.codec` | - | string | yes | "h264" | enum: ["h264","h265"] | Supports h264 and h265. Defaults to h264. |
| `video.fps` | - | integer | no | - | min: 1; max: 240 | Frame rate of the output video, ranging from 1 through 240. Setting fps activates fps_mode; if omitted, the output fully follows the source frame rate. |
| `video.fps_mode` | - | string | no | "vfr" | enum: ["vfr","cfr"] | Frame-rate mode that supports vfr and cfr, defaulting to vfr. In vfr mode fps is a maximum, while cfr force-converts output to the constant fps regardless of source frame rate. This mode applies only when fps is set; otherwise the source frame rate is followed and fps_mode is ignored. |
| `video.is_hdr_to_sdr` | - | boolean | no | true | - | Whether to convert HDR to SDR, defaulting to true. Set it to false to preserve HDR. |
| `video.scale_height` | - | integer | no | - | min: 0; max: 4320 | Target height of the output video, measured in px and ranging from 0 through 4320. This setting applies only when scale_type is 2; when only the width or height is supplied, the other edge scales proportionally. |
| `video.scale_long` | - | integer | no | - | min: 0; max: 4320 | Target long-edge length of the output video, measured in px and ranging from 0 through 4320. This setting applies only when scale_type is 1; when only the short or long edge is supplied, the other edge scales proportionally. |
| `video.scale_mode` | - | integer | no | 0 | enum: [0,1,2] | Scaling strategy for upscaling the output video, which defaults to 0. It supports no-upscaling mode 0, stretch-upscaling mode 1, and letterbox-upscaling mode 2, and applies only when scale_type is 1 or 2. |
| `video.scale_short` | - | integer | no | - | min: 0; max: 4320 | Sets the short edge of the video, measured in px and can range from 0 through 4320. This applies only when scale_type is 1; if only the short edge or the long edge is provided, the other edge scales proportionally. |
| `video.scale_type` | - | integer | no | 0 | enum: [0,1,2] | Controls how the video is scaled. It supports follow-source mode 0, long/short edge mode 1, and width/height mode 2. Mode 0 performs no scaling, ignores all scale parameters, and supports up to 8K resolution. Mode 1 activates scale_short and scale_long, scales the other edge proportionally, and supports up to 4K resolution. Mode 2 activates scale_width and scale_height, scales the other edge proportionally, and supports up to 4K resolution. It defaults to 0. |
| `video.scale_width` | - | integer | no | - | min: 0; max: 4320 | Target scaling width in px, ranging from 0 to 4320. It applies only when scale_type is 2, and if only the width or the height is supplied, the other edge scales proportionally. |
| `video_url` | `--video-url` | string | yes | - | - | The URL of the video to be transcoded. Supported video formats include mp4, flv, ts, avi, mov, wmv, and mkv. Only publicly accessible HTTP/HTTPS URLs are supported as the file source. |

### Result

| Path | Type | Required | Mode | Description |
| --- | --- | --- | --- | --- |
| `request_id` | string | no | Cloud | Request id returned by the service; forwarded only when non-empty. |
| `task_id` | string | yes | Cloud | Unique async task id used to query status and the terminal result. |
| `task_type` | string | no | Cloud | Task type returned by the service; forwarded only when non-empty. |
| `result.duration` | number | no | Cloud terminal | The output video duration in seconds. |
| `result.resolution` | string | no | Cloud terminal | The output video resolution can be 240p, 360p, 480p, 540p, 720p, 1080p, 2k, or 4k. |
| `result.video_codec` | string | no | Cloud terminal | The video codec of the output video. |
| `result.video_url` | string | no | Cloud terminal | The download URL of the output video. This URL is valid for 24 hours by default, so please download the output in time. |

After a successful Cloud call, read `task_id` and query the terminal result:

```bash
MEDIAKIT_BYTEPLUS_SURFACE=skill MEDIAKIT_BYTEPLUS_RUNTIME=<runtime> \
  byteplus-mediakit-cli shared query-task --task-id <task_id> --poll-complete
```

### Machine contract

These commands read live help/schema only and do not call Cloud:

```bash
byteplus-mediakit-cli video martencode-video --help
byteplus-mediakit-cli video martencode-video --schema
```

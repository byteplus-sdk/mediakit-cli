# martencode-video

## Capability

Low-bitrate HD transcoding analyzes video scenes, motion, content, and textures and selects optimal encoding parameters. The task outputs video with better perceived quality at a relatively lower bitrate, reducing bandwidth costs while improving the viewing experience.

## Parameter rules

- Optional parameters may be filled only when the user specifies them, or when they can be determined accurately from user intent; do not invent values. Omit them when uncertain; ask the user if they are required to complete the task. Before submitting a business task, resolve required fields and required alternatives from the user's explicit requirements, authoritative prior results, or an unambiguous documented mapping. A media type, genre, or broad processing goal does not by itself supply missing user choices or selection criteria. If a required choice has no documented default and remains unresolved, ask a focused clarification, preserve the user's known constraints, and stop before business submission. Do not invent a custom criterion merely to make a required field present. For optional parameters, infer only values implementing a behavior the user actually requested or required by a declared parameter relation. Do not add extra processing, alignment, formatting, or tuning merely because it seems helpful.
- The codec and bitrate-mode restrictions listed here apply to this BytePlus MediaKit API. Audio bitrate describes the audio stream; total output size also depends on video data and container overhead. Use the user's explicit bitrate when an exact numeric target is needed.
- Words such as low-bitrate HD select this capability; they do not license a numeric bitrate. Fill only nested video fields the user named, such as codec from H.264. If Cloud requires additional nested fields to submit, use a documented default only when it matches the user's requested behavior; otherwise ask for the required target values. Never invent a non-default bitrate_kbps or any scale field. On retry, do not change fields the user did not name.

## Cloud

### Command and lifecycle

- Command: `byteplus-mediakit-cli video martencode-video`
- Lifecycle: async
- Return: returns `task_id`; query the terminal result next.

### Usage guide

- JSON parameters (`--audio`, `--metadata-add-tags`, `--video`) must be valid JSON. Prefer a UTF-8 file using `@params.json`, or pass correctly escaped inline JSON; field names and nesting must match the Parameters table below.
- Do not pass comma-separated values or plain text for these parameters.
- Inline JSON needs shell escaping. A single-quoted shell argument cannot contain an unescaped apostrophe, such as in It's ready; use a JSON file for text containing apostrophes.
- Array parameters (`--metadata-keep-tags`) must use comma-separated values wrapped in quotes, for example `--metadata-keep-tags "value1,value2"`.
- A comma inside one array item must be quoted by CSV rules, including doubled embedded double quotes, or use a JSON array file with `--metadata-keep-tags @params.json`. A single string URL may contain commas; this CSV rule applies only to array encoding.
- For one operation on an ordered collection, include all items in one `--metadata-keep-tags` array argument in the user's order. Submit separate tasks only for independent operations requested by the user or a documented input limit.
- `{}` and `[{}]` illustrate JSON container shapes. Replace `<parameter_json>` in command templates with valid JSON matching the parameter table, including required fields. Empty objects or arrays are valid only when the Schema permits them, including a required object with no required child fields. Required means field presence; null, empty values, zero and false are checked against their own type and constraints. Child fields marked optional remain optional; do not infer required fields from illustrative prose. Do not submit placeholders.

### Example

```bash
byteplus-mediakit-cli video martencode-video \
  --video-url '<video_url>' \
  --container-format '<container_format>' \
  --video '<video_json>'
```

Replace placeholders with real user input only. Optional flags follow the parameter rules; do not invent URLs, files, enums, or business parameters.
Pass each scalar value as one shell argument. The quotes in these templates are illustrative: escape embedded quotes for the actual shell, or use an argument array when invoking a process. Never concatenate raw user input into shell code.

### Parameters

| Path | CLI flag | Type | Required | Default | Enum/range | Description |
| --- | --- | --- | --- | --- | --- | --- |
| `audio` | `--audio` | object | no | - | - | The audio object defines audio transcoding parameters. If it is omitted, audio is encoded with the aac codec while following the source. |
| `audio.bitrate_kbps` | - | integer | no | 128 | min: 10; max: 500 | Audio bitrate, measured in Kbps, ranging from 10 through 500, with a default value of 128. If this parameter is omitted, the output audio bitrate matches the source audio. |
| `audio.bitrate_mode` | - | string | no | "cbr" | enum: ["cbr","cae"] | Audio bitrate mode. It supports cbr and cae, and defaults to cbr. cbr tries to keep every second at bitrate_kbps, making file size predictable for strict bandwidth-stability streaming; cbr is supported only when video.codec is h264. cae dynamically adjusts instantaneous bitrate by content complexity while targeting the bitrate_kbps whole-file average; cae is supported only when audio.codec is aac. |
| `audio.channels` | - | integer | no | 2 | enum: [1,2] | Audio channels. Supports 1 for mono and 2 for stereo, and defaults to 2. |
| `audio.codec` | - | string | no | "aac" | enum: ["aac"] | Audio codec. Supports only aac, and defaults to aac. |
| `audio.sample_rate` | - | integer | no | 44100 | enum: [8000,11025,12000,16000,22050,24000,32000,44100,48000,64000,88200,96000] | Sets the audio sample rate. The aac codec supports sample rates of 8000, 11025, 12000, 16000, 22050, 24000, 32000, 44100, 48000, 64000, 88200, and 96000 Hz. Defaults to 44100 Hz. |
| `audio.volume_integrated_loudness` | - | number | no | -12 | min: -70; max: -5 | Sets the integrated loudness of the audio, measured in LUFS and ranging from -70 through -5. Defaults to -12. |
| `audio.volume_loudness_range` | - | number | no | 7 | min: 1; max: 20 | Sets the loudness range of the audio, measured in LU and ranging from 1 through 20, and applies when volume_method is 2Pass. Defaults to 7. |
| `audio.volume_method` | - | string | no | - | enum: ["2Pass"] | Setting this to 2Pass enables two-pass loudness analysis and processing and activates the three loudness parameters. If omitted, volume is not processed. |
| `audio.volume_true_peak` | - | number | no | 0 | min: -9; max: 0 | The true peak is measured in dBTP and ranges from -9 through 0. It defaults to 0. |
| `callback_args` | `--callback-args` | string | no | - | - | Custom callback argument returned unchanged when the task finishes, used to correlate business records; maximum 512 bytes. Pass only when the user provides this value; do not generate, infer, or fill it. |
| `callback_url` | `--callback-url` | string | no | - | - | Callback URL that receives this task result; when set it takes priority over the global callback URL and must start with http:// or https://. Pass only when the user provides this value; do not generate, infer, or fill it. |
| `client_token` | `--client-token` | string | no | - | - | Idempotency token; case-sensitive, at most 64 printable ASCII characters. Omit by default. Reuse the same token for retries of the same logical request. Do not mint a new token for each attempt. The CLI does not infer retry intent or auto-generate tokens. Omit by default on the first asynchronous submission. Use a user-provided value as-is when specified. A retry of the same uncertain submission must keep the original token policy: reuse its token if provided, or keep the field omitted if it was not provided. After a result exists, an explicitly requested new run must use a new token. Do not mint a different value for each retry attempt. Synchronous tasks do not support idempotency checks. The CLI runtime does not infer retry intent and does not auto-generate tokens. |
| `container_format` | `--container-format` | string | yes | "MP4" (schema default; still required) | enum: ["MP4","FLV","MPEGTS"] | Selects the output container format. Supports MP4, FLV, and MPEGTS, and defaults to MP4. |
| `metadata_add_tags` | `--metadata-add-tags` | array<object> | no | - | - | Lists the metadata tags to add to the output video. An added tag overrides the source value when it has the same name as a kept tag. This parameter has no effect when container_format is MPEGTS. |
| `metadata_add_tags[].key` | - | string | no | - | - | The name of the metadata tag to add. An added tag with the same name overrides the retained source tag. Metadata tags do not take effect for MPEGTS output. |
| `metadata_add_tags[].value` | - | string | no | - | - | The value to assign to the metadata tag. Metadata tags do not take effect for MPEGTS output. |
| `metadata_keep_tags` | `--metadata-keep-tags` | array<string> | no | - | - | Lists the metadata tag keys to keep from the source video. By default, transcoding discards most metadata, such as title and artist. This parameter has no effect when container_format is MPEGTS. |
| `video` | `--video` | object | yes | - | - | Defines the video transcoding parameters and is required. |
| `video.bitrate_crf` | - | number | no | 25 | min: 0; max: 51 | This value defaults to 25 and ranges from 0 through 51. A smaller CRF value means higher quality and a larger file, and 0 means lossless. It applies only when bitrate_mode is crf. |
| `video.bitrate_kbps` | - | integer | yes | 2000 (schema default; still required) | min: 10; max: 50000 | Video bitrate in Kbps, ranging from 10 to 50000. This value is the maximum in crf mode, the average target in abr mode, and the constant target in cbr mode. Defaults to 2000. |
| `video.bitrate_mode` | - | string | yes | "crf" (schema default; still required) | enum: ["crf","abr","cbr"] | Supports crf, abr, and cbr. crf keeps overall visual quality at bitrate_crf while limiting instantaneous bitrate to bitrate_kbps and is recommended for most scenarios. abr targets the bitrate_kbps average and suits cases where file size must stay within a specific range. cbr targets bitrate_kbps each second and suits streaming with strict transmission-stability requirements. Defaults to crf. |
| `video.codec` | - | string | yes | "h264" (schema default; still required) | enum: ["h264","h265"] | Supports h264 and h265. Defaults to h264. |
| `video.fps` | - | integer | no | - | min: 1; max: 240 | Frame rate of the output video, ranging from 1 through 240. Setting fps activates fps_mode; if omitted, the output fully follows the source frame rate. |
| `video.fps_mode` | - | string | no | "vfr" | enum: ["vfr","cfr"] | Frame-rate mode that supports vfr and cfr, defaulting to vfr. In vfr mode fps is a maximum, while cfr force-converts output to the constant fps regardless of source frame rate. This mode applies only when fps is set; otherwise the source frame rate is followed and fps_mode is ignored. |
| `video.is_hdr_to_sdr` | - | boolean | no | true | - | Whether to convert HDR to SDR, defaulting to true. Set it to false to preserve HDR. |
| `video.scale_height` | - | integer | no | - | min: 0; max: 4320 | Target height of the output video, measured in px and ranging from 0 through 4320. This setting applies only when scale_type is 2; when only the width or height is supplied, the other edge scales proportionally. |
| `video.scale_long` | - | integer | no | - | min: 0; max: 4320 | Target long-edge length of the output video, measured in px and ranging from 0 through 4320. This setting applies only when scale_type is 1; when only the short or long edge is supplied, the other edge scales proportionally. |
| `video.scale_mode` | - | integer | no | 0 | enum: [0,1,2] | Scaling strategy for upscaling the output video, which defaults to 0. It supports no-upscaling mode 0, stretch-upscaling mode 1, and letterbox-upscaling mode 2, and applies only when scale_type is 1 or 2. |
| `video.scale_short` | - | integer | no | - | min: 0; max: 4320 | Sets the short edge of the video, measured in px and can range from 0 through 4320. This applies only when scale_type is 1; if only the short edge or the long edge is provided, the other edge scales proportionally. |
| `video.scale_type` | - | integer | no | 0 | enum: [0,1,2] | Controls how the video is scaled. It supports follow-source mode 0, long/short edge mode 1, and width/height mode 2. Mode 0 performs no scaling, ignores all scale parameters, and supports up to 8K resolution. Mode 1 activates scale_short and scale_long, scales the other edge proportionally, and supports up to 4K resolution. Mode 2 activates scale_width and scale_height, scales the other edge proportionally, and supports up to 4K resolution. It defaults to 0. |
| `video.scale_width` | - | integer | no | - | min: 0; max: 4320 | Target scaling width in px, ranging from 0 to 4320. It applies only when scale_type is 2, and if only the width or the height is supplied, the other edge scales proportionally. |
| `video_url` | `--video-url` | string | yes | - | min length: 1 | The URL of the video to be transcoded. Supported video formats include mp4, flv, ts, avi, mov, wmv, and mkv. Cloud requests use publicly accessible HTTP/HTTPS URLs. CLI inputs may be local file paths or public HTTP/HTTPS URLs. The declared media input format also accepts vod:// and tos:// resource URIs. The CLI handles local file upload internally; pass the original input directly. |

### Result

| Path | Type | Required | Mode | Description |
| --- | --- | --- | --- | --- |
| `request_id` | string | no | Cloud | Request id returned by the service; forwarded only when non-empty. Constraints: min length: 1. |
| `task_id` | string | yes | Cloud | Unique async task id used to query status and the terminal result. Constraints: min length: 1. |
| `task_type` | string | no | Cloud | Task type returned by the service; forwarded only when non-empty. Constraints: min length: 1. |
| `duration` | number | no | Cloud terminal | The output video duration in seconds. |
| `resolution` | string | no | Cloud terminal | The output video resolution can be 240p, 360p, 480p, 540p, 720p, 1080p, 2k, or 4k. Constraints: enum: ["240p","360p","480p","540p","720p","1080p","2k","4k"]. |
| `video_codec` | string | no | Cloud terminal | The video codec of the output video. Constraints: enum: ["h264","h265"]. |
| `video_url` | string | no | Cloud terminal | The download URL of the output video. This URL is valid for 24 hours by default, so please download the output in time. |

After a successful Cloud call, read the returned `task_id` and check its current status. This command queries once and returns; when the task is still running, query the same task again later until terminal. Do not resubmit the business task. Use `--poll-complete` only when continuous waiting is intended; it waits without a limit by default. Pass a polling timeout or attempt limit only when specified by the user. Interrupting the CLI stops waiting without canceling the Cloud task:

```bash
byteplus-mediakit-cli shared query-task --task-id '<task_id>'
```

After a synchronous business tool succeeds or an asynchronous task reaches completed, return its actual result and any output asset URLs to the user. Use the returned URLs as inputs to requested dependent steps. Download assets or perform additional local processing only when the user requests it, and retain the Cloud result URL alongside any downloaded file. Do not present task submission as completion. Do not replace a completed Cloud output with a locally recreated file from the original input, or infer permission for local recreation from a requested duration or quality target. If a returned asset's metadata or name conflicts with the requested constraints, probe that asset with an available metadata tool before claiming a mismatch or performing a correction. Corrective processing must consume the completed Cloud output, preserving its lineage; present the original Cloud result URL alongside any corrected output and explain any confirmed limitation. Never omit an available completed result URL in favor of an unfinished correction or a local fallback.

### Machine contract

These commands read live help/schema only and do not call Cloud:

```bash
byteplus-mediakit-cli video martencode-video --help
byteplus-mediakit-cli video martencode-video --schema
```

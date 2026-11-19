# transcode-video

## Capability

Video transcoding converts one video bitstream into another, typically changing the encoding format, encoding parameters, and container format.

## Parameter rules

- Optional parameters may be filled only when the user specifies them, or when they can be determined accurately from user intent; do not invent values. Omit them when uncertain; ask the user if they are required to complete the task. A conditionally required field still needs explicit presence even when the Schema lists a default. Use that documented default only when it matches the user's requested behavior; otherwise ask for the required target values before submitting. Before submitting a business task, resolve required fields and required alternatives from the user's explicit requirements, authoritative prior results, or an unambiguous documented mapping. A media type, genre, or broad processing goal does not by itself supply missing user choices or selection criteria. If a required choice has no documented default and remains unresolved, ask a focused clarification, preserve the user's known constraints, and stop before business submission. Do not invent a custom criterion merely to make a required field present. For optional parameters, infer only values implementing a behavior the user actually requested or required by a declared parameter relation. Do not add extra processing, alignment, formatting, or tuning merely because it seems helpful.
- The codec and bitrate-mode restrictions listed here apply to this BytePlus MediaKit API. Audio bitrate describes the audio stream; total output size also depends on video data and container overhead. Use the user's explicit bitrate when an exact numeric target is needed.

## Cloud

### Command and lifecycle

- Command: `byteplus-mediakit-cli video transcode-video`
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

#### Video transcode options

Providing `video.fps_mode` requires explicit `video.fps` in the same request. In `vfr` mode, `fps` is the maximum frame rate: a lower source rate is kept, and a higher source rate is reduced to the specified rate. In `cfr` mode, `fps` is the forced constant output frame rate. Omit both fields when the user wants the source frame rate and has not requested a frame-rate mode.

Choose `video.scale_type` using the documented scaling paths:

| Value | Scaling path |
| --- | --- |
| `0` | Follow the source with no scaling, supporting up to 8K. `scale_mode`, `scale_width`, `scale_height`, `scale_short` and `scale_long` are ignored. |
| `1` | Limit the long or short side using `scale_long` or `scale_short`; the other side scales proportionally. Supports up to 4K. |
| `2` | Limit width or height using `scale_width` or `scale_height`; the other side scales proportionally. Supports up to 4K. |

`video.scale_mode` applies only when `video.scale_type` is `1` or `2`:

| Value | Behavior |
| --- | --- |
| `0` | No upsampling. Shrink a source larger than the target; keep the original dimensions when the source is smaller. |
| `1` | Stretch to the target width and height, which may distort the picture. |
| `2` | Scale proportionally to fit inside the target frame and pad the remaining space with black bars. |

### Example

```bash
byteplus-mediakit-cli video transcode-video \
  --video-url '<video_url>' \
  --container-format '<container_format>' \
  --video '<video_json>'
```

Replace placeholders with real user input only. Optional flags follow the parameter rules; do not invent URLs, files, enums, or business parameters.
Pass each scalar value as one shell argument. The quotes in these templates are illustrative: escape embedded quotes for the actual shell, or use an argument array when invoking a process. Never concatenate raw user input into shell code.

### Parameters

| Path | CLI flag | Type | Required | Default | Enum/range | Description |
| --- | --- | --- | --- | --- | --- | --- |
| `audio` | `--audio` | object | no | - | - | Optional. Configures the audio transcoding parameters. If this object is not provided, audio is transcoded with default parameters (codec aac, and other parameters match those of the source file). |
| `audio.bitrate_kbps` | - | integer | no | 128 | min: 10; max: 500 | Audio bitrate in Kbps, from 10 to 500. The official parameter table lists a default of 128 and also states that omission preserves the source audio bitrate; it does not distinguish omission of the audio object from omission of this child field. Omit bitrate_kbps when the user requests source-bitrate preservation, and pass an explicit numeric target when the user supplies one. Verify the resulting audio bitrate instead of promising a specific value for the unresolved omission cases. |
| `audio.bitrate_mode` | - | string | no | "cbr" | enum: ["cbr","cae"] | The audio bitrate control mode for this BytePlus MediaKit API. cbr is the documented default and uses bitrate_kbps as the constant audio bitrate target; this API supports it only when video.codec is h264. cae adjusts bitrate to content complexity and is supported only with audio.codec=aac. Verify the actual output audio bitrate and total file size after processing; a target audio bitrate alone does not determine the size of the complete video file. |
| `audio.channels` | - | integer | no | 2 | enum: [1,2] | The number of audio channels. It can be set to `1` for mono or `2` for stereo (used by default). This field is optional. |
| `audio.codec` | - | string | no | "aac" | enum: ["aac"] | The audio codec. Currently only `aac` is supported, and it is used by default. |
| `audio.sample_rate` | - | integer | no | 44100 | enum: [8000,11025,12000,16000,22050,24000,32000,44100,48000,64000,88200,96000] | The audio sample rate, in Hz. Defaults to `44100`. When the audio codec is `aac`, the supported sample rates are `8000`, `11025`, `12000`, `16000`, `22050`, `24000`, `32000`, `44100`, `48000`, `64000`, `88200`, and `96000`. |
| `audio.volume_integrated_loudness` | - | number | yes when audio.volume_method=2Pass | -12 | min: -70; max: -5 | Optional. Specifies the target integrated loudness in LUFS, which is used to set the overall perceived volume of the audio. The value range is `[-70, -5]`, with a default of `-12`. Required when `volume_method` is `2Pass`. |
| `audio.volume_loudness_range` | - | number | no | 7 | min: 1; max: 20 | Optional. Specifies the loudness range (dynamic range) in LU, which is used to adjust the difference between the loudest and quietest parts of the audio. The value range is `[1, 20]`, with a default of `7`. Effective when `volume_method` is `2Pass`. |
| `audio.volume_method` | - | string | no | - | enum: ["2Pass"] | Optional. Controls loudness normalization. Set to `2Pass` to enable two-pass loudness analysis and processing, in which case `volume_integrated_loudness`, `volume_true_peak`, and `volume_loudness_range` take effect. If not set, the volume is not processed. |
| `audio.volume_true_peak` | - | number | yes when audio.volume_method=2Pass | 0 | min: -9; max: 0 | Optional. Specifies the true peak level in dBTP, which is used to set the maximum limit of the audio signal to prevent clipping distortion. The value range is `[-9, 0]`, with a default of `0`. Required when `volume_method` is `2Pass`. |
| `callback_args` | `--callback-args` | string | no | - | - | Custom callback argument returned unchanged when the task finishes, used to correlate business records; maximum 512 bytes. Pass only when the user provides this value; do not generate, infer, or fill it. |
| `callback_url` | `--callback-url` | string | no | - | - | Callback URL that receives this task result; when set it takes priority over the global callback URL and must start with http:// or https://. Pass only when the user provides this value; do not generate, infer, or fill it. |
| `client_token` | `--client-token` | string | no | - | - | Idempotency token; case-sensitive, at most 64 printable ASCII characters. Omit by default. Reuse the same token for retries of the same logical request. Do not mint a new token for each attempt. The CLI does not infer retry intent or auto-generate tokens. Omit by default on the first asynchronous submission. Use a user-provided value as-is when specified. A retry of the same uncertain submission must keep the original token policy: reuse its token if provided, or keep the field omitted if it was not provided. After a result exists, an explicitly requested new run must use a new token. Do not mint a different value for each retry attempt. Synchronous tasks do not support idempotency checks. The CLI runtime does not infer retry intent and does not auto-generate tokens. |
| `container_format` | `--container-format` | string | yes | "MP4" (schema default; still required) | enum: ["MP4","FLV","MPEGTS"] | Required. Specifies the container format of the output video. Can be set to `MP4` (the default), `FLV`, or `MPEGTS`. |
| `metadata_add_tags` | `--metadata-add-tags` | array<object> | no | - | - | Optional. The list of metadata tags to add to the output video, provided as an Array of Objects. Each tag consists of a key and a value. If an added tag has the same name as a kept tag, the setting here overrides the source file's value. This parameter does not take effect when the container_format is set to MPEGTS. |
| `metadata_add_tags[].key` | - | string | no | - | - | The name of the metadata tag to add. An added tag with the same name overrides the retained source tag. Metadata tags do not take effect for MPEGTS output. |
| `metadata_add_tags[].value` | - | string | no | - | - | The value to assign to the metadata tag. Metadata tags do not take effect for MPEGTS output. |
| `metadata_keep_tags` | `--metadata-keep-tags` | array<string> | no | - | - | The metadata tags to keep from the source video, provided as an Array of Strings. By default, transcoding discards most metadata such as title and artist, so use this parameter to specify the tag keys to keep. This parameter is optional and does not take effect when container_format is set to MPEGTS. |
| `video` | `--video` | object | yes | - | - | Video transcoding parameter configuration. This parameter is required. |
| `video.bitrate_crf` | - | number | no | 25 | min: 0; max: 51 | The CRF (Constant Rate Factor) parameter is the primary quality controller and is effective only when bitrate_mode is crf. Its value ranges from 0 to 51, with a default of 25. A smaller value produces higher quality and a larger file, and 0 means lossless. This parameter is optional. |
| `video.bitrate_kbps` | - | integer | no | 2000 | min: 10; max: 50000 | The target video bitrate, in Kbps, ranging from 10 to 50000 with a default of 2000. Its exact effect depends on the bitrate mode: in crf mode it is the maximum bitrate limit, in abr mode it is the average bitrate target, and in cbr mode it is the constant bitrate target. |
| `video.bitrate_mode` | - | string | no | "crf" | enum: ["crf","abr","cbr"] | The bitrate control mode, which defaults to crf. In crf (Constant Rate Factor) mode, the system tries to keep the overall visual quality at your target level, where a smaller value yields higher quality, while ensuring the instantaneous bitrate does not exceed the bitrate limit. This mode offers the best balance between quality and file size and is recommended for most scenarios. |
| `video.codec` | - | string | no | "h264" | enum: ["h264","h265"] | The video codec, which defaults to h264 and can be set to h264 or h265. |
| `video.fps` | - | integer | yes when video.fps_mode is provided | - | min: 1; max: 240 | The target frame rate, with a supported value ranging from 1 to 240. Setting this parameter activates the fps_mode logic: in vfr mode it represents the maximum frame rate, while in cfr mode it represents the constant frame rate. If not set, the source's frame rate is used. This parameter is optional. |
| `video.fps_mode` | - | string | no | "vfr" | enum: ["vfr","cfr"] | Controls the frame-rate mode for the transcoded video and is optional. It takes effect only after you set the fps parameter. The default is vfr, a variable frame-rate mode in which the fps value acts as the maximum frame-rate limit, so when the source frame rate is lower than your fps value the source rate is kept. Providing video.fps_mode requires explicit video.fps. |
| `video.is_hdr_to_sdr` | - | boolean | no | true | - | Optional. Whether to convert HDR (High Dynamic Range) video to SDR (Standard Dynamic Range) video. Defaults to `true`; set to `false` to keep HDR. |
| `video.scale_height` | - | integer | no | - | min: 0; max: 4320 | The target height, in px. The value range is [0, 4320]. Effective only when scale_type is 2 (width/height limit mode). If only one of scale_width or scale_height is provided, the other side scales proportionally. This parameter is optional. |
| `video.scale_long` | - | integer | no | - | min: 0; max: 4320 | The target long side, in px. The value range is [0, 4320]. Effective only when scale_type is 1 (long/short-side limit mode). If only one of scale_short or scale_long is provided, the other side scales proportionally. This parameter is optional. |
| `video.scale_mode` | - | integer | no | 0 | enum: [0,1,2] | The fill and stretch strategy. It takes effect only when scale_type is 1 or 2, and is used to handle mismatches between your target dimensions and the source aspect ratio. Set it to 0 for no upsampling, where the video is shrunk if the source is larger than the target and the original size is kept if it is smaller, or to 1 for stretch upsampling, which force-stretches to the target width and height. Defaults to 0. This parameter is optional. |
| `video.scale_short` | - | integer | no | - | min: 0; max: 4320 | The target length of the short side of the video, in px, with a value range of [0, 4320]. It takes effect only when scale_type is set to 1 (long/short-side limit mode). If only one of scale_short or scale_long is provided, the other side scales proportionally. This field is optional. |
| `video.scale_type` | - | integer | no | 0 | enum: [0,1,2] | The video scaling mode, which defaults to 0. It can be set to 0 for follow-source mode, where no scaling is performed and scale_mode, scale_width, scale_height, scale_short, and scale_long are all ignored; this mode supports up to 8K resolution. It can be set to 1 for long/short-side limit mode, which activates scale_short and scale_long so you can set the long or short side of the output while the other side scales proportionally. This field is optional. |
| `video.scale_width` | - | integer | no | - | min: 0; max: 4320 | The target width of the video, in px, with a value range of [0, 4320]. It takes effect only when scale_type is set to 2 (width/height limit mode). If only one of scale_width or scale_height is provided, the other side scales proportionally. This field is optional. |
| `video_url` | `--video-url` | string | yes | - | min length: 1 | The URL of the video to be transcoded. This parameter is required. Cloud requests use publicly accessible HTTP/HTTPS URLs. Supported formats are mainstream video formats such as mp4, flv, ts, avi, mov, wmv, mkv. CLI inputs may be local file paths or public HTTP/HTTPS URLs. The declared media input format also accepts vod:// and tos:// resource URIs. The CLI handles local file upload internally; pass the original input directly. |

### Parameter relations

- `$.audio.volume_integrated_loudness` is required when audio.volume_method=2Pass.
- `$.audio.volume_true_peak` is required when audio.volume_method=2Pass.
- `$.video.fps` is required when video.fps_mode is provided.
- `$.video.codec` must satisfy const: "h264" when audio is provided and audio.bitrate_mode=cbr.

### Result

| Path | Type | Required | Mode | Description |
| --- | --- | --- | --- | --- |
| `request_id` | string | no | Cloud | Request id returned by the service; forwarded only when non-empty. Constraints: min length: 1. |
| `task_id` | string | yes | Cloud | Unique async task id used to query status and the terminal result. Constraints: min length: 1. |
| `task_type` | string | no | Cloud | Task type returned by the service; forwarded only when non-empty. Constraints: min length: 1. |
| `duration` | number | no | Cloud terminal | The duration of the output video, in seconds. |
| `resolution` | string | no | Cloud terminal | The resolution specification of the transcoded video. It can be 240p, 360p, 480p, 540p, 720p, 1080p, 2k, 4k, and so on. Constraints: enum: ["240p","360p","480p","540p","720p","1080p","2k","4k"]. |
| `video_codec` | string | no | Cloud terminal | The codec of the transcoded video. |
| `video_url` | string | no | Cloud terminal | The download URL of the output video. This URL is valid for 24 hours by default; please save the output file promptly. |

After a successful Cloud call, read the returned `task_id` and check its current status. This command queries once and returns; when the task is still running, query the same task again later until terminal. Do not resubmit the business task. Use `--poll-complete` only when continuous waiting is intended; it waits without a limit by default. Pass a polling timeout or attempt limit only when specified by the user. Interrupting the CLI stops waiting without canceling the Cloud task:

```bash
byteplus-mediakit-cli shared query-task --task-id '<task_id>'
```

After a synchronous business tool succeeds or an asynchronous task reaches completed, return its actual result and any output asset URLs to the user. Use the returned URLs as inputs to requested dependent steps. Download assets or perform additional local processing only when the user requests it, and retain the Cloud result URL alongside any downloaded file. Do not present task submission as completion. Do not replace a completed Cloud output with a locally recreated file from the original input, or infer permission for local recreation from a requested duration or quality target. If a returned asset's metadata or name conflicts with the requested constraints, probe that asset with an available metadata tool before claiming a mismatch or performing a correction. Corrective processing must consume the completed Cloud output, preserving its lineage; present the original Cloud result URL alongside any corrected output and explain any confirmed limitation. Never omit an available completed result URL in favor of an unfinished correction or a local fallback.

### Machine contract

These commands read live help/schema only and do not call Cloud:

```bash
byteplus-mediakit-cli video transcode-video --help
byteplus-mediakit-cli video transcode-video --schema
```

# transcode-video

## Capability

Video transcoding converts one video bitstream into another, typically changing the encoding format, encoding parameters, and container format.

## Parameter rules

- Optional parameters may be filled only when the user specifies them, or when they can be determined accurately from user intent; do not invent values. Omit them when uncertain; ask the user if they are required to complete the task.

## Cloud

### Command and lifecycle

- Command: `byteplus-mediakit-cli video transcode-video`
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
  byteplus-mediakit-cli video transcode-video \
  --video-url <video_url> \
  --container-format <container_format> \
  --video <video>
```

Replace placeholders with real user input only. Optional flags follow the parameter rules; do not invent URLs, files, enums, or business parameters.

### Parameters

| Path | CLI flag | Type | Required | Default | Enum/range | Description |
| --- | --- | --- | --- | --- | --- | --- |
| `audio` | `--audio` | object | no | - | - | Optional. Configures the audio transcoding parameters. If this object is not provided, audio is transcoded with default parameters (codec aac, and other parameters match those of the source file). |
| `audio.bitrate_kbps` | - | integer | yes | 128 | min: 10; max: 500 | The audio bitrate, in Kbps. The value ranges from 10 to 500, with a default of 128. If this parameter is not set, the output audio bitrate matches the source audio. |
| `audio.bitrate_mode` | - | string | yes | "cbr" | enum: ["cbr","cae"] | The audio bitrate control mode. It can be set to `cbr`, the default constant bitrate mode that is supported only when `video.codec` is `h264` and keeps every second of the audio stream strictly at the bitrate set by `bitrate_kbps`, making the file size precisely predictable and suitable for streaming scenarios with high bandwidth-stability requirements, or to `cae`, an adaptive bitrate mode. |
| `audio.channels` | - | integer | no | 2 | enum: [1,2] | The number of audio channels. It can be set to `1` for mono or `2` for stereo (used by default). This field is optional. |
| `audio.codec` | - | string | yes | "aac" | enum: ["aac"] | The audio codec. Currently only `aac` is supported, and it is used by default. |
| `audio.sample_rate` | - | integer | yes | 44100 | enum: [8000,11025,12000,16000,22050,24000,32000,44100,48000,64000,88200,96000] | The audio sample rate, in Hz. Defaults to `44100`. When the audio codec is `aac`, the supported sample rates are `8000`, `11025`, `12000`, `16000`, `22050`, `24000`, `32000`, `44100`, `48000`, `64000`, `88200`, and `96000`. |
| `audio.volume_integrated_loudness` | - | number | no | -12 | min: -70; max: -5 | Optional. Specifies the target integrated loudness in LUFS, which is used to set the overall perceived volume of the audio. The value range is `[-70, -5]`, with a default of `-12`. Required when `volume_method` is `2Pass`. |
| `audio.volume_loudness_range` | - | number | no | 7 | min: 1; max: 20 | Optional. Specifies the loudness range (dynamic range) in LU, which is used to adjust the difference between the loudest and quietest parts of the audio. The value range is `[1, 20]`, with a default of `7`. Effective when `volume_method` is `2Pass`. |
| `audio.volume_method` | - | string | no | - | enum: ["2Pass"] | Optional. Controls loudness normalization. Set to `2Pass` to enable two-pass loudness analysis and processing, in which case `volume_integrated_loudness`, `volume_true_peak`, and `volume_loudness_range` take effect. If not set, the volume is not processed. |
| `audio.volume_true_peak` | - | number | no | 0 | min: -9; max: 0 | Optional. Specifies the true peak level in dBTP, which is used to set the maximum limit of the audio signal to prevent clipping distortion. The value range is `[-9, 0]`, with a default of `0`. Required when `volume_method` is `2Pass`. |
| `callback_args` | `--callback-args` | string | no | - | - | Custom callback argument returned unchanged when the task finishes, used to correlate business records; maximum 512 bytes. Pass only when the user provides this value; do not generate, infer, or fill it. |
| `callback_url` | `--callback-url` | string | no | - | - | Callback URL that receives this task result; when set it takes priority over the global callback URL and must start with http:// or https://. Pass only when the user provides this value; do not generate, infer, or fill it. |
| `client_token` | `--client-token` | string | no | - | - | Idempotency token; case-sensitive, at most 64 printable ASCII characters. Omit by default. Reuse the same token for retries of the same logical request. Do not mint a new token for each attempt. The CLI does not infer retry intent or auto-generate tokens. Omit by default. Use the user-provided value as-is when specified. When the user explicitly retries, the same logical request must reuse the same token. If a token already exists, reuse it; if earlier requests had no token, you may create one starting with this retry and keep reusing it, but that token does not provide retroactive idempotency for earlier requests. A change in business parameters is a new request and must not reuse the old token. Do not mint a different value for each attempt. The CLI runtime does not infer retry intent and does not auto-generate tokens. |
| `container_format` | `--container-format` | string | yes | "MP4" | enum: ["MP4","FLV","MPEGTS"] | Required. Specifies the container format of the output video. Can be set to `MP4` (the default), `FLV`, or `MPEGTS`. |
| `metadata_add_tags` | `--metadata-add-tags` | array<object> | no | - | - | Optional. The list of metadata tags to add to the output video, provided as an Array of Objects. Each tag consists of a key and a value. If an added tag has the same name as a kept tag, the setting here overrides the source file's value. This parameter does not take effect when the container_format is set to MPEGTS. |
| `metadata_add_tags[].key` | - | string | no | - | - | The key of the metadata tag. |
| `metadata_add_tags[].value` | - | string | no | - | - | The value of the metadata tag. |
| `metadata_keep_tags` | `--metadata-keep-tags` | array<string> | no | - | - | The metadata tags to keep from the source video, provided as an Array of Strings. By default, transcoding discards most metadata such as title and artist, so use this parameter to specify the tag keys to keep. This parameter is optional and does not take effect when container_format is set to MPEGTS. Pass multiple values as comma-separated strings or by repeating the flag; do not pass a JSON array string. |
| `video` | `--video` | object | yes | - | - | Video transcoding parameter configuration. This parameter is required. |
| `video.bitrate_crf` | - | number | no | 25 | min: 0; max: 51 | The CRF (Constant Rate Factor) parameter is the primary quality controller and is effective only when bitrate_mode is crf. Its value ranges from 0 to 51, with a default of 25. A smaller value produces higher quality and a larger file, and 0 means lossless. This parameter is optional. |
| `video.bitrate_kbps` | - | integer | yes | 2000 | min: 10; max: 50000 | The target video bitrate, in Kbps, ranging from 10 to 50000 with a default of 2000. Its exact effect depends on the bitrate mode: in crf mode it is the maximum bitrate limit, in abr mode it is the average bitrate target, and in cbr mode it is the constant bitrate target. |
| `video.bitrate_mode` | - | string | yes | "crf" | enum: ["crf","abr","cbr"] | The bitrate control mode, which defaults to crf. In crf (Constant Rate Factor) mode, the system tries to keep the overall visual quality at your target level, where a smaller value yields higher quality, while ensuring the instantaneous bitrate does not exceed the bitrate limit. This mode offers the best balance between quality and file size and is recommended for most scenarios. |
| `video.codec` | - | string | yes | "h264" | enum: ["h264","h265"] | The video codec, which defaults to h264 and can be set to h264 or h265. |
| `video.fps` | - | integer | no | - | min: 1; max: 240 | The target frame rate, with a supported value ranging from 1 to 240. Setting this parameter activates the fps_mode logic: in vfr mode it represents the maximum frame rate, while in cfr mode it represents the constant frame rate. If not set, the source's frame rate is used. This parameter is optional. |
| `video.fps_mode` | - | string | no | "vfr" | enum: ["vfr","cfr"] | Controls the frame-rate mode for the transcoded video and is optional. It takes effect only after you set the fps parameter; if you do not provide fps, the source frame rate is used and this setting is ignored. The default is vfr, a variable frame-rate mode in which the fps value acts as the maximum frame-rate limit, so when the source frame rate is lower than your fps value the source rate is kept. |
| `video.is_hdr_to_sdr` | - | boolean | no | true | - | Optional. Whether to convert HDR (High Dynamic Range) video to SDR (Standard Dynamic Range) video. Defaults to `true`; set to `false` to keep HDR. |
| `video.scale_height` | - | integer | no | - | min: 0; max: 4320 | The target height, in px. The value range is [0, 4320]. Effective only when scale_type is 2 (width/height limit mode). If only one of scale_width or scale_height is provided, the other side scales proportionally. This parameter is optional. |
| `video.scale_long` | - | integer | no | - | min: 0; max: 4320 | The target long side, in px. The value range is [0, 4320]. Effective only when scale_type is 1 (long/short-side limit mode). If only one of scale_short or scale_long is provided, the other side scales proportionally. This parameter is optional. |
| `video.scale_mode` | - | integer | no | 0 | enum: [0,1,2] | The fill and stretch strategy. It takes effect only when scale_type is 1 or 2, and is used to handle mismatches between your target dimensions and the source aspect ratio. Set it to 0 for no upsampling, where the video is shrunk if the source is larger than the target and the original size is kept if it is smaller, or to 1 for stretch upsampling, which force-stretches to the target width and height. Defaults to 0. This parameter is optional. |
| `video.scale_short` | - | integer | no | - | min: 0; max: 4320 | The target length of the short side of the video, in px, with a value range of [0, 4320]. It takes effect only when scale_type is set to 1 (long/short-side limit mode). If only one of scale_short or scale_long is provided, the other side scales proportionally. This field is optional. |
| `video.scale_type` | - | integer | no | 0 | enum: [0,1,2] | The video scaling mode, which defaults to 0. It can be set to 0 for follow-source mode, where no scaling is performed and scale_mode, scale_width, scale_height, scale_short, and scale_long are all ignored; this mode supports up to 8K resolution. It can be set to 1 for long/short-side limit mode, which activates scale_short and scale_long so you can set the long or short side of the output while the other side scales proportionally. This field is optional. |
| `video.scale_width` | - | integer | no | - | min: 0; max: 4320 | The target width of the video, in px, with a value range of [0, 4320]. It takes effect only when scale_type is set to 2 (width/height limit mode). If only one of scale_width or scale_height is provided, the other side scales proportionally. This field is optional. |
| `video_url` | `--video-url` | string | yes | - | - | The URL of the video to be transcoded. This parameter is required. Only publicly accessible HTTP/HTTPS URLs are supported. Supported formats are mainstream video formats such as mp4, flv, ts, avi, mov, wmv, mkv. |

### Result

| Path | Type | Required | Mode | Description |
| --- | --- | --- | --- | --- |
| `request_id` | string | no | Cloud | Request id returned by the service; forwarded only when non-empty. |
| `task_id` | string | yes | Cloud | Unique async task id used to query status and the terminal result. |
| `task_type` | string | no | Cloud | Task type returned by the service; forwarded only when non-empty. |
| `result.duration` | number | no | Cloud terminal | The duration of the output video, in seconds. |
| `result.resolution` | string | no | Cloud terminal | The resolution specification of the transcoded video. It can be 240p, 360p, 480p, 540p, 720p, 1080p, 2k, 4k, and so on. |
| `result.video_codec` | string | no | Cloud terminal | The codec of the transcoded video. |
| `result.video_url` | string | no | Cloud terminal | The download URL of the output video. This URL is valid for 24 hours by default; please save the output file promptly. |

After a successful Cloud call, read `task_id` and query the terminal result:

```bash
MEDIAKIT_BYTEPLUS_SURFACE=skill MEDIAKIT_BYTEPLUS_RUNTIME=<runtime> \
  byteplus-mediakit-cli shared query-task --task-id <task_id> --poll-complete
```

### Machine contract

These commands read live help/schema only and do not call Cloud:

```bash
byteplus-mediakit-cli video transcode-video --help
byteplus-mediakit-cli video transcode-video --schema
```

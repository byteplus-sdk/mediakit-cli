# transcode-audio

## Capability

Audio transcoding converts one audio bitstream into another and can change the encoding format, encoding parameters, and container format to adapt to business scenarios, playback devices, and network environments.

## Parameter rules

- Optional parameters may be filled only when the user specifies them, or when they can be determined accurately from user intent; do not invent values. Omit them when uncertain; ask the user if they are required to complete the task.
- Fill nested audio fields the user named, such as bitrate_kbps from a stated kbps. If Cloud requires additional nested fields to submit, pass only the documented defaults from the parameter table. Never invent a non-default sample_rate, channels, or loudness. On retry, do not change fields the user did not name.

## Cloud

### Command and lifecycle

- Command: `byteplus-mediakit-cli audio transcode-audio`
- Lifecycle: async
- Return: returns `task_id`; query the terminal result next.

### Usage guide

- Object or object array parameters (`--audio`, `--metadata-add-tags`) must be passed as valid JSON strings wrapped in single quotes; field names and nesting must match the Parameters table below.
- Do not pass comma-separated values or plain text for these parameters.
- Array parameters (`--metadata-keep-tags`) must use comma-separated values wrapped in quotes, for example `--metadata-keep-tags "url1,url2"`.
- Individual file names or URLs must not contain commas (`,`); if that happens, ask the user for comma-free paths or URLs before calling.

### Example

```bash
MEDIAKIT_BYTEPLUS_SURFACE=skill MEDIAKIT_BYTEPLUS_RUNTIME=<runtime> \
  byteplus-mediakit-cli audio transcode-audio \
  --audio-url <audio_url> \
  --container-format <container_format> \
  --audio <audio>
```

Replace placeholders with real user input only. Optional flags follow the parameter rules; do not invent URLs, files, enums, or business parameters.

### Parameters

| Path | CLI flag | Type | Required | Default | Enum/range | Description |
| --- | --- | --- | --- | --- | --- | --- |
| `audio` | `--audio` | object | yes | - | - | Contains the audio parameter configuration. |
| `audio.bitrate_kbps` | - | integer | yes | 128 | min: 10; max: 500 | The audio bitrate in Kbps. Supported values range from a minimum of 10 to a maximum of 500, and it defaults to 128. If left empty, the output audio bitrate matches the source audio. |
| `audio.bitrate_mode` | - | string | yes | "cbr" | enum: ["cbr","cae"] | Controls the audio bitrate mode. Supported values are cbr and cae, and the mode defaults to cbr. In cbr mode, the encoder tries to keep every second of the audio stream strictly at bitrate_kbps, making file size precisely predictable, so cbr is suitable for streaming scenarios with high bandwidth-stability requirements. In cae mode, the encoder dynamically adjusts instantaneous bitrate based on content complexity while ensuring the overall average bitrate approaches bitrate_kbps. cae is supported only when container_format is M4A. |
| `audio.channels` | - | integer | no | - | enum: [1,2] | Specifies the number of audio channels. Supported values are 1 for Mono and 2 for Stereo. This field is optional and defaults to 2. |
| `audio.sample_rate` | - | integer | yes | 48000 | enum: [8000,11025,12000,16000,22050,24000,32000,44100,48000,64000,88200,96000] | Specifies the audio sample rate in Hz, and it defaults to 48000. Filling this field according to the target encoder is recommended. MP3 supports sample rates 8000, 11025, 12000, 16000, 22050, 24000, 32000, 44100, and 48000. AAC supports sample rates 8000, 11025, 12000, 16000, 22050, 24000, 32000, 44100, 48000, 64000, 88200, and 96000. Opus supports sample rate 48000. |
| `audio.volume_integrated_loudness` | - | number | no | -12 | min: -70; max: -5 | Sets the overall perceived audio volume in LUFS. The minimum value is -70 and the maximum value is -5, with a default of -12. This field is optional. |
| `audio.volume_loudness_range` | - | number | no | 7 | min: 1; max: 20 | Adjusts the difference between the loudest and quietest audio parts, measured in LU. The minimum value is 1 and the maximum value is 20, with a default of 7. This field is optional and takes effect when volume_method is set to 2Pass. |
| `audio.volume_method` | - | string | no | - | enum: ["2Pass"] | Controls loudness normalization for the audio, and is optional. If it is not set, the volume is not processed. Setting it to 2Pass enables two-pass loudness analysis and processing, and the integrated loudness, true peak, and loudness range settings then take effect. |
| `audio.volume_true_peak` | - | number | no | 0 | min: -9; max: 0 | Sets the maximum audio signal limit to prevent clipping distortion, in dBTP. This field is optional and defaults to 0, which is also the maximum allowed value. The minimum is -9. |
| `audio_url` | `--audio-url` | string | yes | - | - | The URL of the audio to be transcoded. This field is required, and only publicly accessible HTTP/HTTPS URLs are supported as the file source. Supported input formats include mainstream audio formats such as mp3, m4a, wav, wma, amr, aac, ogg, and flac. |
| `callback_args` | `--callback-args` | string | no | - | - | Custom callback argument returned unchanged when the task finishes, used to correlate business records; maximum 512 bytes. Pass only when the user provides this value; do not generate, infer, or fill it. |
| `callback_url` | `--callback-url` | string | no | - | - | Callback URL that receives this task result; when set it takes priority over the global callback URL and must start with http:// or https://. Pass only when the user provides this value; do not generate, infer, or fill it. |
| `client_token` | `--client-token` | string | no | - | - | Idempotency token; case-sensitive, at most 64 printable ASCII characters. Omit by default. Reuse the same token for retries of the same logical request. Do not mint a new token for each attempt. The CLI does not infer retry intent or auto-generate tokens. Omit by default. Use the user-provided value as-is when specified. When the user explicitly retries, the same logical request must reuse the same token. If a token already exists, reuse it; if earlier requests had no token, you may create one starting with this retry and keep reusing it, but that token does not provide retroactive idempotency for earlier requests. A change in business parameters is a new request and must not reuse the old token. Do not mint a different value for each attempt. The CLI runtime does not infer retry intent and does not auto-generate tokens. |
| `container_format` | `--container-format` | string | yes | "MP3" | enum: ["MP3","M4A","OGG"] | Specifies the target container format. Specifying this field is recommended, and it defaults to MP3. Supported container formats are MP3, M4A, and OGG. The system selects the MP3 encoder for MP3, the AAC encoder for M4A, and the Opus encoder for OGG. |
| `metadata_add_tags` | `--metadata-add-tags` | array<object> | no | - | - | An optional Array of Objects specifying the metadata tags to add to the output audio. |
| `metadata_add_tags[].key` | - | string | no | - | - | The key of a metadata tag. |
| `metadata_add_tags[].value` | - | string | no | - | - | The value of a metadata tag. |
| `metadata_keep_tags` | `--metadata-keep-tags` | array<string> | no | - | - | An optional list of metadata tag keys to keep from the source audio, given as an Array of Strings. By default, transcoding discards most metadata, such as title and artist. Pass multiple values as comma-separated strings or by repeating the flag; do not pass a JSON array string. |

### Result

| Path | Type | Required | Mode | Description |
| --- | --- | --- | --- | --- |
| `request_id` | string | no | Cloud | Request id returned by the service; forwarded only when non-empty. |
| `task_id` | string | yes | Cloud | Unique async task id used to query status and the terminal result. |
| `task_type` | string | no | Cloud | Task type returned by the service; forwarded only when non-empty. |
| `result.audio_url` | string | no | Cloud terminal | The download URL of the output video. This URL is valid for 24 hours by default. |
| `result.duration` | number | no | Cloud terminal | The duration of the output audio, in seconds. |

After a successful Cloud call, read `task_id` and query the terminal result:

```bash
MEDIAKIT_BYTEPLUS_SURFACE=skill MEDIAKIT_BYTEPLUS_RUNTIME=<runtime> \
  byteplus-mediakit-cli shared query-task --task-id <task_id> --poll-complete
```

### Machine contract

These commands read live help/schema only and do not call Cloud:

```bash
byteplus-mediakit-cli audio transcode-audio --help
byteplus-mediakit-cli audio transcode-audio --schema
```

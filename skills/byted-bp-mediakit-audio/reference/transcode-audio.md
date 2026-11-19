# transcode-audio

## Capability

Audio transcoding converts one audio bitstream into another and can change the encoding format, encoding parameters, and container format to adapt to business scenarios, playback devices, and network environments.

## Parameter rules

- Optional parameters may be filled only when the user specifies them, or when they can be determined accurately from user intent; do not invent values. Omit them when uncertain; ask the user if they are required to complete the task. A conditionally required field still needs explicit presence even when the Schema lists a default. Use that documented default only when it matches the user's requested behavior; otherwise ask for the required target values before submitting. Before submitting a business task, resolve required fields and required alternatives from the user's explicit requirements, authoritative prior results, or an unambiguous documented mapping. A media type, genre, or broad processing goal does not by itself supply missing user choices or selection criteria. If a required choice has no documented default and remains unresolved, ask a focused clarification, preserve the user's known constraints, and stop before business submission. Do not invent a custom criterion merely to make a required field present. For optional parameters, infer only values implementing a behavior the user actually requested or required by a declared parameter relation. Do not add extra processing, alignment, formatting, or tuning merely because it seems helpful.
- The codec and bitrate-mode restrictions listed here apply to this BytePlus MediaKit API. Audio bitrate describes the audio stream; audio file size also depends on duration and container overhead. Use the user's explicit bitrate when an exact numeric target is needed.
- Fill nested audio fields the user named, such as bitrate_kbps from a stated kbps. If Cloud requires additional nested fields to submit, use a documented default only when it matches the user's requested behavior; otherwise ask for the required target values. Never invent a non-default sample_rate, channels, or loudness. On retry, do not change fields the user did not name.

## Cloud

### Command and lifecycle

- Command: `byteplus-mediakit-cli audio transcode-audio`
- Lifecycle: async
- Return: returns `task_id`; query the terminal result next.

### Usage guide

- JSON parameters (`--audio`, `--metadata-add-tags`) must be valid JSON. Prefer a UTF-8 file using `@params.json`, or pass correctly escaped inline JSON; field names and nesting must match the Parameters table below.
- Do not pass comma-separated values or plain text for these parameters.
- Inline JSON needs shell escaping. A single-quoted shell argument cannot contain an unescaped apostrophe, such as in It's ready; use a JSON file for text containing apostrophes.
- Array parameters (`--metadata-keep-tags`) must use comma-separated values wrapped in quotes, for example `--metadata-keep-tags "value1,value2"`.
- A comma inside one array item must be quoted by CSV rules, including doubled embedded double quotes, or use a JSON array file with `--metadata-keep-tags @params.json`. A single string URL may contain commas; this CSV rule applies only to array encoding.
- For one operation on an ordered collection, include all items in one `--metadata-keep-tags` array argument in the user's order. Submit separate tasks only for independent operations requested by the user or a documented input limit.
- `{}` and `[{}]` illustrate JSON container shapes. Replace `<parameter_json>` in command templates with valid JSON matching the parameter table, including required fields. Empty objects or arrays are valid only when the Schema permits them, including a required object with no required child fields. Required means field presence; null, empty values, zero and false are checked against their own type and constraints. Child fields marked optional remain optional; do not infer required fields from illustrative prose. Do not submit placeholders.

### Example

```bash
byteplus-mediakit-cli audio transcode-audio \
  --audio-url '<audio_url>'
```

Replace placeholders with real user input only. Optional flags follow the parameter rules; do not invent URLs, files, enums, or business parameters.
Pass each scalar value as one shell argument. The quotes in these templates are illustrative: escape embedded quotes for the actual shell, or use an argument array when invoking a process. Never concatenate raw user input into shell code.

### Parameters

| Path | CLI flag | Type | Required | Default | Enum/range | Description |
| --- | --- | --- | --- | --- | --- | --- |
| `audio` | `--audio` | object | no | - | - | Contains the audio parameter configuration. |
| `audio.bitrate_kbps` | - | integer | no | 128 | min: 10; max: 500 | The audio bitrate in Kbps. Supported values range from a minimum of 10 to a maximum of 500, and it defaults to 128. |
| `audio.bitrate_mode` | - | string | no | "cbr" | enum: ["cbr","cae"] | Controls the audio bitrate mode. Supported values are cbr and cae, and the mode defaults to cbr. In cbr mode, the encoder tries to keep every second of the audio stream strictly at bitrate_kbps, making file size precisely predictable, so cbr is suitable for streaming scenarios with high bandwidth-stability requirements. In cae mode, the encoder dynamically adjusts instantaneous bitrate based on content complexity while ensuring the overall average bitrate approaches bitrate_kbps. cae is supported only when container_format is M4A. |
| `audio.channels` | - | integer | no | - | enum: [1,2] | Specifies the number of audio channels. Supported values are 1 for Mono and 2 for Stereo. When omitted, the client does not send a default value and the backend handles it according to the source audio and task configuration. |
| `audio.sample_rate` | - | integer | no | 48000 | enum: [8000,11025,12000,16000,22050,24000,32000,44100,48000,64000,88200,96000] | Specifies the audio sample rate in Hz, and it defaults to 48000. Filling this field according to the target encoder is recommended. MP3 supports sample rates 8000, 11025, 12000, 16000, 22050, 24000, 32000, 44100, and 48000. AAC supports sample rates 8000, 11025, 12000, 16000, 22050, 24000, 32000, 44100, 48000, 64000, 88200, and 96000. Opus supports sample rate 48000. |
| `audio.volume_integrated_loudness` | - | number | yes when audio.volume_method=2Pass | -12 | min: -70; max: -5 | Sets the overall perceived audio volume in LUFS. The minimum value is -70 and the maximum value is -5, with a default of -12. This field is optional. |
| `audio.volume_loudness_range` | - | number | no | 7 | min: 1; max: 20 | Adjusts the difference between the loudest and quietest audio parts, measured in LU. The minimum value is 1 and the maximum value is 20, with a default of 7. This field is optional and takes effect when volume_method is set to 2Pass. |
| `audio.volume_method` | - | string | no | - | enum: ["2Pass"] | Controls loudness normalization for the audio, and is optional. If it is not set, the volume is not processed. Setting it to 2Pass enables two-pass loudness analysis and processing, and the integrated loudness, true peak, and loudness range settings then take effect. |
| `audio.volume_true_peak` | - | number | yes when audio.volume_method=2Pass | 0 | min: -9; max: 0 | Sets the maximum audio signal limit to prevent clipping distortion, in dBTP. This field is optional and defaults to 0, which is also the maximum allowed value. The minimum is -9. |
| `audio_url` | `--audio-url` | string | yes | - | min length: 1 | The URL of the audio to be transcoded. This field is required, and Cloud requests use publicly accessible HTTP/HTTPS URLs. Supported input formats include mainstream audio formats such as mp3, m4a, wav, wma, amr, aac, ogg, and flac. CLI inputs may be local file paths or public HTTP/HTTPS URLs. The declared media input format also accepts vod:// and tos:// resource URIs. The CLI handles local file upload internally; pass the original input directly. |
| `callback_args` | `--callback-args` | string | no | - | - | Custom callback argument returned unchanged when the task finishes, used to correlate business records; maximum 512 bytes. Pass only when the user provides this value; do not generate, infer, or fill it. |
| `callback_url` | `--callback-url` | string | no | - | - | Callback URL that receives this task result; when set it takes priority over the global callback URL and must start with http:// or https://. Pass only when the user provides this value; do not generate, infer, or fill it. |
| `client_token` | `--client-token` | string | no | - | - | Idempotency token; case-sensitive, at most 64 printable ASCII characters. Omit by default. Reuse the same token for retries of the same logical request. Do not mint a new token for each attempt. The CLI does not infer retry intent or auto-generate tokens. Omit by default on the first asynchronous submission. Use a user-provided value as-is when specified. A retry of the same uncertain submission must keep the original token policy: reuse its token if provided, or keep the field omitted if it was not provided. After a result exists, an explicitly requested new run must use a new token. Do not mint a different value for each retry attempt. Synchronous tasks do not support idempotency checks. The CLI runtime does not infer retry intent and does not auto-generate tokens. |
| `container_format` | `--container-format` | string | yes when audio is provided and audio.bitrate_mode=cae | "MP3" | enum: ["MP3","M4A","OGG"]; when audio is provided and audio.bitrate_mode=cae: const: "M4A" | Specifies the target container format. Specifying this field is recommended, and it defaults to MP3. Supported container formats are MP3, M4A, and OGG. The system selects the MP3 encoder for MP3, the AAC encoder for M4A, and the Opus encoder for OGG. |
| `metadata_add_tags` | `--metadata-add-tags` | array<object> | no | - | - | An optional Array of Objects specifying the metadata tags to add to the output audio. |
| `metadata_add_tags[].key` | - | string | no | - | - | The name of the metadata tag to add. An added tag with the same name overrides the retained source tag. |
| `metadata_add_tags[].value` | - | string | no | - | - | The value to assign to the metadata tag. |
| `metadata_keep_tags` | `--metadata-keep-tags` | array<string> | no | - | - | An optional list of metadata tag keys to keep from the source audio, given as an Array of Strings. By default, transcoding discards most metadata, such as title and artist. |

### Parameter relations

- `$.audio.volume_integrated_loudness` is required when audio.volume_method=2Pass.
- `$.audio.volume_true_peak` is required when audio.volume_method=2Pass.
- `$.container_format` is required when audio is provided and audio.bitrate_mode=cae.
- `$.container_format` must satisfy const: "M4A" when audio is provided and audio.bitrate_mode=cae.

### Result

| Path | Type | Required | Mode | Description |
| --- | --- | --- | --- | --- |
| `request_id` | string | no | Cloud | Request id returned by the service; forwarded only when non-empty. Constraints: min length: 1. |
| `task_id` | string | yes | Cloud | Unique async task id used to query status and the terminal result. Constraints: min length: 1. |
| `task_type` | string | no | Cloud | Task type returned by the service; forwarded only when non-empty. Constraints: min length: 1. |
| `audio_url` | string | no | Cloud terminal | The download URL of the output audio. This URL is valid for 24 hours by default. |
| `duration` | number | no | Cloud terminal | The duration of the output audio, in seconds. |

After a successful Cloud call, read the returned `task_id` and check its current status. This command queries once and returns; when the task is still running, query the same task again later until terminal. Do not resubmit the business task. Use `--poll-complete` only when continuous waiting is intended; it waits without a limit by default. Pass a polling timeout or attempt limit only when specified by the user. Interrupting the CLI stops waiting without canceling the Cloud task:

```bash
byteplus-mediakit-cli shared query-task --task-id '<task_id>'
```

After a synchronous business tool succeeds or an asynchronous task reaches completed, return its actual result and any output asset URLs to the user. Use the returned URLs as inputs to requested dependent steps. Download assets or perform additional local processing only when the user requests it, and retain the Cloud result URL alongside any downloaded file. Do not present task submission as completion. Do not replace a completed Cloud output with a locally recreated file from the original input, or infer permission for local recreation from a requested duration or quality target. If a returned asset's metadata or name conflicts with the requested constraints, probe that asset with an available metadata tool before claiming a mismatch or performing a correction. Corrective processing must consume the completed Cloud output, preserving its lineage; present the original Cloud result URL alongside any corrected output and explain any confirmed limitation. Never omit an available completed result URL in favor of an unfinished correction or a local fallback.

### Machine contract

These commands read live help/schema only and do not call Cloud:

```bash
byteplus-mediakit-cli audio transcode-audio --help
byteplus-mediakit-cli audio transcode-audio --schema
```

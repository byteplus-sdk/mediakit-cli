# mix-audio

## Capability

Mixes multiple audio files, such as background music, sound effects, and voice, into one new audio file.

## Parameter rules

- Optional parameters may be filled only when the user specifies them, or when they can be determined accurately from user intent; do not invent values. Omit them when uncertain; ask the user if they are required to complete the task. Before submitting a business task, resolve required fields and required alternatives from the user's explicit requirements, authoritative prior results, or an unambiguous documented mapping. A media type, genre, or broad processing goal does not by itself supply missing user choices or selection criteria. If a required choice has no documented default and remains unresolved, ask a focused clarification, preserve the user's known constraints, and stop before business submission. Do not invent a custom criterion merely to make a required field present. For optional parameters, infer only values implementing a behavior the user actually requested or required by a declared parameter relation. Do not add extra processing, alignment, formatting, or tuning merely because it seems helpful.
- Omit `format` unless the user names a documented format or explicitly requires preserving the source format. For format preservation, use a documented original value when available; otherwise confirm the input format from reliable metadata and pass its supported value. Ask when the source format is unknown or unsupported. If the user names a documented format, pass it even when it equals the default. After a call succeeds or is intercepted, do not retry the same tool with an invented format.

## Cloud

### Command and lifecycle

- Command: `byteplus-mediakit-cli editing mix-audio`
- Lifecycle: async
- Return: returns `task_id`; query the terminal result next.

### Usage guide

- Array parameters (`--audio-urls`) must use comma-separated values wrapped in quotes, for example `--audio-urls "url1,url2"`.
- A comma inside one array item must be quoted by CSV rules, including doubled embedded double quotes, or use a JSON array file with `--audio-urls @params.json`. A single string URL may contain commas; this CSV rule applies only to array encoding.
- For one operation on an ordered collection, include all items in one `--audio-urls` array argument in the user's order. Submit separate tasks only for independent operations requested by the user or a documented input limit.

### Example

```bash
byteplus-mediakit-cli editing mix-audio \
  --audio-urls "url1,url2"
```

Replace placeholders with real user input only. Optional flags follow the parameter rules; do not invent URLs, files, enums, or business parameters.
Pass each scalar value as one shell argument. The quotes in these templates are illustrative: escape embedded quotes for the actual shell, or use an argument array when invoking a process. Never concatenate raw user input into shell code.

### Parameters

| Path | CLI flag | Type | Required | Default | Enum/range | Description |
| --- | --- | --- | --- | --- | --- | --- |
| `audio_urls` | `--audio-urls` | array<string> | yes | - | min items: 1; max items: 100; element min length: 1 | The list of audio file URLs to be mixed. This field is required and must contain at least 1 item and no more than 100 items. Each array item may be a local file path or public HTTP/HTTPS URL. The declared media input format also accepts vod:// and tos:// resource URIs. The CLI handles local file upload internally; pass the original input directly. Cloud requests use publicly accessible HTTP/HTTPS URLs. Common supported input audio formats include mp3, wav, and flac. A single input file is recommended not to exceed 10 GB. CLI inputs may be local file paths or public HTTP/HTTPS URLs. The CLI handles local file upload internally; pass the original input directly. |
| `callback_args` | `--callback-args` | string | no | - | - | Custom callback argument returned unchanged when the task finishes, used to correlate business records; maximum 512 bytes. Pass only when the user provides this value; do not generate, infer, or fill it. |
| `callback_url` | `--callback-url` | string | no | - | - | Callback URL that receives this task result; when set it takes priority over the global callback URL and must start with http:// or https://. Pass only when the user provides this value; do not generate, infer, or fill it. |
| `client_token` | `--client-token` | string | no | - | - | Idempotency token; case-sensitive, at most 64 printable ASCII characters. Omit by default. Reuse the same token for retries of the same logical request. Do not mint a new token for each attempt. The CLI does not infer retry intent or auto-generate tokens. Omit by default on the first asynchronous submission. Use a user-provided value as-is when specified. A retry of the same uncertain submission must keep the original token policy: reuse its token if provided, or keep the field omitted if it was not provided. After a result exists, an explicitly requested new run must use a new token. Do not mint a different value for each retry attempt. Synchronous tasks do not support idempotency checks. The CLI runtime does not infer retry intent and does not auto-generate tokens. |
| `format` | `--format` | string | no | "m4a" | enum: ["mp3","m4a","ogg","flac","wav"] | Specifies the output audio format. Optional; defaults to m4a. Supports mp3, m4a, ogg, flac, and wav. |
| `queue_id` | `--queue-id` | string | no | - | - | Target queue ID for the submitted task. Omit to use the system default queue. Pass only when the user provides this value; do not generate, infer, or fill it. |

### Result

| Path | Type | Required | Mode | Description |
| --- | --- | --- | --- | --- |
| `request_id` | string | no | Cloud | Request id returned by the service; forwarded only when non-empty. Constraints: min length: 1. |
| `task_id` | string | yes | Cloud | Unique async task id used to query status and the terminal result. Constraints: min length: 1. |
| `task_type` | string | no | Cloud | Task type returned by the service; forwarded only when non-empty. Constraints: min length: 1. |
| `audio_url` | string | no | Cloud terminal | The download URL for the generated mixed audio file. It is an HTTPS temporary download link that is valid for 24 hours by default. |
| `duration` | number | no | Cloud terminal | The total duration of the output audio, measured in seconds. The output duration is based on the longest input audio file. |

After a successful Cloud call, read the returned `task_id` and check its current status. This command queries once and returns; when the task is still running, query the same task again later until terminal. Do not resubmit the business task. Use `--poll-complete` only when continuous waiting is intended; it waits without a limit by default. Pass a polling timeout or attempt limit only when specified by the user. Interrupting the CLI stops waiting without canceling the Cloud task:

```bash
byteplus-mediakit-cli shared query-task --task-id '<task_id>'
```

After a synchronous business tool succeeds or an asynchronous task reaches completed, return its actual result and any output asset URLs to the user. Use the returned URLs as inputs to requested dependent steps. Download assets or perform additional local processing only when the user requests it, and retain the Cloud result URL alongside any downloaded file. Do not present task submission as completion. Do not replace a completed Cloud output with a locally recreated file from the original input, or infer permission for local recreation from a requested duration or quality target. If a returned asset's metadata or name conflicts with the requested constraints, probe that asset with an available metadata tool before claiming a mismatch or performing a correction. Corrective processing must consume the completed Cloud output, preserving its lineage; present the original Cloud result URL alongside any corrected output and explain any confirmed limitation. Never omit an available completed result URL in favor of an unfinished correction or a local fallback.

### Machine contract

These commands read live help/schema only and do not call Cloud:

```bash
byteplus-mediakit-cli editing mix-audio --help
byteplus-mediakit-cli editing mix-audio --schema
```

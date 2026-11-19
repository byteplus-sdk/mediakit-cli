# call-maas-proxy

## Capability

Call a supported ModelArk or Seed Speech model through AI MediaKit by supplying its service, API name and model-specific arguments.

## Parameter rules

- Optional parameters may be filled only when the user specifies them, or when they can be determined accurately from user intent; do not invent values. Omit them when uncertain; ask the user if they are required to complete the task. Before submitting a business task, resolve required fields and required alternatives from the user's explicit requirements, authoritative prior results, or an unambiguous documented mapping. A media type, genre, or broad processing goal does not by itself supply missing user choices or selection criteria. If a required choice has no documented default and remains unresolved, ask a focused clarification, preserve the user's known constraints, and stop before business submission. Do not invent a custom criterion merely to make a required field present. For optional parameters, infer only values implementing a behavior the user actually requested or required by a declared parameter relation. Do not add extra processing, alignment, formatting, or tuning merely because it seems helpful.

## Cloud

### Command and lifecycle

- Command: `byteplus-mediakit-cli shared call-maas-proxy`
- Lifecycle: async
- Return: returns `task_id`; query the terminal result next.

### Usage guide

- Object parameters (`--maas-params`) must be valid JSON. Prefer a UTF-8 file with `--maas-params @params.json`, or pass correctly escaped inline JSON; field names and nesting must match the “Enum/range” column and nested field descriptions in the Parameters table below.
- Do not pass comma-separated values or plain text for these parameters.
- Inline JSON needs shell escaping. A single-quoted shell argument cannot contain an unescaped apostrophe, such as in It's ready; use a JSON file for text containing apostrophes.
- `{}` and `[{}]` illustrate JSON container shapes. Replace `<parameter_json>` in command templates with valid JSON matching the parameter table, including required fields. Empty objects or arrays are valid only when the Schema permits them, including a required object with no required child fields. Required means field presence; null, empty values, zero and false are checked against their own type and constraints. Child fields marked optional remain optional; do not infer required fields from illustrative prose. Do not submit placeholders.

#### ModelArk image generation

For a text-to-image request, select `api=ark.image_generation` and provide a JSON `maas_params` object containing `model` and the user's `prompt`. The requested model must be enabled in ModelArk, and AI MediaKit must have the corresponding cross-service authorization. Ask for missing authorization or model choice before submitting; never invent an inference endpoint.

The BytePlus MaaS proxy API and its image-generation guide document this mapping:

| Requested model | Documented ModelArk identifier |
| --- | --- |
| Seedream 5.0 Pro (Seedream 5 Pro) | `dola-seedream-5-0-pro-260628` |

Use this mapping when the user requests that model and confirms it is enabled. Use a different enabled model or endpoint only when the caller supplies it or an authoritative model mapping establishes it; do not generalize this identifier to other Seedream versions.

For Seedream 5.0 Pro image generation, `prompt` is required. Preserve the user's content and text; do not add a reference image, output format, size, or watermark setting that the user did not request. Omit optional settings to use the model service's documented defaults. This model's default size is `2K`; supported size levels are `1K`, `1.5K`, and `2K`. An explicit `widthxheight` size must have 921,600 through 4,624,220 total pixels and an aspect ratio from 1/16 through 16. The resolution-level method and explicit dimensions are alternative size representations. The proxy's image-to-image contract supports reference URLs through `image_url` and does not support `image_base64`; do not substitute the direct ModelArk API's Base64 input shape.

Seedream 5.0 Pro does not support the sequential-image-generation or streaming options documented for other Seedream models. Do not infer those options from sibling models. The documented default `response_format` is `url`, output format is `jpeg`, and watermark is enabled. If the user specifies an optional setting, check its supported values and model conditions against the service contract before passing it unchanged.

Submit the proxy task once, retain its real `task_id`, and query that ID with the shared query protocol until a terminal state. On success, read `maas_result.data[].url` for generated image URLs; these expire within 24 hours. Report the actual returned asset or a truthful terminal failure. Do not fabricate result URLs or interpret the immediate submission response as the generated image.

These model settings belong inside `maas_params`, not to new MediaKit top-level flags. Other proxy interfaces keep their own model service contracts and must not inherit the image-generation constraints above.

### Example

```bash
byteplus-mediakit-cli shared call-maas-proxy \
  --api '<api>' \
  --maas-params '<maas_params_json>'
```

Replace placeholders with real user input only. Optional flags follow the parameter rules; do not invent URLs, files, enums, or business parameters.
Pass each scalar value as one shell argument. The quotes in these templates are illustrative: escape embedded quotes for the actual shell, or use an argument array when invoking a process. Never concatenate raw user input into shell code.

### Parameters

| Path | CLI flag | Type | Required | Default | Enum/range | Description |
| --- | --- | --- | --- | --- | --- | --- |
| `api` | `--api` | string | yes | - | enum: ["ark.image_generation","ark.video_generation","doubao.audio_generation","doubao.asr"] | Target model interface. Ark.image_generation generates images, ark.video_generation generates videos, doubao.audio_generation generates audio and doubao.asr performs standard audio-file recognition. |
| `callback_args` | `--callback-args` | string | no | - | - | Custom callback argument returned unchanged when the task finishes, used to correlate business records; maximum 512 bytes. Pass only when the user provides this value; do not generate, infer, or fill it. |
| `callback_url` | `--callback-url` | string | no | - | - | Callback URL that receives this task result; when set it takes priority over the global callback URL and must start with http:// or https://. Pass only when the user provides this value; do not generate, infer, or fill it. |
| `client_token` | `--client-token` | string | no | - | - | Idempotency token; case-sensitive, at most 64 printable ASCII characters. Omit by default. Reuse the same token for retries of the same logical request. Do not mint a new token for each attempt. The CLI does not infer retry intent or auto-generate tokens. Omit by default on the first asynchronous submission. Use a user-provided value as-is when specified. A retry of the same uncertain submission must keep the original token policy: reuse its token if provided, or keep the field omitted if it was not provided. After a result exists, an explicitly requested new run must use a new token. Do not mint a different value for each retry attempt. Synchronous tasks do not support idempotency checks. The CLI runtime does not infer retry intent and does not auto-generate tokens. |
| `maas_params` | `--maas-params` | object | yes | - | - | Parameters required by the selected model service, forwarded unchanged. Use that service's official request contract. MediaKit manages the asynchronous task, callbacks and idempotency. Image-to-image reference inputs must use image_url; image_base64 is unsupported. |
| `queue_id` | `--queue-id` | string | no | - | - | Target queue ID for the submitted task. Omit to use the system default queue. Pass only when the user provides this value; do not generate, infer, or fill it. |

### Result

| Path | Type | Required | Mode | Description |
| --- | --- | --- | --- | --- |
| `request_id` | string | no | Cloud | Request id returned by the service; forwarded only when non-empty. Constraints: min length: 1. |
| `task_id` | string | yes | Cloud | Unique async task id used to query status and the terminal result. Constraints: min length: 1. |
| `task_type` | string | no | Cloud | Task type returned by the service; forwarded only when non-empty. Constraints: min length: 1. |
| `api` | string | no | Cloud terminal | Model interface that was called, matching the requested api. |
| `duration` | number | no | Cloud terminal | Total generated audio or video duration in seconds. Image tasks report zero; some tasks, including speech recognition, may omit this field. |
| `maas_result` | object | no | Cloud terminal | Unmodified result from the selected model service. Interpret its fields, usage and asset links using that service's official result contract. |

After a successful Cloud call, read the returned `task_id` and check its current status. This command queries once and returns; when the task is still running, query the same task again later until terminal. Do not resubmit the business task. Use `--poll-complete` only when continuous waiting is intended; it waits without a limit by default. Pass a polling timeout or attempt limit only when specified by the user. Interrupting the CLI stops waiting without canceling the Cloud task:

```bash
byteplus-mediakit-cli shared query-task --task-id '<task_id>'
```

After a synchronous business tool succeeds or an asynchronous task reaches completed, return its actual result and any output asset URLs to the user. Use the returned URLs as inputs to requested dependent steps. Download assets or perform additional local processing only when the user requests it, and retain the Cloud result URL alongside any downloaded file. Do not present task submission as completion. Do not replace a completed Cloud output with a locally recreated file from the original input, or infer permission for local recreation from a requested duration or quality target. If a returned asset's metadata or name conflicts with the requested constraints, probe that asset with an available metadata tool before claiming a mismatch or performing a correction. Corrective processing must consume the completed Cloud output, preserving its lineage; present the original Cloud result URL alongside any corrected output and explain any confirmed limitation. Never omit an available completed result URL in favor of an unfinished correction or a local fallback.

### Machine contract

These commands read live help/schema only and do not call Cloud:

```bash
byteplus-mediakit-cli shared call-maas-proxy --help
byteplus-mediakit-cli shared call-maas-proxy --schema
```

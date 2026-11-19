# enhance-video

## Capability

Enhances video quality using AI algorithms for denoising, color enhancement, artifact removal, and super-resolution.

## Parameter rules

- Optional parameters may be filled only when the user specifies them, or when they can be determined accurately from user intent; do not invent values. Omit them when uncertain; ask the user if they are required to complete the task. A conditionally required field still needs explicit presence even when the Schema lists a default. Use that documented default only when it matches the user's requested behavior; otherwise ask for the required target values before submitting. Before submitting a business task, resolve required fields and required alternatives from the user's explicit requirements, authoritative prior results, or an unambiguous documented mapping. A media type, genre, or broad processing goal does not by itself supply missing user choices or selection criteria. If a required choice has no documented default and remains unresolved, ask a focused clarification, preserve the user's known constraints, and stop before business submission. Do not invent a custom criterion merely to make a required field present. For optional parameters, infer only values implementing a behavior the user actually requested or required by a declared parameter relation. Do not add extra processing, alignment, formatting, or tuning merely because it seems helpful.
- This capability is one of two documented enhancement paths: enhance-video (quality-first, including cinema-grade professional) and enhance-video-fast (speed-first). If the user only asks to enhance the video and does not choose quality/cinema-grade versus speed/low-latency, ask which path to use. Do not submit either tool until they choose. If you already asked and the user has not answered, stop; do not submit a guessed tool.

## Cloud

### Command and lifecycle

- Command: `byteplus-mediakit-cli video enhance-video`
- Lifecycle: async
- Return: returns `task_id`; query the terminal result next.

### Parameters

| Path | CLI flag | Type | Required | Default | Enum/range | Description |
| --- | --- | --- | --- | --- | --- | --- |
| `bit_depth` | `--bit-depth` | integer | yes when not (pix_fmt is provided) and codec is provided; yes when not (pix_fmt is provided) and dynamic_range=hdr | 8 | enum: [8,10,12,16]; when not (pix_fmt is provided) and dynamic_range=hdr: enum: [10,12,16] | Professional output color depth. Eight bits is the default and supports H.264 or H.265; 10 supports H.265 or ProRes; 12 supports H.265; 16 supports FFV1 or EXR. Sixteen-bit input is limited to 40 seconds, ignores bitrate settings and is processed serially, so it may take longer. |
| `bitrate` | `--bitrate` | integer | no | - | min: 10; max: 150000 | Target average bitrate from 10 to 150,000 kilobits per second. Overrides bitrate_level when supplied; omission derives the bitrate from level, resolution and frame rate. |
| `bitrate_level` | `--bitrate-level` | string | no | "medium" | enum: ["low","medium","high"] | Controls the average bitrate of the output video, affecting visual quality and final file size. This parameter is optional and defaults to medium. It can be set to low, medium, or high, where low selects a low bitrate, medium selects the recommended medium bitrate, and high selects a high bitrate. The actual bitrate is automatically calculated from the target resolution and frame rate. The documented low, medium, and high target output bitrates in Mbps are defined by output resolution and frame-rate bands from 15 through 120 fps. The actual output bitrate is typically 0.8 to 1.5 times the listed target because the system dynamically adjusts for content complexity. |
| `callback_args` | `--callback-args` | string | no | - | - | Custom callback argument returned unchanged when the task finishes, used to correlate business records; maximum 512 bytes. Pass only when the user provides this value; do not generate, infer, or fill it. |
| `callback_url` | `--callback-url` | string | no | - | - | Callback URL that receives this task result; when set it takes priority over the global callback URL and must start with http:// or https://. Pass only when the user provides this value; do not generate, infer, or fill it. |
| `client_token` | `--client-token` | string | no | - | - | Idempotency token; case-sensitive, at most 64 printable ASCII characters. Omit by default. Reuse the same token for retries of the same logical request. Do not mint a new token for each attempt. The CLI does not infer retry intent or auto-generate tokens. Omit by default on the first asynchronous submission. Use a user-provided value as-is when specified. A retry of the same uncertain submission must keep the original token policy: reuse its token if provided, or keep the field omitted if it was not provided. After a result exists, an explicitly requested new run must use a new token. Do not mint a different value for each retry attempt. Synchronous tasks do not support idempotency checks. The CLI runtime does not infer retry intent and does not auto-generate tokens. |
| `codec` | `--codec` | string | no | - | enum: ["h264","h265","prores","ffv1","exr"] | Professional output codec. When supplied, also provide a compatible bit_depth, except that gbrp16le selects FFV1 and 16 bits automatically. If only depth is supplied, eight bits selects H.264, 10 or 12 selects H.265 and 16 selects FFV1. ProRes, FFV1 and EXR ignore bitrate and bitrate_level. |
| `dynamic_range` | `--dynamic-range` | string | no | "sdr" | enum: ["sdr","hdr"] | Professional output range, default SDR. HDR uses AI tone mapping of SDR input to HLG and requires 10, 12 or 16 bits; gbrp16le supplies 16 automatically. HDR playback requires a compatible display and player. EXR forces HDR and ignores this setting. |
| `enhance_style` | `--enhance-style` | string | no | "hd" | enum: ["hd","natural"] | Enhancement style for standard or professional. Hd is the default and gives moderate sharpening; natural reduces sharpening artifacts for a softer, more natural appearance. |
| `fps` | `--fps` | number | no | - | min: 15; max: 120 | Specifies the target frame rate, in frames per second. This parameter is optional; when it is not specified, the output preserves the source frame rate. The value ranges from a minimum of 15 to a maximum of 120, and we recommend that it not exceed 4 times the source frame rate. |
| `pix_fmt` | `--pix-fmt` | string | no | - | enum: ["gbrp16le"] | Professional output pixel format. Gbrp16le forces lossless FFV1 in MOV with GBR pixels and 16-bit depth. Explicit codec and bit_depth must agree; bitrate settings have no effect. Input is limited to 40 seconds. Output files can be extremely large and are unsuitable for direct streaming distribution. Omission selects the format from codec and depth. |
| `queue_id` | `--queue-id` | string | no | - | - | Target queue ID for the submitted task. Omit to use the system default queue. Pass only when the user provides this value; do not generate, infer, or fill it. |
| `resolution` | `--resolution` | string | no | - | enum: ["240p","360p","480p","540p","720p","1080p","2k","4k","6k","8k"] | Target resolution from 240p through 8k, including 6k. Mutually exclusive with resolution_limit. |
| `resolution_limit` | `--resolution-limit` | integer | no | - | min: 128; max: 4320 | Sets the short-side pixel limit for the target resolution. The system scales the video proportionally to this limit while preserving the original aspect ratio. This parameter is optional, with values ranging from a minimum of 128 to a maximum of 4320. It cannot be configured together with resolution, as the two are mutually exclusive. |
| `scene` | `--scene` | string | no | "common" | enum: ["common","ugc","short_series","aigc","old_film"] | Selects a preset enhancement template for a specific business scenario. This parameter is optional and defaults to common. It supports common for general-purpose content, ugc for UGC short video, short_series for short-form drama series, aigc for AI-generated content, and old_film for old film restoration. This setting takes effect only when tool_version is standard. |
| `tool_version` | `--tool-version` | string | yes when ((bit_depth is provided) or (codec is provided) or (pix_fmt is provided) or (dynamic_range is provided)) | "standard" | enum: ["standard","professional"]; when ((bit_depth is provided) or (codec is provided) or (pix_fmt is provided) or (dynamic_range is provided)): const: "professional" | Optional. It determines the enhancement algorithms' strength and scope and the billing. It defaults to standard and can be set to standard or professional. The standard version balances processing speed and video quality, includes over 10 frequently used enhancement algorithms, meets mainstream playback platform quality requirements, and suits video distribution quality improvement. The professional version provides ultimate quality enhancement with over 30 built-in algorithms, ensures shot-level visual effects, and suits cinema-grade video production. |
| `video_url` | `--video-url` | string | yes | - | min length: 1 | The URL of the video to be enhanced. This parameter is required. It supports publicly accessible HTTP/HTTPS URLs. Supported input video formats include mp4, flv, ts, avi, mov, wmv, and mkv. Input video resolution supports up to 2K. It is recommended that a single input file not exceed 10 GB. CLI inputs may be local file paths or public HTTP/HTTPS URLs. The declared media input format also accepts vod:// and tos:// resource URIs. The CLI handles local file upload internally; pass the original input directly. |

### Parameter relations

- `$.bit_depth` is required when not (pix_fmt is provided) and codec is provided.
- `$.bit_depth` is required when not (pix_fmt is provided) and dynamic_range=hdr.
- `$.tool_version` is required when ((bit_depth is provided) or (codec is provided) or (pix_fmt is provided) or (dynamic_range is provided)).
- `$.tool_version` must satisfy const: "professional" when ((bit_depth is provided) or (codec is provided) or (pix_fmt is provided) or (dynamic_range is provided)).
- `$.bit_depth` must satisfy const: 16 when pix_fmt=gbrp16le.
- `$.codec` must satisfy const: "ffv1" when pix_fmt=gbrp16le.
- `$.bit_depth` must satisfy const: 8 when codec=h264.
- `$.bit_depth` must satisfy enum: [8,10,12] when codec=h265.
- `$.bit_depth` must satisfy const: 10 when codec=prores.
- `$.bit_depth` must satisfy const: 16 when codec is one of ["ffv1","exr"].
- `$.bit_depth` must satisfy enum: [10,12,16] when not (pix_fmt is provided) and dynamic_range=hdr.

### Example

```bash
byteplus-mediakit-cli video enhance-video \
  --video-url '<video_url>'
```

Replace placeholders with real user input only. Optional flags follow the parameter rules; do not invent URLs, files, enums, or business parameters.
Pass each scalar value as one shell argument. The quotes in these templates are illustrative: escape embedded quotes for the actual shell, or use an argument array when invoking a process. Never concatenate raw user input into shell code.

### Result

| Path | Type | Required | Mode | Description |
| --- | --- | --- | --- | --- |
| `request_id` | string | no | Cloud | Request id returned by the service; forwarded only when non-empty. Constraints: min length: 1. |
| `task_id` | string | yes | Cloud | Unique async task id used to query status and the terminal result. Constraints: min length: 1. |
| `task_type` | string | no | Cloud | Task type returned by the service; forwarded only when non-empty. Constraints: min length: 1. |
| `duration` | number | no | Cloud terminal | The total duration of the output video, in seconds. |
| `fps` | number | no | Cloud terminal | The frame rate of the output video. |
| `resolution` | string | no | Cloud terminal | Resolution of the enhanced video. |
| `tool_version` | string | no | Cloud terminal | The tool version actually used for the task. |
| `video_url` | string | no | Cloud terminal | The download URL of the enhanced video. Please be sure to save it in time, as this URL remains valid for 24 hours. |

After a successful Cloud call, read the returned `task_id` and check its current status. This command queries once and returns; when the task is still running, query the same task again later until terminal. Do not resubmit the business task. Use `--poll-complete` only when continuous waiting is intended; it waits without a limit by default. Pass a polling timeout or attempt limit only when specified by the user. Interrupting the CLI stops waiting without canceling the Cloud task:

```bash
byteplus-mediakit-cli shared query-task --task-id '<task_id>'
```

After a synchronous business tool succeeds or an asynchronous task reaches completed, return its actual result and any output asset URLs to the user. Use the returned URLs as inputs to requested dependent steps. Download assets or perform additional local processing only when the user requests it, and retain the Cloud result URL alongside any downloaded file. Do not present task submission as completion. Do not replace a completed Cloud output with a locally recreated file from the original input, or infer permission for local recreation from a requested duration or quality target. If a returned asset's metadata or name conflicts with the requested constraints, probe that asset with an available metadata tool before claiming a mismatch or performing a correction. Corrective processing must consume the completed Cloud output, preserving its lineage; present the original Cloud result URL alongside any corrected output and explain any confirmed limitation. Never omit an available completed result URL in favor of an unfinished correction or a local fallback.

### Machine contract

These commands read live help/schema only and do not call Cloud:

```bash
byteplus-mediakit-cli video enhance-video --help
byteplus-mediakit-cli video enhance-video --schema
```

# add-image-invisible-watermark

## Capability

Embed hidden text in an image for copyright tracing. Record the selected model version for later extraction.

## Parameter rules

- Invisible watermark settings. Optional parameters may be filled only when the user specifies them, or when they can be determined accurately from user intent; do not invent values. Omit them when uncertain; ask the user if they are required to complete the task. Before submitting a business task, resolve required fields and required alternatives from the user's explicit requirements, authoritative prior results, or an unambiguous documented mapping. A media type, genre, or broad processing goal does not by itself supply missing user choices or selection criteria. If a required choice has no documented default and remains unresolved, ask a focused clarification, preserve the user's known constraints, and stop before business submission. Do not invent a custom criterion merely to make a required field present. For optional parameters, infer only values implementing a behavior the user actually requested or required by a declared parameter relation. Do not add extra processing, alignment, formatting, or tuning merely because it seems helpful.
- Omit `output_format` unless the user names a documented format or explicitly requires a documented format property, such as transparency or preserving the source format. When transparency is requested, use a documented alpha-capable output format. A compatible documented default may be omitted. If the requested output format or default cannot meet that requirement, ask the user to choose among the documented compatible formats. For format preservation, use a documented original value when available; otherwise confirm the input format from reliable metadata and pass its supported value. Ask when the source format is unknown or unsupported. If the user names a documented format, pass it even when it equals the default. After a call succeeds or is intercepted, do not retry the same tool with an invented format.

## Cloud

### Command and lifecycle

- Command: `byteplus-mediakit-cli image add-image-invisible-watermark`
- Lifecycle: sync
- Return: returns the Cloud business result directly.

### Parameters

| Path | CLI flag | Type | Required | Default | Enum/range | Description |
| --- | --- | --- | --- | --- | --- | --- |
| `image_url` | `--image-url` | string | yes | - | min length: 1 | PNG, JPG, JPEG or WebP image to watermark. The API accepts public HTTP/HTTPS URLs. CLI inputs may be local file paths or public HTTP/HTTPS URLs. The CLI handles local file upload internally; pass the original input directly. |
| `output_format` | `--output-format` | string | no | "png" | enum: ["png","jpeg","webp"] | Output format, chosen from PNG, JPEG and WebP. Defaults to PNG. |
| `output_quality` | `--output-quality` | integer | no | 90 | min: 75; max: 100 | Output quality from 75 to 100, default 90. Lower values compress more and reduce image quality. For adapt, recommended minima are 75 for PNG, 78 for JPEG and 90 for WebP. For adapt_resize, the recommended minimum is 75. |
| `queue_id` | `--queue-id` | string | no | - | - | Target queue ID for the submitted task. Omit to use the system default queue. Pass only when the user provides this value; do not generate, infer, or fill it. |
| `strength_level` | `--strength-level` | string | no | "medium" | enum: ["low","medium","strong"] | Watermark strength. Low favors image quality, particularly on solid-color images; medium is the default balance; strong favors attack resistance on textured images. Higher strength can have a greater visible effect on the source image. |
| `tool_version` | `--tool-version` | string | yes | - | enum: ["adapt","adapt_resize"] | Watermark model. Adapt balances image quality and resistance to moderate scaling, cropping and compression for AIGC color images. Adapt_resize gives stronger resistance to scaling and compression for content-theft evidence collection. Record the version and use it again for extraction. |
| `watermark_content` | `--watermark-content` | string | yes | - | min length: 1; max length: 128 | Hidden text, up to 128 characters. For text containing only Chinese characters, keep it within the recommended 42 characters. |

### Example

```bash
byteplus-mediakit-cli image add-image-invisible-watermark \
  --image-url '<image_url>' \
  --tool-version '<tool_version>' \
  --watermark-content '<watermark_content>'
```

Replace placeholders with real user input only. Optional flags follow the parameter rules; do not invent URLs, files, enums, or business parameters.
Pass each scalar value as one shell argument. The quotes in these templates are illustrative: escape embedded quotes for the actual shell, or use an argument array when invoking a process. Never concatenate raw user input into shell code.

### Result

| Path | Type | Required | Mode | Description |
| --- | --- | --- | --- | --- |
| `image_format` | string | no | Cloud | Format of the output image. |
| `image_height` | integer | no | Cloud | Height of the output image in pixels. |
| `image_size` | integer | no | Cloud | Size of the output image in bytes. |
| `image_url` | string | no | Cloud | Processed image download link. Download and save it within the 24-hour validity period. |
| `image_width` | integer | no | Cloud | Width of the output image in pixels. |

After a synchronous business tool succeeds or an asynchronous task reaches completed, return its actual result and any output asset URLs to the user. Use the returned URLs as inputs to requested dependent steps. Download assets or perform additional local processing only when the user requests it, and retain the Cloud result URL alongside any downloaded file. Do not present task submission as completion. Do not replace a completed Cloud output with a locally recreated file from the original input, or infer permission for local recreation from a requested duration or quality target. If a returned asset's metadata or name conflicts with the requested constraints, probe that asset with an available metadata tool before claiming a mismatch or performing a correction. Corrective processing must consume the completed Cloud output, preserving its lineage; present the original Cloud result URL alongside any corrected output and explain any confirmed limitation. Never omit an available completed result URL in favor of an unfinished correction or a local fallback.

### Machine contract

These commands read live help/schema only and do not call Cloud:

```bash
byteplus-mediakit-cli image add-image-invisible-watermark --help
byteplus-mediakit-cli image add-image-invisible-watermark --schema
```

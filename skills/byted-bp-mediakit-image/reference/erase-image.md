# erase-image

## Capability

Remove text, icons or selected regions from an image and reconstruct the surrounding background.

## Parameter rules

- Optional parameters may be filled only when the user specifies them, or when they can be determined accurately from user intent; do not invent values. Omit them when uncertain; ask the user if they are required to complete the task. Before submitting a business task, resolve required fields and required alternatives from the user's explicit requirements, authoritative prior results, or an unambiguous documented mapping. A media type, genre, or broad processing goal does not by itself supply missing user choices or selection criteria. If a required choice has no documented default and remains unresolved, ask a focused clarification, preserve the user's known constraints, and stop before business submission. Do not invent a custom criterion merely to make a required field present. For optional parameters, infer only values implementing a behavior the user actually requested or required by a declared parameter relation. Do not add extra processing, alignment, formatting, or tuning merely because it seems helpful.
- Omit `output_format` unless the user names a documented format or explicitly requires a documented format property, such as transparency or preserving the source format. When transparency is requested, use a documented alpha-capable output format. A compatible documented default may be omitted. If the requested output format or default cannot meet that requirement, ask the user to choose among the documented compatible formats. For format preservation, use a documented original value when available; otherwise confirm the input format from reliable metadata and pass its supported value. Ask when the source format is unknown or unsupported. If the user names a documented format, pass it even when it equals the default. After a call succeeds or is intercepted, do not retry the same tool with an invented format.

## Cloud

### Command and lifecycle

- Command: `byteplus-mediakit-cli image erase-image`
- Lifecycle: sync
- Return: returns the Cloud business result directly.

### Usage guide

- Object parameters (`--selected-area`) must be valid JSON. Prefer a UTF-8 file with `--selected-area @params.json`, or pass correctly escaped inline JSON; field names and nesting must match the “Enum/range” column and nested field descriptions in the Parameters table below.
- Do not pass comma-separated values or plain text for these parameters.
- Inline JSON needs shell escaping. A single-quoted shell argument cannot contain an unescaped apostrophe, such as in It's ready; use a JSON file for text containing apostrophes.
- `{}` and `[{}]` illustrate JSON container shapes. Replace `<parameter_json>` in command templates with valid JSON matching the parameter table, including required fields. Empty objects or arrays are valid only when the Schema permits them, including a required object with no required child fields. Required means field presence; null, empty values, zero and false are checked against their own type and constraints. Child fields marked optional remain optional; do not infer required fields from illustrative prose. Do not submit placeholders.

### Example

```bash
byteplus-mediakit-cli image erase-image \
  --image-url '<image_url>'
```

Replace placeholders with real user input only. Optional flags follow the parameter rules; do not invent URLs, files, enums, or business parameters.
Pass each scalar value as one shell argument. The quotes in these templates are illustrative: escape embedded quotes for the actual shell, or use an argument array when invoking a process. Never concatenate raw user input into shell code.

### Parameters

| Path | CLI flag | Type | Required | Default | Enum/range | Description |
| --- | --- | --- | --- | --- | --- | --- |
| `image_url` | `--image-url` | string | yes | - | min length: 1; max file bytes: 10485760; allowed formats only: ["png","jpg","jpeg","webp","tiff","bmp","heic"]; min width (px): 10; min height (px): 10 | Image to erase and repair, at least 10 by 10 pixels, no greater than 2560 by 1440 pixels and at most 10 MB. Supports PNG, JPG, JPEG, WebP, TIFF, BMP and HEIC. The API accepts public HTTP/HTTPS URLs. CLI inputs may be local file paths or public HTTP/HTTPS URLs. The declared media input format also accepts tos:// resource URIs. The CLI handles local file upload internally; pass the original input directly. |
| `mask_url` | `--mask-url` | string | no | - | - | Black-and-white mask defining an arbitrary erase region in selected_area_erase mode. Its resolution must match image_url. A supplied rectangle is combined with the mask. The API requires a public HTTP/HTTPS URL. CLI inputs may be local file paths or public HTTP/HTTPS URLs. The declared media input format also accepts tos:// resource URIs. The CLI handles local file upload internally; pass the original input directly. |
| `output_format` | `--output-format` | string | no | "webp" | enum: ["png","jpeg","webp"] | Output format, chosen from WebP, PNG and JPEG. Defaults to WebP. |
| `queue_id` | `--queue-id` | string | no | - | - | Target queue ID for the submitted task. Omit to use the system default queue. Pass only when the user provides this value; do not generate, infer, or fill it. |
| `selected_area` | `--selected-area` | object | no | - | - | Rectangle to erase in selected_area_erase mode. Coordinates are normalized from 0 to 1, with the source image's top-left corner as the origin. When a mask is also supplied, both areas are combined. |
| `selected_area.bottom_right_x` | - | number | yes | - | min: 0; max: 1 | Right edge of the erase rectangle as a fraction of the source width. Must be greater than top_left_x. |
| `selected_area.bottom_right_y` | - | number | yes | - | min: 0; max: 1 | Bottom edge of the erase rectangle as a fraction of the source height, from 0 to 1. Must be greater than top_left_y. |
| `selected_area.top_left_x` | - | number | yes | - | min: 0; max: 1 | Left edge of the erase rectangle as a fraction of the source width. |
| `selected_area.top_left_y` | - | number | yes | - | min: 0; max: 1 | Top edge of the erase rectangle as a fraction of the source height. |
| `standard_erase_text` | `--standard-erase-text` | string | no | - | - | Text to remove in full_screen_text_erase mode. Omission removes all recognized text. |
| `standard_scene` | `--standard-scene` | string | no | "full_screen_text_erase" | enum: ["full_screen_text_erase","full_screen_icon_erase","selected_area_erase"] | Standard erasure scene, default full_screen_text_erase. Full-screen text removes the specified text, or all text when none is specified; full-screen icon removes icons. Selected-area mode requires a rectangle, a mask image or both, and erases their union. |
| `tool_version` | `--tool-version` | string | no | "standard" | enum: ["standard"] | Erasure model. Standard is currently the only supported version and is intended for simple, clearly defined removal tasks. |

### Parameter relations

- At least one of (($.selected_area is provided) or ($.mask_url is provided)) must hold when standard_scene=selected_area_erase and tool_version=standard.
- `$.selected_area.bottom_right_x` must be greater than `$.selected_area.top_left_x`.
- `$.selected_area.bottom_right_y` must be greater than `$.selected_area.top_left_y`.

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
byteplus-mediakit-cli image erase-image --help
byteplus-mediakit-cli image erase-image --schema
```

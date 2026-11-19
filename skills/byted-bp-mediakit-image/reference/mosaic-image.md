# mosaic-image

## Capability

Apply pixelation to a specified rectangular region of an image.

## Parameter rules

- Image processing settings. Optional parameters may be filled only when the user specifies them, or when they can be determined accurately from user intent; do not invent values. Omit them when uncertain; ask the user if they are required to complete the task. A conditionally required field still needs explicit presence even when the Schema lists a default. Use that documented default only when it matches the user's requested behavior; otherwise ask for the required target values before submitting. Before submitting a business task, resolve required fields and required alternatives from the user's explicit requirements, authoritative prior results, or an unambiguous documented mapping. A media type, genre, or broad processing goal does not by itself supply missing user choices or selection criteria. If a required choice has no documented default and remains unresolved, ask a focused clarification, preserve the user's known constraints, and stop before business submission. Do not invent a custom criterion merely to make a required field present. For optional parameters, infer only values implementing a behavior the user actually requested or required by a declared parameter relation. Do not add extra processing, alignment, formatting, or tuning merely because it seems helpful.
- Omit `output_format` unless the user names a documented format or explicitly requires a documented format property, such as transparency or preserving the source format. When transparency is requested, use a documented alpha-capable output format. A compatible documented default may be omitted. If the requested output format or default cannot meet that requirement, ask the user to choose among the documented compatible formats. For format preservation, use a documented original value when available; otherwise confirm the input format from reliable metadata and pass its supported value. Ask when the source format is unknown or unsupported. If the user names a documented format, pass it even when it equals the default. After a call succeeds or is intercepted, do not retry the same tool with an invented format.

## Cloud

### Command and lifecycle

- Command: `byteplus-mediakit-cli image mosaic-image`
- Lifecycle: sync
- Return: returns the Cloud business result directly.

### Usage guide

- Array parameters (`--mosaic-regions`) must be valid JSON. Prefer a UTF-8 file with `--mosaic-regions @params.json`, or pass correctly escaped inline JSON; field names and nesting must match the “Enum/range” column and nested field descriptions in the Parameters table below.
- Do not pass comma-separated values or plain text for these parameters.
- Inline JSON needs shell escaping. A single-quoted shell argument cannot contain an unescaped apostrophe, such as in It's ready; use a JSON file for text containing apostrophes.
- `{}` and `[{}]` illustrate JSON container shapes. Replace `<parameter_json>` in command templates with valid JSON matching the parameter table, including required fields. Empty objects or arrays are valid only when the Schema permits them, including a required object with no required child fields. Required means field presence; null, empty values, zero and false are checked against their own type and constraints. Child fields marked optional remain optional; do not infer required fields from illustrative prose. Do not submit placeholders.

### Example

```bash
byteplus-mediakit-cli image mosaic-image \
  --image-url '<image_url>'
```

Replace placeholders with real user input only. Optional flags follow the parameter rules; do not invent URLs, files, enums, or business parameters.
Pass each scalar value as one shell argument. The quotes in these templates are illustrative: escape embedded quotes for the actual shell, or use an argument array when invoking a process. Never concatenate raw user input into shell code.

### Parameters

| Path | CLI flag | Type | Required | Default | Enum/range | Description |
| --- | --- | --- | --- | --- | --- | --- |
| `image_url` | `--image-url` | string | yes | - | min length: 1; max file bytes: 36700160; allowed formats only: ["png","jpg","jpeg","webp"]; max width and height (px): 10000 | Static PNG, JPG, JPEG or WebP image to process. Width and height must each be no greater than 10,000 pixels; the maximum file size is 35 MB. The API accepts public HTTP/HTTPS URLs. CLI inputs may be local file paths or public HTTP/HTTPS URLs. The declared media input format also accepts tos:// resource URIs. The CLI handles local file upload internally; pass the original input directly. |
| `mosaic_regions` | `--mosaic-regions` | array<object> | yes when mosaic_type=specify-region | - | min items: 1; max items: 3 | Rectangles to pixelate in specify-region mode, required in that mode. Supply up to three regions. |
| `mosaic_regions[].bottom_right_x` | - | integer | yes | - | min: 0 | Nonnegative right-edge coordinate in pixels, measured from the source image's top-left corner. |
| `mosaic_regions[].bottom_right_y` | - | integer | yes | - | min: 0 | Nonnegative bottom-edge coordinate in pixels, measured from the source image's top-left corner. |
| `mosaic_regions[].top_left_x` | - | integer | yes | - | min: 0 | Nonnegative left-edge coordinate in pixels, measured from the source image's top-left corner. |
| `mosaic_regions[].top_left_y` | - | integer | yes | - | min: 0 | Nonnegative top-edge coordinate in pixels, measured from the source image's top-left corner. |
| `mosaic_shape` | `--mosaic-shape` | string | no | "circle" | enum: ["circle","rectangle"] | Mosaic cell shape, default circle. Circular or elliptical cells produce a softer appearance; rectangular cells produce a regular pattern. |
| `mosaic_step_x` | `--mosaic-step-x` | integer | no | 12 | min: 1 | Mosaic cell width in pixels, at least one. Defaults to 12; larger values produce coarser pixelation. |
| `mosaic_step_y` | `--mosaic-step-y` | integer | no | 12 | min: 1 | Mosaic cell height in pixels, at least one. Defaults to 12; larger values produce coarser pixelation. |
| `mosaic_type` | `--mosaic-type` | string | no | "full-image" | enum: ["full-image","specify-region"] | Mosaic scope. Full-image is the default and covers the whole image; specify-region covers the rectangles in mosaic_regions. |
| `output_format` | `--output-format` | string | no | "original" | enum: ["original","png","jpeg","webp"] | Output image format. Defaults to original, which retains the input format; PNG, JPEG and WebP are also available. |
| `queue_id` | `--queue-id` | string | no | - | - | Target queue ID for the submitted task. Omit to use the system default queue. Pass only when the user provides this value; do not generate, infer, or fill it. |

### Parameter relations

- `$.mosaic_regions` is required when mosaic_type=specify-region.
- `$.mosaic_regions[].bottom_right_x` must be greater than `$.mosaic_regions[].top_left_x`.
- `$.mosaic_regions[].bottom_right_y` must be greater than `$.mosaic_regions[].top_left_y`.

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
byteplus-mediakit-cli image mosaic-image --help
byteplus-mediakit-cli image mosaic-image --schema
```

# crop-image

## Capability

Crop an image by direction, origin, custom coordinates or an inscribed circle.

## Parameter rules

- Image cropping settings. Optional parameters may be filled only when the user specifies them, or when they can be determined accurately from user intent; do not invent values. Omit them when uncertain; ask the user if they are required to complete the task. Before submitting a business task, resolve required fields and required alternatives from the user's explicit requirements, authoritative prior results, or an unambiguous documented mapping. A media type, genre, or broad processing goal does not by itself supply missing user choices or selection criteria. If a required choice has no documented default and remains unresolved, ask a focused clarification, preserve the user's known constraints, and stop before business submission. Do not invent a custom criterion merely to make a required field present. For optional parameters, infer only values implementing a behavior the user actually requested or required by a declared parameter relation. Do not add extra processing, alignment, formatting, or tuning merely because it seems helpful.
- Omit `output_format` unless the user names a documented format or explicitly requires a documented format property, such as transparency or preserving the source format. When transparency is requested, use a documented alpha-capable output format. A compatible documented default may be omitted. If the requested output format or default cannot meet that requirement, ask the user to choose among the documented compatible formats. For format preservation, use a documented original value when available; otherwise confirm the input format from reliable metadata and pass its supported value. Ask when the source format is unknown or unsupported. If the user names a documented format, pass it even when it equals the default. After a call succeeds or is intercepted, do not retry the same tool with an invented format.

## Cloud

### Command and lifecycle

- Command: `byteplus-mediakit-cli image crop-image`
- Lifecycle: sync
- Return: returns the Cloud business result directly.

### Parameters

| Path | CLI flag | Type | Required | Default | Enum/range | Description |
| --- | --- | --- | --- | --- | --- | --- |
| `crop_height` | `--crop-height` | integer | yes when (crop_mode is omitted or crop_mode=directional) or crop_mode=origin | - | min: 0 | The crop height in pixels. Required in origin mode. In directional mode, the API table requires both dimensions and disallows both being zero; a zero height is calculated from the width. The linked guide also shows an omitted dimension. Confirm this difference before relying on omission. |
| `crop_mode` | `--crop-mode` | string | no | "directional" | enum: ["directional","origin","custom","circle"] | Crop mode, default directional. Directional uses dimensions and position; origin uses dimensions, offsets and an anchor; custom uses corner coordinates; circle extracts the maximum inscribed circle without additional crop settings. |
| `crop_position` | `--crop-position` | string | no | "center" | enum: ["center","up","down","left","right"] | Directional crop position, chosen from center, up, down, left and right. Defaults to center. |
| `crop_width` | `--crop-width` | integer | yes when (crop_mode is omitted or crop_mode=directional) or crop_mode=origin | - | min: 0 | The crop width in pixels. Required in origin mode. In directional mode, the API table requires both dimensions and disallows both being zero; a zero width is calculated from the height. The linked guide also shows an omitted dimension. Confirm this difference before relying on omission. |
| `custom_x1` | `--custom-x1` | integer | yes when crop_mode=custom | - | - | Horizontal coordinate of the custom crop's top-left corner, in pixels. Required in custom mode. |
| `custom_x2` | `--custom-x2` | integer | yes when crop_mode=custom | - | - | Horizontal coordinate of the custom crop's bottom-right corner, in pixels. Required in custom mode. |
| `custom_y1` | `--custom-y1` | integer | yes when crop_mode=custom | - | - | Vertical coordinate of the custom crop's top-left corner, in pixels. Required in custom mode. |
| `custom_y2` | `--custom-y2` | integer | yes when crop_mode=custom | - | - | Vertical coordinate of the custom crop's bottom-right corner, in pixels. Required in custom mode. |
| `image_url` | `--image-url` | string | yes | - | min length: 1; max file bytes: 36700160; allowed formats only: ["png","jpg","jpeg","webp"]; max short edge (px): 2048 when crop_mode=circle | Static PNG, JPG, JPEG or WebP image to crop. Keep the file within 35 MB. Circle mode requires the source image's shorter side to be no greater than 2048 pixels. The API accepts public HTTP/HTTPS URLs. CLI inputs may be local file paths or public HTTP/HTTPS URLs. The declared media input format also accepts tos:// resource URIs. The CLI handles local file upload internally; pass the original input directly. |
| `origin_gravity` | `--origin-gravity` | string | no | "northwest" | enum: ["northwest","north","northeast","west","center","east","southwest","south","southeast"] | Crop anchor in the nine-position layout, default northwest at the top-left corner. |
| `origin_x` | `--origin-x` | integer | yes when crop_mode=origin | - | - | Horizontal offset from the crop anchor in pixels, required in origin mode. Positive values move right and negative values move left. |
| `origin_y` | `--origin-y` | integer | yes when crop_mode=origin | - | - | Vertical offset from the crop anchor in pixels, required in origin mode. Positive values move down and negative values move up. |
| `output_format` | `--output-format` | string | no | "original" | enum: ["original","png","jpeg","webp"] | Output format, default original; PNG, JPEG and WebP are available. Circle mode should use PNG or WebP for transparency; JPEG fills the area outside the circle with white. |
| `queue_id` | `--queue-id` | string | no | - | - | Target queue ID for the submitted task. Omit to use the system default queue. Pass only when the user provides this value; do not generate, infer, or fill it. |

### Parameter relations

- Exactly one of (crop_width is provided and crop_height is provided and (crop_mode is omitted or (crop_mode=directional))) or (crop_width is provided and crop_height is provided and origin_x is provided and origin_y is provided and crop_mode=origin) or (custom_x1 is provided and custom_y1 is provided and custom_x2 is provided and custom_y2 is provided and crop_mode=custom) or (crop_mode=circle) must hold.
- Must not satisfy ($.crop_height=0 and $.crop_width=0) when ((crop_mode=directional) or (crop_mode is omitted)).
- `$.custom_x2` must be greater than `$.custom_x1`.
- `$.custom_y2` must be greater than `$.custom_y1`.

### Example

```bash
byteplus-mediakit-cli image crop-image \
  --image-url '<image_url>' \
  --crop-width '<crop_width>' \
  --crop-height '<crop_height>'
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
byteplus-mediakit-cli image crop-image --help
byteplus-mediakit-cli image crop-image --schema
```

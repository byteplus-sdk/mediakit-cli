# add-image-watermark

## Capability

Add a visible text or image watermark to an image, with controls for placement, appearance and tiling.

## Parameter rules

- Image processing settings. Optional parameters may be filled only when the user specifies them, or when they can be determined accurately from user intent; do not invent values. Omit them when uncertain; ask the user if they are required to complete the task. A conditionally required field still needs explicit presence even when the Schema lists a default. Use that documented default only when it matches the user's requested behavior; otherwise ask for the required target values before submitting. Before submitting a business task, resolve required fields and required alternatives from the user's explicit requirements, authoritative prior results, or an unambiguous documented mapping. A media type, genre, or broad processing goal does not by itself supply missing user choices or selection criteria. If a required choice has no documented default and remains unresolved, ask a focused clarification, preserve the user's known constraints, and stop before business submission. Do not invent a custom criterion merely to make a required field present. For optional parameters, infer only values implementing a behavior the user actually requested or required by a declared parameter relation. Do not add extra processing, alignment, formatting, or tuning merely because it seems helpful.
- Omit `output_format` unless the user names a documented format or explicitly requires a documented format property, such as transparency or preserving the source format. When transparency is requested, use a documented alpha-capable output format. A compatible documented default may be omitted. If the requested output format or default cannot meet that requirement, ask the user to choose among the documented compatible formats. For format preservation, use a documented original value when available; otherwise confirm the input format from reliable metadata and pass its supported value. Ask when the source format is unknown or unsupported. If the user names a documented format, pass it even when it equals the default. After a call succeeds or is intercepted, do not retry the same tool with an invented format.

## Cloud

### Command and lifecycle

- Command: `byteplus-mediakit-cli image add-image-watermark`
- Lifecycle: sync
- Return: returns the Cloud business result directly.

### Usage guide

- Boolean parameters (`--enable-tile`) must be written as `--flag=true` or `--flag=false`, or as bare `--flag` (equivalent to true). Do not pass a space-separated value such as `--flag true`, which the CLI treats as a positional argument.
- Pass a boolean explicitly when the user specifies true or false, including values equal to the default. Omit it when the user has not specified a value and no relation requires it.
- Requesting the main operation does not request every optional behavior. Do not enable an optional switch or fill its dependent defaults merely because input metadata differs or a changed output would be more convenient. Keep those fields omitted unless the user requested that separate behavior or a declared parameter relation requires their explicit presence.

### Example

```bash
byteplus-mediakit-cli image add-image-watermark \
  --image-url '<image_url>' \
  --watermark-text '<watermark_text>'
```

Replace placeholders with real user input only. Optional flags follow the parameter rules; do not invent URLs, files, enums, or business parameters.
Pass each scalar value as one shell argument. The quotes in these templates are illustrative: escape embedded quotes for the actual shell, or use an argument array when invoking a process. Never concatenate raw user input into shell code.

### Parameters

| Path | CLI flag | Type | Required | Default | Enum/range | Description |
| --- | --- | --- | --- | --- | --- | --- |
| `enable_tile` | `--enable-tile` | boolean | no | false | - | Repeat the watermark across the image at fixed spacing. Defaults to false. Tiled text also rotates 30 degrees counterclockwise; tiled image watermarks are not rotated. |
| `image_url` | `--image-url` | string | yes | - | min length: 1; max file bytes: 36700160; allowed formats only: ["png","jpg","jpeg","webp"]; max width and height (px): 10000 | Static PNG, JPG, JPEG or WebP image to process. Width and height must each be no greater than 10,000 pixels; the maximum file size is 35 MB. The API accepts public HTTP/HTTPS URLs. CLI inputs may be local file paths or public HTTP/HTTPS URLs. The declared media input format also accepts tos:// resource URIs. The CLI handles local file upload internally; pass the original input directly. |
| `output_format` | `--output-format` | string | no | "original" | enum: ["original","png","jpeg","webp"] | Output image format. Defaults to original, which retains the input format; PNG, JPEG and WebP are also available. |
| `queue_id` | `--queue-id` | string | no | - | - | Target queue ID for the submitted task. Omit to use the system default queue. Pass only when the user provides this value; do not generate, infer, or fill it. |
| `watermark_image_opacity` | `--watermark-image-opacity` | integer | no | 100 | min: 0; max: 100 | Image watermark opacity from 0 to 100; smaller values are more transparent. Defaults to 100. |
| `watermark_image_url` | `--watermark-image-url` | string | yes when watermark_type=image | - | max file bytes: 5242880; allowed formats only: ["jpg","jpeg","webp"] | Watermark image, required for an image watermark. Supports only JPG, JPEG and WebP; keep the file within 5 MB. The API requires a public HTTP/HTTPS URL. CLI inputs may be local file paths or public HTTP/HTTPS URLs. The CLI handles local file upload internally; pass the original input directly. |
| `watermark_position` | `--watermark-position` | string | no | "bottom_right" | enum: ["top_left","top_right","bottom_left","bottom_right","left_center","right_center","top_center","bottom_center","center"] | Position in the nine-grid layout, default bottom_right. Horizontal and vertical offsets have no effect for positions whose name contains center. |
| `watermark_position_offset_x` | `--watermark-position-offset-x` | integer | no | 0 | min: 0 | Nonnegative horizontal offset in pixels from the chosen position, default zero. Has no effect when the position name contains center. |
| `watermark_position_offset_y` | `--watermark-position-offset-y` | integer | no | 0 | min: 0 | Nonnegative vertical offset in pixels from the chosen position, default zero. Has no effect when the position name contains center. |
| `watermark_text` | `--watermark-text` | string | yes when watermark_type=text; yes when watermark_type is omitted | - | max length: 64; when watermark_type=text: min length: 1; when watermark_type is omitted: min length: 1 | Watermark text, required for a text watermark and limited to 64 characters. |
| `watermark_text_color` | `--watermark-text-color` | string | no | "#FFFFFF" | - | Text color, specified as hexadecimal, RGB or another supported color form. Defaults to white, |
| `watermark_text_font` | `--watermark-text-font` | string | no | "SourceHanSans-Regular.ttf" | enum: ["SourceHanSans-Regular.ttf","SourceHanSans-Bold.ttf","SourceHanSans-ExtraLight.ttf","SourceHanSans-Heavy.ttf","SourceHanSans-Light.ttf","SourceHanSans-Medium.ttf","SourceHanSans-Normal.ttf","SourceHanSerifCN-Regular.ttf","SourceHanSerifCN-Bold.ttf","SourceHanSerifCN-ExtraLight.ttf","SourceHanSerifCN-Heavy.ttf","SourceHanSerifCN-Light.ttf","SourceHanSerifCN-SemiBold.ttf","zcool-heiti.ttf","zcool_gaoduanhei.ttf","zcool_kuaileti.ttf","zcool_huangyou.ttf","FZLTHK.TTF"] | Font file for a text watermark. Defaults to SourceHanSans-Regular.ttf. Choose a supported file from the documented font catalog, including Source Han Sans, Source Han Serif, ZCOOL and FZLanTingHei families. |
| `watermark_text_font_size` | `--watermark-text-font-size` | integer | no | 30 | min: 1; max: 200 | Text size in pixels, from 1 to 200. Defaults to 30. |
| `watermark_text_opacity` | `--watermark-text-opacity` | integer | no | 30 | min: 0; max: 100 | Text opacity from 0 to 100; smaller values are more transparent. Defaults to 30. |
| `watermark_type` | `--watermark-type` | string | no | "text" | enum: ["text","image"] | Watermark type, text or image. Defaults to text. |

### Parameter relations

- `$.watermark_image_url` is required when watermark_type=image.
- `$.watermark_text` is required when watermark_type=text.
- `$.watermark_text` is required when watermark_type is omitted.
- `$.watermark_text` must satisfy min length: 1 when watermark_type=text.
- `$.watermark_text` must satisfy min length: 1 when watermark_type is omitted.

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
byteplus-mediakit-cli image add-image-watermark --help
byteplus-mediakit-cli image add-image-watermark --schema
```

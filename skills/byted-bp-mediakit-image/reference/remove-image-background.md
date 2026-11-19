# remove-image-background

## Capability

Retain the subject of an image while removing its background, using a general, human or product matting scene.

## Parameter rules

- Image background removal settings. Optional parameters may be filled only when the user specifies them, or when they can be determined accurately from user intent; do not invent values. Omit them when uncertain; ask the user if they are required to complete the task. Before submitting a business task, resolve required fields and required alternatives from the user's explicit requirements, authoritative prior results, or an unambiguous documented mapping. A media type, genre, or broad processing goal does not by itself supply missing user choices or selection criteria. If a required choice has no documented default and remains unresolved, ask a focused clarification, preserve the user's known constraints, and stop before business submission. Do not invent a custom criterion merely to make a required field present. For optional parameters, infer only values implementing a behavior the user actually requested or required by a declared parameter relation. Do not add extra processing, alignment, formatting, or tuning merely because it seems helpful.
- Omit `output_format` unless the user names a documented format or explicitly requires a documented format property, such as transparency or preserving the source format. When transparency is requested, use a documented alpha-capable output format. A compatible documented default may be omitted. If the requested output format or default cannot meet that requirement, ask the user to choose among the documented compatible formats. For format preservation, use a documented original value when available; otherwise confirm the input format from reliable metadata and pass its supported value. Ask when the source format is unknown or unsupported. If the user names a documented format, pass it even when it equals the default. After a call succeeds or is intercepted, do not retry the same tool with an invented format.

## Cloud

### Command and lifecycle

- Command: `byteplus-mediakit-cli image remove-image-background`
- Lifecycle: sync
- Return: returns the Cloud business result directly.

### Usage guide

- Boolean parameters (`--need-contour`, `--need-crop-background`) must be written as `--flag=true` or `--flag=false`, or as bare `--flag` (equivalent to true). Do not pass a space-separated value such as `--flag true`, which the CLI treats as a positional argument.
- Pass a boolean explicitly when the user specifies true or false, including values equal to the default. Omit it when the user has not specified a value and no relation requires it.
- Requesting the main operation does not request every optional behavior. Do not enable an optional switch or fill its dependent defaults merely because input metadata differs or a changed output would be more convenient. Keep those fields omitted unless the user requested that separate behavior or a declared parameter relation requires their explicit presence.

### Example

```bash
byteplus-mediakit-cli image remove-image-background \
  --image-url '<image_url>' \
  --scene '<scene>'
```

Replace placeholders with real user input only. Optional flags follow the parameter rules; do not invent URLs, files, enums, or business parameters.
Pass each scalar value as one shell argument. The quotes in these templates are illustrative: escape embedded quotes for the actual shell, or use an argument array when invoking a process. Never concatenate raw user input into shell code.

### Parameters

| Path | CLI flag | Type | Required | Default | Enum/range | Description |
| --- | --- | --- | --- | --- | --- | --- |
| `contour_color` | `--contour-color` | string | no | "#FFFFFF" | pattern: "^#[0-9a-fA-F]{6}$" | Outline color in hexadecimal RGB, default white, |
| `contour_size` | `--contour-size` | integer | no | 10 | min: 1; max: 100 | Outline width from 1 to 100 pixels, default 10. Applies when need_contour is true in human or product mode. |
| `image_url` | `--image-url` | string | yes | - | min length: 1; max file bytes: 31457280; allowed formats only: ["png","jpg","jpeg","webp","tiff","bmp","heic"]; max pixels: 40000000 when scene=general; max short edge (px): 6000 when scene=general; max long edge (px): 10240 when scene=general; max short edge (px): 4320 when scene=human; max long edge (px): 7680 when scene=human; max short edge (px): 4320 when scene=product; max long edge (px): 7680 when scene=product | PNG, JPG, JPEG, WebP, TIFF, BMP or HEIC image, at most 30 MB. General mode allows a long side up to 10,240 pixels, short side up to 6000 and area up to 40 megapixels. Human and product modes allow a long side up to 7680 and short side up to 4320. The API accepts public HTTP/HTTPS URLs. CLI inputs may be local file paths or public HTTP/HTTPS URLs. The declared media input format also accepts tos:// resource URIs. The CLI handles local file upload internally; pass the original input directly. |
| `need_contour` | `--need-contour` | boolean | no | false | - | Draw a subject outline in human or product mode. Defaults to false and is ignored in general mode. |
| `need_crop_background` | `--need-crop-background` | boolean | no | false | - | Crop the transparent background tightly around the subject in human or product mode. Defaults to false and is ignored in general mode. |
| `output_format` | `--output-format` | string | no | "png" | enum: ["png","jpeg","webp"] | Output format, default PNG. PNG and WebP retain transparency; JPEG fills transparent areas with black. |
| `queue_id` | `--queue-id` | string | no | - | - | Target queue ID for the submitted task. Omit to use the system default queue. Pass only when the user provides this value; do not generate, infer, or fill it. |
| `scene` | `--scene` | string | yes | - | enum: ["general","human","product"] | Subject selection. General handles subjects whose category is uncertain; human keeps people; product keeps products. |

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
byteplus-mediakit-cli image remove-image-background --help
byteplus-mediakit-cli image remove-image-background --schema
```

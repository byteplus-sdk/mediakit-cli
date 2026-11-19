# resize-image

## Capability

Resize an image by dimensions, edge length or scale, with a choice of aspect ratio handling.

## Parameter rules

- Image processing settings. Optional parameters may be filled only when the user specifies them, or when they can be determined accurately from user intent; do not invent values. Omit them when uncertain; ask the user if they are required to complete the task. Before submitting a business task, resolve required fields and required alternatives from the user's explicit requirements, authoritative prior results, or an unambiguous documented mapping. A media type, genre, or broad processing goal does not by itself supply missing user choices or selection criteria. If a required choice has no documented default and remains unresolved, ask a focused clarification, preserve the user's known constraints, and stop before business submission. Do not invent a custom criterion merely to make a required field present. For optional parameters, infer only values implementing a behavior the user actually requested or required by a declared parameter relation. Do not add extra processing, alignment, formatting, or tuning merely because it seems helpful.
- Omit `output_format` unless the user names a documented format or explicitly requires a documented format property, such as transparency or preserving the source format. When transparency is requested, use a documented alpha-capable output format. A compatible documented default may be omitted. If the requested output format or default cannot meet that requirement, ask the user to choose among the documented compatible formats. For format preservation, use a documented original value when available; otherwise confirm the input format from reliable metadata and pass its supported value. Ask when the source format is unknown or unsupported. If the user names a documented format, pass it even when it equals the default. After a call succeeds or is intercepted, do not retry the same tool with an invented format.

## Cloud

### Command and lifecycle

- Command: `byteplus-mediakit-cli image resize-image`
- Lifecycle: sync
- Return: returns the Cloud business result directly.

### Usage guide

- Array parameters (`--resize-adaptive`) must use comma-separated values wrapped in quotes, for example `--resize-adaptive "enlarge,shrink"`.
- A comma inside one array item must be quoted by CSV rules, including doubled embedded double quotes, or use a JSON array file with `--resize-adaptive @params.json`. A single string URL may contain commas; this CSV rule applies only to array encoding.
- For one operation on an ordered collection, include all items in one `--resize-adaptive` array argument in the user's order. Submit separate tasks only for independent operations requested by the user or a documented input limit.

### Example

```bash
byteplus-mediakit-cli image resize-image \
  --image-url '<image_url>' \
  --resize-long '<resize_long>'
```

Replace placeholders with real user input only. Optional flags follow the parameter rules; do not invent URLs, files, enums, or business parameters.
Pass each scalar value as one shell argument. The quotes in these templates are illustrative: escape embedded quotes for the actual shell, or use an argument array when invoking a process. Never concatenate raw user input into shell code.

### Parameters

| Path | CLI flag | Type | Required | Default | Enum/range | Description |
| --- | --- | --- | --- | --- | --- | --- |
| `image_url` | `--image-url` | string | yes | - | min length: 1; max file bytes: 36700160; allowed formats only: ["png","jpg","jpeg","webp"]; max width and height (px): 10000 | Static PNG, JPG, JPEG or WebP image to process. Width and height must each be no greater than 10,000 pixels; the maximum file size is 35 MB. The API accepts public HTTP/HTTPS URLs. CLI inputs may be local file paths or public HTTP/HTTPS URLs. The declared media input format also accepts tos:// resource URIs. The CLI handles local file upload internally; pass the original input directly. |
| `output_format` | `--output-format` | string | no | "original" | enum: ["original","png","jpeg","webp"] | Output image format. Defaults to original, which retains the input format; PNG, JPEG and WebP are also available. |
| `queue_id` | `--queue-id` | string | no | - | - | Target queue ID for the submitted task. Omit to use the system default queue. Pass only when the user provides this value; do not generate, infer, or fill it. |
| `resize_adaptive` | `--resize-adaptive` | array<string> | no | ["enlarge","shrink"] | min items: 1; unique items: true; element enum: ["enlarge","shrink"] | Conditions under which to resize. Enlarge permits enlarging smaller inputs; shrink permits shrinking larger inputs. Omission enables both conditions. One resize condition, enlarge or shrink. |
| `resize_long` | `--resize-long` | integer | required in an alternative: resize_long or resize_short | 0 | min: 0 | Target long-edge length in pixels. Supply a positive value for at least one target edge. When the short edge is omitted or zero, resize proportionally; when both are positive, use resize_mode. |
| `resize_mode` | `--resize-mode` | string | no | "contain" | enum: ["exact","contain","cover"] | Scaling mode when both target edges are supplied. Contain is the default and fits the image inside the target rectangle while retaining its aspect ratio. Cover retains the aspect ratio and center-crops to fill the rectangle. Exact forces both dimensions and can distort the image. |
| `resize_short` | `--resize-short` | integer | required in an alternative: resize_long or resize_short | 0 | min: 0 | Target short-edge length in pixels. Supply a positive value for at least one target edge. When the long edge is omitted or zero, resize proportionally; when both are positive, use resize_mode. |

### Parameter relations

- At least one of (resize_long is provided and resize_long satisfies (min: 1)) or (resize_short is provided and resize_short satisfies (min: 1)) must hold.

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
byteplus-mediakit-cli image resize-image --help
byteplus-mediakit-cli image resize-image --schema
```

# probe-image-metadata

## Capability

Inspect an image's metadata, embedded AIGC information or transparency information.

## Parameter rules

- Image metadata query settings. Optional parameters may be filled only when the user specifies them, or when they can be determined accurately from user intent; do not invent values. Omit them when uncertain; ask the user if they are required to complete the task. Before submitting a business task, resolve required fields and required alternatives from the user's explicit requirements, authoritative prior results, or an unambiguous documented mapping. A media type, genre, or broad processing goal does not by itself supply missing user choices or selection criteria. If a required choice has no documented default and remains unresolved, ask a focused clarification, preserve the user's known constraints, and stop before business submission. Do not invent a custom criterion merely to make a required field present. For optional parameters, infer only values implementing a behavior the user actually requested or required by a declared parameter relation. Do not add extra processing, alignment, formatting, or tuning merely because it seems helpful.

## Cloud

### Command and lifecycle

- Command: `byteplus-mediakit-cli image probe-image-metadata`
- Lifecycle: sync
- Return: returns the Cloud business result directly.

### Parameters

| Path | CLI flag | Type | Required | Default | Enum/range | Description |
| --- | --- | --- | --- | --- | --- | --- |
| `image_url` | `--image-url` | string | yes | - | min length: 1; max file bytes: 36700160; allowed formats only: ["png","jpg","jpeg","webp"]; max width and height (px): 10000 | Image to inspect, in a common format such as PNG, JPG, JPEG or WebP. Each dimension must be at most 10,000 pixels; keep the file within 35 MB. Animated-image handling depends on the query type. The API accepts public HTTP/HTTPS URLs. CLI inputs may be local file paths or public HTTP/HTTPS URLs. The declared media input format also accepts tos:// resource URIs. The CLI handles local file upload internally; pass the original input directly. |
| `info_type` | `--info-type` | string | no | "metadata" | enum: ["metadata","avghue","alpha","blurhash"] | Query type, default metadata. Metadata returns basic image information; avghue returns the dominant color; alpha analyzes transparency; blurhash returns a BlurHash string. Check the documented limits for the selected query type and animation format. |
| `queue_id` | `--queue-id` | string | no | - | - | Target queue ID for the submitted task. Omit to use the system default queue. Pass only when the user provides this value; do not generate, infer, or fill it. |

### Example

```bash
byteplus-mediakit-cli image probe-image-metadata \
  --image-url '<image_url>'
```

Replace placeholders with real user input only. Optional flags follow the parameter rules; do not invent URLs, files, enums, or business parameters.
Pass each scalar value as one shell argument. The quotes in these templates are illustrative: escape embedded quotes for the actual shell, or use an argument array when invoking a process. Never concatenate raw user input into shell code.

### Result

| Path | Type | Required | Mode | Description |
| --- | --- | --- | --- | --- |
| `aigc` | object / null | no | Cloud | Embedded AIGC metadata, or null when absent. |
| `alpha_ratio` | number | no | Cloud | Fraction of pixels that are fully or partly transparent. |
| `blurhash` | string | no | Cloud | BlurHash encoding of the image. |
| `color` | string | no | Cloud | Dominant image color in hexadecimal form, such as |
| `color_model` | string | no | Cloud | Image color model, such as grayscale. |
| `duration` | number | no | Cloud | Animated-image duration in seconds. May be absent for a static image. |
| `exif` | object / null | no | Cloud | EXIF metadata, or null when absent. |
| `format` | string | no | Cloud | The image format, such as jpeg. |
| `frame_count` | number | no | Cloud | Number of image frames; usually one for a static image. |
| `has_alpha` | boolean | no | Cloud | Whether the image has an alpha channel. |
| `height` | number | no | Cloud | The image height in pixels. |
| `is_animation` | boolean | no | Cloud | Whether the image is animated. |
| `md5` | string | no | Cloud | Image file MD5 checksum. |
| `orientation` | object / array / string / number / boolean / null | no | Cloud | Orientation metadata for the image. |
| `quality` | number | no | Cloud | Image compression quality parameter. |
| `size` | number | no | Cloud | Image file size in bytes. |
| `width` | number | no | Cloud | The image width in pixels. |

After a synchronous business tool succeeds or an asynchronous task reaches completed, return its actual result and any output asset URLs to the user. Use the returned URLs as inputs to requested dependent steps. Download assets or perform additional local processing only when the user requests it, and retain the Cloud result URL alongside any downloaded file. Do not present task submission as completion. Do not replace a completed Cloud output with a locally recreated file from the original input, or infer permission for local recreation from a requested duration or quality target. If a returned asset's metadata or name conflicts with the requested constraints, probe that asset with an available metadata tool before claiming a mismatch or performing a correction. Corrective processing must consume the completed Cloud output, preserving its lineage; present the original Cloud result URL alongside any corrected output and explain any confirmed limitation. Never omit an available completed result URL in favor of an unfinished correction or a local fallback.

### Machine contract

These commands read live help/schema only and do not call Cloud:

```bash
byteplus-mediakit-cli image probe-image-metadata --help
byteplus-mediakit-cli image probe-image-metadata --schema
```

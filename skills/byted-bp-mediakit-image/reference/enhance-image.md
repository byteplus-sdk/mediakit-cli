# enhance-image

## Capability

Improve image clarity and detail, with optional upscaling and a choice of enhancement model versions.

## Parameter rules

- Optional parameters may be filled only when the user specifies them, or when they can be determined accurately from user intent; do not invent values. Omit them when uncertain; ask the user if they are required to complete the task. Before submitting a business task, resolve required fields and required alternatives from the user's explicit requirements, authoritative prior results, or an unambiguous documented mapping. A media type, genre, or broad processing goal does not by itself supply missing user choices or selection criteria. If a required choice has no documented default and remains unresolved, ask a focused clarification, preserve the user's known constraints, and stop before business submission. Do not invent a custom criterion merely to make a required field present. For optional parameters, infer only values implementing a behavior the user actually requested or required by a declared parameter relation. Do not add extra processing, alignment, formatting, or tuning merely because it seems helpful.

## Cloud

### Command and lifecycle

- Command: `byteplus-mediakit-cli image enhance-image`
- Lifecycle: sync
- Return: returns the Cloud business result directly.

### Usage guide

- Boolean parameters (`--enable-correct-color`) must be written as `--flag=true` or `--flag=false`, or as bare `--flag` (equivalent to true). Do not pass a space-separated value such as `--flag true`, which the CLI treats as a positional argument.
- Pass a boolean explicitly when the user specifies true or false, including values equal to the default. Omit it when the user has not specified a value and no relation requires it.
- Requesting the main operation does not request every optional behavior. Do not enable an optional switch or fill its dependent defaults merely because input metadata differs or a changed output would be more convenient. Keep those fields omitted unless the user requested that separate behavior or a declared parameter relation requires their explicit presence.

### Example

```bash
byteplus-mediakit-cli image enhance-image \
  --image-url '<image_url>'
```

Replace placeholders with real user input only. Optional flags follow the parameter rules; do not invent URLs, files, enums, or business parameters.
Pass each scalar value as one shell argument. The quotes in these templates are illustrative: escape embedded quotes for the actual shell, or use an argument array when invoking a process. Never concatenate raw user input into shell code.

### Parameters

| Path | CLI flag | Type | Required | Default | Enum/range | Description |
| --- | --- | --- | --- | --- | --- | --- |
| `enable_correct_color` | `--enable-correct-color` | boolean | no | true | - | Apply color correction in max mode, default true. It tries to retain the source colors. Disable it if correction produces excessive sharpening. |
| `generative_enhance_mode` | `--generative-enhance-mode` | string | no | "generative_first" | enum: ["generative_first","fidelity_first"] | Enhancement strategy for professional or max. Fidelity_first prioritizes existing details for faithful portrait, product or text restoration. Generative_first is the default and recreates richer texture for blurry or compressed inputs; choose it when some detail reconstruction is acceptable. |
| `image_url` | `--image-url` | string | yes | - | min length: 1; max file bytes: 10485760; allowed formats only: ["png","jpg","jpeg","webp"]; min short edge (px): 16 when tool_version=standard; max short edge (px): 1440 when tool_version=standard; min long edge (px): 16 when tool_version=standard; max long edge (px): 2160 when tool_version=standard; min short edge (px): 256 when tool_version=professional; max long edge (px): 2048 when tool_version=professional | PNG, JPG, JPEG or WebP image to enhance, at most 10 MB. Input and output dimension limits depend on tool_version. Max also limits the long-to-short-side ratio to 32. The API accepts public HTTP/HTTPS URLs; check the selected model's limits in the enhancement guide. CLI inputs may be local file paths or public HTTP/HTTPS URLs. The declared media input format also accepts tos:// resource URIs. The CLI handles local file upload internally; pass the original input directly. |
| `multiple` | `--multiple` | number | no | - | min: 1; max: 30 | Upscaling factor with at most two decimal places. Standard supports 1 to 8; professional and max support 1 to 30. Both output dimensions must stay within the selected model's maximum resolution. |
| `queue_id` | `--queue-id` | string | no | - | - | Target queue ID for the submitted task. Omit to use the system default queue. Pass only when the user provides this value; do not generate, infer, or fill it. |
| `target_height` | `--target-height` | integer | no | - | min: 64; max: 10240 | Target height in pixels. Standard ranges from the source height to 6144, with at most 8-fold enlargement. Professional and max support 64 to 10,240. It can be combined with target_width or used alone to retain the source aspect ratio. |
| `target_width` | `--target-width` | integer | no | - | min: 64; max: 10240 | Target width in pixels. Standard ranges from the source width to 6144, with at most 8-fold enlargement. Professional and max support 64 to 10,240. For max, keep the requested dimensions within the recommended 8000 by 8000 pixels to reduce timeout risk. |
| `tool_version` | `--tool-version` | string | no | "standard" | enum: ["standard","professional","max"] | Enhancement model, default standard. Standard balances speed, output-size growth and quality. Professional restores natural texture with attention to fidelity. Max uses a generative model to restore texture while preserving structure and meaning. Versions have different limits, effects and prices; consult their comparison before choosing. |

### Parameter relations

- `$.multiple` must satisfy max: 8 when tool_version=standard.
- `$.target_height` must satisfy max: 6144 when tool_version=standard.
- `$.target_width` must satisfy max: 6144 when tool_version=standard.
- `$.multiple` must satisfy max: 30 when tool_version=professional.
- `$.target_height` must satisfy max: 10240 when tool_version=professional.
- `$.target_width` must satisfy max: 10240 when tool_version=professional.

### Result

| Path | Type | Required | Mode | Description |
| --- | --- | --- | --- | --- |
| `image_format` | string | no | Cloud | Standard always returns PNG. Professional and max retain the input image format. |
| `image_height` | integer | no | Cloud | Height of the output image in pixels. |
| `image_size` | integer | no | Cloud | Size of the output image in bytes. |
| `image_url` | string | no | Cloud | Processed image download link. Download and save it within the 24-hour validity period. |
| `image_width` | integer | no | Cloud | Width of the output image in pixels. |

After a synchronous business tool succeeds or an asynchronous task reaches completed, return its actual result and any output asset URLs to the user. Use the returned URLs as inputs to requested dependent steps. Download assets or perform additional local processing only when the user requests it, and retain the Cloud result URL alongside any downloaded file. Do not present task submission as completion. Do not replace a completed Cloud output with a locally recreated file from the original input, or infer permission for local recreation from a requested duration or quality target. If a returned asset's metadata or name conflicts with the requested constraints, probe that asset with an available metadata tool before claiming a mismatch or performing a correction. Corrective processing must consume the completed Cloud output, preserving its lineage; present the original Cloud result URL alongside any corrected output and explain any confirmed limitation. Never omit an available completed result URL in favor of an unfinished correction or a local fallback.

### Machine contract

These commands read live help/schema only and do not call Cloud:

```bash
byteplus-mediakit-cli image enhance-image --help
byteplus-mediakit-cli image enhance-image --schema
```

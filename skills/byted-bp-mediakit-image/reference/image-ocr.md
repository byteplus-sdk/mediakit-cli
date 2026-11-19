# image-ocr

## Capability

Recognize text in an image, with standard and advanced recognition modes and optional keyword extraction.

## Parameter rules

- Optional parameters may be filled only when the user specifies them, or when they can be determined accurately from user intent; do not invent values. Omit them when uncertain; ask the user if they are required to complete the task. A conditionally required field still needs explicit presence even when the Schema lists a default. Use that documented default only when it matches the user's requested behavior; otherwise ask for the required target values before submitting. Before submitting a business task, resolve required fields and required alternatives from the user's explicit requirements, authoritative prior results, or an unambiguous documented mapping. A media type, genre, or broad processing goal does not by itself supply missing user choices or selection criteria. If a required choice has no documented default and remains unresolved, ask a focused clarification, preserve the user's known constraints, and stop before business submission. Do not invent a custom criterion merely to make a required field present. For optional parameters, infer only values implementing a behavior the user actually requested or required by a declared parameter relation. Do not add extra processing, alignment, formatting, or tuning merely because it seems helpful.

## Cloud

### Command and lifecycle

- Command: `byteplus-mediakit-cli image image-ocr`
- Lifecycle: sync
- Return: returns the Cloud business result directly.

### Usage guide

- Array parameters (`--max-keywords`) must use comma-separated values wrapped in quotes, for example `--max-keywords "value1,value2"`.
- A comma inside one array item must be quoted by CSV rules, including doubled embedded double quotes, or use a JSON array file with `--max-keywords @params.json`. A single string URL may contain commas; this CSV rule applies only to array encoding.
- For one operation on an ordered collection, include all items in one `--max-keywords` array argument in the user's order. Submit separate tasks only for independent operations requested by the user or a documented input limit.

### Example

```bash
byteplus-mediakit-cli image image-ocr \
  --image-url '<image_url>'
```

Replace placeholders with real user input only. Optional flags follow the parameter rules; do not invent URLs, files, enums, or business parameters.
Pass each scalar value as one shell argument. The quotes in these templates are illustrative: escape embedded quotes for the actual shell, or use an argument array when invoking a process. Never concatenate raw user input into shell code.

### Parameters

| Path | CLI flag | Type | Required | Default | Enum/range | Description |
| --- | --- | --- | --- | --- | --- | --- |
| `image_url` | `--image-url` | string | yes | - | min length: 1; max file bytes: 10485760; allowed formats only: ["png","jpg","jpeg","webp","tiff","bmp","heic"]; max short edge (px): 2160; max long edge (px): 3840 | Image to recognize, with a short side up to 2160 pixels, a long side up to 3840 pixels and file size at most 10 MB. Supports PNG, JPG, JPEG, WebP, TIFF, BMP and HEIC. Avoid rotation above 30 degrees and split very dense pages to reduce recognition errors and timeouts. The API accepts public HTTP/HTTPS URLs. CLI inputs may be local file paths or public HTTP/HTTPS URLs. The declared media input format also accepts tos:// resource URIs. The CLI handles local file upload internally; pass the original input directly. |
| `max_keywords` | `--max-keywords` | array<string> | yes when task_type=keyword and tool_version=max | - | min items: 1 | Keywords to find, required in max keyword mode. Supply at least one keyword. |
| `queue_id` | `--queue-id` | string | no | - | - | Target queue ID for the submitted task. Omit to use the system default queue. Pass only when the user provides this value; do not generate, infer, or fill it. |
| `task_type` | `--task-type` | string | no | "spotting" | enum: ["spotting","keyword"] | OCR task in max mode, default spotting. Spotting returns recognized text blocks and coordinates in ocr_result; keyword returns matches for max_keywords in keyword_result. |
| `tool_version` | `--tool-version` | string | no | "standard" | enum: ["standard","max"] | OCR model, default standard. Standard recognizes printed Simplified Chinese and English. Max recognizes printed and handwritten text in more than 100 languages, supports keyword matching and performs best on Chinese and English, followed by the 13 listed common languages. |

### Parameter relations

- `$.max_keywords` is required when task_type=keyword and tool_version=max.

### Result

| Path | Type | Required | Mode | Description |
| --- | --- | --- | --- | --- |
| `keyword_result` | object | no | Cloud | Matches in max keyword mode, mapping each supplied keyword to its recognized matching text. |
| `ocr_result` | array<object> | no | Cloud | Recognized text blocks, returned in standard mode or max spotting mode. |
| `ocr_result[].bottom_right_x` | number | no | Cloud | Right edge of the text box in pixels. |
| `ocr_result[].bottom_right_y` | number | no | Cloud | Bottom edge of the text box in pixels. |
| `ocr_result[].confidence` | number | no | Cloud | Recognition confidence from 0 to 1, returned by standard mode. Higher values indicate greater confidence. Constraints: min: 0; max: 1. |
| `ocr_result[].content` | string | no | Cloud | Recognized text in the block. |
| `ocr_result[].top_left_x` | number | no | Cloud | Left edge of the text box in pixels. |
| `ocr_result[].top_left_y` | number | no | Cloud | Top edge of the text box in pixels. |

After a synchronous business tool succeeds or an asynchronous task reaches completed, return its actual result and any output asset URLs to the user. Use the returned URLs as inputs to requested dependent steps. Download assets or perform additional local processing only when the user requests it, and retain the Cloud result URL alongside any downloaded file. Do not present task submission as completion. Do not replace a completed Cloud output with a locally recreated file from the original input, or infer permission for local recreation from a requested duration or quality target. If a returned asset's metadata or name conflicts with the requested constraints, probe that asset with an available metadata tool before claiming a mismatch or performing a correction. Corrective processing must consume the completed Cloud output, preserving its lineage; present the original Cloud result URL alongside any corrected output and explain any confirmed limitation. Never omit an available completed result URL in favor of an unfinished correction or a local fallback.

### Machine contract

These commands read live help/schema only and do not call Cloud:

```bash
byteplus-mediakit-cli image image-ocr --help
byteplus-mediakit-cli image image-ocr --schema
```

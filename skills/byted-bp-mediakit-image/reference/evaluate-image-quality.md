# evaluate-image-quality

## Capability

Assess image quality across selected standard dimensions or with a professional model that returns a fixed set of scores.

## Parameter rules

- Image quality assessment settings. Optional parameters may be filled only when the user specifies them, or when they can be determined accurately from user intent; do not invent values. Omit them when uncertain; ask the user if they are required to complete the task. Before submitting a business task, resolve required fields and required alternatives from the user's explicit requirements, authoritative prior results, or an unambiguous documented mapping. A media type, genre, or broad processing goal does not by itself supply missing user choices or selection criteria. If a required choice has no documented default and remains unresolved, ask a focused clarification, preserve the user's known constraints, and stop before business submission. Do not invent a custom criterion merely to make a required field present. For optional parameters, infer only values implementing a behavior the user actually requested or required by a declared parameter relation. Do not add extra processing, alignment, formatting, or tuning merely because it seems helpful.

## Cloud

### Command and lifecycle

- Command: `byteplus-mediakit-cli image evaluate-image-quality`
- Lifecycle: sync
- Return: returns the Cloud business result directly.

### Usage guide

- Array parameters (`--standard-evaluate-items`) must use comma-separated values wrapped in quotes, for example `--standard-evaluate-items "vqscore,advcolor"`.
- A comma inside one array item must be quoted by CSV rules, including doubled embedded double quotes, or use a JSON array file with `--standard-evaluate-items @params.json`. A single string URL may contain commas; this CSV rule applies only to array encoding.
- For one operation on an ordered collection, include all items in one `--standard-evaluate-items` array argument in the user's order. Submit separate tasks only for independent operations requested by the user or a documented input limit.

### Example

```bash
byteplus-mediakit-cli image evaluate-image-quality \
  --image-url '<image_url>'
```

Replace placeholders with real user input only. Optional flags follow the parameter rules; do not invent URLs, files, enums, or business parameters.
Pass each scalar value as one shell argument. The quotes in these templates are illustrative: escape embedded quotes for the actual shell, or use an argument array when invoking a process. Never concatenate raw user input into shell code.

### Parameters

| Path | CLI flag | Type | Required | Default | Enum/range | Description |
| --- | --- | --- | --- | --- | --- | --- |
| `image_url` | `--image-url` | string | yes | - | min length: 1; max file bytes: 10485760; allowed formats only: ["png","jpeg","webp","heic"]; max short edge (px): 4320; max long edge (px): 7680 | PNG, JPEG, WebP or HEIC image to assess, at most 10 MB. The short side must be at most 4320 pixels and the long side at most 7680. The API accepts public HTTP/HTTPS URLs. CLI inputs may be local file paths or public HTTP/HTTPS URLs. The declared media input format also accepts tos:// resource URIs. The CLI handles local file upload internally; pass the original input directly. |
| `queue_id` | `--queue-id` | string | no | - | - | Target queue ID for the submitted task. Omit to use the system default queue. Pass only when the user provides this value; do not generate, infer, or fill it. |
| `standard_evaluate_items` | `--standard-evaluate-items` | array<string> | no | ["vqscore","noise","aesthetic","blur"] | element enum: ["vqscore","advcolor","blockiness","noise","aesthetic","blur","cg","contrast","texture","brightness","overexposure","hue","saturation","green","cmartifacts"] | Dimensions to assess in standard mode. An empty selection returns vqscore, noise, aesthetic and blur. Other dimensions cover color, blockiness, scene type, contrast, texture, brightness, exposure, hue, saturation, green regions and compression artifacts. One supported standard assessment dimension. |
| `tool_version` | `--tool-version` | string | no | "standard" | enum: ["standard","professional"] | Assessment version, default standard. Standard offers 15 selectable dimensions with lower cost and flexible coverage. Professional uses a large model to return fixed comprehensive scores and does not accept custom dimensions. |

### Parameter relations

- At least one of (($.tool_version is omitted) or ($.tool_version=standard)) must hold when standard_evaluate_items is provided.

### Result

| Path | Type | Required | Mode | Description |
| --- | --- | --- | --- | --- |
| `advcolor` | number | no | Cloud | Overall color quality from 0 to 100; higher means better. The documented bands are poor up to 50, medium from 50 to 60 and good from 60 upward. |
| `aesthetic` | number | no | Cloud | Popular-aesthetic appeal from 0 to 100; higher means greater appeal. |
| `aesthetics` | number | no | Cloud | Professional aesthetics score from 0 to 100, with two decimal places. Higher means more coordinated composition, color and style. |
| `artifacts` | number | no | Cloud | Professional artifact score from 0 to 100, with two decimal places. Higher means fewer compression, generation or distortion artifacts and a more natural image. |
| `blockiness` | number | no | Cloud | Standard blockiness score. The API describes larger values as stronger blockiness while also labeling 0 to 50 poor, 50 to 60 medium and 60 to 100 good. A value of -1 denotes a non-conventional image such as a game or effects image; the contradictory direction labels require review before interpretation. |
| `blur` | number | no | Cloud | The sharpness score, from 0 to 100. A higher score means a clearer, sharper image. Supports two decimal places. |
| `brightness` | number | no | Cloud | Average brightness from 0 to 255; higher means brighter. |
| `cg` | number | no | Cloud | Scene-type score from 0 to 100. Values near zero indicate natural scenes; values near 100 indicate game, screen-recording or other non-natural scenes. |
| `cmartifacts` | number | no | Cloud | Compression artifacts from 0 to 100; higher means more artifacts and lower quality. Below 30 indicates none or slight artifacts, 30 to below 60 indicates visible artifacts, and 60 upward indicates obvious artifacts. |
| `contrast` | number | no | Cloud | Contrast from 0 to 100; lower means less contrast. |
| `green` | number | no | Cloud | Green-region measure from 0 to 255; higher means more green area and a greater likelihood of a green screen. |
| `hue` | number | no | Cloud | Hue balance from 0 to 100; higher means more balanced hue. |
| `noise` | number | no | Cloud | The noise score, from 0 to 100. A higher score means a cleaner image. Supports two decimal places. |
| `overall` | number | no | Cloud | Professional combined quality score from 0 to 100, with two decimal places. Higher means better quality. |
| `overexposure` | number | no | Cloud | Overexposure measure from 0 to 100; higher means overexposure is more likely. |
| `saturation` | number | no | Cloud | Saturation balance from 0 to 100; higher means more balanced saturation. |
| `texture` | number | no | Cloud | Texture richness from 0 to 255; higher means richer texture. |
| `tool_version` | string | no | Cloud | The assessment version used, standard or professional. |
| `vqscore` | number | no | Cloud | Subjective image quality from 0 to 100; higher means better quality. |

After a synchronous business tool succeeds or an asynchronous task reaches completed, return its actual result and any output asset URLs to the user. Use the returned URLs as inputs to requested dependent steps. Download assets or perform additional local processing only when the user requests it, and retain the Cloud result URL alongside any downloaded file. Do not present task submission as completion. Do not replace a completed Cloud output with a locally recreated file from the original input, or infer permission for local recreation from a requested duration or quality target. If a returned asset's metadata or name conflicts with the requested constraints, probe that asset with an available metadata tool before claiming a mismatch or performing a correction. Corrective processing must consume the completed Cloud output, preserving its lineage; present the original Cloud result URL alongside any corrected output and explain any confirmed limitation. Never omit an available completed result URL in favor of an unfinished correction or a local fallback.

### Machine contract

These commands read live help/schema only and do not call Cloud:

```bash
byteplus-mediakit-cli image evaluate-image-quality --help
byteplus-mediakit-cli image evaluate-image-quality --schema
```

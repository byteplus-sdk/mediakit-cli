---
name: byted-bp-mediakit-image
version: "0.0.2"
license: "SEE LICENSE IN LICENSE"
description: "Use this skill to inspect image metadata or transparency, recognize text, assess quality, resize or compress an image, edit its geometry or appearance, remove its background or selected content, mask faces, or add and extract hidden watermarks."
permissions:
  - shell
metadata:
  requires:
    bins: ["byteplus-mediakit-cli"]
  cliHelp: "byteplus-mediakit-cli image --help"
  product: "byteplus-mediakit-cli/skills"
  domain: image
  capability_count: 22
---
# image MediaKit Skill

1. Read `../byted-bp-mediakit-shared/SKILL.md` first and run the shared checks. Stop if that Skill is missing.

Read the shared setup and the operation's capability reference. Image operations are synchronous: consume their returned result without starting a task polling loop. When dimensions, file size, animation or transparency affect eligibility, explicitly inspect the image with probe-image-metadata and use the result to choose a supported operation.

Check each tool's own format, size and dimension limits. Many editing tools accept static images only. A limit for one image tool must not be applied to another. Choose resize-image for dimensions and aspect ratio, compress-image for an explicit compression target, and slim-image for intelligent compression; JPEG re-encoding can leave an already compressed file unchanged or slightly larger.

Enhancement models differ in fidelity, texture reconstruction, limits and price. Choose fidelity_first when faithful source details matter and generative_first when some detail reconstruction is acceptable. Professional and max settings must follow their model-specific limits. Image OCR's max keyword mode needs at least one keyword; quality assessment's professional mode returns fixed scores and cannot select standard dimensions.

Erasure regions use normalized coordinates and require bottom-right coordinates beyond the corresponding top-left coordinates. In selected-area erasure, a rectangle and mask combine as a union; the mask must match the source dimensions. For background removal or rounded corners, choose a format that preserves the requested transparency and explain the documented JPEG fill behavior.

Invisible-watermark extraction must use the add operation's model version. Record that version when adding a watermark. Higher watermark strength can affect source appearance. Save all returned image files within their documented validity period.

## Tools

| Tool | Description | Mode | Command | Reference |
| --- | --- | --- | --- | --- |
| add-image-invisible-watermark | Embed hidden text in an image for copyright tracing. Record the selected model version for later extraction. | Cloud | `byteplus-mediakit-cli image add-image-invisible-watermark` | [reference/add-image-invisible-watermark.md](reference/add-image-invisible-watermark.md) |
| add-image-watermark | Add a visible text or image watermark to an image, with controls for placement, appearance and tiling. | Cloud | `byteplus-mediakit-cli image add-image-watermark` | [reference/add-image-watermark.md](reference/add-image-watermark.md) |
| adjust-image-color | Adjust an image's brightness, contrast or saturation using a preset effect. | Cloud | `byteplus-mediakit-cli image adjust-image-color` | [reference/adjust-image-color.md](reference/adjust-image-color.md) |
| compress-image | Reduce image file size by setting a quality level or size limit, with optional output format conversion. | Cloud | `byteplus-mediakit-cli image compress-image` | [reference/compress-image.md](reference/compress-image.md) |
| crop-image | Crop an image by direction, origin, custom coordinates or an inscribed circle. | Cloud | `byteplus-mediakit-cli image crop-image` | [reference/crop-image.md](reference/crop-image.md) |
| enhance-image | Improve image clarity and detail, with optional upscaling and a choice of enhancement model versions. | Cloud | `byteplus-mediakit-cli image enhance-image` | [reference/enhance-image.md](reference/enhance-image.md) |
| erase-image | Remove text, icons or selected regions from an image and reconstruct the surrounding background. | Cloud | `byteplus-mediakit-cli image erase-image` | [reference/erase-image.md](reference/erase-image.md) |
| evaluate-image-quality | Assess image quality across selected standard dimensions or with a professional model that returns a fixed set of scores. | Cloud | `byteplus-mediakit-cli image evaluate-image-quality` | [reference/evaluate-image-quality.md](reference/evaluate-image-quality.md) |
| extract-image-invisible-watermark | Recover text embedded by AI MediaKit image invisible watermarking, using the same model version as the add operation. | Cloud | `byteplus-mediakit-cli image extract-image-invisible-watermark` | [reference/extract-image-invisible-watermark.md](reference/extract-image-invisible-watermark.md) |
| face-blur-image | Detect and mask faces in an image using mosaic or Gaussian blur, with controls for detection and masking strength. | Cloud | `byteplus-mediakit-cli image face-blur-image` | [reference/face-blur-image.md](reference/face-blur-image.md) |
| flip-image | Flip an image horizontally or vertically. | Cloud | `byteplus-mediakit-cli image flip-image` | [reference/flip-image.md](reference/flip-image.md) |
| gaussian-blur-image | Apply Gaussian blur to an image with a configurable blur strength. | Cloud | `byteplus-mediakit-cli image gaussian-blur-image` | [reference/gaussian-blur-image.md](reference/gaussian-blur-image.md) |
| image-ocr | Recognize text in an image, with standard and advanced recognition modes and optional keyword extraction. | Cloud | `byteplus-mediakit-cli image image-ocr` | [reference/image-ocr.md](reference/image-ocr.md) |
| invert-image | Invert an image's colors to create a negative effect. | Cloud | `byteplus-mediakit-cli image invert-image` | [reference/invert-image.md](reference/invert-image.md) |
| mosaic-image | Apply pixelation to a specified rectangular region of an image. | Cloud | `byteplus-mediakit-cli image mosaic-image` | [reference/mosaic-image.md](reference/mosaic-image.md) |
| probe-image-metadata | Inspect an image's metadata, embedded AIGC information or transparency information. | Cloud | `byteplus-mediakit-cli image probe-image-metadata` | [reference/probe-image-metadata.md](reference/probe-image-metadata.md) |
| remove-image-background | Retain the subject of an image while removing its background, using a general, human or product matting scene. | Cloud | `byteplus-mediakit-cli image remove-image-background` | [reference/remove-image-background.md](reference/remove-image-background.md) |
| resize-image | Resize an image by dimensions, edge length or scale, with a choice of aspect ratio handling. | Cloud | `byteplus-mediakit-cli image resize-image` | [reference/resize-image.md](reference/resize-image.md) |
| rotate-image | Rotate an image by a specified angle and set the fill color for uncovered areas. | Cloud | `byteplus-mediakit-cli image rotate-image` | [reference/rotate-image.md](reference/rotate-image.md) |
| round-corner-image | Add circular or elliptical rounded corners to an image. | Cloud | `byteplus-mediakit-cli image round-corner-image` | [reference/round-corner-image.md](reference/round-corner-image.md) |
| sharpen-image | Strengthen image edges and fine details with a configurable sharpening strength. | Cloud | `byteplus-mediakit-cli image sharpen-image` | [reference/sharpen-image.md](reference/sharpen-image.md) |
| slim-image | Use intelligent image compression to reduce file size while improving texture detail and reducing visual artifacts. | Cloud | `byteplus-mediakit-cli image slim-image` | [reference/slim-image.md](reference/slim-image.md) |

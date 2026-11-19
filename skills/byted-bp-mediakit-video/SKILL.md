---
name: byted-bp-mediakit-video
version: "0.0.2"
license: "SEE LICENSE IN LICENSE"
description: "Use this skill to inspect video metadata or quality, enhance or restore video, repair stutter, extract frames, mask faces, remove subtitles, create depth or matting video, segment scenes and semantic units, reconstruct a script, generate highlights, or understand video content."
permissions:
  - shell
metadata:
  requires:
    bins: ["byteplus-mediakit-cli"]
  cliHelp: "byteplus-mediakit-cli video --help"
  product: "byteplus-mediakit-cli/skills"
  domain: video
  capability_count: 24
---
# video MediaKit Skill

1. Read `../byted-bp-mediakit-shared/SKILL.md` first and run the shared checks. Stop if that Skill is missing.

Read the shared setup and the selected capability reference. Use probe-video-metadata before decisions that depend on duration, resolution, frame rate, dynamic range or audio presence. Use the declared input limits of the selected tool rather than general video limits.

Enhancement settings must follow the version, codec, color-depth, pixel-format and HDR combinations. Sixteen-bit and gbrp16le tasks require video no longer than 40 seconds. Their large outputs and serial processing can affect processing time and storage; gbrp16le is unsuitable for direct streaming. HDR needs a compatible display and player. Generative enhancement accepts SDR and has its own input dimensions and recommended frame-rate ratio.

Smoothness detection can run without repair. A returned repaired-video URL denotes detection-and-repair billing; an absent URL denotes detection-only billing. Detection counts do not prove that the corresponding repairs were applied. V5.6 subtitle erasure takes longer than v5 and may still leave incomplete erasure on animated text or complex backgrounds. Per-segment and global erasure boxes are mutually exclusive.

Script reconstruction requires readable hard subtitles, compatible live-action dialogue and plot, identical input resolutions and the documented file and duration limits. Professional semantic segmentation requires a strategy. Request optional shots alongside scenes or sentences when their shot IDs need to be resolved, and expect clip links only when clip generation was requested and produced them.

An English video-understanding prompt mentioning sound does not automatically enable audio analysis; set manual_option.need_audio=true when audio analysis is required. Enable the appropriate ModelArk service and cross-service authorization before using model-backed understanding or the shared model proxy. Pass model-specific arguments according to that service's own official contract.

Depth output is a relative-depth HEVC video. Video invisible-watermark content is a positive 64-bit integer represented as a digit string with no leading zeros. Save output files within their documented validity period, keep task_id and use the shared query protocol for asynchronous completion.

## Tools

| Tool | Description | Mode | Command | Reference |
| --- | --- | --- | --- | --- |
| add-video-invisible-watermark | Embed a hidden numeric watermark in a video for copyright tracing and later authenticity checks. | Cloud | `byteplus-mediakit-cli video add-video-invisible-watermark` | [reference/add-video-invisible-watermark.md](reference/add-video-invisible-watermark.md) |
| assess-video-quality | Assess the perceptual quality of a video and return a VQScore from 0 to 100. | Cloud | `byteplus-mediakit-cli video assess-video-quality` | [reference/assess-video-quality.md](reference/assess-video-quality.md) |
| drama-script | Reconstruct a script from videos with readable hard subtitles, returning dialogue, visual descriptions and related analysis assets. | Cloud | `byteplus-mediakit-cli video drama-script` | [reference/drama-script.md](reference/drama-script.md) |
| enhance-video | Enhances video quality using AI algorithms for denoising, color enhancement, artifact removal, and super-resolution. | Cloud | `byteplus-mediakit-cli video enhance-video` | [reference/enhance-video.md](reference/enhance-video.md) |
| enhance-video-fast | Uses a speed-first algorithm combining lightweight super-resolution and intelligent quality enhancement to balance efficiency and visual quality. | Cloud | `byteplus-mediakit-cli video enhance-video-fast` | [reference/enhance-video-fast.md](reference/enhance-video-fast.md) |
| enhance-video-generative | Improve low-quality video using AI enhancement, with optional upscaling and frame interpolation. This tool accepts SDR input. | Cloud | `byteplus-mediakit-cli video enhance-video-generative` | [reference/enhance-video-generative.md](reference/enhance-video-generative.md) |
| enhance-video-smoothness | Detect and optionally repair periodic stutter and duplicate frames in a video. Detection can also run without producing a repaired video. | Cloud | `byteplus-mediakit-cli video enhance-video-smoothness` | [reference/enhance-video-smoothness.md](reference/enhance-video-smoothness.md) |
| erase-video-subtitle-pro | This capability provides high-quality, seamless subtitle removal and restores the original picture to the greatest extent possible. Different queues are the primary way to allocate costs to specific projects. | Cloud | `byteplus-mediakit-cli video erase-video-subtitle-pro` | [reference/erase-video-subtitle-pro.md](reference/erase-video-subtitle-pro.md) |
| extract-frames | This capability can capture one or more still images from a video. The captured images can be used for video covers, preview thumbnails, sprite sheets, or as input for other video understanding tasks. | Cloud | `byteplus-mediakit-cli video extract-frames` | [reference/extract-frames.md](reference/extract-frames.md) |
| extract-video-invisible-watermark | Recover numeric watermarks embedded by AI MediaKit video invisible watermarking for tracing and authenticity checks. | Cloud | `byteplus-mediakit-cli video extract-video-invisible-watermark` | [reference/extract-video-invisible-watermark.md](reference/extract-video-invisible-watermark.md) |
| face-blur-video | It is suitable for protecting facial privacy, including short-drama content anonymization and preprocessing reference material for generative video. | Cloud | `byteplus-mediakit-cli video face-blur-video` | [reference/face-blur-video.md](reference/face-blur-video.md) |
| martencode-video | Low-bitrate HD transcoding analyzes video scenes, motion, content, and textures and selects optimal encoding parameters. The task outputs video with better perceived quality at a relatively lower bitrate, reducing bandwidth costs while improving the viewing experience. | Cloud | `byteplus-mediakit-cli video martencode-video` | [reference/martencode-video.md](reference/martencode-video.md) |
| matte-greenscreen-video | Separate a video's subject from a green-screen or solid-color background for compositing or a replacement background. | Cloud | `byteplus-mediakit-cli video matte-greenscreen-video` | [reference/matte-greenscreen-video.md](reference/matte-greenscreen-video.md) |
| matte-portrait-video | Separate people from a video's background for compositing or a replacement background. | Cloud | `byteplus-mediakit-cli video matte-portrait-video` | [reference/matte-portrait-video.md](reference/matte-portrait-video.md) |
| probe-video-metadata | Inspect a video and return its container, duration, resolution, frame rate and stream information. | Cloud | `byteplus-mediakit-cli video probe-video-metadata` | [reference/probe-video-metadata.md](reference/probe-video-metadata.md) |
| remux-video | Remuxing can quickly convert between mainstream container formats such as MP4, FLV, and MPEG-TS with no quality loss and at very low cost. It changes only the container format and does not re-encode or re-decode the audio/video bitstreams. This is used for VOD distribution adaptation, streaming segmentation and packaging, and multi-device compatibility. | Cloud | `byteplus-mediakit-cli video remux-video` | [reference/remux-video.md](reference/remux-video.md) |
| segment-scenes | Automatically splits a video into multiple scene segments based on transitions and changes in visual content, and outputs each segment's timeline information and independent video file. | Cloud | `byteplus-mediakit-cli video segment-scenes` | [reference/segment-scenes.md](reference/segment-scenes.md) |
| semantic-segment | Analyze a video's visual, audio and narrative structure and return semantically complete segment timelines. | Cloud | `byteplus-mediakit-cli video semantic-segment` | [reference/semantic-segment.md](reference/semantic-segment.md) |
| semantic-segment-pro | Split a video into semantically complete segments using content-specific strategies, with optional shot, scene, sentence and segment-video results. | Cloud | `byteplus-mediakit-cli video semantic-segment-pro` | [reference/semantic-segment-pro.md](reference/semantic-segment-pro.md) |
| transcode-video | Video transcoding converts one video bitstream into another, typically changing the encoding format, encoding parameters, and container format. | Cloud | `byteplus-mediakit-cli video transcode-video` | [reference/transcode-video.md](reference/transcode-video.md) |
| video-depth | Generate a continuous relative-depth video that retains motion and camera movement for downstream motion-reference and visual-effects workflows. | Cloud | `byteplus-mediakit-cli video video-depth` | [reference/video-depth.md](reference/video-depth.md) |
| video-frame-interpolation | Uses deep learning to intelligently synthesize new frames and increase the video's frame rate to a specified target. This is particularly effective for enhancing the viewing experience of low-frame-rate, fast-motion, or large-screen videos. | Cloud | `byteplus-mediakit-cli video video-frame-interpolation` | [reference/video-frame-interpolation.md](reference/video-frame-interpolation.md) |
| video-highlights-llm | Automatically generates highlight reels from video content. | Cloud | `byteplus-mediakit-cli video video-highlights-llm` | [reference/video-highlights-llm.md](reference/video-highlights-llm.md) |
| video-understand-router | Analyze video frames and optionally audio to produce structured video understanding results, using automatic or manual analysis settings. | Cloud | `byteplus-mediakit-cli video video-understand-router` | [reference/video-understand-router.md](reference/video-understand-router.md) |

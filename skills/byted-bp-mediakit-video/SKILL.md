---
name: byted-bp-mediakit-video
version: "0.0.1"
license: "Apache-2.0"
description: "Use this skill to improve video or prep it for distribution: restore noisy, grainy, dark, washed-out, low-res, or old phone footage (denoise, fix color and artifacts, upscale/super-res); re-encode at a lower bitrate while keeping it sharp for streaming, change codec, or re-wrap into another container (e.g. MP4) with no re-encoding or quality loss; grab still frames for covers or thumbnails; split clips automatically at scene changes; auto-generate highlight reels; raise frame rate for smoother motion; blur faces to anonymize people; and erase burned-in on-screen subtitles."
permissions:
  - shell
metadata:
  requires:
    bins: ["byteplus-mediakit-cli"]
  cliHelp: "byteplus-mediakit-cli video --help"
  product: "byteplus-mediakit-cli/skills"
  domain: video
  capability_count: 11
---
# video MediaKit Skill

## Rules

1. Read `../byted-bp-mediakit-shared/SKILL.md` first and run the shared checks. Stop if that Skill is missing.
2. Choose only from the `video` table below. Distinguish similar tools by each tool's description and parameter bounds.
3. Read the matching reference before calling. Approved copy and the live CLI `--schema` are the contract; do not invent fields.
4. Execute when the user provides a real media URL/path, a clear tool intent, and every required business parameter that has no documented default (or can be derived from the wording / a prior step). Ask for missing required parameters before submitting. Optional fields with documented defaults may be omitted so Cloud defaults apply — do not invent non-default values. Optional shared fields (`callback_*`, `client_token`, `queue_id`) may be passed only when the user provides them.
5. Before execution, set `MEDIAKIT_BYTEPLUS_SURFACE=skill` on the same command line to mark Skill-driven calls.
6. Before execution, set `MEDIAKIT_BYTEPLUS_RUNTIME=<runtime>` on the same command line; use `unknown` when the Agent host cannot be determined reliably.

## Clarification and cross-domain routing

If the user only mentions video and does not state a business goal, clarify first. Trim, concat, logo overlay, adding captions, mix, and photo slideshows belong to editing. Audio-only transcode or vocal separation belong to audio.

When a multi-step request starts with an editing action (trim/crop/flip/rotate/filter/subtitle/volume) and only later asks for enhance/transcode/highlights, the first Skill to load is still editing; this Skill covers the video-domain tail steps after editing outputs are ready. Do not steal the whole chain away from editing based only on the enhance/transcode tail.

Enhance routing: unqualified “enhance”, “enhance quality”, or “enhance image quality” → `enhance-video`. Phrases with fast/quickly/speed/low-latency → `enhance-video-fast`. Never ask the user which enhancement path to use.

Vague size requests such as “make the video smaller”, “缩小”, or “compress a bit” without naming crop, bitrate/container transcode, quality enhance, or low-bitrate HD/martencode are ambiguous across `crop-video`, `transcode-video`, `enhance-video` / `enhance-video-fast`, and `martencode-video`. Ask which treatment the user wants; do not submit any of those tools until they choose.

## Execution policy

- Prefer execution over clarification when required parameters are present or documented defaults cover them. If a required field has no default and was not provided, ask — do not invent it.
- Multi-step requests: before submitting **any** step, check that every later step's required-no-default business parameters are already known (user wording or a prior step output). If a later step still needs user-provided required content (for example subtitle text/`subtitle_url` for add-subtitle-to-video, crop rectangle, flip axis, rotate direction, highlight `target_duration`), ask for it first and do **not** submit earlier steps in parallel. Once the full chain is runnable, run tools in the user's stated order; after each async task reaches a terminal state, pass `result.video_url` / `result.audio_url` / `result.voice_audio_url` (whichever the next tool requires) into the next step. Do not invent intermediate URLs. Do not pause mid-chain to ask about optional or documented-default fields on later steps.
- Ambiguous goals that could mean different tools (for example “make the video smaller” could mean crop, bitrate transcode, quality enhance, or martencode): ask which treatment the user wants; do not submit any of those tools until they choose.

- If you already asked a clarifying question and the user has not answered, stop. Do not submit any business call, invent a default choice, or “proceed anyway”.
- Never silently clamp, truncate, pad, or drop values to fit a documented limit (bitrate, sample rate, character length, item count). Clarify or refuse; do not submit an altered request.

## Tools

| Tool | Description | Mode | Command | Reference |
| --- | --- | --- | --- | --- |
| enhance-video | Enhances video quality using AI algorithms for denoising, color enhancement, artifact removal, and super-resolution. | Cloud | `byteplus-mediakit-cli video enhance-video` | [reference/enhance-video.md](reference/enhance-video.md) |
| enhance-video-fast | Uses a speed-first algorithm combining lightweight super-resolution and intelligent quality enhancement to balance efficiency and visual quality. | Cloud | `byteplus-mediakit-cli video enhance-video-fast` | [reference/enhance-video-fast.md](reference/enhance-video-fast.md) |
| erase-video-subtitle-pro | This capability provides high-quality, seamless subtitle removal and restores the original picture to the greatest extent possible. Different queues are the primary way to allocate costs to specific projects. | Cloud | `byteplus-mediakit-cli video erase-video-subtitle-pro` | [reference/erase-video-subtitle-pro.md](reference/erase-video-subtitle-pro.md) |
| extract-frames | This capability can capture one or more still images from a video. The captured images can be used for video covers, preview thumbnails, sprite sheets, or as input for other video understanding tasks. | Cloud | `byteplus-mediakit-cli video extract-frames` | [reference/extract-frames.md](reference/extract-frames.md) |
| face-blur-video | It is suitable for protecting facial privacy, including short-drama content anonymization and preprocessing reference material for generative video. | Cloud | `byteplus-mediakit-cli video face-blur-video` | [reference/face-blur-video.md](reference/face-blur-video.md) |
| martencode-video | Low-bitrate HD transcoding analyzes video scenes, motion, content, and textures and selects optimal encoding parameters. The task outputs video with better perceived quality at a relatively lower bitrate, reducing bandwidth costs while improving the viewing experience. | Cloud | `byteplus-mediakit-cli video martencode-video` | [reference/martencode-video.md](reference/martencode-video.md) |
| remux-video | Remuxing can quickly convert between mainstream container formats such as MP4, FLV, and MPEG-TS with no quality loss and at very low cost. It changes only the container format and does not re-encode or re-decode the audio/video bitstreams. This is used for VOD distribution adaptation, streaming segmentation and packaging, and multi-device compatibility. | Cloud | `byteplus-mediakit-cli video remux-video` | [reference/remux-video.md](reference/remux-video.md) |
| segment-scenes | Automatically splits a video into multiple scene segments based on transitions and changes in visual content, and outputs each segment's timeline information and independent video file. | Cloud | `byteplus-mediakit-cli video segment-scenes` | [reference/segment-scenes.md](reference/segment-scenes.md) |
| transcode-video | Video transcoding converts one video bitstream into another, typically changing the encoding format, encoding parameters, and container format. | Cloud | `byteplus-mediakit-cli video transcode-video` | [reference/transcode-video.md](reference/transcode-video.md) |
| video-frame-interpolation | Uses deep learning to intelligently synthesize new frames and increase the video's frame rate to a specified target. This is particularly effective for enhancing the viewing experience of low-frame-rate, fast-motion, or large-screen videos. | Cloud | `byteplus-mediakit-cli video video-frame-interpolation` | [reference/video-frame-interpolation.md](reference/video-frame-interpolation.md) |
| video-highlights-llm | Automatically generates highlight reels from video content. | Cloud | `byteplus-mediakit-cli video video-highlights-llm` | [reference/video-highlights-llm.md](reference/video-highlights-llm.md) |

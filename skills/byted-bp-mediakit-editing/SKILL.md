---
name: byted-bp-mediakit-editing
version: "0.0.2"
license: "SEE LICENSE IN LICENSE"
description: "Use this skill to trim, join, crop, rotate, flip, speed up, slow down, filter or compose existing media, add subtitles or overlays, apply a camera motion, or create a scrolling-text video."
permissions:
  - shell
metadata:
  requires:
    bins: ["byteplus-mediakit-cli"]
  cliHelp: "byteplus-mediakit-cli editing --help"
  product: "byteplus-mediakit-cli/skills"
  domain: editing
  capability_count: 24
---
# editing MediaKit Skill

1. Read `../byted-bp-mediakit-shared/SKILL.md` first and run the shared checks. Stop if that Skill is missing.

Read the shared setup and the relevant capability reference. Inspect source metadata when time, dimensions or frame rate affect the operation. For intervals, use seconds and preserve each documented ordering relation; apply nested relations independently to every array element.

Supply subtitle content through a subtitle file or the structured subtitles array. The file takes priority when both are provided. Use the documented position and style controls; do not infer arbitrary coordinates or text styling from a preset. Select fonts, filters, transitions and camera motions through their approved semantic-to-ID mappings.

Use apply-camera-motion for zoom, pan-zoom, orbit-360 or bounce, with optional start and end times. Use text-to-scrolling-video for a 9:16 scrolling-text video. Keep important background content outside its top and bottom masking regions and choose readable text contrast.

Prefer a UTF-8 JSON file for structured subtitles, overlays, video lists and other nested settings. Inline JSON, @file, stdin and file URI must describe the same typed values. Preserve commas and quotation marks inside CSV string values. Keep the task ID for asynchronous results and query through the shared protocol.

## Tools

| Tool | Description | Mode | Command | Reference |
| --- | --- | --- | --- | --- |
| add-image-to-video | Can programmatically add a logo, watermark, or other image to a video. | Cloud | `byteplus-mediakit-cli editing add-image-to-video` | [reference/add-image-to-video.md](reference/add-image-to-video.md) |
| add-subtitle-to-video | Burns a specified subtitle file or text content into video frames with custom styling and produces a new video with embedded subtitles. | Cloud | `byteplus-mediakit-cli editing add-subtitle-to-video` | [reference/add-subtitle-to-video.md](reference/add-subtitle-to-video.md) |
| adjust-audio-speed | Adjusts the playback speed of an audio file to produce fast-forward or slow-motion effects while keeping the pitch natural. | Cloud | `byteplus-mediakit-cli editing adjust-audio-speed` | [reference/adjust-audio-speed.md](reference/adjust-audio-speed.md) |
| adjust-video-speed | Adjusts video playback speed to produce fast-forward or slow-motion effects. | Cloud | `byteplus-mediakit-cli editing adjust-video-speed` | [reference/adjust-video-speed.md](reference/adjust-video-speed.md) |
| adjust-video-volume | Adjusts the volume of a video. The output video is in MP4 format and keeps the same resolution as the source video. | Cloud | `byteplus-mediakit-cli editing adjust-video-volume` | [reference/adjust-video-volume.md](reference/adjust-video-volume.md) |
| apply-camera-motion | Add a zoom, pan-and-zoom, orbit or bounce effect to a video, with optional control over the affected time range. | Cloud | `byteplus-mediakit-cli editing apply-camera-motion` | [reference/apply-camera-motion.md](reference/apply-camera-motion.md) |
| apply-video-filter | Applies a filter effect to a specified video. | Cloud | `byteplus-mediakit-cli editing apply-video-filter` | [reference/apply-video-filter.md](reference/apply-video-filter.md) |
| concat-audio | Joins multiple audio files into a single complete audio file. The input audio files are joined in order. | Cloud | `byteplus-mediakit-cli editing concat-audio` | [reference/concat-audio.md](reference/concat-audio.md) |
| concat-video | Joins video clips in a specified order and can apply transition effects at the joins to create a seamless final video. | Cloud | `byteplus-mediakit-cli editing concat-video` | [reference/concat-video.md](reference/concat-video.md) |
| crop-video | Crops a video to a specified rectangular region and can return a new video containing only that region. | Cloud | `byteplus-mediakit-cli editing crop-video` | [reference/crop-video.md](reference/crop-video.md) |
| extract-animated-image | Can create an animated image in GIF or WebP format from a specific segment of a video. It is ideal for generating cover animations, marketing materials, and short previews. | Cloud | `byteplus-mediakit-cli editing extract-animated-image` | [reference/extract-animated-image.md](reference/extract-animated-image.md) |
| extract-audio | Separates the audio track from an input video file and generates a standalone audio file. | Cloud | `byteplus-mediakit-cli editing extract-audio` | [reference/extract-audio.md](reference/extract-audio.md) |
| fade-audio | Applies a fade-in or fade-out effect to a standalone audio file. It processes an independent audio source and returns a new audio file with the specified volume transitions. | Cloud | `byteplus-mediakit-cli editing fade-audio` | [reference/fade-audio.md](reference/fade-audio.md) |
| fade-video-audio | It modifies the audio component without altering the video stream. The processing provides a smoother listening experience. The output has the same resolution as the source. Using different queues is the primary way to allocate costs to specific projects. | Cloud | `byteplus-mediakit-cli editing fade-video-audio` | [reference/fade-video-audio.md](reference/fade-video-audio.md) |
| flip-video | Mirrors a specified video vertically or horizontally. | Cloud | `byteplus-mediakit-cli editing flip-video` | [reference/flip-video.md](reference/flip-video.md) |
| gaussian-blur-video | This tool can apply Gaussian blur to specific rectangular regions within defined time ranges. It can programmatically mask sensitive information or unwanted elements in a video. Common uses include obscuring faces, watermarks, or logos. The processed video is returned in MP4 format. | Cloud | `byteplus-mediakit-cli editing gaussian-blur-video` | [reference/gaussian-blur-video.md](reference/gaussian-blur-video.md) |
| image-to-video | This image-to-video tool combines multiple images in order and can optionally configure transition animations and in-image animations to produce a dynamic video. | Cloud | `byteplus-mediakit-cli editing image-to-video` | [reference/image-to-video.md](reference/image-to-video.md) |
| mix-audio | Mixes multiple audio files, such as background music, sound effects, and voice, into one new audio file. | Cloud | `byteplus-mediakit-cli editing mix-audio` | [reference/mix-audio.md](reference/mix-audio.md) |
| mux-audio-video | Combines an input audio stream and a video stream into a new video file. | Cloud | `byteplus-mediakit-cli editing mux-audio-video` | [reference/mux-audio-video.md](reference/mux-audio-video.md) |
| rotate-video | Rotates a video. | Cloud | `byteplus-mediakit-cli editing rotate-video` | [reference/rotate-video.md](reference/rotate-video.md) |
| stitch-video | Spatially combines the input videos horizontally or vertically to produce a single composited video, ideal for scenarios such as multi-view comparison and frame composition. The output video duration matches the longest input video, and shorter input videos are padded with a black screen after playback finishes. Output audio mixes the audio tracks of all input videos whose keep_audio value is true. | Cloud | `byteplus-mediakit-cli editing stitch-video` | [reference/stitch-video.md](reference/stitch-video.md) |
| text-to-scrolling-video | Turn text and a background image into a vertical scrolling-text video, with optional background music and timing controls. | Cloud | `byteplus-mediakit-cli editing text-to-scrolling-video` | [reference/text-to-scrolling-video.md](reference/text-to-scrolling-video.md) |
| trim-audio | Extracts a specified segment from an input audio file based on the given start and end times. | Cloud | `byteplus-mediakit-cli editing trim-audio` | [reference/trim-audio.md](reference/trim-audio.md) |
| trim-video | Extracts a specified segment from an input video based on the given start and end times. | Cloud | `byteplus-mediakit-cli editing trim-video` | [reference/trim-video.md](reference/trim-video.md) |

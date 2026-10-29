---
name: byted-bp-mediakit-editing
version: "0.0.1"
license: "Apache-2.0"
description: "Use this skill first for hands-on editing of an existing clip, recording, voice note, or set of photos — load it before asking for files, paths, or timestamps. Use it to trim video or audio to one moment or a middle section; stitch, join, or merge clips, takes, or voice notes end to end in order into one file; turn still photos into a slideshow or a video moment into a GIF. Sound: change speed while keeping the voice natural, adjust or fade volume including a video's own soundtrack without touching the picture, mix a voiceover over music, extract a video's soundtrack into its own file, or swap in a new track. Picture: add a logo or watermark, burn captions or subtitles into the frames, place videos side by side, crop to vertical 9:16, rotate or mirror sideways footage, blur a region like a license plate, or apply a warm cinematic look."
permissions:
  - shell
metadata:
  requires:
    bins: ["byteplus-mediakit-cli"]
  cliHelp: "byteplus-mediakit-cli editing --help"
  product: "byteplus-mediakit-cli/skills"
  domain: editing
  capability_count: 22
---
# editing MediaKit Skill

## Rules

1. Read `../byted-bp-mediakit-shared/SKILL.md` first and run the shared checks. Stop if that Skill is missing.
2. Choose only from the `editing` table below. Distinguish similar tools by each tool's description and parameter bounds.
3. Read the matching reference before calling. Approved copy and the live CLI `--schema` are the contract; do not invent fields.
4. Execute when the user provides a real media URL/path, a clear tool intent, and every required business parameter that has no documented default (or can be derived from the wording / a prior step). Ask for missing required parameters before submitting. Optional fields with documented defaults may be omitted so Cloud defaults apply — do not invent non-default values. Optional shared fields (`callback_*`, `client_token`, `queue_id`) may be passed only when the user provides them.
5. Before execution, set `MEDIAKIT_BYTEPLUS_SURFACE=skill` on the same command line to mark Skill-driven calls.
6. Before execution, set `MEDIAKIT_BYTEPLUS_RUNTIME=<runtime>` on the same command line; use `unknown` when the Agent host cannot be determined reliably.

## Clarification and cross-domain routing

If only a media type is given and the user has not said whether to cut, join, overlay, or adjust, clarify first. Picture enhancement, burned-in subtitle removal, and face-identity blur belong to video. Audio format conversion and vocal isolation belong to audio.

Multi-step mixed tasks that start with editing operations (trim, crop, flip, rotate, adjust-video-volume, apply-video-filter, add-subtitle, etc.) are owned by this Skill first: load editing, run the editing steps, then hand off to video/audio Skills for later enhance/transcode/separate-voice steps. Do not abandon editing for video merely because a later step is enhance or transcode.

## Execution policy

- Prefer execution over clarification when required parameters are present or documented defaults cover them. If a required field has no default and was not provided, ask — do not invent it.
- Multi-step requests: before submitting **any** step, check that every later step's required-no-default business parameters are already known (user wording or a prior step output). If a later step still needs user-provided required content (for example subtitle text/`subtitle_url` for add-subtitle-to-video, crop rectangle, flip axis, rotate direction, highlight `target_duration`), ask for it first and do **not** submit earlier steps in parallel. Once the full chain is runnable, run tools in the user's stated order; after each async task reaches a terminal state, pass `result.video_url` / `result.audio_url` / `result.voice_audio_url` (whichever the next tool requires) into the next step. Do not invent intermediate URLs. Do not pause mid-chain to ask about optional or documented-default fields on later steps.
- Ambiguous goals that could mean different tools (for example “make the video smaller” could mean crop, bitrate transcode, quality enhance, or martencode): ask which treatment the user wants; do not submit any of those tools until they choose.

- If you already asked a clarifying question and the user has not answered, stop. Do not submit any business call, invent a default choice, or “proceed anyway”.
- Never silently clamp, truncate, pad, or drop values to fit a documented limit (bitrate, sample rate, character length, item count). Clarify or refuse; do not submit an altered request.

## Tools

| Tool | Description | Mode | Command | Reference |
| --- | --- | --- | --- | --- |
| add-image-to-video | Can programmatically add a logo, watermark, or other image to a video. | Cloud | `byteplus-mediakit-cli editing add-image-to-video` | [reference/add-image-to-video.md](reference/add-image-to-video.md) |
| add-subtitle-to-video | Burns a specified subtitle file or text content into video frames with custom styling and produces a new video with embedded subtitles. | Cloud | `byteplus-mediakit-cli editing add-subtitle-to-video` | [reference/add-subtitle-to-video.md](reference/add-subtitle-to-video.md) |
| adjust-audio-speed | Adjusts the playback speed of an audio file to produce fast-forward or slow-motion effects while keeping the pitch natural. | Cloud | `byteplus-mediakit-cli editing adjust-audio-speed` | [reference/adjust-audio-speed.md](reference/adjust-audio-speed.md) |
| adjust-video-speed | Adjusts video playback speed to produce fast-forward or slow-motion effects. | Cloud | `byteplus-mediakit-cli editing adjust-video-speed` | [reference/adjust-video-speed.md](reference/adjust-video-speed.md) |
| adjust-video-volume | Adjusts the volume of a video. The output video is in MP4 format and keeps the same resolution as the source video. | Cloud | `byteplus-mediakit-cli editing adjust-video-volume` | [reference/adjust-video-volume.md](reference/adjust-video-volume.md) |
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
| trim-audio | Extracts a specified segment from an input audio file based on the given start and end times. | Cloud | `byteplus-mediakit-cli editing trim-audio` | [reference/trim-audio.md](reference/trim-audio.md) |
| trim-video | Extracts a specified segment from an input video based on the given start and end times. | Cloud | `byteplus-mediakit-cli editing trim-video` | [reference/trim-video.md](reference/trim-video.md) |

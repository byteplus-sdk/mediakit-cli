# BytePlus MediaKit CLI

BytePlus MediaKit CLI provides 75 Cloud media processing capabilities across audio, editing, image, video and shared workflows.

## Install and inspect

Install with Node.js 18 or newer. Installation downloads the platform binary and installs all five Skills.

```bash
npm install --global @byteplus/mediakit-cli@0.0.2
byteplus-mediakit-cli --version
byteplus-mediakit-cli --domains
byteplus-mediakit-cli <domain> <tool> --help
byteplus-mediakit-cli <domain> <tool> --schema
```

## Credentials and execution

Supply your BytePlus API key through `MEDIAKIT_BYTEPLUS_API_KEY`. Run through an Agent host that supplies the runtime context.

Capability commands submit requests to BytePlus MediaKit under your account's service and billing terms. Stopping polling stops the local wait; it does not cancel a submitted remote task.

Read the selected capability help before supplying parameters. A required parameter must be present; its type, empty-value rules and ranges come from its Schema. Preserve explicitly supplied zero and false values.

The CLI returns structured JSON and service media URLs. Pass the returned media URL to a dependent operation after its task completes.

For an asynchronous result, query the returned task ID with `byteplus-mediakit-cli shared query-task --task-id <returned_task_id> --poll-complete`.

## Capabilities

| Domain | Tool | Description |
| --- | --- | --- |
| audio | `detect-voice-activity` | Identify speech and non-speech intervals in an audio or video recording and return their timestamps. |
| audio | `probe-audio-metadata` | Inspect an audio file and return its format, duration, bitrate, sample rate and channel information. |
| audio | `separate-voice` | Separates the vocals and background audio from an audio or video file into two independent audio files. |
| audio | `transcode-audio` | Audio transcoding converts one audio bitstream into another and can change the encoding format, encoding parameters, and container format to adapt to business scenarios, playback devices, and network environments. |
| editing | `add-image-to-video` | Can programmatically add a logo, watermark, or other image to a video. |
| editing | `add-subtitle-to-video` | Burns a specified subtitle file or text content into video frames with custom styling and produces a new video with embedded subtitles. |
| editing | `adjust-audio-speed` | Adjusts the playback speed of an audio file to produce fast-forward or slow-motion effects while keeping the pitch natural. |
| editing | `adjust-video-speed` | Adjusts video playback speed to produce fast-forward or slow-motion effects. |
| editing | `adjust-video-volume` | Adjusts the volume of a video. The output video is in MP4 format and keeps the same resolution as the source video. |
| editing | `apply-camera-motion` | Add a zoom, pan-and-zoom, orbit or bounce effect to a video, with optional control over the affected time range. |
| editing | `apply-video-filter` | Applies a filter effect to a specified video. |
| editing | `concat-audio` | Joins multiple audio files into a single complete audio file. The input audio files are joined in order. |
| editing | `concat-video` | Joins video clips in a specified order and can apply transition effects at the joins to create a seamless final video. |
| editing | `crop-video` | Crops a video to a specified rectangular region and can return a new video containing only that region. |
| editing | `extract-animated-image` | Can create an animated image in GIF or WebP format from a specific segment of a video. It is ideal for generating cover animations, marketing materials, and short previews. |
| editing | `extract-audio` | Separates the audio track from an input video file and generates a standalone audio file. |
| editing | `fade-audio` | Applies a fade-in or fade-out effect to a standalone audio file. It processes an independent audio source and returns a new audio file with the specified volume transitions. |
| editing | `fade-video-audio` | It modifies the audio component without altering the video stream. The processing provides a smoother listening experience. The output has the same resolution as the source. Using different queues is the primary way to allocate costs to specific projects. |
| editing | `flip-video` | Mirrors a specified video vertically or horizontally. |
| editing | `gaussian-blur-video` | This tool can apply Gaussian blur to specific rectangular regions within defined time ranges. It can programmatically mask sensitive information or unwanted elements in a video. Common uses include obscuring faces, watermarks, or logos. The processed video is returned in MP4 format. |
| editing | `image-to-video` | This image-to-video tool combines multiple images in order and can optionally configure transition animations and in-image animations to produce a dynamic video. |
| editing | `mix-audio` | Mixes multiple audio files, such as background music, sound effects, and voice, into one new audio file. |
| editing | `mux-audio-video` | Combines an input audio stream and a video stream into a new video file. |
| editing | `rotate-video` | Rotates a video. |
| editing | `stitch-video` | Spatially combines the input videos horizontally or vertically to produce a single composited video, ideal for scenarios such as multi-view comparison and frame composition. The output video duration matches the longest input video, and shorter input videos are padded with a black screen after playback finishes. Output audio mixes the audio tracks of all input videos whose keep_audio value is true. |
| editing | `text-to-scrolling-video` | Turn text and a background image into a vertical scrolling-text video, with optional background music and timing controls. |
| editing | `trim-audio` | Extracts a specified segment from an input audio file based on the given start and end times. |
| editing | `trim-video` | Extracts a specified segment from an input video based on the given start and end times. |
| image | `add-image-invisible-watermark` | Embed hidden text in an image for copyright tracing. Record the selected model version for later extraction. |
| image | `add-image-watermark` | Add a visible text or image watermark to an image, with controls for placement, appearance and tiling. |
| image | `adjust-image-color` | Adjust an image's brightness, contrast or saturation using a preset effect. |
| image | `compress-image` | Reduce image file size by setting a quality level or size limit, with optional output format conversion. |
| image | `crop-image` | Crop an image by direction, origin, custom coordinates or an inscribed circle. |
| image | `enhance-image` | Improve image clarity and detail, with optional upscaling and a choice of enhancement model versions. |
| image | `erase-image` | Remove text, icons or selected regions from an image and reconstruct the surrounding background. |
| image | `evaluate-image-quality` | Assess image quality across selected standard dimensions or with a professional model that returns a fixed set of scores. |
| image | `extract-image-invisible-watermark` | Recover text embedded by AI MediaKit image invisible watermarking, using the same model version as the add operation. |
| image | `face-blur-image` | Detect and mask faces in an image using mosaic or Gaussian blur, with controls for detection and masking strength. |
| image | `flip-image` | Flip an image horizontally or vertically. |
| image | `gaussian-blur-image` | Apply Gaussian blur to an image with a configurable blur strength. |
| image | `image-ocr` | Recognize text in an image, with standard and advanced recognition modes and optional keyword extraction. |
| image | `invert-image` | Invert an image's colors to create a negative effect. |
| image | `mosaic-image` | Apply pixelation to a specified rectangular region of an image. |
| image | `probe-image-metadata` | Inspect an image's metadata, embedded AIGC information or transparency information. |
| image | `remove-image-background` | Retain the subject of an image while removing its background, using a general, human or product matting scene. |
| image | `resize-image` | Resize an image by dimensions, edge length or scale, with a choice of aspect ratio handling. |
| image | `rotate-image` | Rotate an image by a specified angle and set the fill color for uncovered areas. |
| image | `round-corner-image` | Add circular or elliptical rounded corners to an image. |
| image | `sharpen-image` | Strengthen image edges and fine details with a configurable sharpening strength. |
| image | `slim-image` | Use intelligent image compression to reduce file size while improving texture detail and reducing visual artifacts. |
| shared | `call-maas-proxy` | Call a supported ModelArk or Seed Speech model through AI MediaKit by supplying its service, API name and model-specific arguments. |
| video | `add-video-invisible-watermark` | Embed a hidden numeric watermark in a video for copyright tracing and later authenticity checks. |
| video | `assess-video-quality` | Assess the perceptual quality of a video and return a VQScore from 0 to 100. |
| video | `drama-script` | Reconstruct a script from videos with readable hard subtitles, returning dialogue, visual descriptions and related analysis assets. |
| video | `enhance-video` | Enhances video quality using AI algorithms for denoising, color enhancement, artifact removal, and super-resolution. |
| video | `enhance-video-fast` | Uses a speed-first algorithm combining lightweight super-resolution and intelligent quality enhancement to balance efficiency and visual quality. |
| video | `enhance-video-generative` | Improve low-quality video using AI enhancement, with optional upscaling and frame interpolation. This tool accepts SDR input. |
| video | `enhance-video-smoothness` | Detect and optionally repair periodic stutter and duplicate frames in a video. Detection can also run without producing a repaired video. |
| video | `erase-video-subtitle-pro` | This capability provides high-quality, seamless subtitle removal and restores the original picture to the greatest extent possible. Different queues are the primary way to allocate costs to specific projects. |
| video | `extract-frames` | This capability can capture one or more still images from a video. The captured images can be used for video covers, preview thumbnails, sprite sheets, or as input for other video understanding tasks. |
| video | `extract-video-invisible-watermark` | Recover numeric watermarks embedded by AI MediaKit video invisible watermarking for tracing and authenticity checks. |
| video | `face-blur-video` | It is suitable for protecting facial privacy, including short-drama content anonymization and preprocessing reference material for generative video. |
| video | `martencode-video` | Low-bitrate HD transcoding analyzes video scenes, motion, content, and textures and selects optimal encoding parameters. The task outputs video with better perceived quality at a relatively lower bitrate, reducing bandwidth costs while improving the viewing experience. |
| video | `matte-greenscreen-video` | Separate a video's subject from a green-screen or solid-color background for compositing or a replacement background. |
| video | `matte-portrait-video` | Separate people from a video's background for compositing or a replacement background. |
| video | `probe-video-metadata` | Inspect a video and return its container, duration, resolution, frame rate and stream information. |
| video | `remux-video` | Remuxing can quickly convert between mainstream container formats such as MP4, FLV, and MPEG-TS with no quality loss and at very low cost. It changes only the container format and does not re-encode or re-decode the audio/video bitstreams. This is used for VOD distribution adaptation, streaming segmentation and packaging, and multi-device compatibility. |
| video | `segment-scenes` | Automatically splits a video into multiple scene segments based on transitions and changes in visual content, and outputs each segment's timeline information and independent video file. |
| video | `semantic-segment` | Analyze a video's visual, audio and narrative structure and return semantically complete segment timelines. |
| video | `semantic-segment-pro` | Split a video into semantically complete segments using content-specific strategies, with optional shot, scene, sentence and segment-video results. |
| video | `transcode-video` | Video transcoding converts one video bitstream into another, typically changing the encoding format, encoding parameters, and container format. |
| video | `video-depth` | Generate a continuous relative-depth video that retains motion and camera movement for downstream motion-reference and visual-effects workflows. |
| video | `video-frame-interpolation` | Uses deep learning to intelligently synthesize new frames and increase the video's frame rate to a specified target. This is particularly effective for enhancing the viewing experience of low-frame-rate, fast-motion, or large-screen videos. |
| video | `video-highlights-llm` | Automatically generates highlight reels from video content. |
| video | `video-understand-router` | Analyze video frames and optionally audio to produce structured video understanding results, using automatic or manual analysis settings. |

## npm installation

Install the CLI and its five bundled Agent Skills with `npx @byteplus/mediakit-cli@0.0.2 install -y`.

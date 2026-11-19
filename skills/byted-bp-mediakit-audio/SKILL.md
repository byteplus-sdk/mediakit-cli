---
name: byted-bp-mediakit-audio
version: "0.0.2"
license: "SEE LICENSE IN LICENSE"
description: "Use this skill to inspect an audio file, locate speech intervals, change an audio encoding, or separate vocals, music, background and sound effects according to the recording's scene."
permissions:
  - shell
metadata:
  requires:
    bins: ["byteplus-mediakit-cli"]
  cliHelp: "byteplus-mediakit-cli audio --help"
  product: "byteplus-mediakit-cli/skills"
  domain: audio
  capability_count: 4
---
# audio MediaKit Skill

1. Read `../byted-bp-mediakit-shared/SKILL.md` first and run the shared checks. Stop if that Skill is missing.

Read the shared setup and the selected capability reference. Use probe-audio-metadata when the decision depends on format, duration, bitrate, sample rate or channels. Use detect-voice-activity for speech and non-speech intervals, supplying exactly one audio or video input.

Use transcode-audio to change encoding or packaging. Follow the reference's conditional container and audio settings; an omitted optional setting remains omitted. Use separate-voice for track separation. Audio and Music produce vocals and background; Drama, Narrate and Game produce vocals, music and sound effects. The output default remains MP3. A background track must not be described as pure instrumental accompaniment without inspecting it.

Keep the submitted task ID and query its result through the shared protocol. Save returned files within their documented validity period. Use the output metadata and the user's playback requirements when choosing any further processing.

## Tools

| Tool | Description | Mode | Command | Reference |
| --- | --- | --- | --- | --- |
| detect-voice-activity | Identify speech and non-speech intervals in an audio or video recording and return their timestamps. | Cloud | `byteplus-mediakit-cli audio detect-voice-activity` | [reference/detect-voice-activity.md](reference/detect-voice-activity.md) |
| probe-audio-metadata | Inspect an audio file and return its format, duration, bitrate, sample rate and channel information. | Cloud | `byteplus-mediakit-cli audio probe-audio-metadata` | [reference/probe-audio-metadata.md](reference/probe-audio-metadata.md) |
| separate-voice | Separates the vocals and background audio from an audio or video file into two independent audio files. | Cloud | `byteplus-mediakit-cli audio separate-voice` | [reference/separate-voice.md](reference/separate-voice.md) |
| transcode-audio | Audio transcoding converts one audio bitstream into another and can change the encoding format, encoding parameters, and container format to adapt to business scenarios, playback devices, and network environments. | Cloud | `byteplus-mediakit-cli audio transcode-audio` | [reference/transcode-audio.md](reference/transcode-audio.md) |

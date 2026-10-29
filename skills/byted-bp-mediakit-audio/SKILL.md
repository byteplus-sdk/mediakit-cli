---
name: byted-bp-mediakit-audio
version: "0.0.1"
license: "Apache-2.0"
description: "Use this skill when the user wants either (1) the same recording delivered in a different audio encoding — MP3, AAC, WAV, another codec, bitrate, or container — so a device or app can play it, or (2) one song taken apart into two outputs: an isolated vocal and an instrumental backing, such as removing the singer to get a karaoke track. Load for re-encoding audio or splitting the singing out of accompaniment, even when the exact tool is unclear."
permissions:
  - shell
metadata:
  requires:
    bins: ["byteplus-mediakit-cli"]
  cliHelp: "byteplus-mediakit-cli audio --help"
  product: "byteplus-mediakit-cli/skills"
  domain: audio
  capability_count: 2
---
# audio MediaKit Skill

## Rules

1. Read `../byted-bp-mediakit-shared/SKILL.md` first and run the shared checks. Stop if that Skill is missing.
2. Choose only from the `audio` table below. Distinguish similar tools by each tool's description and parameter bounds.
3. Read the matching reference before calling. Approved copy and the live CLI `--schema` are the contract; do not invent fields.
4. Execute when the user provides a real media URL/path, a clear tool intent, and every required business parameter that has no documented default (or can be derived from the wording / a prior step). Ask for missing required parameters before submitting. Optional fields with documented defaults may be omitted so Cloud defaults apply — do not invent non-default values. Optional shared fields (`callback_*`, `client_token`, `queue_id`) may be passed only when the user provides them.
5. Before execution, set `MEDIAKIT_BYTEPLUS_SURFACE=skill` on the same command line to mark Skill-driven calls.
6. Before execution, set `MEDIAKIT_BYTEPLUS_RUNTIME=<runtime>` on the same command line; use `unknown` when the Agent host cannot be determined reliably.

## Clarification and cross-domain routing

If the user only mentions audio and does not state a business goal, clarify first. Trim, concat, speed, fade, mix, extract, or mux belong to editing. Subtitles, understanding, and video enhancement belong to video.

## Execution policy

- Prefer execution over clarification when required parameters are present or documented defaults cover them. If a required field has no default and was not provided, ask — do not invent it.
- Multi-step requests: before submitting **any** step, check that every later step's required-no-default business parameters are already known (user wording or a prior step output). If a later step still needs user-provided required content (for example subtitle text/`subtitle_url` for add-subtitle-to-video, crop rectangle, flip axis, rotate direction, highlight `target_duration`), ask for it first and do **not** submit earlier steps in parallel. Once the full chain is runnable, run tools in the user's stated order; after each async task reaches a terminal state, pass `result.video_url` / `result.audio_url` / `result.voice_audio_url` (whichever the next tool requires) into the next step. Do not invent intermediate URLs. Do not pause mid-chain to ask about optional or documented-default fields on later steps.
- Ambiguous goals that could mean different tools (for example “make the video smaller” could mean crop, bitrate transcode, quality enhance, or martencode): ask which treatment the user wants; do not submit any of those tools until they choose.

- If you already asked a clarifying question and the user has not answered, stop. Do not submit any business call, invent a default choice, or “proceed anyway”.
- Never silently clamp, truncate, pad, or drop values to fit a documented limit (bitrate, sample rate, character length, item count). Clarify or refuse; do not submit an altered request.

## Tools

| Tool | Description | Mode | Command | Reference |
| --- | --- | --- | --- | --- |
| separate-voice | Separates the vocals and background audio from an audio or video file into two independent audio files. | Cloud | `byteplus-mediakit-cli audio separate-voice` | [reference/separate-voice.md](reference/separate-voice.md) |
| transcode-audio | Audio transcoding converts one audio bitstream into another and can change the encoding format, encoding parameters, and container format to adapt to business scenarios, playback devices, and network environments. | Cloud | `byteplus-mediakit-cli audio transcode-audio` | [reference/transcode-audio.md](reference/transcode-audio.md) |

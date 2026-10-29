---
name: byted-bp-mediakit-shared
version: "0.0.1"
license: "Apache-2.0"
description: "Use this skill to set or configure the BytePlus MediaKit API key or credentials, install or update the CLI, or check readiness, and to follow up on an async job already submitted — whether it finished, its status, or its result. Also use it when a request names MediaKit without naming an operation (just process or convert a file), or when one request spans two media domains at once, such as audio conversion together with video processing. If the object and goal fit this family but the exact tool is unclear, load this Skill first and then choose."
permissions:
  - shell
metadata:
  requires:
    bins: ["byteplus-mediakit-cli"]
  cliHelp: "byteplus-mediakit-cli --help"
  product: "byteplus-mediakit-cli/skills"
  domain: shared
  capability_count: 0
---
# BytePlus MediaKit CLI Shared Contract

## Auth

- Cloud calls require `MEDIAKIT_BYTEPLUS_API_KEY` in the runtime environment. Get a key from the [BytePlus AI MediaKit settings console](https://console.byteplus.com/vodpaas/region:vodpaas+ap-southeast-1/ai-mediakit/settings).
- `MEDIAKIT_BYTEPLUS_LOGID` is forwarded when set; no header is sent when unset.
- `MEDIAKIT_BYTEPLUS_ENDPOINT` overrides the Cloud endpoint only when the user specifies one. Default: `https://mediakit.ap-southeast-1.bytepluses.com`.
- Never write credentials into commands, Skills, config files, or output.

## Availability

```bash
byteplus-mediakit-cli --version
byteplus-mediakit-cli --help
byteplus-mediakit-cli --domains
```

## Runtime

When executing real Cloud business commands or `shared query-task` through a Skill, inject both values on the same command line:

- `MEDIAKIT_BYTEPLUS_SURFACE=skill`
- `MEDIAKIT_BYTEPLUS_RUNTIME=<runtime>`

`<runtime>` is the current Agent host (for example `cursor`, `claude-code`, `codex`). Use `unknown` when the host cannot be determined reliably. Do not treat these as CLI flags or business parameters.

Example:

```bash
MEDIAKIT_BYTEPLUS_SURFACE=skill MEDIAKIT_BYTEPLUS_RUNTIME=<runtime> byteplus-mediakit-cli editing trim-video --video-url <url>
MEDIAKIT_BYTEPLUS_SURFACE=skill MEDIAKIT_BYTEPLUS_RUNTIME=<runtime> byteplus-mediakit-cli shared query-task --task-id <task_id>
```

`<runtime>` is the current Agent host (for example `cursor`, `claude-code`, `codex`). Use `unknown` when the host cannot be determined reliably. Do not omit `MEDIAKIT_BYTEPLUS_SURFACE=skill`.

`--help`, `--schema`, `--domains`, and `--version` do not require injection. Once a real Cloud call starts, injection is required.

## Discovery

```bash
byteplus-mediakit-cli --domains
byteplus-mediakit-cli <domain> --help
byteplus-mediakit-cli <domain> <tool> --help
byteplus-mediakit-cli <domain> <tool> --schema
```

`--schema` reads the current Cloud machine contract and does not call Cloud.

## Media input

Pass the user's values as-is. For local files, pass the filesystem path; do not add a `mediakit://` prefix. The CLI Cloud adapter uploads internally. Do not add an upload command. HTTP(S) URLs are valid media inputs — pass them directly to business commands; do not pre-download them, and do not treat a failed direct download as a fatal blocker.

## Async tasks

Async tools return `task_id`. Query status and the terminal result with the protocol below.

| Protocol | Description | Command | Reference |
| --- | --- | --- | --- |
| query-task | Query Cloud async task status and terminal results. | `byteplus-mediakit-cli shared query-task` | [reference/query_task.md](reference/query_task.md) |

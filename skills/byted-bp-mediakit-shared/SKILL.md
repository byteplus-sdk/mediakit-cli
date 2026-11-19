---
name: byted-bp-mediakit-shared
version: "0.0.2"
license: "SEE LICENSE IN LICENSE"
description: "Use this skill to choose a BytePlus MediaKit workflow, configure credentials, install or update the CLI, check readiness, query an existing asynchronous task, or call a supported model through MediaKit. Also use it when a request spans audio, editing, image or video workflows."
permissions:
  - shell
metadata:
  requires:
    bins: ["byteplus-mediakit-cli"]
  cliHelp: "byteplus-mediakit-cli --help"
  product: "byteplus-mediakit-cli/skills"
  domain: shared
  capability_count: 1
---
# BytePlus MediaKit CLI Shared Contract

## Auth

- Cloud calls require `MEDIAKIT_BYTEPLUS_API_KEY` in the runtime environment.
- `MEDIAKIT_LOGID` is forwarded when set; no header is sent when unset.
- `MEDIAKIT_BYTEPLUS_ENDPOINT` overrides the Cloud endpoint only when the user specifies one.
- Never write credentials into commands, Skills, config files, or output.

## Availability

```bash
byteplus-mediakit-cli --version
byteplus-mediakit-cli --help
byteplus-mediakit-cli --domains
```

Load the operation's capability reference before choosing parameters. Route audio inspection, transcoding and track separation to audio; clip editing and composition to editing; image inspection and processing to image; video inspection, enhancement and understanding to video. Use the shared model-proxy reference for supported ModelArk and Seed Speech interfaces.

Use the caller's existing BytePlus API key and the shared runtime setup. For a Skill business command, set MEDIAKIT_BYTEPLUS_SURFACE=skill and MEDIAKIT_BYTEPLUS_RUNTIME to the current Agent host; use unknown when it cannot be identified. Help and Schema discovery do not require credentials or runtime injection.

Treat required-field presence separately from value validity. Supply required flags explicitly, including a documented default when the request supports that choice. Preserve valid zero, false, empty text, empty collections and nullable values. Optional fields can remain omitted. An explicitly supplied empty value or null must still satisfy its Schema.

JSON object and object-array parameters accept inline JSON, @path, stdin (-) and file://. One command may have only one stdin consumer. Scalar arrays also accept CSV and repeated flags. JSON string arrays use a file, stdin or file URI; numeric and boolean arrays also accept inline JSON. Quote CSV string literals that look like input sources. Use UTF-8 files for complex PowerShell input.

Use the user's local path or HTTP(S) media URL directly. Local input upload is handled by the CLI. When duration, dimensions, frame rate, audio presence or speech intervals affect the chosen operation, explicitly use the relevant metadata probe or voice-activity tool and inspect its result before submitting the operation.

Synchronous image tools return their result directly. For asynchronous tools, keep task_id and query that task through shared query-task. While a task is queued or running, continue querying its existing ID. A stopped wait is not a failed task and must not trigger another submission. A retry of the same uncertain submission keeps its original client_token policy; an explicitly requested new run uses a new token when supported.

Present a failed parameter path and its violated constraint to the user. Preserve the returned service error and any task or request ID. Recommend a resize or compression only when its input contract accepts the same source and its documented transformation can satisfy the failed limit; obtain missing target settings and explain any quality or format change before executing it.

## Discovery

```bash
byteplus-mediakit-cli --domains
byteplus-mediakit-cli <domain> --help
byteplus-mediakit-cli <domain> <tool> --help
byteplus-mediakit-cli <domain> <tool> --schema
```

`--schema` reads the current Cloud machine contract and does not call Cloud.

## Media input

Pass the user's values as-is. For local files, pass the filesystem path; do not add a `mediakit://` prefix. The CLI Cloud adapter uploads internally. Do not add an upload command.

## Async tasks

Async tools return `task_id`. Query status and the terminal result with the protocol below.

| Protocol | Description | Command | Reference |
| --- | --- | --- | --- |
| query-task | Query Cloud async task status and terminal results. | `byteplus-mediakit-cli shared query-task` | [reference/query_task.md](reference/query_task.md) |

## Tools

| Tool | Description | Mode | Command | Reference |
| --- | --- | --- | --- | --- |
| call-maas-proxy | Call a supported ModelArk or Seed Speech model through AI MediaKit by supplying its service, API name and model-specific arguments. | Cloud | `byteplus-mediakit-cli shared call-maas-proxy` | [reference/call-maas-proxy.md](reference/call-maas-proxy.md) |

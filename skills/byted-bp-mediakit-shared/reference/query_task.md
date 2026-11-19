# Query Task

## Capability

Query async task status and results

## Parameter rules

- task_id must come from a real async task submission result. Optional polling parameters may be filled only when the user specifies them, or when they can be determined accurately from user intent; do not invent values. Before submitting a business task, resolve required fields and required alternatives from the user's explicit requirements, authoritative prior results, or an unambiguous documented mapping. A media type, genre, or broad processing goal does not by itself supply missing user choices or selection criteria. If a required choice has no documented default and remains unresolved, ask a focused clarification, preserve the user's known constraints, and stop before business submission. Do not invent a custom criterion merely to make a required field present. For optional parameters, infer only values implementing a behavior the user actually requested or required by a declared parameter relation. Do not add extra processing, alignment, formatting, or tuning merely because it seems helpful.
- max_poll_timeout_seconds defaults to 0 (unlimited); fill it only when the user explicitly needs to cap total polling time for one call. When the timeout is greater than 0, poll_interval_seconds × max_poll_attempts must not exceed this limit.

## Cloud

### Command and lifecycle

- Command: `byteplus-mediakit-cli shared query-task`
- Lifecycle: sync
- Return: returns the Cloud business result directly.

### Usage guide

- Boolean parameters (`--poll-complete`) must be written as `--flag=true` or `--flag=false`, or as bare `--flag` (equivalent to true). Do not pass a space-separated value such as `--flag true`, which the CLI treats as a positional argument.
- Pass a boolean explicitly when the user specifies true or false, including values equal to the default. Omit it when the user has not specified a value and no relation requires it.
- Requesting the main operation does not request every optional behavior. Do not enable an optional switch or fill its dependent defaults merely because input metadata differs or a changed output would be more convenient. Keep those fields omitted unless the user requested that separate behavior or a declared parameter relation requires their explicit presence.

### Example

```bash
byteplus-mediakit-cli shared query-task \
  --task-id '<task_id>'
```

Replace placeholders with real user input only. Optional flags follow the parameter rules; do not invent URLs, files, enums, or business parameters.
Pass each scalar value as one shell argument. The quotes in these templates are illustrative: escape embedded quotes for the actual shell, or use an argument array when invoking a process. Never concatenate raw user input into shell code.

### Parameters

| Path | CLI flag | Type | Required | Default | Enum/range | Description |
| --- | --- | --- | --- | --- | --- | --- |
| `max_poll_attempts` | `--max-poll-attempts` | integer | no | 0 | min: 0 | Maximum poll attempts; 0 means query once when poll_complete is false, or unlimited polling when poll_complete is true. Interrupt the CLI process to stop waiting without canceling the Cloud task. |
| `max_poll_timeout_seconds` | `--max-poll-timeout-seconds` | number | no | 0 | min: 0 | Total polling timeout in seconds; 0 means unlimited. When this limit is greater than 0, poll_interval_seconds × max_poll_attempts must not exceed it. |
| `poll_complete` | `--poll-complete` | boolean | no | false | - | Whether to keep polling until the task reaches a terminal state. |
| `poll_interval_seconds` | `--poll-interval-seconds` | number | no | 10 | greater than: 0 | Polling interval in seconds; must be greater than 0 and is used only during continuous polling. |
| `task_id` | `--task-id` | string | yes | - | min length: 1 | Unique async task id used to query status and the terminal result. |

### Result

| Path | Type | Required | Mode | Description |
| --- | --- | --- | --- | --- |
| `created_at` | integer | no | Cloud | Task creation timestamp. |
| `error` | object / null | no | Cloud | Error information when the query or the task fails. Preserve backend error content; the CLI may provide a diagnostic object when failure details are missing. |
| `expires_at` | integer | no | Cloud | Task result expiration timestamp. |
| `finished_at` | integer | no | Cloud | Task completion or failure timestamp. |
| `queue_id` | string | no | Cloud | Identifier of the task queue. |
| `request_id` | string | no | Cloud | Request id; present only when the backend returns a non-empty value. |
| `result` | object | no | Cloud | Business result returned after task completion; its structure varies by task type. |
| `status` | string | no | Cloud | Task status: running, completed or failed. Completed is a successful terminal state; failed is a failure terminal state. Constraints: enum: ["running","completed","failed"]. |
| `success` | boolean | no | Cloud | Returns false for failure terminal states; other values appear only when returned by the backend. |
| `task_id` | string | no | Cloud | Unique async task id used to query status and the terminal result. |
| `task_type` | string | no | Cloud | Task type; present only when the backend returns a non-empty value. |

After a synchronous business tool succeeds or an asynchronous task reaches completed, return its actual result and any output asset URLs to the user. Use the returned URLs as inputs to requested dependent steps. Download assets or perform additional local processing only when the user requests it, and retain the Cloud result URL alongside any downloaded file. Do not present task submission as completion. Do not replace a completed Cloud output with a locally recreated file from the original input, or infer permission for local recreation from a requested duration or quality target. If a returned asset's metadata or name conflicts with the requested constraints, probe that asset with an available metadata tool before claiming a mismatch or performing a correction. Corrective processing must consume the completed Cloud output, preserving its lineage; present the original Cloud result URL alongside any corrected output and explain any confirmed limitation. Never omit an available completed result URL in favor of an unfinished correction or a local fallback.

### Machine contract

These commands read live help/schema only and do not call Cloud:

```bash
byteplus-mediakit-cli shared query-task --help
byteplus-mediakit-cli shared query-task --schema
```

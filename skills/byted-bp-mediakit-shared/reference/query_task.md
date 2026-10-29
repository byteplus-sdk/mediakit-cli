# Query Task

## Capability

Query async task status and results

## Parameter rules

- task_id must come from a real async task submission result. Optional polling parameters may be filled only when the user specifies them, or when they can be determined accurately from user intent; do not invent values.
- max_poll_timeout_seconds defaults to 0 (unlimited); fill it only when the user explicitly needs to cap total polling time for one call. poll_interval_seconds × max_poll_attempts must not exceed this limit.

## Cloud

### Command and lifecycle

- Command: `byteplus-mediakit-cli shared query-task`
- Lifecycle: sync
- Return: returns the Cloud business result directly.

### Usage guide

- Boolean parameters (`--poll-complete`) must be written as `--flag=true` or `--flag=false`, or as bare `--flag` (equivalent to true). Do not pass a space-separated value such as `--flag true`, which the CLI treats as a positional argument.
- Omit boolean flags when they would repeat the default value.

### Example

```bash
MEDIAKIT_BYTEPLUS_SURFACE=skill MEDIAKIT_BYTEPLUS_RUNTIME=<runtime> \
  byteplus-mediakit-cli shared query-task \
  --task-id <task_id>
```

Replace placeholders with real user input only. Optional flags follow the parameter rules; do not invent URLs, files, enums, or business parameters.

### Parameters

| Path | CLI flag | Type | Required | Default | Enum/range | Description |
| --- | --- | --- | --- | --- | --- | --- |
| `max_poll_attempts` | `--max-poll-attempts` | integer | no | 0 | min: 0 | Maximum poll attempts; 0 means query once. |
| `max_poll_timeout_seconds` | `--max-poll-timeout-seconds` | number | no | 0 | min: 0 | Total polling timeout in seconds; 0 means unlimited. poll_interval_seconds × max_poll_attempts must not exceed this limit. |
| `poll_complete` | `--poll-complete` | boolean | no | false | - | Whether to keep polling until the task reaches a terminal state. |
| `poll_interval_seconds` | `--poll-interval-seconds` | number | no | 10 | greater than: 0 | Polling interval in seconds; must be greater than 0 and is used only during continuous polling. |
| `task_id` | `--task-id` | string | yes | - | - | Unique async task id used to query status and the terminal result. |

### Result

| Path | Type | Required | Mode | Description |
| --- | --- | --- | --- | --- |
| `error` | any | no | Cloud | Raw error content for a failed terminal state; present only when the task actually failed and the backend returned it. |
| `request_id` | string | no | Cloud | Request id; present only when the backend returns a non-empty value. |
| `status` | string | no | Cloud | Task status; completed is a successful terminal state, failed/canceled/cancelled are failure terminal states. |
| `success` | boolean | no | Cloud | Returns false for failure terminal states; other values appear only when returned by the backend. |
| `task_id` | string | no | Cloud | Unique async task id used to query status and the terminal result. |
| `task_type` | string | no | Cloud | Task type; present only when the backend returns a non-empty value. |
| `usage` | object | no | Cloud | Optional top-level field. Forwarded only for entitled accounts when a Cloud sync call succeeds or query-task reaches completed and the service returns billable usage; omitted for other states, async submission, and Local calls. |
| `usage.normalized_usage` | number | yes | Cloud | Normalized billable usage. Calculated server-side as BillingCount / fixed unit conversion × list_price, rounded to 6 decimal places; the client validates and forwards as-is without computing, inferring, or supplementing. |

### Machine contract

These commands read live help/schema only and do not call Cloud:

```bash
byteplus-mediakit-cli shared query-task --help
byteplus-mediakit-cli shared query-task --schema
```

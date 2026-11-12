# Retry or fallback

Classify an error, apply backoff, and retry the request.

## Examples

| Example | What you will learn | Source | Notebook |
|---|---|---|---|
| [Retrying transient errors](examples/transient_error_retry/) | Classify the error → back off → retry (no backup model) | [Official example](examples/transient_error_retry/src/) | [English walkthrough](examples/transient_error_retry/notebooks/retry_or_fallback-en.ipynb) |

The example implements retries. Model fallback is outside its scope.

Add new scenarios in separate example directories under this pattern. See the [contributing guide](../../docs/contributing.md) for the example layout.

[All patterns](../../README.md)

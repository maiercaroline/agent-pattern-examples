# Retry or fallback: transient_error_retry

[Run in Colab](notebooks/README.md#running-in-colab)

Configure a retry policy and backoff, then test the error path with simulated HTTP responses.

## Running the example

You need Python 3.10 or later and uv. Starting from the repository root, use the commands below to create a CLI environment inside this example. To use a shared environment, follow [environment setup](../../../../docs/setup.md) and run the example from its directory.

```bash
cd patterns/retry_or_fallback/examples/transient_error_retry
uv venv --python 3.12
uv pip install -r requirements.txt
```

### macOS / Linux

```bash
source .venv/bin/activate
export OPENAI_API_KEY="your_api_key_here"
python -m src.main
```

### Windows PowerShell

```powershell
.venv\Scripts\Activate.ps1
$env:OPENAI_API_KEY="your_api_key_here"
python -m src.main
```

The CLI reads the API key from the environment. Set it as shown above. Live calls incur API charges.

The CLI adapts the upstream workflow and defaults to `gpt-6-luna`. The notebook's `RUN_API` and `AGENT_MODEL` settings apply only to the notebook. Check the model configured in `src/` against your account's access.

## Notebook and CLI differences

The [English notebook](notebooks/retry_or_fallback-en.ipynb) walks through this pattern cell by cell in local Jupyter or Colab. It defaults to `RUN_API=False`. The `.env.local` file is optional. For live calls in Colab, the notebook reads `OPENAI_API_KEY` from Secrets if the environment has no key. See the [notebook instructions](notebooks/README.md). The CLI makes live calls when run with the environment configured.

Set `AGENT_MODEL` to choose the notebook model (default `gpt-6-luna`). The CLI uses the model settings in `src/` independently of this variable.

| Feature | CLI (`src/main.py`) | Notebook |
|---|---|---|
| Reproducing errors | Calls a real model and logs retry decisions when transient errors occur. | A mock HTTP transport returns 429, then success. Asserts exactly two requests, even with `RUN_API=False`. |
| Retry settings | Up to four retries, 0.5-second initial delay, 5-second maximum, with jitter. | Up to two retries, 0.1-second initial delay, 1-second maximum, without jitter. |
| Policy configuration | Includes `retry_after()` and decision logging. Configures both the agent and `RunConfig`. | Uses a simplified agent-level policy. Disables the OpenAI client's own retries in the local reproduction. |
| Live execution | Calls the model when run. | Runs a separate live model call when `RUN_API=True`. |

The standalone example near the end has its own `run_api=False` argument. Set it separately from the guided exercise's `RUN_API` switch.

## Included files

- [src/](src/) contains the entry point and all supporting Python modules in their required directory structure.
- [notebooks/](notebooks/) contains the English notebook and instructions for local Jupyter and Colab.
- `requirements.txt` lists dependencies installed from PyPI.
- `THIRD_PARTY_NOTICES.md` contains the upstream copyright notice and MIT license.
- `UPSTREAM.json` records the source and hashes of upstream files.

Original web tutorial (Korean): https://agent-pattern-examples.vercel.app/patterns/retry_or_fallback/

Keep the entire `src/` directory together and run the command above so helper modules are available to `src/main.py`.

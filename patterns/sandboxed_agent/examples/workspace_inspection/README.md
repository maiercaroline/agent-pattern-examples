# Sandboxed agent: workspace_inspection

[Run in Colab](notebooks/README.md#running-in-colab)

Read files with a shell tool in an isolated workspace and clean up resources when the run ends. The notebook supports Docker locally and a remote Modal Sandbox in Colab.

## Running the example

You need Python 3.10 or later and uv. Starting from the repository root, use the commands below to create a CLI environment inside this example. To use a shared environment, follow [environment setup](../../../../docs/setup.md) and run the example from its directory.

```bash
cd patterns/sandboxed_agent/examples/workspace_inspection
uv venv --python 3.12
uv pip install -r requirements.txt
```

### macOS / Linux

```bash
source .venv/bin/activate
export OPENAI_API_KEY="your_api_key_here"
python -m src.main --backend docker
```

### Windows PowerShell

```powershell
.venv\Scripts\Activate.ps1
$env:OPENAI_API_KEY="your_api_key_here"
python -m src.main --backend docker
```

The CLI reads the API key from the environment. Set it as shown above. Live calls incur API charges.

The CLI adapts the upstream workflow and defaults to `gpt-6-luna`. The notebook's `RUN_API` switch and `SANDBOX_MODEL` setting are independent of the CLI. Check your access to the model configured in the source.

Start the Docker daemon before running. The example creates the small files it needs. These commands use Docker. Modal requires additional dependencies and account setup.

## Notebook and CLI differences

The [English notebook](notebooks/sandboxed_agent-en.ipynb) walks through this pattern cell by cell in local Jupyter or Colab. It defaults to `RUN_API=False`. The `.env.local` file is optional. For live calls in Colab, the notebook reads `OPENAI_API_KEY` from Secrets if the environment has no key. See the [notebook instructions](notebooks/README.md). The CLI makes live calls when run with the environment configured.

Set `SANDBOX_MODEL` to choose the notebook's sandbox model (default `gpt-6-luna`). Use `--model` in the CLI.

| Feature | CLI (`src/main.py`) | Notebook |
|---|---|---|
| Backend and input | Choose Docker or Modal with `--backend`. Set the input and model with `--question` and `--model`. | Defaults to local Docker or remote Modal in Colab. Override with `SANDBOX_BACKEND`. Set `question` in the cell and the model with `SANDBOX_MODEL`. |
| Execution and output | Streams text and tool events with `Runner.run_streamed()`. | Displays the response after `await Runner.run()` completes and checks for a tool call. |
| Code organization | Imports the shell helper from `src/workspace_shell.py`. | Defines the equivalent class directly in a cell. |
| Resources | Creates a sandbox when the CLI runs. | Skips sandbox creation and API calls with `RUN_API=False`. Attempts to delete the session after a live run. Modal in Colab requires separate tokens and may incur charges. |

The standalone example near the end has its own `run_api=False` argument. Set it separately from the guided exercise's `RUN_API` switch.

## Included files

- [src/](src/) contains the entry point and all supporting Python modules in their required directory structure.
- [notebooks/](notebooks/) contains the English notebook and instructions for local Jupyter and Colab.
- `requirements.txt` lists dependencies installed from PyPI.
- `THIRD_PARTY_NOTICES.md` contains the upstream copyright notice and MIT license.
- `UPSTREAM.json` records the source and hashes of upstream files.

Original web tutorial (Korean): https://agent-pattern-examples.vercel.app/patterns/sandboxed_agent/

Keep the entire `src/` directory together and run the command above so helper modules are available to `src/main.py`.

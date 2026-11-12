# Workflow planning: research_report

[Run in Colab](notebooks/README.md#running-in-colab)

Break a research question into a search plan and combine the collected information into one report.

## Running the example

You need Python 3.10 or later and uv. Starting from the repository root, use the commands below to create a CLI environment inside this example. To use a shared environment, follow [environment setup](../../../../docs/setup.md) and run the example from its directory.

```bash
cd patterns/workflow_planning/examples/research_report
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

The Python source preserves the upstream workflow and model settings. The notebook's `RUN_API` and `AGENT_MODEL` settings apply only to the notebook. Check the model configured in `src/` against your account's access.

## Notebook and CLI differences

The [English notebook](notebooks/workflow_planning-en.ipynb) walks through this pattern cell by cell in local Jupyter or Colab. It defaults to `RUN_API=False`. The `.env.local` file is optional. For live calls in Colab, the notebook reads `OPENAI_API_KEY` from Secrets if the environment has no key. See the [notebook instructions](notebooks/README.md). The CLI makes live calls when run with the environment configured.

Set `AGENT_MODEL` to choose the notebook model (default `gpt-5.6-luna`). The CLI uses the model settings in `src/` independently of this variable.

| Feature | CLI (`src/main.py`) | Notebook |
|---|---|---|
| Input and structure | Reads a terminal question and divides responsibilities among `manager.py`, `agents/`, and `printer.py`. | Edit `query` in a cell and inspect the agent definitions and planning, search, and writing stages. |
| Search scope | Asks the planner for 5–20 search terms. | Requests two searches. The schema accepts one or two. |
| Report | Asks the writer for 5–10 pages and at least 1,000 words. | Asks for a short report under 400 words. |
| Execution and display | Writes the report with `Runner.run_streamed()` and shows progress with Rich. | Awaits each stage with `Runner.run()`, runs searches in parallel, and displays the report in Markdown. |

The standalone example near the end has its own `run_api=False` argument. Set it separately from the guided exercise's `RUN_API` switch.

## Included files

- [src/](src/) contains the entry point and all supporting Python modules in their required directory structure.
- [notebooks/](notebooks/) contains the English notebook and instructions for local Jupyter and Colab.
- `requirements.txt` lists dependencies installed from PyPI.
- `THIRD_PARTY_NOTICES.md` contains the upstream copyright notice and MIT license.
- `UPSTREAM.json` records the source and hashes of upstream files.

Original web tutorial (Korean): https://agent-pattern-examples.vercel.app/patterns/workflow_planning/

Keep the entire `src/` directory together and run the command above so helper modules are available to `src/main.py`.

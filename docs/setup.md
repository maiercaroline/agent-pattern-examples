# Environment setup

## Virtual environment with uv

Use Python 3.12 and uv. From the repository root, run the following commands.

```bash
uv venv .venv --python 3.12
uv pip install --python .venv/bin/python -r requirements-notebooks.txt
source .venv/bin/activate
python -m ipykernel install --prefix "$VIRTUAL_ENV" --name agent-pattern-examples --display-name "Agent Pattern Examples"
python -m jupyterlab
```

On Windows, use `uv pip install --python .venv/Scripts/python.exe ...` and `.venv\Scripts\Activate.ps1`. The installation above includes Jupyter, validation tools, and shared SDK dependencies. For CLI examples alone, install `requirements.txt`. The SDK comes from PyPI as `openai-agents[docker]==0.22.1`.

Local notebooks use `find_dotenv(".env.local", usecwd=True)` to search the working directory and its parents for an optional `.env.local`. Existing environment variables take precedence. Offline runs require neither the file nor an API key. Keep keys out of notebooks and outputs. Set the environment variable explicitly before running the CLI, which does not load environment files automatically.

If your environment uses a SOCKS proxy and reports a missing `socksio` dependency, install it in the virtual environment.

```bash
uv pip install --python .venv/bin/python "httpx[socks]"
```

## Running the source examples

Each example has its own `src/` package. Run it from its example directory. For instance, from the repository root:

```bash
cd patterns/triage_handoff/examples/language_routing
python -m src.main
```

See each example README for its command.

## Running and validating notebooks

Open an example's `notebooks/<pattern>-en.ipynb` and select the `Agent Pattern Examples` kernel. Initialization works from the notebook directory or the repository root.

- The default `RUN_API=False` builds agents and runs offline checks.
- Set `RUN_API=True` in the setup cell to enable model calls in the guided exercises. API and web search calls may incur charges.
- To run the standalone example near the end, separately change `run_api=False` to `True` in its invocation cell. The earlier `RUN_API` switch is independent.
- `AGENT_MODEL` overrides the default `gpt-6-luna`. The sandbox has a separate `SANDBOX_MODEL` setting that defaults to `gpt-6-luna`.
- Notebooks use `await Runner.run(...)` in Jupyter's existing event loop.
- Clear all cell outputs and execution counts before committing.

The following commands validate cells in execution order in a real Jupyter kernel and preserve the notebook files on disk.

```bash
python scripts/check_notebooks.py
python scripts/check_notebooks.py --live
python scripts/check_notebooks.py --live --example language_routing
```

Use `--live` for actual API calls in the guided exercises. Standalone examples keep `run_api=False`. A full run with local defaults also requires Docker for the sandbox. Results go to `.cache/notebook-validation.json`. Failure returns exit code 1. See the [validation record](notebook-validation.md).

## Running in Colab

All six notebooks support Colab. To open an English notebook from this clone, use **File → Upload notebook** in [Colab](https://colab.research.google.com/) and choose the corresponding `-en.ipynb` file. Direct GitHub launch links require a published branch containing the English files.

The first setup cell installs packages only in Colab. Most notebooks use `openai-agents==0.22.1`. The sandbox uses `openai-agents[modal]==0.22.1`. Both also install `python-dotenv`. You can run offline without a checkout, Drive mount, API key, or `.env.local`. For live execution, add `OPENAI_API_KEY` to Colab Secrets, grant notebook access, and set `RUN_API=True`. An existing environment variable takes precedence. Your local `.env.local` file is not transferred to Colab.

The sandbox uses remote Modal in Colab. Add `MODAL_TOKEN_ID` and `MODAL_TOKEN_SECRET` to Secrets for live runs. Modal requires an account and may charge separately. Offline runs require no tokens. See the [sandbox notebook guide](../patterns/sandboxed_agent/examples/workspace_inspection/notebooks/README.md).

## Sandbox example

The notebook defaults to Docker locally and Modal in Colab. Override with `SANDBOX_BACKEND=docker` or `SANDBOX_BACKEND=modal`. Live execution requires the selected backend's packages, credentials, and runtime. The example README documents the separate CLI options.

The Docker backend requires a running Docker daemon. The shared dependencies include the SDK's Docker extra.

On macOS, if Docker Desktop's credential helper is installed but missing from PATH, add the path below and restart Jupyter from that terminal.

```bash
export PATH="/Applications/Docker.app/Contents/Resources/bin:$PATH"
```

This path applies to Docker Desktop on macOS. With Colima or another environment, check your Docker configuration and credential helper installation.

To use Modal locally, install the additional package in the active virtual environment and configure authentication.

```bash
uv pip install --python .venv/bin/python "openai-agents[modal]==0.22.1"
python -m modal setup
export SANDBOX_BACKEND=modal
```

You can use an existing Modal profile or the `MODAL_TOKEN_ID` and `MODAL_TOKEN_SECRET` environment variables. Colab reads the tokens from Secrets when the environment variables are absent. `MODAL_APP_NAME` defaults to `agent-pattern-examples`. The sandbox timeout is 300 seconds. Cleanup attempts to delete the session. After a forced stop, check its status in the Modal dashboard. Modal and model API usage are billed separately.

## Validation scope

The original project statically checked CLI source hashes, Python syntax, and local imports. The [validation record](notebook-validation.md) describes notebook checks and results.

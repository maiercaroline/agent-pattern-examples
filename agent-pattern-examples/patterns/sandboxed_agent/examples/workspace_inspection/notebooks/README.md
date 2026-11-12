# workspace_inspection — Notebook

Follow [sandboxed_agent-en.ipynb](sandboxed_agent-en.ipynb) to define agents, run the workflow, inspect results, and try a short exercise.

## Running locally

1. Follow the [uv setup guide](../../../../../docs/setup.md) to prepare the kernel.
2. Open `sandboxed_agent-en.ipynb` and select the `Agent Pattern Examples` kernel.
3. The default skips API calls. To run a live exercise, set `RUN_API=True` in the setup cell.
4. Choose Restart & Run All to execute the notebook from top to bottom. Existing environment variables take precedence over the optional `.env.local` file. Offline execution requires neither a key nor that file.

## Running in Colab

1. Open [Colab](https://colab.research.google.com/), choose **File → Upload notebook**, and select the English notebook from this directory. You can run it without cloning the repository into Colab or mounting Drive.
2. Run the first setup cell. It installs the Colab dependencies, including `openai-agents[modal]==0.22.1`. A CPU runtime is sufficient. Restart the runtime first if you already imported another SDK version.
3. Keep `RUN_API=False` for offline exercises. For live execution, add your existing key to Colab Secrets as `OPENAI_API_KEY`, grant notebook access, and set `RUN_API=True`.

The notebook looks for a key in the environment, then an optional `.env.local` file, then Colab Secrets. It reads Secrets only for live execution. Local key files are not transferred to Colab. Key values are never printed or saved.

Set the sandbox model with `SANDBOX_MODEL` or in the setup cell. To run the standalone example, separately set `run_api=True` in its invocation cell. Live model calls may incur charges.

Colab Secrets access and error handling follow the [official implementation](https://github.com/googlecolab/colabtools/blob/main/google/colab/userdata.py).

[Source code](../src/main.py) · [Example guide](../README.md) · [Validation results](../../../../../docs/notebook-validation.md)

`sandboxed_agent-en.ipynb` includes the walkthrough, exercises, and standalone example. Add further exercises in its **Try it yourself** section.

## Sandbox backends

A sandbox backend creates an isolated workspace for the agent and executes commands inside it. **Modal** is a cloud compute service. A **Modal Sandbox** is an isolated container created on demand to work with files or run shell commands. See the [Modal Sandbox guide](https://modal.com/docs/guide/sandboxes).

Colab runs the notebook and agent workflow. Modal provides the remote workspace where the shell tool inspects files. The notebook creates the workspace, prepares the files, and uses the tool results to answer the question. Colab therefore needs no local Docker daemon. Configure Modal credentials for the workspace and OpenAI credentials for model calls.

The defaults are local Docker and remote Modal in Colab. To run live in Colab, create a Modal account and API token, add `MODAL_TOKEN_ID` and `MODAL_TOKEN_SECRET` to Colab Secrets, and grant notebook access. You also need `OPENAI_API_KEY`. See [Modal authentication](https://modal.com/docs/reference/modal.config).

With `RUN_API=False`, the notebook skips token lookup and sandbox creation. Live Modal usage may incur separate charges. The notebook attempts session deletion in `finally` and sets a 300-second timeout. After a forced stop, check the Modal dashboard for remaining resources.

Set `SANDBOX_BACKEND` to `docker` or `modal`. For local Modal execution, install `openai-agents[modal]==0.22.1` and configure Modal authentication. Set the model with `SANDBOX_MODEL` (default `gpt-5.6-sol`).

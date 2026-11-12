# story_refinement — Notebook

Follow [evaluator_optimiser-en.ipynb](evaluator_optimiser-en.ipynb) to define agents, run the workflow, inspect results, and try a short exercise.

## Running locally

1. Follow the [uv setup guide](../../../../../docs/setup.md) to prepare the kernel.
2. Open `evaluator_optimiser-en.ipynb` and select the `Agent Pattern Examples` kernel.
3. The default skips API calls. To run a live exercise, set `RUN_API=True` in the setup cell.
4. Choose Restart & Run All to execute the notebook from top to bottom. Existing environment variables take precedence over the optional `.env.local` file. Offline execution requires neither a key nor that file.

## Running in Colab

1. Open [Colab](https://colab.research.google.com/), choose **File → Upload notebook**, and select the English notebook from this directory. You can run it without cloning the repository into Colab or mounting Drive.
2. Run the first setup cell. It installs the dependencies needed in Colab and works with a CPU runtime. Restart the runtime first if you have already imported a different SDK version.
3. Keep `RUN_API=False` for offline exercises. For live execution, add your existing key to Colab Secrets as `OPENAI_API_KEY`, grant notebook access, and set `RUN_API=True`.

The notebook looks for a key in the environment, then an optional `.env.local` file, then Colab Secrets. It reads Secrets only for live execution. Local key files are not transferred to Colab. Key values are never printed or saved.

Set the model with `AGENT_MODEL` or in the setup cell. For the standalone example near the end, separately set `run_api=True` in its invocation cell. Live model calls may incur charges.

Colab Secrets access and error handling follow the [official implementation](https://github.com/googlecolab/colabtools/blob/main/google/colab/userdata.py).

[Source code](../src/main.py) · [Example guide](../README.md) · [Validation results](../../../../../docs/notebook-validation.md)

`evaluator_optimiser-en.ipynb` includes the walkthrough, exercises, and standalone example. Add further exercises in its **Try it yourself** section.

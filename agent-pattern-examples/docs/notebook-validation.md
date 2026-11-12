# Notebook validation record

## Upstream offline validation

- Recorded at 09:38:11 KST on September 12, 2026 (`2026-09-12T00:38:11.564822+00:00` in JSON).
- Command: `python scripts/check_notebooks.py` without `--live`.
- Environment: temporary virtual environment created with `python3 -m venv` and populated from `requirements-notebooks.txt`.
- Python 3.14.3, openai-agents 0.22.1, openai 3.13.0
- nbclient 0.11.0, nbformat 5.11.1, ipykernel 7.3.0, python-dotenv 1.2.3
- Working directory: each example's `notebooks/` directory, with a separate Jupyter kernel per notebook.
- Result: all six notebooks and 39 code cells passed, with zero errors and exit code `0`.
- Output: `.cache/notebook-validation.json`, generated locally and excluded from Git.

The setup guide recommends Python 3.12. This run used Python 3.14.3. The notebooks read `.env.local` and skipped live API calls and Docker or Modal operations. The run did not measure API responses, costs, or model availability.

| Example | Code cells executed | Offline run | Live API run in this check |
|---|---|---|---|
| `story_refinement` | 6/6 | Passed | Not run |
| `translation_candidates` | 6/6 | Passed | Not run |
| `transient_error_retry` | 6/6 | Passed | Not run |
| `workspace_inspection` | 8/8 | Passed | Not run |
| `language_routing` | 6/6 | Passed | Not run |
| `research_report` | 7/7 | Passed | Not run |

## Coverage

- Checked nbformat structure for six notebooks and syntax for 39 code cells, allowing top-level `await`.
- Checked that published notebooks have empty outputs, cleared execution counts, and `RUN_API=False`.
- Scanned for strings resembling `sk-` API keys. The scan covers only that pattern.
- Verified recorded CLI source hashes and checked sources for changes before and after execution.
- Ran each notebook's cells in order and verified the source notebook was preserved.
- Exercised a mock HTTP 429 followed by success and asserted exactly two attempts.
- Used `RUN_API=False` for guided exercises and `run_api=False` for standalone examples.

Four kernel integration tests also passed. Using test notebooks without API calls, they covered mode switching, working directory and top-level await, source preservation, continuing after failure, kernel isolation, omission of raw error details, example selection, and blocking execution when static checks fail.

## Colab compatibility checks

All 16 tests passed on September 12, 2026. In addition to the four kernel integration tests, the suite included the following.

- Eight tests in `tests/test_workflow_colab.py` checked the Colab installation branch and environment loading across notebooks. They covered existing key precedence, optional environment files, skipping Secrets offline, missing keys, and Secrets access errors.
- Four tests in `tests/test_sandbox_colab.py` checked backend defaults and invalid selections, Modal authentication, options and session handling with a mock client, and cleanup across Docker error paths. With Modal 1.4.3 installed, the optional test also passed.

All six notebooks also passed in local Jupyter kernels with `google.colab` and Secrets mocked and API and Modal environment variables and dotenv loading disabled. This separate script is outside the repository. Its results and the unit test results are separate from `.cache/notebook-validation.json`.

Hosted Colab package installation and Secrets access, real Modal session creation, and model API calls were outside this validation run.

## Historical live API validation

Earlier documentation records successful live API execution for all six examples. These historical results predate the offline run above. The earlier count of 33 code cells refers to the notebooks as they existed then.

The recorded environment used Python 3.12.13, openai-agents 0.22.1, openai 3.10.0, nbclient 0.11.0, nbformat 5.11.1, and ipykernel 7.3.0. The sandbox used Docker 29.5.2 and `python:3.14-slim`. The earlier notes also record an offline rerun after the `.env.local` discovery change on September 9, 2026. The current offline JSON does not record the exact time or model of the live runs.

The earlier live checks covered the following.

- **Language routing** — observed a handoff event and confirmed the French specialist handled the final response.
- **Parallel execution** — generated three translation candidates and checked the selection.
- **Retry** — reproduced HTTP 429 followed by success in exactly two attempts with a local mock transport. A separate live call returned a normal model response.
- **Evaluation and refinement** — checked structured feedback and termination on passing or reaching the round limit. A run can pass on the first round.
- **Research** — generated a plan, ran two web searches, and produced a report. Source accuracy was outside the evaluation.
- **Sandbox** — observed a tool call and final response in a Docker workspace. The notebook removed the session in its `finally` path.

The verification environment needed `socksio` for a SOCKS proxy. The sandbox was rechecked after adding the Docker credential helper to PATH. See the [setup guide](setup.md) for environment-specific fixes.

The checks did not establish CLI–notebook output equivalence, benchmark cost or speed, or exercise every error path.

## Rerunning validation

Prepare the [environment](setup.md), then run the following from the repository root.

```bash
python scripts/check_notebooks.py
python scripts/check_notebooks.py --example language_routing
python scripts/check_notebooks.py --live
```

Use `--live` to enable API calls in the guided exercises. A full run also uses the selected sandbox backend. Locally the default is Docker. With `SANDBOX_BACKEND=modal`, install Modal support and configure authentication. Colab defaults to Modal. Standalone examples keep `run_api=False`. Live API and Modal usage may incur fees.

Each run overwrites `.cache/notebook-validation.json` and preserves the published notebooks. Failures return exit code `1`. Update this document manually when recording a new result, including the execution mode and environment.

Run the integration, Colab, and sandbox tests without external API calls.

```bash
python -m unittest discover -s tests -v
```

For the optional Modal adapter test, run `python -m pip install "openai-agents[modal]==0.22.1"` in the active environment. The test skips when Modal is unavailable and requires no Modal credentials.

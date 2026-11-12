# Adding examples and notebooks

## Directory conventions

Add examples under `patterns/<pattern>/examples/<example>/` with `src/main.py` as the entry point. Use a descriptive `snake_case` directory name.

```text
<example>/
├── README.md
├── src/
│   ├── __init__.py
│   └── main.py
├── notebooks/
├── data/          # Optional input data
└── outputs/       # Generated results, excluded from Git
```

Follow the [code organization guide](code-organization.md) for filenames and module boundaries. Avoid nested copies of the upstream hierarchy and empty directories for planned examples.

## README

- **Pattern README** — explain the concept, list examples and learning objectives, and indicate notebook availability.
- **Example README** — describe the scenario, commands, inputs, outputs, notebooks, provenance, and implementation scope.
- Update the pattern README when adding an example.
- For published notebooks, include Colab links in the pattern listing and at the top of the example README and notebook. Use a published GitHub branch and update links after merges or moves.

## Notebook

Name the notebook `notebooks/<pattern>-en.ipynb`. Each example currently combines a walkthrough, exercises, and a standalone implementation in one notebook.

A suggested sequence is learning objectives, environment check, input and model settings, tools, agents, execution, interpreting results, exercises, and sources. Define the core agent code in cells so learners can edit and experiment.

- Support local execution from the notebook directory and repository root. Keep core code in cells so Colab can run without a checkout or Drive mount. Document any external data and paths.
- If you import the same example's `src.*`, explain local import paths and how to make the source available in Colab. Use a fresh kernel for each example to avoid package-name collisions.
- Install local dependencies in the shared environment. In Colab, detect the runtime in the first setup cell and install only required packages. The pinned SDK is `0.22.1`, with the `modal` extra for sandbox use in Colab.
- With `RUN_API=False` and standalone `run_api=False`, the notebook must work without keys or `.env.local`. For live calls, check environment variables first, then optional `.env.local`, then Colab Secrets.
- Default the sandbox to local Docker or Modal in Colab. Follow [environment setup](setup.md) for backend selection, authentication, and costs.
- Identify cells that make model calls and may incur charges. Keep keys out of notebook inputs, outputs, and metadata.
- Use Restart & Run All to verify execution order. Clear outputs and execution counts before committing. Record any execution you could not verify.
- Include the upstream link and describe adaptations in instructional notebooks.

## Dependencies and data

Manage shared dependencies in the root `requirements.txt` and notebook tooling in `requirements-notebooks.txt`. If an example needs additional packages, provide its own `requirements.txt` and document installation after the shared environment is ready.

Keep examples independent of one another's code. Put only small, shareable inputs in `data/`. Save generated results in the Git-ignored `outputs/` directory.

## Provenance and verification

For unmodified copies, record the local path, upstream path, and SHA-256 in [UPSTREAM.json](../UPSTREAM.json). For a move, update only the local path. When changing the upstream commit, verify the SDK dependency and the full upstream file inventory.

For modified upstream files, record `modified: true`, `upstream_sha256`, `local_sha256`, and `changes` in `UPSTREAM.json`. Keep `sha256` for unmodified files. Record the commit and changes in the example README and preserve the copyright notice in [THIRD_PARTY_NOTICES.md](../THIRD_PARTY_NOTICES.md).

Check relative README links, Colab links, working directories, and local imports. Run `python scripts/check_notebooks.py` offline and run the relevant tests. Record local runs, simulated Colab tests, and actual Colab or API calls separately in the [validation record](notebook-validation.md).

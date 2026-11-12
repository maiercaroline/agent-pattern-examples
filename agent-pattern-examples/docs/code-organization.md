# Example code organization and naming

## Purpose and scope

These project conventions make entry points easy to find and keep each scenario's code and notebook together. Python and the OpenAI Agents SDK do not require these filenames.

Place each example in `patterns/<pattern>/examples/<example>/`, with code in `src/` and notebooks in `notebooks/`. All six existing examples use this layout.

## Directory structure

```text
patterns/
└── evaluator_optimiser/
    ├── README.md
    └── examples/
        └── story_refinement/
            ├── README.md
            ├── src/
            │   └── main.py
            └── notebooks/
                └── evaluator_optimiser-en.ipynb
```

The notebook names above refer to existing examples. Add files only when implemented.

- The pattern directory identifies the architecture. The example directory identifies a concrete scenario.
- Use a single `examples/` directory immediately below each pattern to group its scenarios.
- Within a scenario, use `src/` for source code and `notebooks/` for notebooks.
- Add `data/`, `outputs/`, or an example-specific `requirements.txt` only when needed.
- Avoid nesting the upstream `examples/agent_patterns/` hierarchy inside an example.

## Use main.py as the entry point

Use `main.py` for the executable entry point. A directory name such as `story_refinement` already identifies the scenario.

For example, move an adapted `llm_as_a_judge.py` to `story_refinement/src/main.py`. Record the original filename and path in the provenance manifest.

Use `main.py` to collect input, call the workflow, and display results. Start execution inside `if __name__ == "__main__":` so importing the module does not prompt for input or call the API.

## Start small examples in one file

```text
src/
└── main.py
```

Keep agent definitions, simple tools, and control flow together when they are easy to follow in one file. There is no line-count threshold for splitting a module.

## Split complex examples by responsibility

Split modules as agents and tools grow or when several entry points need the same definition.

```text
src/
├── __init__.py
├── main.py
├── agents.py
├── tools.py
└── workflow.py
```

| File | Responsibility | When to use it |
|---|---|---|
| `main.py` | Input, workflow invocation, and output | Always the default entry point |
| `agents.py` | Agent configuration and factories | When definitions obscure the workflow or need reuse |
| `tools.py` | Tool functions and configuration | When there are multiple tools or substantial implementations |
| `workflow.py` | Planning, delegation, concurrency, and evaluation loops | When the control flow warrants independent study or reuse |
| `schemas.py` | Structured input and output types | When multiple modules share types |

Create only the modules you need. Give helpers descriptive names such as `printer.py` or `report_writer.py`. To distinguish a local `agents.py` module from the SDK's `agents` package, use explicit relative imports such as `from .agents import ...`.

## Naming conventions

- Use `snake_case` for directories and Python modules.
- Name examples for scenarios, such as `story_refinement` or `language_routing`, in preference to numbered names like `example_01`.
- Reserve `main.py` for the default entry point. Give other modules names that describe their role.
- Name English notebooks `notebooks/<pattern>-en.ipynb`, for example `evaluator_optimiser-en.ipynb` or `workflow_planning-en.ipynb`.
- Add automation helpers when there is a use for them.

## Running examples and resolving imports

Use the individual example directory as the working directory. Run the following from the repository root.

```bash
cd patterns/evaluator_optimiser/examples/story_refinement
python -m src.main
```

When splitting modules into a package, add `src/__init__.py` and use relative imports internally. Document execution with `python -m`. Keep each example independent of other examples' `src` packages.

Put run commands in the example README. Update their working directories whenever files move.

## Relationship between notebooks and source code

Explain core agent definitions and control flow cell by cell. Let learners edit instructions, tools, and inputs. Shared tools and other reusable pieces may be imported from the same example.

When importing source code, specify the working directory and import path in the first setup cell. Start a fresh kernel when switching examples because each has a package called `src`. Document differences in CLI and notebook inputs or exercise steps in the example README.

## When renaming or moving files

1. Update entry points and internal imports.
2. Update code links and run commands in the root, pattern, example, and notebook READMEs. For published branches, update Colab links in READMEs and notebooks to match the branch and path. Switch to the default branch links after merging.
3. Update notebook source references and relative data paths.
4. Update `local` paths in [UPSTREAM.json](../UPSTREAM.json). Preserve the upstream repository, commit, and paths.
5. If content changed, record `modified: true`, `upstream_sha256`, `local_sha256`, and `changes`. Keep existing content hashes for moves or renames alone.
6. Preserve the pinned commit and [THIRD_PARTY_NOTICES.md](../THIRD_PARTY_NOTICES.md). Describe adaptations in the example README.
7. Check Python syntax, local imports, documentation links, and working directories. Record separately whether you ran live API calls.

See the [contributing guide](contributing.md) and [environment setup](setup.md).

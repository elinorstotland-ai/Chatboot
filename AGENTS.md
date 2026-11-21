# Repository instructions

## Project files

- For every new or materially changed code feature, create or update a Google Colab-compatible Jupyter notebook (`.ipynb`) that demonstrates or runs the code. Store notebooks in `notebooks/` and keep them usable in Google Colab.
- Keep reusable implementation code in normal project source files; use the notebook as a runnable example or workflow.

## Git workflow

- After adding or changing code, create a Git commit with a clear, concise message and push it to the configured `origin` remote.
- Include the related notebook and documentation changes in the same commit as the code they describe.
- Never commit secrets, credentials, or generated environment files.
- If a commit or push cannot complete, explain the concrete blocker and report which changes remain unpushed.

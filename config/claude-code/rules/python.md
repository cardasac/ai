# Python

- Always use uv for projects, dependencies, virtual environments, Python versions and running code. Never use pip, venv or other tools for this.
- Create projects with `uv init`, add dependencies with `uv add` (`uv add --dev` for dev dependencies), remove them with `uv remove`, sync with `uv sync`, and run scripts and commands with `uv run`.
- Manage Python versions with `uv python install` and `uv python pin`.
- Run one-off tools with `uvx`.
- Always use Ruff for linting and formatting. Add it with `uv add --dev ruff`, then run `uv run ruff check` and `uv run ruff format`.

# About Me
I'm an economist and data scientist. My work involves conventional data science projects,
occasionally including big data pipelines and heavy-duty Bayesian MCMC. I do not do web
development. I never use STATA.

# General Principles
- Code should be well-commented, computationally efficient, reproducible, and as simple
  as possible but no simpler
- Follow best practices in whatever language/framework is in use
- Add new libraries or packages only when genuinely necessary — prefer solutions using
  what's already available
- Before starting any non-trivial task, briefly state your plan and wait for confirmation
- Never push to git without explicit instruction
- Never delete files without explicit instruction

# Languages
My primary language is R. I am still learning Python and defer to your judgment on Python
best practices.

**Prefer R** for data manipulation, cleaning, analysis, and visualization.
**Use Python** when R is clearly unsuitable (e.g., certain ML frameworks, tasks where the
Python ecosystem is dominant).

# R Preferences
- Use Tidyverse by default except where clearly unsuitable
- For large datasets requiring a SQL backend, prefer solutions that allow Tidyverse-like
  syntax (e.g., dbplyr, duckdb with dplyr bindings, arrow)
- Use `here()` for file paths to ensure reproducibility
- Prefer `purrr` for iteration over base `apply` functions
- For Bayesian MCMC, use Stan via `cmdstanr` unless there's a strong reason otherwise;
  prefer `brms` for models it can handle
- Write code that is readable first, clever second

# Python Preferences
- Follow current Python best practices and community conventions
- Use `uv` for package management: `uv init` to set up a project, `uv add <package>` to
  add dependencies
- Prefer well-established libraries (numpy, pandas, scikit-learn, etc.) over newer
  alternatives unless there's a clear reason
- Write clean, typed code where appropriate

# Project Structure
- For simple data cleaning and analysis projects: house everything in a single `.qmd` file
- For more complex projects: separate data cleaning and analysis scripts that are sourced
  from the `.qmd`
- Use Quarto (`.qmd`) for all documents, reports, and presentations
- Quarto output targets include LaTeX articles, Beamer presentations, and HTML documents

# Reproducibility
- Scripts should be runnable from top to bottom without manual intervention
- Set random seeds wherever randomness is involved
- Prefer explicit over implicit (e.g., name function arguments, don't rely on positional
  defaults for non-obvious args)
- Document data sources and any manual steps that cannot be scripted

# Development Environment
I work inside a Docker dev container managed by VSCode Dev Containers. This is a sandboxed
Linux environment — do not assume access to anything outside the container. All project
repos live under `/root/`. Data that shouldn't go to GitHub lives at `/data/`.

The container runs persistently via a Docker volume, so installed packages and files under
`/root/` survive container restarts and rebuilds.

# Python Package Management
I use `uv` for Python package management. For each project:
- `uv init` to initialize a project environment
- `uv add <package>` to add dependencies
- Dependencies are recorded in `pyproject.toml`, which should be committed to git
- Do not use pip directly; use uv

# R Package Management
R packages are installed globally in the container and persist via the Docker volume.
For projects requiring strict reproducibility or collaboration, use `renv`:
- `renv::init()` to initialize
- `renv::snapshot()` to record current state
- `renv::restore()` to reinstall from snapshot
- Commit `renv.lock` to git
For solo projects, renv is optional.

# Version Control
- Never push to git without explicit instruction
- Never delete files without explicit instruction
- Commit `pyproject.toml` and `renv.lock` (if used) to git for reproducibility
- Data files that shouldn't go to GitHub live in `/data/` and should be added to
  `.gitignore`

# Agentic Workflow
- For any non-trivial task, write a brief plan first and wait for confirmation before
  executing. A few sentences is enough — just enough to catch misunderstandings early.
- After writing code, run it. Read the output. If there are errors or unexpected results,
  fix and run again. Do not consider a task complete until you have seen it execute
  successfully and verified the output looks correct.
- When the output is a data analysis or statistical result, do a basic sanity check:
  do the numbers make sense, are sample sizes what you'd expect, are there obvious signs
  of data error?
- After completing a task, briefly identify any assumptions made or potential failure
  modes — especially around missing data, data types, or edge cases.
- If a task has multiple distinct components, handle them sequentially with confirmation
  between steps rather than doing everything at once.

# Token Efficiency
- Be concise. Do not narrate what you are about to do — just do it.
- Do not explain code line by line unless asked. Comments in the code itself are
  sufficient.
- Read only the files directly relevant to the current task. Do not speculatively read
  the entire codebase.
- If a task is ambiguous, ask one focused clarifying question before starting rather than
  making assumptions and proceeding — or making assumptions and then listing them all
  afterward.
- Prefer targeted validation (run the code, check a specific condition) over broad review
  passes.
- Use the least capable model sufficient for the task. Routine coding and data
  manipulation does not require extended thinking or the most powerful model.

# Lint Standard

## Objective

Keep a single, automatically enforced code style across both submodules, so that
code review discusses behaviour instead of formatting, and so that no
"style-only" diff noise is mixed into feature commits.

Every rule described here is checked automatically on every pull request. Nothing
in this document has to be remembered by hand: the tooling reports the problem and,
in most cases, fixes it for you.

## Automated Checks

Each submodule repository runs its own workflows. They are triggered on pull
requests to `main` and `develop`, and on pushes to those branches.

| Repository | Workflow | What it verifies |
|---|---|---|
| frontend | `Lint` | ESLint (error rules) and Prettier (formatting) |
| frontend | `Build` | `npm ci` and `npm run build` |
| frontend | `Commit Lint` | Commit messages, see [Commit Standards](./Commit%20Standards.md) |
| backend | `Lint` | Ruff check (error rules) and Ruff format (formatting) |
| backend | `Build` | Docker image build, `compileall` and `manage.py check` |
| backend | `Commit Lint` | Commit messages, see [Commit Standards](./Commit%20Standards.md) |

A pull request cannot be merged while any of these checks is failing.

## Separation of Responsibilities

Both submodules follow the same principle:

- **The linter finds bugs.** Unused variables, undefined names, unreachable code,
  unsorted imports, framework-specific mistakes.
- **The formatter owns the layout.** Indentation, quotes, semicolons, line breaks,
  trailing commas.

The two never overlap. In the frontend, `eslint-config-prettier` is the last entry
in `eslint.config.js` and explicitly disables every stylistic ESLint rule
(`semi`, `quotes`, `indent`, `comma-dangle` and others are set to `off`). In the
backend, Ruff already separates `ruff check` from `ruff format`.

This has one practical consequence worth knowing: **a formatting mistake is never
reported as an editor error.** A missing semicolon or a wrong indent produces no
red squiggle — it is silently corrected when you save the file, or reported by the
CI as a formatting failure. Only real defects are underlined in the editor.

## Frontend — ESLint and Prettier

### ESLint

Configured in `eslint.config.js` using the flat config format. Three layers:

| Layer | Purpose |
|---|---|
| `@eslint/js` — `recommended` | Core JavaScript error rules: `no-undef`, `no-unused-vars`, `no-useless-assignment`, and similar |
| `eslint-plugin-vue` — `flat/recommended` | Vue 3 rules: template syntax, incorrect directive use, component and attribute order |
| `eslint-config-prettier` | Disables every stylistic rule so ESLint and Prettier do not conflict |

Two scope-specific adjustments:

- `src/**` runs with browser globals; `*.config.js` runs with Node globals. Without
  this split, `window` would be undefined in application code and `process` would be
  undefined in `vite.config.js`.
- `vue/multi-word-component-names` is disabled. `Button.vue` and `Navbar.vue` are
  single-word names. The rule exists to avoid collisions with native HTML tags;
  since these components are always used in PascalCase (`<Button>`, `<Navbar>`),
  Vue resolves them without ambiguity. If they are ever renamed to `AppButton` and
  `AppNavbar`, the rule should be turned back on.

### Prettier

Configured in `.prettierrc.json`:

| Option | Value | Meaning |
|---|---|---|
| `semi` | `false` | No semicolons at the end of statements |
| `singleQuote` | `true` | `'text'` instead of `"text"` |
| `printWidth` | `100` | Maximum line length before wrapping |
| `trailingComma` | `all` | Trailing comma in multi-line lists and arguments |
| `arrowParens` | `always` | `(item) => ...` instead of `item => ...` |
| `endOfLine` | `lf` | Line endings always LF |

`.prettierignore` excludes `dist/`, `node_modules/`, `package-lock.json`,
`commitlint.config.mjs` and all Markdown files. Markdown is excluded on purpose:
Prettier rewrites README tables and fenced blocks in full, producing large diffs
with no real gain.

### Line endings

The repository contains a `.gitattributes` with `* text=auto eol=lf`, which forces
LF in the working tree on every operating system.

This is not cosmetic. Without it, a Windows checkout with `core.autocrlf=true`
produces CRLF files locally, and `prettier --check` fails on *every file* on the
developer machine while passing in CI, which runs on Linux. If you cloned the
repository before this file existed, run `git add --renormalize .` once.

## Backend — Ruff

Ruff replaces flake8, black and isort with a single, much faster tool. It is
configured in `pyproject.toml` and pinned in `requirements-dev.txt`, which is never
installed into the Docker image — the `Dockerfile` only installs `requirements.txt`.

### Selected rule families

| Code | Family | What it catches |
|---|---|---|
| `E` | pycodestyle errors | Line length, spacing and other PEP 8 violations |
| `W` | pycodestyle warnings | Trailing whitespace, missing newline at end of file |
| `F` | pyflakes | Unused imports and variables, undefined names |
| `I` | isort | Import block ordering and grouping |
| `UP` | pyupgrade | Older syntax that Python 3.12 expresses better |
| `B` | flake8-bugbear | Common traps, such as mutable default arguments |
| `DJ` | flake8-django | Django-specific practices, such as `null=True` on string fields |

### Other settings

| Setting | Value | Reason |
|---|---|---|
| `line-length` | `100` | |
| `target-version` | `py312` | Same Python version as the `Dockerfile` |
| `extend-exclude` | `core/migrations` | Migrations are generated by Django; style rules do not apply |
| `per-file-ignores` | `scripts/setup_env.py` → `E501` | Its docstring contains copy-and-paste Docker commands that must not be wrapped |

## Fixing Lint Problems Locally

Run these before opening a pull request, and the CI will have nothing left to
report. Most problems are corrected automatically.

### Frontend

```bash
npm install          # only the first time, or after a git pull that changed package.json

npm run lint         # list problems
npm run lint:fix     # fix what can be fixed automatically
npm run format       # apply Prettier to the whole project
npm run format:check # only check, same command the CI runs
```

The usual sequence when the CI fails:

```bash
npm run lint:fix && npm run format && npm run lint && npm run format:check
```

### Backend

```bash
pip install -r requirements.txt -r requirements-dev.txt   # only the first time

ruff check .           # list problems
ruff check --fix .     # fix what can be fixed automatically
ruff format .          # apply the formatting to the whole project
ruff format --check .  # only check, same command the CI runs
```

The usual sequence when the CI fails:

```bash
ruff check --fix . && ruff format . && ruff check . && ruff format --check .
```

### Committing the result

A fix that only changes formatting should be its own commit, using the `style`
type, so that it does not hide behind a feature change:

```text
style(#XX): apply prettier to the upload components
```

## Editor Setup — VS Code

With the editor configured, most problems are fixed on save and never reach the CI.

Install the three extensions:

| Extension | ID |
|---|---|
| ESLint | `dbaeumer.vscode-eslint` |
| Prettier | `esbenp.prettier-vscode` |
| Vue (Official) | `vue.volar` |

Then add to your settings:

```json
{
  "editor.formatOnSave": true,
  "editor.defaultFormatter": "esbenp.prettier-vscode",
  "prettier.requireConfig": true,
  "eslint.workingDirectories": [{ "mode": "auto" }],
  "editor.codeActionsOnSave": { "source.fixAll.eslint": "explicit" }
}
```

`prettier.requireConfig` matters in this repository: it makes Prettier format only
files that have a configuration file nearby. Without it, Prettier would also
reformat backend and documentation files using its own defaults.

Two notes specific to this project's layout:

- VS Code only reads `.vscode/settings.json` from the **root of the open folder**.
  If you open the parent repository instead of `frontend/`, a settings file placed
  in `frontend/.vscode/` is ignored. Either open `frontend/` directly, add it with
  *File > Add Folder to Workspace*, or put the settings at the root.
- `eslint.workingDirectories` is only re-read when the language server restarts:
  `Ctrl+Shift+P` → *ESLint: Restart ESLint Server*. Every other setting applies on
  save.

To confirm ESLint is actually running, write `console.log(doesNotExist)` in any
`.js` file and save. It must be underlined with `no-undef`. If it is not, open
`Ctrl+Shift+P` → *ESLint: Show Output Channel*, which reports whether the
configuration file was found and loaded.

## Troubleshooting

| Symptom | Cause and fix |
|---|---|
| `format:check` fails on every file locally but passes in CI | CRLF line endings. Confirm `.gitattributes` exists, then run `git add --renormalize .` |
| ESLint underlines nothing at all | The open folder is not the submodule root, so `eslint.config.js` was not found. See the VS Code section above |
| A missing semicolon or wrong indent is not reported as an error | Expected. Formatting belongs to Prettier and Ruff format, not to the linter. Save the file, or run the format command |
| CI reports a rule that seems wrong for this project | Do not add an inline ignore silently. Open an issue so the rule is discussed and, if agreed, changed in `eslint.config.js` or `pyproject.toml` for everyone |

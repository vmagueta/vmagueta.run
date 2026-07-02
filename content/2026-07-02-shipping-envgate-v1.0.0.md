---
  title: "Shipping EnvGate v1.0.0: Stable API, Zero Dependencies, and Smart .env Loading"
  date: 2026-07-02
  tags: [python, open-source, devops, envgate]
  ---

  [![PyPI version](https://img.shields.io/pypi/v/envgate.svg)](https://pypi.org/project/envgate/)
  [![Coverage](https://img.shields.io/badge/coverage-100%25-brightgreen.svg)](https://codecov.io/gh/vmagueta/envgate)
  [![CI](https://github.com/vmagueta/envgate/actions/workflows/ci.yml/badge.svg)](https://github.com/vmagueta/envgate/actions)

  Today marks a major milestone for one of my open-source projects. I'm officially bumping EnvGate to v1.0.0, moving its PyPI classifier from Alpha to Production/Stable, and freezing the public API.

  EnvGate was born because I wanted a zero-dependency, predictable environment validation library for Python. Its whole reason to exist is this: instead of your app blowing up at runtime on the *first* missing variable, EnvGate validates the entire schema up front and reports **every** problem at once.

  ```
  Environment validation failed:
      - Environment variable 'DATABASE_URL' is not set.
      - Environment variable 'PORT' has invalid value 'not-a-number' (expected int).
      - Environment variable 'ALLOWED_HOSTS' has 1 invalid item(s) (expected list):
        - item at index 1: ''
  ```

  One run, one list, fixed once. No correct-restart-correct-restart loop.

  With this release, the core API (`get_env`, `validate`, `load_env`, and our five exceptions) is stable. Any breaking change from this point forward means a major version bump.

  Here's what went into the v1.0.0 release.

  ## 1. Zero-Dependency .env Loading

  Before cutting v1.0.0, I wanted to close the loop on local development ergonomics without bloat. The new `load_env(path=".env")` reads a dotenv-style file, injects it into `os.environ`, and hands you back a dictionary of what it parsed.

  You just call it right before `validate()`, and the schema picks up the file's values seamlessly.

  ```python
  from envgate import load_env, validate

  load_env()                 # .env → os.environ
  config = validate({
      "DATABASE_URL": {"type": "str"},
      "PORT": {"type": "int"},
  })
  ```

  Architectural choices worth calling out:

  - **Real Env Vars Win by Default:** In standard production environments, actual environment variables should always win. `os.environ.setdefault` is used under the hood, so we never overwrite them quietly.

  - **The Return Value Is What the File Declared:** `load_env` returns the *full* parsed contents of the file — every key it read, mapped to its raw string value — regardless of what actually landed in `os.environ`. That's deliberately useful for logging or diffing what a file *would* set without inspecting global state.

  - **Fail on Malformed Files, Silent on Missing Ones:** If a `.env` file doesn't exist, it's a silent no-op (exactly what you want in production). But if the file exists and has a line EnvGate can't parse — no `=` separator, or an empty variable name — it raises a new `EnvFileError`. EnvGate exists to prevent silent infrastructure failures, not to swallow them.

  - **The Parser Stays Tiny — and Opinionated About `#`:** Built entirely in standard Python (no external dependencies). Here's the decision I'm most happy with: EnvGate **deliberately does not treat `#` inside a value as an inline comment.** A `#` is kept verbatim, because it's a perfectly legal character in URLs, passwords, and tokens — and this is exactly where naive parsers mangle your `DATABASE_URL`. Only full-line comments and blank lines are skipped. On top of that, it strips a single pair of surrounding quotes while preserving whitespace *inside* the quotes, and tolerates a leading `export ` for files meant to be sourced.

  ## 2. The override Flag for Testing Fixtures

  While building the parser, a classic edge case appeared: testing. During local test execution, you often want the `.env` file or a specific fixture file to be the absolute source of truth, overriding whatever is currently set in your terminal's environment.

  To solve this, I introduced a keyword-only `override` option to `load_env()`:


To solve this, I introduced a keyword-only `override` option to `load_env()`:

```python
# Real environment variables still win (default)
load_env()

# The file wins, replacing whatever is already set (perfect for tests)
load_env(override=True)
```

Under the hood, `override=True` switches from `setdefault` to `os.environ.update`. Because it's keyword-only and defaults to `False`, it introduces zero breaking changes for existing codebases.

## What's Next?

With the API frozen and stable, EnvGate is fully ready for production pipelines. It is formatted and linted with Ruff, ships 100% type hints (`py.typed`), and is backed by a robust test suite sitting at 100% coverage.

You can check out the source code or contribute here: [github.com/vmagueta/envgate](https://github.com/vmagueta/envgate)
Or pull it directly into your next Python stack: `pip install envgate`

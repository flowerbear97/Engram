# Contributing to Neuragram

Thanks for your interest in contributing! Here's how to get started.

## Development Setup

```bash
git clone https://github.com/flowerbear97/neuragram.git
cd neuragram
pip install -e ".[dev]"
```

## Running Tests

```bash
pytest                    # all tests
pytest tests/test_client.py  # specific file
pytest -x                 # stop on first failure
```

## Code Quality

```bash
ruff check src/ tests/    # lint
ruff format src/ tests/   # format
mypy src/neuragram/       # type check
```

## Code Style

- Python 3.9+ compatible — no `match` statements or `X | Y` type unions
- Ruff for linting/formatting (line length 100)
- Async-first: store operations are async, client provides sync wrappers
- Tests use `pytest-asyncio` with `asyncio_mode = "auto"`
- Test fixtures use in-memory SQLite (`:memory:`)

## Pull Requests

1. Fork the repo and create a branch from `main`
2. Add tests for any new functionality
3. Ensure `pytest` and `ruff check` pass
4. Keep PRs focused — one feature or fix per PR

## Reporting Issues

Open an issue at [github.com/flowerbear97/neuragram/issues](https://github.com/flowerbear97/neuragram/issues) with:
- What you expected vs. what happened
- Minimal reproduction steps
- Python version and OS

## License

By contributing, you agree that your contributions will be licensed under the Apache-2.0 License.

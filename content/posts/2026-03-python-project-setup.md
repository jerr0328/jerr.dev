---
title: "2026 Python Project Setup"
date: 2026-03-26T18:30:00+02:00
---

I tend to not like cookiecutter/boilerplate repos because those tend to get out of date quickly, and usually just base new projects off of whatever project I've been mostly recently working on.
However, I figured it might be better to make occasional blog posts to get some check points on what I think is a good setup for a Python project, which can be interesting to see later on how things change.

## Dependency management

I've been a big fan of [uv](https://docs.astral.sh/uv/) for a while, especially as it supports a lot of the native project management in the standard [pyproject.toml file](https://packaging.python.org/en/latest/guides/writing-pyproject-toml/). It's great for having separate "production" and "development" dependencies with just the direct dependency needs, and all the pinned versions and transitive dependencies end up in `uv.lock` for applications.

`uv` also nicely supports the [inline script metadata](https://packaging.python.org/en/latest/specifications/inline-script-metadata/#inline-script-metadata) when calling `uv run my_script.py`, so you can do completely without a `pyproject.toml` file if you want.

## Python version management

Previously, I was using `pyenv` with `pyenv-virtualenv` and it served me well for many years, but I've been moving to `uv` instead as it tends to quickly bring in the latest python versions and has a very fast install process, without requiring a complicated `PATH` setup using shims.
It also keeps all the virtual environments in the source directory (under `.venv`), avoided bloating a common folder with dozens of environments.
Having to clean up environments with pyenv was not so easy, but with `uv` I don't really have to worry and can easily switch Python versions when needed.

## Formatting and linting

I've really admired the movement towards having opinionated formatters, and for the longest time I've used [black](https://black.readthedocs.io/en/stable/), but lately I've been using [ruff](https://docs.astral.sh/ruff/) on newer projects since I anyways use ruff as a linter.
The two are quite similar in their formatting, and honestly either one is perfectly fine to use.

As I mentioned, I use ruff primarily as a linter, and a big reason that I've started liking it is because of its ability to run quickly and fix many common issues. Combined with a formatter, I don't have to setup too many exceptions or make decisions on which rules to enable.
I do wish, however, that there would be an option for having a more standard set enabled by default for a more config-less approach when setting up projects.

For instance, here's a starting config I use in my `pyproject.toml`:

```toml
[tool.ruff.lint]
select = [
    "A",
    "ARG",
    "B",
    "C4",
    "DTZ",
    "E",
    "F",
    "FA",
    "FLY",
    "FURB",
    "I",
    "INP",
    "ISC",
    "N",
    "PERF",
    "PIE",
    "PTH",
    "RET",
    "RUF",
    "SIM",
    "TC",
    "TID",
    "TRY",
    "UP",
]
```

These rules combine a lot of tools I used to use (like isort, pyupgrade) and brings in linters I didn't know about, but brings quite a lot of value.
I used to spend quite some time on code reviews dealing with some tricky issues or bug-prone practices that the linter can easily handle and even fix for me.
I've gone through many repos and was able to bring in many rules quite quickly, even before the time of AI tools.

A full configuration would include more rules (like `S` for some security checks, `PT` for pytest), but it also requires a bit more configuration (need to exclude S101 from test files, since we do want use `assert` statements there).

One thing I do want to point out is that I've avoided linting docstrings.
I tend to find that linting documentation to be too much of a hurdle to writing anything meaningful, and can lead to easily throwing linter ignores or poor quality documentation that adds no value but adds extra work for developers.
In fact, a lot of pain I had with linters in the past was because of the annoying rules that didn't really add any value but made it a much bigger chore when doing any changes.

## Type checking

Python's typing system has been getting better with each release, and it's really at the point now where there's no excuse for new projects to skip type hints.
I won't go into all the benefits of type-checking here, there are plenty of resources out there supporting moving to better type hint support.

I primarily use [mypy](https://www.mypy-lang.org) for type checking, as it's quite widely supported with plugins, stubs, and good documentation on why mypy raises certain issues. Although it can be slow on some larger projects, it's still relatively fast compared to running a comprehensive test suite.

Some notes though on how to get a better experience with mypy:

- Make any `# type: ignore` comment a last resort. If you do need to put one for some reason, put another comment explaining why (e.g. if there's a known issue)
- If bringing up existing code up with type hints, start at the edges of your project (modules which are imported elsewhere, like models, utilities) and set ignore rules where you haven't gotten to yet. Be sure to actually set the time to get to completing this or you may forget to complete it!
- Set linter rules to check for annotations and type-checking best practices (e.g. Ruff rules `ANN` and `TC`)
- Enable stricter rules as you get more of the project type-hinted
- Use [Protocol](https://typing.python.org/en/latest/spec/protocol.html) classes to enable duck typing benefits while still having some type enforcement
- Use [Generics](https://typing.python.org/en/latest/spec/generics.html) instead of `Any` type (or instead of unions), especially with functions that can take in various types but typically return the same type passed in.

## Testing

I am a fan of [pytest](https://docs.pytest.org/en/stable/), this has been a solid testing tool that provides a lot of functionality, such as parameterization, fixtures, and rich asserts (without needing to define special assert methods/functions). Pytest also has a very good plugin system, and I have a few go-to plugins for testing:

- [pytest-mock](https://pytest-mock.readthedocs.io/en/latest/index.html) - provides the `mocker` fixture which makes mocking/patching way cleaner
- [time-machine](https://time-machine.readthedocs.io/en/latest/) - allows setting fixed date/time in tests (though be careful with this one, since it also can stop time and have unexpected side effects)
- [pytest-cov](https://pytest-cov.readthedocs.io/en/latest/) - run coverage checks directly from the pytest invocation

Some packages also come with their own pytest plugins (like [anyio](https://anyio.readthedocs.io/en/stable/index.html)), while others may have a dedicated support plugin (like [pytest-django](https://pytest-django.readthedocs.io/en/latest/index.html)).

Testing is not my strong suit, I know there's a lot to do better (such as dealing with dependency injection, or better handling of [mocks](https://martinfowler.com/articles/mocksArentStubs.html)), so perhaps I will need to follow up in the future on improvements in testing.

## Pre-commit hooks

With all the linting and formatting tools being quick to run, it makes sense to have those checked before you commit code.
For years I've been using [pre-commit](https://pre-commit.com), but these days I've started replacing that with [prek](https://prek.j178.dev), another Rust-based tool to bring even faster execution.
Prek has numerous advantages over pre-commit, especially when using similar hooks in several repositories.
It's also nice that it can work as a drop-in replacement, making it easier to switch and not requiring everyone to update their tooling at the same time.

One important note here, do **not** run `mypy` as a pre-commit hook, unless you run it in the right virtualenv, it won't have all your project dependencies and will give different results.
It's also sometimes a bit too slow on larger projects to make it worthwhile to check in pre-commit hooks.
I generally anyways do a mypy check in CI.

Another thing I recommend **against** is any form of pre-push checks. For instance, I know some folks might like to run tests as a pre-push check, but since the tests can be slow and require docker to run, this isn't a great idea if you anyways have CI/CD running tests and would only need to push some README update.

## Conclusion

Astral's Rust-based tooling of ruff and uv have made a huge impact on the Python community.
While their [recent acquisition by OpenAI](https://astral.sh/blog/openai) is concerning in an age of [enshittification](https://en.wikipedia.org/wiki/Enshittification), their tools are still MIT licensed and would likely be quickly forked and supported by the community if anything were to start going wrong.

Tools that offer fast, convenient, opinionated setup that doesn't cause much toil tends to be what attracts me to using them.
Especially with more AI tooling/assistants, this continues to help keep guardrails on projects.
I hope to never have to go back to tabs vs spaces or discussions on where curly braces should go (from those C++ days).

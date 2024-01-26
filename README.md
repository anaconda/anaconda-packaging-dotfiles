# anaconda-packaging-dotfiles
This repository contains a set of standard dotfiles and configuration files to ensure a minimum level of software
standards for Anaconda packaging tooling in all new Python projects going forward.

These tools and standards have been proven and used in a number of our open source Python projects:
- [anaconda-packaging-utils](https://github.com/anaconda-distribution/anaconda-packaging-utils)
- [anaconda-linter](https://github.com/anaconda-distribution/anaconda-linter)
- [percy](https://github.com/anaconda-distribution/percy)

## Primary Tools Being Used
- `black`
  - Standard rule set, with modifications for `isort` compatibility and support for 120-column line lengths.
- `pylint`
  - An Anaconda-developer friendly variant of the [Google Python Style Guide](https://google.github.io/styleguide/pyguide.html)
- `mypy`
- `make`
- `pre-commit`
- `pytest`
- `docconvert`

# On-boarding Process

## File and Folder Structure
Most of the config files provided by this repository will have a `TODO` where a project name should be.
Project names generally should start with `anaconda-` and use `-`'s to represent spaces. Underscores will need to
be used in any corresponding package names.

Roughly, the project should look like:
```
/
|-- <dotfiles>
|-- <project_name_with_underscores>
    |-- __init__.py # An empty file
    |-- py.typed # An empty file
    |-- <Other source files and module folders>
|-- tests/
    |-- <pytest config files, `test_*.py`>
    |-- <test utility files>
    |-- test_aux_files/
        |-- <Auxiliary test files used in tests>
```

## Steps
1. Move the project into its own repository, if applicable. If not, incorporate the tool into an existing project
   1. Many package builder scripts may fall into the `perseverance-scripts` umbrella
1. Establish which version(s) of Python we need to target. For now, new tools are targeting `3.11+`
1. Copy the dotfiles and configurations from this repository over. The following bullet points go into specific
   detail.
1. Update the `pyproject.toml` as needed.
1. Setup an `environment.yaml` file to ensure dependencies are installable by `conda`
1. Setup the standard `Makefile` to ensure a consistent set of `make` targets across our projects.
   1. Not all of these will work until the following steps are configured.
1. Setup `pre-commit`
   1. Our projects use `pre-commit` and `make` to enforce our code and test requirements. If done properly,
      `make dev` will create an environment with `pre-commit` automatically installed. Developers should be able
      to blow away and start over from scratch by just running `make dev`
1. Setup and enforce some amount of unit test coverage with `pytest`. Eventually the goal should be at to require at
   least 80% coverage for your project.
1. Setup and enforce our standard `pylint` rules
1. Setup and enforce our standard `mypy` rules
1. Setup GitHub Actions that run the `pre-commit` checks and unit tests on all supported versions of Python.
   Enforce that all PRs must pass these checks as merge requirements through the repository's GitHub settings. This
   requires the Actions to run on a PR at least once before they will show up in the GitHub settings page.
1. If this is a library, configure the project to publish to the `distro-tooling` channel as a "typed" project and
   create an appropriate feedstock in `aggregate`.
1. If this is CLI-based tool, consider using the `click` library to build a modular command line interface
   and Python's built-in `logging` module for logging errors, debug information, etc.


# TODOs & Future Goals
1. GitHub Workflows should use this shared configuration and set of minimum actions. This should be pulled in by new
   new projects, in one of the following ways (TBD):
   1. Each repository's GitHub Workflow can pull and execute the GitHub Workflow found in this project, configuring
      environment variables as needed.
   1. Repositories that can access GitHub Enterprise features can be configured to automatically run a common GitHub
      Workflow.
1. Find a way to share/pull-in the dotfiles without copying-and-pasting files
   1. Some projects may need to modify the base file.
   1. This might add some breakage risk but should simplify the process of improving all projects at once.
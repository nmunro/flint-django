# Flint

Flint is an opinionated [cookiecutter](https://cookiecutter.readthedocs.io/en/latest/) template for bootstrapping Python based projects.

It uses [Poetry](http://poetry.eustace.io/) as its package manager and comes with the following tools:

1. [flake8](https://pypi.org/project/flake8/)
2. [coverage](https://pypi.org/project/coverage/)
3. [sphinx](https://pypi.org/project/Sphinx/)
4. [isort](https://pypi.org/project/isort/)

To get you a project built with good tooling out of the box.

Flint projects use `make` to run various commands to help you run your project, there are several rules configured by default (although you are free to continue adding your own), they are:

|Rules      | Description
|-----------|-----------------------------------------------------------------|
| env       | Builds the virtual environment.                                 |
| run       | Runs your app, you may need to configure this rule.             |
| test      | Runs your unit tests, you do write them, right?                 |
| requires  | Builds a classic requirements.txt file for use on servers.      |
| lint      | Runs flake8 to help lint your code.                             |
| docs      | Runs sphinx to document your code, you do write docs, right?    |

Flint also runs `git init` on your newly generated project ready for you to start committing,
with that in mind it also installs a git-precommit hook that ensures upon each commit that your
requirements.txt file is kept up to date with poetry and that both `make lint` & `make test`
are ran.

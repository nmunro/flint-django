# {{cookiecutter.project_name}}

{{cookiecutter.project_short_description}}

## Getting Started

These instructions will get you a copy of the project up and running on your local machine for development and testing purposes. See deployment for notes on how to deploy the project on a live system.

{%- if cookiecutter.project_sys_deps -%}
### Prerequisites

You need to ensure the following are installed to enable this to build/run.

```
{{ cookiecutter.project_sys_deps }}
```
{% endif %}

### Installing

Setting up a Flint project should be as simple as:

```
make env
```

## Running the tests

```
make tests
```

## Generating the documentation

```
make docs
```

## Authors

* {{cookiecutter.author_name}} ({{cookiecutter.author_email}})

## Acknowledgments

* Hat tip to anyone whose code was used
* Inspiration
* etc


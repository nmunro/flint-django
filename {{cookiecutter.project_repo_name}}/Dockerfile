FROM python:latest as DEV
# Configure Poetry

ENV POETRY_VERSION=1.6
ENV POETRY_HOME=/opt/poetry
ENV POETRY_VENV=/opt/poetry-venv
ENV POETRY_CACHE_DIR=/opt/.cache


WORKDIR /app/{{cookiecutter.project_repo_name}}
# Install poetry separated from system interpreter

RUN python3 -m venv $POETRY_VENV \
    && $POETRY_VENV/bin/pip install -U pip setuptools wheel \
    && $POETRY_VENV/bin/pip install poetry==${POETRY_VERSION}

# Add `poetry` to PATH
ENV PATH="${PATH}:${POETRY_VENV}/bin"

COPY . /app/{{cookiecutter.project_repo_name}}
RUN poetry install && poetry lock && poetry export -f requirements.txt --output requirements.txt
RUN groupadd -r docker && useradd -r -m -g docker docker
RUN chown -R docker /opt
ENV PYTHONPATH $PYTHONPATH:/app

FROM python:3.11 as PROD
ENV PYTHONUNBUFFERED=1

WORKDIR /app/{{cookiecutter.project_repo_name}}
RUN apt update -y && apt upgrade -y

COPY . /app/{{cookiecutter.project_repo_name}}
COPY --from=dev /app/{{cookiecutter.project_repo_name}}/requirements.txt .

RUN pip install -r requirements.txt
RUN groupadd -r docker && useradd -r -m -g docker docker


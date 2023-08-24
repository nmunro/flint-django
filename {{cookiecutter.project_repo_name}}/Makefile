.PHONY: build clean clean-docker clean-docs docs repl test shell start stop lint migrate migrations dev-tools-check logs static poetry create-super-user
.DEFAULT_GOAL: build

REPORT := $(or $(REPORT),report -m)
GIT_CHANGED_PYTHON_FILES := $(shell git diff --name-only -- '***.py')
DEV_TOOLS := $(and $(shell which docker git))
COMPOSE_FILE := 'docker-compose.yml'
SERVICE := $(or $(SERVICE),web-dev)

dev-tools-check:
ifeq ($(DEV_TOOLS),)
	$(error Some of your dev tools are missing, unable to proceed)
endif

build: dev-tools-check
ifeq ($(FORCE),true)
	@docker container rm -f $(SERVICE)
	@docker container prune
endif
	@docker compose -f $(COMPOSE_FILE) build $(SERVICE)

start: dev-tools-check
	@docker compose -f $(COMPOSE_FILE) up $(SERVICE) -d --remove-orphans

stop: dev-tools-check
	@docker compose -f $(COMPOSE_FILE) down $(SERVICE) --remove-orphans

ifeq ($(FORCE),true)
	@docker container stop $(shell docker container ls -q --filter name="{{ cookiecutter.project_repo_name }}")
endif

lint:
	@$(foreach file, $(GIT_CHANGED_PYTHON_FILES), $(shell docker compose -f $(COMPOSE_FILE) run --rm $(SERVICE) /bin/bash -c "poetry run black ${file} && poetry run isort ${file} && poetry run flake8 ${file}"))

static: dev-tools-check
	@docker compose -f $(COMPOSE_FILE) run --rm $(SERVICE) python ./manage.py collectstatic

migrate: dev-tools-check
	@docker compose -f $(COMPOSE_FILE) run --rm $(SERVICE) python ./manage.py migrate

migrations: dev-tools-check
	@docker compose -f $(COMPOSE_FILE) run --rm $(SERVICE) python ./manage.py makemigrations $(ARGS)

repl: dev-tools-check
	@docker compose -f $(COMPOSE_FILE) run --rm $(SERVICE) python manage.py shell

shell:
	@docker compose -f $(COMPOSE_FILE) run --rm $(SERVICE) /bin/bash

test: dev-tools-check
ifneq ($(SERVICE),web-dev)
	$(error Command not available for service: '$(SERVICE)')
else ifneq ($(and $(TEST-CASE),$(SRC)),)
	@rm -rf coverage
	@docker compose -f $(COMPOSE_FILE) run --rm $(SERVICE) coverage run --source=$(SRC) --branch ./manage.py test --no-input $(TEST-CASE); docker compose -f $(COMPOSE_FILE) run --rm $(SERVICE) coverage $(REPORT)
else ifneq ($(SRC),)
	@rm -rf coverage
	@docker compose -f $(COMPOSE_FILE) run --rm $(SERVICE) coverage run --source=$(SRC) --branch ./manage.py test --no-input; docker compose -f $(COMPOSE_FILE) run --rm $(SERVICE) coverage $(REPORT)
else ifneq ($(TEST-CASE),)
	@rm -rf coverage
	@docker compose -f $(COMPOSE_FILE) run --rm $(SERVICE) coverage run --branch ./manage.py test --no-input $(TEST-CASE) --parallel
else
	@rm -rf coverage
	@docker compose -f $(COMPOSE_FILE) run --rm $(SERVICE) coverage run --branch ./manage.py test --no-input --parallel
endif
	@rm -rf .coverage.*

clean:
	@docker container prune -f

ifeq ($(IMAGES),true)
	@docker image prune -fa
	@docker builder prune -fa
endif

ifeq ($(VOLUMES),true)
	@docker volume prune -fa
endif

ifeq ($(SYSTEM),true)
	@docker system prune -fa
	@docker builder prune -fa
endif

logs:
	@docker compose logs $(SERVICE) -f

poetry:
ifeq ($(SERVICE),web-dev)
	@docker compose -f $(COMPOSE_FILE) run --rm $(SERVICE) poetry $(CMD)
else
	$(error Command not available for service: '$(SERVICE)')
endif

create-super-user:
	@docker compose -f $(COMPOSE_FILE) run --rm $(SERVICE) python ./manage.py createsuperuser

docs:
ifeq ($(SERVICE),web-dev)
	@docker compose -f $(COMPOSE_FILE) run --rm $(SERVICE) poetry run sphinx-apidoc -f -o docs/source/ . ./*/test/*.py ./tests/*.py ./*/migrations/*.py ./*/tests/*.py ./settings/*.py
	@cd docs && make html COMPOSE_FILE=$(COMPOSE_FILE) SERVICE=$(SERVICE)
else
	$(error Command not available for service: '$(SERVICE)')
endif


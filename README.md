# UK EO Data Hub Platform: template repository

This serves as a template repository for new UKEODHP Python-base software components.

When using this template remember to:
* Edit `pyproject.toml` and set everything marked `CHANGEME`
* Update the dependencies as described below.
* Create a Docker repo in AWS (https://eu-west-2.console.aws.amazon.com/ecr/create-repository?region=eu-west-2 for
  the Telespazio UKEODHP repos) and update `Makefile` with the name of the Docker image to build.
* Consider adding `dockerrun` and/or `run` targets to the `Makefile`.
* Update `Dockerfile`
* Add a description here of how to do local development.

# Development of this component

## Getting started

### Prerequisites

Install the `uv` package manager by following the [official documentation](https://docs.astral.sh/uv/getting-started/installation/).

### Install via makefile

```commandline
make setup
```

This will create a Python virtual environment, install basic testing and linting
dependencies and install `pre-commit`.

After `make setup` you can run `make check` to run linting and type checking.

## Building and testing

This component uses `pytest` tests and the `ruff` linter and `pyright` type checker.

See Makefile for all available commands.

A number of `make` targets are defined:
* `make test`: run tests continuously
* `make testonce`: run tests once
* `make check`: run linting and type checking
* `make format`: reformat code
* `make install`: install dependencies
* `make update`: update dependencies
* `make dockerbuild`: build a `latest` Docker image (use `make dockerbuild `VERSION=1.2.3` for a release image)
* `make dockerpush`: push a `latest` Docker image (again, you can add `VERSION=1.2.3`) - normally this should be done
  only via the build system and its GitHub actions.

## Managing dependencies

To add a new dependency, run `uv add <dependency>` or see `uv help add`.

## Releasing

Ensure that `make check` and `make test` work correctly and produce no further changes to code formatting before
continuing.

Releases tagged `latest` and targeted at development environments can be created from the `main` branch. Releases for
installation in non-development environments should be created from a Git tag named using semantic versioning. For
example, using

* `git tag 1.2.3`
* `git push --tags`

Normally, Docker images will be built automatically after pushing to the UKEODHP repos. Images can also be created
manually in the following way:

* For versioned images create a git tag.
* Log in to the Docker repository service. For the UKEODHP environment this can be achieved with the following command
  ```AWS_ACCESS_KEY_ID=...  AWS_SECRET_ACCESS_KEY=... aws ecr get-login-password --region eu-north-1 | docker login --username AWS --password-stdin 312280911266.dkr.ecr.eu-west-2.amazonaws.com```
  You will need to create an access key for a user with permission to modify ECR first.
* Run `make dockerbuild` (for images tagged `latest`) or `make dockerbuild VERSION=1.2.3` for a release tagged `1.2.3`.
  The image will be available locally within Docker after this step.
* Run `make dockerpush` or `make dockerpush VERSION=1.2.3`. This will send the image to the ECR repository.

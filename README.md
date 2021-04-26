# CI/CD Workflow with CircleCI, Azure and GitHub

A collection of sources, guides and config files for deployment of development projects with CI/CD.

The process of CI/CD during development stage of a `C++` or Python project implies building on each push (on any branch)

The repository contains various sources that help to develop a proper pipeline for [*CircleCI*](https://circleci.com/) and also [*GitHub Actions*](https://github.com/features/actions).

> Since June2020, development integrated with Azure DevOps (namely, [Azure Pipelines](https://azure.microsoft.com/en-us/services/devops/pipelines/)) is also adopted into CI/CD workflow for various C++ and Python projects.

* Official GitHub Actions documentation: [here](https://help.github.com/en/actions)
* Official CircleCI documentation: [here](https://circleci.com/docs/)
* Official Azure Pipelines documentation: [here](https://docs.microsoft.com/en-us/azure/devops/pipelines/?view=azure-devops)

Read about each workflow implementation and actual documentation [here](docs.md)

## Content

1. Documentation and sources - [here](docs.md) (`.pdf` version available as well.)
2. CI/CD Issues & CircleCI in general - [here](issues.md)
3. About CMake- [here](cmake.md)
4. About GitHub Actions - [here](cmake.md)
5. About Azure DevOps - Pipelines - [here](azurepipelines.md)

**Update**: Fast setup configuration scripts for Centos, Debian and Ubuntu containers added for CircleCI CI/CD workflow.

## June 2020 update

C++ and Python projects integrated with **Azure Pipelines** were also added into GitHub repos. There are two projects which use *Azure Pipelines* (Azure DevOps) for CI/CD workflow.

* [Python & CI/CD - Azure Pipelines](https://github.com/basavyr/python-azure-pipelines)
* [Histogram maker](https://github.com/basavyr/histogram-maker)

*Histogram maker* has also implemented build/configure process with CMake. Issues and documentation will be provided with further development progress on the actual projects. (e.g. issues, pull requests).

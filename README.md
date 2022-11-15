[![Build Status](https://dev.azure.com/asottile/asottile/_apis/build/status/asottile.azure-pipeline-templates?branchName=main)](https://dev.azure.com/asottile/asottile/_build/latest?definitionId=7&branchName=main)
[![pre-commit.ci status](https://results.pre-commit.ci/badge/github/asottile/azure-pipeline-templates/main.svg)](https://results.pre-commit.ci/latest/github/asottile/azure-pipeline-templates/main)

azure-pipeline-templates
========================

## usage

**First configure a github service connection**

It is suggested to use a generic name, such as `github` so forks can also
configure the same.

You can find this in `Project Settings` => `Service connections` in the
Azure Devops dashboard for your project.  Project settings is located in the
bottom left corner of the UI as of 2019-03-07.

Below I'm using the endpoint name **`github`**

**Next add this to the beginning of your `azure-pipelines.yml`**

```yaml
resources:
  repositories:
    - repository: asottile
      type: github
      endpoint: github
      name: asottile/azure-pipeline-templates
      ref: refs/tags/v2.5.1
```

this will make the templates in this repository available in the `asottile`
namespace.

## job templates

### `job--python-tox.yml`

_new in v0.0.1_

This job template will install python and invoke tox.

#### parameters

- `toxenvs`: the list of `tox` environment names to run
- `os`: choices (`linux`, `windows`, `osx`)
- `architectures`: _new in v2.0.0_ list with choices (`x64`, `x86`),
  default `[x64]` (only affects windows)
- `coverage`: _new in v0.0.7_ after the run publish coverage to azure
  pipelines, default `true`
- `wheel_tags`: _new in v0.0.10_ after a run of a tag, build a wheel and
  publish it as an artifact, default `false`.  the artifacts can be downloaded
  using the `bin/download-wheels` script included in this repository.
- `additional_variables`: _new in v0.0.13_ additional pipeline `variables`
- `pre_test`: _new in v0.0.5_ `steps` to run before running `tox`, such as
  installing tools, etc.  default: `[]`
- `name_postfix`: _new in v0.0.5_ string to be appended to job name if you need
  to make it unique, default: `''`

The tox environments must either:
- be equal to: `py37`, `py38`, `py39`, `py310`, `py311`, `py312`, `pypy3`
- start with: `py37-`, `py38-`, `py39-`, `py310-`, `py311-`, `py312-`, `pypy3-`

for now, python3.12 is only available on linux -- it is installed from
[deadsnakes](https://github.com/deadsnakes)

coverage information can be displayed using a
[shields.io badge](https://shields.io/category/coverage)

#### example

```yaml
- template: job--python-tox.yml@asottile
  parameters:
    toxenvs: [py27, py37]
    os: windows
```

- [example using this template: asottile/pyupgrade](https://github.com/asottile/pyupgrade/blob/8701d8b7/azure-pipelines.yml#L14-L21)

### `job--pre-commit.yml`

_new in v0.0.2_

This job template will invoke [pre-commit](https://pre-commit.com) against all
files.

#### parameters

- `ruby`: the version of ruby to install to the system (used by some hooks),
  defaults to `'>= 2.4'`
- `python`: the python version to run pre-commit with, defaults to `'3.7'`

#### example

```yaml
- template: job--pre-commit.yml@asottile
```

- [example using this template: pre-commit/pygrep-hooks](https://github.com/pre-commit/pygrep-hooks/blob/2968c93e/azure-pipelines.yml#L9-L10)

### `job--go-test.yml`

_new in v0.0.6_

This job checks out a go project, runs `go get` and then `go test`

#### parameters

- `go_versions`: list of go versions to test against
- `os`: choices (`linux`, `windows`, `macos`)
- `tests`: what to `go test ...`, default `./...`
- `pre_test`: `steps` to run before running `tox`, such as installing tools,
  etc.  default: `[]`
- `name_postfix`: string to be appended to job name if you need to make it
  unique, default: `''`

#### example

```yaml
- template: job--go-test.yml@asottile
  parameters:
    go_versions: ['1.11.5', '1.12']
    os: 'linux'
```

- [example using this template: asottile/dockerfile](https://github.com/asottile/dockerfile/blob/2bd942dc/azure-pipelines.yml#L16-L21)

## step templates

When referring to a step template as part of `pre_test`, omit the `@asottile`
repository selector (as the template is included *after* job templating).

### `step--git-install.yml`

_new in v0.0.8_

This step template will install `git` from source and put it on the `PATH`.
This step is currently only supported on `linux`.

#### parameters

- `versions`: ref to install at, default `HEAD`

#### example

```yaml
  - template: step--install-git.yml
```

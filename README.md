[![Build Status](https://dev.azure.com/asottile/asottile/_apis/build/status/asottile.azure-pipeline-templates?branchName=master)](https://dev.azure.com/asottile/asottile/_build/latest?definitionId=7&branchName=master)

azure-pipeline-templates
========================

## usage

**First find the name of your github service connection**

You can find this in `Project Settings` => `Service connections` in the
Azure Devops dashboard for your project.

**Next add this to the beginning of your `azure-pipelines.yml`**

```yaml
resources:
  repositories:
    - repository: asottile
      type: github
      endpoint: <<<service connection name>>>
      name: asottile/azure-pipeline-templates
      ref: refs/tags/v0.0.5
```

this will make the templates in this repository available in the `asottile`
namespace.

### `job--python-tox.yml`

_new in v0.0.1_

This job template will install python and invoke tox.

#### parameters

- `toxenvs`: the list of `tox` environment names to run
- `os`: choices (`linux`, `windows`, `osx`)
- `architecture`: _new in v0.0.6_ choices (`x64`, `x86`), default `x64`
  (only affects windows)
- `coverage`: _new in v0.0.3_ after the run publish coverage to azure
  pipelines, default `true`
- `pre_test`: _new in v0.0.5_ `steps` to run before running `tox`, such as
  installing tools, etc.  default: `[]`
- `name_postfix`: _new in v0.0.5_ string to be appended to job name if you need
  to make it unique, default: `''`

The tox environments must either:
- be equal to: `py27`, `py34`, `py35`, `py36`, `py37`, `py38`
- start with: `py27-`, `py34-`, `py35-`, `py36-`, `py37-`, `py38-`

for now, python3.8 is only available on linux -- it is installed from
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

- `go_versions`: list of go versions to test againsg
- `os`: choices (`linux`, `windows`, `osx`)
- `tests`: what to `go test ...`, default `./...`
- `pre_test`: _new in v0.0.5_ `steps` to run before running `tox`, such as
  installing tools, etc.  default: `[]`
- `name_postfix`: _new in v0.0.5_ string to be appended to job name if you need
  to make it unique, default: `''`

#### example

```yaml
- template: job--go-test.yml@asottile
  parameters:
    go_versions: ['1.11.5', '1.12']
    os: 'linux'
```

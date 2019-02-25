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
      ref: refs/tags/v0.0.3
```

this will make the templates in this repository available in the `asottile`
namespace.

### `job--python-tox.yml`

_new in v0.0.1_

This job template will install python and invoke tox.

#### parameters

- `tox`: the `tox` environment name to run
- `os`: choices (`linux`, `windows`, `osx`)
- `coverage`: _new in v0.0.3_ after the run publish coverage to azure
  pipelines, default `true`

The tox environments must either:
- be equal to: `py27`, `py34`, `py35`, `py36`, `py37`, `py38`
- start with: `py27-`, `py34-`, `py35-`, `py36-`, `py37-`, `py38-`

for now, python3.8 is only available on linux -- it is installed from
[deadsnakes](https://github.com/deadsnakes)

coverage information can be displayed using a
[shield.io badge](https://shields.io/category/coverage)

#### example

```yaml
- template: job--python-tox.yml@asottile
  parameters: {tox: py37, os: windows}
```

- [example using this template: asottile/pyupgrade](https://github.com/asottile/pyupgrade/blob/a19ba9be/azure-pipelines.yml#L10-L11)

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

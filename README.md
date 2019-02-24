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
      ref: refs/tags/v0.0.1
```

this will make the templates in this repository available in the `asottile`
namespace.

### `job--python-tox.yml`

This job template will install python and invoke tox.

#### parameters

- `tox`: the `tox` environment name to run
- `os`: choices (`linux`, `windows`, `osx`)

The tox environments must either:
- be equal to: `py27`, `py34`, `py35`, `py36`, `py37`, `py38`
- start with: `py27-`, `py34-`, `py35-`, `py36-`, `py37-`, `py38-`

for now, python3.8 is only available on linux -- it is installed from
[deadsnakes]

#### example

```yaml
- template: job--python-tox.yml@asottile
  parameters: {tox: py37, os: windows}
```

[deadsnakes]: https://github.com/deadsnakes

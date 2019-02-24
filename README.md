azure-pipeline-templates
========================

## usage

Add this to the beginning of an azure pipeline to use the templates

```yaml
resources:
  repositories:
    - repository: asottile
      type: github
      name: asottile/azure-pipeline-templates
      ref: refs/tags/v0.0.0
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

for now, python3.8 is only available on linux

#### example

```yaml
- template: job--python-tox.yml@asottile
  parameters: {tox: py37, os: windows}
```

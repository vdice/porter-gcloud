# gcloud CLI Mixin for Porter

This is a mixin for Porter that provides the gcloud CLI.

## Mixin Syntax

See the [gcloud CLI Command Reference](https://cloud.google.com/sdk/gcloud/reference/) for the supported commands

```yaml
gcloud:
  description: "Description of the command"
  groups: GROUP
  command: COMMAND
  arguments:
  - arg1
  - arg2
  flags:
    FLAGNAME: FLAGVALUE
    REPEATED_FLAG:
    - FLAGVALUE1
    - FLAGVALUE2
  outputs:
    - name: NAME
      jsonPath: JSONPATH
```

```yaml
gcloud:
  description: "Description of the command"
  groups:
  - GROUP 1
  - GROUP 2
  command: COMMAND
  arguments:
  - arg1
  - arg2
  flags:
    FLAGNAME: FLAGVALUE
    REPEATED_FLAG:
    - FLAGVALUE1
    - FLAGVALUE2
  outputs:
    - name: NAME
      jsonPath: JSONPATH
```

### Outputs

The mixin supports `jsonpath` outputs.


#### JSON Path

The `jsonPath` output treats stdout like a json document and applies the expression, saving the result to the output.

```yaml
outputs:
- name: NAME
  jsonPath: JSONPATH
```

For example, if the `jsonPath` expression was `$[*].id` and the command sent the following to stdout: 

```json
[
  {
    "id": "1085517466897181794",
    "name": "my-vm"
  }
]
```

Then then output would have the following contents:

```json
["1085517466897181794"]
```

---

## Examples

### Authenticate

```yaml
gcloud:
  description: "Authenticate"
  groups:
    - auth
  command: activate-service-account
  flags:
    key-file: gcloud.json
```

### Provision a VM

```yaml
gcloud:
  description: "Create VM"
  groups:
    - compute
    - instances
  command: create
  arguments:
    - porter-test
  flags:
    project: porterci
    zone: us-central1-a
    machine-type: f1-micro
    image: debian-9-stretch-v20190729
    image-project: debian-cloud
    boot-disk-size: 10GB
    boot-disk-type: pd-standard
    boot-disk-device-name: porter-test
  outputs:
    - name: vms
      jsonPath: "$[*].id"
```

### Configure SSH Keys

```yaml
gcloud:
  description: "Configure SSH"
  groups: compute
  command: config-ssh
  flags:
    ssh-config-file: ./gce-ssh-config
    ssh-key-file: ./gce-ssh-key
```
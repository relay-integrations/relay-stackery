# stackery-deploy

This [Stackery](https://www.stackery.io/) deployment step container deploys a
Stackery stack to a Stackery environment. It uses the CodeBuild strategy for Git
repositories that are linked to a Stackery environment and the local strategy
for Git repositories that are provided as part of the step specification.

## Specifications

| Setting | Child setting | Data type | Description | Default | Required |
|---------|---------------|-----------|-------------|---------|----------|
| `stackery` || mapping | A mapping of Stackery account configuration. | None | True |
|| `key` | string | The [CLI token](https://app.stackery.io/settings/cli-tokens) key to use. | None | True |
|| `secret` | string | The CLI token secret corresponding to the given key. | None | True |
| `aws` || mapping | A mapping of AWS account configuration. | None | True |
|| `accessKeyID` | string | An access key ID for the AWS account. You can pass the ID into Nebula as a secret. See the example below. | None | True |
|| `secretAccessKey` | string | The secret access key corresponding to the access key ID. Pass the key into Nebula as a secret. See the example below.| None | True |
|| `region` | string | The AWS region to use (for example, `us-west-2`). | None | True |
| `stackName` || string | The name of the Stackery stack to deploy. | None | True |
| `environmentName` || string | The name of the Stackery environment to deploy to. | None | True |
| `templatePath` || string | The path, within the given Git repository, to the SAM template file to build. | `container.yaml` | False |
| `git` || mapping | A map of git configuration. If you're using HTTPS, only `name` and `repository` are required. | None | False |
|| `ssh_key` | string | The SSH key to use when cloning the git repository. You can pass the key to Nebula as a secret. See the example below. | None | False |
|| `known_hosts` | string | SSH known hosts file. Use a Nebula secret to pass the contents of the file into the workflow as a base64-encoded string. See the example below. | None | False |
|| `name` | string | A directory name for the git clone. | None | False |
|| `branch` | string | The Git branch to clone or reference for the build. | `master` | False |
|| `repository` | string | The git repository URL. | None | False |
| `os` || mapping | Additional configuration for the step container's operating environment. | None | False |
|| `packages` | array | A list of additional [Alpine Linux packages](https://pkgs.alpinelinux.org/packages) to install. | None | False |
|| `commands` | array | A list of additional Bash commands to run before the step is executed. | None | False |

> **Note**: The value you set for a secret must be a string. If you have
> multiple key-value pairs to pass into the secret, or your secret is the
> contents of a file, you must encode the values using base64 encoding, and use
> the encoded string as the secret value.

## Limitations

This step does not currently support local builds that need a .NET Core runtime.

## Examples

### Using CodeBuild

```yaml
steps:
# ...
- name: stackery-deploy
  image: projectnebula/stackery-deploy
  spec:
    stackery:
      key:
        $type: Secret
        name: stackery-key
      secret:
        $type: Secret
        name: stackery-secret
    stackName: my-wonderful-stack
    environmentName: production
    aws:
      accessKeyID:
        $type: Secret
        name: aws-access-key-id
      secretAccessKey:
        $type: Secret
        name: aws-secret-access-key
      region: us-west-2
    git:
      branch: prod
```

### Building locally

```yaml
steps:
# ...
- name: stackery-deploy
  image: projectnebula/stackery-deploy
  spec:
    stackery:
      key:
        $type: Secret
        name: stackery-key
      secret:
        $type: Secret
        name: stackery-secret
    stackName: my-wonderful-stack
    environmentName: production
    aws:
      accessKeyID:
        $type: Secret
        name: aws-access-key-id
      secretAccessKey:
        $type: Secret
        name: aws-secret-access-key
      region: us-west-2
    git:
      ssh_key:
        $type: Secret
        name: ssh_key
      known_hosts:
        $type: Secret
        name: known_hosts
      name: deploy
      branch: master
      repository: git@github.com:stackery/react-single-page.git
```


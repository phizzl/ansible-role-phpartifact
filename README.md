# Ansible role phpartifact
A role to generate artifacts for PHP based applications

## Requirements
- Hosts should be bootstrapped for ansible usage (have python,...)

## Role Variables

| Variable | Description | Default value |
|----------|-------------|---------------|
| `phpartifact_php_executable` | Path to the php executable that is used during composer install | php |
| `phpartifact_composer_phar_file`| Path to the `composer.phar`. If it doesn't exist it will be instaled to the given location | "~/composer.phar" |
| `phpartifact_workdir`| Workdir for this role. The given repo will be cloned to the given location. | "/tmp/php_artifact_workdir" |
| `phpartifact_archivedir`| Path where the generated archives will be stored. | "~/phpartifact/archives" |
| `phpartifact_sources`| The artifact definitions. See below for details. | [] |


### `phpartifact_sources` details

This list contains the definitions for all artifacts to build

| Variable | Description | Required | Default |
|----------|-------------|----------|---------|
| `repo` | A GIT repository to clone the docker-compose setup from | yes | / |
| `version` | The version for the GIT repo to checkout | no | HEAD |
| `composer_install_directories` | List of directories to run the `composer install` command. See below for details. | no | [] |
| `key_file` | The private key file to use for GIT | no | ~/.ssh/id_rsa |
| `excludes` | List of files and directories to exclude from artifact | no |  [] |
| `keep_workdir` | Keep cloned repo after the artifact was built? | no |  no |
| `commands` | List of shell commands to execute after `composer install` | no |  / |


#### `composer_install_directories` details

| Variable | Description | Required | Default |
|----------|-------------|----------|---------|
| `path` | The path to execute the `composer install` command in | yes | / |
| `extraflags` | Extra flags like `--no-dev` to pass to the `composer install` command | no | "" |

## Dependencies

* phizzl.composer
* phizzl.archive

## Example playbook
```yaml
- name: Create artifact archive
  hosts: phpartifact
  roles:
    - role: phpartifact
      vars:
        phpartifact_sources:
          - repo: git@gitlab.myhost.dev.org:application/shop.git
            version: master
            composer_install_directories:
              - path: "."
                extraflags: "--optimize-autoloader --prefer-dist --no-dev --no-scripts"
              - path: "www"
                extraflags: "--optimize-autoloader --prefer-dist --no-dev --no-scripts"
            excludes:
              - ".docker/"
              - "Dockerfile"
              - "env/"
              - "docker-*.yml"
              - "init.*"
              - ".git/"
              - ".git*"
              - "tests/"
```

# Terraform State

[![Artifact](https://github.com/BadgerHobbs/terraform-state/actions/workflows/artifact.yml/badge.svg)](https://github.com/BadgerHobbs/terraform-state/actions/workflows/artifact.yml) [![Artifact Encrypted](https://github.com/BadgerHobbs/terraform-state/actions/workflows/artifact_encrypted.yml/badge.svg)](https://github.com/BadgerHobbs/terraform-state/actions/workflows/artifact_encrypted.yml) [![Repository File](https://github.com/BadgerHobbs/terraform-state/actions/workflows/repository_file.yml/badge.svg)](https://github.com/BadgerHobbs/terraform-state/actions/workflows/repository_file.yml) [![Repository File Encrypted](https://github.com/BadgerHobbs/terraform-state/actions/workflows/repository_file_encrypted.yml/badge.svg)](https://github.com/BadgerHobbs/terraform-state/actions/workflows/repository_file_encrypted.yml)

Terraform State is a GitHub Action that manages the storage of your Terraform state file as an (optionally) encrypted artifact or repository file. This makes it easier for you to handle your state file securely and efficiently within GitHub, not requiring a 3rd party service.

## Getting Started

Below you can find documentation on how to setup and use the `terraform-state` GitHub Action.

### Setup

The following inputs are used by the GitHub Action.

| Variable       | Description                                                                                                 | Required | Default |
| -------------- | ----------------------------------------------------------------------------------------------------------- | -------- | ------- |
| encryption_key | AES-256 Encryption key used to encrypt/decrypt the Terraform state file.                                    | False    | N/A     |
| operation      | Specifies if the operation is to download or upload the Terraform state file. Options: `download`, `upload` | True     | N/A     |
| location       | Specifies the storage location of the Terraform state file. Options: `repository`, `artifact`.              | True     | N/A     |
| directory      | Directory of the Terraform state file.                                                                      | False    | "."     |
| github_token   | GitHub Access Token used to retrieve latest artifact.                                                       | False    | N/A     |

It is recommended to use GitHub secrets to store the `encryption_key` and `github_token`.

### Usage

The following examples illustrates the best practices to use `terraform-state` to handle various scenarios of uploading and downloading a Terraform state file.

In addition, please note that while storing encrypted state within the repository ensures reasonable security, it is not recommended specifically for public repositories. Preferably, you should use artifacts. However, keep in mind that artifacts by default only last 90 days (can be changed in the repository settings).

When using storing the Terraform state within the repository, changes are commited to the current branch. To prevent endless loops when the GitHub Action is triggered to run on push, configure the following.

```yml
push:
  paths-ignore:
    - "**.tfstate"
    - "**.tfstate.encrypted"
```

#### Artifact

Please see thie [Example Workflow](.github/workflows/artifact.yml).

```yml
- name: Download Artifact
    uses: badgerhobbs/terraform-state@v1
    with:
        operation: download
        location: artifact
        github_token: ${{ secrets.gh_access_token }}
    continue-on-error: true

- name: Upload Artifact
    uses: badgerhobbs/terraform-state@v1
    with:
        operation: upload
        location: artifact
        github_token: ${{ secrets.gh_access_token }}
```

#### Artifact Encrypted

Please see thie [Example Workflow](.github/workflows/artifact_encrypted.yml).

```yml
- name: Download Encrypted Artifact & Decrypt Artifact
    uses: badgerhobbs/terraform-state@v1
    with:
        encryption_key: ${{ secrets.encryption_key }}
        operation: download
        location: artifact
        github_token: ${{ secrets.gh_access_token }}
    continue-on-error: true

- name: Encrypt Artifact & Upload Encrypted Artifact
    uses: badgerhobbs/terraform-state@v1
    with:
        encryption_key: ${{ secrets.encryption_key }}
        operation: upload
        location: artifact
        github_token: ${{ secrets.gh_access_token }}
```

#### Repository File

Please see thie [Example Workflow](.github/workflows/repository_file.yml).

```yml
- name: Commit Repository File
    uses: badgerhobbs/terraform-state@v1
    with:
        operation: upload
        location: repository
```

#### Repository File Encrypted

Please see thie [Example Workflow](.github/workflows/repository_file_encrypted.yml).

```yml
- name: Decrypt Repository File
    uses: badgerhobbs/terraform-state@v1
    with:
        encryption_key: ${{ secrets.encryption_key }}
        operation: download
        location: repository
    continue-on-error: true

- name: Encrypt and Commit Repository File
    uses: badgerhobbs/terraform-state@v1
    with:
        encryption_key: ${{ secrets.encryption_key }}
        operation: upload
        location: repository
```

## Acknowledgments

Despite different approaches, the development of this GitHub Action was influenced by the previous work of:

- [sturlabragason/terraform_state_artifact](https://github.com/sturlabragason/terraform_state_artifact)
- [devgioele/terraform-state-artifact](https://github.com/devgioele/terraform-state-artifact)

## License

The scripts and documentation in this project are released under the [MIT License](LICENSE).

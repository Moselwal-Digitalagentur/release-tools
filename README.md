# Moselwal Digitalagentur Release Tools

<details>
<summary>Table of Content</summary>

[TOC]

</details>

## Overview
Welcome to the Moselwal Digitalagentur Release Tools repository. This project provides a set of GitLab CI/CD components designed to streamline and automate the release process for various projects. The release tools are designed to be flexible, with configurable inputs to tailor the CI/CD pipeline to your specific needs.

## License
This program is free software: you can redistribute it and/or modify it under the terms of the GNU General Public License as published by the Free Software Foundation, either version 3 of the License, or (at your option) any later version.

This program is distributed in the hope that it will be useful, but WITHOUT ANY WARRANTY; without even the implied warranty of MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE. See the GNU General Public License for more details.

You should have received a copy of the GNU General Public License along with this program. If not, see <https://www.gnu.org/licenses/>.

## Components

### Component: release:cleanup
This job is responsible for cleanup the project by deleting RC, Alpha, and Beta tags and their associated releases after an stable release is published.

This job is executed when an MR is made from release to the default branch, e.g. main.
In our workflow, stable releases are created on the branch release.

#### Inputs

| **Input**                   | **Description**                                                                                     | **Example**                           | **Default Value**                                                      |
|-----------------------------|-----------------------------------------------------------------------------------------------------|---------------------------------------|------------------------------------------------------------------------|
| `stage`                     | (Optional) The stage in the CI/CD pipeline where this job will run.                                 | `release`                             | `release`                                                              |
| `rules-config`              | (Optional) Configuration rules that determine when the job should run.                             | See below for default rules           | See below for default rules                                            |
| `image-config`              | (Optional) The Docker image to use for the job. This should use semantic versioning.               | `registry.example.com/alpine:3.14`    | `${CI_DEPENDENCY_PROXY_GROUP_IMAGE_PREFIX}/alpine:3.19`                |
| `tags-config`               | (Optional) Tags used to select specific runners to execute the job.                                | `docker`, `release`                   | `[]`                                                                   |
| `additional-script-begin`   | (Optional) Add additional script at the beginning of the script.                                   | `echo "Start cleanup"`                | `''`                                                                   |
| `additional-script-end`     | (Optional) Add additional script at the end of the script.                                         | `echo "End cleanup"`                  | `''`                                                                   |

##### Default `rules-config`
```yaml
- if: $CI_MERGE_REQUEST_SOURCE_BRANCH_NAME == "release" && $CI_MERGE_REQUEST_TARGET_BRANCH_NAME == CI_DEFAULT_BRANCH
  when: always
```  

- **Stage**: $[[ inputs.stage ]]
- **Rules**: $[[ inputs.rules-config ]]
- **Image**: $[[ inputs.image-config | expand_vars ]]
- **Tags**: $[[ inputs.tags-config ]]

### Component: release:package
This job is responsible for packaging and releasing the project when a new tag is created.

#### Inputs

| **Input**                   | **Description**                                                                                     | **Example**                         | **Default Value**                                                      |
|-----------------------------|-----------------------------------------------------------------------------------------------------|-------------------------------------|------------------------------------------------------------------------|
| `stage`                     | (Optional) The stage in the CI/CD pipeline where this job will run.                                 | `release`                           | `release`                                                              |
| `rules-config`              | (Optional) Configuration rules that determine when the job should run.                             | See below for default rules         | See below for default rules                                            |
| `image-config`              | (Optional) The Docker image to use for the job. This should use semantic versioning.               | `registry.example.com/php/7.4/ci:latest` | `registry.moselwal.io/devops/images/php/$PHP_VERSION/ci:latest`         |
| `tags-config`               | (Optional) Tags used to select specific runners to execute the job.                                | `docker`, `release`                 | `[]`                                                                   |
| `additional-script-begin`   | (Optional) Add additional script at the beginning of the script.                                   | `echo "Start release"`              | `''`                                                                   |
| `additional-script-end`     | (Optional) Add additional script at the end of the script.                                         | `echo "End release"`                | `''`                                                                   |

##### Default `rules-config`
```yaml
- if: $CI_COMMIT_TAG != null
  when: always
```

- **Stage**: `$[[ inputs.stage ]]`
- **Rules**: `$[[ inputs.rules-config ]]`
- **Image**: `$[[ inputs.image-config | expand_vars ]]`
- **Tags**: `$[[ inputs.tags-config ]]`


### Component: release:semver
This job is responsible for handling the semantic versioning release process during the release stage.

#### Inputs

| **Input**                   | **Description**                                                                                     | **Example**                         | **Default Value**                                                      |
|-----------------------------|-----------------------------------------------------------------------------------------------------|-------------------------------------|------------------------------------------------------------------------|
| `stage`                     | (Optional) The stage in the CI/CD pipeline where this job will run.                                 | `release`                           | `release`                                                              |
| `rules-config`              | (Optional) Configuration rules that determine when the job should run.                             | See below for default rules         | See below for default rules                                            |
| `image-config`              | (Optional) The Docker image to use for the job. This should use semantic versioning.               | `registry.example.com/node:14-alpine` | `${CI_DEPENDENCY_PROXY_GROUP_IMAGE_PREFIX}/node:22-alpine3.19`         |
| `tags-config`               | (Optional) Tags used to select specific runners to execute the job.                                | `docker`, `release`                 | `[]`                                                                   |
| `additional-script-begin`   | (Optional) Add additional script at the beginning of the script.                                   | `echo "Start release"`              | `''`                                                                   |
| `additional-script-end`     | (Optional) Add additional script at the end of the script.                                         | `echo "End release"`                | `''`                                                                   |

##### Default `rules-config`
```yaml
- if: $CI_COMMIT_TAG
  when: never
- if: $CI_PIPELINE_SOURCE == "schedule"
  when: never
- if: $CI_COMMIT_REF_NAME != null && $CI_COMMIT_TAG == null && $CI_PIPELINE_SOURCE != "schedule"
```

- **Stage**: `$[[ inputs.stage ]]`
- **Rules**: `$[[ inputs.rules-config ]]`
- **Image**: `$[[ inputs.image-config | expand_vars ]]`
- **Tags**: `$[[ inputs.tags-config ]]`

#### Variables
- `GIT_SUBMODULE_STRATEGY`: `recursive`
- `GIT_SUBMODULE_DEPTH`: `1`

### Component: release:ter
This job is responsible for releasing the project to the TYPO3 Extension Repository (TER) when a new tag is created.

#### Inputs

| **Input**                   | **Description**                                                                                     | **Example**                         | **Default Value**                                                      |
|-----------------------------|-----------------------------------------------------------------------------------------------------|-------------------------------------|------------------------------------------------------------------------|
| `stage`                     | (Optional) The stage in the CI/CD pipeline where this job will run.                                 | `release`                           | `release`                                                              |
| `rules-config`              | (Optional) Configuration rules that determine when the job should run.                             | See below for default rules         | See below for default rules                                            |
| `image-config`              | (Optional) The Docker image to use for the job. This should use semantic versioning.               | `registry.example.com/composer:2`   | `${CI_DEPENDENCY_PROXY_GROUP_IMAGE_PREFIX}/composer:2`                 |
| `tags-config`               | (Optional) Tags used to select specific runners to execute the job.                                | `docker`, `release`                 | `[]`                                                                   |
| `additional-script-begin`   | (Optional) Add additional script at the beginning of the script.                                   | `echo "Start release"`              | `''`                                                                   |
| `additional-script-end`     | (Optional) Add additional script at the end of the script.                                         | `echo "End release"`                | `''`                                                                   |
| `typo3-api-token`           | (Required) TER API Token for publishing an extension.                                              | `${TYPO3_API_TOKEN}`                | `''`                                                                   |

##### Default `rules-config`
```yaml
- if: $CI_COMMIT_REF_PROTECTED == "true"
  changes:
    - CHANGELOG.md
    - .gitlab-ci.yml
  when: never
- if: $CI_COMMIT_TAG && $CI_COMMIT_BRANCH == "release"
  when: always
```

- **Stage**: $[[ inputs.stage ]]
- **Rules**: $[[ inputs.rules-config ]]
- **Image**: $[[ inputs.image-config | expand_vars ]]
- **Tags**: $[[ inputs.tags-config ]]

## Contributing
Please read about CI/CD components and best practices at: https://docs.gitlab.com/ee/ci/components    
We welcome contributions to improve the Moselwal Digitalagentur Release Tools. Please read our [Contributions Documentation](CONTRIBUTING.md)

## Support
For support, please contact [Moselwal Digitalagentur GmbH](mailto:support@moselwal.de).
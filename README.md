# GitHub action to publish npm packages with semanantic versioning rules

[![CI](https://github.com/iuccio/npm-semantic-publish-action/actions/workflows/ci.yml/badge.svg)](https://github.com/iuccio/npm-semantic-publish-action/actions/workflows/ci.yml)
[![CodeQL](https://github.com/iuccio/npm-semantic-publish-action/actions/workflows/codeql-analysis.yml/badge.svg)](https://github.com/iuccio/npm-semantic-publish-action/actions/workflows/codeql-analysis.yml)
[![Lint Codebase](https://github.com/iuccio/npm-semantic-publish-action/actions/workflows/linter.yml/badge.svg)](https://github.com/iuccio/npm-semantic-publish-action/actions/workflows/linter.yml)
[![Check Transpiled JavaScript](https://github.com/iuccio/npm-semantic-publish-action/actions/workflows/check-dist.yml/badge.svg)](https://github.com/iuccio/npm-semantic-publish-action/actions/workflows/check-dist.yml)
[![NPM Version](https://img.shields.io/npm/v/npm-semantic-publish.svg)](https://npmjs.org/package/npm-semantic-publish)
[![Downloads](https://img.shields.io/npm/dm/npm-semantic-publish.svg)](https://npmjs.org/package/npm-semantic-publish)
[![Coverage](badges/coverage.svg)](badges/coverage.svg)

[![Marketplace](https://img.shields.io/badge/GitHub_Action_-iuccio%2Fnpm--semantic--publish--action%40latest-2ea44f)](https://github.com/marketplace/actions/npm-semver-publish)

Follow [me](https://github.com/iuccio), and consider starring the project to
show your :heart: and support.

## Table of Contents

<!-- toc -->

- [GitHub action to publish npm packages with semanantic versioning rules](#github-action-to-publish-npm-packages-with-semanantic-versioning-rules)
  - [Table of Contents](#table-of-contents)
  - [Semantic versioning over commit message](#semantic-versioning-over-commit-message)
  - [Action Usage](#action-usage)
    - [Secrets Configuration](#secrets-configuration)
    - [Action configuration](#action-configuration)
      - [Step 1](#step-1)
      - [Step 2](#step-2)
      - [Step3](#step3)
      - [Step 4](#step-4)
    - [Complete job example](#complete-job-example)
    - [Action Parameters](#action-parameters)
  - [Development](#development)
  - [License](#license)
  - [Buy me a Coffee](#buy-me-a-coffee)

<!-- tocstop -->

This action allow your project to create a new release, based on
[semantic versionig](https://semver.org/) principles, and publish it to your npm
registry.

## Semantic versioning over commit message

Once your repository is configured with this action, to generate a new version
you have just to add to the commit message one of the following string:

- **[MAJOR]** or **[major]**: new major release, e.g. v1.0.0 -> v2.0.0
  - `git commit -m "add best feature ever [major]"`
- **[PATCH]** or **[patch]**: new patch release, e.g. v1.0.0 -> v1.0.1
  - `git commit -m "fix best feature ever [patch]"`
- without any of the above keywords a new minor release will be applied, e.g.
  v1.0.0 -> v1.1.0
  - `git commit -m "update best feature ever"`

An new release is only exeuted on the defined **target-branch** (see **Action
Usage**)

## Action Usage

### Secrets Configuration

This action requires the following Secrets:

1. **NPM_TOKEN**:
   1. generate a new npm token able to publish
      [Creating and viewing access tokens](https://docs.npmjs.com/creating-and-viewing-access-tokens)
   1. add the above generated token in the secret **NPM_TOKEN** (see
      [Using secrets in GitHub Actions](https://docs.github.com/en/actions/security-guides/using-secrets-in-github-actions))

### Action configuration

#### Step 1

Add permissions to to push on git and publish on npm

```yaml
permissions:
  contents: write
  id-token: write
```

#### Step 2

Add to the checkout action:

```yaml
uses: actions/checkout@v4
```

#### Step3

Add an [actions/setup-node](https://github.com/actions/setup-node) step to your
workflow. If you have one already, ensure that the registry-url input is set
(e.g. to [https://registry.npmjs.org](https://registry.npmjs.org)) so that this
action can populate your .npmrc with authentication info:

```yaml
uses: actions/setup-node@v4
with:
  node-version: 20.x
  registry-url: 'https://registry.npmjs.org'
```

#### Step 4

add **actions/npm-semver-publish** step:

```yaml
name: Run my Action
id: run-action
uses: iuccio/npm-semver-publish-action@v1.0.0
with:
  target-branch: 'master' #where a new release is applied
  provenance: true #if you want to publish on npm registry the provenance
env:
  NODE_AUTH_TOKEN: ${{ secrets.NPM_TOKEN }}
```

That's it!

### Complete job example

```yaml
on:
  push:
    branches: main

jobs:
  publish:
    runs-on: ubuntu-latest
    permissions:
      id-token: write
    steps:
      - name: Checkout
      id: checkout
      uses: actions/checkout@v4
      with:
        token: ${{ secrets.ACTION_TOKEN }}
      uses: actions/setup-node@v4
      with:
        node-version: 20.x
        registry-url: 'https://registry.npmjs.org'
    - name: Run my Action
      id: run-action
      uses: iuccio/npm-semver-publish-action@v1.0.0
      with:
        target-branch: master
        provenance: true
      env:
        NODE_AUTH_TOKEN: ${{ secrets.NPM_TOKEN }}
```

### Action Parameters

See [action.yml](action.yml)

|     Name      |  Type  | Default |                Description                 |
| :-----------: | :----: | :-----: | :----------------------------------------: |
| target-branch | string | master  | Branch name new release should be executed |
|  provenance   | string |  false  |           NPM package provenance           |

When the Action Parameter **provenance** is set to true the **id-token**
permission must be set to **write**:

```yaml
permissions:
  id-token: write
```

## Development

See [Development](DEVELOPMENT.md) for more information.

## License

CSVtoJSON is licensed under the GNU General Public License v3.0
[License](LICENSE).

## Buy me a Coffee

Just if you want to support this repository:

- **BTC** tip address: 37vdjQhbaR7k7XzhMKWzMcnqUxfw1njBNk

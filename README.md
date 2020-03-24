# Slate Documenation Builder Action
GitHub action to build repositories Markdown files using the [slate framework](https://github.com/slatedocs/slate)

**Table of Contents**

- [Slate Documenation Builder Action](#slate-documenation-builder-action)
  - [Getting Started](#getting-started)
  - [Using as GitHub Action](#using-as-github-action)
    - [Settings for v2.0.0+ release](#settings-for-v200-release)
      - [Advanced configuration](#advanced-configuration)
        - [Get the Result](#get-the-result)

## Getting Started
The docker image can be used to locally build your markdown files. For this you need, first of all, to build the docker image:

```bash
docker build -t whisperlab/slate-builder .
```

Once built run the container using the following command:

```bash
docker run -it --rm -v <md files folder>:/usr/src/doc whisperlab/slate-builder
```

The slate build result will be stored into the build subdir of `<md files folder>`.

## Using as GitHub Action

### Settings for v2.0.0+ release

Create a file into your root project directory: .github/workflows/slate-builder-action.yml:

```
on:
  push:
    branches:
      - master

name: Create Slate Documentation on Push
jobs:
  slate-documentation:
    runs-on: ubuntu-18.04
    steps:
    - uses: actions/checkout@master
    - name: Build documentation
      uses: whisperlab/slate-builder-action@v3.0.1
      env:
        DOC_BASE_FOLDER = "."
```

The `DOC_BASE_FOLDER` environement variable is the path, in your gitHub repository, containing the Markdown files you want to build as Slate website.

#### Advanced configuration
You can override the default Slate template, providing a repository containing your custom slate template.

```
on:
  push:
    branches:
      - master

name: Create Slate Documentation on Push
jobs:
  slate-documentation:
    runs-on: ubuntu-18.04
    steps:
    - uses: actions/checkout@master
    - name: Build documentation
      uses: whisperlab/slate-builder-action@v3.0.1
      env:
        GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
        DOC_BASE_FOLDER = "."
        TEMPLATE_REPO = "whisperlab/custom-slate"
```

The `GITHUB_TOKEN` secrets variable contains the [Private Access Token](https://help.github.com/en/articles/creating-a-personal-access-token-for-the-command-line). This secret is mandatory if you want to access to a private repository but you can leave it empty if the template you want to use is opensource.

##### Get the Result
The result is built into `DOC_BASE_FOLDER/build` folder. You have to chain this action with one taking care about deployment, delivery, or whatever you want to do with your built documentation.
To simplify the export, the result is zipped into `DOC_BASE_FOLDER/documentation.zip`. You can disable the zipping function providing `ZIP_BUILD=false` as environment variable.

```
on:
  push:
    branches:
      - master
name: Create Slate Documentation on Push
jobs:
  slate-documentation:
    runs-on: ubuntu-18.04
    steps:
    - uses: actions/checkout@master
    - name: Build documentation
      uses: whisperlab/slate-builder-action@v3.0.1
      env:
        DOC_BASE_FOLDER = "."
        ZIP_BUILD = "false"
```

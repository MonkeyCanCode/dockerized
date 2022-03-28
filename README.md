![Dockerized](dockerized-banner.png)

# Dockerized [![Compile and Test](https://github.com/datastack-net/dockerized/actions/workflows/test.yml/badge.svg)](https://github.com/datastack-net/dockerized/actions/workflows/test.yml) 

Run popular commandline tools without installing them.

```shell
dockerized <command>
```

![demo](terminalizer.gif)

## Supported commands

> If your favorite command is not included, it can be added very easily. See [Add a command](DEV.md).  
> Dockerized will also fall back to over 150 commands defined in [jessfraz/dockerfiles](https://github.com/jessfraz/dockerfiles).

- Cloud
  - [aws](apps/aws/README.md)
  - [az](apps/az/README.md) (Azure CLI)
  - [doctl](apps/doctl/README.md)
  - [s3cmd](apps/s3cmd/README.md)
- Dev-Ops & Docker
  - ansible
    - ansible-playbook
  - helm
- Git
  - git 
  - [gh](apps/gh/README.md) (Github)
- Languages & SDKs
  - [dotnet](apps/dotnet/README.md)
  - go
  - (haskell)
    - ghci
  - java
  - perl
  - php
  - lua
  - node
    - [npm](apps/npm/README.md)
    - npx
    - vue
    - yarn
  - python
    - pip
    - python
    - python2
  - ruby
    - gem
    - rake
  - (rust)
    - rustc
  - (prolog)
    - swipl (SWI-Prolog)
  - typescript
    - tsc
- Networking:
  - http
  - telnet
  - wget
- Unix
  - tree
  - zip
- Other utilities
  - youtube-dl (Youtube downloader)
  - jq
  - (latex)
    - pdflatex
  - protoc
  - swagger-codegen

## Installation

- Make sure [Docker](https://docs.docker.com/get-docker/) is installed on your machine.
- Download the latest zip from our [Release Page](https://github.com/datastack-net/dockerized/releases).
  - Extract it to a folder, for example your home directory.
- Add the `dockerized/bin` directory to your `PATH`  
   <details>
   <summary>Instructions</summary>
  
     **Linux / MacOS:**

     ```bash
     export PATH="$PATH:$HOME/dockerized/bin"
     ```
     **Windows**

     > See: [How to add a folder to `PATH` environment variable in Windows 10](https://stackoverflow.com/questions/44272416)
  
   </details>

### Running from source

You can run dockerized directly from source-code.

<details>
<summary>Installation from Source
</summary>

- Requirements:
  - [Git](https://git-scm.com/downloads)
  - [Docker](https://docs.docker.com/get-docker/)
- Steps
  - Clone the repository
    ```shell
    git clone https://github.com/datastack-net/dockerized.git
    ```
  - Run `dockerized`:  
    ```shell
    bin/dockerized --help
    ```

    - The first time you run dockerized, it will compile itself.
      - Compilation is done within docker, no dependencies needed.
    - To re-compile after changing the source code, run:
      - `dockerized --compile` (runs within docker)
      - `dockerized --compile=host` (runs on your machine, requires Go 1.17+)
    - You do not need to re-compile to add commands. See [Add a command](DEV.md).

</details>


## Usage

Run any supported command, but within Docker.

```shell
dockerized <command>
```

Examples:

```bash
dockerized node --version             # v16.13.0
dockerized vue create new-project     # create a project with vue cli
dockerized tsc --init                 # initialize typescript for the current directory
dockerized npm install                # install packages.json
```

## Use Cases

- Quickly try out command line tools without the effort of downloading and installing them.
- Installing multiple versions of node/python/typescript.
- You need unix commands on Windows.
- You don't want to pollute your system with a lot of tools you don't use.
- Easily update your tools.
- Ensure everyone on the team is using the same version of commandline tools.

## Design Goals

- All commands work out of the box.
- Dockerized commands behave the same as their native counterparts.
  - Files in the current directory are accessible using relative paths.
- Cross-platform: Works on Linux, MacOS, and Windows (CMD, Powershell, Git Bash).
- Suitable for ad-hoc usage (i.e. you quickly need to run a command, that is not on your system).
- Configurability: for use within a project or CI/CD pipeline.

## Switching command versions 

**Ad-hoc**

Add `:<version>` to the end of the command to override the version.

```shell
dockerized node:15
```


**Listing versions**

To see which versions are available, run:

```shell
dockerized node:?
# or
dockerized node:
```

**Environment Variables**

Each command has a `<COMMAND>_VERSION` environment variable which you can override.

- `python`: `PYTHON_VERSION`
- `node`: `NODE_VERSION`
- `tsc`: `TSC_VERSION`

Notes:
- Versions of some commands are determined by other commands.  
  For example, to configure the version of `npm`, you should override `NODE_VERSION`.
- See [.env](.env) for a list of configurable versions.



**Global**

- Create a `dockerized.env` file in your home directory for global configuration.   

    ```shell
    # dockerized.env (example)
    NODE_VERSION=16.13.0
    PYTHON_VERSION=3.8.5
    TYPESCRIPT_VERSION=4.6.2
    ```
  
- List of configuration variables, and defaults:
  - [.env](.env)


**Per directory**

You can also specify version and other settings per directory.
This allows you to "lock" your tools to specific versions for your project.

- Create a `dockerized.env` file in your project directory.
- All commands executed within this directory will use the settings specified in this file.


**Ad-hoc (Unix)**

- Override the environment variable before the command, to specify the version for that command.

    ```shell
    NODE_VERSION=15.0.0 dockerized node
    ```

**Ad-hoc (Windows Command Prompt)**

- Set the environment variable in the current session, before the command.

    ```cmd
    set NODE_VERSION=15.0.0
    dockerized node
    ```

## Localhost

Dockerized applications run within an isolated network. To access services running on your machine, you need to use `host.docker.internal` instead of `localhost`. 

```shell
dockerized telnet host.docker.internal 8080 # instead of telnet localhost 8080
```

## Limitations

- It's not currently possible to access parent directories. (i.e. `dockerized tree ../dir` will not work)
  - Workaround: Execute the command from the parent directory. (i.e. `cd .. && dockerized tree dir`)
- Commands will not persist changes outside the working directory, unless specifically supported by `dockerized`.

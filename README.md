# Template for Tensorflow Projects in Haskell (GPU)
This template contains the basis for a [TensorFlow](https://www.tensorflow.org/) project in [Haskell](https://www.haskell.org/), which runs on a Nvidia GPU.

> *Note*: as [tensorflow/haskell](https://github.com/tensorflow/haskell) only supports [TensorFlow v2.3.0](https://github.com/tensorflow/tensorflow/releases/tag/v2.3.0), Nvidia's Ampere architectures (3000 series) are not supported (See https://github.com/tensorflow/haskell/issues/280). Running on this gpu template on a 3000 series will raise `TensorFlowException TF_INTERNAL "CUDA runtime implicit initialization on GPU:0 failed. Status: device kernel image is invalid"`.

## Features
The [Ubuntu 18.04](https://releases.ubuntu.com/18.04/) development container is built from the [tensorflow/haskell Dockerfile](https://github.com/tensorflow/haskell/blob/master/docker/Dockerfile) with the following additions:

- [OpenSSH 9.0](https://www.openssh.com/txt/release-9.0).
- Support for git ssh [commit signing](https://docs.github.com/en/authentication/managing-commit-signature-verification/signing-commits).
- Includes [GHCup](https://www.haskell.org/ghcup/), [Stack](https://docs.haskellstack.org/en/stable/) and [Cabal](https://www.haskell.org/cabal/).
- Preconfigured [Haskell VSCode extension](https://marketplace.visualstudio.com/items?itemName=haskell.haskell), with [HLint](https://hackage.haskell.org/package/hlint-1.7/src/hlint.htm) syntax highlighting and type tooltips.
- To speed up succeeding builds, stores [Stack](https://docs.haskellstack.org/en/stable/) build cache in a volume.

## Installation
To get started with this template, follow the instructions below.

### Prerequisites
- [VSCode](https://code.visualstudio.com/)
    - With the [Dev Containers extension](https://marketplace.visualstudio.com/items?itemName=ms-vscode-remote.remote-containers) installed.
- [Docker](https://www.docker.com/)

### Cloning the Repository
This repository uses [git submodules](https://git-scm.com/book/en/v2/Git-Tools-Submodules) for the [tensorflow/haskell](https://github.com/tensorflow/haskell) dependencies. To include the submodules when cloning the repository, execute the following command.
```
git clone --recurse-submodules <repository-url>
```
If the repository was cloned without recursing over the submodules, execute the following command instead.
```
git submodule update --init --recursive
```

> Slack does not support referencing repositories with [submodules](https://git-scm.com/book/en/v2/Git-Tools-Submodules). The depedency [tensorflow/haskell](https://github.com/tensorflow/haskell) uses [submodules](https://git-scm.com/book/en/v2/Git-Tools-Submodules). So, this template uses submodules too.

### Running the project
Open the repository folder in VSCode. If the [Dev Containers extension](https://marketplace.visualstudio.com/items?itemName=ms-vscode-remote.remote-containers) is installed, VSCode will prompt to re-open the folder in the development container. To manually instruct VSCode to do so, hit `Ctrl + Shift + P` and select `> Remote-Containers: Rebuild Container`. Once VSCode is opened inside the container, when clicking a `.hs` file, the [Haskell VSCode extension](https://marketplace.visualstudio.com/items?itemName=haskell.haskell) will prompt to install the required [Haskell ToolChain](https://www.haskell.org/ghcup/install/#supported-tools). After clicking yes, [Ghcup](https://www.haskell.org/ghcup/) will build the project. Once finished, [HLint](https://hackage.haskell.org/package/hlint-1.7/src/hlint.htm) syntax highlighting and type tooltips will be available. To check the build status, navigate to `Output` and select the `Haskell` output.

> If a file is highlighted with `Please ensure that ghcide is compiled with the same GHC`, running `stack build` at the project root and reloading the window may resolve the issue.

### Verifing the Installation
To verify the template installation, run the tensorflow example included in the [tensorflow/haskell](https://github.com/tensorflow/haskell) submodule.
```
cd extra-deps/tensorflow-mnist
stack run
```

#### Setting up GitHub SSH Authentication & SSH Signing
The dev container supports GitHub SSH Authentication and GitHub SSH Signing.

In order to enable this, perform the steps below.

Setup WSL2 with Ubuntu as follows:
1. Create a `~/.ssh` folder with the following files.
    - Add github ssh private and public key to `~/.ssh/<KEYNAME>`, `~/.ssh/<KEYNAME>.pub`
    - Add config file to `~/.ssh/config` with the following contents
        ```
        Host github.com
            user git
            IdentityFile ~/.ssh/<KEYNAME>
        ```
2. Install packages `keychain`.
3. Create `~/.bash_profile` and add the following line: 
    ```
    eval `keychain --eval --agents ssh github_30112021`. 
    ```
4. Setup `~/.gitconfig` as follows
    ```
    [user]
        email = <EMAIL>
        name = <NAME>
        signingkey = <CONTENT OF PUBLIC KEY (e.g. ssh-ed25519 AAAAC3(...) user@example.com)>
    [commit]
        gpgsign = true
    [gpg]
        format = ssh
    [tag]
        gpgsign = true
    ```

## Resources
The resources below may help when starting with [tensorflow/haskell](https://github.com/tensorflow/haskell) for the first time.

- [tensorflow/haskell 0.3.0.0 documentation](https://tensorflow.github.io/haskell/haddock/)
    - [core](https://tensorflow.github.io/haskell/haddock/tensorflow-0.3.0.0/TensorFlow-Core.html)
    - [built-in TensorFlow operations](https://tensorflow.github.io/haskell/haddock/tensorflow-ops-0.3.0.0/TensorFlow-Ops.html)
- [tensorflow 2.3.0 python documentation](https://www.tensorflow.org/versions/r2.3/api_docs/python/tf/math/argmax) for reference, [tensorflow/haskell](https://github.com/tensorflow/haskell) implementation does not exactly match.
- [Monday Morning Haskell - Haskell and Tensor Flow](https://mmhaskell.com/machine-learning/tensorflow) for a small introduction to TensorFlow and Haskell.
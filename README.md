# devcontainer-cli

devcontainer-cli is the start of a CLI to improve the experience of working with [Visual Studio Code devcontainers](https://code.visualstudio.com/docs/remote/containers)

**Status: this is a pet project that I've been experimenting with. It is not supported and you should expect bugs :-)**

**NOTE: To avoid conflicts with the [official CLI](https://github.com/devcontainers/cli) the binary for this project has been renamed to `devcontainerx`**

## Installation

Head to the [latest release page](https://github.com/stuartleeks/devcontainer-cli/releases/latest) and download the archive for your platform.

Extract `devcontainerx` from the archive and place in a folder in your `PATH`.

You can also install using `homebrew` with `brew install stuartleeks/tap/devcontainer`

Or if you just don't care and are happy to run random scripts from the internet:

```bash
export OS=linux # also darwin
export ARCH=amd64 # also 386
wget https://raw.githubusercontent.com/stuartleeks/devcontainer-cli/main/scripts/install.sh
chmod +x install.sh
sudo -E ./install.sh
```

## Enabling bash completion

To enable bash completion, add the following to you `~/.bashrc` file:

```bash
source <(devcontainerx completion bash)
```

Or to alias `devcontainerx` (to `dcx` in this example):

```bash
alias dcx=devcontainerx
complete -F __start_devcontainerx dcx
```

## Docs

See [the documentation](https://stuartleeks.github.io/devcontainer-cli)  on how to work with `devcontainerx`.

---
title: Installing Node.js with `nvm` to Linux & macOS & WSL
---

A quick guide on how to setup Node.js development environment.


## Install `nvm` for managing Node.js versions

[nvm](https://github.com/nvm-sh/nvm) allows installing several versions of Node.js to the same system. Sometimes applications require a certain versions of Node.js to work. Having the flexibility of using specific versions can help.

1. Open new _Terminal_ window.
2. Run [nvm](https://github.com/nvm-sh/nvm) installer. (If you already had it, remember to update to more recent versions.)
   - … with _either_ `curl` *or* `wget`, depending on what your computer has available.
     - `curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.7/install.sh | bash`
     - `wget -qO- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.7/install.sh | bash`
   - The script clones the nvm repository to `~/.nvm` and adds the source line to your profile (`~/.bash_profile`, `~/.zshrc,` `~/.profile,` or `~/.bashrc`). (You can add the source loading line manually, if the automated install tool does not add it for you.)

     ```sh
     export NVM_DIR="$HOME/.nvm"
     [ -s "$NVM_DIR/nvm.sh" ] && \. "$NVM_DIR/nvm.sh"  # This loads nvm
     [ -s "$NVM_DIR/bash_completion" ] && \. "$NVM_DIR/bash_completion"  # This loads nvm bash_completion
     ```

   - Another option: when you have consistent directory location between systems, following example Bash/Zsh configuration allows to load `nvm` when the directory exists.
   This allows more consistent sharing of your shell configuration between systems, improving reliability of rest of your configuration even when nvm does not exist on a specific system.

     ```sh
     if [ -d "$HOME/.nvm" ]; then
       # export NVM_DIR="$([ -z "${XDG_CONFIG_HOME-}" ] && printf %s "${HOME}/.nvm" || printf %s "${XDG_CONFIG_HOME}/nvm")"
       export NVM_DIR="$HOME/.nvm"

       # This loads nvm
       [ -s "$NVM_DIR/nvm.sh" ] && \. "$NVM_DIR/nvm.sh"

       # This loads nvm bash_completion
       [ -s "$NVM_DIR/bash_completion" ] && \. "$NVM_DIR/bash_completion"
     fi
     ```

3. If everything went well, you should now either open a new Terminal window/tab, or reload the shell configuration by running:
   - `source ~/.bashrc`
4. Verify installation
   - To check if nvm command got installed, run:
     - `command -v nvm`

## Install Node.js with `nvm`

1. List installed Node.js versions with:
   - `nvm ls`
2. **Install latest LTS Version of [Node.js](https://nodejs.org/en/)** (for production quality applications)
   - `nvm install v20.11.1`
3. Install latest [Node.js](https://nodejs.org/en/) Current release (for testing new feature improvements)
   - `nvm install v21.6.2`
4. If you want to change the default Node version later, you can run a command to adjust it.
    - `nvm alias default v20.11.1` [changelog](https://github.com/nodejs/node/blob/main/doc/changelogs/CHANGELOG_V20.md#20.11.1) (for _production quality_ applications)
    - `nvm alias default v21.6.2` [changelog](https://github.com/nodejs/node/blob/main/doc/changelogs/CHANGELOG_V21.md#21.6.2) (if you use Node.js features from the _Current_ release)

You can select Node.js version by running `nvm use v20.11.1` (or another version number). Another alternative: create a small Bash shell script to enable the right environment variables for your project.

Read the Node.js [Long Term Support (LTS) schedule](https://nodejs.org/en/about/releases/ "Releases | Node.js") to have more understanding of their release roadmap. List of all [previous releases](https://nodejs.org/en/download/releases/ "Previous Releases | Node.js") is also useful for finding details about Node.js release history.

[npm](https://www.npmjs.com/) package repository has a lot of packages to discover.
Have a good time with the freshly installed tools.

## Migrating packages from a previous Node.js version

If you already have existing Node.js version via `nvm`, you can migrate older packages from the installed Node.js versions.

- Open new Terminal window (to make sure you have latest Node.js version active in your command line environment).
- Before running next commands, remember to switch to the right version of Node with `nvm use` command.
  For example:
  - `nvm use v20.11.1`
  - `nvm use v21.6.2`
- Linking global packages from previous version:
  - `nvm reinstall-packages v20.3.1`
  - `nvm reinstall-packages v18.16.1`

### Deleting old Node.js versions

- Check installed Node.js versions with:
  - `nvm ls`
- Delete an older version (if you don't use it in some of your projects):
  - `nvm uninstall v20.3.1`
  - `nvm uninstall v18.16.1`

---

### Updating outdated packages

> _Warning:_ Make sure you have latest `nvm` version installed, just in case. Update instructions of it in the beginning of this article. [nvm `v0.39.2`](https://github.com/nvm-sh/nvm/releases/tag/v0.39.2) version of  fixes potential problems that might cause older node environments to break if accidentally installing `npm v9` to those.

#### List of globally installed top level packages

```sh
npm ls -g --depth=0.
```

#### List outdated global packages

```sh
npm outdated -g --depth=0.
```

#### Updating all globally installed npm packages

> _Warning:_ **This might be too risky approach.** Instead, it is better to update packages individually, as that avoids the risk of accidentally updating `npm` package to incompatible versions. `npm v9` installation to older incompatible Node.js environments is known to cause problems (potentially breaking down the development environment), so it's better be safe than sorry.

```sh
npm update -g
```

---

#### CLI aliases for Bash & Zsh environments

Example configuration for your Bash & Zsh command line environments.

```sh

# -----------------------------------------------------------
# npm helpers
# -----------------------------------------------------------

# List what (top level) packages are installed globally
alias list-installed-npm-packages="npm ls -g --depth=0."

# List what globally installed packages are outdated
alias list-outdated-npm-packages="npm outdated -g --depth=0."

# Update outdated globally installed npm packages
alias update-npm-packages="npm update -g"

```


#### Fixing old package versions

If you have older npm packages with compiled native extensions, recompiling native extensions can improve compatibility with the new Node.js version. Go to your project’s root directory, and run `npm rebuild` command.

```sh
cd PROJECT_NAME
npm rebuild
```

---

## Notes about this documentation

[@d2s](https://github.com/d2s "GitHub profile of Daniel Schildt") tested older versions of these install instructions with:

- [Debian 11.2](https://www.debian.org/News/2021/20211218)
- [Debian 10](https://www.debian.org/News/2019/20190706)
- [Ubuntu on WSL2 (Windows Subsystem for Linux)](https://docs.microsoft.com/en-us/windows/wsl/about)
- [Ubuntu 20.04 LTS](http://releases.ubuntu.com/bionic/)
- [Ubuntu 22.04 LTS](https://releases.ubuntu.com/jammy/)

## Contributions

If you have improvement suggestions to make these instructions simpler & better, post a comment under the [original Gist by @d2s](https://gist.github.com/d2s/372b5943bce17b964a79 "Installing Node.js to Linux & macOS & WSL with nvm") with your documentation improvement suggestions. If you are reading a forked version of the document, check the original Gist for a more recent instructions.
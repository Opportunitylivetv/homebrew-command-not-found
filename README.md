# homebrew-command-not-found

[![Build Status](https://travis-ci.org/Homebrew/homebrew-command-not-found.svg?branch=master)](https://travis-ci.org/Homebrew/homebrew-command-not-found)

This project reproduces Ubuntu’s `command-not-found` for Homebrew users on
macOS.

On Ubuntu, when you try to use a command that doesn’t exist locally but is
available through a package, Bash will suggest you a command to install it.
Using this script, you can replicate this feature on macOS:

```
# on Ubuntu
$ when
The program 'when' is currently not installed.  You can install it by typing:
sudo apt-get install when

# on macOS with Homebrew
$ when
The program 'when' is currently not installed. You can install it by typing:
  brew install when
```

Over 5900 formulae are supported, representing more than 21000 different commands
(100% of the main Homebrew repo + 99.9% of all official taps).

## Install

First, tap this repository:

```bash
brew tap homebrew/command-not-found
```

* **Bash and Zsh**: Add the following line to your `~/.bash_profile` (bash) or `~/.zshrc` (zsh):

    ```bash
    if brew command command-not-found-init > /dev/null 2>&1; then eval "$(brew command-not-found-init)"; fi
    ```

* **Fish**: Add the following line to your `~/.config/fish/config.fish`:

    ```fish
    brew command command-not-found-init > /dev/null 2>&1; and . (brew command-not-found-init)
    ```

## Requirements

This tool requires one of the following:

* [Bash](https://www.gnu.org/software/bash/) (version 4 and higher)
* [Fish](https://fishshell.com)
* [Zsh](https://www.zsh.org)

macOS ships Bash 3.x so you must upgrade to v4.x and configure it to be used with:

```bash
brew update && brew install bash
# Add the new shell to the list of allowed shells
sudo bash -c 'echo /usr/local/bin/bash >> /etc/shells'
# Change to the new shell
chsh -s /usr/local/bin/bash
```

## How does it work?

When you tap the repo you’ll get two more `brew` commands: `brew which-formula`
and `brew which-update`. The first one uses a database file to gives you which
formula you have to install in order to get a specific command. The file is
generated by the second command by crawling all installed formulae and
collecting their binaries. Having this as a tap means you get an up-to-date
binaries database each time you run `brew update`.

The `handler.sh` script defines a `command_not_found_handle` function which is
used by Bash when you try a command that doesn’t exist. The function calls
`brew which-formula` on your command, and if it finds a match it’ll print it to
you. If not, you’ll get an error as expected.

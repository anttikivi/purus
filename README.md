# Purus

> Pretty, minimal and fast ZSH prompt

## Overview

Most prompts are cluttered, ugly and slow. We wanted something visually pleasing that stayed out of our way.

### Why?

- Comes with the perfect prompt character.
  Author went through the whole Unicode range to find it.
- Shows `git` branch and whether it's dirty (with a `*`).
- Indicates when you have unpushed/unpulled `git` commits with up/down arrows. _(Check is done asynchronously!)_
- Prompt character turns red if the last command didn't exit with `0`.
- Command execution time will be displayed if it exceeds the set threshold.
- Username and host only displayed when in an SSH session or a container.
- Shows the current path in the title and the current folder & command when a process is running.
- Support VI-mode indication by reverse prompt symbol (Zsh 5.3+).
- Makes an excellent starting point for your own custom prompt.

## Install

1. Clone this repo somewhere. Here we'll use `$HOME/.zsh/purus`.

```sh
mkdir -p "$HOME/.zsh"
git clone https://github.com/sindresorhus/purus.git "$HOME/.zsh/purus"
```

2. Add the path of the cloned repo to `$fpath` in `$HOME/.zshrc`.

```sh
# .zshrc
fpath+=($HOME/.zsh/purus)
```

## Getting started

Initialize the prompt system (if not so already) and choose `purus`:

```sh
# .zshrc
autoload -U promptinit; promptinit
prompt purus
```

## Options

| Option                            | Description                                                                                    | Default value  |
| :-------------------------------- | :--------------------------------------------------------------------------------------------- | :------------- |
| **`PURUS_CMD_MAX_EXEC_TIME`**     | The max execution time of a process before its run time is shown when it exits.                | `5` seconds    |
| **`PURUS_GIT_PULL`**              | Prevents Purus from checking whether the current Git remote has been updated.                  | `1`            |
| **`PURUS_GIT_UNTRACKED_DIRTY`**   | Do not include untracked files in dirtiness check. Mostly useful on large repos (like WebKit). | `1`            |
| **`PURUS_GIT_DELAY_DIRTY_CHECK`** | Time in seconds to delay git dirty checking when `git status` takes > 5 seconds.               | `1800` seconds |
| **`PURUS_PROMPT_SYMBOL`**         | Defines the prompt symbol.                                                                     | `❯`            |
| **`PURUS_PROMPT_VICMD_SYMBOL`**   | Defines the prompt symbol used when the `vicmd` keymap is active (VI-mode).                    | `❮`            |
| **`PURUS_GIT_DOWN_ARROW`**        | Defines the git down arrow symbol.                                                             | `⇣`            |
| **`PURUS_GIT_UP_ARROW`**          | Defines the git up arrow symbol.                                                               | `⇡`            |
| **`PURUS_GIT_STASH_SYMBOL`**      | Defines the git stash symbol.                                                                  | `≡`            |

## Zstyle options

Showing git stash status as part of the prompt is not activated by default. To activate this you'll need to opt in via `zstyle`:

`zstyle :prompt:purus:git:stash show yes`

You can set Purus to only `git fetch` the upstream branch of the current local branch. In some cases, this can result in faster updates for Git arrows, but for most users, it's better to leave this setting disabled. You can enable it with:

`zstyle :prompt:purus:git:fetch only_upstream yes`

`nix-shell` integration adds the shell name to the prompt when used from within a nix shell. It is enabled by default, you can disable it with:

`zstyle :prompt:purus:environment:nix-shell show no`

## Colors

As explained in ZSH's [manual](http://zsh.sourceforge.net/Doc/Release/Zsh-Line-Editor.html#Character-Highlighting), color values can be:

- A decimal integer corresponding to the color index of your terminal. If your `$TERM` is `xterm-256color`, see this [chart](https://upload.wikimedia.org/wikipedia/commons/1/15/Xterm_256color_chart.svg).
- The name of one of the following nine colors: `black`, `red`, `green`, `yellow`, `blue`, `magenta`, `cyan`, `white`, and `default` (the terminal’s default foreground)
- `#` followed by an RGB triplet in hexadecimal format, for example `#424242`. Only if your terminal supports 24-bit colors (true color) or when the [`zsh/nearcolor` module](http://zsh.sourceforge.net/Doc/Release/Zsh-Modules.html#The-zsh_002fnearcolor-Module) is loaded.

Colors can be changed by using [`zstyle`](http://zsh.sourceforge.net/Doc/Release/Zsh-Modules.html#The-zsh_002fzutil-Module) with a pattern of the form `:prompt:purus:$color_name` and style `color`. The color names, their default, and what part they affect are:

- `execution_time` (yellow) - The execution time of the last command when exceeding `PURUS_CMD_MAX_EXEC_TIME`.
- `git:arrow` (cyan) - For `PURUS_GIT_UP_ARROW` and `PURUS_GIT_DOWN_ARROW`.
- `git:stash` (cyan) - For `PURUS_GIT_STASH_SYMBOL`.
- `git:branch` (242) - The name of the current branch when in a Git repository.
- `git:branch:cached` (red) - The name of the current branch when the data isn't fresh.
- `git:action` (242) - The current action in progress (cherry-pick, rebase, etc.) when in a Git repository.
- `git:dirty` (218) - The asterisk showing the branch is dirty.
- `host` (242) - The hostname when on a remote machine.
- `path` (blue) - The current path, for example, `PWD`.
- `prompt:error` (red) - The `PURUS_PROMPT_SYMBOL` when the previous command has _failed_.
- `prompt:success` (magenta) - The `PURUS_PROMPT_SYMBOL` when the previous command has _succeeded_.
- `prompt:continuation` (242) - The color for showing the state of the parser in the continuation prompt (PS2). It's the pink part in [this screenshot](https://user-images.githubusercontent.com/147409/70068574-ebc74800-15f8-11ea-84c0-8b94a4b57ff4.png), it appears in the same spot as `virtualenv`. You could for example matching both colors so that Purus has a uniform look.
- `suspended_jobs` (red) - The `✦` symbol indicates that jobs are running in the background.
- `user` (242) - The username when on remote machine.
- `user:root` (default) - The username when the user is root.
- `virtualenv` (242) - The name of the Python `virtualenv` when in use.

The following diagram shows where each color is applied on the prompt:

```
┌────────────────────────────────────────────────────── user
│      ┌─────────────────────────────────────────────── host
│      │           ┌─────────────────────────────────── path
│      │           │          ┌──────────────────────── git:branch
│      │           │          │     ┌────────────────── git:dirty
│      │           │          │     │ ┌──────────────── git:action
│      │           │          │     │ │        ┌─────── git:arrow
│      │           │          │     │ │        │ ┌───── git:stash
│      │           │          │     │ │        │ │ ┌─── execution_time
│      │           │          │     │ │        │ │ │
antti@macbook-air ~/dev/purus master* rebase-i ⇡ ≡ 42s
venv ❯
│    │
│    └───────────────────────────────────────────────── prompt
└────────────────────────────────────────────────────── virtualenv (or prompt:continuation)
```

### RGB colors

There are two ways to use RGB colors with the hexadecimal format. The correct way is to use a [terminal that support 24-bit colors](https://gist.github.com/XVilka/8346728) and enable this feature as explained in the terminal's documentation.

If you can't use such terminal, the module [`zsh/nearcolor`](http://zsh.sourceforge.net/Doc/Release/Zsh-Modules.html#The-zsh_002fnearcolor-Module) can be useful. It will map any hexadecimal color to the nearest color in the 88 or 256 color palettes of your terminal, but without using the first 16 colors, since their values can be modified by the user. Keep in mind that when using this module you won't be able to display true RGB colors. It only allows you to specify colors in a more convenient way. The following is an example on how to use this module:

```sh
# .zshrc
zmodload zsh/nearcolor
zstyle :prompt:purus:path color '#FF0000'
```

## Example

```sh
# .zshrc

autoload -U promptinit; promptinit

# optionally define some options
PURUS_CMD_MAX_EXEC_TIME=10

# change the path color
zstyle :prompt:purus:path color white

# change the color for both `prompt:success` and `prompt:error`
zstyle ':prompt:purus:prompt:*' color cyan

# turn on git stash status
zstyle :prompt:purus:git:stash show yes

prompt purus
```

## License

This project is licensed under the MIT License. The project is based on and a fork of [Pure](https://github.com/sindresorhus/pure) by Sindre Sorhus. See [LICENSE](LICENSE) for more information.

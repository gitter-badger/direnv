direnv -- Unclutter your .profile
=================================

`direnv` is an environment switcher for the shell. It knows how to hook into
bash, zsh, tcsh and fish shell to load or unload environment variables
depending on the current directory. This allows to have project-specific
environment variables and not clutter the "~/.profile" file.

Before each prompt it checks for the existence of an ".envrc" file in the
current and parent directories. If the file exists (and authorized), it is
loaded into a bash sub-shell and all exported variables are then captured by
direnv and then made available the current shell.

Because direnv is compiled into a single static executable it is fast enough
to be unnoticeable on each prompt. It is also language agnostic and can be
used to build solutions similar to rbenv, pyenv, phpenv, ...


## Example

```
$ cd ~/my_project
$ echo ${FOO-nope}
nope
$ echo export FOO=foo > .envrc
.envrc is not allowed
$ direnv allow .
direnv: reloading
direnv: loading .envrc
direnv export: +FOO
$ echo ${FOO-nope}
foo
$ cd ..
direnv: unloading
direnv export: ~PATH
$ echo ${FOO-nope}
nope
```

## Install

### From source

Dependencies: make, golang

```bash
git clone https://github.com/zimbatm/direnv
cd direnv
make install
# or symlink ./direnv into the $PATH
```

### Packaged

There's package definitions on Homebrew, Arch's AUR and NixOS's nixpkgs.

Links to binary builds are also available on
[each release](https://github.com/zimbatm/direnv/releases).

## Setup

For direnv to work properly it needs to be hooked into the shell. Each shell
has it's own extension mechanism:

### BASH

Add the following line at the end of the "~/.bashrc" file:

```sh
eval "$(direnv hook bash)"
```

Make sure it appears even after rvm, git-prompt and other shell extensions
that manipulate the prompt.

### ZSH

Add the following line at the end of the "~/.zshrc" file:

```sh
eval "$(direnv hook zsh)"
```

### FISH

Add the following line at the end of the "~/.config/fish/config.fish" file:

```fish
eval (direnv hook fish)
```

### TCSH

Add the following line at the end of the "~/.cshrc" file:

```sh
eval `direnv hook tcsh`
```


## Usage

In some target folder, create an ".envrc" file and add some export(1)
directives in it.

On the next prompt you will notice that direnv complains about the ".envrc"
being blocked. This is the security mechanism to avoid loading new files
automatically. Otherwise any git repo that you pull, or tar archive that you
unpack, would be able to wipe your hard drive once you `cd` into it.

So here we are pretty sure that it won't do anything bad. Type `direnv allow .`
and watch direnv loading your new environment. Note that `direnv edit .` is a
handy shortcut that opens the file in your $EDITOR and automatically allows it
if the file's modification time has changed.

Now that the environment is loaded you can notice that once you `cd` out
of the directory it automatically gets unloaded. If you `cd` back into it it's
loaded again. That's the base of the mechanism that allows you to build cool
things.

### The stdlib

Exporting variables by hand is a bit repetitive so direnv provides a set of
utility functions that are made available in the context of the ".envrc" file.

As an example, the `PATH_add` function is used to expand and prepend a path to
the $PATH environment variable. Instead of `export $PATH=$PWD/bin:$PATH` you
can write `PATH_add bin`. It's shorter and avoid a common mistake where
`$PATH=bin`.

To find the documentation for all available functions check the
direnv-stdlib(1) man page.

It's also possible to create your own extensions by creating a bash file at
"~/.config/direnv/direnvrc" or "~/.direnvrc". This file is loaded before your
".envrc" and thus allows you to make your own extensions to direnv.

#### Loading layered .envrc

Lets say you have the following structure:

- "/a/.envrc"
- "/a/b/.envrc"

If you add the following line in "/a/b/.envrc", you can load both of the
".envrc" when you are in `/a/b`:

```sh
source_env ..
```

## Similar projects

* [Environment Modules](http://modules.sourceforge.net/) - one of the oldest (in a good way) environment loading system
* [autoenv](https://github.com/kennethreitz/autoenv) - lightweight, doesn't support unloads

## Contribute

Bug reports, contributions and forks are welcome.

All bugs or other forms of discussion happen on
<http://github.com/zimbatm/direnv/issues>

There is also a wiki available where you can share your usage patterns or
other tips and tricks <https://github.com/zimbatm/direnv/wiki>

Or drop by on the [#direnv channel on FreeNode](irc://#direnv@FreeNode) to
have a chat.

[![Build Status](https://api.travis-ci.org/zimbatm/direnv.png?branch=master)](http://travis-ci.org/zimbatm/direnv)

## COPYRIGHT

Copyright (C) 2014 shared by all
[contributors](https://github.com/zimbatm/direnv/graphs/contributors) under
the MIT licence.

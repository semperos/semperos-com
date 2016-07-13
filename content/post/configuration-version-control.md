+++
date = "2016-04-20T21:12:51-04:00"
tags = ["programming", "git", "stow", "version-control", "configuration", "shell"]
title = "Configuration in Version Control"
+++

Props to [Brandon Invergo](http://brandon.invergo.net/news/2012-05-26-using-gnu-stow-to-manage-your-dotfiles.html) for describing this technique before me.

I manage the configuration of my software via two primary mechanisms:

*   Storing them in version control ([Git](https://git-scm.com/))
*   Symlinking them with [GNU Stow](https://www.gnu.org/software/stow/)

On modern Mac OSX and Linux distributions, Git should already be installed. On Windows, I suggest using [Github for Windows](https://windows.github.com/) since it includes not only a graphical interface, but also a command-line interface.

For GNU Stow, I suggest using [Homebrew](http://brew.sh/) on Mac OSX and your package manager under Linux. I have not used this technique under Windows, but if you want to give it a shot, you might take a look at [a Stow-like Python program](https://github.com/ericsubach/stow-lite) or write a script yourself that will symlink things as Stow does.

If you run `stow [folder-name]`, Stow will treat the files in `folder-name` as if they should exist in the parent folder from where you issue that command. In my config, I use a separate directory for each program I keep configuration for and clone the whole repo into a `$HOME/config` folder. From there, I can run `stow` separately for each program as I'm ready to symlink its configuration.

Depending on the program, there may already exist configuration files on your filesystem with the same names as the ones you're trying to symlink, which causes the `stow` command to raise an error. In this case, I suggest renaming existing configuration files (as a backup) and then running `stow` until it doesn't complain about conflicts.

**Caveat emptor:** If you keep credentials of any kind in your configuration files, make sure to remove them from what you store in version control.

One of the hardest bits of configuration to keep sane is your shell configuration, especially if you use the same configuration on different operating systems. Tricks I've found helpful are:

*   Pipe stdout and stderr to `/dev/null` liberally for non-essential configuration.
*   Check for the existence of files and commands before calling them.
*   Rely on installations relative to either your `$HOME` or (as I prefer) something like `$HOME/opt`, as this tends to be more cross-OS-proof.

With this configuration, setting up a new system involves:

1.  Install all desired packages (as well as Git and Stow required for this setup)
2.  Clone my configuration to `$HOME/config`
3.  Step into `$HOME/config`
4.  Run `stow [folder-name]` for each program I'm ready to configure
5.  Fix errors that have crept in since the last setup I've performed, commit and push!

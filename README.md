
# Table of Contents

1.  [next-cfg](#orgb2063c1):next_browser:
    1.  [Author: Taylor Viti](#orgcfde60b)
    2.  [Note about versions](#orge982d3f)
    3.  [Features](#orgb79be06)
        1.  [Automatically determine the `dbus` socket's location on macOS](#orgdd51e84)
        2.  [Make buffer deletion prompt more consistent w/ Emacs](#org7789160)
        3.  [`delete-all-buffers`](#orgd239ac1)
        4.  [`open-home-dir`](#orgf74d01a)
        5.  [Vim `ex` style command abbreviations](#orgb813610)
        6.  [Use `C-[` like `ESCAPE`](#org861db1a)
        7.  ["Hot-swapping" and version controlling `bookmark-db` files](#org644cd97):bookmarks:
    4.  [`README.org` TODO-list](#orgc2e7a66)
        1.  [Literate style init file?](#org6305d51)


<a id="orgb2063c1"></a>

# next-cfg     :next_browser:


<a id="orgcfde60b"></a>

## Author: Taylor Viti

A repo for version controlling my `next-browser` init/config file(s).

For more information on `next-browser`, see:

-   <https://github.com/next-browser/next>
-   <https://github.com/atlas-engineer/next/blob/master/documents/MANUAL.org>


<a id="orge982d3f"></a>

## Note about versions

This config works up til `next-browser` v1.3.2 [commit df99518](https://github.com/atlas-engineer/next/commit/df99518f03d1bb01c0a95b9cfa385af26cc39a2e), but seems to
be broken when run with the latest **dev version** (i.e. the master branch of
the `next-browser` repo). It may work with further commits, but is only
tested up to that particular one (the latest as of this writing, [commit
628680f](https://github.com/atlas-engineer/next/commit/628680f9b396513a3874bf00084042f5a07bee4f), is unusable).

From what I can tell, this is due to the switch to
*functional* style configurations (see [this issue ticket](https://github.com/atlas-engineer/next/issues/419)), but I honestly
haven't spent a whole lot of time debugging so far.


<a id="orgb79be06"></a>

## Features


<a id="orgdd51e84"></a>

### Automatically determine the `dbus` socket's location on macOS

macOS doesn't define the env var `DBUS_SESSION_BUS_ADDRESS` on it's own, and
I have also noticed that often times `DBUS_LAUNCHD_SESSION_BUS_SOCKET` will
be pointing to the wrong location, so I query the value of the latter and
then use it to set the former when `next` starts


<a id="org7789160"></a>

### Make buffer deletion prompt more consistent w/ Emacs

The default behavior of `C-x k` in Emacs (at least with `evil-mode` active)
is to query the user for a buffer to delete, with the default being the
active buffer, while in `next`, the completion function for the
`delete-buffer` command explicitly selects a non-active buffer as the default
for deletion. Here, I use a modified completion function that retains the
Emacs behavior (the command implementing this is un-creatively termed
`my-delete-buffer`).


<a id="orgd239ac1"></a>

### `delete-all-buffers`

The command `delete-all-buffers` will delete ALL buffers except for the
currently active one.


<a id="orgf74d01a"></a>

### `open-home-dir`

The current file manager implementation felt a little un-intuitive and clunky
to me, so when I need to open a local `html` file, I often just start by
calling `open-home-dir`, and then link-hint my way to where I need to be.


<a id="orgb813610"></a>

### Vim `ex` style command abbreviations

In Emacs, I rely heavily on the `b` and `e` `ex` commands for swapping
buffers and opening files. Here, they are aliases for `switch-buffer` and
`set-url-new-buffer`. Of course, unlike in Vim and Emacs, they don't
actually take args in `next`.

1.  TODO Better implementation of `def-cmd-alias`

    I'm guessing that my method of implementing the aliases is probably a
    dirty hack, so I should redo that at some point.


<a id="org861db1a"></a>

### Use `C-[` like `ESCAPE`

This one isn't an *actual* alias for `ESCAPE`, but will do the same thing in
`vi-normal-mode`, `vi-insert-mode`, and `minibuffer-mode`.


<a id="org644cd97"></a>

### "Hot-swapping" and version controlling `bookmark-db` files     :bookmarks:

The command `select-bookmark-db` allows you to change `bookmark-db-path`
(i.e. the "active" bookmark database file) on the fly, via a minibuffer
prompt implementing `file-manager-mode`. The selected file will be created if
it doesn't already exist. If a `.git` directory is found in the directory
housing the selected file, the command `git add <bookmark-db>` is called,
followed by steps (1) and (2) of `bookmark-db-push`.

The command `bookmark-db-push` will call the following commands in sequence:

1.  `git ... add --update`
2.  `git ... commit -m "bookmark-db-push"`
3.  `git ... push origin master`

where `...` denotes the flags `--git-dir=<db-dir>.git --work-tree=<db-dir>`.
The command `bookmark-db-pull` will perform the same sequence, but with the
commit message of (2) as `bookmark-db-pull`, and (3) as a `pull` instead.
Both commands will print a warning to the repl (**not** the minibuffer) if no
`.git` folder is detected in the bookmark-db folder.

These commands will all assume that `origin` is setup to be accessed over
ssh, and that the requisite ssh-key has already been added to the ssh-agent
(i.e. they are very primitive, and rely on the assumption that you will not
be prompted for a username/password at any point when calling `git`).

The commands `bookmark-db-cp` and `bookmark-db-mv` can be used to duplicate
and move bookmarks (resp.) between your different database files. They will
first prompt the user for an entry in the currently active bookmark
database, then for a path to the destination database. The state of the
bookmark repo is committed at the start of the command (before any changes
have occurred), and then upon command completion. Like `select-bookmark-db`,
the destination database will be created and added to the repo if it does
not already exist.

1.  TODO Better system for git interaction

    -   I can't help but feel that the current system is a little excessive with
        commit frequency.

2.  TODO Should we use command hooks for git interaction?

    -   It may be elegant to call the start/end repo updates in the entry/exit
        command hooks (e.g. for `bookmark-db-mv` and `bookmark-db-cp`). One
        possible downside though, is that since the git interaction is not coded
        explicitly in the function body, it may become more challenging to
        understand what is going on if these things get more complicated (and I
        tend to be stupid so&#x2026;)

3.  TODO Allow user to specify remote and branch

4.  TODO Display git command output in minibuffer

5.  TODO Password prompts

6.  TODO Select-bookmark-db should glob for .db files


<a id="orgc2e7a66"></a>

## `README.org` TODO-list


<a id="org6305d51"></a>

### TODO Literate style init file?

Vindarel's *literate style* init file using `erudite` is really damned
slick. Should we do the same thing?


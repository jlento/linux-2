---
title: Command line
titleslide: true
---


# In short

- know the shortcuts
- use commands you typed previously
- switch between processes such as command line and editor efficiently


# Keyboard shortcuts

- Shell uses `readline` library for reading your input
- Emacs style shortcuts by default

| Shortcut      | Action                                         |
| --------      | :------                                        |
| `C-a` / `C-e` | Move cursor to the beginning / end of the line |
| `C-k`         | Kill (cut) rest of the line                    |
| `C-y`         | Yank (paste) what you just killed              |

{:.Q}

Configuring readline: You can configure readline to use Vi style shortcuts, too. How?


# Completion — avoid typing (and typos)

- Pressing the `TAB` key attempts to complete the current word.
- Bash attempts completion on variables (`$..`), usernames (`~..`), hostnames (`@..`),
  commands, aliases, functions, and file names.
- To just see possible completions hit `M-?` (or `TAB` twice), and to insert all
  possible completions hit `M-*`.

{:.Q}

Completion: Some commands add their own completion hints to bash. For example,
try typing `git sta` and hit `TAB` twice. How do they do it, or at least, where
is this documented?


# Re-use command lines, history

There is plenty of customization on how Bash keeps a record of the previous
commands, see <https://www.shellhacks.com/tune-command-line-history-bash/>. To
use the command history, it is usually enough to remember three key shortcuts,

| `UP` / `DOWN` | Scroll up and down the command history |
| `CTRL-r`      | Search the command history             |

Also 'HISTORY EXPANSION' is available, see `man bash`.

{:.Q}

Which environment variable controls how many previous commands Bash keeps in the
history, and which controls how many are saved to a file when the shell exits?


# Process/job control

Usually one has at least two programs running simultaneously, an interactive
shell session and a text editor. One can switch between these either by using
some sort of windowing system or just switch active process using `CTRL-z` for
suspending the current process, and commands `fg`, `bg`, and `jobs`.

{:.Q}

Job control: Which do you prefer, window manager, multiplexing terminal emulator
(`tmux`), or just single terminal and job control? What are the relative
benefits and drawbacks of different solutions?

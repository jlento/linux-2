---
title: Command line
---


# Command line

## Prompting — the shell expects something from you

```console
user@host:~/$
```

- When executing interactively, bash displays the primary prompt string `PS1`
  when it is ready to read a command, and the secondary prompt `PS2` when it
  needs more input to complete a command.
- Bash allows these prompt strings (environment variables) to be customized. A
  number of backslash-escaped special characters can be used to display
  various information.

As an example:

```bash
PS1="(\!) \h:\W\$ "
```

where

  | `\!` | history number of this command                |
  | `\h` | hostname up to the first `.`                  |
  | `\W` | the basename of current working directory     |
  | `\$` | if effective UID is 0, a `#`, otherwise a `$` |

There is plenty of more special escapes to various info. You know where to find
them, search for section `PROMPTING` :)

Look at the prompt string in your machine. Can you figure out how it is built?

Can you figure out what this shell function does?

```bash
tutorial_prompt () {
    FONT_RESET=$(tput sgr0)
    FONT_BOLD=$(tput bold)
    PS1="\[${FONT_RESET}\][\$?]\w \$ \[${FONT_BOLD}\]"
    trap 'echo -ne "${FONT_RESET}" > $(tty)' DEBUG
}
```

Hint: Just run it. Starting a sub-shell with command `bash` before defining the
function and running it might be a good idea, too. You can exit from the
sub-shell with command `exit`, and return to your original environment.


## Line continuation

You can continue writing the command line on the next line by typing `\`, for
example,

```bash
echo \
1 \
2 \
3
```

This can be useful when you need to split long commands on multiple lines, for
readability. However, it is not necessary to add the explicit `\` at the end of
the line if Bash knows that the command line must continue. This is usually far
more nicer to read than all the `\`'s. For example, if you have a very narrow
terminal :)

```bash
echo "
I am a multi-line
string!
"

array=(
  I am a
  compound
  assignment
)

for s in compound command
do
  echo $s
done

true list &&
  echo $?
```


## Less

Learn how to use *pager* `less`, especially how to search with `/`-command, and
jump to the next match. Note, because the search string is interpreted as
*regular expression*, you may need to *escape* some special characters by
prepending them with `\`.

Less is also the default pager for man pages (`MANPAGER`), so the same search
tricks work with man pages, too!

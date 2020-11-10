---
title:	Shell internals
author:	CSC Training
titleslide: True
lang:	en
---


# What is Bash used for?

The Bash shell is both a command interpreter and a programming language.
- As a command interpreter, the shell provides the user interface to the rich
  set of utilities.
- The programming language features allow these utilities to be
  combined.

{:.Q}

The discussion of the shell internals may feel abstract at first. Return to it
later after going through practical exercises. Did it help understanding the
shell commands and scripts?


# How does the shell work?

At its base, a shell is simply a macro processor that executes commands. It
parses the command line (or a line of a script), makes variable expansions,
*etc*, and finally, *tries* to execute the first word. The remaining
words are agruments to the first (command).

We'll return to *etc* in more detail later.

{:.Q}

Command search: Have you ever seen `...: command not
found` error? How does the shell search for commands?!?


# What is a command?

A "command" can be a shell builtin, a shell keyword, an executable binary, an
executable script, a shell function or a shell alias. These all behave almost
the same.

{:.Q}

Command types: Which commands can you use to find what a "command"
more precisely is, and where it comes from? What is the precedence of
different command types? For example, if you have a shell function and
an executable binary with the same name, which one gets executed?


# Exit status

Every command returns an exit status value (0-255) to the calling shell. Value
zero indicates success, and non-zero value indicates failure. It is up to the
calling shell (script) to decide what to do with the exit status.

Special parameter `$?` expands to the status of the most recently executed
foregorund command (pipeline).


# Command inputs and outputs

Six standard "channels":

IN: 1) *command arguments*, 2) *standard input stream (stdin)*, 3) *environment
variables*

OUT: 1) *standard output stream (stdout)*, 2) *standard error stream (stderr)*,
3) *command exit status*

{:.Q}

Command inputs and outputs: What are the conventions of using
different input and output channels, what type of data is communicated
through different channels? Give examples for using each.


# Variables

As a working assumption, shell has two variable types, strings and arrays of
strings (and hash arrays, for Bash 4+). String variables may be exported to the
shell's environment (see ENVIRONMENT chapter in `man bash`).

- only the environment variables are visible to the commands that the shell
  starts
- variables are a "sub-class" of `PARAMETERS`, see `man bash`...

{:.Q}

Environment: How do you promote a variable to an environment variable?


# Sub-shells

- Shell spawns sub-shells when explicitly asked to, and...
- Sub-shells inherit exported variables from their parent. No variables or other
  shell environment changes propagate from the sub-shell back to the parent.

TIP: When playing around, start a new subshell with command `bash`, and when you
are done, `exit`, and you are back in the original, unaltered environment.

{:.Q}

Subshells: How do you run a command (or compound command!) in a sub-shell? Can
you name situations when the shell creates a sub-shell?


# Interactive or non-interactive?

Shells can be started in interactive or in non-interactive mode. In interactive
mode, they accept input typed from the keyboard. When executing
non-interactively, shells execute commands read from a file.

{:.Q}

Shell initialization: Which shell initialization files are read (in your
machine) when interactive/non-interactive shell is started? What initializations
should go to which initialization file and why?


# Simple command expansion — I press enter and…?

The shell performs the following actions, from left to right:

1. Variable assignments and redirections are stripped away and saved for later processing.
2. Any words left are *expanded* (next slide).
3. Redirections are performed.
4. Variable assignments undergo expansions and quote removal.
5. Command is executed.

{:.Q}

Command expansion: Explain why `a=works echo $a` does not work, but both `a=works
bash -c 'echo $a'` and `a=works; echo $a` work?


# Expansion types

Expansion is a process of exchanging a word with another word(s), with certain rules.
Bash performs following expansions (in order):
  1. Brace expansion `{...}`
  2. Tilde expansion `~...`
  3. Parameter and variable expansion `$...`, `${...}`
  4. Arithmetic expansion `$((...))`
  5. Command substitution `$(...)`
  6. *Word splitting*, see `man bash` and IFS
  7. Pathname expansion (filename wildcards) `*`, `?`, `[...]`

Tip: Remember the `set -x` trick!


# Quoting — Take it literally

Quoting is used to control expansions, and word splitting

| `\`  | preserve the literal value of the next character                                                            |
| `''` | preserve the literal value of each character within the quotes                                              |
| `""` | preserve the literal value of each character within the quotes, with the exception of `$`, `` ` ``, and `\` |

{:.Q}

Quoting: ...is pretty important. Can you guess what you get with `file="file
name"; touch $file`? Spaces in filenames is evil, the correct solution is to use
*snake_case* or *CamelCase*. Using double quotes is a good default when
expanding variables, unless you intentionally wish to expand a string variable
as separate words/arguments!


# Signals — there's a trap

Signals are asynchronous notifications that are sent to processes when certain
events occur. Command `trap` allows you to catch signals and execute specified
code when a process receives a signal. Option `-l` lists all available signals,
and option `-p` lists all signals currently trapped.

{:.Q}

Signals: Write a script that uses a temporary file (really, much better to avoid
temporary files altogether), and uses `trap` to clean it after the script exits,
or even when the script is interrupted before finishing!


# Common pitfalls

Common pitfalls is basically everything above. Let's try to hit as many as
possible.

Good luck! :)

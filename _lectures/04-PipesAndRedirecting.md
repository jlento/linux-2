---
title:	Pipes and redirecting
author:	CSC Training
titleslide: true
lang: en
---


# Files and streams

*Streams* of characters are used abundantly in shell. A stream can be read from
a device file, an actual file, string, *here document*, *process substitution*,
*named pipe*, etc., or it can be a *pipe* between two commands.

Plenty of nice material on this in internet, for example
<https://developer.ibm.com/tutorials/l-lpic1-103-4/>.

{:.Q}

How does reading a stream (file) differ from reading an actual file on the disk?


# Standard streams and file descriptors

Every *command* can access *standard input*, *standard output*, and *standard
error* streams. The corresponding *file descriptors* are

| stream | Descriptor | default direction |
| ------:|:----------:|:------------------|
| stdin  | 0          | terminal input    |
| stdout | 1          | terminal output   |
| stderr | 2          | terminal output   |


# Redirection

Before a command is executed, its input and output may be connected to
non-default streams (*redirection*) using operators `|`, `<`, `>`, `<<<`, and/or
`>>`.

In many cases explicit temporary (yuck!) files can be avoided using redirections
and pipes.

{:.Q}

What does `echo I got >&2 an error && false` print, why, and where?


# Pipes and process substitution

The pipeline is a redirection where output (stdout) of a command is connected to
the input (stdin) of the next command via a pipe `|`.

The commands in the pipelines are run in sub-shells, which may pose occational
problems when one of the commands fails. In some cases *process substitution*,
`<(...)` or `>(...)` combined with the redirection of the stdin `<` or the
stdout `>` may work better.

{:.Q}

Explain what `echo >(:)` does? Can you even do `a=>(:); echo $a`?

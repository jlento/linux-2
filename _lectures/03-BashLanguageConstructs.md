---
title:	Bash Language constructs
author:	CSC Training
titleslide: true
lang:	en
---


# Comments

- A word beginning with `#` causes that word and all remaining characters on
  that line to be ignored. Usually it is used as the first character of the
  line, but it is possible to put it somewhere else, too.

```bash
echo "Have a nice day!"   # Yes, indeed
```

{:.Q}

Comments are extra lines. Give examples of comments that are unnecessary and
could be avoided altogether? Give examples of comments that are useful?


# Lists of commands

Sequentially executed commands are separated by newlines or by semicolons `;`.
Sequentially means that the shell waits for the command to finish before running
the next command.

Commands followed (separated) by ampressands `&` are executed asynchronously in
*sub-shells*. Current shell runs the next command without waiting the sub-shell
to finish.

{:.Q}

Wait! How can you ask the shell to wait for asynchronous program(s) to finish?
Give an example when would you need to do that?


# Conditionally executing lists

AND and OR lists are sequences of one or more commands separated by the control
operators `&&` and `||`, respectively. If you are familiar with "short
circuiting" conditional operators in other languages, this works the same.

| `command1 && command2` | _command2_ is executed if _command1_ succeeds |
| `command1 || command2` | _command2_ is executed if _command1_ fails |

{:.Q}

Success or fail? Recall, what does it actually mean for a command to "succeed"
of "fail"?


# Conditional expressions

One can view the conditional expressions as commands in which one is interested
mostly in the exit status of the command. Success (zero exit status) is
considered to be true, failure (non-zero exit status) corresponds to false. The
actual conditional expressions begin with `[[` compound command (Bash only), or
`test` and `[ ` builtin commands, but any command can used as one, since all
commands return an exit value.

{:.Q}

Integer arithmetic conditions are often useful. What is the syntax for
arithmetic evaluation?


# Compound commands

Bash provides conditional constructs (`if-then-else-fi`), looping constructs
(`for-do-done`, `while-do-done`), and mechanisms to group commands and execute
them as a unit. Grouping commands using parenthesis `()` runs them in a
sub-shell, using curly braces `{}` runs commands in the current shell.

Tip: Deeply nested conditional or looping constructs are often a sign of
inefficient or unclear code.

{:.Q}

Can you use the return value of any command as the conditional expression in the
if or while compound commands?


# Functions

Functions are the most important programming construct in any language,
unfortunately often neglegted in shell programming.

Bash functions can be viewed as named compound commands, which can take
arguments. The function arguments are available in positional parameters within
the function.

{:.Q}

Positional parameters. Shell scripts have access to their call arguments the
same way as functions, using positional parameters. How do you print all
(current) positional parameters, and how do you refer to them individually?

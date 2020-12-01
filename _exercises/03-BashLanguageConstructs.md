---
title: Bash language constructs
---


# Comments

## Shebang

If a file begins with`#!`, for example

```bash
#!/bin/bash
```

or

```bash
`#!/usr/bin/python
```

it has a special meaning. What is it?

There is a default interpretation if the shebang is missing altogether. What is it?

Tip: Some file `prog.c` could begin like this(!):

```bash
//usr/bin/env cc "$0" && exec ./a.out "$@"
```

What could this possibly do?


# Lists of commands

In general, prefer newlines over semicolons for readability. One-liners are ok
on command line, but mostly ugly in scripts.

Task parallelism is super-easy with bash. For example, if you have some commands
that need to wait for an outside service response, such as www request, you can
easily run them in the background in subshells:

```bash
some-slow-command > file1 &
some-other-slow-command > file2 &
some-other-slow-command > file3 &
```

If your script then needs to work on the results of the background commands, you
need to add something. What is it?


# Conditionally executing lists

It is quite easy to get a good feeling how conditionally executing lists work
using two simple commands, `true` and `false`, that only return exit value 0
(success) or 1 (error), respectively, and do nothing else. Try for example
something like

```bash
false && true || false
echo $?
true && true || false
echo $?
```

These are equivalent to if-then-else compound command. You can create more
complex logic using command grouping with `{...; }`, but usually if-then-else is
easier to read. Or even better, always try to simplify complex logic to simple
one :)

The most common use case is simple

```bash
command-that-can-fail || what-to-do-when-previous-command-failed
```

In general, if you ever find deep nesting if's or for-loops (also called
tornados), it is a warning sign. You probably need to do some re-thinking and
flatten those deep nesting structures. This applies to all programming
languages, not only Bash.


# Conditional expressions

The _test-commands_ often involves numerical or string comparison tests, but it
can be any command that returns a status of zero when it succeeds, and some
other status when it fails. Bash provides two comparison expressions, command
`test` (and it's synonym `[`), which is more portable but limited in features,
and the "new test" command `[[`, which has fewer surprises and is generally
safer to use — but works only in Bash.

There are many ways you can write the same conditional:

```bash
if [ "$LOGNAME" = "cscuser" ]; then
   echo 'Good day!';
fi

if [ "$LOGNAME" = "cscuser" ]; then echo 'Good day!'; fi

if test "$LOGNAME" = "cscuser"; then echo 'Good day!'; fi

test "$LOGNAME" = "cscuser" && echo 'Good day!'

[ "$LOGNAME" = "cscuser" ] && echo 'Good day!'
```

Of course you can test many other things besides the string equivalence, see
`man test`, for example.

Why do you need spaces between the arguments `"$LOGNAME"`, `=`, and `"cscuser"`?
What were you actually testing, if you would write the arguments together
without spaces in between?

How would you test if a file 1) exists, 2) is a directory, or 3) is newer than
some other file?



# Compound commands

## Looping constructs

Bash supports the following looping constructs:

```bash
until test-commands; do commands ; done

while test-commands; do commands; done

for name in words; do commands; done

for (( initialize-variable; stop-condition; increment-variable ))
do
    commands
done
```

Note how a `;` may be replaced with a newline, or vice versa.

How would you write a loop that prints out the squares of numbers from 1 to 6?

How would you loop over a list of files in the current directory?

How would you write a loop that waits for the user to input a line and prints
it, and repeats until the user gives input "end"?

## Avoiding loops

Often you can avoid writing a loop by just realizing that a command can operate
on multiple arguments, such as file names, instead of just a single one at a
time. Can you find such commands?


## Command grouping

Bash provides two ways to group a list of commands to be executed as a unit.

### ( _list_ )
  
Placing a _list_ of commands between parentheses causes a subshell environment
to be created, and each of the commands in _list_ to be executed in that
subshell.

###  { _list_; }

Placing a _list_ of commands between curly braces causes the _list_ to be
executed in the current shell context. The semicolon (or newline) following
_list_ is required.

Example:

```bash
{ echo "Directory listing:"; ls; } > dir_list
```


# Functions

Shell functions are a way to group commands for later execution using a single name for the group. There are two ways to declare a function:

```bash
function-name () compound-command
function function-name compound-command
```

Function declaration may be followed by redirections.

When a function is executed, the arguments to the function become the positional
parameters during its execution.

```bash
$ ll () { ls -laF "$@" | more; }
```

## Functional style

It is possible to write also functional style functions in Bash, although they
are not necessary as efficient as in actual functional programming languages. A
nice example can be found in
<https://unix.stackexchange.com/questions/81202/passing-a-code-block-as-an-anon-function>

## Exercises in functions

Write a function that checks if a given year is a leap year.

Write a function that checks if a given string is a valid date of the form
DDMMYYYY.

Write a function that checks if a given year is a valid finnish social security
number. (This is entirely optional exercise.)

Many Linux systems have a nice program `pstree` that can be used to print
process trees. A bit like a more visual `ps`. For example, if you would like to
see all the ancestors of your current process, you coud run `pstree -s $$`. Mac
OS is lacking this program. Write a function that lists all the ancestors of
your current process (a process stack) using a while loop. Can you write the
same using a recursive function?

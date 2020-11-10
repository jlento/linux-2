---
title: Shell internals
---


# How does shell work?

If you doubt how shell expands a command line before executing a _simple_
command, you can always put an `echo` in front of the puzzling line! Be avare of
the "command separators", such as `;`, and other constructs which actually are
command lists.


# What is a command

## Command search path

What is the *environment variable* that has a list of directories that shell
uses when it searches commands from the disk?

Why it is generally a bad idea to modify the search path environment variable in
`~/.bashrc` (although many intructions guide you to do so)? What better ways you
have to achieve the same goals?

## Functions

How would you list all currently defined shell functions?


# Exit status

Modify your command prompt so that it shows the exit status of the previous
command!


# Command "inputs" and "outputs"

Most of the commands operate through streams, discussed later in the context of
"Pipes and Redirections." The other types of inputs, command line arguments and
evironment variables, mostly provide options and control the details of how the
command should work. The success or failure of a command is communicated through
the exit status of the command.

You can give arguments to your shell scripts, too. If you have a shell script
called `echo-arguments`, you can run it like this:

```console
bash echo-arguments arg1 arg2 arg3
```

Write the `echo-arguments` script that just prints it's arguments!

Tip: Search for "Positional Parameters" and "Special parameters" from `man
bash`.


# Variables

## Show all variables

How do you print all currently defined variables? How do you print only
environment variables?

Tip: There is a lot of variables already defined. Declare a local shell variable
with easy to search name. Pipe your command that list all variables to `less`,
and search your own variable from the list.

## Parameters

Term *variable* refers to named *parameters* that can be set by the user. The
term *parameter* is more general, and in addition to variables, refers to
parameters set by the shell.

## Environment variables

You can set an environment variable temporarily for just a single command by
appending the command with the assingment(s):

```bash
MANPAGER=cat man ls
```

The value of `MANPAGER` in the current shell is not changed.

However, this does not work

```bash
LANG=fi_FI.utf8 echo $LANG
```

quite as maybe expected. Why?

## Empty variables

Empty string is a valid parameter value. There is a difference between a
parameter with empty value, and an undefined parameter. How do you completely
undefine an existing variable?


# Sub-shells

## Jumping around in the directory tree

How would you change to another directory, run a command there, and then change
back to the original directory with and without using sub-shell? What if you
need to jump in multiple directories and run commands, and then return to the
directory where you started from?


## How deep?

You can open a sub-shell within the current shell by typing `bash`. You can do
that multiple times. How do you know how `deep` in the sub-shells are you?


# Parameter expansion

- The `$` character introduces *parameter expansion*, *command substitution*, or
  *arithmetic expansion*.
- The basic form of parameter expansion is `${parameter}`. The value of
  _parameter_ is substituted.
- The braces are required when _parameter_ is a positional parameter with more
  than one digit, or when _parameter_ is followed by a character that is not
  to be interpreted as part of its name.

```bash
$ echo $HOME   # ——> echo /home/cscuser
$ echo ${HOME}s   # ——> echo /home/cscusers
```

## Quoting special characters

There are basically two ways to escape the special meaning of `$`:

```bash
echo \$HOME
echo '$HOME'
```

Which other special characters need to be escaped if you need to print them
literally?

How would you echo string "I'm making some $$$!"?

The general rule is that use single quotes whenever no parameter expansions are
needed within a string, and use double quotes when parameter expansion is
needed.

## Modified parameter expansion

In addition to a simple parameter substitution to it's full value, there are
lot's of other parameter expansions for expanding to a default value or a
substring of the value, such as

```bash
echo ${string:-bummer}
echo ${string:=bummer}
```

What is the difference between the above two?

Give an example of every possible "modified" parameter expansion in the chapter
"Parameter Expansion" in the Bash man page.

What is the shortest way to give a default value to a variable that is unset or
null?

What is the shortest way to throw an error message and exit a script if a
variable is unset or null?


## Positional parameters

*Positional parameters* are assigned from the command's arguments when it is invoked. A positional parameter is a parameter denoted by one or more digits, other than the single digit 0. Positional parameter `n` may be referenced as `${n}`, or as `$n` when _n_ consists of a single digit.

```bash
mkdir -p one two/three   # ––> command="mkdir", $1="-p", $2="one", $3="two/three"
```

## Special parameters

| parameter | expands to |
|`$@` | positional parameters, starting from one |
| `$#` | number of positional parameters |
| `$?` | exit status of the most recently executed foreground pipeline |
| `$!` | process ID of the job most recently placed into the background |
| `$$` | process ID of the current shell |
| `$0` | name of the shell or shell script |


## Shell Variables

Shell sets a list of named variables, too. Have a look at "Shell Variables"
chapter in Bash manual page. There are variables that can be used for fancy
error messages, for example, reporting the name of the currently executing shell
script, function, or the line number. What are the names of these variables?


## Command substitution

Command substitution allows the output of a command to be used as the expansion.

```bash
$ list=$(ls exp)
$ echo $list
```

Old-style backquote form is still in use, but it's usage is strongly discouraged.

```bash
$ list=`ls exp`
$ echo $list
```

This is super useful. How would you find the file size of the `ls` command?


## Arithmetic expansion

Arithmetic expansion evaluates an arithmetic expression and substitutes of the
result. The format for arithmetic expansion is: `$(( expression ))`

```bash
$ echo $(( 1+2*3**4 ))
```

Supports only integers and (some of the) valid operators are:

- exponentiation "`**`";
- multiplication "`*`", division "`/`", remainder "`%`";
- addition "`+`" and subtraction "`-`";
- comparisons "`<= => < >`", equality "`==`" and inequality "`!=`"; and
- bitwise AND "`&`", XOR "`^`" and OR "`|`", and logical AND "`&&`" and OR "`||`".

For non-integer arithmetic one needs to resort to utilities such as `bc` (simple
pocket calculator), `awk` (spread sheet type calculations) or some more suitable
programming language.

How would you calculate and print the result of "1000 / 1024 * 123456" on the
command line?


## Brace Expansion

Brace expansion is a mechanism by which arbitrary strings may be generated. It
is of form `[preamble]{str[,str,…]}[postscript]`.

```bash
mkdir -p exp/{jan,feb,mar,apr}/run{1..3}
```

Brace expansions may be nested.

```bash
{% raw %}
$ touch exp/{{jan,mar},feb/run{1,2}}/skip
{% endraw %}
```

How would you print a list of integers from 1 to 10 so that each number is in
double quotes?


## Tilde expansion — A way to home

- Tilde expansion (almost) always expands to (some) user's home directory `$HOME`.

```bash
echo ~
echo ~root
```

Tilde expansion can also be used to refer to current working directory `$PWD`, or previous working directory `$OLDPWD`.

```bash
cd ~/Documents
cd /tmp
echo ~
echo ~+
cd ~-
```

Can you find a script where `~+` or `~-` is actually used? I cannot :)


## Filename expansion — the wildcards

The shell scans the command line for the characters `*`, `?`, and `[`. If one of
these is found, the word is regarded as a _pattern_, and replaced with an
alphabetically sorted list of file names matching the pattern.

| `*`                | any string, including an empty string  |
| `?`                | any single character                   |
| `[…]`              | any one of the enclosed characters     |
| `[^…]` or `[!...]` | any one of the not enclosed characters |


```bash
ls -d ?e*
ls -d *[cw]*
echo {/usr,}/bin/t[!r]*
```

# Shell builtin commands

There are too many to list here, but having a quick (or even a proper) look at
chapter "SHELL BUILTIN COMMANDS" in Bash man page is definitely worth it.

Pick one that you find especially useful, bonus if it is new to you. Give an
example where you would use it!


## Trap

Trap is a shell builtin command. It can be used to control how the shell reacts to signals, such as "CTRL-C".

```bash
# Run something important, no Ctrl-C allowed.
trap "" SIGINT
important_command

# Less important stuff from here on out, Ctrl-C allowed.
trap - SIGINT
not_so_important_command
```

Probably the most common use case is triggering "clean up" after the script
finishes, whether it finished succesfully or not, or was interrupted.

Write a shell script that 1a) traps scripts normal exit and interrupts by
CTRL-C, 1b) removes possible temporary files with the trap, 2) creates a
temporary file, and 3) sleeps for one minute. Test the script!

Note: Use of temporary files should be avoided in general. There are almost
always better ways to achieve the same result without them, and much more
efficiently.

---
title:	Pipes and redirecting streams
author:	CSC Training
lang: en
---


# Files and streams

List open files that are currently connected to your shell. Which command can
you use to do that? Can you see the names of the character devices that are
connected to the *stdin*, *stdout*, and *stderr* of your current shell? What
program could be in the "other end" of the streams?


# Standard streams and file descriptors

Standard streams and the corresponding file descriptors `&0`, `&1` and `&2` are
used abundantly. The use of other file descriptor (numbers) is far less common.
Can you find any examples where some other (`n > 2`) file descriptors are used?


# Redirection

The general format for redirecting output is `n>file` or `n>&m`. Redirection of
output causes the *file* to be opened for writing on file descriptor `n`. It
defaults to standard output if `n`is omitted. `m` is some other file descriptor.

## Redirecting output

```bash
grep -r "global" /etc   >output   # redirects default stdout
grep -r "global" /etc  2>error    # redirects stderr
```

If your script prints an error message, using `echo`command for example, how do
you redirect the error message to stderr (correct) instead of stdout
(not-so-correct)?


## Multiple redirections with descriptor duplication:

```bash
grep -r "global" /etc >output 2>error   # stderr and stout to separate files
grep -r "global" /etc >output 2>&1      # stderr to stdout to file
```

The order in which the redirections are in a simple command affect the outcome.
Demonstrate this with a command that produces an error message (to stderr).
Redirect stderr to stdout, and stdout to a file. Both stdout and stderr should
go to the file, or should they? How do you fix this?


## Overwriting or appending redirected output

Unless the shell option `noclobber` has been set, redirecting to a file always
overwrites an existing file. Which command can you use to set noclobber option?

If you need to **append** to a file instead of overwriting it, replace the `>`
operator with `>>`,

```bash
$ ls >>output
$ grep -r "global" /etc  >>output 2>&1
```

## Special files

Unix has three special, so-called _pseudo device files_, which are commonly
being used with redirections.

| `/dev/null` | happily discards all data written to it |
| `/dev/zero` | endless stream of zeros (bytes, not characters) |
| `/dev/random` | endless stream of random bytes |

How would you discard both the stdout and stderr of a command?

How would you produce a dummy file of the size of exactly one kB?

How would you produce a list of 100 random numbers from 1 to 6 in Bash (You
don't have to use `/dev/random` directly, but you can)?

## Redirecting input

The general format for redirecting input is `n<file`. Redirection of input
causes the file to be opened for reading on file descriptor `n`. If `n` is
omitted it defaults to the standard input, which is the most used case.

What do the commands below do?

```bash
tr a-z A-Z <adg.txt
<adg.txt >output tr a-z A-Z
```

## Here-documents

A here-document is an input redirection using source data specified directly at
the command line until a line containing only a certain keyword is seen. The
format of here-documents is:

```
command <<[-]keyword
    contents of
    here-document
keyword
```

All of the lines read up to that point are then used as the standard input for a
command. Brace expansion, tilde expansion, parameter and variable expansion,
command substitution, arithmetic expansion, and quote removal are performed
within the here-documents. How can you easily prevent all the expansions, and
take the here-document as is?

Here-documents are often used in scripts for printing out instructions

```bash
cat <<EndOfText
A line of text.
Second line of text.
EndOfText
```

More often you can get nicer looking and more useful result by using a
multi-line string at the beginning of your script, such as

```bash
usage="Usage: $0 arg1

Command $0 ....
"
```

and then print the string where ever you need.

Here-documents are also sometimes used to steer an interactive program:

```bash
gnuplot <<end
set terminal dumb
f(x)=1/x
plot f(x)
end
```

In case your system lacks `gnuplot`, install it by

```bash
sudo apt-get install gnuplot`
```

A variant of here documents is redirection of a string to command's stdin,
`command <<<word`.

```bash
bc <<< 2^10
```

How would you improve `echo 'one two three' | tr a-z A-Z` by redirecting the string
to `tr`command instead of using `echo` and a pipe?


# Pipes and process substitution

Let's say you have a program that runs a while and prints out progress report
and other information to stdout. How would you run this program so that the
output goes to your terminal so you can monitor the progress and also get the
same output to a log file for later inspection? Hint: `man tee`. How could you
filter what's going into the log file?

You can find differences between files using `diff` command. How would you find
differences between the outputs of two commands? Give an example.

## Named pipes (optional material)

You can also create your own pipes, although they are rare. You can often use a
regular unnamed pipe or process substitution instead of named pipes. A named
pipe (FIFO) works much like a regular pipe, but does have some noticeable
differences:

- Named pipes exist as a device special file in the file system.
- Processes of different ancestry can share data through a named pipe.
- When all I/O is done by sharing processes, the named pipe remains in the file
  system for later use.

A named pipe is created with `mkfifo` command:

```bash
$ mkfifo -m a=rw MYFIFO
```

FIFO files can be quickly identified in a physical file system by the "p"
indicator seen here in a long directory listing:

```bash
$ ls -lF MYFIFO
[…]
prw-rw-rw-  1 userid  group  0 Dec 5 13:16 MYFIFO|
```

Named pipes can be used to communicate between processes. For example, open two
terminals. In the first terminal, write

```bash
cat <<EOF >MYFIFO
  Hello, is there anybody in there?
  Not if you can see me.
  Is there anyone at all?
EOF
```

In the second terminal type

```bash
$ cat <MYFIFO
```

Notice how the first command blocks until the pipe is read. Can you run the
commands in the terminals in reverse order?

You can remove named pipes the same as regular files,

```bash
$ rm MYFIFO
```

Can you find a practical case where named pipes are actually used?

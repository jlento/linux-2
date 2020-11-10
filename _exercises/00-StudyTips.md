---
title: Study Tips
---

# Study Tips

## Bash-fu attitude

This exercise shows how it sometimes takes a longer trip to develop a nice Bash
function. Our example function here is a small part of a larger script. The
exercise is to read the Bash code, to understand how each iteration of the
function works, and to spot different Bash language constructs.

We do not expect you to understand all the details in the code at this
point. If everything is familiar and crystal clear immediately, this course may
be too easy for you. See what you can easily get out of the code now at the
beginning of the course, and return to this exercise later in the course, and
see how much you have learned!

The true lesson of this exercise is that when you understand what the code needs
to do, and what different commands can do, the implementation becomes obvious.


### Iteration 0, the original version

First, let's figure out what this function is supposed to do. It can be
difficult from the source only. In "real life" cases, spend some time here, ask
the person who wrote it in the first place, etc. Sometimes it can be easier to
start cleaning the original script, and figuring out what is going on while
cleaning.

```bash
long_format="%-25s %8s %5s %7s %5s %8s %9s %9s %5s"
desc_format="%-27s %8s/%-6s %8sk/%sk"

# Quota query for login node local temp directories, one argument:
#  1. Filesystem path (currently only /tmp and /local_scratch have quotas)
function temp_quotaquery() {
    local quota=$(quota -w --show-mntpoint | tail -n +3)
    echo "${quota}" | while IFS= read -r line; do
        if [[ "$1" == "desc" ]]; then
            IFS=' '; local values=($line); unset IFS
            printf "${desc_format}\n" \
                   ${values[1]} \
                   $(numfmt --to=iec ${values[2]}) \
                   $(numfmt --to=iec ${values[3]}) \
                   ${values[6]} \
                   ${values[7]}
        fi
        if [[ "$1" == "long" ]]; then
            IFS=' '; local values=($line); unset IFS
            printf "${long_format}\n" \
                   ${values[1]} \
                   $(numfmt --to=iec ${values[2]}) $(numfmt --to=iec ${values[3]}) \
                   $(numfmt --to=iec ${values[4]}) "-" $(numfmt --to=iec ${values[5]}) \
                   ${values[6]} ${values[7]} "-"
        fi
    done
}
```

In this function, you should be able to spot normal, local to the function, and
environment *variable definitions*, a *command substitution*, *pipelines*,
*quoting*, string and array *variable expansions*, a while loop, if statements
and *conditional expression* *compound commands*, and the use of the array
*compound assignment* as a primitive "parser". Also make a mental note, command
`read` is very handy in parsing strings, and/or reading text line by line when
combined with `while` loop. In short, looks like this function runs system
command `quota`, and reformats it's output in two different ways :)

For reference, the output of the `quota` command in the target machine is

```console
$ quota -w --show-mntpoint
Disk quotas for user jlento (uid 8520): 
     Filesystem  blocks   quota   limit   grace   files   quota   limit   grace
/dev/mapper/rhel-tmp /tmp       0  1048576 2097152               1       0       0        
/dev/mapper/local-scratch /local_scratch       0  83886080 104857600               5       0       0        
```

The command may list both mount points (file systems), one of them, or neither.
If your system does not have quotas, you can make a Bash function which acts
like one.

```bash
quota () {
echo "Disk quotas for user jlento (uid 8520): 
     Filesystem  blocks   quota   limit   grace   files   quota   limit   grace
/dev/mapper/rhel-tmp /tmp       0  1048576 2097152               1       0       0
/dev/mapper/local-scratch /local_scratch       0  83886080 104857600               5       0       0"
}
export -f quota
```

What is that `export` command doing and why it is there?


### Iteration 1, cleaning and adding "tricks"

Not really thinking what the function does, just cleaning it.

- using the output format variable name as the function argument
- since the (2) filesystems are known, looping over them one at the time, catching
  possible errors
- using `set` command and *positional* parameters "trick" to use short array
  element references instead of the long ones
- adding option `-s` to `quota` for human readable numbers, which saves the use
  of `numfmt` (really hand tool by itself, from GNU core-utils)

The human readable `quota` command's output:

```console
$ quota -ws --show-mntpoint
Disk quotas for user jlento (uid 8520): 
     Filesystem   space   quota   limit   grace   files   quota   limit   grace
/dev/mapper/local-scratch /local_scratch      0K  81920M    100G               1       0       0
/dev/mapper/rhel-tmp /tmp      0K   1024M   2048M               1       0       0
```

The first iteration of the function:

```bash
long_format="%-25s %8s %5s %7s %5s %8s %9s %9s %5s"
desc_format="%-27s %8s/%-6s %8sk/%sk"

# takes arguments "long_format" or "desc_format"
function temp_quotaquery() {
    local printstyle="$1"
    for fs in /tmp /local_scratch; do
        quota=$(quota --show-mntpoint -wsf $fs 2>/dev/null) || continue
        set -- $(tail -1 <<<"$quota")
        case "$printstyle" in
            desc_format)
                set -- $2 $3 $4 $7 $8
                ;;
            long_format)
                set -- $2 $3 $4 $5 - $6 $7 $8 -
                ;;
            *)
                echo "Internal error, bad argument" >&2
                return 1;;
        esac
        printf "${!printstyle}\n" "$@"
    done
}
```

Having read the man page of `quota`command, a colleague immediately spotted an
error in the script. In the case of the user exceeding the quota, the `quota`
command returns non-zero exit value. This drops out likely the most important
lines from the loop and output... LESSON: When you rewrite code, you may
introduce new bugs. This is why more serious projects have *regression tests*.

In addition to the language constructs in the zeroth version, you should spot the
use of `for` loop and `case` *compound commands*, standard input, output and error
*redirections*, command *list* `||`, *positional parameter* "trick" using `set`,
variable *indirection* with `${!..}`, and *special parameter* `"$@"` (and the
precise meaning of *quoting* with `$@`).

All in all, slightly cleaner, although works incorrectly :)


### Iteration 2, back to running the `quota`command only once

This version is much more true to the original.

```bash
long_format="%-25s %8s %5s %7s %5s %8s %9s %9s %5s"
desc_format="%-27s %8s/%-6s %8sk/%sk"

function temp_quotaquery() {
    local printstyle="$1"
    while read line; do
        set -- $line
        case "$printstyle" in
            desc_format)
                set -- $2 $3 $4 $7 $8
                ;;
            long_format)
                set -- $2 $3 $4 $5 - $6 $7 $8 -
                ;;
            *)
                echo "Internal error, bad argument" >&2
                return 1;;
        esac
        printf "${!printstyle}\n" "$@"
    done < <(quota --show-mntpoint -ws | tail -n +3)
}
```

In addition to the earlier language constructs, spot the *redirection* of
*stdin* for the `while` loop *compound command* from the *process substitution*.
There is a small practical difference between this redirection from the process
substitution and simple piping of the output of the `quota` command to the
`while` compound command. What is it? Ten points if you can tell it :)


### Iteration 3, thinking again what the function should do...

Duh, much better, right?

```bash
desc_format="%.0s%-27s %8s/%-6s %.0s%.0s%8sk/%sk"
long_format="%.0s%-25s %8s %5s       - %5s %8s %9s %9s     -"

# Takes the format string directly as an argument, note the use of
# non-printing argument format "%.0s", for example call with:
#
#     temp_quotaquery "%.0s%-27s %8s/%-6s %.0s%.0s%8sk/%sk"
#     temp_quotaquery "%.0s%-25s %8s %5s       - %5s %8s %9s %9s     -"
#
function temp_quotaquery() {
    while read line; do
        printf "$1\n" $line
    done < <(quota --show-mntpoint -ws | tail -n +3)
}
```

The "trick" here is to notice that `printf` has a zero width format directive
`%.0s`. We can give `printf` all the fields from the `quota`command without
pre-filtering them, and show only the ones that we wish. Notice also, how
`printf` considers the fields of un-quoted `$line` as separate arguments.


### Epilogue

If you go even a little bit further, this function could be generalized even
more:

```bash
desc_format="%.0s%-27s %8s/%-6s %.0s%.0s%8sk/%sk\n"
long_format="%.0s%-25s %8s %5s       - %5s %8s %9s %9s     -\n"

# printff takes format string as an argument, and for each line in the stdin,
# splits the line into arguments, and prints them using `printf` and the
# given format string.
#
function printff() {
    while read line; do
        printf "$1" $line
    done
}
```

In this context you would call it like this, for example:

```bash
printff "${desc_format}" < <(quota --show-mntpoint -ws | tail -n +3)
```

There exists also specialized utilities for line-by-line processing of text,
such as `awk` and `sed`, which in many cases are better suited to the task than
Bash alone.

In fact the whole wrapper function and loop are not really necessary in this
example. The the same can be achieved using plain `printf`:

```bash
printf "$desc_format" $(quota --show-mntpoint -ws | tail -n +3)
```

How and why does this work (something about how `printf` works, and how shell
*command expansion* works)?

And yes, it is ok to cry now a little bit. The last command line achieves
basically the same goal as the original function...

---
title: Bash language constructs
---


# Conditional expressions

- The _test-commands_ often involves numerical or string comparison tests, but it can be any command that returns a status of zero when it succeeds, and some other status when it fails.
- Bash provides two comparison expressions:
  - command `test` (and it's synonym `[`), which is more portable but limited in features; and
  - the "new test" command `[[`, which has fewer surprises and is generally safer to use — but works only in Bash.

```bash
$ if [ "$LOGNAME" = "cscuser" ]; then
>    echo 'Good day!';
> fi
```


# Conditional expressions

```bash
$ if [ "$LOGNAME" = "cscuser" ]; then echo 'Good day!'; fi
$ if test "$LOGNAME" = "cscuser"; then echo 'Good day!'; fi
$ test "$LOGNAME" = "cscuser" && echo 'Good day!'
$ [ "$LOGNAME" = "cscuser" ] && echo 'Good day!'

$ if [ "$LOGNAME" = "cscuser" ]; then echo 'Good day!'; else echo 'How do you do!'; fi
$ [ "$LOGNAME" = "cscuser" ] && echo 'Good day!' || echo 'How do you do!'

$ [ -e "Desktop" ] && echo "File Desktop exists."
$ [ "Desktop" -nt "exp" ] && echo "Desktop is newer" || echo "exp is newer"

$ [ $(id -u) -ne $UID ] && echo "User ID's do not match."
$ [ $(( $(id -u) + 1 )) -ne $UID ] && echo "User ID's do not match."

$ if [ -e Desktop -a -d Desktop ]; then echo "Desktop is a directory"; fi
$ [[ -e Desktop && -d Desktop ]] && echo "Desktop is a directory"   # This is similar with [[]]
$ [ -e Desktop && -d Desktop ] && echo "Desktop is a directory"   # This does not work
```


# Looping constructs

- Bash supports the following looping constructs (note that a `;` may be replaced with a newline):
  - `until` _test-commands_ `; do` _consequent-commands_ `; done`<br/>
  Execute _consequent-commands_ as long as _test-commands_ has an exit status which is not zero.
  - `while` _test-commands_`; do` _consequent-commands_`; done`<br/>
  Execute _consequent-commands_ as long as _test-commands_ has an exit status of zero.
  - `for` _name_ `[ [in [`_words …_`] ] ; ] do` _commands_`; done`<br/>
  Expand _words_ and execute _commands_ once for each member in the resultant list, with _name_ bound to the current member.


# Looping constructs

```bash
$ for sqr in 1 2 3 4 5 6; do echo $(( 2 ** $sqr )); done
$ for sqr in {1..6}; do echo $(( 2 ** $sqr)); done
$ for (( sqr = 1; sqr <= 6; sqr++ )); do echo $(( 2 ** $sqr )); done
```

```bash
$ for sqr in {1..6..2}; do echo $(( 2 ** $sqr )); done
```

```bash
$ for i in *; do echo "$i"; done   # Equivalent to 'ls -1'
```

```bash
$ for ns in $(grep ^nameserver /etc/resolv.conf); do
>   if [ $ns = nameserver ]; then continue; fi
>   ping -c1 $ns
> done
$ for ns in "$(grep ^nameserver /etc/resolv.conf)"; do ping -c1 ${ns##nameserver }; done
```

{:.Q}

Avoiding loops: Often you can avoid writing a loop by just realizing that a
command can operate on multiple arguments, such as file names, instead
of just a single one at a time. Can you find such commands?


# Looping constructs

```bash
while read -r line; do
  echo "You typed: $line"
  [ "$line" = "end" ] && break
done
```

```bash
until [ "$line" = "end" ]; do
  read -r line
  echo "You typed: $line"
done
```


# Command grouping

- Bash provides two ways to group a list of commands to be executed as a unit.

  ( _list_ )
    : Placing a _list_ of commands between parentheses causes a subshell environment to be created, and each of the commands in _list_ to be executed in that subshell.

  { _list_; }
    : Placing a _list_ of commands between curly braces causes the _list_ to be executed in the current shell context. The semicolon (or newline) following _list_ is required.

```bash
$ ( echo "Directory listing:"; ls ) > dir_list
```




- Shell functions are a way to group commands for later execution using a single name for the group.
- Functions are declared using either of these these syntax:<br/>
_name_ `()` _compound-command_ `[` _redirections_ `]`<br/>
`function` _name_ `[()]` _compound-command_ `[` _redirections_ `]`

- When a function is executed, the arguments to the function become the positional parameters during its execution.

```bash
$ ll () { ls -laF "$@" | more; }
```

https://unix.stackexchange.com/questions/81202/passing-a-code-block-as-an-anon-function

# List processing
map() { while IFS='' read -r x; do "$@" "$x"; done; }
filter() { while IFS='' read -r x; do "$@" "$x" >&2 && echo "$x"; done; }
foldr() { local f="$1"; local result="$2"; shift 2;  while IFS='' read -r x; do result="$( "$f" "$@" "$x" "$result" )"; done; echo "$result"; }
foldl() { local f="$1"; local result="$2"; shift 2;  while IFS='' read -r x; do result="$( "$f" "$@" "$result" "$x" )"; done; echo "$result"; }

# Helpers
re() { [[ "$2" =~ $1 ]]; }

# Example helpers
toLower() { tr '[:upper:]' '[:lower:]'; }
showStructure() { [[ "$1" == "--curly" ]] && echo "{$2; $3}" || echo "($1, $2)"; }

# All lib* directories, ignoring case, using regex
ls /usr | map toLower | filter re 'lib.*'

# All block devices. (Using test, for lack of a full bash [[ … ]].)
cd /dev; ls | filter test -b

# Show difference between foldr and foldl
$ ls / | foldr showStructure '()'
(var/, (usr/, (tmp/, (sys/, (sbin/, (run/, (root/, (proc/, (opt/, (mnt/, (media/, (lost+found/, (lib64/, (lib32/, (lib@, (home/, (etc/, (dev/, (daten/, (boot/, (bin/, ())))))))))))))))))))))
$ ls / | foldr showStructure '{}' --curly
{var/; {usr/; {tmp/; {sys/; {sbin/; {run/; {root/; {proc/; {opt/; {mnt/; {media/; {lost+found/; {lib64/; {lib32/; {lib@; {home/; {etc/; {dev/; {daten/; {boot/; {bin/; {}}}}}}}}}}}}}}}}}}}}}}

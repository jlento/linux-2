---
title: Text utilities and regular expressions
---

# Manipulating text


## Regular expressions

In it's simplest form, a regular expression is a string of symbols to match "as is".

  | Regex     |   Matches   |
  |-----------|-------------|
  | `abc`     | **abc**def  |
  | `234`     | 1**234**5   |

```bash
grep '234'
```


## Basic regexes vs. extended regexes

The Basic Regular Expressions or _BRE flavor_ standardizes a flavor similar to the one used by the traditional UNIX `grep` command. The only supported quantifiers are `.` (dot), `^` (caret), `$` (dollar), and `*` (star). To match these characters literally, escape them with a `\` (backslash).

Most modern regex flavors are extensions to the BRE flavor, thus called _ERE
flavor_. By today's standard, the POSIX ERE flavor is rather bare bones. We will
be using extended regexes, so:

```bash
alias grep='grep --color=auto -E'
```


## Quantifiers

To match several characters you need to use a quantifier:
  - `*` matches any number of what's before it, from zero to infinity.
  - `?` matches zero or one of what's before it.
  - `+` matches one or more of what's before it.

  | Regex     | Matches                         |
  |-----------|---------------------------------|
  | `23*4`    | 1**24**5, 1**234**5, 1**2334**5 |
  | `23?4`    | 1**24**5, 1**234**5             |
  | `23+4`    | 1**234**5, 1**2334**5           |

Does `grep '23*4'` match '123456', '122456' and/or '1233334456'?


## Regexes are greedy

By default, regexes are greedy. They match as many characters as possible, as
found out in the previous question. You can define how many instances of a match
you want by using ranges:

  - `{m}` matches only _m_ number of what's before it.
  - `{m,n}` matches _m_ to _n_ number of what's before it (`{0,1}` = `?`).
  - `{m,}` matches _m_ or more number of what's before it (`{1,}`= `+`).


## Special characters

A lot of special characters are available for regex building. Here are some of
the more usual ones:

  - `.` matches any single character.
  - `^` matches the beginning of the input string.
  - `$` matches the end of the input string.
  - `\w` matches an alphanumeric character, `\W` a non-alphanumeric.
  - `\` to escape special characters, e.g. `\.` matches a dot, and `\\` matches
    a backslash.

Especially anchors `^` and `$` are often useful. How would you match an empty
line, a line that contains no characters, or only spaces?

  | Regex     | Matches                      | Does not match       |
  |-----------|------------------------------|----------------------|
  | `1.3`     | **123**4, **1z3**, 0**133**  | 13                   |
  | `1.*3`    | **13**, **123**, **1zdfkj3** |                      |
  | `\w+@\w+` | **a@a**, **email@oy**.ab     | ,.-!"#€%&/           |
  | `^1.*3$`  | **13**, **123**, **1zdfkj3** | x13, 123x, x1zdfkj3x |


## Character classes

You can group characters by putting them between square brackets. This way, any
character in the class will match any *one* character in the input.

  - `[abc]` matches any of a, b, and c.
  - `[a-z]` matches any character between a and z.
      - Note: if you want to include `-` in the matching charaters, add it as
        the first or last entry in the class, otherwise it will be interpreted
        as a range definition!
  - `[^abc]` matches anything other than a, b, or c.
      - Note that here the caret `^` at the beginning indicates "not" instead of
        beginning of line.
  - `[+*?.]` matches any of +, *, ? or the dot.
      - Most special characters have no meaning inside the square brackets.

Can you find three string examples that match and three examples that do not
match `[^ab]`, `^[1-9][0-9]*$`, `[0-9]*[,.]?[0-9]+`? Can you construct any
tricky ones?


## Grouping and alternatives

It might be necessary to group things together, which is done with parentheses
`(` and `)`.

  | Regex   | Matches                    | Does not match |
  |---------|----------------------------|----------------|
  | `(ab)`  | **ab**, **abab**, a**ab**b | aa, bb         |

Grouping itself usually does not do much, but combined with other features turns
out to be very useful. The OR operator `|` may be used for alternatives.

  | Regex      | Matches                    | Does not match |
  |------------|----------------------------|----------------|
  | `(aa|bb)`  | **aa**, **bbaa**, **aabb** | abab           |

You can grep all lines that have either word 'Juha' and 'Thomas' with `grep -e
Juha -e Thomas`, by giving grep two separate regexes to match. How can you
achieve the same with a single regex?


## Subexpressions

With parentheses, you can also define subexpressions to store the match after it
has happened and then refer to it later on.

  | Regex       | Matches                      | Does not match |
  |-------------|------------------------------|----------------|
  | `(ab)\1`    | **abab**cdcd                 | ab, abcabc     |
  | `(ab)c.*\1` | **abcabc**, **abcdefab**cdef | abc, ababc     |

Command `sed` uses quite similar mechanism for search and replace.


## Some practical(?) examples

Check for a valid format for email address:

```bash
grep '\w[A-Za-z0-9._+-]+[^.]@\w[A-Za-z0-9.-]+\.[A-Za-z]{2,}'
```

Here

  - `\w[A-Za-z0-9._+-]+[^.]` matches all acceptable characters not starting or
    ending with a dot.
  - `@` matches the @ sign.
  - `\w[A-Za-z0-9.-]+` matches any domain name, incl. dots.
  - `\.[A-Za-z]{2,}$` matches a literal dot followed by two or more characters
    at the end.

Does 'firstname.lastname+noreply@helsinki.fi' qualify as a valid email address?
Can you send email to yourself, if you add '+something' to the local part (the
part before '@') of your email address?


# Text utilities

## What we will work over

- Your shell has built-in spread-sheet functions
- You can find/extract/combine text row or column-wise

You can download the short text files used in the exercise with either of the
commands below

```bash
wget https://jlento.github.io/linux-2/handson/{count,sheep}.txt
curl -O -O https://jlento.github.io/linux-2/handson/{count,sheep}.txt
```


## Adding files side-by-side: paste

```bash
paste [-d del -s] file1 file2 [file3 …]
```

Merges lines of several input files.

- `-d` insert different delimiter *del* (between merged lines) than tabulator (default)
- `-s` sequential in case of more than two files: `[(file1 + file2) + file3] + file4`

Let's try the following:

```bash
paste count.txt sheep.txt > counting_sheep_tab.txt
paste -d ' ' count.txt sheep.txt > counting_sheep_space.txt
```

What command can you use to make spaces and tabulators "visible", for example
replacing them with `_` and `>` in the previous paste examples?


## Trimming files: cut

```bash
cut [-d del -f no -s] file1 file2 …
```

Extracts fields/columns from each line of files.

- `-d del` use different delimiter *del* (to identify fields) than tabulator
      (default)
- `-f no` select fields *no* 
- `-s` skip lines not containing delimiters (e.g., header lines)

How would you cut away the counting numbers column from `counting_sheep_tab.txt`?


## Counting lines [and sheep]: wc

```bash
wc [-l -w -m -c] file1 [file2 …]
```

Counts lines, words as well as characters or bytes in a file (`wc` stands for
**w**ord **c**ount):

  - `-l` count lines
  - `-w` count words
  - `-m` count characters
  - `-c` count bytes
  - without arguments displays lines, words, and byte-counts (as `-l -w -c`)
  - a word is a non-zero-length sequence of characters delimited by white space

```bash
wc -l counting_sheep_space.txt
```


## Combining files end to start: cat

```bash
cat [-n -E -v -T] file1 file2 …
```
Con**cat**enates files and prints to stdout.

  - `-n` numbering output lines (e.g., source-code listing)
  - `-E` indicate ends with a $
  - `-v` show non-printing
  - `-T` indicate tabs

```bash
cat -n counting_sheep_space.txt > sheep_lines.txt
```

```bash
cat -T -E counting_sheep_tab.txt
```


## Extracting beginning and end of files: head and tail

```bash
head [-n N] file1 [file2 …]
```

Extracts head of files.

- `-n N` display *N* first lines

```bash
tail [-n N -f --pid PID] file1 [file2 …]
```

Extracts tail of files
  - `-n N` display *N* last lines
  - `-f` continuously display updates of file (useful to display log-files)
  - `--pid PID` terminate tail-command in sync with termination of process with
    process ID *PID*

How would you extract all lines after the 3rd line?


## Bringing order into files: sort

```bash
sort [-d -f -g ] file1 [file2 …]
```

- Sorts lines of text files (alphabetical or numerical).
  - `-d` dictionary (alphanumeric) order
  - `-f` ignore upper/lower case
  - `-g` general numeric
- Spot the difference:

```bash
$ sort -d counting_sheep_space.txt
$ sort -g sheep_lines.txt
```


## Removing redundancy in files: uniq

```bash
 uniq [-c -f -s -w ] file1 [file2 …]
```

Filters adjacent matching (redundant) files. 

  - `-c` prefix lines by number of their occurrence
  - `-f N` avoid comparing the first *N* fields
  - `-s N` avoid comparing the first *N* characters
  - `-w N` compare not more than *N* characters/line

Skips the first column (the previously inserted numbers) and matches in max. 10
characters (i.e., avoiding the later columns) and prefixes the number of
occurrence (hint: try with –f 2):

```bash
$ uniq -c -f 1 -w 10 sheep_lines.txt
```

Because `uniq` only compares adjacent lines, you may need to sort the text
first. But is there an option in sort that drops redundant lines?


## Awk - text processing

Awk was developed at Bell Labs in 1977 by Aho (not Esko, but Alfred Vainö!),
Weinberger, and Kernighan. It is a versatile scripting language which resembles
C (surprise! - Kernighan & Ritchie). It is powerful with spread-sheet type /
tabulated data.

Typical usage is in one-liners with matching / reordering / formatting /
calculating fields from the existing tables of data, but any longer programs are
very possible.

The general idea of Awk program is that it reads text line by line, checks which
of the patterns given in the Awk program match the line, and runs the associated
blocks of code if the line matches the pattern. In general something like this:

```
pattern1{block-of-awk-commands}
pattern2{block-of-awk-commands}
...
```

Patterns can be a regular expression, or many of the other kind of expressions
understood by Awk. If pattern is omitted, the block of commands is executed for
every line.

Remember to check `man awk`!


## awk - command line 

To print the second column (word) of a file, type the following to the terminal:

```bash
$ awk '{print $2}' /etc/mime.types
```

By default you assume that the file is separated by blank spaces. If you look at
file `/etc/passwd`, for example, you see that the fields are separated by colons
`:`. How do you tell Awk to use `: ` as field separator, and print the second
column from `/etc/passwd`?

When you construct your own tabular data text files, which characters are "good"
field separators, and why?

What do the file suffix `.csv` or `.tsv` abbreviations mean?


## awk pattern matching 

Awk commands allow to test the input against regular expression (enclosed in `/
/`):

```bash
awk '/regexp/ { action }' file
```

An exclamation mark inverts the match:

```bash
awk '!/regexp/ { action }' file

```

For example, we want to print all relevant lines in `/etc/mime.types`, i.e.,
exclude all comment-lines that start with `#`:

```bash
awk '!/^#/' /etc/mime.types | less
```


## awk scripts

You can save your awk program to a separate file. Why should you?

- Sometimes one-liners get too long.
  - You want to be able to easily reproduce your awk-command.
  - Useful if you need to declare user defined functions through command scripts.
  - Not mandatory, but useful to give suffix .awk.

Script is read using option -f:

```bash
awk -f myscript.awk inputfile.txt > outputfile.txt
```

## Special patterns

You can run actions *before* and *after* the text file is parsed. This is
achieved by special patterns `BEGIN` and `END`. `BEGIN` is often used to
initialize variables before the first input line has been read in, and `END` is
usually used to print some summary information after input has been finished.


Let's write a script to display all nologin accounts in the system. Use your
favourite text editor and create a new file called `nologin.awk`. Fill it with
the following contents and save thereafter:

```awk
BEGIN {
  x=0
}
/nologin/ {
  x=x+1
  print x, " ...", $1
}
END {
  print "------------------"
  print "nologins=", x
}
```

Use `-f` option to launch the script:

```bash
awk -f nologin.awk /etc/passwd
```

How to get all users _with_ login accounts are shown?

How can you produce a similar result as with `grep`?


## Field separator

You can use regex to specify multiple sepration characters. For example, `FS` is
either colon `:` or comma `,` (`NF` is number of fields in the current line):

```bash
echo "0 1:2,3 4" | awk -F"[:,]" '{print "entries:" NF " last column: " $NF}'
```

spot the difference with not using regexp:

```bash
echo "0 1:2,3 4" | awk -F":," '{print "entries:" NF " last column:" $NF}'
```
or also including a blank:

```bash
echo "0 1:2,3 4" | awk -F"[:, ]" '{print "entries:" NF " last column:" $NF}'
```


## Counters of columns, rows and records	

Awk fields are accessed through variables `$1`, `$2`, …, `$(NF-1)`, `$(NF)`.
`NF` (Number of Fields) is the number of fields on each line (# columns in row).

```bash
echo "0 1:2,3 4" | awk -F"[:, ]" '{print "entries:" NF " first:" $1 " last:" $NF}'
```

Variable `$0` refers to the whole input row.

```bash
awk -F":" '{printf "user: %s\n    whole line: %s\n", $1, $0}' /etc/passwd
```

`printf` enables formatted printout - we will discuss in more details later.
`NR` (Number of Records) is the number of input records (lines):

```bash
awk 'END {print NR}' /etc/passwd
```

Much simpler still: `wc -l /etc/passwd`


## Loops in awk

Arithmetic loops in awk are very much C-style:

```awk
for (countervar=initvalue; condition of validity; increment) {action}
```

e.g., displaying single fields in row:

```bash
awk -F: '{for (i=1; i<=NF; i++) {print i, $i}; print " "}' /etc/passwd
```

or to invert

```bash
awk -F: '{for (i=NF; i>=1; i--) {print i, $i}; print " "}' /etc/passwd
```

or only odd lines

```bash
awk -F: '{for (i=1; i<=NF; i=i+2) {print i, $i}; print " "}' /etc/passwd
```

There is a second style of for loops, that is useful with array variables.


## Output in awk

Generic `print` just takes either strings or variables.

```bash
awk -F: '{print "string", $2, $NF, NF, NR}' /etc/passwd
```

Alternatively, `printf` offers a wide range of C-style formatting capabilities, e.g.:

```bash
date | awk -F"[ :]" '{printf("Time=%2d hours and %2d minutes\n", $4, $5)}'
```

Remember not to forget to supply the newline `\n` in `printf`! The generic print
already adds that for you automatically. Formats are `%d` for integer, `%f` for
floats,`%e` for scientific, `%s` for string, etc. Lengths can be prescribed:

```bash
  $ echo "1234.5678 910.16" | awk '{printf "%4.2f %1.3e \n", $1, $2}'
```

All in all, quite the same as `printf` in C or Bash.


## Variables in awk

In addition to the already mentioned awk internal variables `NR,NF,$1,$2,...`, Awk has
user defined variables. User defined variables are conventionally written in lowercase.

Variables can be set inside script/command line

```bash
awk 'BEGIN{myvar="Hello !"; a=1; b=2; print myvar, a, "+", b "=", a+b}'
```
or can be passed to awk from outside,

```bash
awk -F: -v n=1 '{print $n}' /etc/passwd
```

Why everything in in inside `BEGIN` section in the first example?

In the second example, try with `n=2,3,...`. What exactly does the `$n` refer to?


## Array variables

We can use arrays in awk,

```bash
awk 'BEGIN{t[1,1]=1; t[1,2]=2; i=1; print t[1,2], t[i,i], t[i,1]}'
```

Awk arrays are in fact 1 dimensional associative arrays (hash tables). The index
into an array does not have to be an integer number, it can be (actually is) a
string,
  
```bash
awk 'BEGIN{car["sweden"]="volvo"; car["russia"]="lada"; car["usa"]="pontiac"; //
for (i in car) {print i, ":", car[i]}}'
```

NB: `//` at the end tells bash to continue the line - you may type that in one row.

Let's say you give arguments to your Awk program like you would give arguments
to your shell program,

```bash
awk '{prog}' arg1 arg2 arg3 ...
```

How do you loop conveniently over all array elements?

Can you write an Awk program `prog` that prints it's command line arguments?


## Built-in functions 

Awk has the usual numerical functions: `int, exp, log, sin, cos, sqrt`,

```bash
$ for ((x=1; x<=180; x++)); { echo $x; } > angles.dat
$ awk '{print $1, cos($1*3.1415927/180.0)}' < angles.dat | tee cosine.dat
```

string functions, `tolower, toupper, sprintf,  match, …`, etc.

```bash
$ awk '{print toupper($0)}' /etc/group
```

and bit manipulation functions: `and, or, xor, …`

```bash
$ awk 'BEGIN{printf "and(1,0)=%x or(1,0)=%x \n", and(1,0), or(1,0)}'
```

For more details, see e.g. [gawk manual
pages](https://www.gnu.org/software/gawk/manual/html_node/String-Functions.html
"gawk manual pages")


## Control statements

The `if-else` statement (save into `sign.awk`):

```awk
{
  printf "cos(%f)=%2.2f, ", $1, $2
  if ($2 > 0) {print " positive"}\\
  else {print "negative"}
}
```

```bash
awk -f sign.awk cosine.dat
```


## Logical operators: and `&&`,  or `||`

```awk
# write awk script sign_product.awk
BEGIN {print "enter 2 numbers separated by space (end with CTRL+D)"}
{
    if (($1 == 0) || ($2 == 0)) {
        sign="zero"
    }
    else if ( (($1 < 0) && ($2 > 0)) || (($1 > 0) && ($2 < 0)) ) {
        sign="negative"
    } else {
        sign="positive"
    }
    printf "product of %f x %f is %s\n", $1, $2, sign
}
```

```bash
awk -f sign_product.awk
```


## Further resources

Like always, man-pages:

```bash
man awk
info awk
```

Awk web-manual by GNU [https://www.gnu.org/software/gawk/manual](https://www.gnu.org/software/gawk/manual/ "gawk manual pages")
The Internet, e.g.: [https://stackoverflow.com](https://stackoverflow.com/search?q=awk "stackoverflow")

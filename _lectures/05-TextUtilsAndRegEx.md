---
title: Text utilities and regular expressions
author: CSC Training
titleslide: true
lang: en
---


# Manipulating text

Unix has powerful tools to search and modify _line oriented_ text files, such as
`grep`, `awk`, `sed`, `diff`, `sort`, `cut`, `paste`, `fmt`, `column`, ... Many
of these tools can match string patterns, using *regular expressions*.

Warning: Using these tools for other than line oriented text file formats, such
as XML, YAML, or JSON, may be tricky.


# Regular expressions

Regular expressions provide a rich "language" for matching string patterns.
There are couple of things to know about them, though:

- There are many variants of regular expressions, basic and extended POSIX,
  Perl, etc. Shell *pattern matching* is similar, but not the same...
- The "greedy" logic of regular expressions may be difficult to catch at first.


# Regular expressions in Bash

Bash has a quite useful regular expression binary operator `=~` that can be used
within the conditional expressions `[[...]]`. The use of this operator should be
preferred over calling external programs in shell scripts.

Tip: In general, one should prefer internal bash operators and constructs over
calling external programs in shell scripts whenever possible, within reason :)


# Regex Tips

Because `.*` is greedy, it is often necessary to be very specific, or tell what
not to match!

| `^`, `$` | Anchors, match the begin or the end of the string |
| `[^X]*` | Match anything but `X` |

How to "easily" list `man bash` chapters:

```bash
zcat $(man -w bash) | sed -nr 's/^\.SH +"?([^"]*)"?/\1/p'
```

{:.Q}

How do you quickly jump to "SHELL BUILTIN COMMANDS" chapter in `man bash`?


# Text utilities

Personal favorites:

| grep    | Filter the interesting lines     |
| awk     | Spreadsheet programming language |
| sed     | Search and replace               |
| sort    | Sort lines                       |
| fmt     | Format text                      |
| column  | Format tables                    |
| gnuplot | Visualize data                   |

Check the corresponding man pages!

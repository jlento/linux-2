---
title: Study tips
titleslide: true
---


# Problem solving

1. Google "Bash how to ..." and what you want to achieve, to get
   ideas. Do this even when you already know some way to solve the task.
2. Check the command details from the man pages
3. Try it out on the command line!
4. Develop longer command chains gradually, checking that each step
   produces intended output.

{:.Q}

Googling challenge: If you have a bash programming question that you
cannot find a decent answer, throw the Googling challenge to your
fellows in the discussion forum!


# Man pages

- Learn how to search (`/`, `n`) and jump (`p`, `G`) in man pages
- Learn the conventions (structure) of man pages, see `man man-pages`
- Different machines have different versions of the programs. Man pages
  document exactly the version in your machine.

{:.Q}

Pager: What is pager and what does it do? Name few pagers? What pager
is your man command using?


# Man Bash

The most important course material for this course is `man bash`. The lectures
and exercises contain terms (and operators) that you can search from the Bash
man page.

{:.Q}

Man bash. How would you jump to the section of the Bash man page that describes
*Parameter expansion*?


# Debugging shell scripts

You can turn on command echoing with `set -x`, to see how the variables
are expanded and how the program flows.

{:.Q}

Command echoing: How do you turn off command echoing? What other means of
shell script debugging can you find?


# Keep notes

Save useful commands and short scripts to a file for later reference, and push
your notes to GitHub every now and then. Bash history keeps all the commands
that you typed, but it also keeps the ones that did not work...


# Bash-fu attitude!

Do not settle with the first solution. Your next solution can be more
obvious and succinct.

In practice, if you receive a shell script from a colleague, do not
expect it to work for you out of the box. As is, it will not work for
your colleague either, most likely. Instead, take it as very valuable
documentation, and while fixing the details, develop it to make it
just a little bit more concise, obvious and beautiful.

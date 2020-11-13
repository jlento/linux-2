---
title: Linux-2
author: CSC Training
---

CSC training course for advanced command line use, common GNU/Unix
tools, and Bash programming.

To whom: You are already using Linux command line and writing Bash
scripts, but would like to know better how do these actually
work. After this course you should be able to use command line more
efficiently, and write more elegant shell scripts, and fix the
misbehaving ones.

Practicalities for the [online course]({{ site.baseurl }}{% link
StudyGroupDec2020.md %}) and the regular [class room course at CSC's
premises]({{ site.baseurl }}{% link WhereAboutsCSCPremises.md %}).


# Course pre-requirements

Before Linux-2, you should have completed
[Linux-1](https://github.com/csc-training/linux-1/tree/ThomasZ), or
equivalently,

- be familiar with basic Linux command line use and shell programming
- be familiar with some text editor such as Emacs, Vi or Nano
- have access to a Linux (virtual) machine, preferably with rights to
  install additional utility software


# Lectures

Lectures as HTML slides. Use cursor keys or click left/right side of
the slide to change it. You can print the slides to PDF with Chrome,
just tweak the print settings a bit.

{% for slide in site.lectures %}
- [{{ slide.title }}]({{ slide.url | relative_url }})
{% endfor %}


# Excercises

{% for exercise in site.exercises %}
- [{{ exercise.title }}]({{ exercise.url | relative_url }})
{% endfor %}


# Extras

{% for extra in site.extras %}
- [{{ extra.title }}]({{ extra.url | relative_url }})
{% endfor %}

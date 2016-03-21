---
layout: post
title: Vim, Syntastic, and LaTeX
modified:
categories: blog
excerpt:
tags: []
image:
  feature:
date: 2016-03-09T18:27:48-06:00
---

You're editing your [LaTeX][] buffer in [Vim][], you write out the buffer, and
suddenly [Syntastic][] screams at you that errors are everywhere.  It's not your
fault.  Maybe.  Double-check that the errors are not actually helpful and that
your document compiles as expected.

## Errors, erroneous errors

When everything is working perfectly---your document compiles, things are
laying out as expected---but Syntastic is reporting errors to you anyway, it
can get annoying.  More than being annoying, these false-positive error
statements take your attention away from other legitimate concerns.

### Checkers isn't just a game...

First, let's explore what checkers Syntastic is using on your buffer.  Fire
off the helpful `:SyntasticInfo` and notice the lines for "Available checkers"
and "Currently enabled checkers".  Assuming you have a generic setup and you
ran the command with a `.tex` file in the local buffer, Syntastic will report
that it both has and is using the checkers LaCheck and ChkTeX.

#### ChkTeX

When *warnings* are reported, that's ChkTeX giving you grief.  Fortunately,
there's a built-in way to make ChkTeX ignore a warning.  Just add a
specially formatted comment string to the end of the offending line.  For
example, if you get warning 8 on line 14, append a comment like this to line
14:

{% highlight latex %}
foo bar baz   % ChkTeX 8
{% endhighlight %}

If this isn't enough for you, or you'd like to learn more about [ChkTeX][c], I'd
suggest taking a look at its [manual][m].

#### LaCheck

Instead of warnings, LaCheck reports *errors*.  This can be very helpful, but
when LaCheck gets confused, it becomes unhelpful.  Unfortunately there's no
way to help it be less confused or to tell it to ignore a section of your
document.  As reported by user Yossi Gil on [this tex.stackexchange
post][ttp], LaCheck's man page says:

> LaCheck gets confused by advanced macros, is fooled by simple macros, can't
> figure out if you use a non-standard way to switch italic on or off, does
> not like TeX at all, *does not provide any options to turn off specific
> warnings*, and is at best a crude approximation.

Ok, so there's no way to solve this issue in a way that doesn't cripple
LaCheck.  That's fine, we can use a hammer.  Time for a custom command in your
`.vimrc` file.

{% highlight vimscript %}
"" Tell lacheck to SHUTUP
" `command!` defines a new command and redefines any existing commands of the
" same name
" `SHUTUP` is all-caps so that it is unlikely to clash with any other command
" `b:syntastic_checkers` is a buffer-specific setting (see Syntastic
" documentation)
" `|` strings in a second command, and `\` continues the command on a new line
" `SyntasticCheck` re-runs Syntastic
command! SHUTUP let b:syntastic_checkers=["chktex"] |
    \ SyntasticCheck
{% endhighlight %}

Now when you get LaCheck errors that aren't errors, just fire off a quick
`:SHUTUP` and you'll have your peace.



[LaTeX]: https://latex-project.org/intro.html "LaTeX - A document preparation system"
[Vim]: http://www.vim.org/about.php "Vim - About"
[Syntastic]: https://github.com/scrooloose/syntastic/blob/master/doc/syntastic.txt "Syntastic manual"
[ttp]: http://tex.stackexchange.com/a/155451 "To quote the [LaCheck] man page:"
[c]: http://www.nongnu.org/chktex/ "ChkTeX homepage"
[m]: http://www.nongnu.org/chktex/ChkTeX.pdf "ChkTeX manual"

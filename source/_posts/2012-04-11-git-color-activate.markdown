---
title: Activate color in git
date: 2012-04-11 02:30
category: linux, git
lang: en
---

I've got sick of adding the `--color` flag to each `git` command I type so I went through the `git
config` documentation and found several `color.*` options. Those options can be changed by using
the `git config VAR VALUE` command, so initially I ran:

	code-block::bash
	$ for CONF in branch grep diff interactive status;\ 
		do git config color.$CONF auto --global;\ 
	  done

To activate them all by setting each single color option to `auto`. But after reading a little more
I found a much simpler way to have everything using color by default. Just simply type:

	$ git config color.ui true

And that will do the trick. It is very nice and useful to read coloured output when using git,
specially when reviewing diffs in the midst of a `git add -p` messy jam.

My `.gitconfig` ended up like this:

	[color]
		    ui = true

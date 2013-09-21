---
title: Export part of a git repo to a new one keeping history
date: 2012-06-01
category: linux
tags: git
lang: en
---

I maintain my personal scripts (only a few by now) on a 
[public repository](github.com/jvrsantacruz/scripts) on GitHub, so it forces myself to take them more
seriously and keep it clean. It looked like a good idea back then, because all I've got were just
not enough for a repo on its own. So I kept them together in separate directories under the same
repo this way:

	scripts
	 |- README.md
	 |- project1
	 |   `- script1.py
	 `- project2
	     `- script2.py

And each commit in the repo clarifies which project modifies by preceding it with a *code* this way:

	d65bf0e project1: Adds test_rotate function to test rotate verb
	2943d69 project1: Removes unused TestFileOperations.newfile
	..
	dc37490 project2: Rewrites comments
	9d00b0a project2: Fixes output-based tests redirecting stderr

But as some of them are now well grown up, like `backup.py`, I thought they deserved more
visibility and wanted those specific directories out into a new repo... but keeping the history with
them, and without the commit message *code* boilerplate.

There is one easy obvious way to to this, by *(1)* cloning the repository, then *(2)* using `git
filter-branch` to remove the undesired directories and *(3)* to rewrite commits messages
afterwards.

	# 1: Clone repository
	$ git clone --no-hardlinks /original-repo /new-repo

	# 2: Filter away undesired stuff
	$ git filter-branch --tree-filter "rm -rf project2" --prune-empty HEAD

	# 3: Filter 'project1:' code from commits messages
	$ git filter-branch --msg-filter "sed 's/project1: //' --prune-empty HEAD

The `--prune-empty` option makes git to discard commits that become empty after the filter and
therefore are now useless.

You can se the difference from the original repo and the [new one](github.com/jvrsantacruz/backup) for `backup`.

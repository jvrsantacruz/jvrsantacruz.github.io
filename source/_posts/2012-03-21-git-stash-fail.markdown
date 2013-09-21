---
title: Recover a lost stashed commit in git
date: 2012-03-21 18:44
category: linux
tags: git
lang: en
---

If it happens that you confuse `git stash pop` (apply and remove an stashed commit) with `git stash
drop` (simply remove it), do not cry yet. `git stash drop` yells the hash of the dropped commit, so
you still have a chance to recover it.

	git stash drop
	Dropped refs/stash@{0} (e692be2bc305348380c7c71a80867539babad3d7)

If you manage to keep the hash, you can just do:


	git stash apply e692be2bc305348380c7c71a80867539babad3d7


And save it.

So close.

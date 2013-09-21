---
title: Change email in multiple git commits
date: 2012-05-07 18:12
category: linus, git
lang: en
---

I wanted to *replicate* an old git repo, in which I used as author my old university-work email,
but using my current gmail account as email. Changing it for both *commiter* and *author* for all
commits involves rewriting the whole repo. It's not difficult at all, but better work on a copy of
the repo, and remember that if you shoot yourself on the foot you can always get back by doing `git
reset --hard HEAD`.

The rewrite can be easily done by using the `filter-branch` tool applied to all branches at once:

	code-block::bash
	git filter-branch --env-filter '
	 GIT_AUTHOR_EMAIL=<new.email@gmail.com>
	 GIT_COMMITER_EMAIL=<new.email@gmail.com>' --all

This is the *nuclear* way, it will rewrite absolutely all commits. This won't work straight if you
have code from collaborators, then you'd lose their emails after the unconditional rewrite.

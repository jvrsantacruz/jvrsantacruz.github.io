---
title: "Migrate from Pelican to Jekyll"
layout: post
date: 2013-09-22 12:29
comments: true
categories: linux
published: true
---

After more than a year using [Pelican](http://docs.getpelican.com/en/3.3.0/) I've just decided to move this blog into [Jekyll](http://jekyllrb.com/). 
I'll be using the convienient [Octopress](http://octopress.org/) setup which nicely integrates with [Github Pages](http://pages.github.com/).

Once everything in my server has been backup and had this running along for a bit with nothing
going amiss, then I think I'd shutdown my paying server, and hopefully save some money (*~100€ yearly*).

Yeah, I know that I could have just started deploying my previous *Pelican* blog into Github.
The main reason for a change is that *Pelican* required me a bit of fiddling and hacking around; which, ironically, is actually the reason I chose it in first instance.
But this time, I want a little more features, for a little less effort. And if I end up forcing myself to dig into another system, I don't want it to be in Python this time. 
Do not be mislead, I love Python; I just want to pop out of my comfort zone and see what happens.

## The migration part

I had to convert about 3 years of writing (34 posts) stored as many text files, scattered within a 3 level hierarchy partitioned by year and month:

```
blog/pelican/src/2010
·
·
└── 11
    └── i-want-my-vim-back.md
blog/pelican/src/2011
·
·
└── 06
    └── exportacion-de-m3u-en-python-y-bash.md
blog/pelican/src/2012
·
·
└── 10
    ├── bjarne-stroustrup-c11.md
    └── nexus-one-miui-2.9.md
```

Each file having a header:

```
title: List directories within a dir in C
date: 2012-03-06 03:00
category: linux
tags: linux, c
lang: en
```

This setup is pretty close to the one that *Jekyll* uses, in which: 

1. Post file names must start with the date.
2. The header have the same fields, but must be valid *yaml* at the start of the file, using `---` lines as separator.

So it was just a matter of finding, copying and properly renaming all the posts from the *Pelican* source directory.

```bash
for post in $(find -name *.md) 
do 
	date=$(grep -i "date: " $post | sed -e "s/[dD]ate: \([0-9-]\+\).*/\1/g")

	if [[ ! -z $date ]] 
	then 
		mv $post octopress/source/_posts/$date-$(basename $post)
	fi 
done
```

*The previous listing was prettyfied for displaying, the actual command was more like:*
`for post in $(find -name *.md); do date=$(grep -i "date: " $post | sed -e "s/[dD]ate: \([0-9-]\+\).*/\1/g"); if [[ ! -z $date ]]; then mv $post $date-$(basename $post); fi done`

And then

```
$ rename .md .markdown *.md
```

After that I had to manually add the `---` separator to each post header. 
I couldn't find a simple way of running a multiline `sed` replacement for all of them, so I opened all files in with `vim *.markdown`, recorded a macro, and runned
it for all the 30 buffers. It took me about a minute or two.

Then copy the `img` directory, which contained the assets, maintaining the same relative path for
all post images, and there  you go: `rake generate`.

So, here we are, new hosting, new blog.

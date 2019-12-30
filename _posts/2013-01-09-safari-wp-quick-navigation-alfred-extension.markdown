---
layout: post
status: publish
published: true
title: Safari WP Quick Navigation Alfred Extension
author: DJ Madeira
author_login: webmaster
author_email: djmadeira@me.com
wordpress_id: 8
wordpress_url: http://djmadeira.com/?p=8
date: '2013-01-09 23:08:54 -0500'
date_gmt: '2013-01-09 23:08:54 -0500'
tags:
- archived
- alfred
- extension
- powerpack
- applescript
- programming
- Mountain Lion
- Mac
comments: []
---
**I don't know if this still works, but even if it does there are better tools for this, such as [WordPress CLI](http://wp-cli.org). Do not use.**

<img src="http://sitesahoy.com/misc/wphopperscreen.png" alt="" width="626" height="173" />

Alright, so I was working on a WP site and somebody had hidden the wordpress toolbar for my user. I was too lazy to go turn it back on, so I kept typing /wp-admin/ in the address bar to get back to the admin panel (now that I write that, I don&rsquo;t know why I didn&rsquo;t use the back button, but regardless.)

So anyway, it gave me the idea to write an Alfred extension that lets me jump to some key places in wordpress without showing the toolbar (which is nice, but it can be difficult if your trying to style a page and it&rsquo;s overlapping something) or typing in the address bar. I figured it would be a time-saver.

It was not. I ended up spending about an hour and a half getting this thing working (I&rsquo;m new to applescripts/programming in general), and I&rsquo;m still pretty sure there are much easier &amp; better ways to accomplish what I wrote. But here it is; it does what it&rsquo;s supposed to accomplish and will probably save me a lot of hassle in the future.

Usage: &ldquo;wp {query}&rdquo;

Or to jump to the dashboard, leave off the query: &ldquo;wp&rdquo;

Keywords: posts, pages, media, comments, widgets, styles, plugins, settings, and a few other I don&rsquo;t remember. You can add your own fairly easily by just adding another if statement.

<strong><a href="http://sitesahoy.com/misc/WPHopper.alfredextension" target="_blank">Download</a>&nbsp;</strong>(requires Alfred Powerpack to do anything, obviously. Also, it only works with Safari right now.)

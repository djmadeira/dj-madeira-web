---
layout: post
status: publish
published: true
title: Benchmarking a MacBook Pro without the bottom cover
author: DJ Madeira
author_login: webmaster
author_email: djmadeira@me.com
wordpress_id: 557
wordpress_url: http://djmadeira.com/?p=557
date: '2015-01-04 21:03:23 -0500'
date_gmt: '2015-01-04 21:03:23 -0500'
tags: 
- archived
comments:
- id: 19379
  author: David Madeira
  author_email: ''
  author_url: https://brid-gy.appspot.com/like/twitter/dj_madeira/557245203388841985/103156176
  date: '2015-01-20 05:33:42 -0500'
  date_gmt: '2015-01-20 05:33:42 -0500'
  content: favorited this.
---
My first (and current) real computer, a late 2011 15" Macbook Pro, is a great machine. I like how it looks, I like how it feels, I like the size, the weight, I even like how it looks on the inside. I've replaced the RAM, swapped the boot drive with an SSD, changed out the disk drive for the old HDD, and just generally messed around with it for as long as I've had it.

There are 2 things I don't like about it: the graphics performance, and the fan noise. The fans are pretty noisy when they get significantly above minimum speed (2000 RPMs), and they always kick on when the CPU goes above ~30% or any kind of graphics task is started (such as a 3D game).

So when I read an article by <a href="http://ifixit.org/blog/6882/why-i-drilled-holes-in-my-macbook-pro-and-put-it-in-the-oven/">an iFixit guy who drilled holes in a similar model</a> to solve some heat-related issues, I was intrigued. Even though his issues were system-destroying, whereas mine are only annoying, I had long suspected that the cooling issues and the performance issues were related. So I decided to do some testing to see how much of a gain I could get from removing the bottom cover altogether and operating it that way.

My setup was this: I removed the bottom cover (easily done by removing the 10 phillips screws securing it) and suspended the laptop with 2 tissue boxes. I ran benchmarks with this configuration, then ran those same benchmarks with the cover attached (though not screwed in; there are clips that secure it in the middle that hold it in place sufficiently for testing.)

Here were my results with various benchmarking applications: left is with case attached, right is without.

<a href="http://djmadeira.com/wp-content/uploads/2015/01/geekbench.jpg"><img class="size-full wp-image-558" src="http://djmadeira.com/wp-content/uploads/2015/01/geekbench.jpg" alt="With case: 2476 (single core), 9183 (multi-core). Without case: 2545 (single core), 9332 (multi-core)" width="1422" height="720" /></a>
Geekbench: a modest improvement

<a href="http://djmadeira.com/wp-content/uploads/2015/01/novabench.jpg"><img src="http://djmadeira.com/wp-content/uploads/2015/01/novabench.jpg" alt="With case: 974, Without case: 974" width="1072" height="482" class="size-full wp-image-559" /></a>
Novabench: no change

<a href="http://djmadeira.com/wp-content/uploads/2015/01/cinebench.jpg"><img src="http://djmadeira.com/wp-content/uploads/2015/01/cinebench.jpg" alt="With case: OpenGL 14.42 fps, CPU 415 cb. Without case: OpenGL 15.58 fps,  CPU 422 cb." width="610" height="163" class="size-full wp-image-560" /></a>
Cinebench: again, modest gains

<a href="http://djmadeira.com/wp-content/uploads/2015/01/valley.jpg"><img src="http://djmadeira.com/wp-content/uploads/2015/01/valley.jpg" alt="With case: fps 7.3, score 304. Without case:  fps 7.5, score 313" width="1122" height="485" class="size-full wp-image-561" /></a>
Unigine Valley: very slight improvement, possibly insignificant.

On some tests, there are observable gains, but on others, it's the same or basically the same. The most pronounced gains seemed to be on tests where the fans kicked on: on the GeekBench test, the fans kicked into mid-range activity (monitored with <a href="http://www.crystalidea.com/macs-fan-control">Macs Fan Control</a>), but without the case they never went above the minimum. On the Novabench, however, the fans didn't kick in on either test, and the results are identical, which conforms with my theory.

On the Unigine Valley benchmark, an additional test showed the same score with and without, but with a slightly <em>lower</em> framerate without the case, so I think that test is a wash. However, the Cinebench test showed significant, if not huge, improvement, so I'm going to say that there is a noticeable difference for graphics, but maybe not big enough to drill holes in your case if that's all you care about.

If the fan noise during CPU activity spikes annoys you, your warranty is expired, and you don't mind holes in the bottom of your case, it might be worth it to try making some holes in the bottom cover around the fans, as pictured in the <a href="http://ifixit.org/blog/6882/why-i-drilled-holes-in-my-macbook-pro-and-put-it-in-the-oven/">iFixit article</a> I mentioned earlier. Even if you don't care about gaming, it seems like the biggest gains to be had are with reduced fan activity &amp; and better performance on CPU operations. Lower operating temps of increased ventilation might also increase the life of both the fans and the CPU.

As for me, I won't mess with it until my warranty expires. I could buy another bottom cover (replacement covers seem to run in the $80-$180 range) and drill holes in the old one, but to me the gains don't outweigh the cost.

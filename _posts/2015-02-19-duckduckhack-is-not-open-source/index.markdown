---
layout: post
status: publish
published: true
title: DuckDuckHack is not open source
author: DJ Madeira
author_login: webmaster
author_email: djmadeira@me.com
wordpress_id: 579
wordpress_url: http://djmadeira.com/?p=579
date: '2015-02-19 05:19:14 -0500'
date_gmt: '2015-02-19 05:19:14 -0500'
tags: []
comments: []
---
<a href="http://duckduckhack.com/">DuckDuckHack</a> is an initiative by "the search engine that doesn't track you", DuckDuckGo, which allows people to write tools that integrate with DuckDuckGo search to make it better. For example, you might integrate with a 3rd party API to display various stats when someone searches for "Tale of Two Cities page count", or make a script that converts measuring units. It's an impressive feat of engineering and an interesting way for them to counter Google's enormous piles of money.

The fact that they use the open source label anywhere near it is outrageous, and the fact that most people seem to be ok with this even more so.

DuckDuckHack calls itself "an open source community changing the future of search". The way it works is, you pull in their development environment (using what is essentially a SaaS version of <a href="https://www.vagrantup.com/">vagrant</a>), fork their sample repo and clone it into the VM, and away you go, writing software for a real search engine! If DuckDuckGo like what you made, they pat you on the head and integrate your hack into their closed-source search engine so "everyone" can "use" it. You just "helped make the internet a better place!"

While this may feel open-source-y, open source is just the delivery mechanism that lets you write features for their SaaS product for the low, low price of a warm fuzzy feeling (apparently some people also got t-shirts). I'm sure that this project was started with good intentions, but its effect on open source in general is incredibly negative for a few reasons. But first, what makes it not open source? What is open source?

There are lots of different licenses that different people variously do or don't count as open source, but most people would probably define it something like this: "software that can be freely used, changed, and shared (in modified or unmodified form) by anyone" (<a href="http://opensource.org/osd">more detail available here</a>.) There is another, separate movement called the "Free/libre" software movement that defines open software by whether it provides the "<a href="https://www.gnu.org/philosophy/free-sw.html">4 essential freedoms</a>" (a definition I tend to prefer):

<ul>
<li>The freedom to run the program as you wish, for any purpose.</li>
<li>The freedom to study how the program works, and change it so it does your computing as you wish. Access to the source code is a precondition for this.</li>
<li>The freedom to redistribute copies so you can help your neighbor.</li>
<li>The freedom to distribute copies of your modified versions to others. By doing this you can give the whole community a chance to benefit from your changes. Access to the source code is a precondition for this.</li><br />
</ul>

So when DuckDuckGo appropriates the open source label for their initiative, it is technically "true". The software that <strong>you</strong> write <em>is</em> open source, but you can't use it like <a href="https://www.gnu.org/philosophy/floss-and-foss.html">FLOSS</a> because you don't have access to the runtime, DuckDuckGo. <em>Maybe</em> you can take the code you wrote and turn it into an standalone tool, but the whole point is to integrate it directly into the search results so no one needs to download or use your tool. Any work you put into DuckDuckHack benefits DuckDuckGo's paid product immensely, and the community at large very little.

Open source is not a LICENSE file in a GitHub repository. Open source is about adding value to the community by making something from which everyone can benefit, repurpose, or hook up to other things to create something new. DuckDuckHack is not an "open source community". It's a crowdsourcing model that uses open source trappings as a delivery mechanism.

Imagine that Google had a similar initiative, and that they didn't use the words "open source" anywhere. Wouldn't it seem really weird, even unethical, for them to just ask people to make their product better for free?

Do I sound cranky? Because I feel cranky. Words matter more than they ever have, and we have to defend them with still more words if we want them to retain their meaning. If DuckDuckHack's model were to become a common practice, we could have a whole generation of software developers whose understanding of open source is "a way for developers to give back to their favorite brands", or something equally disastrous.

If DuckDuckGo wants to crowdsource their development, more power to them. They don't even have to offer t-shirts. But <em>we</em> have to do stupid stuff like write blog posts and tweet really loud until they stop misusing our words. The people who control the meaning of words are the people who use them.

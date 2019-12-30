---
layout: post
status: publish
published: true
title: Why The Picture Element Isn't Ready
author: DJ Madeira
author_login: webmaster
author_email: djmadeira@me.com
wordpress_id: 348
wordpress_url: http://djmadeira.com/?p=348
date: '2014-05-23 02:13:48 -0400'
date_gmt: '2014-05-23 02:13:48 -0400'
tags: 
- archived
comments: []
---
The picture element is obviously the result of a lot of really intelligent minds working really hard to solve a complex problem. If you spend a little time thinking about it, and you're familiar with all the pitfalls that surround this issue, you&nbsp;can see the reasons for many of the&nbsp;decisions that they made. But it's still not what the web needs.

Before I get into all the reasons why picture is bad, let me say this: I don&rsquo;t have a different solution. Responsive images is a hard problem, and I don&rsquo;t know what the right solution is. I&rsquo;m not even saying the picture element is not the right solution for <i>today</i>, but I hope that it doesn&rsquo;t have to be the solution we all live with for the next decade.

<h2>1. The markup is really confusing&hellip; and *unsemantic* (oh no he didn&rsquo;t)</h2>
Before you jump straight to the comments to tell me what an idiot I am, let me say that I&rsquo;ve read the spec. I &ldquo;get&rdquo; how it works. I get the reasons it &ldquo;had&rdquo; to be the way that it is. That doesn&rsquo;t mean it&rsquo;s ok. Look at this:

<pre><code>
&lt;picture&gt;
&lt;source media=&ldquo;(min-width: 320px)&rdquo; sizes=&ldquo;100vw&rdquo; srcset=&ldquo;pic320.jpg 320w, pic640.jpg 640w, pic800.jpg 800w&rdquo;&gt;
&lt;source media=&ldquo;(min-width: 1024px)&rdquo; sizes=&ldquo;980px&rdquo; srcset=&ldquo;pic800.jpg 1x, pic1200.jpg 1.5x, pic1600.jpg 2x&rdquo;&gt;
&lt;source media=&ldquo;(min-width: 1200px)&rdquo; sizes=&ldquo;calc((100vw / 3) - 100px)&rdquo; srcset=&ldquo;pic1200.jpg 1200w, pic1600.jpg 1600w, pic2000.jpg 2000w&rdquo;&gt;
&lt;img src=&ldquo;pic320.jpg&rdquo; alt=&ldquo;Some alt text"&gt;
&lt;/picture&gt;
</code></pre>

That&rsquo;s a real use case. We have media queried sources, because at different device widths, we use the image differently in the layout. We have srcsets for each one, because we don&rsquo;t know exactly how large the screen of viewing is <i>and </i>we don&rsquo;t know the device pixel density (one of the biggest challenges in responsive images). This covers a broad number of devices across 3 layout breakpoints. And it&rsquo;s <i>horrifying</i>.

The real problem with the markup, however, is that we have a layout-relevant element <i>inside </i>what is essentially&nbsp;a meta-data wrapper. The thing you have to understand about the picture element is that

&lt;picture&gt; is not the thing you style in your CSS. The &lt;img&gt; is. The picture element is just a wrapper for the sources. It&rsquo;s basically invisible in the DOM. <b>That&rsquo;s insane</b>.

With the video and audio elements, the metadata goes <i>inside </i>the element that has layout consequences, which makes sense and compartmentalizes the information nicely. Why are we designing the HTML spec around fallback support? The markup is bad, it&rsquo;s confusing, and it doesn&rsquo;t make sense structurally.

<h2>2. Presentation details in the markup</h2>
HTML is for creating structure and meaning in the content of pages. CSS is for controlling the presentation of that content across the many devices on which it&rsquo;s displayed. So why are we creating, supporting, and polyfilling an element that makes it <em>impossible</em> to divorce the HTML content from it&rsquo;s CSS presentation?

I know why the sizes and media queries have to be in the HTML. I know that the whole point of the picture spec is to increase speed&nbsp;and decrease latency, and needing to wait for the CSS to parse defeats the whole purpose. The point of this spec is to give parsers the info they need to download only the smallest image that works, and maybe to load different images on the fly when layout changes. But there has to be a way to do that without violating one of the core tenants of CSS: <a href="http://en.wikipedia.org/wiki/Separation_of_concerns" target="_blank">separation of concerns</a>.

We <i>have </i>to be able to do better than this. Imagine WordPress implementing the picture element natively? What a UI and code logic&nbsp;<i>nightmare</i>.

<h2>3. This isn&rsquo;t going to be supported for <i>years</i>&hellip; so why are we settling?</h2>
This isn&rsquo;t going to actually have broad enough support to use in production for a really, really long time (yea, except for picturefill). And when it is, we&rsquo;ll be stuck with supporting it <i>forever</i>. Think about that. Is this how to want to add images to your web pages for the next decade? What happens when it&rsquo;s time for a redesign? I hope you use a CMS that implemented picture smartly, or your life is going to be a manual find and replace <em>hell</em>.

To reiterate, I'm&nbsp;not opposed to the <em>values</em> of the picture element. Performance. Network conservation. Returning control to the user. Thinking about responsive web design as more than just linearly scaling up the UX quality with device width. But this implementation is not good enough. We have to dig deeper. We have to try something else.

<h2>How do we solve it?</h2>
Like I said before, I don&rsquo;t know what the solution is. There are a lot of people thinking about this that are much smarter than me. That&rsquo;s part of the reason I&rsquo;m so bummed that this solution isn&rsquo;t what we need it to be. I know we, as web developers, designers, and spec authors, can do better. We have to.

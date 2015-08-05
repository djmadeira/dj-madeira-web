---
layout: post
status: publish
published: true
title: Naming Parameters
author: DJ Madeira
author_login: webmaster
author_email: djmadeira@me.com
wordpress_id: 464
wordpress_url: http://djmadeira.com/?p=464
date: '2014-08-23 23:39:42 -0400'
date_gmt: '2014-08-23 23:39:42 -0400'
tags: []
comments: []
---
I've been working on a project at work for the last couple of weeks, and I just got done writing the preliminary documentation for the various front-end views. As a result, I've been spending a lot of time thinking about how to name parameters in an API.

When I talk about an API, what I mean is something like this (PHP in this case):

<pre><code>get_block('BlockName',
  array(
    'display_thumbnail' => true,
    'thumbnail_size' => 50,
    'read_more_link_url' => 'http://example.com'
));
</code></pre>

Something where you call a function and pass it an associative array (or object in JS) of key-values that change how the function/API/module behaves.

<h2>What makes a good API naming scheme?</h2>
I'm sure there are many different opinions on this, but this is what I value in an API:

<ul>
<li>Memorable</li>
<li>Consistent</li>
<li>Predictable</li>
<li>Name is hint about expected value</li>
</ul>

Memorable seems obvious, but I'm not talking about making the names for things unusual just to stick out. I feel like names for parameters should be the first thing you think they would be, so when I haven't touched the API for 2 months and come back to it, I remember "oh yea, the API is this because I remember it being what I expected it to be". Any time I can avoid needing to check the docs for how something is worded is a productivity win.

Consistency is an easy one to take for granted, but actually requires a lot of planning and intentionality to do right. For example, if you decide you want to use the word "thumbnail" to mean an image associated with a blog post, but later find a need for literal thumbnails, now you have one word that has two meanings (WordPress is guilty of this).

To make an API predictable to the people who are going to use it, I also feel like it's helpful to look at other, similar projects when coming up with a naming convention. If you're in an ecosystem like Grunt or WordPress, then one of the easiest ways to meet people's expectations about what things are called is to mimic the APIs they're already used to using.

There's tons of room for disagreement on all of this because naming things is so nuanced and because people remember things so differently. For example, I said earlier that using weird, unintuitive words is a bad idea; but some people find that weird things are actually the best way for them to remember something. YMMV.

<h2>Examples</h2>
WordPress, in general, does a good job naming parameters. They tend to stick to the "if you were saying it out loud" way of naming things. For example:

<pre><code>$args = array(
  'name'          => __( 'Sidebar name', 'theme_text_domain' ),
  'id'            => 'unique-sidebar-id',
  'description'   => '',
  'class'         => '',
  'before_widget' => '
<li id="%1$s" class="widget %2$s">',
  'after_widget'  => '</li>',
  'before_title'  => '
<h2 class="widgettitle">',
  'after_title'   => '</h2>' );
</code></pre>

In WordPress land, multi-word parameters are written the way they would be said, e.g. "The text before the title is &lt;h2 class="widgettitle"&gt;"

I think this makes a lot of sense, and partially because I'm <em>in</em> WordPress land and partly because I just like it, this is the pattern I've adopted.
Ember.js also adopts this pattern, but they take it one step further: the name of the parameter gives clues about the expected input. For example:

<pre><code>MyView = Ember.View.extend({
  classNames: ['my-class', 'my-other-class']
});
</code></pre>

Here, "classNames" gives you a hint that it should (or just can) be an array. It seems obvious once you point it out, but it has a big impact.

Another patter in the vein of "clues to expected value" is to phrase boolean values as a question (usually by prefixing the name with "is". In WordPress, the template tags follow this fashion: <code>is_home()</code>, <code>is_front_page()</code>, etc.

I like this pattern, and I think it makes sense to imagine all our parameters as a question, since that's basically what we're doing. To use the earlier example, we'd imagine 'before_title' phrased as "What should be displayed before the title?". Even though it doesn't necessarily change how we phrase the parameters, it feels right.

<h2>Conclusion</h2>
My favorite APIs are the ones that don't overthink it; unlike creative fields, where you should think outside the box and throw out your early ideas, when it comes to making good APIs, I find it's best to create guides for how you name things, and then use the first thing you think of that fits in your guidelines.

Maybe you have some examples of great APIs? Maybe they subvert or contradict some of the things I've said? I'd love to hear about it! Leave me a comment below.

---
layout: post
status: publish
published: true
title: How To Make Sure Your Child Theme's CSS Always Loads Last
author: DJ Madeira
author_login: webmaster
author_email: djmadeira@me.com
wordpress_id: 333
wordpress_url: http://blog.sitesahoy.com/?p=333
date: '2014-01-29 15:02:55 -0500'
date_gmt: '2014-01-29 15:02:55 -0500'
categories:
- The Web
tags:
- programming
- php
- wordpress
- theme development
comments: []
---
If you do CSS customizations in Wordpress, you know what an enormous pain it is when other plugins/themes use !important declarations. It's <em>my</em> job to write hacky, incorrectly cascading code, not <em>theirs</em>, you say. So you dutifully put an !important declaration on the relevant code, hit refresh... and it <em>still</em> didn't work! Because the offending CSS is loading <em>after</em> the child theme CSS. No matter how many times you yell at your monitor, you're never going to be able to override that CSS. Unless you go into full <a href="http://www.stuffandnonsense.co.uk/archives/css_specificity_wars.html" target="_blank">specificity wars mode</a>.

Unfortunately, as far as I know, there's no right way to fix this. You are supposed to use a functions.php file in your child theme to call your stylesheet using <em><a href="http://codex.wordpress.org/Function_Reference/wp_enqueue_style" target="_blank">wp_enqeue_style()</a></em>, and list the file you want it to come after as a dependency. But this won't work if the offending styles didn't also use <em>enqeue_style</em>, because you have to know that style's ID.

If you want to beat bad code, you have to become the bad code.

I'm going to assume you're using a child theme for this tutorial. If you're not using a child theme, and you have FTP access to the site you're working on, it takes <a href="http://www.elegantthemes.com/blog/resources/wordpress-child-theme-tutorial" target="_blank">5 minutes to set up</a> and it's much better and easier than editing theme files directly. &nbsp;If you're making a theme or plugin, you probably already know where to put this code to make it work.

The first thing you have to do is make a functions.php file. Just right click in your child theme's root folder in your FTP client (/wp-content/themes/your-child-theme/) and make a new file called functions.php. If your FTP client won't let you make new files, just make a blank file with a text editor, save it with the name functions.php and upload it. You'll also need to make a CSS file other than style.css in your child theme's root folder that you can link to in your functions.php file (you could call it main.css).

Now, open up that functions.php file you uploaded and put this code in there:

<pre><code>if (!function_exists('childtheme_css')) { function childtheme_css () {
echo '
&lt;link rel="stylesheet" type="text/css" href="' . get_stylesheet_directory_uri() . '/&lt;strong&gt;your-css.css"&lt;/strong&gt;&gt;';
} }
add_action('wp_head', 'childtheme_css', 9999); }</code></pre>

This is a function that echoes (prints) a link tag that links to your stylesheet (make sure to change <strong>your-css.css</strong> to a real file). The <em>add_action()</em> function is a function that tells Wordpress to call (do) your function when the action <em>wp_head()</em> is called. The 9999 tells wordpress what order you want your function to be called inside&nbsp;<em>wp_head</em>. If other CSS is still loading after yours, try adding some more 9s!
If you asked a member of the Wordpress theme about this, they would tell you that this is the wrong way to do this. But sometimes, when other developers play dirty, you have to stoop to their level to do your job.

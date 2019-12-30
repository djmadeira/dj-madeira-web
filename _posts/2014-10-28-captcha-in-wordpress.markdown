---
layout: post
status: publish
published: true
title: Captcha in WordPress
author: DJ Madeira
author_login: webmaster
author_email: djmadeira@me.com
wordpress_id: 509
wordpress_url: http://djmadeira.com/?p=509
date: '2014-10-28 04:26:19 -0400'
date_gmt: '2014-10-28 04:26:19 -0400'
categories:
- The Web
tags: 
- archived
comments: []
---
**Update: This worked temporarily, but as time passed and traffic grew, it was just not enough. I was inunduated with spam, and forced to move to a static site generator to get away from it. If you really want to protect a WordPress blog from spam, a) don't use WordPress, or b) write your own AJAX-using plugin that nonces to prevent spammers from submitting directly to wp-post-comment.php**

Captchas are awful. But you know what's worse than captchas? Comment spam. So it's probably a necessary evil, but what I want is to make it as not-evil as possible. Here's how I approached it.

<h2>Planning</h2>
The major schools of thought in captcha today are:

<ul>
<li>Type the jumbled letters</li>
<li>Perform a task (such as picking the red objects, assembling the puzzle, doing simple math, etc.)</li>
<li><strong>Don't</strong> fill out the field (add a hidden field that only robots will fill out; aka a honeypot)</li>
</ul>

Jumbled letters are out. Besides being a user experience nightmare, relying on a third-party for a key part of my site isn't a great way to make sure it works a long time in the future (although there is <a href="https://wordpress.org/plugins/peters-custom-anti-spam-image/">a plugin</a> that works without a 3rd party).

I like the honeypot technique a lot, because it has zero costs for users; however, it's easy to do wrong. In my experience, many spam bots don't bother with anything that has <code>display:none;</code>, which would be the accessible way to hide the field (meaning that screen readers won't read it either). <code><input type="hidden"></code> is a dead giveaway as well. So it needs to be hidden in a way that will fool the bots, not display to sighted users, and not confuse the sight impaired. A clear explanation that the field should not be filled out is a must.

In addition to those caveats, in my experience the honeypot doesn't stop enough spam. It'll stop most of your typical WP spam bots, but not enough that I don't have to think about it.

So that leaves performing a task. While this does cost your users something, if you do it right it can be very low-impact. I chose simple math problems. I decided to limit the operands to adding, subtracting and multiplying, and to limit the number range of numbers to 5. I also decided to allow solutions that are negative numbers (e.g. 1 - 5 = -4.)

<h2>Implementation</h2>
My first attempt was with PHP:

<pre><code class="php">add_filter( 'comment_form_default_fields', 'dj_add_comment_captcha' );
function dj_add_comment_captcha( $fields ) {
  $operation = rand(1,3);
  $num_one = rand(1, 5);
  $num_two = rand(1, 5);
  switch ($operation) {
    case 1:
      $op_text = 'Multiply';
      $preposition = 'by';
      $answer = $num_one * $num_two;
      break;
    case 2:
      $num_one = rand(1, $num_two);
      $op_text = 'Take';
      $preposition = 'away from';
      $answer = $num_two - $num_one;
      break;
    default:
      $op_text = 'Add';
      $preposition = 'and';
      $answer = $num_one + $num_two;
  }
  $output = '
&lt;p class="comment-form-captcha"&gt;&lt;label for="captcha"&gt;' . $op_text . ' ' . $num_one . ' ' . $preposition . ' ' . $num_two . ' (to prove you're not a spam robot).&lt;span class="required"&gt;*&lt;/span&gt;&lt;/label&gt;' . '&lt;input id="captcha" name="captcha" type="text" size="2" aria-required="true"&gt;&lt;/p&gt;&lt;input name="captcha-answer" type="hidden" value="' . $answer . '"&gt;';
  $fields['captcha'] = $output;
  return $fields;
}
</code></pre>

<code>comment_form_default_fields</code> is an undocumented filter in WP that lets you modify the array of fields output in the comments template, which is handy if you don't want to modify any templates to make this work.

Another important thing to note is that we're giving the user the lock and the key; the correct value is actually passed right along in <code><input name="captcha-answer"</code>. The reason I did this is basically security through obscurity; if someone wanted to write a custom exploit for my website, they could do that pretty easily even if I did some tricky <a href="http://codex.wordpress.org/WordPress_Nonces">nonce-ing</a>. So the only thing I'm worried about stopping are the off-the-shelf bots that just attack every public WordPress site they can find.

So that's the fields that are output to the front-end; now it needs to be validated when the comment is submitted:

<pre><code class="php">function dj_validate_comment_captcha ($ID) {
  $answer = $_POST['captcha-answer'];
  $user_answer = $_POST['captcha'];
  if ($answer != $user_answer) {
    wp_die('Captcha answer was incorrect');
  }
}
add_action( 'pre_comment_on_post', 'dj_validate_comment_captcha' );
</code></pre>

Ok, that all works fine, but I'm on cheap hosting, and I want to be caching my site in case my post I get tweeted by Chris Coyer. The only option available to me on said cheap host is file-base caching, which basically makes this method useless (it may not be worth the trouble to write a custom exploit, but if the field value is the same every time, it seems like asking for trouble).

My next thought was something AJAX-based. I don't like walling things off to only JavaScript users if I don't have to, but in this case, it makes sense because the JS requirement alone will surely filter out some bots, and it's not essential to using the site on a basic level.

After figuring out WordPress AJAX, I realized that since I was giving the user both the lock and the key, I could do it all client side anyway:
<pre><code class="javascript">addEventListener('DOMContentLoaded', function () {
  var operator = Math.floor(Math.random() * 3);
  var num1 = Math.floor(Math.random() * 5) + 1;
  var num2 = Math.floor(Math.random() * 5) + 1;
  var questionTextEl = document.getElementById('captcha-label-text');
  var captchaAnswer = document.getElementById('captcha-answer');
  switch(operator) {
    case 0:
      var message = "Add " + num1 + " and " + num2 + ".";
      captchaAnswer.value = num1 + num2;
      break;
    case 1:
      var message = "Subtract " + num2 + " from " + num1 + ".";
      captchaAnswer.value = num1 - num2;
      break;
    case 2:
      var message = "Multiply " + num1 + " by " + num2 + ".";
      captchaAnswer.value = num1 * num2;
      break;
  }
  questionTextEl.innerHTML = message;
});
</code></pre>

I'm still doing the validation server-side, because that would mean anyone with JS disabled would automatically get through. Now, I can have my site cached even using only static files and still get comment filtering.

This is the simplest, dumbest way I could come up with to keep 99% of comment spam out, and it seems to be working Ok so far. If the spam starts seeping back in, I'll probably move to an AJAX, nonced something something, but the important part to remember in the <a href="http://arstechnica.com/security/2008/04/gone-in-60-seconds-spambot-cracks-livehotmail-captcha/">spam detection arms race</a> is that no matter how elaborate the captcha mechanism, whether it's a turing test or a honeypot or some combination of both, can be broken by just <a href="http://web.archive.org/web/20140625092453/http://www.protypers.com/">paying a person pennies an hour</a> to fill them out by hand. Like DRM, anything more than extremely simple hurts your legitimate users more than the people you're trying to stop.

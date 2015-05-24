---
layout: post
title: "[Repost] 2015 02 10 VIM  moving around for jumping in and jumping out."
description: ""
category:
tags: Vim
---
{% include JB/setup %}

<p>
  As a developer, I was not very intersting at using vim as primary editor, cause I had been happy using IDEs such as Eclipse or Visual Studio.
</p>

<p>
  At the begining days of learning ruby, I felt quite painful about using pure-text editor, such as Sublime, Atom and Notepad, I have to type every English words by hand. It costs time to adpte as a pure-text developer.
</p>

<p>
  As time went on, it seems that I have been used to use pure-text editor. Instead of using IDEs, those pure-text editors are faster, more powerful and more concentrated on the structure of code.
</p>

<p>
  I couldn't clearly remeber when I started using vim, maybe after I had decided switch myself as a ruby/rails developer from Java.
  The first time I learned to use Linux officially was I attended the transition courses in Clement-Ferrand, France.
  The course called "Système d'exploitation"(Operating system in English), the tutor, or called him the master told us that we should master Linux, cause It's powerful, efficient and low-cost.
  He is a big fan of Apple's Mac, and he is as funny as what he looks like. During the course of OS, he showed us the first Apple Mac machine, the moment of boosting up the Mac, I was shocked. It was amazing, I have never thought of seeing such magic event in my life.
</p>
<p>
  That's maybe why I am using Mac now.
</p>

<p>
  Ok, end of personal story. Tutorial time.
</p>
<p>
  In command mode, means you won't see INSERT at the bottom of vim screen.
</p>
<pre>
  <code>
    1) H J K L
  </code>
</pre>
<p>
  H: move left<br>
  J: move down<br>
  K: move up<br>
  L: move right<br>
</p>
<pre>
  <code>
    2) w, W
  </code>
</pre>
<p>
  Lower case w: single word jump in a line, move forward<br>
  Upper case W: use shift-w means ignore "-", such as "hello-world" will be considered as one single word.<br>
</p>
<pre>
  <code>
    3) b, B
  </code>
</pre>
<p>
  Lower case b: single word jump in a line, move backward<br>
  Upper case B: use shift-w means ignore "-", such as "hello-world" will be considered as one single word.<br>
</p>
<pre>
  <code>
    4) $
  </code>
</pre>
<p>
  $: Move to the end of paragraph.<br>
</p>
<pre>
  <code>
    5) ^, 0
  </code>
</pre>
<p>
  ^: move to the begin of paragraph,<br>
  0: move to the very beging of paragraph.<br>
</p>
<pre>
  <code>
    6) gg, G
  </code>
</pre>
<p>
  gg: jump to the head line<br>
  G:  jump to the end line<br>
</p>
<pre>
  <code>
    7) {, }
  </code>
</pre>
<p>
  {, }: move between paragraph, {: backward, }: forward<br>
</p>
<pre>
  <code>
    8) f + 'word', F + 'word', number + f + 'word', e.g. 3 + f + 'word'
  </code>
</pre>
<p>
  f + 'word': find the next occurence of specific word, forward<br>
  F + 'word': find the next occurence of specific word, backward<br>
  number + f + 'word': find the number-th occurence of specific word, forward<br>
</p>
<pre>
  <code>
    9) t + ',', T + ','
  </code>
</pre>
<p>
  t + ',': find the left side word of ',', forward<br>
  T + ',': find the right side word of ',', backward<br>
</p>
<pre>
  <code>
    10) 3{, 3}, applicable for all numbers
  </code>
</pre>
<p>
  3{: three line move forward<br>
  3}: three line move backward<br>
</p>
<pre>
  <code>
    11) 3gg, 3G
  </code>
</pre>
<p>
  3gg, 3G: move to the 3th line of current page<br>
</p>
<pre>
  <code>
    12) :8, , applicable for all numbers
  </code>
</pre>
<p>
  :8: Goto 8th line<br>
</p>

<h3>Thanks to José Mota, a nice tutor at tutsplus.</h3>

<p>
  Happy coding with vim.
</p>
<hr/>
<p>
  Note: Sorry about my English, that why I want to write such article by sharing information. If you readers have intention to help me or to exchange your thoughts with me, please send me an email hongbo.zhen[at]gmail.com without hesitating. thanks.
</p>

---
layout: post
title: "[Repost] 2015 01 20 2015 01 20 Pry   A powerful interactive Ruby console"
description: ""
category:
tags: pry
---
{% include JB/setup %}

<p>
  Today, I have spent a few hours for learing a new development console for ruby -- <a href="https://github.com/pry/pry"><b>Pry</b></a>.
</p>

<p>
  I firstly knew about <b>Pry</b> was reading one artcile <a href="http://www.sandimetz.com/blog/2014/12/19/suspicions-of-nil">Suspicions of nil</a>, from Sandi Metz's blog. She is the author of <a href="http://www.amazon.com/Practical-Object-Oriented-Design-Ruby-Addison-Wesley/dp/0321721330/ref=tmm_pap_title_0">Practical Object Oriented Design in Ruby</a>.
</p>

<p>
  I had no intention to learn the new console, until I noticed that the output of entering <i>"NilClass.instance_methods"</i> is all instance methods of NilClass in proper order, unlike <a href="http://en.wikipedia.org/wiki/Interactive_Ruby_Shell">irb</a>, another ruby interactive console, retrun with unsorted methods names (I do not mean irb it bad or unuseful, but I perfer one sequential methods list personnally as a beginner Rubyist).
</p>

<p>
  For having fun with Pry, type
  <pre>
    <code>$ install gem pry</code>
  </pre>
  in your console, then follow the official document on Github <a href="https://github.com/pry/pry/wiki">here</a>.
</p>

<p>
  Spend two hours for learing this tool, could save your time more than 2 hours in the future.
</p>

<p>
  Happy coding with pry.
</p>

<hr/>
<p>
  Note: Sorry about my English, that why I want to write such article by sharing information. If you readers have intention to help me or to exchange your thoughts with me, please send me an email hongbo.zhen[at]gmail.com without hesitating. thanks.
</p>

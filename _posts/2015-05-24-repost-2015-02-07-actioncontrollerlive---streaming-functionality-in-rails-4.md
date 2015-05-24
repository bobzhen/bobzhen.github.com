---
layout: post
title: "[Repost] 2015 02 07 ActionController::Live   Streaming Functionality in Rails 4"
description: ""
category:
tags: []
---
{% include JB/setup %}

<p>
  I am following one tutorial on <a href="http://www.sitepoint.com/mini-chat-rails-server-sent-events/">Sitepoint</a>, which talks about "Server-Sent-Events" short for SSE being introduced in rails 4.
</p>
<p>
  It's a long tutorial, and be curious for new features which you are new to them.
</p>
<p>
  Also, remeber, the source code is your best friend, it's always more readable and more understandable than rails doc.
</p>
<p>
  So, as a rails newbie, I had no idea about a Live mixin in rails 4. I searched for LIVE keyword in rails' source code, then I see this:
</p>
<pre>
  <code>
    class CommentsController < ActionController::Base
      include ActionController::Live

      def index
        response.headers['Content-Type'] = 'text/event-stream'
        100.times {
          response.stream.write "hello world\n"
          sleep 1
        }
      ensure
        response.stream.close
      end
    end
  </code>
</pre>

<p>
  I guessed that the result for requesting localhost:3000/comments/index should be one 100 lines of "hello word"s in a plain-text web page (not strictly plain-text, it's <i>text/event-stream</i>).
</p>
<p>
  It's your turn to have fun with SSE using Live module in rails 4.
</p>
<p>
  Happy coding.
</p>
<hr/>
<p>
  Note: Sorry about my English, that why I want to write such article by sharing information. If you readers have intention to help me or to exchange your thoughts with me, please send me an email hongbo.zhen[at]gmail.com without hesitating. thanks.
</p>

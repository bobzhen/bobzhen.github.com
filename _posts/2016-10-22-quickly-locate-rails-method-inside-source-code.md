---
layout: post
title: "How to locate Rails method that is exact what you need?"
description: "How to locate Rails method that is exact what you need?"
category:
tags: rails source-code method
---
{% include JB/setup %}

## How to locate Rails method that is exact what you need?

### Intro
Sometimes, we need to read source code, for the purpose to understand the `truth`.

For rails, it isn’t easy to locate your wanted method. For example, you want to know what’s behind `render` method for controller action, so you open [Rails API Doc](http://api.rubyonrails.org/), type `render` into left-top form, boom~ You get results, but the first isn’t render method, API website is smart enough to pattern matching what it thinks best sort. Then you add brackets, `render()`, voila, you get 9 `render` method definitions. WTF.

### How to do

I have learnt one smart way to locate method definition.

One rails question gets my attention today: `How is something like 30.seconds.ago implemented?` [Original]() [Stackoverflow Answer](http://stackoverflow.com/questions/33638803/how-is-something-like-30-seconds-ago-implemented)

When I first time see this question, I type `ago` on api doc. As you can see, I get five `ago` methods. I was confusing, which one should I read? Should I read them all? (What an idiot!) Luckily, the first match is what I need for, `ActiveSupport::Duration#ago`.
```ruby
# File activesupport/lib/active_support/duration.rb, line 115

def ago(time = ::Time.current)
  sum(-1, time)
end
```

Next, I search for the definition of `seconds`. This time only one match, great!
```ruby
# File activesupport/lib/active_support/core_ext/numeric/time.rb, line 21

def seconds
  ActiveSupport::Duration.new(self, [[:seconds, self]])
end
```

Ok, at this time, I know better about `30.seconds.ago`: `30.seconds` was converted to be an `ActiveSupport::Duration` instance, then call `ago` methods, whom calls protected [`sum`](https://github.com/rails/rails/blob/434df0016e228a7d51f1ad0c3d1f89faeffbed9a/activesupport/lib/active_support/duration.rb#L157) method implicitly.

I got the answer at the end, but is a waste of time. Thanks to the trick, at the end of the answer [here](http://stackoverflow.com/questions/33638803/how-is-something-like-30-seconds-ago-implemented?answertab=votes#tab-top):

```shell
pry(main)> 30.method(:seconds).source_location
=> ["/Users/bob/.rvm/gems/ruby-2.2.2/gems/activesupport-4.2.0/lib/active_support/core_ext/numeric/time.rb", 34]
pry(main)> 30.seconds.method(:ago).source_location
=> ["/Users/bob/.rvm/gems/ruby-2.2.2/gems/activesupport-4.2.0/lib/active_support/duration.rb", 84]
```

Pretty simple, hmmm.

### Conclusion
Thanks to great DHH and rails core team, for creating rails. It would be better that we know more tricks or techniquesto read source code, for our own sake.

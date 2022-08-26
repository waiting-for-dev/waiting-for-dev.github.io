---
layout: post
title: "Ruby gems could harm your memory"
date: 2016-09-07 14:10:25 +0000
comments: true
categories: [ruby, rails]
published: false
---
It is easy to get amazed with the wonderful world that the open source community has created around ruby gems. It seems to exist a library for everything you can imagine. Some people can even get the illusion that software development is as easy as putting together the pieces of a puzzle: just take rails, put this gem here and that one there and you are done.

It is true that it is amazing. So much work done thanks to the enthusiasm and generosity of so many people. But it is not less true that adding gems to your application is not free at all. The most important thing to consider is that you are depending on code neither you nor your team wrote, and this can get you in some troubles when it changes or when it has to cooperate with the rest of your application universe.

But in this post I want to emphasize another side effect of adding external dependencies: memory consumption. When you add that apparent innocent line to your Gemfile, you are fetching a bunch of code that may or may not be written carefully, and may or may not do much more than you need. On the other side, your server resources are not unlimited and you don't want memory exceeded errors to appear in your logs.

Following are some tips than can help to minimize your application memory consumption related to your dependencies.

* Only install the gems you really need. If there is a gem that does some fancy thing that is very cool for your code but it has not actual value, probably you are better off not adding it. (**UPDATE 2016-09-11:** As suggested by Sergey Alekseev in a comment, I point here to a [list of gems with known memory issues](https://github.com/ASoftCo/leaky-gems).) 

* Add the gems you need in their appropriate group. For example, if you use [rubocop](https://github.com/bbatsov/rubocop) as code analysis tool, you only need it in development environment:

```ruby
# Gemfile

# Bad. You will add this gem to the production environment even if you are not using it there
gem 'rubocop'

# Good
gem 'rubocop', group: :development
```

* If you add some gem which is only used to run some rake tasks do not require it on load time. You can require it in the `Rakefile`. E.g.:

```ruby
# Gemfile

gem 'seed-fu', require: false

# Rakefile

require 'seed-fu'
```

* If a gem scope is very well limited, you can also require it only when it is needed. For example, [prawn](https://github.com/randym/axlsx) is used to generate PDFs. If you have a class `ApplicationPdf` which is the base class for any other pdf, you could do something like:

```ruby
# Gemfile

gem 'prawn', require: false

# application_pdf.rb

require 'prawn'

class ApplicationPdf
  # ...
end
```

* Try to keep your gems updated. Hopefully their maintainers will keep improving them also in this aspect.

* From time to time, monitor your gems memory consumption. For rails projects you can use [derailed_benchmarks](https://github.com/schneems/derailed_benchmarks).

Of course, doing the opposite would be even crazier. Don't reinvent the wheel in every application and serve yourself of good and battle-tested gems out there. For example, it would be a tremendous mistake to implement a custom authentication system having [devise](https://github.com/plataformatec/devise) at your disposition. Just be judicious. In the end, software development is much funnier because your judgement is your best asset, and not being good putting together the pieces of a puzzle. 

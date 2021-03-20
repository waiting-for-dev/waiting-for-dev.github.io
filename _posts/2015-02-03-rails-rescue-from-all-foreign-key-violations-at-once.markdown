---
layout: post
title: "Rails: Rescue from all foreign key violations at once"
date: 2015-02-03 13:25:55 +0100
comments: true
published: false
categories: [ruby, rails]
---
**UPDATE:** With the time I see that it is clearly an anti-pattern: it may seem a good idea but it treats an exception that can arise for different reasons as it was always for one of them. So, better avoid this and control each case in your application logic. 

With Rails 4.2 already out of the box, [foreign key definition support](http://edgeguides.rubyonrails.org/4_2_release_notes.html#foreign-key-support) comes bundled in its core. Since 4.1, the best option to manage these important database constraints was to use [foreigner](https://github.com/matthuhiggins/foreigner) great gem.

Anyway, if you are using foreign keys in your Rails application, it's easy to come across the situation where your application crashes due to an attempt to destroy a resource which id is a foreign key of another one. Trying to handle these situations on a case-by-case basis is quite painful, but with a very simple trick we can rest assured that it won't happen. (**UPDATE:** Please, read comments conversation with Robert Fletcher. He pointed out some important concerns you have to keep in mind before adapting this mostly pragmatic solution.)

When `ActiveRecord` encounters a foreign key violation, it raises an [`ActiveRecord::InvalidForeignKey`](http://api.rubyonrails.org/) exception. Even if in its documentation it just says that it is raised *when a record cannot be inserted or updated because it references a non-existent record*, the fact is that it is also used in the case we are interested.

With that and an [around filter](http://guides.rubyonrails.org/v4.2.0/action_controller_overview.html#after-filters-and-around-filters), considering we are only deleting in `destroy` actions, we can just add to `ApplicationController` or to a controller concern:

```ruby
around_action :rescue_from_fk_contraint, only: [:destroy]

def rescue_from_fk_contraint
  begin
    yield
  rescue ActiveRecord::InvalidForeignKey
    # Flash and render, render API json error... whatever
  end
end
```

In case we need more fine grained control, we could also rescue in the model layer through a reusable [concern](http://api.rubyonrails.org/classes/ActiveSupport/Concern.html), adding a meaningful error in each situation and letting to the controller the task to render the error, but in many cases (surely mainly REST API's) this simple trick should be enough.

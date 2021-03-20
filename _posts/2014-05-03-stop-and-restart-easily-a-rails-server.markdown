---
layout: post
title: "Stop and restart easily a rails server"
date: 2014-05-03 11:09:24 +0200
comments: true
published: false
categories: [ruby, rails]
---
I like to start rails server as a daemon with `rails server -d`, because I don't want a terminal window or a tmux pane just hanging around with the server foreground process.

But each time a new gem is added to the application or some changes are made in some configuration file, it is a pain to manually look for the pid in `tmp/pids/server.pid`, kill it and start again the server.

I wished if I could have convenient `rails start`, `rails stop` and `rails restart` commands to help me in the workflow. And it is very easy to get.

Bellow you'll find a simple `bash` (or `zsh`) function that should be added to your `.bashrc` (or `.zshrc`). Once done, you'll be able to do things like the following:

```bash
rails start # To start the server in development environment
rails start production # To start the server in production environment
rails stop # To stop the server
rails stop -9 # To stop the server sending -9 kill signal
rails restart # To restart the server in development environment
rails restart production # To restart the server in production environment
rails whatever # Will send the call to original rails command
```

Here it is the function:

{% gist 11495350 rails_server_helper.bash %}

You see, the code has not too much mystery. Inside the `else` block, `command` is a bash built-in command which executes the given command (in this case `rails`), ignoring any shell function named as it (our function). So, we use it to default to original rails executable when the first argument is not `start`, `stop` or `restart`, passing any other argument with `#@`.

If you don't feel right overwriting `rails`, just change the function name and remove the `else` block.

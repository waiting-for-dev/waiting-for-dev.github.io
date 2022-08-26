---
layout: post
title: "Distributable and organized dotfiles with homeshick and mr"
date: 2014-05-04 09:45:19 +0200
comments: true
categories: [unix, git]
published: false
---
[dotfiles](http://dotfiles.github.io/) are the personality of your computer. Without them, it would be like any other computer in the world. That's why it is a good idea to keep them in a distributable way, so that you can import them easily when you migrate to another system.

But dotfiles have a well-know problem when it comes to make them distributable: they are all scattered in your home directory, so it makes difficult to create repositories from them.

As far as I know, there are two main tools that help us with it:

* [vcsh](https://github.com/RichiH/vcsh) allows you to mantain several git repositories in one single directory (`home`, when talking about dotfiles). To achieve it, it uses a little amount of magic through git internals.

* [homeshick](https://github.com/andsens/homeshick) creates regular git repositories with just one special condition: they have in their root a directory named `home`, inside of which you'll put your dotfiles. Later, you can tell homeshick to symlink the contents of that directory into your actual `home` directory. Of course, applications using your dotfiles won't notice the difference. (Note: homeshick is based in [homesick](https://github.com/technicalpickles/homesick), but I prefer the former because it is written in bash, while homesick is a ruby gem, so you would need more dependencies when importing your dotfiles to a pristine system.)

It is just a matter of taste which one to choose. Personally, I prefer *homeshick* because its condition about the `home` directory. That way I can put outside of it other files that I want to keep close of my dotfiles but not mixed in my actual `home` directory (remember only the contents of that directory would be symlinked), like some README or scripts to install the application that will be using that dotfiles.

Installing homeshick is very easy and you can follow its [homepage instructions](https://github.com/andsens/homeshick/wiki/Installation). Repositories created with it are called castles (just a name), and working with them is also very easy. Here it is what you could do to create your vim castle:

```bash
homeshick generate vim-castle # Create a repository called vim-castle with an empty home directory inside
homeshick track vim-castle ~/.vimrc # Add .vimrc inside the home directory of your vim castle. Automatically ~/.vimrc is now a symlink
homeshick cd vim-castle # Enter castle
git commit -am 'Initial configuration' # Commit your changes
git remote add origin git@github.com:username/vim-castle.git # Add a remote repository
git push # Push your changes
```

Now we are able to create repositories from our dotfiles, keep track of our configuration changes and push them to a save place from where we will be able to pull when we need it. How would we recover them in another machine? Easy:

```bash
homeshick clone git@github.com:username/castle-vim.git # When starting from scratch
homeshick pull vim-castle # To update it with the remote changes
```

So far so good. But we use vim, tmux, tmuxinator, zsh, newsbeuter, mutt... a lot of dotfiles, a lot of castles, a little mess... Why don't we create a one single castle with all of our dotfiles? For some people it can be a reasonable option, but, in general, having them organized has some advantages:

* You can keep different configurations for the same application. A ssh at home and another at work.

* You can keep public the dotfiles you would like to share with the community (vim) and private the ones you don't want to (mutt).

* You can pick which castles you would like to recover. Maybe you don't want newsbeuter at work.

Here it is when the other star comes in. It is [myrepos](https://github.com/joeyh/myrepos), a tool that allows you to work with a lot of repositories at once. With it, you can push, pull, commit and other operations at the same time in a set of registered repositories.

Installing it is again very easy. It has a self-contained `mr` executable which only dependency is perl. You can have more details in its homepage. Once done, you can run `mr help` to know about the bunch of magic you can do with it.

Let's see a possible workflow for our dotfiles. Imagine we have just two castles, `vim-castle` and `tmux-castle`. First, `mr` needs that repositories to exist in your filesystem and to already have a remote registered.

```bash
homeshick cd vim-castle # Enter your vim castle
mr register # Register it to mr
homeshick cd tmux-castle # Enter your tmux castle
mr register # Register it to mr
```

Once done the above, you should have a `~/.mrconfig` file with something like the following:

    [.homesick/repos/vim-castle]
    checkout = git clone 'git@github.com:username/vim-castle.git' 'vim-castle'
    
    [.homesick/repos/tmux-castle]
    checkout = git clone 'git@github.com:username/tmux-castle.git' 'tmux-castle'

Between the square brackets `[]` there are the local filesystem locations for the repositories (relative to `home`; the source used in he example is the default homeshick location), and the value for the `checkout` option is the command that `mr` will run to checkout your repositories.

Then, when you migrate to a new system, you just have to get your `.mrconfig` back (so, it is a good idea to build another castle with it) and run:

```bash
mr checkout # Checkout all the repositories in one single command 
```

Or, if you prefer, you could also run `mr bootstrap <url>` to allow getting the mrconfig file from an external URL.

With any of the above commands, you have recovered all you castles without pain, and now you just have to create the symbolic links in your home directory:

```bash
homeshick link # Create symlinks from all your castle files in the actual home directory
```

With `mr` the workflow is really easy. You can run `mr push` to update all remotes at once, `mr commit -m 'message'` to commit all the changes you have been doing in different castles...

Another very interesting option is to use its hooks to run scripts, for example, after checking out a castle to install the application using that castle, or simply to prepare scripts that setup other aspects of our system.

Having this bit of discipline with your dotfiles is highly rewarding. This way you can keep synchronized different systems where you work and, also, the next time you have to migrate to a new system you will only need the almost ubiquitous dependencies git, bash and perl to feel again at home.

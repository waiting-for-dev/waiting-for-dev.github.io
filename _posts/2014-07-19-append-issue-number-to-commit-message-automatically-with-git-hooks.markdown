---
layout: post
title: "Append issue number to commit message automatically with git hooks"
date: 2014-07-19 13:27:59 +0200
comments: true
categories: git
published: false
---
A great feature in the integration between issue tracking systems and repository hosting services is the possibility to reference an issue from a commit message. You add the issue number and everything gets nicely linked, allowing the subsequent exploration of all commits that led to an issue resolution.

But long numbers are mainly for machines, and it is a pain having to add the issue number each time you commit something, specially if, like me, you tend to commit quite often.

This repetitive and boring task is ideal for some kind of computing automatism magic, and git hooks has a lot of it.

Git comes with the `prepare-commit-msg` hook, which, as its [man page](https://www.kernel.org/pub/software/scm/git/docs/githooks.html#_prepare_commit_msg) states:

> The purpose of the hook is to edit the message file in place [...]

Just taking some consideration about our workflow and our preferences we can get a lot of benefit from it. Following, I explain what works for me, but it can be easily tweaked to adjust other preferences.

As I told before, I think long numbers are for machines, so I don't want to give the issue number a prevailing visual importance. I just want it to be there for machines to use it. So, I don't want the number to appear on the top commit message line, but at the beginning of the body, from where I can move it only if I need to.

But from where will the number be read? Taking advantage of another best practice like feature branches are, we can name them like `user_auth-87369`, where everything after the dash (-) is the issue number. Again, I want it to be at the end of the string.

Then, with the following script in the `.git/hooks/prepare-commit-msg` file, at every commit the number will be automatically taken and introduced in the third line of the message.

{% gist b17e3ba956c29f8972c3 prepare-commit-msg %}

So, when the editor is opened and I edit the commit message, it would look something like that:

    Add authentication gem
    
    [#87369]
    # Please enter the commit message for your changes. Lines starting
    # with '#' will be ignored, and an empty message aborts the commit.
    # On branch user_auth-87369
    # Changes to be committed:
    #	modified:   Gemfile
    #

The script is really simple. To understand it, first it is good to know which parameters git passes to it. Again, from the man page:

> It takes one to three parameters. The first is the name of the file that
> contains the commit log message. The second is the source of the commit
> message, and can be: message (if a -m or -F option was given); template (if a
> -t option was given or the configuration option commit.template is set); merge
> (if the commit is a merge or a .git/MERGE_MSG file exists); squash (if a
> .git/SQUASH_MSG file exists); or commit, followed by a commit SHA-1 (if a -c,
> -C or --amend option was given).

So, first the script checks if `$2` is set. If so, it does nothing, because it would mean that the message is comming from the command line, a merge, a template... They are situations where usually you don't want the issue number, even maybe it is not always the case (fit your needs). Then it uses `sed` to extract the number from the branch name and append it to `$1`, which is the name of the file containing the default commit message.

With this simple script in place, `-` can only be used in the branch name to separate the issue number, but for me its all right because for the other cases I use `_`, but, as I said, it is very easily customizable.

If you want this to be present in any brand new git repository you create, take a look at the [git init template directory](https://www.kernel.org/pub/software/scm/git/docs/git-init.html#_template_directory).

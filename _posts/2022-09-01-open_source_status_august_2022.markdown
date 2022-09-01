---
layout: post
title: "Open Source Status: August 2022"
date: 2022-09-01 00:00:00 +0000
comments: true
categories: ["Open Source Status", hanami] 
published: true
---
Hey, there!

This month I decided to take the step and open [sponsorship on
GitHub](https://github.com/sponsors/waiting-for-dev). I reckon this entails
some kind of acknowledgment, so I'll try to publish regular updates about my
Open Source status.

Let's start with what happened last month (August 2022)!

**Important notice: I won't report about what we're doing at [Solidus](https://solidus.io/), as that's
already part of my paid work with the fantastic team at
[Nebulab](https://nebulab.com/).**

## More robust application detection in Hanami

We've improved how [Hanami detects the application
file](https://github.com/hanami/hanami/pull/1197) to set up its environment.
Previously, it wasn't possible to run `bundle exec hanami console` from a
sub-directory within the application root. Still, a bit of recursion up the
filesystem hierarchy improves the development experience [at the command
line](https://github.com/hanami/cli/pull/34). I initially took this one, and
[Tim](https://timriley.info/), who knows best how Hanami is organized, reshaped
it in the best possible way.

## Listing middlewares

This one came from July, but the [command to list all registered Rack
middlewares](https://github.com/hanami/cli/pull/30) got merged and included in
the latest beta of Hanami. The output is pretty informative, rendering the type
of the middleware (class, object...), the arguments provided on initialization,
and the path where it applies. It also required some [minor adjustments in
Hanami](https://github.com/hanami/hanami/pull/1191)'s main repo.

## Reloading Hanami console

We've introduced a [`reload` method available in the Hanami
console](https://github.com/hanami/cli/pull/36). Its implementation was no
secret; it was just copied from what Hanami 1 did: replacing the current
process with a new invocation.

## Minor improvements

We [removed a leftover file](https://github.com/hanami/hanami/pull/1206) and
all [the old integration tests](https://github.com/hanami/hanami/pull/1207).
The latter will make the test suite less confusing for new developers.

## Under way: decoupling the router from the application

Hanami 2 is not only for web applications, so the router is not a mandatory
system part. We're using the same logic already present for actions and views
to [make router configuration
optional](https://github.com/hanami/hanami/pull/1204). Following Tim's
recommendations, we've also improved the developer experience with a clear
error message.

That's pretty much everything. However, I don't want to say goodbye without
thanking [Seb](https://github.com/swilgosz) for being my first sponsor (and,
once more, for his amazing work at
[HanamiMastery](https://hanamimastery.com/)).

See you soon!

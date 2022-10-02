---
layout: post
title: "Open Source Status: September 2022"
date: 2022-10-02 00:00:00 +0000
comments: true
categories: ["Open Source Status", hanami] 
published: true
---
This month was important in the personal sphere, as I moved out to the
mountains (well, in a town in the mountains, to be fair). Still, I managed to
dedicate some time to Open Source.

## Decoupling the router from the application

I already mentioned in the [past update]({% post_url
2022-09-01-open_source_status_august_2022 %}) that we were making hanami-router
an optional component in Hanami. I [was working on a
PR](https://github.com/hanami/hanami/pull/1204), but it needed to be reverted.
We were also trying to improve DX by raising an error when fetching the
configuration for a gem that is not there. We still need more work there, but
we extracted the part that makes hanami-router optional and merged it on
[another PR](https://github.com/hanami/hanami/pull/1209).

## In the freezer: taking the app path on `hanami new`

I [started this one](https://github.com/hanami/cli/pull/43) as a personal
initiative, as I really wanted to have `hanami new .` work. I always develop
using Docker, so my flow consists of initializing a container in the current
working directory where I want to create the application. One thing led to the
other, and I realized it was more natural to have the `hanami new` argument
always be a path instead of the app module name with `.` as an exception.
However, there're some concerns about how it communicates to the user, and as
it's not a priority at this point, we'll retake it once more important stuff
has been done.

## In progress: making nested slices first-class

Tim introduced the option to [register a slice within another
slice](https://github.com/hanami/hanami/pull/1162). However, they're not picked
for _slice configurable_ things. We [need to fix
it](https://github.com/hanami/hanami/pull/1212).

## Excited with a layer for service objects

I [already said]({% post_url 2022-09-01-open_source_status_august_2022 %}) I
don't talk about my work for Solidus here, as that's part of my paid job, while
my OS Status is primarily something directed to my GitHub sponsorship.

However, I'm very excited about a service layer we'll add there, as it will be
built taking concepts from monads, railway programming, and atomicity patterns.
That's just the same idea I had for a new version of dry-transaction, so
there's a total overlap in my mind. I don't know where it will land, but I see
a field for collaboration here.

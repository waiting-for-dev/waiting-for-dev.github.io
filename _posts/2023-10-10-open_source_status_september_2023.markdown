---
layout: post
title: "Open Source Status: September 2023 - Hello, dry-operation!"
date: 2023-10-10 00:00:00 +0000
comments: true
categories: ["Open Source Status", hanami] 
published: true
---
You promised yourself to provide monthly updates on your Open Source activities, and suddenly, a whole year has passed since the last one. But rest assured, I haven't been idly lounging on the sofa during this time, as much as I might have wished for that luxury.

As I shared in my [last update back in September 2022]({% post_url 2022-10-01-open_source_status_september_2022 %}), I've been dedicating a significant amount of thought to shaping a library for handling business transactions in Ruby. The spark was reignited when the possibility arose for this library to become an integral part of a service layer for [Solidus](https://solidus.io/). However, due to other pressing priorities within the project, my efforts were redirected, but I persevered, chipping away at it during my limited free time. Eventually, I successfully [developed a prototype](https://github.com/waiting-for-dev/kwork), which I affectionately named "kwork." Naming can be quite a challenge. In this case, it paid homage to [Murray Gell-Mann](https://en.wikipedia.org/wiki/Murray_Gell-Mann), who originally contemplated the spelling "kwork" for [quarks](https://en.wikipedia.org/wiki/Quark) (one of the fundamental constituents of matter). The pun tried to mirror the idea that business transactions, too, should be indivisible when it comes to their outputs:

> In 1963, when I assigned the name "quark" to the fundamental constituents of the nucleon, I had the sound first, without the spelling, which could have been "kwork". Then, in one of my occasional perusals of Finnegans Wake, by James Joyce, I came across the word "quark" in the phrase "Three quarks for Muster Mark".

## Kwork is no more; long live dry-operation

In Catalan, there's a saying that goes, "Roda el món i torna al Born," which loosely translates to "Travel the world and come back to El Born." [El Born](https://ca.wikipedia.org/wiki/El_Born) is an ancient district with medieval origins in Barcelona. My grandmother settled there with her family after fleeing their rural hometown, escaping the Francoist army, and it's also where my mother was born. Today, it's transformed into a hub of trendy shops, and the local community can no longer afford to reside there. Nevertheless, this saying conveys the notion that, after a multitude of new experiences, one often finds themselves back where they started. This sentiment beautifully encapsulates my journey with business transactions in Ruby.

Over four years ago, I submitted a [PR to dry-transaction](https://github.com/dry-rb/dry-transaction/pull/119) to address one of its most significant limitations, which was the inability to freely reuse output from previous steps. Following discussions within the dry-rb team, Tim Riley informed me that they had decided to [retire the library](https://github.com/dry-rb/dry-transaction/pull/119#issuecomment-507005499) due to this and other constraints. At that time, it was quite a disappointment for me, as I believed we were on the cusp of delivering exactly what Ruby needed: an idiomatic monadic DSL.

A couple of years later, now as a contributor to dry-rb, I proposed a couple of solutions to resurrect dry-transaction. The [first approach](https://gist.github.com/waiting-for-dev/caece1891a84125fb3415026f8d310b3#file-dry_transaction_resurrection-rb) closely mirrored the old dry-transaction and the work in that PR, while [the second](https://gist.github.com/waiting-for-dev/caece1891a84125fb3415026f8d310b3#file-dry_transaction_resurrection2-rb) leaned more towards the final shape of kwork or dry-monad's do notation. However, the team was deeply engrossed in their work on Hanami, and it was entirely understandable that they couldn't manage everything simultaneously.

Just last month, I learned that Tim Riley was eager to commence work on a dry-transaction successor. We engaged in extensive conversations over Slack, exchanging lengthy messages with Tim and Brooke Kuhlmann (author of [transactable](https://alchemists.io/projects/transactable)). Concepts surrounding monads, do notation, dry-transaction, and the Railway Pattern were all in the mix, helping us to envisage what could potentially be the most fitting form for a Ruby library.

And so, here I am, actively contributing to the inception of [dry-operation](https://github.com/dry-rb/dry-operation). My initial work this month has involved the [extraction of key elements from kwork, molding them to align with the established patterns within the dry-rb ecosystem. I'm thrilled to report that we've already got the [syntax for the fundamental features](https://github.com/dry-rb/dry-operation/pull/6) up and running, and we're now diligently seeking the best possible [compromise to enhance the developer experience](https://github.com/dry-rb/dry-operation/pull/9).

On a personal note, I've been eagerly looking forward to deepening my collaboration with dry-rb for quite some time. Having the opportunity to spearhead the development of a new library fills me with immense joy and gratitude. I'd like to extend my heartfelt thanks to Tim and the entire team for placing their trust in me for this exciting endeavor!

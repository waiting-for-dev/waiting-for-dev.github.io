---
layout: post
title: "A walk with JWT and security (III): JWT secure usage"
date: 2017-01-25 08:43:41 +0000
comments: true
categories: [jwt, "A walk with JWT and security"] 
---
In this post, after having discussed the [why](http://waiting-for-dev.github.io/blog/2017/01/23/stand_up_for_jwt_revocation/) and [how](http://waiting-for-dev.github.io/blog/2017/01/24/jwt_revocation_strategies/) on revoking JWT tokens, we'll talk about some general advices that should be kept in mind while using JWT for user authentication.

The most important one is what we have already said: add a revocation layer on top of JWT, even if it implies losing its stateless nature. The opposite is, most of the times, not acceptable. But there are more aspects to consider.

* Don't add information that may change for a user. For instance, a lot of times people recommend adding role information so that a database query can be saved. For example, in your JWT payload you would add that `alice` user has `3` as id and she has `admin` role. What happens if `admin` privilege is revoked for `alice`? If the token that states the opposite is still valid she could still 'look' like an `admin`.

* Don't add private information unless you encrypt your tokens. Bare JWT (without encryption) is just a signed token. It means that the server perfectly knows whether it issued the incoming token or not. But the information contained in the token is readable by **everyone** ([try it in 'Debugger' section on JWT site](https://jwt.io/)); it is just base64 encoded. So I recommend just coding harmless information like the user id.

* Be careful on what you use to identify a user. For example, if you just add the user id and you have two different user resources, say a `User` and an `AdminUser`, a valid token for the `User` with id `3` would be valid for the `AdminUser` with the same id. In this case, you should add and use something like a `scope` claim to distinguish between the two.

With all of that in mind, in my view, when choosing an authentication mechanism, if possible, you should prefer using cookies. They have been around for a lot of time and they are battle tested. Authentication is an essential security aspect of an application and the less you move away from the standard way the most placidly you will sleep.

So, in which situations may it be better not to use cookies? I can think of two:

* It is not easy to share cookies between different domains. That's not true for [CORS](https://en.wikipedia.org/wiki/Cross-origin_resource_sharing) requests, where `Access-Control-Allow-Credentials` header can be used to instruct the browser to accept them. But, for instance, routine `GET` requests let you out of the game.

* Mobile platforms support. It is not longer true for acceptable modern APIs, but if you need legacy compatibility you could have troubles.

Besides, you should be aware that JWT authentication could expose you to [XSS attacks](https://en.wikipedia.org/wiki/Cross-site_scripting), even if it is not less true that cookies could expose you to [CSRF attacks](https://en.wikipedia.org/wiki/Cross-site_request_forgery). In both cases, the best you can do is using good and modern tools and frameworks, always avoiding to reinvent the wheel.

---
layout: post
title: "A walk with JWT and security (and IV): A secure JWT authentication implementation for Rack and Rails"
date: 2017-01-26 08:19:23 +0000
comments: true
categories: [jwt, ruby, rails, "A walk with JWT and security"] 
---
After having discussed in the three previous posts ([I]({% post_url 2017-01-23-stand_up_for_jwt_revocation %}), [II]({% post_url 2017-01-24-jwt_revocation_strategies %}) and [III]({% post_url 2017-01-25-jwt_secure_usage %})) about JWT authentication and security, I would like to share two Ruby libraries I made in order to implement these security tips we have discussed so far. One of them is [warden-jwt_auth](https://github.com/waiting-for-dev/warden-jwt_auth), which can be used in any Rack application that uses [Warden](https://github.com/hassox/warden) as authentication library. The other one is [devise-jwt](https://github.com/waiting-for-dev/devise-jwt), which is just a thin layer on top of the first that automatically configures for [devise](https://github.com/plataformatec/devise) and, subsequently, for Ruby on Rails.

## What did I expect from a JWT authentication Ruby library?

When I looked for current Ruby libraries helping with JWT authentication I wanted them to have a string of conditions:

* I wanted it to rely on Warden, a heavily tested authentication library that works for any kind of Rack application. This decision was also based on the fact that most part of applications in my current company use it.
* It should be easily pluggable into Rails with Devise (which uses Warden). That way, in Rails applications, I could use Devise database authentication for the sign in action and JWT for the rest.
* Relying on Warden and not being a full authentication system, it should be very simple to audit.
* Zero monkey patching. A lot of libraries meant to work with Devise have a lot of monkey patching. I don't like that.
* It should be ORM agnostic when used outside of Rails.
* It should support or make easy the implementation of a revocation strategy on top of JWT.
* It should be flexible enough to distinguish between different user resources (like a `User` and an `AdminUser`), so that a token valid for one of them can't impersonate another resource user record.

I looked at what it had been done so far, and here it is what I found.

* [Knock](http://www.wordreference.com/es/en/translation.asp?spen=auditoria). Surely, it is the most serious attempt of implementing JWT authentication for Rails applications. But it is Rails specific, [does not integrate with devise](https://github.com/nsarno/knock/issues/70) and it has [ruled out adding a revocation layer](https://github.com/nsarno/knock/issues/15).

* [rack-jwt](https://github.com/eigenbart/rack-jwt) It is nice that it is for any Rack application, but it is also true that it needs some handwork to integrate with tested authentication libraries like Warden. Besides, it doesn't help with revocation.

* [jwt_authentication](https://github.com/Rezonans/jwt_authentication) It is only for Rails with devise. I think is quite complex and tries to mix [simple_token_authentication](https://github.com/gonzalo-bulnes/simple_token_authentication), which adds even more complexity and monkey patching. It doesn't support revocation.

## What I have done

As I said, finally I ended up programming two libraries. I'm not going to go through their details, because they can be consulted in their README and surely they will change over time. I'll just give some generic vision of what they are.

### warden-jwt_auth

[warden-jwt_auth](https://github.com/waiting-for-dev/warden-jwt_auth) works for any Rack application with Warden.

At its core, this library consists of:

- A Warden strategy that authenticates a user if a valid JWT token is present in the request headers.
- A rack middleware which adds a JWT token to the response headers in configured requests.
- A rack middleware which revokes JWT tokens in configured requests.

As it requires the user to implement user resource interfaces along with revocation strategies, it is completely ORM agnostic.

Having warden the 'scopes' concept, they can be leveraged to flag each token in order not to confuse user records from different resources.

### devise-jwt

[devise-jwt](https://github.com/waiting-for-dev/devise-jwt)] is just a thin layer on top of warden-jwt_auth. It configures it to be used out of the box for devise and rails.

Basically, it does:

- Creates a devise module, which when added to a user devise model configures it to be able to use the JWT authentication strategy. This devise module implements required user interface for ActiveRecord.
- Implements some revocation strategies for ActiveRecord.
- Configure create session devise requests to dispatch tokens.
- Configure destroy session devise requests to revoke tokens.

Hope they can be useful.

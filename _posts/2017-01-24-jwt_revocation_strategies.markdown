---
layout: post
title: "A walk with JWT and security (II): JWT revocation strategies"
date: 2017-01-24 06:20:17 +0000
comments: true
categories: [jwt, "A walk with JWT and security"]
---
In [my last post]({% post_url 2017-01-23-stand_up_for_jwt_revocation %}) I concluded why, in my view, JWT revocation makes a lot of sense. Now, it's time to analyze which revocation strategies can be used and what their pros and cons are

## Short-Lived tokens

The first one I'll mention is not an actual revocation strategy, but some people argue that it is the best you can do with JWT to maintain its stateless nature while still mitigating its lack of built-in revocation. They say JWT tokens should have short expiration times, like, say, 15 minutes. This way, the time frame for an attacker to do some harm is reduced.

I don't agree with that. I think it is still unacceptable not to have an actual server-side sign-out request. Shouldn't the client do the right job destroying the token, a user could think that she has signed out from a server, but somebody coming just after them could be able to impersonate them.

Furthermore, it also has implications from a usability point of view. In some sites, such as online banking, it makes sense to force a user to sign in again if he has been inactive for 15 minutes, but in other scenarios, it can be a real hassle.

## Signing Key Modification

Changing the signing key automatically invalidates all issued tokens, but it is not a way to invalidate single tokens. Doing that in a sign-out request would mean that everybody gets kicked out when just one requires leaving. Changing the signing key is something that must be done when there is some indication that it could have been compromised, but it doesn't fit the scenario we are talking about.

## Tokens Denylist

Keeping a denylist of tokens is the simplest actual revocation strategy for individual tokens that can be implemented. If we want to invalidate a token, we extract from it something that uniquely identifies it, like its "jti" claim, and we store this information in somewhere (for example, a database table). Then, each time a valid token arrives, the same information is extracted from it and queried against the denylist to decide whether to accept or refuse the token.

It is very important to notice that what is persisted is not the whole token but some information that uniquely identifies it. Should we persist the whole token, we will do that using encryption and adding a different salt for each one of them, treating it similarly to a password. However, for example, a "jti" claim alone is completely useless for an attacker.

A denylist strategy has some advantages:

* Very easy to implement.

* It works well when we can have a user signed in from different devices, and we just want to sign her out from one of them. Each denylist record references one single token, so we have complete control.

But it also has some drawbacks:

* With a denylist, there would be no way to sign a user out from all their devices. We would need to add some flag in the user record.

* A denylist can grow quite rapidly if the user base is large enough because, at least, every sign-out request would add a record. To mitigate it, maintenance cleaning could be scheduled, which would empty the list at the same time that the signing key is changed. Of course, this would sign out all users, but surely it is something we can live with.

* A denylist usually requires a second query (the one to the list) besides the usually needed query for the user.

## User-Attached Token

This strategy is analogous to what is usually done with opaque tokens. Here, the information that uniquely identifies a token is stored attached to the same user record that it authorizes; for example, as another column in the user record. When a token with a valid signature comes in, that information is extracted and compared with the one attached to the user to decide whether authorization should be allowed. When we want to sign out the token, we simply change or nullify it.

In fact, these two steps in the checking process (fetching the user and comparing the token with the persisted information) can be reduced to a single one if we fetch the user through the token information. For example, we could have a "uid" column where we would store the "jti" claim of the current active token. When fetching the user, we would look that column up, and no matching would mean that the token is not valid. Of course, we need to be sure that the "uid" is actually unique.

As in the case of the denylist strategy, it is very important to understand that it is not the whole token that is persisted but a unique representation of it to avoid security concerns related to secrets storage (see the denylist section for details).

User-attached tokens have several advantages that, for me, make it a better idea than the denylist strategy:

* Instead of storing which tokens are not valid (which grows over time), we store the one that is valid. As a consequence, there is no need to schedule any clean-up.

* A single query to the user resource suffices to do the checking, instead of having to query both the user and a denylist.

On the other side, it also has one inconvenience:

- It makes dealing with multiple client applications from which a single user can interact more challenging. In this scenario, in the user storage, we should differentiate active tokens per client application, maybe using different columns or doing some kind of serialization. From the token side, a good idea would be to differentiate the client through the "aud" claim. Signing out from one device would mean revoking just the affected token, while signing out from all clients would require revoking all the tokens for that user.

---
layout: post
title: "A walk with JWT and security (I): Stand up for JWT revocation"
date: 2017-01-23 11:02:55 +0000
comments: true
published: true
categories: [jwt, "A walk with JWT and security"]
---
There is some debate about whether JWT tokens should be revoked (for example, on signing an user out) or whether, on the other side, doing so is a nonsense that breaks the primary reason why this technology exists.

JWT tokens are self-describing. They encapsulate the information that a client is trying to provide to an API, such as the id of a user that is trying to authenticate. For this reason, they are defined as stateless. The server only needs to validate that the incoming token has its own signature in order to trust it; it doesn't have to query any other server, like a database, another API...

Self-describing tokens exist in opposition of more traditional opaque tokens. These usually are a string of characters which an API needs to check against another server to reveal if they match the one associated to a user record.

Stateless nature of a pure JWT implementation has a very important implication. A server has nothing to say about a token except whether it was signed by itself or not, so it has no way to revoke them individually (it could invalidate all issued tokens at once changing its signing key, but not a single one in isolation). This means that it is not possible to create an actual sign out request from the server side.

Even if this fact can acknowledge a stateless purism, I consider it near an abomination from the security point of view. It is true that if both client and API belong to the same team token client-side revocation can be kept under control. But server-side technologies have better tools to deal with security and has less attack vectors like, say, a web browser. If the API is consumed by third party clients, then relying on the fact that they will do the right job is completely unacceptable.

However, there is nothing in the JWT technology that prevents adding a revocation layer on top of it. In this scenario, an incoming token is verified and, like in opaque tokens, another server is reached to check if it is still valid.

At first impression it actually seems nonsense. It looks like we are ending at the same land of opaque tokens with the additional signature verification overhead. However, a closer look reveals that we gain some security benefits and that, in fact, it doesn't exist such overhead.

Let's examine first which security benefits we can get. When revoking a JWT token there is no need to store the whole token in the database. As it contains readable information, we can, for example, extract its `jti` claim, which uniquely identifies it. This is a huge advantage, because it means that stored information is completely useless for an attacker. Therefore, there is no need to hash it to accomplish a good zero-knowledge policy, and there is no need to keep a salt value for each user to protect us from rainbow table attacks.

Now about the alleged overhead that JWT with revocation would suppose. As we said, with JWT we have to take two steps: signature verification and a server query. In opaque tokens, instead, it seems we just have to query the server. But last is not true. A secure opaque token implementation should not store unencrypted tokens. Instead, it should require the client to send a kind of user uid along with the unencrypted token. The user uid would be used to fetch the user and the unencrypted token would be securely compared with the hashed one. So, this hash comparison is also a second step which, even I haven't benchmarked it, should have a similar overhead with signature verification.

Using a standard like JWT has also some abstract benefits which are difficult to measure. For example, usually, with current libraries, you get for free an integrated expiration management through the `exp` claim. However, as far as I know, there is no standard for opaque tokens, which make libraries prone to reinvent the wheel every time. In general, using JWT should be more portable.

Of course, I'm not saying that JWT with revocation is always good and opaque tokens are always bad. There has been detected JWT specific attacks that good libraries should have fixed, and irresponsible use of JWT can have some dangers that we'll examine in further posts. At the end, developers must be aware of what they are using and a secure opaque token implementation is also very valid. But adding the revocation layer on top of JWT shouldn't be disregarded as easily. On the next post, we'll take a look at some revocation strategies that can be implemented.

---

Some background in the debate about JWT security and revocation:

- [Cookies vs Tokens: The Definitive Guide](https://auth0.com/blog/cookies-vs-tokens-definitive-guide/)
- [Stop using JWT for sessions](http://cryto.net/~joepie91/blog/2016/06/13/stop-using-jwt-for-sessions/)
- [I don’t see the point in Revoking or Blacklisting JWT](https://www.dinochiesa.net/?p=1388)

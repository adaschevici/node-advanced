+++
title = "Authentication Dance"
date = 2021-01-17T11:05:29+01:00
draft = true
weight = 1
+++

Authentication is one of the more complex features you will be tasked with however it is one of those things that
is __fire and forget__ because if you get it right you won't have to review it again.

The problem with it is if you don't get it right it may open the api up to security faults and unless you are a really
big player it will cause you to lose customers instantly potentially opening the door to bankruptcy.

There are ways to avoid implementing the authentication yourselves by using an __authentication as a service provider__.
That would be prefered to hacking something together that you are not 💯 on. My personal favorite is [Auth0](https://auth0.com/authentication),
they provide a lot of code samples and have client side validation tools as well.

{{% notice tip %}}
__If you don't have time to implement it yourself, delegate!__
{{% /notice %}}

## JWT

Of the modern techniques for authentication JWT is the simplest as it only involves local resources. `JWT` is a type of
token that allows you to secure your services. It is composed of 2 parts, the payload and the signature. It has quite a few benefits:

{{< lazy-image image="login.png" lightbox=false />}}

- it's pretty straightforward to implement
- the flow is quite easy to wrap your head around
- the cryptographic complexity is similar to that of ssh
- it's stateless so it can be easy to have a decentralized architecture in your API
- the standard is very well defined
- it allows for maintaining a set of claims of the user as a payload
- it is secure as it is crypto signed to prevent tampering

## Other concepts in the JWT lingo

- __JOSE__ : JS standard object encryption, this is a common standard and it does not refer to an implementation but
  rather a set of practices around JWT. This sometimes is refered to as __JOSE__ headers.
- __JWE__ : is a __JWT__ that is signed and encrypted using a __JWK__, therefore the set of claims is encrypted and
  opaque to the client, it can be decoded and checked on the API
- __JWS__: is the classic __JWT__ that is commonly called __JWT__ even if it isn't technically that. It is the signed version of the token. It's tamper
  proof because the payload is signed cryptographically and if you were to tamper with it it would fail verification on
  the api side. The payload is `base64` encoded and can be decoded easily on the client side or using tools such as
  [Auth0s JWT inspector](https://jwt.io/#debugger-io)

This authentication type strikes a balanced approach between security and ease of implementation. The stateless token
makes it a logical choice in distributed systems. It makes sense when you are only protecting you API and once you find
that you wish to grant third party services access you can start about thinking of Oauth2 as well. They are not mutually
exclusive.

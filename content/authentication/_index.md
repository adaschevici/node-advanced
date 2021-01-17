+++
title = "Authentication & the musts"
date = 2021-01-17T11:02:40+01:00
draft = true
pre = "<b>2. </b>"
weight = 2
+++


When building a REST api it has become mandatory to secure it using both `authentication` and `authorization` as web applications require role based access as well as identity restricted access.

This needs to be done as once you expose an endpoint to the `WWW` it will get abused however obscure it may be.

There are multiple ways to perform authentication. The rundown of the various techniques would be as follows:

1. Basic Authentication - this is the most straightforward and it is not recommended, however you may come across some
   endpoints that are still only protected using this method. While it may be acceptable in a proof of concept it is
   not recommended to use this in any public facing APIs

2. Bearer Authentication - this is one of the most used methods today. The basic usage is via the `{ 'Authorization':
   'Bearer <token>' }` header. This authentication type is used in both JWT authentication and Oauth 1 & 2. It is used
   with centralized authorization servers which may allow for statelessness of the authentication and helps to
   distribute your api across multiple application servers. The most common implementation of this is JWT based.

3. Api Key Authentication - this can be used for authenticatiing at api level. It uses the `{ 'Authorization': 'Apikey
   <apikey>' }` header. The security levels of this authentication are not any better than Basic Authentication but it
   can be used in internal communication between you services. Another point to take into account is that the security
   risk is considered acceptable as the API is knowingly given a good amount of permissions. Restrictions can be
   enforced at locality level from the network layer.

4. Oauth2 - this is not an authentication mechanism. The accepted description is that Oauth2 is in fact a delegation
   protocol above all else. It has multiple concepts that could be retrofitted into an authentication flow however that
   does not make it authentication. Most current authentication mechanisms are based in some way on Oauth. An example of
   this would be the `OpenID Connect` which is used by Amazon for providing authentication. Under the hood it uses
   Oauth2 and JWT for authentication.

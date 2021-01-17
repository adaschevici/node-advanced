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

1. __Basic Authentication__ - this is the most straightforward and it is not recommended, however you may come across some
   endpoints that are still only protected using this method. While it may be acceptable in a proof of concept it is
   not recommended to use this in any public facing APIs

2. __Bearer Authentication__ - this is one of the most used methods today. The basic usage is via the `{ 'Authorization':
   'Bearer <token>' }` header. This authentication type is used in both JWT authentication and Oauth 1 & 2. It is used
   with centralized authorization servers which may allow for statelessness of the authentication and helps to
   distribute your api across multiple application servers. The most common implementation of this is JWT based.

3. __Api Key Authentication__ - this can be used for authenticatiing at api level. It uses the `{ 'Authorization': 'Apikey
   <apikey>' }` header. The security levels of this authentication are not any better than Basic Authentication but it
   can be used in internal communication between you services. Another point to take into account is that the security
   risk is considered acceptable as the API is knowingly given a good amount of permissions. Restrictions can be
   enforced at locality level from the network layer.

4. __Oauth2__ - this is __not__ an authentication mechanism. The accepted description is that Oauth2 is in fact a delegation
   protocol above all else. It has multiple concepts that could be retrofitted into an authentication flow however that
   does not make it authentication. Most current authentication mechanisms are based in some way on Oauth. An example of
   this would be the `OpenID Connect` which is used by Amazon for providing authentication. Under the hood it uses
   __Oauth2 and JWT for authentication__. You can find a list of __certified OpenID providers__ [here](https://openid.net/developers/certified/)


Authentication is most often implemented with a mixture of Bearer tokens and Oauth. This is currently the most maintaned
method in the security landscape and has various implementations from the big companies. It is offered as SAAS services
and centralized logins with common platforms. Google, Facebook, Twitter, Github, Gitlab and many others offer Oauth
authorization servers for your apps so in essence you can grant authentication capability by using their trusted
authorization servers. This type of authentication requires for the app to be approved, and the tokens are provide by
Google, Facebook etc.

There are other more specific platforms that offer authentication as a service such as [Auth0](https://auth0.com/authentication) and [Okta](https://www.okta.com/free-trial/customer-identity/).
The choice for a specific method of implementation depends a lot on the security requirements for your app and what you
need to integrate with.

Most application ecosystems prefer implementing methods for single sign on or SSO, essentially allowing you to use the same
authentication and authorization entity across multiple, potentially unrelated services. The best approach at the time
of writing is to use OpenID Connect which is an evolution of the OpenID standard that started circa 2005.

{{% notice note %}}
It is important to keep in mind the distinction between authentication and authorization, the simple rule of 👍
is authentication is all about identity and authorization is about permissions to perform specific types of actions.
{{% /notice %}}


{{% notice tip %}}
The authentication methods in the current landscape promote authenticating across multiple services in a granular
fashion. This is what __Oauth__ and __Oauth2__ to a bigger extent provide. This is what is meant by delegation. A
resource owner can grant limited authorization to a third party for accessing a protected resource using a token that is
generated on the fly as permissions are granted.
{{% /notice %}}

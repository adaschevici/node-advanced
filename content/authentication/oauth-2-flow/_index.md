+++
title = "Oauth2 Flow"
date = 2021-01-17T20:59:57+01:00
draft = true
weight = 2
+++

Oauth2 is __not an authentication protocol__. It is often mistook as one because Oauth is used __inside__ many of the
existing authentication protocols. Treating it as an authentication protocol could in fact make your applications less secure.

{{< lazy-image image="oauth2_flow.png" lightbox=false />}}

The main cause why __Oauth2__ is such a handful is because the flow is quite complicated. It also requires
to implement a predefined set of endpoints, and the standard refers to a set of urls that have to be accessed in this
authorization dance.

The main usecase for oauth is to allow apps to request authorization for calling into a service from its owner. At the
center of this sits the authorization server that also has the ability to generate tokens that allow you to access the
protected resources.

The tokens used in oauth can be __JWT__, that's right __Oauth__ and __JWT__ are not mutually exclusive. Another thing to
keep in mind is that provided you use a library for oauth they will provide hooks for the various endpoint prerequisites
the flow has.

Since it is a very complex flow it is better to use the libraries available for implementing it as they will prevent you
from shooting yourself in the foot. There are very few cases when you will want to implement an oauth2 server from
scratch.

Oauth2 is an extension to Oauth providing extra grant types. The `grant_type` in the payload is what sets Oauth2 from
Oauth and why it isn't technically an authentication system.

The grant types that are encountered most often are:
- __`authorization_code`__: you want to use this when you implement oauth for an app with existing platforms (google, FB,
Github etc.)
- __`client_credentials`__: can be used to emulate authentication, the token can be generated directly using a username
and password withouth user intervention for approval
- __`refresh_token`__: this is the one that allows you to refresh tokens

[__(See More...)__](https://alexbilbie.com/guide-to-oauth-2-grants/)

Oauth2 also comes with a `scope` parameter that you could use as you would __JWT__ claims, allowing you
to implement role based access for your app. This can be added as a list of strings in the `scope` of the Oauth2
request.


{{% notice tip %}}
__In the real world the most common application of Oauth/Oauth2 is to provide social login__
{{% /notice %}}

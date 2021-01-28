+++
title = "Manual Testing"
date = 2021-01-27T20:39:52+01:00
draft = false
weight = 4
+++

If writing the unittests as you go is not exactly your jam, and I understand sometimes it can become tedious you can
also have a go at testing the api using a rest client, and my favorite one is [__Postman__](https://www.postman.com/downloads/)

## Postman


{{< lazy-image image="postman-pic.png" lightbox=false />}}

Postman can be used as a standalone client for sending requests to your express api. This is handy for not having to run
the client app(in the most likely case this would be a single page app).

One of postman's nice features is that it allows you to also keep a collection of the requests you have made previously.
This helps you by not having to keep track of what requests you made and does it for you automatically.

Another nice feature is that it allows you to share request collections with your team(on paid plans), but you can
export a serialized collection as well.

{{< lazy-image image="collection-export-pic.png" lightbox=false />}}

Aside from sharing request collections you can also [document APIs](https://learning.postman.com/docs/publishing-your-api/documenting-your-api/).
This is not as slick as swagger documentation but it is still pretty useful for sharing say with the frontend
development teams.

The last and potentially the most interesting part of postman is the ability to validate and test responses via the
`Tests` tab. You can also validate the response using JSON schema.

{{< lazy-image image="test-response-pic.png" lightbox=false />}}

## Swagger

[Swaggeer](https://swagger.io/) is another way to document your api. There is a good package for doing [this with
express](https://www.npmjs.com/package/swagger-ui-express). It will add an extra endpoint to your app where you can view
the existing api endpoints. It also allows you to specify the request that can be made along with example payloads.

[See example...](https://petstore.swagger.io/)

A thing to note is that you need to document an api swagger will be part of the api so it needs to either be restricted
through IP restrictions or authentication to a reserved few. The best approach would be to be setup by the environment.

Because of this postman may prove to be more useful in some cases.

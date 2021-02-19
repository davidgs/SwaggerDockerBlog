<img src="https://img.shields.io/badge/Camunda%20DevRel%20Project-Created%20by%20the%20Camunda%20Developer%20Relations%20team-0Ba7B9">

# Swagger API access with Camunda Platform

Have you ever fired up the Camunda Platform Docker instance and wished you could do live-calls to the API via a [swagger server](https://swagger.io)? We have! And like most things we wish we could do, we go out and make it happen.

## Coming Soon

To be clear, this integration is coming to the official Camunda Platform Docker container. It's just not ready yet. So this is really more of an interim solution rather than the be-all and end-all solution, but it works, and it makes sending API calls to a live instance of Camunda Platform a *lot* eaiser. So follow along and we'll show you how to run it yourself.

## CORS is your friend, and not your friend

In general, and on the regular internet, Cross Origin Resource Sharing (CORS) keeps you safe by not loading resources from random, untrusted sources. This is generally a good thing. Until it isn't.

When isn't it? When you want to do something like make API calls from one host to another when the 2 hosts don't have an explicit trust agreement. Like between 2 docker containers. Or between your laptop and a docker container.

Yes, you can go in and set a header in the HTTP server such that `Access-Control-Allow-Origin: *` and that will solve the problem (while creating a host of other problems). But when you're delaing with a pre-built docker container that runs a service via tomcat, it's never quite that simple.

## How this works

We decided that, given the above CORS issue, the simplest way to tackle the whole thing was to add an nginx proxy server to the existing docker container. That way you can have everything run in one container, and not have to worry about CORS at all.

We made no changes to the underlying Camunda Platform instance to make this work. That instance is still accessible via the docker container's port 8080.

What We did was add the swagger server on port 8081 within that same docker container.

And now you're thinking "but that doesn't solve the CORS issue!" and you're right, it doesn't. If you go to the swagger instance on port 8081 (if you export that port when you start the docker container) you will get the swagger server and see the APIs. But if you try to execute any of those API calls, you will quickly see the impact of CORS. Your API calls will all fail.

![Screenshot showing the API server on port 8081](images/Screen%20Shot%202021-02-19%20at%2012.19.33%20PM.png)

Enter nginx. Nginx is a very small, super lightweight web server that is configurable to act as a proxy. I set it up to listen on port 8000 of the docer container, and to proxy calls based on the URL. point your browser at http://docker-container:8000/docs and nginx will forward that call to port 8081, where the swagger server lives. Point your browser to http://docker-container:8000/camunda and you will be redirected to the standard Camunda Platform Task Manager, Cockpit, etc.

You will need to change the port in the swagger server to port 8000 from port 8080:

![Screenshot showing using port 8000](images/Screen%20Shot%202021-02-19%20at%2012.21.08%20PM.png)

## Making API Calls

Why is all this even necessary? Well, if you've ever wanted to try out API calls, to a live server, and get actual results, then swagger is your friend.

Swagger lets you make live API calls against a running server instance, and get real results back!

![screenshot of live API call](images/Screen%20Shot%202021-02-19%20at%2012.21.36%20PM.png)




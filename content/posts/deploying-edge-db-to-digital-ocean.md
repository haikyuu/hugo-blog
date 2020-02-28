---
title: "Deploying Edge Db to Digital Ocean - nginx reverse proxy"
date: 2020-02-28
draft: false
toc: false
images:
  - images/reverse_proxy.png
thumbnail: "images/reverse_proxy.png"
tags:
  - devops
  - digital ocean
  - edgedb
  - nginx
---

If you're not familiar with EdgeDB, make sure you read [my short intro](https://abdellah.blog/posts/exploring-edge-db/) here. Or check out
their [blog](https://edgedb.com/blog) or [roadmap](https://edgedb.com/roadmap) to get a sense. Or even better, [try it out!](https://edgedb.com/download).
tl;dr: It's the DataBase of the future without much hype!

When the alpha2 version of EdgeDB was released, i quickly downloaded it to my Mac Air. But
since i had a relatively old OS, i had some problems running it. And since there were some issues
with docker install for Mac, i had no other solution than deploying it to the cloud.

It turned out quite easy and fun to do. Let's see how that was.

## Setting up Digital ocean

Signing up for [Digital Ocean](digitalocean.com/) requires you to enter your credit card and verify your ID.
Yes, i know ... Tedious!
But fear not. That's the only tedious part of it.
First, it was super quick (less than a minute). And after that it gets easier.

- Creating a droplet was fast and easy. I went with the smallest one i chose Ubuntu as a distro.
- Setting up SSH was quite easy. So i used it as if it was my own machine. I installed VIM and ohmyzsh and a couple more utilities.
- I also saved a snapshot for future use. Now i can destroy the droplet (to save money 😎 ) and load it again.
- And since i found it very fun, i even created my own CLI to try out the API. More on that in a separate post.

## Installing EdgeDB and trying the GraphQL API

It took a couple of minutes since it's only a couple of commands. So i created a simple schema and exposed the DB as a GraphQL API.
Now i could hit `server-ip-address:port/explore` to play around with the amazing GraphiQL.

## What's next?

Now that i'm up and running, the next thing was to start creating the schema of my project.
Yes, i am building a product using alpha quality tools. Not for the feint hearted, i know.

First, i tried using the GraphQL API from my web app, but i was hitting CORS issues.
So i created an [issue in Github](https://github.com/edgedb/edgedb/issues/1230) and after discussing with [@elprans](https://twitter.com/elprans), one of the creators of EdgeDB.
He told me the HTTP implementation is still basic in alpha2, and i need to use a reverse proxy with nginx to proceed.

## Nginx

Before looking at the config, here is the big picture of what we want. Created with the amazing [excalidraw](excalidraw.com/)

![reverse proxy schema](/images/reverse_proxy.png)

After installing the latest [nginx](https://www.nginx.com/) in my Ubuntu server, i created a simple reverse proxy i found in the net as follows:

```nginx
server {
  listen 7000;
  listen [::]:7000;

  access_log /var/log/nginx/reverse-access.log;
  error_log /var/log/nginx/reverse-error.log;

  location / {
    # Simple requests
    if ($request_method ~* "(GET|POST)") {
      add_header "Access-Control-Allow-Origin"  *;
    }

    # Preflighted requests
    if ($request_method = OPTIONS ) {
      add_header "Access-Control-Allow-Origin"  *;
      add_header "Access-Control-Allow-Methods" "GET, POST, OPTIONS, HEAD";
      add_header "Access-Control-Allow-Headers" "Authorization, Origin, X-Requested-With, Content-Type, Accept";
      return 200;
    }
    proxy_pass http://edgedb_server_address:8888;
  }
}
```

I know it's not secure but it's enough for my lovely side project's
staging environment. In prod, i will use the same domain.

Now i can call my GraphQL API from the web app. And It's looking great so far.

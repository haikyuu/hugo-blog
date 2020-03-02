---
title: "Why I created my own Digital Ocean CLI"
date: 2020-02-03T13:16:29+01:00
draft: false
toc: false
images:
  - images/logo-do.jpg
thumbnail: images/logo-do.jpg
tags:
  - digital ocean
  - CLI
  - JavaScript
  - Cloud
---
## What?
I have several side projects ongoing. And i don't want to run them locally.
Instead i'd like to deploy them to Digital Ocean and work from there.

## Why?
- I want to go easy on my lovely Mac Air machine.
- [EdgeDB](https://edgedb.com/) is one of the tools in my ToolBox™️ but it doesn't work in my Mac (High Sierra)
- I'd like to get acquainted with [Digital Ocean](https://digitalocean.com/).
- Why not?!

## The Problem
Since i have several side projects, deploying them is going to cost some money.
And i don't need to keep them alive for now since i don't have any users yet.

## Solution 1: manually
The solution is to:
1. Create a droplet and put my project in there.
2. Save a snapshot when i'm done.
3. Destroy the droplet

This works because Digital Ocean bills hourly on usage.
But it's a bit tedious to do using the admin panel (easy but repetitive).

## Solution 2: automatic
The good news is that Digital Ocean has got an API.
And I can use it to automate this little process.

## Implementation
First, authentication.
> You can generate an OAuth token by visiting the [Apps & API section](https://cloud.digitalocean.com/settings/applications) of the DigitalOcean control panel for your account.

After getting the API key, i created a cli app using [oclif](https://oclif.io/).
It's pretty straightforward. Here is the command that saves a snapshot:

```ecmascript 6
class droplet extends Command{

async snapshot(id) {
    let dropletId = id;
    if (!id) {
      const { droplets } = await fetch(`/droplets`);
      const { droplet } = await prompt([
        {
          name: "droplet",
          message: "Select a droplet to create a snapshot from",
          type: "list",
          choices: droplets.map(({ name }) => name)
        }
      ]);
      const { id: dropletIdToSnapshot } = droplets.find(({ name }) => droplet);
      dropletId = dropletIdToSnapshot;
    }
    this.log(`Snapshotting Droplet ${dropletId}`);
    const result = await fetch(`/droplets/${dropletId}/actions?type=snapshot`);
    this.log(result);
  }
}
```
Full source code [here](https://github.com/haikyuu/digital-ocean-cli)

[Inquierer](https://github.com/SBoudrias/Inquirer.js/) library provides prompt method which is very handy.
And we get a lot for free when using oclif:
- parameter parsing and validation.
- help in commands
- structure
- logging
- and a lot more

Digital Ocean API is easy to use and well documented. The only thing i miss is the ability to create a droplet from a snapshot.
I contacted support about it, and they were very helpful.

### Debugging method #1: Remove unnecessary libraries and abstractions

It turned out to be working using `Curl`. So i looked closer and added a `Content-Type: application/json` header. It works now 🎉

An alternative is using the official cli.
`doctl compute droplet create test --region nyc3 --size s-1vcpu-1gb --image <snapshot_id>`

That said, i highly enjoyed my experience with Digital Ocean and it's going to be my provider of choice now.
It's orders of magnitudes simpler than Amazon ... and the UI is much more beautiful too.

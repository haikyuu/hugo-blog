---
title: "Debugging SSH Access"
date: 2020-03-09T11:40:01+01:00
draft: false
tags: 
  - ssh
  - Cloud
---

One of the problems i face when initially trying to access one of my servers hosted in Digital Ocean 
is SSH.

`Permission denied (publickey).`

[This stackoverflow answer](https://askubuntu.com/a/311894/315200) was vey helpful.

1. Run `ssh root@address -vvv` to see full logs
2. configure a section for each server like so 
```
Host SERVERNAME
Hostname ip-or-domain-of-server
User USERNAME
PubKeyAuthentication yes
IdentitiesOnly yes
IdentityFile ./path/to/key
```
3. Use `IdentitiesOnly yes` to limit the authentication to use the single, specified key.

Happy debugging.

---
title: "E2e Tests With Cucumber and Playwright"
date: 2020-02-24T02:01:04+01:00
draft: true
toc: false
images:
tags: 
  - untagged
---

While building my side project, i got frustrated with some bugs.
So i decided to write tests.

And since i am the only developer, i want to extensively test every single feature of my app.
I went to `hapi` docs to see how it's done. But i soon remembered the old times when i
setup e2e tests in a previous job using puppeteer and cucumber.

It was so good ... until we hit the limitations of puppeteer (also because we had some really bad legacy code).

I'd like to give it a shot. But now, with microsoft's playwright.

## First Impressions

Using selectors is way easier since i can choose the engine i want and even combine them.
My favorite is `css` ... you know! You can use it like this `this.page.$('css=#userId')` 

## Thoughts
E2E Test driven development is something i'd like to try.

1. I'll just write all scenarios of my MVP in Cucumber
2. Then i'll write step definitions
3. Write code & revisit step definitions if necessary

It's a great thing for me because i need to write requirements anyway.


## Next

- I want to spin up a separate test env with a separate db and server.
- I want to setup a Github action to do that on each PR
- Record a video of the tests.
- Write a little function that tests graphQL/EdgeQL queries with JSON data
to ensure data is O.K

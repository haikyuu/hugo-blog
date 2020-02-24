---
title: "Exploring Edge db - the next gen database"
draft: false
toc: false
date: 2020-02-28
images:
tags: 
  - Database
  - Backend
  - Paradigm shift
  - Edge-db
---
[Edge-db](https://edgedb.com/) has recently [released the alpha 2](https://edgedb.com/blog/edgedb-1-0-alpha-2)
version of their beautiful database.
It features better GraphQL support (added mutations and other cool stuff) and a new [JavaScript driver](https://github.com/edgedb/edgedb-js).

Unfortunately, the docker setup didn't work in my Mac (High Sierra) ...
Which led me to create an account in Digital Ocean and setup my own linux server
just to try it out.
Turns out it's totally worth it. I created my droplet (Ubuntu) and installed edge-db in minutes.
As a bonus, i get a place where i could deploy my latest side project. More on this in the next post.

The tutorial is straightforward to follow and showcases the power and simplicity of EdgeDB.
To get a taste, just try reading the code. You will understand what's going on!

```ecmascript 6
START TRANSACTION;
## creating our schema
CREATE MIGRATION movies TO {
    module default {
        type Movie {
            required property title -> str;
            # the year of release
            property year -> int64;
            required link director -> Person;
            multi link actors -> Person;
        }
        type Person {
            required property first_name -> str;
            required property last_name -> str;
        }
    }
};

COMMIT MIGRATION movies;

COMMIT;
```
No weird create database stuff, no foreign keys either. Links and multi links ... how expressive is that !!

Wait, there is more ...
```typescript jsx
INSERT Movie {
    title := 'Blade Runner 2049',
    year := 2017,
    director := (
        INSERT Person {
            first_name := 'Denis',
            last_name := 'Villeneuve',
        }
    ),
    actors := {
        (INSERT Person {
            first_name := 'Harrison',
            last_name := 'Ford',
        }),
        (INSERT Person {
            first_name := 'Ryan',
            last_name := 'Gosling',
        }),
        (INSERT Person {
            first_name := 'Ana',
            last_name := 'de Armas',
        }),
    }
};
```
No more inserting actors in the db and then getting their ids and then creating the movie and linking them.
Notice that we have 3 `.then()` in the last sentence ... That's less backend code for you ... for free

Less code `->` Less Bugs 🐛🐞🕷 `+` easier maintainability.

And it doesn't stop here. There is even a [`FOR` loop](https://edgedb.com/docs/edgeql/statements/for#for)!

Wait, there is more.

You get a Graphql API for free!

Spin it up with one command:

```typescript jsx
CONFIGURE SYSTEM INSERT Port {
    protocol := "graphql+http",
    database := "tutorial",
    address := "127.0.0.1",
    port := 8888,
    user := "http",
    concurrency := 1,
};
```

Note that the `concurrency` 1 will work in the smallest Digital Ocean Droplet.
If you're deploying this elsewhere, don't forget to change the address to the server's IP Address.

Now hit `localhost:8888/explore` and see the magic.

## Conclusion

My best discovery for this year is EdgeDB. It's very promising,
and i think it'll beat Prisma and similar tools.
And don't forget, this is an Open Source tool. Spread the ❤️  

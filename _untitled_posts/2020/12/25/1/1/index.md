---
title: null
tags:
  - project:astrid-tech
slug:
  date: 2020-12-26
  ordinal: 0
  name: "1"
date:
  created: 2020-12-25 20:00:00-08:00
  published: 2020-12-25 20:00:00-08:00
---

I attempted to figure out an alternative tagging system for astrid-tech.
Currently, there are 2 types of tags:

1. A tag like `cpp`, which represents a plain old tag.
2. A tag that starts with `/`, like `/projects/astrid-tech`, representing an
   object.

I considered a couple of options to replace this system.

1. **Fully-qualified http[s]:// URI's.** `https://astrid.tech/tags/cpp`,
   `https://astrid.tech/projects/astrid.tech`
2. **Machine tags, in several styles:**
   - **Style 1** `cs:lang=cpp`, `object:project=astrid-tech`
   - **Style 2.** `uses:cslang=cpp`, `mentions:cslang=cpp`,
     `part-of:project=astrid-tech`. This is essentially a graph database. One
     possibly "non-standard" case is `uses:cslang=cpp` and `uses:cslang=python`
     together. Though I suppose this could work like querystrings and
     conglomerate them into an array...
   - **Style 3** `uses:cslang/cpp`, `mentions:cslang/cpp`,
     `part-of:project/astrid-tech`. Machine tags don't require values. I could
     also do something like `uses:cslang/cpp=backend` +
     `uses:cslang/typescript=frontend`.
3. **URN's, with a custom namespace.** `urn:astrid-tech:project:astrid-tech`,
   `urn:astrid-tech:cslang:cpp`
4. **tag: scheme.** `tag:astrid.tech,2020:projects/astrid.tech`

Though the thing is, most of these are essentially my current scheme, but
rehashed to be longer and more absolute, to the point where I could just say
that I'm using relative URI's with my current scheme and call it a day.

You know what? I think I'll do that. Maybe include some machine tags for some of
my posts a la style 3, though.

I'll write a sort of database aggregation thing to read the content directory
and index it, so that it doesn't need to get re-built every single time.

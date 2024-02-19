---
title: null
tags:
  - project:astrid-tech
  - haskell
slug:
  date: 2021-09-25
  ordinal: 0
  name: another-cms-attempt
date:
  created: 2021-09-25 16:04:11-07:00
  published: 2021-09-25 16:04:11-07:00
---

I'm attempting to make a CMS in haskell, _again_. You'd think that given how
poorly it went last time, I'd give up on it, but I guess here we go again.

The plan is for it to be a flat-file-to-API CMS, where you write your flat files
in a Git repo and it imports all of them to an indexed database, then exposes
the content over a GraphQL endpoint.

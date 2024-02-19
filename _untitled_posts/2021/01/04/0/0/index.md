---
title: null
tags:
  - project:infrastructure
slug:
  date: 2021-01-05
  ordinal: 0
  name: "0"
date:
  created: 2021-01-04 20:00:00-08:00
  published: 2021-01-04 20:00:00-08:00
---

I'm self-hosting stuff on my server right now. Not just the backend, but things
like:

- Firefly III to track my finances
- Bookstack wiki to plan out a D&D campaign

All of these services are being reverse proxied by a single Caddy instance to
subdomains under astrid.tech.

I'm planning on setting up the following services next:

- OpenVPN so that the private stuff stays private
- Elasticsearch, Logstash, Kibana (ELK) stack for logging (I may omit Logstash
  because I've heard it's a resource hog) and Filebeat for pushing logs

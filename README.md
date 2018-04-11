Spike to understand how to compose / inherit Dockerfiles.

Context
=======

We want to define sensible defaults in the Dockerfile for foo, but allow
overrides by:

* the Dockerfile for bar
* runtime configuration when running bar

`docker-compose build` will create the images locally. `docker-compose run bar`
and variations thereof are what we are interested in.

The foo application is intended to represent a published image on Docker Hub.
The bar application is intended to represent our local customistations to foo.

In the Dockerfile for foo, we define the protocol to be `https`. We want to
have sensible defaults that work in production, rather than shipping something
with a weakened configuration that would show up on Shodan [1],[2].

It appears like we can't do an override of the `PROTOCOL` environment value in
the bar Dockerfile, so instead we should allow a complete override of the
`FOO_OPTS` value, or do it at runtime.

`docker-compose run -e FOO_OPTS="protocol=http bar" bar`

This suggests that we can define `FOO_OPTS` in the foo Dockerfile and document
what it does. We should also state that extenders/consumers are assumed to know
what they're doing if they feel the need to redefine it.

To be explicit:

We should ship software with a secure default. If you disable that secure
default, it is assumed that you are:

* managing the risk in a different way
* accepting that you are at risk

Decision
========

We should not bother defining `PROTOCOL` in the foo Dockerfile. The layer which
defines the `ENV` for `FOO_OPTS` seems to evaluate it at build time, rather
than at runtime.

Notes
=====

Various combinations of `ENV` and `ARG` have been tried, but couldn't get
`docker-compose run -e PROTOCOL=http bar` or similar to have the desired
effect.

1. https://elweb.co/the-security-footgun-in-etcd/
1. https://thenextweb.com/insider/2017/01/08/mongodb-ransomware-exists-people-bad-security/

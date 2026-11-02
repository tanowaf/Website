+++
title = "Achievement unlocked"
description = "Unexpected effects of working on http parsers"
date = 2026-11-02
+++

The starting goal of this project was to develop a Semantic Firewall for the Docker socket: a way to allow users
to limit access to the Docker daemon by filtering both the requests and responses based on criteria such as
"only allow reading data but not making any changes", "only allow manipulating a restricted set of containers" or
"redact sensitive data from Docker daemon responses".

The communication protocol between Docker clients, such as the `docker` cli command and the daemon is just plain
REST API calls (JSON formatted HTTP requests and responses) over a unix socket ([specs here](https://docs.docker.com/reference/api/engine/)).

![A firewall for docker](/img/blog/firewall_20261102.svg)

Squarely in my ballpark. I thought that the whole thing could be done in a few weeks...

I picked PHP as development language. I chose it because it is the one I am most comfortable with, but also because it is
high-level, well documented, widely deployed and battle tested, and has a plethora of packages available. JSON and HTTP
are supported natively.

Little did I know that this "weekend project" task would turn into a journey chasing the white rabbit down a very,
very long hole.

The enlightening process unfolded along these lines:

1. in order to have an http firewall with filtering of json requests and responses, one has to have an http firewall;
2. an http firewall is nothing more than an http proxy with rules to allow/drop/filter requests and responses;
3. an http proxy is a socket server which can forward an http request to an upstream server and ferry back the response.
   For this, it has to be able to parse the http protocol, fully and correctly;
4. despite PHP being a Web-First development language, it turns out it is not really well suited to implement an http
   proxy, as by default it is not given access to the original, wire-representation of the http it receives from the client;
   instead (most) http fields are pre-parsed and mangled into superglobal variables. Sadly, different webservers have
   different opinions on the exact superglobals to set up and the data to fill them with - and PHP can run on many
   webservers;

   ![A firewall for docker, in PHP](/img/blog/firewall_innards_20261102.svg)

5. a group of standards, known as "PSR" has been developed for PHP, with the aim of making it easy to work with HTTP
   requests and responses, to develop e.g. "middlewares". Multiple implementations exist of those standards, both
   for creating http servers and for creating http clients. That seemed a better building block for the http proxy...
6. ...but it turns out that the PSR abstractions are leaky, or at least unsuitable for this project as they:
   - still do not guarantee that from a "PSR Request" object one will be able to rebuild faithfully the original wire-representation;
   - leave ample space for different implementations of the standards to diverge on many aspects, such as f.e. how to set up
      an http client, and
   - their implementations are often buggy on edge cases, either because they are tested with mock objects instead of
      checking the data that real webservers feed to PHP, or because they focus on handling "proper" HTTP and not the hackish cases
7. still, it seems to make sense to stick to PSR standards, in order to be able to take advantage of existing code libraries
   providing blocks functionality such as caching or routing

At this point, development had snowballed into writing custom implementations of PSR standards, both for an http client
and server, and making sure that those are airtight. As in: behave according to expectations when receiving
well-formed http requests and responses but especially when receiving hack attempts. Request smuggling should not be allowed.
Double `Host` headers should be forbidden. Etc...

Which means writing an extensive test suite. And run it on every webserver that can run PHP and we want to be supported.

And this is how I found my first bug in Nginx (*).

__Achievement unlocked.__

\* = the issue had in fact already been identified, but I failed to find it in the bug tracker, and [reported it again](https://github.com/nginx/nginx/issues/1597)

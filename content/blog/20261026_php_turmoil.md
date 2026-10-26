+++
title = "WAF design and using PHP"
description = "Some clearly good design decisions, some not necessarily so"
date = 2026-09-08
+++

Around the 20th of October, the [PHP2Shell](https://en.wikipedia.org/wiki/PHP2Shell) PHP vulnerability was made known
to the world.

Despite it being a high severity Remote Code Execution flaw, with no requirement for specific php code or php configuration,
and despite the end of the embargo period and coordinated patch release having been slightly botched, the internet
so far is still standing. There are no news of major organizations having been breached.

The key takeaway seems to be that, in the _new AI normal_, it is getting easier and easier to weaponize bugs that have
been lurking openly for a long time, even in the most battle-hardened and widely deployed software.

That basically spells the end of the "if it ain't broke, don't fix it" mentality - from megacorps to the smallest
wordpress sites, there is no alternative to "move fast and patch things".

WAFs can help alleviate the pain associated with upgrading. They become essential to insure the viability of having
legacy or unmaintained applications exposed to the open web, and are the best tools to maintain the safety of all other
apps in the interval between when a vulnerability gets announced and when tested, certified patches are made available.

But not all WAFs are designed equal.

Cloudflare, f.e. is surely to commend for being able to defend a large part of the internet at speed and scale. And
yet, it seems that their WAF had to be "reinvented" in order to be able to handle PHP2Shell.

![Tweets from Cloudflare on Oct 21st, 2026](/img/blog/clouflare_tweets_20261021.png)

The good thing is that this specific design element was in TanoWAF since the start: firewall rules apply filtering
both to the incoming request and to the response from upstream, and the response filter does have full access to
the request.

Of course, TanoWAF is designed for maximum flexibility and ease of development, not for being deployed at scale and
handling millions of requests per second.

Still, it seems that a good design decision was made.

On the other hand, TanoWAF is developed in PHP, and the current preferred candidate for its runtime engine is Swoole.

Which begs the question: is Swoole immune from PHP2Shell? And, is there a long list of PHP RCEs still hiding around
the corner, waiting for the next AI-assisted step in vulnerability discovery?

PHP makes it trivial to avoid memory-management issues in the code it runs, but it is not immune from memory-management
issues in its own codebase. Maybe Rust the only language which should be used nowadays for any kind of web-exposed
development - and that seems to cover 100% of development...

+++
title = "On revoked SSL certificates"
description = "Is PKI at large working as expected, or is it not?"
date = 2026-10-28
+++

Besides the infamous [php2shell vulnerability](https://en.wikipedia.org/wiki/PHP2Shell), which has now achieved undeniably
superstar status - today even my bank alerted me about it! - another issue that made sysadmins spend extra time on
patching and upgrading this month is the discovery of intermediate CA certificates from eMudhra with keys so weak
that  it took all of a couple of minutes to crack them.

This misconfiguration allows an attacker with the private key of any of the compromised intermediate CAs to impersonate
any website or service. In short, it is an enabler for [MITM attacks](https://en.wikipedia.org/wiki/Man-in-the-middle_attack).

The remediation was quite swift and painless, at least at first sight:

- browsers got updates automatically and almost immediately;
- updated root-CA-certs packages were available basically on same day. An `apt-update && apt-upgrade`, followed
  by restarting long-lived processes did the trick.

Or did it?

I maintain a set of applications for a customer. Ths software stack runs on both PHP and Python, within hand-built Docker
containers. The applications do make extensive use of API calls to 3rd party services, from What3Words to Stripe, etc...
A couple of helper applications are set up to ease troubleshooting dev/test environments, running on Perl and Node.js.
A fairly normal setup for a not-too-complicated application, if I was to guess.

The issue at hand is: how to make sure that _all_ the https connections made by the software stack to the outside world
are using the proper, updated CA trust chains - but also that _they are not skipping any of the security features that are
available with TLS_.

I have not (yet) made a 100% accurate inventory of all the software bits that do make http calls to external services,
but, including both container build time and run time, there are for sure at least:
- docker engine (running on the host)
- apt
- curl
- php
- python
- pip (python)
- pdm (python)
- composer (php)
- npm (javascript)
- whatever is used to download and build perl packages
- NextCloud AIO (a black box, can't do anything but trust whoever built those containers)

What pains me is that there seems not to be an obvious, automatable process to verify the multiple possible snags, which
I would broadly group into 3 categories:

1. applications which do not use the root-CA-certs package available in the OS, but bundle their own, needing a separate
   update (Wordpress being [a good example](https://wordpress.org/news/2026/10/wordpress-7-1-4/))
2. applications which do not use existing mechanisms, such as [CRL or OCSP stapling](https://www.thesslstore.com/blog/ocsp-ocsp-stapling-ocsp-must-staple/)
   to check for revoked certificates with close-to-real-time assurance
3. applications which either intentionally or inadvertently disable SSL safety guarantees

Regarding category 1, there are multiple blog posts, such as [this one](https://www.endorlabs.com/learn/emudhra-intermediate-ca-compromise-the-trust-stores-you-patched-and-the-ones-you-didnt-know-you-had),
which highlight the issue and give pointers on possible remediation. Security vulnerability scanners are undoubtedly
carrying an up-to-date list of apps known to bundle outdated CA certs as part of their signatures. A crowdsourced
list of apps/sdks/os still not updated is [available](https://emudhra.we-have-a-problem.com/#developers).

But that does not include any custom, in-house application of course.

Also, in some cases the TLS SDKs/apps behave differently depending on the presence at runtime of CA certs files - they
might use the system bundle if found in a given directory, and the (possibly outdated) bundled list as fallback.

So definitely not too easy to check.

About category 2: there seems to be no way, as of today, to enforce checks for certificate revocation in a consistent manner,
making sure CRLs can be managed centrally and updated once for all installed applications (*= on linux). Curl/Libcurl f.e.
does not seem to support CRL checking as a global option, only as a per-invocation option.

The good news is that there is an effort underway to fix that: [upki](https://jnsgr.uk/2025/12/addressing-linuxs-missing-pki-infra/).
It remains to be seen if that will work out as planned, how widely it will be adopted and how far back it will be backported
into older OS and applications.

Verdict: wait and see...

Category 3 seems to be the most tricky one, as the only way to make sure applications are coded safely is probably to
pour over their source code.

F.e., in PHP there are 2 APIs for making https calls, and options such as `CURLOPT_SSL_VERIFYHOST=false` allow developers
to remove most of the safety guarantees. I am not sure about the equivalent situation for Python or Javascript code.

Verdict: let an AI scan for those kind of mishaps?

__The TL;DR of this seems to be:__

it is way past time to weave certificate revocation checks into every app/library that makes https calls, and reduce as
much as possible the number of apps which bundle their own lists of CA roots;

SBOMs might not be sufficient to ascertain when a given application is safe - there are way too many details they
do not cover, and vulnerabilities might arise depending on presence of certain configuration files at runtime

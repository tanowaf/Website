+++
title = "Tano WAF"
description = "Web API Firewalls and other assorted HTTP Proxies."
#[extra]
#home_layout = "wide"
#home_eyebrow = "Acme documentation"
#home_primary_action_label = "Read the docs"
#home_primary_action_path = "/docs/"
#home_features = [
#    { kicker = "Start", title = "Install Acme", description = "Get a local environment running." },
#    { kicker = "Build", title = "Use the project", description = "Follow focused guides for everyday work." },
#    { kicker = "Reference", title = "Find exact answers", description = "Look up configuration and behavior." },
#]
+++

This project is a Work In Progress.

The MVP stage will consist in having a working Semantic WAF for the Docker daemon control socket, i.e. a firewall
able not only to limit the commands which can be sent to Docker, but also to filter the results of said commands.

The prime example for its use would be allowing access to the Docker Socket to an app / account which should be allowed
to start/stop/manage a given set of containers, but not be allowed to act or even list any other container on the same
host.

You can follow ongoing development in the Git repositories:

- [https://github.com/tanowaf/waf-core](https://github.com/tanowaf/waf-core)
- [https://github.com/tanowaf/Yet-Another-Docker-Socket-Proxy](https://github.com/tanowaf/Yet-Another-Docker-Socket-Proxy)

Or head on to the latest [news](@/blog/_index.md)

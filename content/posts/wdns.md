+++

Description = "wildcard DNS for any IP address"
Tags = ["wdns", "golang"]
date = "2016-10-09T01:20:22-07:00"
title = "wdns"

+++

This is fun project for the weekend; a simple DNS relay server that can
do wildcard DNS resolution  for IP Addresses. Like xip.io, but with a small change.
This supports hyphens in addition to dots.

<!--more-->

Examples

```
  192-168-1-1.int.example.com resolves to 192.168.1.1
  192.168.1.1.int.example.com to resolves 192.168.1.1
  foo.10-1-2-3.int.example.com to resolves 10.1.2.3
```

There are quite a few services which do this like xip.io, I just wanted something
that one can use for other domains. It's fairly simple to use. Just deploy and
delegate this as nameserver for the subdomain you are running this for, and it
will serve only those requests. It can also relay requests for requests about
other domains, so you can use it in your LAN too.

Code is available at [github.com/karteek/wdns](https://github.com/karteek/wdns)

---
title: Configuring mDNS for OpenIndiana
cover: mdns-indiana.png
description: Resolving hostnames on your local network on OpenIndiana
---

<figure>
<a
    class="example-image-link"
    href="/images/mdns-indiana.png"
    data-lightbox="image-1">
<img
    src="/images/mdns-indiana.png"
    alt="mDNS works in Hipster 2016.04"
    width="497"
    height="324"
/>
</a>
</figure>

OpenIndiana is a descendant of [Illumos](), itself a fork of [OpenSolaris](). Not sure if their package and control systems were convergent at some point (it's been a long time since I've touched SunOS), but configuring mDNS was considerably different from [Solaris 11](2016-07-14-mdns-solaris.html).  Like Solaris 11, mDNS is not enabled by default.  There are fewer steps, but unfortunately what [little documentation exists](http://www.machine-unix.com/mdns-on-openindiana-151a/) is scattered, makes a lot of assumptions and is sorely out of date.

I'm running Hipster 2016.04.  Assuming a fresh install:

1. Install the mdns package.
    ```
    $ sudo pkg install pkg:/service/network/dns/mdns
    ```

1. Check to see if `hosts` and `ipnodes` in `/etc/nsswitch.conf` are properly configured.  Mine were on installation. They should look like this:
    ```
    ~/ cat /etc/nsswitch.conf | egrep '^hosts|^ipnodes'
    hosts:      files dns mdns
    ipnodes:   files dns mdns
    ```

1. At this point, we can check to see if the multicast service is enabled and running.  It was disabled for me first time through:
    ```
    ~/ svcs -a | grep multicast
    online         21:53:00 svc:/network/dns/multicast:default
    ```

1. Let's enable multicast and start the service
    ```
    $ sudo svcadm enable multicast
    $ sudo svcadm restart multicast
    ```


1. And finally, let's test it out.
    ```
    $ getent hosts ticonderoga.local
    10.0.40.101 ticonderoga.local.

    $ ping ticonderoga.local
    ticonderoga.local is alive
    ```

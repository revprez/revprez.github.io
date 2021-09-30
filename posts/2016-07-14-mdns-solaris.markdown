---
title: Configuring mDNS for Solaris
cover: mdns-indiana.png
description: Resolving hostnames on your local network on Solaris 11
---

<figure>
<a
    class="example-image-link"
    href="/images/mdns-solaris-02.png"
    data-lightbox="image-1">
<img
    src="/images/mdns-solaris-02.png"
    alt="mDNS works in Solaris 11"
    width="497"
    height="324"
/>
</a>
</figure>

Configuring mDNS for Solaris is pretty [straight forward and well documented](https://docs.oracle.com/cd/E36784_01/html/E36831/dnsref-39.html), unlike the procedure for [OpenIndiana](2016-07-14-mdns-solaris.html). I'm running Solaris 11.3, and as always assuming a fresh install:

1. Install the mdns package.
    ```
    $ sudo pkg install pkg:/service/network/dns/mdns
    ```

1. Configure NSS
    ```
    $ /usr/sbin/svccfg -s svc:/system/name-service/switch

    ...

    svc:/system/name-service/switch> setprop config/host = astring: "files dns mdns"
    svc:/system/name-service/switch> select system/name-service/switch:default
    svc:/system/name-service/switch:default> refresh
    svc:/system/name-service/switch> quit
    ```

1. Enable and start the mDNS service
    ```
    $ svcadm enable svc:/network/dns/multicast:default
    ```

1. And finally, let's test it out.
    ```
    $ getent hosts ticonderoga.local
    10.0.40.101 ticonderoga.local.

    $ ping ticonderoga.local
    ticonderoga.local is alive
    ```

That's it.

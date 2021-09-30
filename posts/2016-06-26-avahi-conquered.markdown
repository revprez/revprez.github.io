---
title: "Avahi Conquered: Configuring multicast DNS in FreeBSD"
cover: "avahi-freebsd.png"
description: "Resolving hostnames on your local network on FreeBSD 10.3"
---

`/etc/hosts` is for losers. [Zeroconf](https://en.wikipedia.org/wiki/Zero-configuration_networking) is your friend.  It's fine when it works out of the box.  It sucks when it doesn't, especially if you have no idea what Zeroconf is, or what [Bonjour (specifically mDNSResponder)](https://en.wikipedia.org/wiki/Bonjour_(software)) does, or you've never heard of Avahi.

This was a bit of a pain since I wanted to do it the package way, but seems no one has bothered to jot down all the steps in one place.  Here's how I did it.

Immediately after a fresh install of [FreeBSD 10.3](https://www.freebsd.org/releases/10.3R/relnotes.html), I su'd to root:

## Install some packages

```
$ su -
$ pkg install vim   # or your favorite editor

...

# Some of this might be redundant
$ pkg install hal dbus avahi avahi-app avahi-libdns avahi-autoipd nss_mdns

...

$ sysrc dbus_enable=YES
$ sysrc hald_enable=YES
$ sysrc avahi_daemon_enable=YES

```

## Add mdns to the host line in /etc/nsswitch.conf

#### Before
```
hosts: files dns
```

#### After
```
hosts: files dns mdns
```

## Reboot (or, if you'd like, just start the daemons)

```
$ /usr/local/etc/rc.d/dbus start && \
    /usr/local/etc/rc.d/avahi-daemon start && \
    /usr/local/etc/rc.d/netatalk start
```

## Test it out

```
$ ping <some machine on your LAN>.local
PING <some machine on your LAN>.local (<its addy>): 56 data bytes
64 bytes from <its addy>: icmp_seq=0 ttl=64 time=4.523 ms
64 bytes from <its addy>: icmp_seq=1 ttl=64 time=5.749 ms
64 bytes from <its addy>: icmp_seq=2 ttl=64 time=2.652 ms
64 bytes from <its addy>: icmp_seq=3 ttl=64 time=85.265 ms
```

<figure>
<a
    class="example-image-link"
    href="/images/avahi-freebsd.png"
    data-lightbox="image-1">
<img
    src="/images/avahi-freebsd.png"
    alt="chuckle"
    width="476"
    height="383"
/>
</a>
</figure>

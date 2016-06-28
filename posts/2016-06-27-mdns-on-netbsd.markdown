---
title: mDNS on a clean NetBSD 7.0.1 install
---

Also not very well documented.  There's [some confusion](https://mail-index.netbsd.org/current-users/2009/12/05/msg011545.html) as to what should go into [nsswitch.conf](http://netbsd.gw.com/cgi-bin/man-cgi?nsswitch.conf+5+NetBSD-1.4.3), but fortunately [\@RadoslawKujawa](https://twitter.com/RadoslawKujawa) cuts through the crap.  

Assuming you've just installed NetBSD for the first time, log in as root and

1. **Enable the multicast and unicast DNS daemon: [mdnsd(8)](http://netbsd.gw.com/cgi-bin/man-cgi?mdnsd++NetBSD-current)**
    ```
    $ echo "mdnsd=YES" >> /etc/rc.conf 
    ```

1. **Add a source named "msdnsd" to the hosts database in [nsswitch.conf(5)](http://netbsd.gw.com/cgi-bin/man-cgi?nsswitch.conf++NetBSD-current)**
    ```
    $ sed 's|^\(hosts.*\)files dns|\1files dns mdnsd|g' /etc/nsswitch.conf
    ```

1. **Restart [mdnsd(8)](http://netbsd.gw.com/cgi-bin/man-cgi?mdnsd++NetBSD-current)**
    ```
    $ /etc/rc.d/mdns restart
    ```

1. **Test**
    ```
    $ getent hosts <another mdns responding host on your lan>.local
    $ ping another mdns responding host on your lan>.local
    ```

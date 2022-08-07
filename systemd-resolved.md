# systemd-resolved

### Use systemd-resolved as the system resolver

First make sure you're using resolved as your system resolver. That is we want
(g)libc to use systemd-resolved rather than doing (unencrypted) DNS queries
directly when calling the standard `gethostbyname` and related functions.
This is done with one of the following lines in `/etc/nsswitch.conf`:

```
# use systemd-resolved only
hosts:	resolve

# fall back to doing unencrypted DNS queries directly if resolved is not available
hosts:	resolve [!UNAVAIL=return] files dns

# older versions of resolved don't parse /etc/hosts, so let glibc look there first
hosts:	files resolve

# ..and if you want to fall back to doing DNS queries directly
hosts:	files resolve [!UNAVAIL=return] dns
```

For this to work we need the `resolve` NSS plugin that comes with
systemd-resolved. Oddly some distributions doesn't package it with the
systemd-resolved daemon itself. Eg. on Debian you'll need
```sh
apt-get install libnss-resolve
```

Some (misbehaving) programs don't use the regular libc calls for resolving
host names, but instead parse `/etc/resolv.conf` and expect to find a DNS
server to do its own (unencrypted) queries against. Obviously this completely
bypasses the settings in `/etc/nsswitch.conf` above. NGINX is one such program.
For this reason systemd-resolved by default listens on the local address
127.0.0.53, so you can add the following line to `/etc/resolv.conf`
```
nameserver 127.0.0.53
```
Now such programs will send their unencrypted DNS queries to systemd-resolved
on the loopback interface instead.

The package containing systemd-resolved usually comes with a static
`resolv.conf` for this purpose, so a better solution is to just symlink that
```sh
ln -s /usr/lib/systemd/resolv.conf /etc/resolv.conf
```

### Use UncensoredDNS
#### As a fallback server

The systemd-resolved server has the concept of a fallback DNS server. This
server is tried when no other DNS server is configured (eg. through DHCP) and
reachable. This is set in `/etc/systemd/resolved.conf`. You'll also need to
enable the DNSOverTLS option here. Use `opportunistic` to let systemd-resolved
fall back to unencrypted connections or `true` to force all DNS server connections to use
TLS. Eg.
```ini
[Resolve]
...
FallbackDNS=91.239.100.100#anycast.censurfridns.dk 89.233.43.71#unicast.censurfridns.dk
DNSOverTLS=opportunistic
...
```

#### Always

You can also set up systemd-resolved to always use UncensoredDNS on all
interfaces. One way to do it is to set the DNS option in
`/etc/systemd/resolved.conf`:

```ini
[Resolve]
...
DNS=91.239.100.100#anycast.censurfridns.dk 89.233.43.71#unicast.censurfridns.dk
DNSOverTLS=true
...
```

If you're using systemd-networkd or another network manager that is able to
talk to systemd-resolved make sure to configure it to not use per interface DNS
servers acquired through DHCP. With systemd-networkd you can set the
DNSDefaultRoute option in the relevant .network file:
```ini
[Network]
...
DNSDefaultRoute=false
...
```

### Management and debugging

Recent versions of systemd-resolved comes with the `resolvectl` tool to query
and control the daemon. In older versions this was called `systemd-resolve` and
had slightly different behaviour and options.

Running just `resolvectl` is similar to `resolvectl status` and shows all
configured per interface, global and fallback DNS servers.

```
$ resolvectl
Global
           Protocols: +LLMNR +mDNS +DNSOverTLS DNSSEC=allow-downgrade/supported
    resolv.conf mode: static
Fallback DNS Servers: 91.239.100.100#anycast.censurfridns.dk 89.233.43.71#unicast.censurfridns.dk

Link 24 (wlan0)
    Current Scopes: DNS LLMNR/IPv4 LLMNR/IPv6 mDNS/IPv4 mDNS/IPv6
         Protocols: -DefaultRoute LLMNR=resolve mDNS=resolve +DNSOverTLS DNSSEC=allow-downgrade/supported
Current DNS Server: 192.0.2.6
       DNS Servers: 192.0.2.6 2001:db8::6
        DNS Domain: camp.bornhack.dk
...
```
Note that `Protocols: -DefaultRoute ...` means that any DNS server configured for this
interface is only used to query subdomains of the ones listed under `DNS
Domain:`. This way you can set up systemd-resolved to only query the DNS
servers acquired through DHCP on your corporate network for certain domains and
use UncensoredDNS for the rest.

Many distributions also has a `systemd-resolvconf` package or an alternatives
mechanism that will set up a symlink to `resolvectl` which allows it to be used
as a drop in replacement for the `resolvconf` tool. This is useful when your
favourite network manager doesn't know how to talk directly to systemd-resolved
and add per interface DNS servers.

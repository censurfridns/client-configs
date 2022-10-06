# dnscrypt-proxy

[dnscrypt-proxy](https://github.com/DNSCrypt/dnscrypt-proxy) is a DNS proxy supporting
[DNSCrypt v2](https://dnscrypt.info/protocol),
[DNS-over-HTTPS](https://www.rfc-editor.org/rfc/rfc8484.txt),
[Anonymized DNSCrypt](https://github.com/DNSCrypt/dnscrypt-protocol/blob/master/ANONYMIZED-DNSCRYPT.txt)
 and
[DoH](https://github.com/DNSCrypt/dnscrypt-resolvers/blob/master/v3/odoh-servers.md)
 protocols.

For more information check this [guide](https://wiki.archlinux.org/title/Dnscrypt-proxy).


After installing in `/etc/dnscrypt-proxy/dnscrypt-proxy.toml`
configuration uncomment the following line or set your on servers:
```
server_names = ['scaleway-fr', 'google', 'yandex', 'cloudflare']
```


dnscrypt-proxy will start a DNS server running on port 53.

You may have to stop and disable `systemd-resolved` and change
`/etc/resolv.conf` to look like this:
```
nameserver ::1
nameserver 127.0.0.1
options trust-ad
```


You can check that everything worked by doing:
```
$ dig @localhost bornhack.dk
...
;; ANSWER SECTION:
bornhack.dk.            2183    IN      A       85.209.118.162

```


## dnsmaq and dnscrypt-proxy

A use case for using [dnsmasq](https://wiki.archlinux.org/title/dnsmasq) is when
setting a Raspberry Pi router/home-server using hostapd to create a software
access point. dnsmasq will perform as a DHCP and DNS server for the clients.
A good place to get started is this
[guide](https://wiki.archlinux.org/title/software_access_point).


To use dnsmasq with dnscrypt-proxy
in `/etc/dnscrypt-proxy/dnscrypt-proxy.toml`:
```
listen_addresses = ['127.0.0.1:5300']
```

and set dnsmasq upstream server to dnscrypt-proxy address in `/etc/dnsmasq.conf`:
```
server=127.0.0.1#5300
```

# Enabling Uncensored DNS over HTTPS in OpenWrt

This is tested in OpenWrt 22.03, but probably works in other versions
as well.


## Install necessary packages

Install the package(s) `https-dns-proxy` and optionally
`luci-app-https-dns-proxy` using your preferred method (e.g. by SSH'ing
into the router or using the web interface).

The luci package will allow you to change some settings via the web
interface. Unfortunately, it only allows you to select predefined DoH
providers.

You can either
- Add Uncensored DNS to the list of predefined DoH providers in the LuCi web interface, or
- Edit the configuration file.


## Add Uncensored DNS to the list of predefined DoH providers in the web interface

SSH into the router and add the file `/usr/lib/lua/luci/https-dns-proxy/providers/org.uncensoreddns.anycast.lua` with contents similar to

```
return {
	name = "anycast.uncensoreddns.org",
	label = _("Uncensored DNS (anycast)"),
	resolver_url = "https://anycast.uncensoreddns.org/dns-query",
	bootstrap_dns = "208.67.222.222,208.67.220.220",
	help_link = "https://blog.uncensoreddns.org/dns-servers/",
	help_link_text = "uncensoreddns.org"
}
```

and add the file `/usr/lib/lua/luci/https-dns-proxy/providers/org.uncensoreddns.unicast.lua` with contents similar to

```
return {
	name = "unicast.uncensoreddns.org",
	label = _("Uncensored DNS (unicast)"),
	resolver_url = "https://unicast.uncensoreddns.org/dns-query",
	bootstrap_dns = "208.67.222.222,208.67.220.220",
	help_link = "https://blog.uncensoreddns.org/dns-servers/",
	help_link_text = "uncensoreddns.org"
}
```

Now you can select the Uncensored DNS DoH provider in the LuCi web interface.

Please note that the _OpenDNS_ DNS servers are used for bootstrapping in
this example. These are only used for the initial unencrypted lookup of
the DNS records for the resolvers. You can change this to something
else, if you prefer another provider.


## Edit the configuration file

You can also SSH into the router and edit the file `/etc/config/https-dns-proxy`

An example configuration could look like this:

```
config main 'config'
        option update_dnsmasq_config '*'
        option force_dns '1'
        list force_dns_port '53'
        list force_dns_port '853'

config https-dns-proxy
        option bootstrap_dns '208.67.222.222,208.67.220.220'
        option resolver_url 'https://anycast.uncensoreddns.org/dns-query'

config https-dns-proxy
        option bootstrap_dns '208.67.222.222,208.67.220.220'
        option resolver_url 'https://unicast.uncensoreddns.org/dns-query'
```

Please note that the _OpenDNS_ DNS servers are used for bootstrapping in
this example. These are only used for the initial unencrypted lookup of
the DNS records for the resolvers. You can change this to something
else, if you prefer another provider.

Save the file and reload the `https-dns-proxy` service. If all goes
well, this is all you need to do.


### Warning about the _DNS HTTPS Proxy_ service page in the web interface

After reloading the service, the Uncensored DNS servers will be listed
as "Unknown Provider DoH" under _Service Status_. Even worse, the 2
editable entries under _Instances_ will be listed as "AhaDNS" or
whatever happens to be the first provider in your built in list of
providers.

Do not press the _Save & Apply_ button, as this will overwrite your
configuration with the servers that are selected under _Instances_. If
you do it anyway, you need to go back and edit the configuration by
hand.

![Screenshot of the DNS HTTPS Proxy service page in the OpenWrt web interface](screenshots/openwrt-luci-doh.png?raw=true "Screenshot of the DNS HTTPS Proxy service page in the OpenWrt web interface")

# openwrt-dirtydns

DirtyDNS is an DNS forwarder written in Shell script.

## Usage

DirtyDNS requires a dirty server and a secured server to work.

DNS forwarding is a traditional way to avoid DNS pollution.
However, forwarding all DNS query through a secured upstream (e.g. SSH tunnel/shadowsocks tunnel)
breaks DNS-based load balance policy of content provider.
DirtyDNS checks responses from dirty server against an IP whitelist (require [ipset](http://ipset.netfilter.org/)).
Query is forwarded to secured upstream when dirty response does not match the whitelist, or a dirty response is responded.

![sequence](www/sequence.png?raw=true)

For example, using following whitelist (shapes represent for unique IP addresses):

![whitelist](www/whitelist.png?raw=true)

| Query   | Response                                                            |
|---------|---------------------------------------------------------------------|
| Query 1 | Dirty response is chosen (matches whitelist).                       |
| Query 2 | Dirty response is ignored and query is forwarded to secured server. |
| Query 3 | Both responses match whitelist. Dirty result (optimal) is chosen.   |
| Query 4 | Dirty server fails and query is forwarded to secured server.        |

DirtyDNS can be configured in UCI:

| Option           | Description                                   |
|------------------|-----------------------------------------------|
| port             | UDP port to listen (default: 5353)            |
| dirty_upstream   | dirty name server (default: 8.8.8.8:53)       |
| secured_upstream | secured name server (default: 127.0.0.1:5300) |
| ipset_dirty      | name of an ipset (default: ss_spec_dst_bp)    |

Default configuration is compatible with
[openwrt-shadowsocks](https://github.com/shadowsocks/openwrt-shadowsocks) ("transparent proxy" and "port forward" should be enabled).

## Installation

```
# package
ssh root@192.168.1.1 opkg install rsync
git clone https://github.com/kamikat/openwrt-dirtydns
rsync -arP openwrt-dirtydns/pkg/ /

# dependencies
ssh root@192.168.1.1
opkg install socat
opkg install ipset kmod-ipt-ipset

# setup service
/etc/init.d/dirtydns enable
/etc/init.d/dirtydns start

# setup dnsmasq
dhcp.@dnsmasq[0].noresolv='1'
dhcp.@dnsmasq[0].server='127.0.0.1#5353'
```

## License

(The MIT License)


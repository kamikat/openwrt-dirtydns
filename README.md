# openwrt-dirtydns

DirtyDNS is an DNS forwarder written in Shell script.

## Usage

DirtyDNS requires a dirty server and a secured server to work.

A record from dirty server is tested against a IP whitelist,
the dirty result is adopted when matches or DirtyDNS forward query to secured server.

DirtyDNS can be configured in UCI:

| option           | description                                   |
|------------------|-----------------------------------------------|
| port             | UDP port to listen (default: 5353)            |
| dirty_upstream   | dirty name server (default: 8.8.8.8:53)       |
| secured_upstream | secured name server (default: 127.0.0.1:5300) |
| ipset_dirty      | name of an ipset (default: ss_spec_dst_bp)    |

NOTE: default configuration is compatible with
[luci-app-shadowsocks](https://github.com/shadowsocks/luci-app-shadowsocks)

## Install

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


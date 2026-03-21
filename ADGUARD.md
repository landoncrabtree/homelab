# Adguard Home Configuration / Documentation

1. Enable the Adguard Home service on the router.

2. Configure upstream DNS servers. I prefer to use DNS-over-HTTPS (DoH) servers with Quad9 and Cloudflare.

```bash
https://dns.quad9.net/dns-query
https://dns.cloudflare.com/dns-query
[/lan/]192.168.8.1:53
[/tail812ed.ts.net/]100.100.100.100
```

This ensures `.lan` hostnames will be routed back to dnsmasq (and uses DHCP lease file for resolution) and `.ts.net` hostnames will be routed back to Tailscale MagicDNS resolver.

3. Configure bootstrap DNS servers. These are the servers that Adguard Home will use to resolve DoH requests.

```bash
9.9.9.9
149.112.112.9
1.1.1.1
1.0.0.1
```

4. Configure Private DNS to point to `192.168.8.1:53` which allows AGH to resolve hostnames. 

5. Enable DNSSEC validation.

6. Configure `eth1` (WAN) interface to use `127.0.0.1` as the DNS server. This ensures that all router DNS requests are handled using our upstreams, rather than ISP defaults.

## DNS Hijacking

To prevent DNS hijacking, we add custom firewall rules <http://192.168.8.1:8080/cgi-bin/luci/admin/network/firewall/custom>. This will redirect all `:53` (dnsmasq) to `:3053` (AGH).

```bash
iptables -t nat -A PREROUTING -i br-lan -p tcp --dport 53 -j DNAT --to-destination 192.168.8.1:3053
iptables -t nat -A PREROUTING -i br-lan -p udp --dport 53 -j DNAT --to-destination 192.168.8.1:3053

iptables -t nat -A PREROUTING -i tailscale0 -p tcp --dport 53 -j DNAT --to-destination 192.168.8.1:3053
iptables -t nat -A PREROUTING -i tailscale0 -p udp --dport 53 -j DNAT --to-destination 192.168.8.1:3053
```

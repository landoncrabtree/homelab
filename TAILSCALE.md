# Tailscale Configuration / Documentation

Tailscale is used to connect all devices to the network. We simply enable it on the router and then add the devices to the Tailscale dashboard. Then, we can connect client devices (e.g. Macbook) and act as if we're on our home network: 192.168.8.X works remotely.

1. Enable Tailscale on the router.

2. Add the devices to the Tailscale dashboard.

3. Ensure dnsmasq binds to `tailscale0` interface. <http://192.168.8.1:8080/cgi-bin/luci/admin/network/dhcp> Add `tailscale0` and `br-lan` to the list of listening interfaces.

4. Configure DNS Override <https://login.tailscale.com/admin/dns> to point to the Tailscale IP address of the router (e.g. `100.69.217.17`). This ensures all devices connected to Tailscale will use our AGH server for DNS resolution.

5. Configure Search Domains <https://login.tailscale.com/admin/dns> to include `lan`.

6. Ensure 'Local domain' <http://192.168.8.1:8080/cgi-bin/luci/admin/network/dhcp> is just set to `lan` (Tailscale integration seems to overwrite this, so restore it.)
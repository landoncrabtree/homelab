# Landon's Homelab

This repository is a collection of assets for my homelab. Configurations, documentation, and Docker compose files for different services.

## Network Architecture

I have a GL-MT6000 (Flint2) router running Tailscale and Adguard Home. It is connected to my ISP's BGW21 modem/router and connected in bridged mode. 

This requires some specific configuration of OpenWRT, Adguard, and Tailscale to work properly. See [TAILSCALE.md](TAILSCALE.md) and [ADGUARD.md](ADGUARD.md) for more details on these configurations. 

Once properly configured, the network architecture should look like this:

```
┌─────────────────────────────────────────────────────────────────────┐
│                        LAN Clients (br-lan)                        │
│                                                                     │
│  DNS query (:53) ──► DNAT (iptables) ──► AdGuard Home (:3053)      │
│                                              │                      │
│                              ┌───────────────┼───────────────┐      │
│                              │               │               │      │
│                          .lan queries   .ts.net queries   All other  │
│                              │               │               │      │
│                              ▼               ▼               ▼      │
│                        dnsmasq (:53)   Tailscale DNS    Quad9/CF    │
│                        (DHCP leases)  (100.100.100.100) (DoH)      │
└─────────────────────────────────────────────────────────────────────┘

┌─────────────────────────────────────────────────────────────────────┐
│                     Tailscale Clients (tailscale0)                  │
│                                                                     │
│  DNS query (:53) ──► DNAT (iptables) ──► AdGuard Home (:3053)      │
│                              │                                      │
│                        (same resolution as LAN clients)             │
└─────────────────────────────────────────────────────────────────────┘

┌─────────────────────────────────────────────────────────────────────┐
│                     Router-originated DNS                           │
│                                                                     │
│  DNS query ──► resolv.conf (127.0.0.1) ──► dnsmasq (:53)          │
│                                                │                    │
│                                        AdGuard Home (:3053)        │
│                                                │                    │
│                                          Quad9/CF (DoH)            │
└─────────────────────────────────────────────────────────────────────┘
```

This flow ensures a few things:
1) All local devices have DNS resolution via DoH using Quad9/CF (better privacy than ISP DNS).
2) All local devices have built-in ad-blocking (less tracking).
3) All local devices have DNSSEC validation (more secure).
4) All local devices can resolve `.lan` hostnames (e.g. `homeassistant.lan`) (easier to memorize).
5) We can connect remotely via Tailscale and get all the above benefits (same DNS resolution, ad-blocking, DNSSEC validation, etc.).

## Devices

- [Raspberry Pi 4](https://www.raspberrypi.com/products/raspberry-pi-4-model-b/) - Running HomeAssistant.
- [GL-MT6000](https://store-us.gl-inet.com/products/flint-2-gl-mt6000-wi-fi-6-high-performance-home-router) - Flint2 router running Tailscale and Adguard Home.
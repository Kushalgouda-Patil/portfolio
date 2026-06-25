---
title: "Network Address Translation (NAT)"
description: "The quiet trick that kept the internet alive when the world ran out of addresses."
publishDate: "2026-05-14"
tags: ["networking", "cloud", "computer-science", "technology"]
---

*The quiet trick that kept the internet alive when the world ran out of addresses.*

## The Problem NAT Solves

When IPv4 was designed, 4.3 billion addresses seemed infinite. It wasn't. As the internet exploded through the 1990s, it became obvious the world would run out of public IP addresses well before everyone was done connecting to it.

NAT was the elegant stopgap. Instead of assigning a unique public IP to every device, we assign one to the router and let every device behind it share it.

## Private vs Public IPs: The Foundation

**Private IP addresses** are reserved ranges that anyone can use internally — `192.168.0.0/16`, `10.0.0.0/8`, and `172.16.0.0/12`. They are free, reusable, and invisible to the public internet.

**Public IP addresses** are globally unique, assigned by ISPs, and routable across the internet. Your ISP gives your router one of these — just one.

NAT lives at the boundary between these two worlds.

## How NAT Actually Works

When your laptop wants to load a webpage, the router acts as a translator standing between your private network and the public internet. Here is the flow:

1. Your laptop (`192.168.1.5`) sends a request to Google (`142.250.80.46`).
2. The router intercepts the packet and replaces the private IP with its public IP (`203.0.113.10`), assigning it a port number to track the connection.
3. The request goes out to the internet looking like it came from the router.
4. Google responds to the router's public IP.
5. The router checks its table, finds who originally made that request, and forwards the response to your laptop.

## The NAT Translation Table

| Private IP | Private Port | Public IP | Public Port | Destination |
|---|---|---|---|---|
| 192.168.1.5 | 52341 | 203.0.113.10 | 10001 | 142.250.80.46 (Google) |
| 192.168.1.8 | 49201 | 203.0.113.10 | 10002 | 104.244.42.1 (x.com) |
| 192.168.1.12 | 61345 | 203.0.113.10 | 10003 | 13.107.42.14 (Microsoft) |

## Types of NAT

| Type | How it works | Real world use |
|---|---|---|
| Static NAT | One private IP maps to one fixed public IP | Hosting a server that needs a permanent address |
| Dynamic NAT | Private IPs draw from a pool of public IPs | Offices with a small block of public IPs |
| PAT (NAT Overload) | Many private IPs share one public IP via ports | Your home router — the most common type |

## The Tradeoffs

- **Inbound connections are hard.** The router only creates table entries when your device initiates a connection. If an outside machine tries to reach a device inside your network, there is no entry to match it — the packet gets dropped. This is why hosting a game server or using peer-to-peer apps often requires port forwarding.

- **It breaks the end-to-end principle.** The internet was originally designed so any device could talk directly to any other. NAT puts a middleman in the way, which complicates VoIP, gaming, and file sharing.
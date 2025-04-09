# VPN Setup and Configuration Guide

This README documents the process of setting up and understanding an OpenVPN server, including networking concepts, iptables rules, routing, and use cases like bypassing censorship. 
It’s based on real-world troubleshooting and discussions, tailored for a setup running on Ubuntu.

## Table of Contents

Overview (#overview)

Network Configuration (#network-configuration)

OpenVPN Server Setup (#openvpn-server-setup)

Routing and NAT (#routing-and-nat)

Split Tunneling vs Full Tunneling (#split-tunneling-vs-full-tunneling)

Bypassing Censorship (#bypassing-censorship)

How the VPN Tunnel Works (#how-the-vpn-tunnel-works)

Key Takeaways (#key-takeaways)

Testing and Troubleshooting (#testing-and-troubleshooting)

Overview
This guide covers setting up an OpenVPN server to:
Connect to an internal network (e.g., 10.20.20.0/24).

Optionally route all internet traffic through the VPN.

Understand the flow of packets, NAT, and routing in a VPN setup.

The server has two interfaces:

ens192: Internal network (10.20.20.22/24).
ens224: Public internet (70.50.50.30/29).
The VPN tunnel uses the 10.8.0.0/24 range, with the server at 10.8.0.1 and clients (e.g., laptop) at 10.8.0.6.


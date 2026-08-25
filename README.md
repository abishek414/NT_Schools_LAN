# NT Schools Network - Multi-Site WAN (GRE over IPsec)

## Background

NT Schools Network Authority operates a Head Office plus two remote school
campuses across the Northern Territory. The sites need to share internal
resources (staff systems, servers, department data) securely, but leased
lines between campuses are too costly to justify. Instead, each site
connects to the public internet through its own ISP link, and a GRE-over-
IPsec site-to-site VPN is used to give the three sites private, encrypted
connectivity across that public network - while each site still uses its
own connection for normal internet access.

This Packet Tracer project (`NT_Schools_LAN.pkt`) designs and configures
that network end to end.

## Objectives

This project demonstrates:

- Multi-site WAN design over a simulated public internet (3-router ISP core)
- GRE tunneling for inter-site connectivity that supports dynamic routing
- IPsec (ISAKMP/IKE + ESP) to encrypt and authenticate the GRE tunnels
- A clean separation between underlay routing (ISP reachability) and
  overlay routing (inter-site LAN reachability, carried only over the tunnels)
- NAT/PAT so internet-bound traffic is translated while inter-site traffic
  is explicitly exempted and travels the encrypted tunnel instead
- VLAN segmentation and inter-VLAN routing (multiple department VLANs at
  HQ; Staff/Student VLANs at each school)
- Router- and switch-based DHCP and DNS
- Extended/named ACLs for inter-VLAN and inter-site traffic control
- Device hardening: SSH-only management, local AAA, port security
- Redundancy: EtherChannel between distribution switches, multiple
  routers/links at branch sites, a meshed 3-router ISP core

## Topology

**Shared ISP Core** - three routers meshed together in the middle,
simulating the public internet. They run their own routing (OSPF) purely
to reach each site's public IP; they have no visibility into any site's
internal VLANs or address space.

**Head Office (HQ)** - a two-tier switched design: two Layer 3 distribution
switches interconnected (EtherChannel) for resiliency, four access
switches dual-homed into the distribution pair, and one edge router
providing the GRE/IPsec/NAT gateway out to the ISP core. Hosts multiple
department VLANs (Admin, HR, Finance/IT, Servers, Guest, etc.).

**School Campus A** - three routers for internal path redundancy plus two
access switches linked by EtherChannel; one router is the WAN edge running
GRE/IPsec back to HQ. Two VLANs (Management, Staff, Students).

**School Campus B** - one WAN edge router plus a small two-tier switch
block (two distribution switches + two access switches); GRE/IPsec tunnel
back to HQ. Three VLANs (Management, Staff, Students).
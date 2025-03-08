# Network Layer

Recommended reading: Kurose chapters 4 and 5

- [Network Layer](#network-layer)
  - [What Does It Do?](#what-does-it-do)
  - [How Does It Do It?](#how-does-it-do-it)
  - [Data Plane (Forwarding)](#data-plane-forwarding)
    - [How Does Forwarding Work?](#how-does-forwarding-work)
    - [IPv4](#ipv4)
      - [Addressing](#addressing)
      - [NAT](#nat)
    - [IPv6](#ipv6)
      - [Migration](#migration)
  - [Control Plane](#control-plane)


## What Does It Do?

> [!IMPORTANT]
> Move datagrams from sending host to receiving host via intermediate routers

![](image.png)

- Example: host H1 sends msg to host H2
    - H1: ... -> transport segments -> network datagrams -> ...
    - H1 -> R1 -> … -> R2 -> H2
      - Up to network layer involved in each hop
    - H2: ... -> network datagrams -> transport segments -> ...
- Network layer used in all nodes in network:
    - hosts
    - packet-switches
        - link-layer switches
        - routers
- c.f. app and transport layers which are only used on end systems (hosts)

## How Does It Do It?

![](image-1.png)

1. **Forwarding** (data plane)
    - Move datagram from **incoming link to appropriate outgoing link**
    - router-local
    - hardware (fast)
1. **Routing** (control plane)  
    - Determine good **end-to-end paths** and update router-local forwarding info
    - network-wide
    - software (slower)

## Data Plane (Forwarding)

### How Does Forwarding Work?

### IPv4

#### Addressing

#### NAT

### IPv6

#### Migration

## Control Plane
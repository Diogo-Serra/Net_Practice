# TCP/IP Addressing and Routing Fundamentals

Oct 20, 2026 · @Diogo Serra

All IP communication reduces to one rule: two devices talk directly only if their IPs fall in the same subnet; otherwise, traffic must go through a gateway or router that has a leg in both.

## Overview

Every IP network follows one pattern: hosts and switches form subnets, routers join subnets, and gateways and routing tables determine where traffic is sent.

&#91;embedded content: two subnets joined by a router · 2 switches, 2 hosts\]

PC A and the Server are in different subnets, so PC A sends its traffic to the gateway (the router's eth0) and the router forwards it out of eth1. Most connectivity faults trace back to a small set of causes: a mismatched mask, a gateway outside the subnet, a duplicate address or a missing route.

## 1. IP addresses

An IPv4 address is 32 bits, written as four decimal numbers (octets) from 0 to 255, for example `192.168.1.10`. Each octet is 8 bits, so each one is a sum of the weights 128, 64, 32, 16, 8, 4, 2, 1.

```csv
bit position,1,2,3,4,5,6,7,8
weight,128,64,32,16,8,4,2,1
```

An address has two parts: a **network part** (which network) and a **host part** (which device inside it). The subnet mask, covered next, says where the split is.

Validity rules for a host address:

- Every octet is between 0 and 255 (so `256` or `-1` is invalid).
- It is not the network address (host bits all 0) or the broadcast address (host bits all 1) of its subnet.
- No two interfaces in the same subnet share the same IP.

Reserved and special ranges:

| Range | Use |
| --- | --- |
| 10.0.0.0 to 10.255.255.255 | Private (class A size) |
| 172.16.0.0 to 172.31.255.255 | Private (class B size) |
| 192.168.0.0 to 192.168.255.255 | Private (class C size) |
| 127.0.0.0/8 | Loopback (the machine itself) |
| 0.0.0.0 | "Any" / unspecified, used in the default route |
| 255.255.255.255 | Limited broadcast |

## 2. Subnet masks and CIDR

A subnet mask is 32 bits: a run of 1s (network part) followed by 0s (host part). `255.255.255.0` is 24 ones then 8 zeros, written `/24` in CIDR notation. The number after the slash is simply how many leading 1 bits the mask has.

The only octet values a mask can have are 0, 128, 192, 224, 240, 248, 252, 254, 255. The following table should be memorised:

| CIDR | Mask | Block size | Total addresses | Usable hosts |
| --- | --- | --- | --- | --- |
| /8 | 255.0.0.0 | 16,777,216 | 16,777,216 | 16,777,214 |
| /16 | 255.255.0.0 | 65,536 | 65,536 | 65,534 |
| /24 | 255.255.255.0 | 256 | 256 | 254 |
| /25 | 255.255.255.128 | 128 | 128 | 126 |
| /26 | 255.255.255.192 | 64 | 64 | 62 |
| /27 | 255.255.255.224 | 32 | 32 | 30 |
| /28 | 255.255.255.240 | 16 | 16 | 14 |
| /29 | 255.255.255.248 | 8 | 8 | 6 |
| /30 | 255.255.255.252 | 4 | 4 | 2 |

Two shortcuts to memorise:

- **Block size = 256 minus the mask octet.** Mask `.192` gives 256 − 192 = 64. Subnets in that octet start at multiples of 64: 0, 64, 128, 192.
- **Usable hosts = block size − 2.** One address is the network, one is the broadcast. A /30 has exactly 2 usable hosts, which is the classic size for a link between two routers.

A mask is written either in dotted decimal or as a `/n`. Convert freely between them: /24 is 255.255.255.0, /25 is .128, /26 is .192, and each step adds one more bit, halving the block.

## 3. The 4-number method

For any IP and mask, derive the following values to determine the network, host range and broadcast address. No binary conversion is required.

1. Find the **interesting octet**: the first octet where the mask is not 255 or 0.
2. Compute the **block size**: 256 − mask value in that octet.
3. **Network address** = the largest multiple of the block size that is ≤ the IP's value in that octet (octets to the left copy over; octets to the right are 0).
4. **Broadcast** = next network address − 1 (octets to the right become 255).
5. **Usable range** = network + 1 up to broadcast − 1.

**Worked example: 192.168.1.77 /26**

```text
Mask /26 = 255.255.255.192   interesting octet = 4th
Block size = 256 - 192 = 64   subnets start at 0, 64, 128, 192
77 falls between 64 and 127
Network    = 192.168.1.64
First host = 192.168.1.65
Last host  = 192.168.1.126
Broadcast  = 192.168.1.127
```

**Same subnet test.** Two IPs can talk directly only if they have the same network address under the same mask. Example: 192.168.1.70/26 and 192.168.1.130/26 give networks .64 and .128, so they are different subnets and need a router between them.

When the interesting octet is not the last one, the same logic applies one octet earlier. With `10.20.35.9 /20`, the mask is 255.255.240.0, the 3rd octet is interesting, block size = 16, 35 sits in 32 to 47, so network = 10.20.32.0 and broadcast = 10.20.47.255.

## 4. Default gateway, switches and routers

The **default gateway** is the IP of the router interface that sits in your own subnet. A host uses it for every destination outside its subnet: it sends the packet to the gateway and lets the router decide.

Requirements for a valid default gateway:

- It must be in the **same subnet** as the host that uses it (same network address under the host's mask).
- It must be a **valid host address**: not the network address, not the broadcast.
- It must be the **IP of a router interface** on that segment, not another host's IP and not the host's own IP.
- A host that only talks inside its own subnet does not need a gateway, but every host that must reach another network does.

The devices:

| Device | OSI layer | IP address | Function |
| --- | --- | --- | --- |
| Switch | 2 | Not assigned | Connects devices into a single subnet; all attached devices share the same network and mask. |
| Router | 3 | One per interface | Interconnects different subnets; each interface belongs to a distinct subnet. |
| Host | 3 and up | Yes | Delivers local traffic directly and forwards all other traffic to its default gateway. |

Rules for router interfaces: every interface lives in a different subnet, two interfaces of one router can never share a subnet, and a link between two routers is a subnet of its own (usually a /30 with 2 usable addresses, one for each end).

## 5. Routing tables

A router decides where to send a packet by looking up the destination in its routing table. Each row has a **destination network** (with mask) and a **next hop** (the IP of the neighbouring router, or the interface to use when the network is directly attached).

```text
Destination        Next hop
10.0.0.0/24        directly connected (interface 2)
192.168.1.0/24     directly connected (interface 1)
172.16.0.0/16      10.0.0.2            <- via the next router
0.0.0.0/0          10.0.0.2            <- default route: everything else
```

How to think about it:

- **Directly connected networks** are known automatically: an interface with an IP address and mask makes its subnet reachable through that interface.
- **Remote networks** require an entry with a next hop. The next hop must be an address in a subnet to which the router is directly attached.
- **The default route `0.0.0.0/0`** matches every address and sends all unmatched traffic to one neighbour. It is the usual way a router reaches the Internet.
- **Longest prefix match.** When several entries match, the most specific one is used: a `/16` entry takes precedence over `0.0.0.0/0`.
- **Return path.** Routes must exist in both directions; a packet that arrives but cannot be answered still results in a failed connection.

Routing faults are most often corrected by adding the default route toward the upstream router, or by setting the next hop to the neighbour's address on the shared link.

## 6. OSI layers

The OSI model splits networking into 7 layers. Each layer only talks to the one above and below it, and data gets wrapped (encapsulated) on the way down and unwrapped on the way up. IP addressing and routing operate almost entirely at **layers 2 and 3**.

| # | Layer | Unit | Examples | Role in addressing and routing |
| --- | --- | --- | --- | --- |
| 7 | Application | Data | HTTP, DNS, SSH | Uses the network; independent of addressing |
| 6 | Presentation | Data | TLS, encoding | Not involved |
| 5 | Session | Data | Sessions, sockets | Not involved |
| 4 | Transport | Segment | TCP, UDP, ports | End-to-end delivery between processes |
| 3 | Network | Packet | IP, routers | Addressing, subnetting, gateways, routing |
| 2 | Data link | Frame | Ethernet, MAC, switches | Delivery within a single subnet |
| 1 | Physical | Bits | Cables, Wi-Fi | Physical links |

Mapping to the TCP/IP model (4 layers): Application (OSI 5 to 7), Transport (4), Internet (3), Link (1 and 2).

In short: switches forward frames by MAC address within one network (layer 2), and routers forward packets by IP address between networks (layer 3).

## 7. How it all connects

Follow one ping from PC A to the Server. The decision at each step uses a different idea from this lecture.

&#91;embedded content: packet from PC A to Server and back · 4 steps\]

Step 1 uses the **mask** (is the destination in my subnet?) and the **gateway**. Step 2 uses the router's **routing table**. Steps 3 and 4 show the **return path**, which needs its own correct gateway and route. At layer 2, the switches only move frames between devices of the same subnet; at layer 3, the router moves packets between subnets. If any of the masks, gateways or routes is wrong, connectivity fails at that exact step, which is how a fault is localised.

## 8. Quick reference and common faults

```text
Block size   = 256 - mask octet        Usable hosts = block - 2
Network      = highest multiple of block <= IP octet
Broadcast    = next network - 1        Range = network+1 .. broadcast-1
/24=.0  /25=.128  /26=.192  /27=.224  /28=.240  /29=.248  /30=.252
Blocks: 256 128 64 32 16 8 4
```

Common configuration faults:

- IP set to the network address (host bits all 0) or the broadcast address (all 1).
- Gateway in a different subnet from the host, or gateway equal to the host's own IP.
- Different masks on devices sharing one switch.
- Two interfaces of one router in the same subnet.
- Duplicate IPs in the same subnet.
- Router-to-router link not sharing one /30 (or other common subnet).
- Missing return route: the packet gets there but the reply cannot come back.
- Next hop that is not on a directly connected network of that router.
- Mask too small for the number of hosts required (hosts + 2 must fit in the block).
- Octet above 255 or a typo such as 192.168.1.256.

The block-size method requires no binary conversion and should be practised by hand until it is routine.

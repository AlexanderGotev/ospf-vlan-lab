# Addressing Table

## Router Loopbacks (Router IDs)

| Router | Loopback0 IP | Used as Router ID |
|--------|-------------|--------------------|
| R1 | 1.1.1.1/32 | 1.1.1.1 |
| R2 | 2.2.2.2/32 | 2.2.2.2 |
| R3 | 3.3.3.3/32 | 3.3.3.3 |

## Inter-Router Point-to-Point Links (/30)

| Link | Subnet | R1 | R2 | R3 |
|------|--------|----|----|----|
| R1 – R2 | 10.0.10.0/30 | 10.0.10.1 (Gi0/0) | 10.0.10.2 (Gi0/0) | — |
| R2 – R3 | 10.0.11.0/30 | — | 10.0.11.1 (Gi0/1) | 10.0.11.2 (Gi0/1) |
| R1 – R3 | 10.0.12.0/30 | 10.0.12.2 (Gi0/2) | — | 10.0.12.1 (Gi0/2) |

## VLAN / LAN Subnets (per site)

Each site (router) hosts the same three departments — **HR**, **IT**, **Sales** — each on its own VLAN, but every site uses a **unique subnet** per VLAN so the addressing doesn't overlap across the routed network.

| Site | VLAN | Department | Subnet | Router Gateway | Sub-interface |
|------|------|-----------|--------|-----------------|----------------|
| R1 | 10 | HR | 192.168.10.0/24 | 192.168.10.1 | Gi0/1.10 |
| R1 | 20 | IT | 192.168.20.0/24 | 192.168.20.1 | Gi0/1.20 |
| R1 | 30 | Sales | 192.168.30.0/24 | 192.168.30.1 | Gi0/1.30 |
| R2 | 10 | HR | 192.168.11.0/24 | 192.168.11.1 | Gi0/2.10 |
| R2 | 20 | IT | 192.168.21.0/24 | 192.168.21.1 | Gi0/2.20 |
| R2 | 30 | Sales | 192.168.31.0/24 | 192.168.31.1 | Gi0/2.30 |
| R3 | 10 | HR | 192.168.12.0/24 | 192.168.12.1 | Gi0/0.10 |
| R3 | 20 | IT | 192.168.22.0/24 | 192.168.22.1 | Gi0/0.20 |
| R3 | 30 | Sales | 192.168.32.0/24 | 192.168.32.1 | Gi0/0.30 |

> Note: R3 also has a native (non-tagged) subnet on Gi0/0 — 192.168.3.0/24 — configured directly on the physical interface, in addition to its three VLAN sub-interfaces.

## Switches

| Switch | Connected Router | Trunk Port | Access Ports |
|--------|-------------------|-----------|----------------|
| SW1 | R1 | Gi0/1 (trunk) | Fa0/1-3 → VLAN 10 (HR), Fa0/4-6 → VLAN 20 (IT), Fa0/7-9 → VLAN 30 (Sales) |
| SW2 | R2 | Gi0/1 (trunk) | Fa0/1-3 → VLAN 10 (HR), Fa0/4-6 → VLAN 20 (IT), Fa0/7-9 → VLAN 30 (Sales) |
| SW3 | R3 | Gi0/1 (trunk) | Fa0/1-3 → VLAN 10 (HR), Fa0/4-6 → VLAN 20 (IT), Fa0/7-9 → VLAN 30 (Sales) |

## OSPF

- Single area design — everything in **Area 0**.
- All routers use `passive-interface default`, then selectively re-enable Hellos with `no passive-interface` only on router-facing links (both physical point-to-point links and VLAN sub-interfaces facing the LAN switches stay passive, since no other router lives there).

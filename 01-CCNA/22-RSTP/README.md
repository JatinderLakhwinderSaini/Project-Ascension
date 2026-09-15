# Rapid Spanning Tree Protocol (RSTP)

## Overview

Rapid Spanning Tree Protocol (RSTP), defined in IEEE 802.1w, is an improved version of the original Spanning Tree Protocol (STP). Like STP, its primary purpose is to prevent Layer 2 switching loops while allowing redundant links for fault tolerance. RSTP achieves this by introducing new port roles, simplifying port states, and using rapid convergence mechanisms. As a result, the network can recover from topology changes in a few seconds instead of the 30–50 seconds typically required by traditional STP.

---

## Purpose

To prevent Layer 2 switching loops while maintaining redundant paths and restoring network connectivity as quickly as possible after a topology change.

---

## Why RSTP Exists

Traditional STP successfully prevents switching loops, but it converges slowly. When a link fails or a new switch is connected, ports must transition through several states before forwarding traffic. During this delay, users may experience packet loss or temporary network outages.

RSTP was introduced to solve this problem by significantly reducing convergence time. It replaces unnecessary waiting states, introduces new port roles, and allows switches to rapidly agree on forwarding paths.

---

## The Problem It Solves

Without STP or RSTP, redundant links create Layer 2 loops. These loops can cause:

* Broadcast storms
* Multiple frame copies
* MAC address table instability (MAC flapping)
* High CPU utilization on switches
* Complete network outages

RSTP prevents these problems while still allowing redundant links to remain available as backup paths.

---

## How It Works

1. Elects the switch with the lowest Bridge ID as the Root Bridge.
2. Each non-root switch selects its best path to the Root Bridge (Root Port).
3. Each network segment elects one Designated Port to forward traffic.
4. Alternate ports remain in the Discarding state as backup paths.
5. If the active path fails, the Alternate Port can quickly transition to Forwarding, greatly reducing downtime.


## Interview Questions

### 1. Why is RSTP faster than STP?

**Answer:**

RSTP converges much faster than traditional STP because it removes unnecessary waiting states, introduces rapid Proposal/Agreement handshakes, and quickly transitions ports to the Forwarding state instead of relying on fixed timers.

---

### 2. What is the difference between a port role and a port state?

**Answer:**

A **port role** defines the port's responsibility in the spanning-tree topology (Root, Designated, Alternate, or Backup).

A **port state** defines what the port is currently doing (Discarding, Learning, or Forwarding).

---

### 3. Why does RSTP use the Discarding state?

**Answer:**

The Discarding state prevents a port from forwarding user traffic or learning MAC addresses while still allowing it to receive and process BPDUs. This prevents Layer 2 loops until the port is allowed to transition to the Forwarding state.

---

### 4. What happens when the Root Port fails?

**Answer:**

When the Root Port fails, the switch immediately selects the best Alternate Port as the new Root Port. Since the Alternate Port has already been receiving BPDUs, it can rapidly transition to the Forwarding state, minimizing network downtime.

---

### 5. How is the Root Bridge elected?

**Answer:**

The switch with the lowest Bridge ID (Bridge Priority + Extended System ID + MAC Address) is elected as the Root Bridge. If all switches have the same priority, the switch with the lowest MAC address becomes the Root Bridge.


### Q: Why is RSTP faster than STP?

❌ My first answer:
"It removes the waiting timers."

✅ Correct understanding:
"It removes unnecessary states and uses Proposal/Agreement handshakes for rapid convergence."

## References
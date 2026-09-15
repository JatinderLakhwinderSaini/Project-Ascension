# RSTP Port States

RSTP uses three port states:

1. Discarding
2. Learning
3. Forwarding

---

# Discarding

The port:

- Does not forward user traffic.
- Does not learn MAC addresses.
- Can receive BPDUs.

Used for:

- Alternate ports
- Backup ports
- Blocked paths

---

# Learning

The port:

- Learns source MAC addresses.
- Does not forward user traffic yet.

Purpose:

Build the MAC address table before forwarding.

---

# Forwarding

The port:

- Sends and receives user traffic.
- Learns MAC addresses.
- Participates in normal switching.

---

# STP vs RSTP States

Traditional STP:
Blocking
Listening
Learning
Forwarding
Disabled

# RSTP States
Discarding
Learning
Forwarding
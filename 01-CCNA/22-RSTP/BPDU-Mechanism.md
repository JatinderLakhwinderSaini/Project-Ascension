# RSTP BPDU Mechanism

## Purpose

BPDUs are control messages used by switches to exchange Spanning Tree information.

RSTP uses BPDUs to:

- Elect Root Bridge.
- Select Root Ports.
- Select Designated Ports.
- Detect topology changes.

---

# STP vs RSTP BPDU Behavior

Traditional STP:

- Root Bridge generates BPDUs.
- Other switches forward received information.

RSTP:

- Every switch generates its own BPDUs.
- Switches directly exchange topology information.

This allows faster reaction.

---

# Important BPDU Information

BPDUs contain information such as:

- Root Bridge ID
- Sender Bridge ID
- Root Path Cost
- Port ID
- Timers
- Flags

---

# Superior BPDU

A Superior BPDU means:

"This path is better."

A switch receiving a superior BPDU may change:

- Root Port
- Designated Port
- Root Bridge information

---

# Important Concept

BPDU decisions and MAC learning are separate.

BPDU:

Controls STP topology.

MAC Address Table:

Controls frame forwarding.
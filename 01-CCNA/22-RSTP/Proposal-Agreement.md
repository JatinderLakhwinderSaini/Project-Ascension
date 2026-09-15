
RSTP replaces timer-based waiting with negotiation.

---

# Important Points

## Who sends Proposal?

The Designated Port sends the Proposal BPDU.

## Who sends Agreement?

The neighboring switch sends Agreement after synchronization.

## What must happen before Agreement?

The receiving switch must synchronize its ports and ensure no alternate forwarding path creates a loop.

## Why is it faster?

Because switches negotiate forwarding using BPDU handshakes instead of waiting for STP timers.

---

# Common Mistake

Wrong understanding:

"RSTP is faster only because it removed Listening state."

Correct understanding:

RSTP is faster mainly because of:

1. Proposal/Agreement handshake.
2. Alternate and Backup port roles.
3. Immediate Edge Port transition.
4. Every switch generating BPDUs.
5. Simplified port states.

---

# Real World Example

A Active uplink between two switches fails.

Traditional STP waits for timers before restoring connectivity.

RSTP already knows the backup path and uses rapid negotiation to move the alternate path into forwarding state within seconds or less.
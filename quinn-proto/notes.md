Range sets in Quinn efficiently track sets of u64 ranges for QUIC protocol operations. They're optimized for long consecutive
  runs with random insert/delete/contains operations.

  Key uses:

  1. ACK tracking (src/connection/spaces.rs:618)
  - Track which packet numbers have been received but not yet acknowledged
  - ArrayRangeSet is used here because ACK numbers are usually consecutive (unless packet loss/reordering occurs)

  2. Stream data reassembly (src/connection/assembler.rs:49-52)
  - Track which byte offsets have been received from the peer
  - Handles out-of-order data delivery by remembering which ranges arrived

  3. Send buffer management (src/connection/send_buffer.rs:23-26)
  - Track acknowledged ranges that can't be discarded yet
  - Track retransmitted ranges deemed lost

  Two implementations:

  ArrayRangeSet (array_range_set.rs:7-18)
  - Array-based with inline storage (2 ranges on stack)
  - Avoids heap allocation for small range counts
  - Cache-friendly iteration
  - Perfect for ACK ranges which are usually small and consecutive

  RangeSet (btree_range_set.rs:11-13)
  - BTreeMap-based for general purpose
  - Better for larger/more fragmented range sets
  - Used for stream data tracking where out-of-order delivery is common

  Essentially, they solve the problem of efficiently tracking "which numbers in a potentially huge space have we seen?" -
  critical for reliable data delivery in QUIC.

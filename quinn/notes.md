- pub fn generate_simple_self_signed(subject_alt_names: impl Into<Vec<String>>) -> Result<CertifiedKey<KeyPair>, Error> why make the param impl into vec string instead of just vec string

⏺ Based on the code analysis, here are the connection errors that can occur after a handshake and during read operations on a RecvStream:

  Errors that CAN happen after handshake during RecvStream reads:

  1. FLOW_CONTROL_ERROR (0x3) - Can occur if the peer sends more data than permitted by the advertised flow control limits during stream reads.
  2. STREAM_LIMIT_ERROR (0x4) - Can happen if frames are received for stream IDs that exceed the advertised limit.
  3. STREAM_STATE_ERROR (0x5) - Can occur if frames are received for a stream in an inappropriate state (e.g., data on a stopped stream).
  4. FINAL_SIZE_ERROR (0x6) - Can happen if a STREAM frame contains a different final size than previously established, which would be detected during reads.
  5. FRAME_ENCODING_ERROR (0x7) - Can occur if badly formatted frames are received while reading stream data.
  6. PROTOCOL_VIOLATION (0xA) - General protocol compliance errors that can occur during data transfer.
  7. KEY_UPDATE_ERROR (0xE) - Can occur during an ongoing connection when key updates fail.
  8. AEAD_LIMIT_REACHED (0xF) - Can happen during data transfer when encryption limits are reached.
  9. NO_VIABLE_PATH (0x10) - Can occur if all network paths become unavailable during an active stream read.
  10. APPLICATION_ERROR (0xC) - Can technically occur post-handshake if the application closes the connection (though the error description mentions "during
  handshake", it's actually usable anytime).

  Errors that CANNOT happen after successful handshake during reads:

  - CONNECTION_REFUSED (0x2) - Only during connection establishment
  - TRANSPORT_PARAMETER_ERROR (0x8) - Only during handshake
  - CONNECTION_ID_LIMIT_ERROR (0x9) - Typically during handshake/connection setup
  - INVALID_TOKEN (0xB) - Only during Initial packet processing (pre-handshake)
  - CRYPTO_BUFFER_EXCEEDED (0xD) - Only during handshake (CRYPTO frames)

  Looking at src/recv_stream.rs:393, when a connection error occurs during a read, it's surfaced as ReadError::ConnectionLost(ConnectionError), so any of the
  post-handshake connection errors above would manifest through this code path.

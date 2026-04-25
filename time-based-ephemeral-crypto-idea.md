# 🔐 Time-Based Ephemeral Crypto – conceptual idea

**Status:** Brainstorming / Conceptual

## The Core Idea

Two endpoints (two laptops) communicate using an encryption key that changes every 30 seconds. The new key is NEVER sent over the network.

Instead, both devices calculate the same key independently using:
- A one-time shared secret (exchanged offline once, via QR code)
- The current time (synchronized)

Even if an attacker intercepts a packet, they cannot decrypt it because they don't have the 30-second key. By the time they try to crack it, the key has already expired.

> "The key runs faster than the attacker."

## How it works

1. **Offline setup (one time):** Device A and Device B generate a random seed. User scans a QR code from A to B. Both have the same seed.

2. **Time-based key derivation:** Every 30 seconds, both devices calculate:
   `current_key = HMAC(seed, current_time_window)`

3. **Encryption:** Device A encrypts packet with current_key and sends it to B.

4. **Decryption:** Device B tries current_key, previous_key, and next_key (to tolerate small time drifts).

## Main challenges

- Clock drift / latency → try a window of keys
- First packet lost → needs sync request
- Replay attack → include timestamp inside encrypted packet
- Attacker steals the seed → seed must be stored securely

## Why this is interesting

- No key exchange over the network after setup
- Each key used for only 30 seconds
- Perfect forward secrecy by design
- Like TOTP (Google Authenticator), but for encryption

## Next steps

- [ ] Python script that derives keys from seed + time
- [ ] Encrypt/decrypt between two scripts
- [ ] Add time drift tolerance
- [ ] Simulate attack: capture and try to decrypt after key expires

---

*Personal brainstorming document. Not production-ready.*

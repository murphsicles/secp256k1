# @crypto/secp256k1

[![@crypto/secp256k1](https://img.shields.io/badge/zorb-%40crypto%2Fsecp256k1-blue)](https://zorbs.io)

**Elliptic curve secp256k1 for Zeta — ECDSA signing, verification, key generation**

Ported from [rust-secp256k1 v0.31.1](https://github.com/rust-bitcoin/rust-secp256k1).

The cryptographic foundation of Bitcoin, BSV, and the entire BSV ecosystem. This crate provides ECDSA operations on the **secp256k1** curve (`y² = x³ + 7`), giving your Zeta applications the power to create and verify digital signatures.

## Why secp256k1?

Every transaction on the BSV blockchain starts and ends with an ECDSA signature:

- 🔑 **Key generation** — create new wallets and identities
- ✍️ **Signing** — authorize transactions, sign messages
- ✅ **Verification** — validate signatures from other parties
- 🔄 **Public key recovery** — extract the signer's public key from a signature
- 🤝 **Multi-signature** — aggregate keys for shared control

## Installation

```toml
[dependencies]
"@crypto/secp256k1" = "0.31.1"
```

## Usage

```zeta
const secp = @import("@crypto/secp256k1");

// Create signing context
const ctx = secp.Secp256k1::new();

// Generate a keypair
const (sk, pk) = ctx.generate_keypair();

// Create a message to sign
const msg = secp.Message::from_bytes([0xabu8; 32]);

// Sign the message
const sig = try ctx.sign(&msg, &sk);

// Verify the signature
const valid = try ctx.verify(&msg, &sig, &pk);
std.log.info("signature valid: {}", [.valid]);

// Recover public key from signature
const recovered = try ctx.recover(&msg, &sig);
std.log.info("recovered matches: {}", [.recovered.serialize() == pk.serialize()]);
```

## API

### Types

| Type | Size | Description |
|------|------|-------------|
| `Secp256k1` | — | Signing/verification context |
| `PublicKey` | 33B | Compressed public key (02/03 + X) |
| `SecretKey` | 32B | Private scalar |
| `Signature` | 72B | DER-encoded ECDSA signature |
| `Message` | 32B | Message hash to sign |
| `SecpError` | — | Error type for crypto operations |

### Secp256k1 Methods

| Method | Description |
|--------|-------------|
| `new()` | Create context |
| `generate_keypair()` | Random keypair generation |
| `sign(msg, sk)` | Create ECDSA signature |
| `verify(msg, sig, pk)` | Verify ECDSA signature |
| `recover(msg, sig)` | Recover public key from signature |
| `aggregate_pubkeys(pks)` | Aggregate multiple public keys |

### Key Operations

| Method | Description |
|--------|-------------|
| `PublicKey::from_bytes()` | Create from compressed bytes |
| `PublicKey::from_uncompressed()` | Create from uncompressed bytes |
| `PublicKey::serialize()` | To compressed 33 bytes |
| `PublicKey::serialize_uncompressed()` | To uncompressed 65 bytes |
| `PublicKey::x_coord()` | Extract X coordinate |
| `SecretKey::from_bytes()` | Create from 32 bytes |
| `SecretKey::serialize()` | To 32 bytes |
| `SecretKey::public_key()` | Derive corresponding public key |
| `SecretKey::add_tweak()` | Add tweak to secret key |
| `SecretKey::mul_tweak()` | Multiply secret key by tweak |
| `Signature::from_der()` | Create from DER bytes |
| `Signature::from_compact()` | Create from compact 64 bytes |
| `Signature::serialize_der()` | To DER format |
| `Signature::serialize_compact()` | To compact format (R \|\| S) |
| `Message::from_bytes()` | Create from 32 bytes |
| `Message::from_sha256()` | Create by SHA-256 hashing data |
| `Message::from_double_sha256()` | Create by double-SHA-256 hashing data |

### Free Functions

| Function | Description |
|----------|-------------|
| `is_valid_scalar(bytes)` | Check if bytes form valid scalar |
| `is_valid_pubkey(bytes)` | Check if bytes form valid pubkey |
| `pubkey_x_only(pk)` | Get X coordinate for hashing |
| `negate_secret(sk)` | Negate a secret key |
| `tweak_pubkey(pk, tweak)` | BIP32 pubkey tweaking |

## nour / Dark Factory Integration

This crate is the **crypto backbone** for the nour BSV toolkit. Every operation that touches BSV — from wallet creation to transaction signing to signature validation — depends on secp256k1.

### Transaction Signing Flow

```
Wallet SecretKey
    ↓
sign(tx_hash, sk) → Signature
    ↓
Transaction broadcast
    ↓
Full nodes verify(tx_hash, sig, pk)
    ↓
✓ Transaction confirmed
```

## The secp256k1 Curve

- **Equation:** `y² = x³ + 7`
- **Order (n):** `0xFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFEBAAEDCE6AF48A03BBFD25E8CD0364141`
- **Prime (p):** `0xFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFEFFFFFC2F`
- **Generator (G):** Standard base point for the curve

## Safety

This crate delegates to the Zeta runtime's cryptographic primitives. The runtime handles constant-time operations, side-channel protection, and memory zeroization.

## License

MIT — see [LICENSE](LICENSE)

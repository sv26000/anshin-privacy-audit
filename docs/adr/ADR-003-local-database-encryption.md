# ADR-003: Local Database Encryption via AES-256-GCM and Argon2id KDF

- **Status**: Accepted
- **Deciders**: Anshin Core Architecture Team
- **Date**: 2024-10-22
- **Technical Context**: Anshin macOS Application (Build 0.1.53+)
- **Keywords**: SQLite, AES-256-GCM, Argon2id, Encryption at Rest, macOS Sandbox, Zero Cloud Sync

---

## Context and Problem Statement

Because Anshin operates 100% on-device, all user records—including journal entries, mood tracking points, CBT thought records, unsent letters, and conversational history—are persisted to the local file system.

If a Mac is stolen, accessed by unauthorized local users, or inspected by other local processes, unencrypted plain text databases could be exposed.

We needed a cryptographic storage engine that:
1. Provides military-grade authenticated encryption at rest.
2. Derives keys using memory-hard, brute-force-resistant key derivation functions (KDF).
3. Leverages macOS kernel-level isolation (App Sandbox).
4. Avoids cloud synchronization vectors that could leak ciphertexts to multi-tenant servers.

---

## Considered Alternatives

1. **Plain SQLite with File-System Permissions Only**
   - *Pros*: Fast, zero cryptographic overhead.
   - *Cons*: Vulnerable to disk forensic tools, physical theft, or compromised local backups.
2. **Cloud-Encrypted Database (e.g., Firebase / Supabase with Server-Side Encryption)**
   - *Pros*: Seamless multi-device sync.
   - *Cons*: Server operator holds encryption keys; vulnerable to cloud subpoenas and breaches.
3. **Encrypted SQLite (AES-256-GCM) with Argon2id Key Derivation within macOS Sandbox**
   - *Pros*: Authenticated encryption with authenticated data (AEAD); Argon2id provides state-of-the-art resistance against GPU/ASIC dictionary attacks; sandboxed inside `~/Library/Containers/com.anshin.app/Data/`.
   - *Cons*: Data is restricted to the local device unless manually exported by the user.

---

## Decision Outcome

**Chosen Alternative: Alternative 3 — Encrypted SQLite via AES-256-GCM and Argon2id KDF.**

### Cryptographic Architecture
- **Cipher**: AES-256 in Galois/Counter Mode (GCM). GCM provides both confidentiality and message authentication, guaranteeing that database pages cannot be secretly modified without triggering an authentication failure.
- **Key Derivation Function (KDF)**: Argon2id (winner of the Password Hashing Competition). Memory-hard configuration prevents parallel GPU-accelerated cracking attempts.
- **Filesystem Isolation**: The database resides inside the macOS App Sandbox container:
  `~/Library/Containers/com.anshin.app/Data/`
  Under macOS kernel policy, third-party sandboxed apps are strictly forbidden from inspecting files within Anshin's container.
- **Zero Silent Cloud Sync**: Anshin does not automatically mirror ciphertext to iCloud or remote servers. Users retain 100% data custody. If they wish to migrate machines, they can trigger an explicit, encrypted JSON export from the application Settings.

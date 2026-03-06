<p align="center">
  <img src="https://github.com/AsoiX-exe/obfus.jar/blob/main/icon.png" width="120" alt="obfus.jar logo"/>
</p>

<h1 align="center">OBFUS.JAR</h1>
<p align="center">
  <b>Advanced Java Obfuscator with VM Virtualization Engine</b>
</p>

<p align="center">
  <img src="https://img.shields.io/badge/Java-8+-orange?logo=openjdk" alt="Java 8+"/>
  <img src="https://img.shields.io/badge/ASM-9.6-blue" alt="ASM 9.6"/>
  <img src="https://img.shields.io/badge/License-MIT-green" alt="MIT"/>
  <img src="https://img.shields.io/badge/Platform-Windows%20%7C%20Linux%20%7C%20macOS-lightgrey" alt="Platform"/>
</p>

---

## What is obfus.jar?

A next-generation obfuscation tool that goes far beyond simple renaming. The core engine **converts your Java bytecode into a custom VM instruction set** — your original code literally doesn't exist in the output JAR. On top of that, multiple layered protections make both static and dynamic analysis extremely difficult.

Works with **Spigot/Paper plugins**, **Fabric/Forge mods**, **BungeeCord/Velocity proxies**, and any standard Java application.

---

## Architecture

```
┌─────────────────────────────────────────────────────┐
│                   INPUT JAR                         │
└──────────────────────┬──────────────────────────────┘
                       │
        ┌──────────────▼──────────────┐
        │     Honeypot Strings        │  ← fake flags, API keys, passwords
        ├─────────────────────────────┤
        │     String Encryption       │  ← SBox cipher, per-JAR key + salt
        ├─────────────────────────────┤
        │     Trash Code Injection    │  ← opaque predicates, dead code
        ├─────────────────────────────┤
        │     Watermark               │  ← author signature (synthetic)
        ├─────────────────────────────┤
        │     Decompiler Crasher      │  ← metadata bombs for FernFlower/CFR
        ├─────────────────────────────┤
        │     Control Flow Flattening │  ← switch dispatcher transformation
        ├─────────────────────────────┤
        │     VM Virtualization       │  ← bytecode → custom VM opcodes
        │       ├ Opcode Permutation  │
        │       ├ Junk Opcodes        │
        │       ├ SBox + XOR Cipher   │
        │       └ CRC32 Integrity     │
        ├─────────────────────────────┤
        │     Name Mangling           │  ← fields/methods → IlIlIl...
        ├─────────────────────────────┤
        │     Class Renaming          │  ← classes → IlIlIl...
        ├─────────────────────────────┤
        │     Reference Hiding        │  ← API calls via invokedynamic
        ├─────────────────────────────┤
        │     Debug Stripping         │  ← remove LineNumbers, LocalVars
        └──────────────────┬──────────┘
                           │
        ┌──────────────────▼──────────┐
        │        OUTPUT JAR           │
        └─────────────────────────────┘
```

---

## Features

### 🔒 VM Virtualization (Core Engine)
The heart of obfus.jar. Your method bytecode is compiled into a **custom instruction set** and executed by an embedded interpreter at runtime.

| Layer | What it does |
|---|---|
| **Custom Opcodes** | 80+ VM instructions covering arithmetic, control flow, method calls, field access, arrays, exceptions |
| **Opcode Permutation** | Opcode table is randomly shuffled per-JAR — no two builds share the same mapping |
| **Junk Opcodes** | Random no-op instructions injected between real ones to bloat analysis |
| **SBox + Streaming Cipher** | VM bytecode is encrypted with a per-JAR SBox permutation, then XOR'd with a position-dependent key derived from the caller's class hash |
| **CRC32 Integrity** | Each method's VM data blob has a CRC32 checksum — tampering breaks execution |
| **Anti-Debug Timing** | `System.nanoTime()` checks detect debugger step-through (limited to first 50 calls to avoid TPS impact on servers) |
| **Caller Verification** | Stack trace analysis ensures VM is called from the expected class — patching the call site breaks decryption |
| **Minecraft-Aware** | Automatically skips hot methods (tick handlers, event listeners, packet handlers) to preserve server performance |

<details>
<summary><b>Before / After</b></summary>

**Before:**
```java
public boolean checkPassword(String password) {
    if (password.equals("admin123")) {
        System.out.println("Access Granted");
        return true;
    }
    return false;
}
```

**After:**
```java
public boolean checkPassword(String password) {
    return (Boolean) IIlIllIl.IlIIlIl.IIIlIl.lIlIlll.lIlIlIlllIIlIl(
        IIlIllIl.IlIIlIl.IIIlIl.lIlIlll.lIllIllIlIIlIl("kR3xPm7Q..."),
        new Object[]{this, password},
        Thread.currentThread().getContextClassLoader()
    );
}
// Original bytecode no longer exists — it's now encrypted VM data in a byte[] field
```
</details>

---

### 🔐 String Encryption
Every string literal is encrypted with a **multi-phase SBox cipher**. The encryption key is split: a random per-string salt goes into the bytecode, but the **per-JAR secret never appears in any class file**.

| Property | Detail |
|---|---|
| Algorithm | Key expansion → SBox substitution → Rolling XOR with state feedback |
| Key derivation | `salt` (in bytecode) + `K` (injected into RuntimeHelper at build time, never in user classes) |
| SBox | Per-JAR 256-byte permutation generated from a unique seed |
| Cache | `ConcurrentHashMap` — each string decrypted only once, then cached |

Without extracting `K` and the SBox seed from the RuntimeHelper class inside the original JAR, **cryptographic decryption is impossible**.

<details>
<summary><b>Before / After</b></summary>

**Before:**
```java
private static final String SECRET = "super_secret_key";
```

**After:**
```java
private static final String SECRET =
    llIllIlllIl.lIlIlIlllIIlIl("eeywmog:1n15m6LOGA==");
// salt="eeywmog", encrypted=Base64("1n15m6LOGA==")
// Key = "eeywmog" + <per-JAR secret> — secret not in bytecode
```
</details>

---

### 🍯 Honeypot Strings (CTF Killer)
Injects **fake flags, passwords, API keys, and credentials** into every class. All honeypots are encrypted with the same cipher as real strings — making them indistinguishable.

- Fake static fields: `HMAC_KEY`, `SECRET`, `LICENSE_KEY`, `DB_PASSWORD`, etc.
- Dead-code methods with **opaque predicates** (`str.length() < 0`) containing bait `equals()` comparisons
- Decrypting a honeypot yields things like `flag{nice_try_but_think_deeper}` or `AWS_SECRET_ACCESS_KEY=wJalrX...`

> A reverser has no way to tell which decrypted strings are real and which are bait.

---

### 💥 Decompiler Crasher
Metadata-only attacks that break FernFlower, CFR, and Procyon **without modifying bytecode** (safe for COMPUTE_FRAMES and JVM verifier):

- **Corrupt SourceFile** — null bytes + RTL override characters
- **Garbage SMAP** — malformed JSR-45 SourceDebugExtension with 100+ overlapping line mappings
- **Malformed Generic Signature** — 200 levels of nested `Map<List<...>>` causes StackOverflow in signature parsers
- **Bogus InnerClasses** — references to non-existent classes with illegal names
- **Annotation Flood** — hundreds of fake nested annotations on classes and methods
- **Duplicate Method Names** — same name, different descriptors (legal in bytecode, illegal in Java source)

---

### 🎭 Masking

| Feature | Description |
|---|---|
| **Name Mangling** | Fields and methods renamed to visually confusing `IlIlIl`-style strings |
| **Class Renaming** | Class names replaced with `IlIlIl`-style identifiers |
| **Reference Hiding** | `invokevirtual` / `invokestatic` calls replaced with `invokedynamic` via a custom bootstrap method — hides which APIs your code uses |
| **Decoy Classes** | Fake classes generated to dilute the real class list |
| **Runtime Renaming** | The VM interpreter itself gets randomized package/class/method names per build |

---

### 🛡️ Hardening

| Feature | Description |
|---|---|
| **Control Flow Flattening** | Transforms method CFG into a switch-dispatcher loop — eliminates readable if/else/for structure |
| **Trash Code** | Injects fake code with opaque predicates + constant obfuscation inside VM bytecode |
| **Junk Opcodes** | Random no-op VM instructions between real ones |
| **Interpreter Encryption** | Strings inside the VM interpreter itself are XOR-encrypted |

---

### 🧹 Cleanup

| Feature | Description |
|---|---|
| **Debug Stripping** | Removes `LineNumberTable`, `LocalVariableTable`, source file info |
| **Watermark** | Injects a custom synthetic field into every class (author signature) |

---

## Visual Transformation

**Before:**

![Before](https://github.com/AsoiX-exe/obfus.jar/blob/main/before3.png)

**After:**

![After](https://github.com/AsoiX-exe/obfus.jar/blob/main/after3.png)

---

## Security Model

```
Attacker must break ALL of these to recover original code:

  1. String Encryption     — per-JAR key not in bytecode
  2. VM Virtualization      — custom ISA, encrypted, per-JAR opcode table
  3. Caller Verification    — stack trace hash feeds into decryption
  4. SBox Cipher            — per-JAR permutation table
  5. CRC32 Integrity        — detects patched VM data
  6. Anti-Debug Timing      — nanoTime checks on interpreter entry
  7. Reference Hiding       — API calls invisible to static analysis
  8. Honeypot Strings       — fake targets waste reverser's time
  9. Decompiler Crasher     — automated tools crash on metadata
```

> Without the RuntimeHelper class from the original JAR, **string decryption is cryptographically impossible**. Without the VMInterpreter, **code recovery requires reimplementing the entire VM**.

---

## Quick Start

1. **Download** the latest build from the [Releases](https://github.com/AsoiX-exe/obfus.jar/releases) page

2. **Requirements:** Java 8+ (compatible with Java 17/21)

3. **Run:**
   ```
   java -jar obfus.jar
   ```

4. **Drag & Drop** your target JAR into the window

5. **Select features** — categories:
   - **ОСНОВНОЕ** — VM Virtualization, String Encryption  
   - **УСИЛЕНИЕ VM** — CFF, Opcode Permutation, Junk Opcodes, Trash Code, Decompiler Crasher  
   - **МАСКИРОВКА** — Decoy Classes, Runtime Renaming, Name Mangling, Reference Hiding, Class Renaming, Honeypot Strings  
   - **ОЧИСТКА** — Debug Stripping, Interpreter Encryption, Watermark  

6. **Click "Protect"** → output: `obfus_yourfile.jar`

---

## Tech Stack

| Component | Version |
|---|---|
| Java target | 8+ |
| ASM (bytecode engineering) | 9.6 |
| FlatLaf (dark theme UI) | 3.4.1 |

---

## License

This project is licensed under the MIT License. See the [LICENSE](https://github.com/AsoiX-exe/obfus.jar/blob/main/LICENSE) file for details.


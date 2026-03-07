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

## What is OBFUS.JAR?

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
        │     Honeypot Strings        │
        ├─────────────────────────────┤
        │     String Encryption       │
        ├─────────────────────────────┤
        │     Trash Code Injection    │
        ├─────────────────────────────┤
        │     Watermark               │
        ├─────────────────────────────┤
        │     Decompiler Crasher      │
        ├─────────────────────────────┤
        │     Control Flow Flattening │
        ├─────────────────────────────┤
        │     VM Virtualization       │
        │       ├ Opcode Permutation  │
        │       ├ Junk Opcodes        │
        │       └ Multi-Layer Cipher  │
        ├─────────────────────────────┤
        │     Name Mangling           │
        ├─────────────────────────────┤
        │     Class Renaming          │
        ├─────────────────────────────┤
        │     Reference Hiding        │
        ├─────────────────────────────┤
        │     Debug Stripping         │
        └──────────────────┬──────────┘
                           │
        ┌──────────────────▼──────────┐
        │        OUTPUT JAR           │
        └─────────────────────────────┘
```

---

## Features

### 🔒 VM Virtualization (Core Engine)
The heart of OBFUS.JAR. Your method bytecode is compiled into a **custom instruction set** and executed by an embedded interpreter at runtime.

| Layer | What it does |
|---|---|
| **Custom Opcodes** | 80+ VM instructions covering arithmetic, control flow, method calls, field access, arrays, exceptions |
| **Opcode Permutation** | Opcode table is randomly shuffled per build — no two JARs share the same mapping |
| **Junk Opcodes** | Random no-op instructions injected between real ones to bloat analysis |
| **Multi-Layer Cipher** | VM bytecode is encrypted with multiple per-JAR keys and permutation tables |
| **Integrity Verification** | Keyed SHA-256 hash validates VM data — any tampering breaks execution |
| **Anti-Dynamic Analysis** | Multiple runtime checks detect debugging, instrumentation, and tampering |
| **Minecraft-Aware** | Automatically skips hot methods (tick handlers, event listeners, packet handlers) to preserve server TPS |

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
// Original bytecode no longer exists — it's encrypted VM data
```
</details>

---

### 🔐 String Encryption
Every string literal is encrypted with a **per-JAR multi-phase cipher**. Decryption requires runtime components that are never exposed in user class files.

- Per-JAR secret key, unique salt per string
- Multi-phase encryption pipeline
- Decrypted values cached for performance — zero TPS impact after first call

> Without access to the runtime internals of a specific build, string decryption is not feasible.

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
```
</details>

---

### 🍯 Honeypot Strings
Injects **fake flags, passwords, API keys, and credentials** into every class. All honeypots are encrypted with the same cipher as real strings — indistinguishable from genuine data.

- Fake static fields: `HMAC_KEY`, `SECRET`, `LICENSE_KEY`, `DB_PASSWORD`, and more
- Dead-code methods with opaque predicates containing bait comparisons
- Wastes significant reverse engineering time on false targets

---

### 💥 Decompiler Crasher
Metadata-level attacks that break popular decompilers (FernFlower, CFR, Procyon) **without modifying runtime bytecode** — fully JVM-verifier safe:

- Corrupt source metadata with illegal characters
- Malformed debug extensions with overlapping mappings
- Deeply nested generic signatures that overflow parsers
- Bogus inner class references and annotation floods
- Duplicate method entries (legal in bytecode, illegal in Java source)

---

### 🎭 Masking

| Feature | Description |
|---|---|
| **Name Mangling** | Fields and methods renamed to visually confusing `IlIlIl`-style identifiers |
| **Class Renaming** | Package and class names replaced with obfuscated identifiers |
| **Reference Hiding** | API calls replaced with `invokedynamic` via custom bootstrap methods — hides which libraries your code uses |
| **Decoy Classes** | Fake classes generated to dilute the real class list |
| **Runtime Renaming** | The VM interpreter itself gets randomized names per build |

---

### 🛡️ Hardening

| Feature | Description |
|---|---|
| **Control Flow Flattening** | Transforms method control flow into a switch-dispatcher loop — eliminates readable structure |
| **Trash Code** | Injects fake code with opaque predicates and constant obfuscation |
| **Junk Opcodes** | Random no-op VM instructions inserted between real ones |
| **Interpreter Encryption** | Internal strings of the VM interpreter are encrypted |

---

### 🧹 Cleanup

| Feature | Description |
|---|---|
| **Debug Stripping** | Removes `LineNumberTable`, `LocalVariableTable`, source file info |
| **Watermark** | Injects a custom author signature into protected classes |

---

## Visual Transformation

**Before:**

![Before](https://github.com/AsoiX-exe/obfus.jar/blob/main/before3.png)

**After:**

![After](https://github.com/AsoiX-exe/obfus.jar/blob/main/after3.png)

---

## Quick Start

1. **Download** the latest build from the [Releases](https://github.com/AsoiX-exe/obfus.jar/releases) page

2. **Requirements:** Java 8+ (compatible with Java 17/21)

3. **Run:**
   ```
   java -jar obfus.jar
   ```

4. **Drag & Drop** your target JAR into the window

5. **Select features** from categories:
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

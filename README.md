![Screenshot](https://github.com/AsoiX-exe/obfus.jar/blob/main/icon.png)
# 🛡️ OBFUS.JAR | Java Obfuscator 

```text
    ██████╗ ██████╗ ███████╗██╗   ██╗███████╗      ██╗ █████╗ ██████╗ 
    ██╔══██╗██╔══██╗██╔════╝██║   ██║██╔════╝      ██║██╔══██╗██╔══██╗
    ██║  ██║██████╔╝█████╗  ██║   ██║███████╗      ██║███████║██████╔╝
    ██║  ██║██╔══██╗██╔══╝  ██║   ██║╚════██║ ██   ██║██╔══██╗██╔══██╗
    ██████╔╝██████╔╝██║     ╚██████╔╝███████║ ╚█████╔╝██║  ██║██║  ██║
    ╚═════╝ ╚═════╝ ╚═╝      ╚═════╝ ╚══════╝  ╚════╝ ╚═╝  ╚═╝╚═╝  ╚═╝ 
```
Obfus.jar is a cutting-edge obfuscation tool designed to protect Java applications, Spigot plugins, and mods from reverse engineering. Unlike traditional rename-only obfuscators, obfus.jar utilizes Ghost Mode technology based on invokedynamic instructions, making static analysis nearly impossible and breaking most modern decompilers (JD-GUI, FernFlower, Procyon).

# 🔥 Key Features
👻 Ghost Mode (Core Engine): Moves method bodies into synthetic "shadow" methods and calls them via invokedynamic instructions using a custom Bootstrap Class Loader. Decompilers see empty calls or InvokeCustoms, while the actual logic remains hidden.

🔢 Math Mutation: Transforms simple integer constants into complex, multi-step mathematical expressions evaluated at runtime. Example: int i = 5 ➔ int i = ((120 ^ 45) & 15) + ...

🛡️ Stack String Encryption: Strings are never stored in plain text. They are constructed dynamically on the stack byte-by-byte and decrypted only when needed using a polymorphic XOR/AES algorithm embedded in the RuntimeHelper.

🌪️ Indirection & Flow Obfuscation: Disrupts linear code execution by injecting proxy methods and circular control flows, turning simple logic into "spaghetti code."

🗑️ Trash Generation: Floods the JAR structure with hundreds of dummy directories (e.g., trash_16273...) to confuse unarchivers and manual inspection tools.

🛑 Anti-Debug & Anti-Tamper: Includes timing checks (System.nanoTime) to detect debuggers and StackTrace analysis to prevent runtime patching.

✍️ Custom Watermark: Injects a custom signature field into every class (e.g., protected by obfus.jar), marking your territory.

# 👁️ Visual Transformation

# BEFORE: 

![Screenshot](https://github.com/AsoiX-exe/obfus.jar/blob/main/before.png)

# AFTER: 

![Screenshot](https://github.com/AsoiX-exe/obfus.jar/blob/main/after.png)

1. Logic Protection (Ghost Mode)
# BEFORE: Standard, readable Java code. Logic is clearly visible.

```Java

public boolean checkPassword(String password) {
    if (password.equals("admin123")) {
        System.out.println("Access Granted");
        return true;
    }
    return false;
}
```
# AFTER: Logic is gone. Replaced by InvokeCustoms calls. Decompilers fail to reconstruct the original flow.

```Java

public boolean checkPassword(String password) {
    // The logic is moved to a shadow method and called via invokedynamic
    return (boolean) InvokeCustoms.CallSite0_ghost$f32ebfa578634ebda37cd31913(this, password);
}

// The actual logic is hidden in synthetic methods like:
// private static synthetic void ghost$f32ebfa578634ebda37cd31913(...) { ... }
```
2. String Encryption
# BEFORE: Sensitive data (passwords, messages) are visible in plain text.

```Java

private static final String SECRET_KEY = "super_secret_key_8821";
```
# AFTER: Strings are replaced by decryption calls or dynamic stack construction. Strings search yields nothing.

```Java

private static final String SECRET_KEY = RuntimeHelper.decrypt("5e884898da...", "key");
// Or dynamically built:
// new String(new char[] { (char)115, (char)117, ... });
```
# 🛠️ Deep Technical Analysis: "Ghost Mode" & InvokeDynamic
One of the most advanced features of obfus.jar is the Ghost Transformation.

How it works:
Standard obfuscators just rename methods (a(), b()). obfus.jar changes how methods are called.

Shadowing: The real code is moved to a private static synthetic method with a randomized name (e.g., ghost$a1b2...).

Bootstrap Linking: The original call site is replaced by an invokedynamic instruction pointing to a custom Bootstrap Method inside GhostLoader.

Runtime Resolution: When the JVM executes the code, GhostLoader dynamically links the call to the shadow method.

Why is this effective?

Static Analysis: Decompilers like JD-GUI or Fernflower rely on standard invokevirtual / invokestatic flows. They cannot handle custom CallSite resolutions and often crash or show empty methods.

Patching: A cracker cannot simply "search and replace" instructions because the link between the caller and the callee is established only at runtime.

# 🚀 Installation and Usage
Download: Get the latest build from the Releases page.

Setup: Ensure you have Java 8 or higher installed.

Run: Launch obfus.jar.

Protect:

Drag & Drop your target JAR into the window.

Select your desired modules (Ghost Mode, Stack Strings, Trash Gen, etc.).

If you need to select specific classes for obfuscation, uncheck "all classes" and select those you need to obfuscate.

Click "Protect".

Result: A new file obfus_yourfile.jar will appear in the source directory.

# ⚠️ Requirements
Runtime: Java 8+ (Compatible with Java 17/21).

Libraries: Built on ASM (Bytecode Engineering) and FlatLaf (UI).

# 📝 License
This project is licensed under the MIT License. See the ![LICENSE](https://github.com/AsoiX-exe/obfus.jar/blob/main/LICENSE) file for details.

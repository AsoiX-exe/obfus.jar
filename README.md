# 🛡️ OBFUS.JAR | Java Obfuscator 

```text
    ██████╗ ██████╗ ███████╗██╗   ██╗███████╗     ██╗ █████╗ ██████╗ 
    ██╔══██╗██╔══██╗██╔════╝██║   ██║██╔════╝     ██║██╔══██╗██╔══██╗
    ██║  ██║██████╔╝█████╗  ██║   ██║███████╗     ██║███████║██████╔╝
    ██║  ██║██╔══██╗██╔══╝  ██║   ██║╚════██║██   ██║██╔══██╗██╔══██╗
    ██████╔╝██████╔╝██║     ╚██████╔╝███████║╚█████╔╝██║  ██║██║  ██║
    ╚═════╝ ╚═════╝ ╚═╝      ╚═════╝ ╚══════╝ ╚════╝ ╚═╝  ╚═╝╚═╝  ╚═╝
# Obfus.jar is an obfuscation tool designed to protect Java applications and Bukkit/Spigot plugins from reverse engineering. Using advanced assembly-based transformations, it makes decompiled code unreadable and causes analysis tools to fail.

#   🔥 Key Features
🔢 Mathematical Mutation: Transforms simple integers into complex mathematical expressions evaluated at runtime (XOR/AND/OR logic).

🛡️ Bitwise String Security: Encrypts strings using custom bitwise operations and hides them behind generated decryption methods.

🏗️ Stack-Based String Builder: Dynamically restores strings on the JVM stack, leaving no trace for static string pool scanners.

🌪️ Spaghetti Flow: Disrupts linear code execution using "flow bridges" and opaque predicates that mislead control flow graphs (CFGs).

💀 Anti-decompiler: Injects "dead sectors"—illegal bytecode sequences that cause tools like Fernflower or JD-GUI to hang or crash, while remaining valid for the JVM.

📂 Garbage Generation: Fills the JAR file with thousands of dummy classes and creates a deep folder structure to hide your real logic.

☃️ Signature: You can create your own watermark that will be displayed in each class (Example: protected by obfus.jar).

📸 Visual Transformation (Before & After)

1. Control Flow & Logic
Before: 

After: 

2. String Protection
Before: Hardcoded API keys or messages in the Constant Pool.

After: Encrypted arrays and stack-based reconstruction.

# 🛠️Deep Technical Analysis: The "lIl1llI" Renaming Strategy
One of the most effective psychological and technical barriers in obfus.jar is the Method & Variable Remapper.

How it works:
Instead of using descriptive names like calculatePremium() or checkLicense(), the obfuscator replaces them with combinations of l (lowercase L), I (uppercase i), and 1 (number one).

# Original Code:

public boolean checkUser(String name) { 
if (name.equals("admin")) { 
return true; 
} 
return false;
}

# Obfuscated Code:

public boolean lIl9281llI(String IIl102l) { 
if (IIl102l.equals(mllI11_8271(new int[]{...}))) { 
return true; 
} 
return false;
}
Why is this effective:
Visual Similarity: In many IDEs and text editors, l, I, and 1 look nearly identical. This causes "visual fatigue" for the reverse engineer.

Search Obstruction: Trying to "Find and Replace" or trace a specific variable becomes a nightmare when there are 500 variables named lIIl1ll, lIl1lIl, and llIl11I.

Analysis Delay: It breaks the mental model of the code. The researcher cannot quickly categorize what a method does based on its name.

# 🚀 Installation and Usage
Download: Download the latest version of obfus.jar from the releases section.

Run: Open obfus.jar without extracting it from the folder, because the folder contains important elements for the interface to work.

Protection: * Drag and drop your JAR file into the graphical interface.

Select protection modules (string encryption, mathematical expression manipulation, etc.).

Click "Protect."

Result: Your protected JAR file will be created in the same folder.

# ⚠️ Requirements
Runtime: Java 8 or higher.

# Libraries: Built on top of the powerful ASM library for bytecode manipulation and FlatLaf for a modern user interface.

# Created with ❤️ for the Java developer community.

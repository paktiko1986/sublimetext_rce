# Sublime Text Remote Code Execution (RCE) via Filename Command Injection

This repository contains a Proof of Concept (PoC) for a **Remote Code Execution (RCE)** vulnerability in Sublime Text (tested on an unregistered version on Linux).

## Vulnerability Details
*   **Vulnerability Type:** OS Command Injection (CWE-78) leading to RCE.
*   **Affected Software:** Sublime Text (Tested on Linux).
*   **Impact:** An attacker can craft a filename containing shell metacharacters (e.g., backticks `` ` ``). When a user opens the file and triggers the default Build command (Ctrl+B), arbitrary commands are executed with the user's privileges.

## Root Cause Analysis
When the default C/C++ build system is triggered (e.g., via `Ctrl+B`), Sublime Text passes the filename to the system compiler (like `gcc`). However, the filename is not properly escaped before being passed to the shell. 

If a filename contains command substitution characters like backticks (`` ` ``), the shell evaluates the embedded command *before* passing the (now modified) string to the compiler.

## Reproduction Steps
1.  **Create a Malicious File:**
    In a Linux terminal, create a file with a backtick payload:
    ```bash
    touch 'test`gnome-calculator`.c'
    ```

    ```bash
    touch 'test`id`.c'
    ```
    
2.  **Open in Sublime Text:**
    Open the newly created file in Sublime Text.
3.  **Trigger the Build:**
    Press **Ctrl+B** (or select `Tools -> Build`).
4.  **Observe the Result:**
    The GNOME Calculator application opens. Additionally, if you use `id` instead of `gnome-calculator`, the compiler output will display the user's UID/GID, proving command execution.

## Evidence
*(Lampirkan kedua screenshot yang Anda miliki di repositori GitHub ini)*

# Login Page

# webauthn

If **Passwords** are like a physical key (which can be copied, stolen, or lost), **WebAuthn** is like using your **biometric thumbprint or a hardware security key** to unlock a website.

Applying the **Pareto Principle**: You don't need to understand the complex cryptography (Public Key Infrastructure) to use it. You just need to know that it is a browser standard that allows users to log in without a password, using their device's built-in security.

---

### 1. The Core Concept: Public-Key Cryptography

WebAuthn works by creating a "Key Pair" for every website you visit.

* **The Private Key:** Stays on your device (phone, laptop, YubiKey). It is never sent over the internet. It is protected by your PIN or Biometrics.
* **The Public Key:** Sent to the website's server. It’s useless to hackers because it can only *verify* a login, not *initiate* one.

---

### 2. How the Flow Works (The "Secret Handshake")

Instead of sending a password, the server sends a "Challenge."

1. **Challenge:** The server says, "Hey, can you sign this random string of text with your private key?"
2. **User Consent:** Your phone pops up: "Use FaceID to log into `sensor-dashboard.com`?"
3. **The Signature:** Your device signs the text and sends the signature back.
4. **Verification:** The server uses your **Public Key** to verify the signature. If it matches, you're in.

---

### 3. Why it’s "Un-phishable"

This is the most important 20% of WebAuthn knowledge.
In a traditional phishing attack, a fake website (e.g., `googIe.com` with a capital 'i') tricks you into typing your password.

With WebAuthn, your device **checks the domain name itself**. If the domain doesn't perfectly match the one used during registration, your device will refuse to sign the challenge. The user doesn't even get the option to "override" it.

---

### 4. Comparison with Other Auth Methods

| Method | Security Level | User Friction | Vulnerable to Phishing? |
| --- | --- | --- | --- |
| **Passwords** | Low | High (Memorization) | **Yes** |
| **SMS OTP** | Medium | Medium (Waiting for code) | **Yes** (Sim-swapping) |
| **WebAuthn** | **High** | **Low (One touch)** | **No** |

---

### 5. Implementation for your Dashboard

Integrating WebAuthn from scratch is very difficult (the "80%" of complexity). To stay efficient, most developers use a library or a service:

* **Simple Route:** Use an IdP like **Auth0** or **Clerk**—they have a "Passkeys" toggle that enables WebAuthn with one click.
* **Self-Hosted Route:** Use a library like `@simplewebauthn/server` for your Node.js backend. It handles the heavy lifting of verifying the cryptographic signatures.

---

### Summary for Claude.code

> "I want to implement **Passkeys** using WebAuthn. Use the `@simplewebauthn` library. Store the `credentialID` and `publicKey` in our SQLite `users` table, and create an endpoint to generate the authentication challenge."

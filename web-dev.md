Here is your condensed, text-only **Web Developer Essentials Cheatsheet**, focusing strictly on how the modern web moves data and secures it.

---

### **1. HTTP/S (The Language of the Web)**

HTTP is a **Stateless, Request-Response** protocol. Every interaction is a "handshake" that starts fresh.

* **HTTP (Plaintext):** Data is sent as raw text. Dangerous for passwords/PII.
* **HTTPS (Secure):** The same HTTP text is wrapped in a **TLS** (Transport Layer Security) tunnel. It uses a "Handshake" to encrypt everything before it leaves your computer.
* **Status Codes (The "Short-hand"):**
* **200/201:** Success / Resource Created.
* **304:** Not Modified (Use your browser's local cache to save bandwidth).
* **400/404:** Client Error (Bad request format / URL doesn't exist).
* **401/403:** Auth Error (You aren't logged in / You don't have permission).
* **500/502:** Server Error (Code crashed / Backend service is down).



---

### **2. Identity & State (Cookies vs. Sessions)**

Since HTTP has no memory, we use these to "remember" who you are.

* **Cookies:** Small text files (4KB) stored **on your browser**. They are domain-locked; your browser only sends "Bank Cookies" to the "Bank Domain."
* **Sessions:** Data stored **on the server's memory**. The server gives you a "Session ID" (stored in a cookie). When you send that ID back, the server looks up your folder in its database.
* **JWT (JSON Web Token):** A digital "key" you store in your code. Unlike cookies, they aren't automatically sent by the browser; you must manually add them to the `Authorization` header.

---

### **3. Browser Security (CORS)**

**CORS (Cross-Origin Resource Sharing)** is a browser-enforced security check. It prevents a malicious site (Site A) from forcing your browser to use your private identity (Cookies) to talk to your Bank (Site B).

* **Origin:** The unique combo of `Protocol + Domain + Port`.
* **Pre-flight (OPTIONS):** For "dangerous" actions (POST, DELETE, JSON), the browser sends a hidden request first to ask the server for permission.
* **The Fix:** If you get a CORS error, it's usually a **Server Configuration** issue. The server must explicitly allow your domain in its headers.

---

### **4. Data Handling (JS Objects vs. JSON)**

* **JavaScript Object:** A "live" data structure in your code. It can hold logic, functions, and complex references.
* **JSON (String):** The "transport format." It is a strict text version of an object.
* `JSON.stringify(obj)`: Prepare data to be sent over the network.
* `JSON.parse(str)`: Turn data received from the network back into a usable object.



---

### **5. Fetch API (The Courier)**

The standard tool for sending HTTP requests. It uses **Promises** and works in two stages: First, you get the headers (to check if `response.ok`), then you download the body (using `response.json()`).

```javascript
// The 80/20 Pattern
async function callAPI() {
  const response = await fetch('https://api.example.com/data');
  if (response.ok) {
    const data = await response.json();
    return data;
  }
}

```

---

### **6. Networking Infrastructure (Proxies)**

* **Forward Proxy:** Sits in front of the **User**. Used to hide your IP or bypass CORS by making the request from a server instead of a browser.
* **Reverse Proxy (e.g., Nginx):** Sits in front of the **Server**. Used for Load Balancing (distributing traffic), SSL Termination (handling HTTPS), and security.
* **API Gateway:** A "Front Door" for APIs that handles rate-limiting (stopping spam) and checking your JWT tokens before your code even runs.

---

### **7. RESTful API Design (The Contract)**

* **Nouns, not Verbs:** Use `/users`, not `/getAllUsers`.
* **HTTP Verbs:** `GET` (Read), `POST` (Create), `PUT` (Update), `DELETE` (Remove).
* **OpenAPI:** A YAML/JSON file that documents every endpoint so teams can work together without guessing the URL structures.

---

**Would you like me to explain how "WebSockets" work next? They are the "always-on" alternative to the HTTP request-response model.**

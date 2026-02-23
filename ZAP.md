### 1. Choose Your Scan Method

Applying the **80/20 rule**, there are two ways to do this:

* **Automated Scan (Quick & Dirty):** Good for catching missing headers (Helmet) or old library vulnerabilities.
* **Authenticated Scan (Professional):** Necessary for your app. It tells ZAP, "Use this JWT cookie so you can test the RBAC and Sensor routes."

---

### 2. Step-by-Step: The Authenticated Scan

#### Step 1: Install and Launch

Download [OWASP ZAP](https://www.zaproxy.org/download/). When you open it, choose **"No, I do not want to persist this session at this moment"** to keep things fast for your first test.

#### Step 2: Manual Explore (Teaching ZAP your App)

ZAP needs to see your app in action to understand the routes.

1. Click **"Manual Explore"** on the main screen.
2. Enter your local URL (e.g., `http://localhost:5173`).
3. Click **"Launch Browser"**.
4. **Crucial:** In the browser window that ZAP opens, **log in to your dashboard.** Navigate through your sensor list, click "Edit," and "Update."
* *Why?* This lets ZAP "record" the JWT cookies and CSRF tokens being used.



#### Step 3: Context & Session (The "Identity" Setup)

Now that ZAP has seen your traffic:

1. In the left "Sites" tree, right-click your URL and select **Include in Context -> Default Context**.
2. Go to the **HTTP Sender** tab at the bottom, find a request that has your `Authorization` header or `JWT` cookie.
3. Right-click it -> **Flag as Context -> Default Context: Authentication ID**.

#### Step 4: Attack (Active Scan)

1. Right-click your site in the tree again.
2. Select **Attack -> Active Scan**.
3. ZAP will now try to perform SQL injection, Cross-Site Scripting (XSS), and Path Traversal on every route it found.

---

### 3. Understanding the Results (The "Alerts" Tab)

ZAP will flag issues with a color-coded system:

* 🔴 **High:** SQL Injection or XSS (Fix immediately).
* 🟡 **Medium:** Missing CSRF tokens or vulnerable cookies.
* 🔵 **Low:** Missing security headers (like the ones `helmet` provides).

---

### 4. Running ZAP in your Docker Pipeline

Since you are learning containerization, you can run ZAP as a "One-off" container to scan your app without installing the GUI.

**Run this command while your app is running:**

```bash
docker run -t ghcr.io/zaproxy/zaproxy:stable zap-full-scan.py -t http://localhost:3000

```

*(Note: Replace `localhost` with your computer's IP if running from within Docker).*

---

### 5. Common "Fails" for your Dashboard

Because of the architecture we discussed, ZAP will likely complain about:

1. **Missing Secure/HttpOnly flags:** If you haven't enabled HTTPS yet.
2. **Information Disclosure:** If your Express errors show "Stack Traces."
3. **CORS Misconfiguration:** If your CORS policy is too wide (`origin: '*'`).

### Summary Checklist

| Step | Action | Why? |
| --- | --- | --- |
| **Proxy** | Manual Explore | To record the "happy path" of your app. |
| **Auth** | Flag JWT/Cookie | So ZAP can scan routes behind the `requireAuth` wall. |
| **Spider** | Run Spider | To find hidden files or folders you forgot about. |
| **Active Scan** | Run Active Scan | To simulate actual attacks (SQLi, XSS). |

**Would you like me to help you fix a specific "High" or "Medium" alert that ZAP found, or should we look at how to automate this scan every time you push code?**

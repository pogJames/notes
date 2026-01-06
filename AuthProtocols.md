# Authentication Methods
> Authentication methods verify a user's identity before granting access to systems, applications, or resources

### Basics Authentications
1. **Basic** ~ Username/password sent in every request (Base64 encoded)
-  Simple but insecure — like shouting your credentials across the room
2. **Form** ~ Login page(username/password submitted via HTML form)
- Server creates a session (stored on server) and sends a cookie to your browser for future requests

> [!NOTE]
> These are single-app only — you log in separately for every site/app

### Adding Security Layers
**MFA** ~ extra layer on top of anything (password + app code, biometric, hardware key)
> Makes stolen passwords much less useful

### Improving User Experience
**SSO** ~ Log in once and access multiple apps/sites without re-logging in
- OAuth 2.0 **Authorization framework** — lets an app access your data on another service without sharing your password
> Uses access tokens (often JWTs). Secure flows with auth code + PKCE
- OIDC **Authentication layer** built on top of OAuth 2.0 (Adds an ID token)
> Perfect for "Sign in with Google/Apple" — gives both login (who you are) and access
- SAML 2.0 **Authentication protocol** XML-based for enterprise SSO
> Exchanges signed "assertions" between Identity Provider (IdP) and Service Provider (SP)
- JWT **token** compact, signed token containing user info/claims -> Stateless - no server session needed
> Often used for APIs, microservices, or as tokens in other protocols

## Basic Authentication (Base64 Encoding)
1. Strengths: Super simple to implement; no sessions or cookies needed.
2. Weaknesses: Credentials sent with every request (even Base64 is not encrypted); very insecure over HTTP; no built-in logout.
3. Opportunities: Rare use in internal APIs with HTTPS.
4. Threats: Highly vulnerable to interception, replay attacks, and credential stuffing; deprecated in modern apps.
```mermaid
sequenceDiagram
    participant Client
    participant Server
    participant DB as Database

    Note over Client: Combine username:password<br>Base64-encode it
    Client->>Server: HTTP Request<br>Authorization: Basic <base64-string>
    Server->>Server: Decode Base64 to get credentials
    Server->>DB: Verify username & hashed password
    DB-->>Server: Valid / Invalid
    alt Valid
        Server-->>Client: 200 OK + Resource
    else Invalid
        Server-->>Client: 401 Unauthorized<br>WWW-Authenticate: Basic
    end
```

## Form-Based Authentication
1. Strengths: Familiar to users (login page); easy to customize; works with sessions/cookies.
2. Weaknesses: Session hijacking risks (cookie theft); server-side state needed; prone to CSRF without protection.
3. Opportunities: Still common in traditional web apps; easy to add MFA.
4. Threats: Phishing and brute-force attacks; being replaced by token-based systems.
```mermaid
sequenceDiagram
    participant User
    participant Browser
    participant Server
    participant DB as Database

    User->>Browser: Enter username/password in form
    Browser->>Server: POST /login (credentials)
    Server->>DB: Verify credentials
    DB-->>Server: Valid / Invalid
    alt Valid
        Server-->>Browser: 302 Redirect + Set-Cookie: sessionID
        Browser->>Server: GET protected resource (with Cookie)
        Server-->>Browser: 200 OK + Resource
    else Invalid
        Server-->>Browser: Login page + Error
    end
```

## Multi-Factor Authentication
1. Strengths: Greatly improves security (something you know + have/are).
2. Weaknesses: Adds friction for users; can be bypassed (e.g., phishing for codes).
3. Opportunities: Essential today; integrates easily with most systems; push toward passwordless.
4. Threats: Social engineering or SIM-swapping attacks; user resistance to extra steps.
```mermaid
sequenceDiagram
    participant User
    participant Client
    participant Server
    participant MFA as MFA Service

    Client->>Server: Submit username & password
    Server->>Server: Verify password
    alt Password Valid
        Server->>MFA: Request second factor (e.g., send push/generate code)
        MFA->>User: Deliver code/push notification
        User->>Client: Enter MFA code/approve
        Client->>Server: Submit MFA response
        Server->>MFA: Validate
        MFA-->>Server: Valid
        Server-->>Client: Auth Success + Session/Token
    else Invalid
        Server-->>Client: Auth Failed
    end
```

## Single Sign-On
1. Strengths: Huge convenience—one login for many apps; better user experience.
2. Weaknesses: Single point of failure (if IdP compromised, all apps at risk).
3. Opportunities: Widely adopted in enterprises and cloud services.
4. Threats: Centralized attack target; dependency on identity provider uptime.
```mermaid
sequenceDiagram
    participant User as User/Browser
    participant SP as Service Provider (App)
    participant IdP as Identity Provider

    User->>SP: Access protected resource
    SP->>User: Redirect to IdP with AuthnRequest
    User->>IdP: AuthnRequest (authenticate if needed)
    IdP->>User: Login page (if no session)
    User->>IdP: Submit credentials
    alt Auth Success
        IdP->>User: POST SAML Assertion to SP ACS
        User->>SP: SAML Response (Assertion)
        SP->>SP: Validate Assertion
        SP-->>User: Grant Access + Resource
    else Auth Failed
        IdP-->>User: Error
    end
```
  
## OAuth
1. Strengths: Secure delegated access without sharing passwords; flexible flows.
2. Weaknesses: Complex to implement correctly; vulnerable if flows misused.
3. Opportunities: Foundation for modern API access; evolving standards (e.g., OAuth 2.1).
4. Threats: Token theft or misconfiguration leaks; phishing for auth codes.
```mermaid
sequenceDiagram
    participant User
    participant Client as Client App
    participant Auth as Authorization Server
    participant RS as Resource Server (API)

    User->>Client: Initiate login/access
    Client->>Auth: Redirect to /authorize (code, PKCE, scopes)
    Auth->>User: Login + Consent
    User->>Auth: Credentials + Approve
    Auth->>Client: Redirect with auth code
    Client->>Auth: POST code + PKCE verifier for access/refresh token
    Auth-->>Client: Access Token + Refresh (optional ID Token if OIDC)
    Client->>RS: Request with Bearer Access Token
    RS-->>Client: Protected Resource
```

## OIDC
1. Strengths: Adds reliable authentication to OAuth; modern, mobile-friendly; great for "Login with...".
2. Weaknesses: Still complex; relies on proper HTTPS and token handling.
3. Opportunities: Preferred for new apps; excellent libraries and support.
4. Threats: Growing attack surface as adoption increases; potential for ID token forgery if keys compromised.
```mermaid
sequenceDiagram
    participant User
    participant RP as Relying Party (Client)
    participant OP as OpenID Provider

    User->>RP: Access app
    RP->>OP: Redirect to /authorize (scope=openid profile email, nonce, etc.)
    OP->>User: Authenticate (username/pw/MFA)
    User->>OP: Submit + Consent
    OP->>RP: Redirect with auth code
    RP->>OP: POST code for ID Token + Access Token
    OP-->>RP: ID Token (JWT with user claims) + Access Token
    RP->>RP: Validate ID Token signature/nonce/expiry
    Note right of RP: User authenticated
```

## SAML
1. Strengths: Robust enterprise SSO; strong security with XML signatures.
2. Weaknesses: Verbose and complex (XML-heavy); poor for mobile/SPAs.
3. Opportunities: Still dominant in legacy enterprise environments.
4. Threats: Being phased out for newer apps; high setup cost drives shift to OIDC.
```mermaid
sequenceDiagram
    participant User
    participant SP as Service Provider
    participant IdP as Identity Provider

    User->>SP: Access protected resource
    SP->>IdP: Redirect with AuthnRequest (signed XML)
    IdP->>User: Login form (if not already authenticated)
    User->>IdP: Credentials + MFA
    IdP->>SP: POST SAML Assertion (signed XML with user attributes)
    SP->>SP: Validate signature, attributes, conditions
    SP-->>User: Grant access (session cookie)
```
## JWT
1. Strengths: Stateless, compact, self-contained; perfect for APIs and microservices.
2. Weaknesses: Can't easily revoke without extra systems; size can grow with claims.
3. Opportunities: Core of modern token-based auth (used in OIDC/OAuth).
4. Threats: Algorithm confusion attacks or weak keys; no built-in encryption for sensitive data.
```mermaid
sequenceDiagram
    participant Client
    participant Server

    Client->>Server: POST /login with credentials
    Server->>Server: Validate creds
    Server-->>Client: JWT (signed token)
    Client->>Server: Subsequent requests with Authorization: Bearer <JWT>
    Server->>Server: Verify signature, expiry, claims
    alt Valid
        Server-->>Client: Resource / Success
    else Invalid
        Server-->>Client: 401 Unauthorized
    end
```

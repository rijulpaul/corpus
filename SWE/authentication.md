# What is Authentication?
Authentication = proving who a user is
Authorization = deciding what they can access

# Core Authentication Models
## Session-based Authentication
- Classic Model (Cookies + Server Memory/Database)
- Flow
    - User logs in - Server verifies credentials
    - Server creates a session ID stored in DB or in-memory (Redis)
    - Server sends session ID to client (stored in HTTP-only cookie)
    - Each requrest sends cookie - server validdates session.
- Pros - Secure, works well with server rendered apps. East to invalidate sessions.
- Cons - Doesn't scale easily across multiple servers (need distributed session store)

## Token-based Authentication
- Server doesn't store sessions, instead issues tokens (usually JWTs).
- Flow
    - User logs in
    - Server issues a JWT (signed, optionally encrypted)
    - Client stores JWT (localStorage, cookie, etc.).
    - Client sends JWT in Authorization: Bearer \<token\>
    - Server verifies signature (using secret or public key)
- Pros - Scales well, no session store needed, works across services (microservices, APIs)
- Cons - Revoking tokens is harder, token bloat, security pitfalls if poorly implemented

## OAuth 2.0 + OpenID Connect
- Delegated auth used for "Login with Google/GitHub/etc."
- OAuth 2.0 - Authorization framework (accessing resources on behald of user)
- OIDC - extension for authentication (who the user is)
- Pros - Don't have to share credentials directly with the application and easier to manage session and content access. 

## API keys
- Static secret string identifying client.
- Usually for service-to-service authentication.
- Stored in headers or query params (better in headers).
- Must be stored securely on backend (never in frontend code).
- Weakest form (no rotation, no user context, no expiration by default).

# Credential Types
- Passwords - Never store raw. Use slow hashing algorithm
    - Argon2id (best), bcrypt or scrypt
    - Always use salts
- Magic Links / One-Time Codes - Email/SMS based login
- MFA/2FA - TOTP (Google Authenticator), SMS OTP, WebAuthn
- Certificates/Mutual TLS - Machine-to-machine auth

# Best Practice for Password Authentication
- Enforce minimum complexity
- Rate limit login attempts
- Use password hashing libraries
- Never log credentials
- Use HTTPS only

# Practical Stack Choices
- Sessions: Express + express-session + Redis.
- JWT: jsonwebtoken (Node), djangorestframework-simplejwt (Django).
- OAuth: Auth0, Keycloak, FusionAuth, AWS Cognito, custom OIDC.
- MFA: TOTP libs (speakeasy, otplib), WebAuthn libs.
- Rate Limiting: Nginx, API Gateway, or libraries like rate-limiter-flexible.

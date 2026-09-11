# Single Sign-On (SSO) Integration

PixelView supports enterprise **Single Sign-On (SSO)** using industry-standard **OpenID Connect (OIDC)** and **OAuth 2.0** protocols. By integrating with **Dex**—an enterprise-grade federated identity broker—PixelView enables organization members to authenticate seamlessly using their existing corporate identity providers (IdPs), including **Keycloak**, **Okta**, **Microsoft Entra ID (Azure AD)**, **Google Workspace**, **GitHub**, and corporate **LDAP / Active Directory**.

---

## Architecture & Authentication Flow

PixelView delegates federated authentication to Dex, ensuring secure, token-based authorization code flow with backend-level verification and session management:

```
+---------------+              1. Click SSO Login (POST /api/ssologin)             +-------------------+
|               | ---------------------------------------------------------------> |                   |
|               |                                                                  | PixelView Backend |
|   End User    | <--------------------------------------------------------------- |  (API & Session)  |
|   (Browser)   |                 2. HTTP 302 Redirect to IdP / Dex                +-------------------+
|               |                                                                            ^
+---------------+                                                                            |
        |                                                                                    |
        | 3. Authenticate with Corporate Credentials & MFA                                   |
        v                                                                                    |
+---------------+                                                                            |
|  Identity     |                                                                            |
|  Provider     |                                                                            |
|  (Dex / IdP)  |                                                                            |
+---------------+                                                                            |
        |                                                                                    |
        | 4. Redirect with Auth Code (GET /api/auth/callback?code=...&state=...)             |
        +------------------------------------------------------------------------------------+
                                                |
                                5. Backend validates code with Dex/IdP,
                                   creates/syncs user in MongoDB, and
                                   issues secure HttpOnly session cookie
                                                |
        +---------------------------------------+
        |
        | 6. HTTP 302 Redirect to /sso-login-success
        v
+-------------------+                                                              +-------------------+
|   PixelView Web   |                     7. GET /api/profile                      | PixelView Backend |
|     Frontend      | -----------------------------------------------------------> |  (API & Session)  |
| (/sso-login-succ) | <----------------------------------------------------------- |                   |
+-------------------+                   8. Session Validated                       +-------------------+
        |
        | 9. Redirect to Dashboard (/openstack)
        v
+-------------------+
| Active Session    |
+-------------------+
```

### Key Flow Characteristics
1. **Direct Backend Handshake**: Clicking **SSO Login** initiates an HTTP POST to `/api/ssologin`. The browser is redirected directly to Dex/IdP.
2. **Secure Callback Endpoint**: The identity provider redirects back to the backend's dedicated callback endpoint (`/api/auth/callback`). Authorization codes and tokens are exchanged on the backend network, never exposed to client-side scripts.
3. **Automated Session Handoff**: Once verified, the backend issues an authenticated HTTP session cookie and forwards the browser to `/sso-login-success`. The frontend confirms the session via `/api/profile` and routes the user directly to the primary operational dashboard.

---

## Prerequisites

Before setting up SSO in PixelView, ensure you have:
* A running **Dex** instance deployed and operated by your organization (acting as the federated identity broker to your upstream corporate IdPs).
* Administrator privileges on your Identity Provider to register applications and configure redirect URIs.
* A fully qualified domain name (FQDN) secured with HTTPS for your PixelView deployment (e.g., `https://cloud.pixelvirt.com`).
* The mandatory PixelView OAuth Callback URL:
  ```text
  https://<your-pixelview-domain>/api/auth/callback
  ```

---

## Dex Federated Identity Broker Setup

PixelView is engineered to authenticate against Dex, which translates upstream identity credentials from any provider into standardized OIDC identity tokens.

### Sample Dex Configuration (`dex.yaml`)

Below is a representative Dex configuration illustrating how upstream identity providers map to PixelView:

```yaml linenums="1"
# The base URL where Dex is publicly accessible
issuer: https://dex.yourdomain.com/dex

# Storage backend for storing Dex state
storage:
  type: memory # or kubernetes / sqlite3 / mongo

# Network binding
web:
  http: 0.0.0.0:5556

# Registered OAuth2/OIDC clients
staticClients:
  - id: pixelview
    redirectURIs:
      - 'https://<your-pixelview-domain>/api/auth/callback'
    name: 'PixelView Cloud Management'
    secret: 'your-secure-dex-client-secret'

# Upstream Identity Provider Connectors
connectors:
  # Example 1: Google Workspace / Gmail
  - type: google
    id: google
    name: Google
    config:
      clientID: $GOOGLE_CLIENT_ID
      clientSecret: $GOOGLE_CLIENT_SECRET
      redirectURI: https://dex.yourdomain.com/dex/callback

  # Example 2: Keycloak / Generic OIDC
  - type: oidc
    id: keycloak
    name: Keycloak
    config:
      issuer: https://sso.yourdomain.com/realms/organization
      clientID: $KEYCLOAK_CLIENT_ID
      clientSecret: $KEYCLOAK_CLIENT_SECRET
      redirectURI: https://dex.yourdomain.com/dex/callback
      scopes:
        - openid
        - profile
        - email

  # Example 3: GitHub / GitHub Enterprise
  - type: github
    id: github
    name: GitHub
    config:
      clientID: $GITHUB_CLIENT_ID
      clientSecret: $GITHUB_CLIENT_SECRET
      redirectURI: https://dex.yourdomain.com/dex/callback
      orgs:
        - name: your-organization

  # Example 4: Microsoft Entra ID (Azure AD)
  - type: microsoft
    id: microsoft
    name: Microsoft
    config:
      clientID: $MICROSOFT_CLIENT_ID
      clientSecret: $MICROSOFT_CLIENT_SECRET
      redirectURI: https://dex.yourdomain.com/dex/callback
      tenant: $AZURE_TENANT_ID

  # Example 5: LDAP / Microsoft Active Directory
  - type: ldap
    id: ldap
    name: Active Directory
    config:
      host: ldap.yourdomain.com:636
      insecureNoSSL: false
      bindDN: "cn=admin,dc=example,dc=com"
      bindPW: "admin_password"
      userSearch:
        baseDN: "ou=Users,dc=example,dc=com"
        filter: "(objectClass=person)"
        username: mail
        idAttr: DN
        emailAttr: mail
        nameAttr: cn
```

> [!IMPORTANT]
> * The `redirectURIs` registered under `staticClients` in Dex **must** point to PixelView's backend callback endpoint: `https://<your-pixelview-domain>/api/auth/callback`.
> * Retain `id: pixelview` as the client identifier matching PixelView's internal OIDC client configuration.

---

## Configuring PixelView Backend

To link PixelView to your Dex or OIDC identity provider, update the environment variables for the `pixelview-backend` container in your deployment configuration (`docker-compose.yml` or `.env` file):

```yaml linenums="1"
services:
  pixelview-backend:
    image: ghcr.io/pixelvirt/pixelview-backend:v0.0.1
    container_name: pixelview-backend
    restart: always
    network_mode: host
    environment:
      # Service Authentication Key
      AUTH_KEY: 6c673f51-6045-47b0-8745-eef9d165a310
      
      # OpenID Connect / Dex Configuration
      DEX_ISSUER_URL: https://dex.yourdomain.com/dex
      DEX_REDIRECT_URI: https://<your-pixelview-domain>/api/auth/callback
      
      # Database and Domain Settings
      MONGO_URI: mongodb://localhost:27017/alertagility
      DOMAIN: yourdomain.com
      SUBDOMAIN: alertagility
      ENVIRONMENT: production
```

### Configuration Parameters

| Environment Variable | Requirement | Description |
| :--- | :--- | :--- |
| `DEX_ISSUER_URL` | **Required** | The base issuer URL of the Dex instance hosted by your organization (must serve `/.well-known/openid-configuration`, e.g., `https://dex.yourdomain.com/dex`). |
| `DEX_REDIRECT_URI` | **Required** | The fully qualified callback endpoint where the browser returns after authentication (`https://<your-pixelview-domain>/api/auth/callback`). |
| `AUTH_KEY` | **Required** | Internal microservice communication authentication token. |
| `DOMAIN` / `SUBDOMAIN` | Optional | Contextual domain definitions used for cookie scoping and multi-tenant URL generation. |

Restart the backend container to apply the new configuration:
```bash
docker compose restart pixelview-backend
```

---

## User Provisioning & Permissions

PixelView implements Just-In-Time (JIT) user provisioning for SSO logins:

* **Automatic Account Creation**: When an authenticated user signs in via SSO for the first time, PixelView automatically provisions a new account record in the database using the verified `email` and identity claims.
* **Default Role Assignment**: Newly provisioned SSO accounts are initially granted the standard `User` role with scoped baseline access.
* **Access Elevation & Governance**: System administrators can elevate any SSO user to `Admin` or grant fine-grained module privileges (OpenStack, Kubernetes, Inventory, Patch Management, Automation) via the [Granular Permissions Matrix](../management/user-management.md#granular-permissions-matrix) located in **Management** &rarr; **Users**.

---

## End-User Login Experience

Once SSO is configured, users authenticate using their standard corporate workflow:

1. Open your browser and navigate to the PixelView login portal (`/login`).
2. Locate the **Continue with SSO** section beneath the primary login form and click **SSO Login**.
3. You will be redirected to your corporate identity provider portal (or Dex connector selector).
4. Enter your corporate credentials and complete any mandatory Multi-Factor Authentication (MFA) challenges.
5. Upon successful authentication, your browser is redirected back through `/api/auth/callback` and `/sso-login-success`, landing directly on your active operational dashboard.

---

## Troubleshooting

| Symptom / Error | Probable Cause | Recommended Resolution |
| :--- | :--- | :--- |
| **`Invalid redirect_uri` on IdP** | The redirect URI in Dex or IdP does not match `https://<domain>/api/auth/callback`. | Ensure the callback URL in your client configuration matches `https://<your-domain>/api/auth/callback` with exact spelling, port, and `https://` protocol. |
| **User lands on `/sso-login-success` but remains unauthenticated** | The session cookie was rejected by the browser due to protocol or domain mismatch. | Ensure your reverse proxy (e.g. Nginx) passes `X-Forwarded-Proto: https` and `X-Forwarded-For` headers to `pixelview-backend`. |
| **`Token verification failed`** | Clock skew between the PixelView host and Dex server. | Synchronize system clocks on both servers using NTP (`chrony` or `systemd-timesyncd`). |
| **Missing user email attribute** | Upstream Identity Provider is not releasing the `email` scope to Dex. | Verify that the `email` and `profile` scopes are enabled in the upstream IdP client configuration. |
| **Dex Issuer Discovery Error** | PixelView backend cannot reach `DEX_ISSUER_URL/.well-known/openid-configuration`. | Test connectivity from inside the `pixelview-backend` container: <br> `docker exec -it pixelview-backend curl -k https://dex.yourdomain.com/dex/.well-known/openid-configuration`. |

# Single Sign-On (SSO) Integration

PixelView supports enterprise **Single Sign-On (SSO)** using industry-standard **OpenID Connect (OIDC)** and **OAuth 2.0** protocols. Integrating SSO allows team members to authenticate seamlessly using their existing corporate identity providers (IdP) such as **Keycloak**, **Okta**, **Microsoft Entra ID (Azure AD)**, and **Google Workspace**.

---

## 1. Architecture & Authentication Flow

PixelView leverages standard authorization code flow with PKCE:

```
+----------------+          1. Click SSO Login          +-------------------+
|                | -----------------------------------> |                   |
|                |                                      |   PixelView Web   |
|   End User     | <----------------------------------- |     Frontend      |
|                |    2. Redirect to IdP Auth URL       +-------------------+
+----------------+                                                |
        |                                                         |
        | 3. Authenticate with IdP Credentials                    |
        v                                                         |
+-------------------+                                             |
|  Identity Provider|                                             |
| (Keycloak / Okta /|                                             |
|   Microsoft / ...) |                                            |
+-------------------+                                             |
        |                                                         |
        | 4. Redirect with Auth Code to /sso-login-success        |
        +-------------------------------------------------------->+
                                                                  |
                                                5. Exchange Code for JWT Session
                                                                  v
                                                        +-------------------+
                                                        | PixelView Backend |
                                                        +-------------------+
```

---

## 2. Prerequisites

Before configuring SSO in PixelView, ensure you have:
* Administrator access to your corporate Identity Provider (IdP).
* A fully qualified domain name (FQDN) configured with HTTPS for your PixelView instance.
* Your PixelView SSO Callback / Redirect URI:
  ```
  https://<your-pixelview-domain>/sso-login-success
  ```

---

## 3. Identity Provider (IdP) Configuration

### Step 1: Register an OIDC Application / Client
In your IdP administration console (e.g. Keycloak or Okta):

1. Create a new **OpenID Connect (OIDC)** client.
2. Set the **Client Type** to `Confidential` (or `Web App`).
3. Set the **Valid Redirect URIs** (or Allowed Callback URLs) to:
   ```
   https://<your-pixelview-domain>/sso-login-success
   ```
4. Set the **Allowed Web Origins** to your base domain:
   ```
   https://<your-pixelview-domain>
   ```
5. Ensure the standard OIDC scopes are granted:
   * `openid`
   * `email`
   * `profile`
6. Copy the generated **Client ID** and **Client Secret**.

---

## 4. Configuring PixelView Backend

Set the following environment variables in your PixelView backend deployment configuration (`.env` or Docker Compose):

```bash
# Enable SSO Authentication
SSO_ENABLED=true

# OpenID Connect Configuration
OIDC_ISSUER_URL=https://idp.yourdomain.com/realms/pixelvirt
OIDC_CLIENT_ID=pixelview-client
OIDC_CLIENT_SECRET=your_super_secret_client_key_here
OIDC_REDIRECT_URI=https://pixelview.yourdomain.com/sso-login-success

# Scopes (default: openid email profile)
OIDC_SCOPES=openid email profile
```

Restart the PixelView backend service to apply the new configuration:
```bash
docker compose restart backend
```

---

## 5. User Provisioning & Permissions

When a user logs in via SSO for the first time:
* **Automatic Account Creation**: PixelView automatically creates a user account using the `email`, `given_name` (First Name), and `family_name` (Last Name) claims received from the identity provider.
* **Default Role Assignment**: New SSO users are assigned the default `User` role with initial scoped permissions.
* **Role Elevation**: Administrators can upgrade roles to `Admin` or customize the [Granular Permissions Matrix](../management/user-management.md#4-granular-permissions-matrix) at any time from the **Management > Users** page.

---

## 6. End-User Login Experience

1. Navigate to the PixelView login page (`/login`).
2. Click the **Single Sign-On (SSO)** button under the login form.
3. Complete authentication on your organization's identity portal.
4. You will be automatically redirected to `/sso-login-success` and then into your PixelView dashboard.

---

## 7. Troubleshooting

| Issue / Error | Potential Cause | Solution |
| :--- | :--- | :--- |
| `Invalid redirect URI` | The Redirect URI in the IdP does not exactly match `https://<domain>/sso-login-success`. | Check trailing slashes and ensure protocol is `https://`. |
| `Token verification failed` | Issuer URL mismatch or clock skew between servers. | Ensure server system clocks are synchronized via NTP and `OIDC_ISSUER_URL` matches the IdP's `iss` claim. |
| `Missing email claim` | The IdP client is not releasing user email attributes. | Add the `email` scope to your client mapper in the IdP console. |

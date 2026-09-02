# Single Sign-On (SSO) Integration

PixelView supports enterprise **Single Sign-On (SSO)** using industry-standard **OpenID Connect (OIDC)** and **OAuth 2.0** protocols. Integrating SSO allows team members to authenticate seamlessly using their existing corporate identity providers (IdP) such as **Keycloak**, **Okta**, **Microsoft Entra ID (Azure AD)**, and **Google Workspace**.

---

## Architecture & Authentication Flow

PixelView leverages standard authorization code flow with PKCE:

```
+----------------+            Click SSO Login           +-------------------+
|                | -----------------------------------> |                   |
|                |                                      |   PixelView Web   |
|   End User     | <----------------------------------- |     Frontend      |
|                |       Redirect to IdP Auth URL       +-------------------+
+----------------+                                                |
        |                                                         |
        | Authenticate with IdP Credentials                       |
        v                                                         |
+-------------------+                                             |
|  Identity Provider|                                             |
| (Keycloak / Okta /|                                             |
|   Microsoft / ...) |                                            |
+-------------------+                                             |
        |                                                         |
        | Redirect with Auth Code to /sso-login-success           |
        +-------------------------------------------------------->+
                                                                  |
                                                    Exchange Code for JWT Session
                                                                  v
                                                        +-------------------+
                                                        | PixelView Backend |
                                                        +-------------------+
```

---

## Prerequisites

Before configuring SSO in PixelView, ensure you have:
* Administrator access to your corporate Identity Provider (IdP).
* A fully qualified domain name (FQDN) configured with HTTPS for your PixelView instance.
* Your PixelView SSO Callback / Redirect URI:
  ```
  https://<your-pixelview-domain>/sso-login-success
  ```

---

## Identity Provider (IdP) Configuration

### Register an OIDC Application / Client
In your IdP administration console (e.g. Keycloak or Okta):

* Create a new **OpenID Connect (OIDC)** client.
* Set the **Client Type** to `Confidential` (or `Web App`).
* Set the **Valid Redirect URIs** (or Allowed Callback URLs) to:
  ```
  https://<your-pixelview-domain>/sso-login-success
  ```
* Set the **Allowed Web Origins** to your base domain:
  ```
  https://<your-pixelview-domain>
  ```
* Ensure the standard OIDC scopes are granted:
  * `openid`
  * `email`
  * `profile`
* Copy the generated **Client ID** and **Client Secret**.

---

## Configuring PixelView Backend

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

## User Provisioning & Permissions

When a user logs in via SSO for the first time:
* **Automatic Account Creation**: PixelView automatically creates a user account using the `email`, `given_name` (First Name), and `family_name` (Last Name) claims received from the identity provider.
* **Default Role Assignment**: New SSO users are assigned the default `User` role with initial scoped permissions.
* **Role Elevation**: Administrators can upgrade roles to `Admin` or customize the [Granular Permissions Matrix](../management/user-management.md#granular-permissions-matrix) at any time from the **Management > Users** page.

---

## End-User Login Experience

* Navigate to the PixelView login page (`/login`).
* Click the **Single Sign-On (SSO)** button under the login form.
* Complete authentication on your organization's identity portal.
* You will be automatically redirected to `/sso-login-success` and then into your PixelView dashboard.

---

## Troubleshooting

| Issue / Error | Potential Cause | Solution |
| :--- | :--- | :--- |
| `Invalid redirect URI` | The Redirect URI in the IdP does not exactly match `https://<domain>/sso-login-success`. | Check trailing slashes and ensure protocol is `https://`. |
| `Token verification failed` | Issuer URL mismatch or clock skew between servers. | Ensure server system clocks are synchronized via NTP and `OIDC_ISSUER_URL` matches the IdP's `iss` claim. |
| `Missing email claim` | The IdP client is not releasing user email attributes. | Add the `email` scope to your client mapper in the IdP console. |

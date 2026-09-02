# Authentication & Login

PixelView provides a secure authentication portal supporting local database credentials and Single Sign-On (SSO) enterprise identity providers.

---

## Default Credentials

On a newly deployed PixelView instance, use the initial administrative credentials to log in:

* **Email / Username**: `admin@localhost`
* **Password**: `password`

!!! warning "Change Default Password"
    It is critical to change the default `admin@localhost` password immediately after your first login by navigating to **Settings** > **Profile** > **Change Password**.

---

## Standard Login Procedure

* Open your browser and navigate to your PixelView host URL (e.g., `https://pixelview.yourdomain.com/login`).
* Enter your registered **Email** address.
* Enter your **Password** (click the **Eye Icon** to toggle password visibility).
* Click **Sign In**.

Upon successful authentication, the system creates an encrypted session token and redirects you to the main dashboard.

---

## Single Sign-On (SSO)

If your organization has integrated an enterprise identity provider (such as Keycloak, Okta, Microsoft Entra ID, or Google Workspace):

* On the login page, click the **Single Sign-On (SSO)** button.
* You will be redirected to your organization's identity provider portal.
* Authenticate using your corporate credentials and multi-factor authentication (MFA).
* Once verified, you will be redirected back to PixelView with an authenticated session.

For identity provider configuration details, see [SSO Integration Guide](../management/sso-integration.md).

---

## Password Recovery & Reset

If you have forgotten your password:

* Click the **Forgot password?** link on the login page (navigates to `/forgot-password`).
* Enter your registered email address and click **Send Reset Link**.
* Check your email for a secure password reset link.
* Click the link to open the **Reset Password** page (`/reset-password`).
* Enter your **New Password** and **Confirm Password** (minimum 8 characters with letters, numbers, and symbols).
* Click **Reset Password** to complete the update.

---

## Logging Out

To end your active session and log out of PixelView:

* Click the **Logout** button located at the bottom of the left navigation sidebar:
  <a href="../../images/logout.png" class="glightbox">
    <img src="../../images/logout.png" alt="Logout Navigation Sidebar">
  </a>
* When triggered, PixelView:
  * Revokes the active session token via the backend API.
  * Clears all local application states, session storage, and cached data.
  * Redirects you securely back to the login portal.
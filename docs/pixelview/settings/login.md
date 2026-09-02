# Authentication & Login

PixelView provides a secure authentication portal supporting local database credentials and Single Sign-On (SSO) enterprise identity providers.

---

## 1. Default Credentials

On a newly deployed PixelView instance, use the initial administrative credentials to log in:

* **Email / Username**: `admin@localhost`
* **Password**: `password`

!!! warning "Change Default Password"
    It is critical to change the default `admin@localhost` password immediately after your first login by navigating to **Settings** > **Profile** > **Change Password**.

---

## 2. Standard Login Procedure

1. Open your browser and navigate to your PixelView host URL (e.g., `https://pixelview.yourdomain.com/login`).
2. Enter your registered **Email** address.
3. Enter your **Password** (click the **Eye Icon** to toggle password visibility).
4. Click **Sign In**.

Upon successful authentication, the system creates an encrypted session token and redirects you to the main dashboard.

---

## 3. Single Sign-On (SSO)

If your organization has integrated an enterprise identity provider (such as Keycloak, Okta, Microsoft Entra ID, or Google Workspace):

1. On the login page, click the **Single Sign-On (SSO)** button.
2. You will be redirected to your organization's identity provider portal.
3. Authenticate using your corporate credentials and multi-factor authentication (MFA).
4. Once verified, you will be redirected back to PixelView with an authenticated session.

For identity provider configuration details, see [SSO Integration Guide](../management/sso-integration.md).

---

## 4. Password Recovery & Reset

If you have forgotten your password:

1. Click the **Forgot password?** link on the login page (navigates to `/forgot-password`).
2. Enter your registered email address and click **Send Reset Link**.
3. Check your email for a secure password reset link.
4. Click the link to open the **Reset Password** page (`/reset-password`).
5. Enter your **New Password** and **Confirm Password** (minimum 8 characters with letters, numbers, and symbols).
6. Click **Reset Password** to complete the update.

---

## 5. Logging Out

To end your active session and log out of PixelView:

1. Click the **Logout** button located at the bottom of the left navigation sidebar:
   <a href="../../images/logout.png" class="glightbox">
     <img src="../../images/logout.png" alt="Logout Navigation Sidebar">
   </a>
2. When triggered, PixelView:
   * Revokes the active session token via the backend API.
   * Clears all local application states, session storage, and cached data.
   * Redirects you securely back to the login portal.
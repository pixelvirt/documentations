# Account Settings

The **Settings** section in PixelView allows users to manage their personal account details, configure system notification integrations, update security credentials, and manage license activations.

The settings menu provides access to three sub-sections:
* **Profile (`/profile`)**: Manage personal details, notification preferences, API keys, and update passwords.
* **Config (`/config`)**: Configure external integrations (Email, SMS, OpenStack, Kubernetes, Vault, Jira).
* **License Key (`/license-key`)**: Manage and activate enterprise license keys.

---

## 1. Profile Management

The **Profile** page (`/profile`) provides a unified dashboard for updating your personal contact details, configuring alert notification preferences, managing your personal API key, and updating your login password.

Navigate to **Settings** > **Profile** from the left navigation sidebar:

<a href="../../images/profile.png" class="glightbox">
  <img src="../../images/profile.png" alt="Account Settings Profile">
</a>

### User Header Banner
The top banner displays:
* **Avatar Initials**: Generated from your First and Last Name (e.g., `AA` for Admin Awesome).
* **Full Name**: Your registered display name.
* **Email**: Registered user email address.

---

### Personal Information

The **Personal Information** card allows you to update your profile details and communication preferences:

| Field | Description |
| :--- | :--- |
| **First Name** | Your first name. |
| **Last Name** | Your last name. |
| **Email** | Registered email address used for platform authentication. |
| **Phone** | Mobile number used for receiving SMS alert notifications. |
| **Notification Option** | Preferred notification channels: <br> • `Email` — Receive notifications only via email. <br> • `SMS` — Receive notifications only via SMS. <br> • `Email & SMS` — Dual-channel delivery for urgent alerts. |
| **API Key** | Masked user API key for authenticating programmatic API requests to PixelView. <br> • **Copy Icon**: Copy key directly to clipboard. <br> • **Regenerate Icon**: Invalidate existing key and generate a new key. |

#### Actions:
* **Cancel**: Revert any unsaved changes to profile fields.
* **Save Changes**: Persist updated names, phone numbers, and notification options to the server.

---

### Change Password

The **Change Password** card allows you to update your account password:

| Field | Description |
| :--- | :--- |
| **New Password** | Enter your new account password. Use the **Eye Icon** to toggle password visibility. |
| **Confirm Password** | Re-enter the new password to verify matching entries. |

!!! tip "Password Requirements"
    Passwords must be at least **8 characters** long and should include a combination of uppercase letters, lowercase letters, numbers, and symbols.

#### Actions:
* **Clear**: Reset both password input fields.
* **Update Password**: Submit and apply the new account password.

---

## 2. Configuration (`/config`)

The **Config** section (`/config`) allows administrators to configure global communication services, infrastructure connectors, and security credentials.

Navigate to **Settings** > **Config** from the left navigation sidebar. The top navigation bar provides tabs for:
* **EMAIL**
* **SMS**
* **OPENSTACK**
* **KUBERNETES**

---

### Email Configuration

Configure the platform's outbound email delivery settings for alert dispatch and user invitations:

<a href="../../images/config-email.png" class="glightbox">
  <img src="../../images/config-email.png" alt="Email Configuration">
</a>

#### Supported Email Providers:

1. **SMTP**:
   * **Email Provider**: Select `smtp`.
   * **SMTP Host** *(Required)*: Hostname or IP address of the SMTP mail server (e.g., `smtp.gmail.com`, `mail.example.com`).
   * **SMTP Port** *(Required)*: Port number used for the SMTP connection (e.g., `587` for STARTTLS, `465` for SSL/TLS, `25`).
   * **SMTP Username** *(Required)*: Username or email address for server authentication.
   * **SMTP Password**: Password or App Password for the SMTP server (toggle visibility using the **Eye Icon**).

2. **Mailgun**:
   * **Email Provider**: Select `mailgun`.
   * **Domain** *(Required)*: The sending domain configured in your Mailgun account.
   * **API Key** *(Required)*: Mailgun API private key.

Click **SAVE** to persist your email configuration.

---

### SMS Configuration

Set up SMS notification providers to deliver urgent, high-priority incident alerts directly to team members' mobile phones:

<a href="../../images/config-sms.png" class="glightbox">
  <img src="../../images/config-sms.png" alt="SMS Configuration">
</a>

#### Supported SMS Providers:

1. **Twilio**:
   * **SMS Provider**: Select `twilio`.
   * **Account SID** *(Required)*: Your Twilio Account SID.
   * **Auth Token** *(Required)*: Your Twilio authentication token (toggle visibility with the **Eye Icon**).
   * **Phone Number** *(Required)*: The active Twilio phone number used as the sender.

2. **Custom**:
   * **SMS Provider**: Select `custom`.
   * **URL** *(Required)*: Custom SMS HTTP gateway endpoint URL.
   * **Token** *(Required)*: Authorization header token for the custom SMS service.

Click **SAVE** to apply SMS configuration settings.

---

### OpenStack Configuration

The **OpenStack** tab manages multi-cloud OpenStack credentials and cluster endpoints via `clouds.yaml`.

<a href="../../images/config-openstack.png" class="glightbox">
  <img src="../../images/config-openstack.png" alt="OpenStack Settings">
</a>

#### OpenStack Settings Table:
* **ID/Name**: Configuration identifier name with a quick **Copy ID** button.
* **Username**: OpenStack keystone authentication username.
* **Project**: Default project/tenant name.
* **Region**: OpenStack region name (e.g., `DFW3`, `RegionOne`).
* **User Domain**: Keystone user domain name.
* **Actions**: Context menu (`...`) to **Delete** existing OpenStack credentials.

#### Adding an OpenStack Configuration:
1. In the **OpenStack** tab, click the **`+`** (Add) button on the table toolbar:
   <a href="../../images/config-openstack-add.png" class="glightbox">
     <img src="../../images/config-openstack-add.png" alt="Add OpenStack Button">
   </a>
2. The **Add OpenStack Config** dialog will open:
   <a href="../../images/config-openstack-modal.png" class="glightbox">
     <img src="../../images/config-openstack-modal.png" alt="Add OpenStack Modal">
   </a>
3. Paste your standard `clouds.yaml` content into the code editor.
   !!! tip "Autofill Example Template"
       Press **`Tab`** inside the empty editor to automatically populate a sample `clouds.yaml` template with required syntax.
4. Click **SAVE** to test and persist the cloud connection.

---

### Kubernetes Configuration

The **Kubernetes** tab allows administrators to register cluster endpoints and credentials using standard `kubeconfig` specifications.

<a href="../../images/config-k8s.png" class="glightbox">
  <img src="../../images/config-k8s.png" alt="Kubernetes Settings">
</a>

#### Kubernetes Settings Table:
* **ID/Cluster Name**: Unique cluster identifier name with a quick **Copy ID** button.
* **Name**: Context name assigned to the cluster.
* **Server**: Kubernetes API server endpoint URL (e.g., `https://10.0.0.1:6443`).
* **Actions**: Context menu (`...`) to **Delete** the cluster configuration.

#### Adding a Kubernetes Configuration:
1. In the **Kubernetes** tab, click the **`+`** (Add) button on the table toolbar.
2. The **Add Kubernetes Config** dialog will open:
   <a href="../../images/config-k8s-modal.png" class="glightbox">
     <img src="../../images/config-k8s-modal.png" alt="Add Kubernetes Modal">
   </a>
3. Fill in the cluster parameters:
   * **Name** *(Required)*: A descriptive identifier name for this Kubernetes configuration.
   * **Kubernetes Config**: Paste your cluster's `kubeconfig` YAML file into the code editor. Press **`Tab`** to autofill a sample template.
4. Click **SAVE** to register the cluster.

---

## 3. License Management

The **License Key** page (`/license-key`) allows administrators to manage and inspect enterprise license entitlements, customer registration metadata, and application password settings.

Navigate to **Settings** > **License Key** from the left navigation sidebar:

<a href="../../images/license.png" class="glightbox">
  <img src="../../images/license.png" alt="License Management">
</a>

### License Details & Fields:

| Field | Description |
| :--- | :--- |
| **Customer Name** | The registered customer name or trial license identifier associated with this deployment. |
| **Domain** | Licensed organization domain (e.g., `pixelvirt.com`). |
| **Organization** | Registered company or organization name. |
| **Expire in days** | Number of remaining active days until license expiration. |
| **License Key** | Current status of the license (e.g., `License is valid`). When activating for the first time, this field accepts the raw license key payload. |
| **Application Password** | Masked password protecting license and configuration updates. Use the **Eye Icon** to toggle visibility. |

#### Applying or Updating a License:
1. Enter or paste your **License Key** into the text area.
2. Enter the **Application Password**.
3. Click **APPLY LICENSE** to validate and activate your PixelView instance.

---

## 4. Theme Mode (Dark / Light Mode)

PixelView provides built-in theme support allowing users to switch between **Dark Mode** (default) and **Light Mode** at any time:

<a href="../../images/theme-toggle.png" class="glightbox">
  <img src="../../images/theme-toggle.png" alt="Theme Mode Toggle">
</a>

* Click **Light Mode** (sun icon) in the bottom-left sidebar to switch to the light visual theme.
* When in Light Mode, the button dynamically updates to **Dark Mode** (moon icon) to switch back.
* Your theme preference is preserved across browser sessions.

---

## Conclusion

The **Settings** module provides comprehensive controls over personal account details, communication channels, infrastructure connections, enterprise licenses, and visual preferences. Ensure critical credentials like API keys and Application Passwords are kept secure.
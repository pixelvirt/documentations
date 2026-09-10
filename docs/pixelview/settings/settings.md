# Account Settings

The **Settings** section in PixelView provides comprehensive controls for updating personal operator credentials, configuring external communication gateways, registering multi-cloud endpoints, and managing enterprise license activations.

The settings subsystem is partitioned into three functional areas:

* **Profile (`/profile`)**: Manage personal account attributes, notification delivery preferences, programmatic API keys, and account passwords.
* **Config (`/config`)**: Administrative management of outbound communication gateways (Email, SMS) and infrastructure cluster credentials (OpenStack `clouds.yaml`, Kubernetes `kubeconfig`).
* **License Key (`/license-key`)**: Inspect enterprise license validity, customer entitlements, domain allocations, and application password security.

---

## Profile Management

The **Profile** page (`/profile`) provides a unified dashboard for managing your individual user account information, alert notification channels, API access tokens, and authentication passwords.

Navigate to **Settings** > **Profile** in the primary navigation sidebar:

<a href="../../images/profile.png" class="glightbox">
  <img src="../../images/profile.png" alt="Account Settings Profile">
</a>

### Profile Header Summary

The top profile card displays current identity details:

* **Avatar Initials**: Dynamically derived from the first letter of your first and last name (e.g., `AA` for Admin Awesome).
* **Full Name**: Your registered display name.
* **Email Address**: Registered email associated with your account.
* **Notification Chip**: Active alert channel indicator (e.g., `Notifications: email+sms`).

---

### Personal Information

The **Personal Information** card allows you to update your profile details and communication preferences:

| Field | Input Type | Description |
| :--- | :--- | :--- |
| **First Name** | Text | Legal or preferred first name. |
| **Last Name** | Text | Legal or preferred last name. |
| **Email** | Read-Only | Registered account email address (used for system authentication). |
| **Phone** | Text | Mobile phone number used for SMS alert delivery. |
| **Notification Option** | Select Menu | Preferred channel for operational alerts: <br> * `email`: Deliver alerts exclusively via email. <br> * `sms`: Deliver alerts exclusively via SMS text messages. <br> * `email+sms`: Deliver alerts concurrently across both email and SMS for high-urgency notifications. |
| **API Key** | Masked Text | Personal API token used to authenticate programmatic requests to the PixelView REST API. <br> * **Copy Icon**: Copies the API key directly to your clipboard. <br> * **Regenerate Icon**: Invalidates the active key and provisions a new token. |

#### Regenerating Your API Key

* Click the **Regenerate API Key** icon adjacent to the API Key field.
* A browser confirmation dialog appears:
  ```
  Regenerating your API key will invalidate the current key. Continue?
  ```
* Confirming the action immediately invalidates the previous token and provisions a fresh key. Update any automation scripts, CI/CD pipelines, or external bots utilizing the previous token.

#### Saving Personal Changes

* Click **Save Changes** to persist updated names, phone numbers, and notification preferences.
* Click **Cancel** to discard uncommitted edits and revert fields to cached values.

---

### Change Password

The **Change Password** card enables secure credential rotation:

| Field | Input Type | Description |
| :--- | :--- | :--- |
| **Old Password** | Password | Current account password required for identity verification. Includes a visibility toggle icon. |
| **New Password** | Password | Desired new password. Includes a visibility toggle icon. |
| **Confirm New Password** | Password | Re-enter the new password to prevent typing mistakes. |

!!! tip "Password Complexity Guidelines"
    For security, passwords must be at least **8 characters** long and should include a balanced combination of uppercase letters, lowercase letters, numbers, and symbols.

**Actions:**

* Click **Update Password** to validate inputs and apply the new credential.
* Click **Clear** to reset all password input fields.

---

## Global Configuration (`/config`)

The **Config** view (`/config`) allows administrators to configure enterprise communication gateways, infrastructure connectors, and security credentials.

Navigate to **Settings** > **Config** in the primary navigation sidebar. The top navigation bar presents four configuration tabs:

* **EMAIL** *(Admin Only)*: Outbound email delivery configuration.
* **SMS** *(Admin Only)*: SMS provider settings for urgent paging.
* **OPENSTACK**: Multi-cloud OpenStack credentials management.
* **KUBERNETES**: Multi-cluster Kubernetes `kubeconfig` management.

!!! info "Role-Based Configuration Visibility"
    The **EMAIL** and **SMS** configuration tabs are exclusively accessible to users holding the `Admin` system role. Operators with the standard `User` role can view and manage **OPENSTACK** and **KUBERNETES** cluster credentials according to their permissions matrix.

---

### Email Configuration

Configure the platform's outbound email delivery settings used for incident notifications, approval notices, and user invitations:

<a href="../../images/config-email.png" class="glightbox">
  <img src="../../images/config-email.png" alt="Email Configuration">
</a>

Select your organization's email provider from the **Email Provider** dropdown:

#### SMTP Configuration

* **Email Provider**: Select `smtp`.
* **SMTP Host** *(Required)*: Hostname or IP address of the SMTP mail relay (e.g., `smtp.gmail.com`, `mail.example.com`).
* **SMTP Port** *(Required)*: Network port for the SMTP connection (e.g., `587` for STARTTLS, `465` for SSL/TLS, or `25`).
* **SMTP Username** *(Required)*: Service account username or email used for SMTP authentication.
* **SMTP Password**: Account or application password. Toggle visibility using the eye icon.
* Click **Save** to persist SMTP settings.

#### Mailgun Configuration

* **Email Provider**: Select `mailgun`.
* **Domain** *(Required)*: Sending domain registered and verified in your Mailgun account.
* **API Key** *(Required)*: Private API key from Mailgun with sending authorization.
* Click **Save** to persist Mailgun settings.

---

### SMS Configuration

Configure SMS delivery providers to transmit high-severity incident notifications directly to operators' mobile devices:

<a href="../../images/config-sms.png" class="glightbox">
  <img src="../../images/config-sms.png" alt="SMS Configuration">
</a>

Select your provider from the **SMS Provider** dropdown:

#### Twilio Configuration

* **SMS Provider**: Select `twilio`.
* **Account SID** *(Required)*: Twilio account security identifier.
* **Auth Token** *(Required)*: Twilio authentication token. Toggle visibility using the eye icon.
* **Phone Number** *(Required)*: E.164 formatted sender phone number provisioned in Twilio (e.g., `+15551234567`).
* Click **Save** to apply Twilio parameters.

#### Custom HTTP SMS Gateway

* **SMS Provider**: Select `custom`.
* **URL** *(Required)*: HTTP REST endpoint of your internal SMS messaging gateway.
* **Token** *(Required)*: Authorization bearer token passed in the gateway request header.
* Click **Save** to apply custom SMS gateway parameters.

---

### OpenStack Configuration

The **OpenStack** tab manages multi-cloud OpenStack credentials and connection endpoints via standard `clouds.yaml` specifications.

<a href="../../images/config-openstack.png" class="glightbox">
  <img src="../../images/config-openstack.png" alt="OpenStack Settings">
</a>

#### OpenStack Settings Table

The table displays registered OpenStack clouds:

| Column | Description |
| :--- | :--- |
| **ID/Name** | Configuration identifier with an integrated **Copy ID** button. |
| **Username** | Keystone authentication username. |
| **Project** | Default project/tenant name. |
| **Region** | OpenStack region identifier (e.g., `RegionOne`, `DFW3`). |
| **User Domain** | Keystone user domain name (default: `Default`). |
| **Actions** | Context menu (`...`) providing options to delete cloud credentials. |

#### Adding an OpenStack Cloud Configuration

* Click the **`+`** (Add) button on the table toolbar:
  <a href="../../images/config-openstack-add.png" class="glightbox">
    <img src="../../images/config-openstack-add.png" alt="Add OpenStack Button">
  </a>
* In the **Add OpenStack Config** modal dialog:
  <a href="../../images/config-openstack-modal.png" class="glightbox">
    <img src="../../images/config-openstack-modal.png" alt="Add OpenStack Modal">
  </a>
* Paste your standard `clouds.yaml` configuration into the code editor.

```yaml
clouds:
  enterprise_cloud:
    auth:
      auth_url: https://openstack.example.com:5000/v3
      username: "pixelview_svc"
      password: "StrongServiceAccountPassword"
      project_name: "infrastructure"
      project_domain_name: "Default"
      user_domain_name: "Default"
    region_name: "RegionOne"
    interface: "public"
    identity_api_version: 3
```

* Click **Save** to register the cloud connection.

---

### Kubernetes Configuration

The **Kubernetes** tab allows administrators to register cluster API endpoints and service credentials using standard `kubeconfig` specifications.

<a href="../../images/config-k8s.png" class="glightbox">
  <img src="../../images/config-k8s.png" alt="Kubernetes Settings">
</a>

#### Kubernetes Settings Table

The table displays all registered Kubernetes clusters:

| Column | Description |
| :--- | :--- |
| **ID/Cluster Name** | Unique cluster identifier with an integrated **Copy ID** button. |
| **Name** | Descriptive name assigned to the cluster configuration. |
| **Server** | Kubernetes API server URL (e.g., `https://k8s-api.example.com:6443`). |
| **Actions** | Context menu (`...`) providing options to delete cluster credentials. |

#### Adding a Kubernetes Cluster Configuration

* Click the **`+`** (Add) button on the table toolbar.
* In the **Add Kubernetes Config** dialog:
  <a href="../../images/config-k8s-modal.png" class="glightbox">
    <img src="../../images/config-k8s-modal.png" alt="Add Kubernetes Modal">
  </a>
* Complete the cluster parameters:
  * **Name** *(Required)*: Enter a descriptive name to identify this cluster in the UI.
  * **Kubernetes Config** *(Required)*: Paste your complete `kubeconfig` YAML file into the code editor.
* Click **Save** to validate and register the cluster.

---

## License Management

The **License Key** view (`/license-key`) allows administrators to inspect enterprise entitlements, manage license keys, and maintain application password security.

Navigate to **Settings** > **License Key** in the primary navigation sidebar:

<a href="../../images/license.png" class="glightbox">
  <img src="../../images/license.png" alt="License Management">
</a>

### License Entitlement Fields

The license management interface displays current licensing attributes:

| Field | Status | Description |
| :--- | :--- | :--- |
| **Customer Name** | Read-Only | Registered enterprise customer or account identifier. |
| **Domain** | Read-Only | Licensed operational domain (e.g., `pixelvirt.com`). |
| **Organization** | Read-Only | Registered company or organization name. |
| **Expire In days** | Read-Only | Number of days remaining until license expiration. |
| **License Key** | Editable / Locked | Multi-line text field for applying an enterprise license string. Once confirmed, displays `License is valid` and is locked against accidental modification. |
| **Application Password** | Password | Master administrative password protecting license alterations and sensitive system configurations. |
| **Confirm Application Password** | Password | Verification field displayed during initial system setup when no password exists yet. |

### Applying or Updating an Enterprise License

* Paste your enterprise license key into the **License Key** text area.
* Enter your **Application Password**.
* Click **APPLY LICENSE**.
* On initial system activation, upon validation, PixelView automatically transitions your session from setup mode to the main dashboard (`/openstack`).

---

## Theme Mode (Dark / Light Mode)

PixelView provides native theme customization allowing operators to toggle between **Dark Mode** (default) and **Light Mode** according to working environment preferences:

<a href="../../images/theme-toggle.png" class="glightbox">
  <img src="../../images/theme-toggle.png" alt="Theme Mode Toggle">
</a>

* Click **Light Mode** (sun icon) at the base of the left navigation sidebar to switch to the light theme.
* Click **Dark Mode** (moon icon) to revert to the dark visual theme.
* Theme selections persist across browser sessions.
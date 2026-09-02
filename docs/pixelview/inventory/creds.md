# Credentials Management

The **Credentials** section (`/creds`) under **Inventory** provides a secure authentication vault for storing, organizing, and linking administrative access profiles (passwords and SSH keys) to servers, network devices, applications, and automation tasks across PixelView.

---

## Navigating to Credentials

To access stored credentials and authentication profiles:

* In the left navigation sidebar under **Inventory**, click **Credentials**:

<a href="../../images/inventory-creds-table.png" class="glightbox">
  <img src="../../images/inventory-creds-table.png" alt="Credentials Overview Table">
</a>

### Credentials Table Overview

The main table lists all active and stored credential profiles with 60-second background polling for real-time synchronization:

| Column | Description |
| :--- | :--- |
| **ID/Name** | Display profile name (e.g., `159.135.206.19`, `ha-hosts-creds`, `testCreds`) with a blue lock icon and copyable UUID chip. |
| **Username** | Administrative login user account (e.g., `root`, `hemant`, `admin@localhost`). |
| **Description** | Operational context or target environment label (e.g., `N/A`, datacenter cluster). |
| **Created At** | Timestamp marking when the credential entry was first registered (`YYYY-MM-DD HH:mm:ss`). |
| **Updated At** | Timestamp tracking the most recent modification to the credential. |
| **Actions** | Context action menu (`...`) for editing or deleting credential profiles. |

### Table Toolbar Controls

The top-right toolbar provides dynamic Material React Table controls:
* **Search / Global Filter**: Real-time filtering across all credential records.
* **Column Filters**: Filter records by specific field values.
* **Show/Hide Columns**: Toggle visibility of individual table columns.
* **Density Toggle**: Switch between compact and expanded row padding.
* **Refresh**: Instantly fetch the latest credential definitions.
* **Add Credential (`+`)**: Launch the credential creation dialog.

---

## Creating Credentials

To register a new authentication profile:

* Click the orange **`+`** (Add Credential) button on the table toolbar:

<a href="../../images/inventory-creds-add-button.png" class="glightbox">
  <img src="../../images/inventory-creds-add-button.png" alt="Add Credential Toolbar Button">
</a>

* The **Create Credential** dialog will open:

<a href="../../images/inventory-creds-create-modal.png" class="glightbox">
  <img src="../../images/inventory-creds-create-modal.png" alt="Create Credential Dialog">
</a>

### Configuration Fields

* **Display Name** *(Required)*: A friendly, recognizable identifier for the credential profile (e.g., `ha-hosts-creds`, `production-bastion`).
* **Username** *(Required)*: The remote system login user account (e.g., `root`, `admin@localhost`).
* **Authentication Methods**:
    * **Password**: Plaintext or masked password for password-based SSH/API authentication.
    * **Public Key**: The OpenSSH public key string (e.g., `ssh-ed25519 AAAAC3...` or `ssh-rsa AAAAB3...`).
    * **Private Key**: The matching OpenSSH or RSA private key block for key-based authentication.

> [!IMPORTANT]
> At least one valid authentication secret (**Password** or **Private Key**) must be provided when creating a credential profile.

* Click **CREATE CREDENTIAL** to securely store the profile in the vault.

---

## Inspecting & Selecting Credentials

* Select any credential row in the table to review its attributes and quick-copy the unique UUID:

<a href="../../images/inventory-creds-row-select.png" class="glightbox">
  <img src="../../images/inventory-creds-row-select.png" alt="Selecting Credential Row">
</a>

* Click the **Copy UUID** icon beside the credential name to copy the system identifier for use in API calls, automation workflows, or backend integrations.

---

## Editing Credentials

To update an existing credential profile or rotate secrets:

* Click the context action menu (**`...`**) in the **Actions** column of the target credential:

<a href="../../images/inventory-creds-context-menu.png" class="glightbox">
  <img src="../../images/inventory-creds-context-menu.png" alt="Credential Context Actions Menu">
</a>

* Select **Edit** from the menu. The **Edit Credential** dialog will appear:

<a href="../../images/inventory-creds-edit-modal.png" class="glightbox">
  <img src="../../images/inventory-creds-edit-modal.png" alt="Edit Credential Dialog">
</a>

### Security and Secret Rotation Rules

* **Username**: Update the associated username if required.
* **Secret Fields (Password & Private Key)**: For security reasons, existing passwords and private keys are never pre-populated or exposed in the UI.
    * Leave these fields blank to retain the current active password or private key.
    * Enter new values only when rotating or replacing secrets.
* **Public Key**: Displays the configured public key string for inspection and editing.
* Click **UPDATE CREDENTIAL** to save changes.

---

## Deleting Credentials

* In the **Actions** context menu (**`...`**), select **Delete Credential**.
* Confirm the prompt to permanently delete the profile from the system vault.

> [!WARNING]
> Ensure the credential is not actively attached to critical production servers, network devices, or scheduled automation playbooks before deleting.

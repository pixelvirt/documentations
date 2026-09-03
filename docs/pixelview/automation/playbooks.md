# Ansible Playbooks

The **Playbooks** section (`/playbooks`) under **Automation** catalogs and manages the Ansible playbooks registered within PixelView. 

Playbooks define idempotent infrastructure-as-code automation tasks—such as package installation, service orchestration, security patching, and configuration management—that execute across target hosts via dedicated Ansible Runners.

---

## Architecture & Runner Mounts

PixelView decouples playbook catalog management from execution runners:

> [!NOTE]
> Playbooks registered in PixelView must physically reside in the storage volume mounted to your **Ansible Runner** containers under the `playbooks/` directory (for example, `/playbooks/install-kubectl.yaml`). 
> 
> Registering a playbook in this inventory enables it to be selected when triggering ad-hoc runs in [Executions](executions.md) or composing multi-step automated Workflows.

---

## Navigating to Playbooks

To view and manage your registered playbooks:

* In the left navigation sidebar under **Automation**, click **Playbooks**:

<a href="../../images/automation-playbooks-table.png" class="glightbox">
  <img src="../../images/automation-playbooks-table.png" alt="Ansible Playbooks Overview Table">
</a>

### Playbooks Table Overview

The main table lists all registered playbooks and their underlying script files:

| Column | Description |
| :--- | :--- |
| **ID/Name** | Playbook display name (e.g., `Install kubectl`, `MariaDB-restart`) with a document icon, clickable link to inspect details, and a copyable UUID chip. |
| **Filename** | Name of the YAML playbook file mounted on the runner (e.g., `install-kubectl.yaml`, `mariadb-restart.yaml`, `info.yaml`). |
| **Description** | Human-readable explanation of what the playbook automates, or `N/A` if unassigned. |
| **Created At** | Timestamp when the playbook was first registered (`YYYY-MM-DD HH:mm:ss`). |
| **Updated At** | Timestamp of the most recent metadata modification (`YYYY-MM-DD HH:mm:ss`). |
| **Actions** | Context menu button (**`...`**) providing editing and deletion options. |

### Table Toolbar Controls

* **Search / Global Filter**: Quickly filter playbooks across names, filenames, and descriptions.
* **Column Filters**: Filter rows by specific table attributes.
* **Show/Hide Columns**: Customize visible table headers.
* **Density Toggle**: Toggle between compact and spacious row heights.
* **Refresh**: Re-fetch the playbook catalog from the backend API.
* **Add Playbook (`+`)**: Open the inventory registration modal.

---

## Adding a Playbook (Add to Playbook Inventory)

To register an existing runner playbook into the PixelView catalog:

* Click the orange **`+`** (Add Playbook) button on the table toolbar:

<a href="../../images/automation-playbooks-add-button.png" class="glightbox">
  <img src="../../images/automation-playbooks-add-button.png" alt="Add Playbook Toolbar Button">
</a>

* The **Add to playbook inventory** modal dialog will appear:

<a href="../../images/automation-playbooks-create-modal.png" class="glightbox">
  <img src="../../images/automation-playbooks-create-modal.png" alt="Add to Playbook Inventory Dialog">
</a>

### Configuration Fields

* **Name** *(Required)*: A unique, descriptive title for the playbook (e.g., `Install kubectl`).
* **Filepath** *(Required)*: The filename of the playbook file residing inside the runner mount. The `playbooks/` directory prefix is automatically prefixed by PixelView. The filename must end in `.yml` or `.yaml` (e.g., `install-kubectl.yaml`).
* **Description** *(Optional)*: Explanatory notes documenting the purpose, required variables, or target operating systems for the playbook.

### Dialog Actions

* **CANCEL**: Abort registration and close the modal.
* **CREATE PLAYBOOK**: Register the playbook into PixelView's inventory.

---

## Managing Playbooks (Edit & Delete)

To modify or delete an existing playbook:

* In the table row, click the **Actions** menu (**`...`**):

<a href="../../images/automation-playbooks-context-menu.png" class="glightbox">
  <img src="../../images/automation-playbooks-context-menu.png" alt="Playbook Actions Context Menu">
</a>

### 1. Editing Playbook Metadata

* Click **Edit** from the actions menu to open the **Edit Playbook** modal:

<a href="../../images/automation-playbooks-edit-modal.png" class="glightbox">
  <img src="../../images/automation-playbooks-edit-modal.png" alt="Edit Playbook Modal Dialog">
</a>

* Update the **Name**, mounted **Filepath**, or **Description**.
* Click **UPDATE PLAYBOOK** to save changes.

### 2. Deleting a Playbook

* Click **Delete Playbook** from the actions menu.
* A browser confirmation dialog will prompt:
  ```text
  Are you sure you want to delete the playbook "[Playbook Name]"? This action cannot be undone.
  ```
* Click **OK** to confirm. The playbook reference will be removed from the catalog.

> [!WARNING]
> Deleting a playbook removes its registration from PixelView. Workflows or automated execution jobs that depend on this playbook reference will fail if invoked.

---

## Inspecting Playbook Details

To inspect the full metadata of a playbook:

* Click directly on the playbook row or display name link in the table (e.g., `ha-playbook-info`):

<a href="../../images/automation-playbooks-row-select.png" class="glightbox">
  <img src="../../images/automation-playbooks-row-select.png" alt="Selecting Playbook Row">
</a>

* The **Playbook Details** modal will open:

<a href="../../images/automation-playbooks-details-modal.png" class="glightbox">
  <img src="../../images/automation-playbooks-details-modal.png" alt="Playbook Details Modal">
</a>

### Displayed Information

* **Name**: Display name of the playbook.
* **UUID**: System-assigned unique identifier with a one-click clipboard copy icon.
* **Filename**: The underlying YAML playbook filename.
* **Description**: Detailed summary of playbook functionality.
* **Created At & Last Updated**: Complete timestamps marking initial registration and last revision.

### Quick Edit from Details

* From the details modal, click the purple **EDIT PLAYBOOK** button:

<a href="../../images/automation-playbooks-details-edit-button.png" class="glightbox">
  <img src="../../images/automation-playbooks-details-edit-button.png" alt="Edit Playbook Button in Details Modal">
</a>

* The **Edit Playbook** dialog opens directly with pre-populated values, allowing operators to make rapid updates without returning to the main table:

<a href="../../images/automation-playbooks-edit-from-details.png" class="glightbox">
  <img src="../../images/automation-playbooks-edit-from-details.png" alt="Edit Playbook Dialog from Details">
</a>

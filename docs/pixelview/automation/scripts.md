# Automation Scripts

The **Scripts** section (`/scripts`) under **Automation** catalogs and manages custom Python automation scripts registered within PixelView.

While Ansible playbooks are ideal for idempotent configuration management and provisioning, custom Python scripts provide operators with full programming flexibility—allowing complex API integrations, custom data transformations, database health checks, and cleanup tasks to be executed seamlessly across runner environments.

---

## Architecture & Runner Mounts

Similar to playbooks, PixelView decouples script catalog management from execution runners:

> [!NOTE]
> Python scripts registered in PixelView must physically reside in the storage volume mounted to your [Ansible Runner](runners.md) containers under the `scripts/` directory (for example, `/scripts/cleanup.py`).
>
> When registering a script, PixelView automatically manages the `scripts/` path prefix and `.py` extension. Once registered, scripts can be executed directly through [Executions](executions.md) or orchestrated into complex multi-step automated workflows.

---

## Navigating to Scripts

To view and manage your registered Python automation scripts:

* In the left navigation sidebar under **Automation**, click **Scripts**:

<a href="../../images/automation-scripts-table.png" class="glightbox">
  <img src="../../images/automation-scripts-table.png" alt="Automation Scripts Overview Table">
</a>

### Scripts Table Overview

The main table lists all registered Python scripts:

| Column | Description |
| :--- | :--- |
| **ID/Name** | Script display name (e.g., `random`) with an icon and an 8-character copyable UUID chip with clipboard utility. |
| **Filename** | Base filename of the script residing in runner storage (e.g., `cleanup`). |
| **Description** | Contextual description of the script's operational task, or `-` if omitted. |
| **Created At** | Timestamp marking initial script registration (`YYYY-MM-DD HH:mm:ss`). |
| **Updated At** | Timestamp marking the most recent metadata update (`YYYY-MM-DD HH:mm:ss`). |
| **Actions** | Context action menu (**`...`**) providing editing and deletion options. |

### Table Toolbar Controls

* **Search / Global Filter**: Instant full-text search across script names, filenames, and descriptions.
* **Column Filters**: Filter rows by specific attribute criteria.
* **Show/Hide Columns**: Customize visible table headers.
* **Density Toggle**: Toggle between compact and spacious row padding.
* **Refresh**: Re-fetch the scripts catalog from the backend API.
* **Add Script (`+`)**: Open the script inventory registration modal.

---

## Adding a Script (Add to Script Inventory)

To register an existing runner Python script into PixelView's catalog:

* Click the orange **`+`** (Add Script) button in the top-right table toolbar:

<a href="../../images/automation-scripts-add-button.png" class="glightbox">
  <img src="../../images/automation-scripts-add-button.png" alt="Add Script Toolbar Button">
</a>

* The **Add to script inventory** modal dialog will open:

<a href="../../images/automation-scripts-create-modal.png" class="glightbox">
  <img src="../../images/automation-scripts-create-modal.png" alt="Add to Script Inventory Dialog">
</a>

### Configuration Fields

* **Name** *(Required)*: A unique, descriptive title identifying the script (e.g., `cleanup-temp-files`, `random`).
* **Filepath** *(Required)*: The filename of the Python script inside the runner's `scripts/` directory. 
  > [!TIP]
  > Enter only the base file name (e.g., `cleanup`). PixelView automatically provides the `scripts/` prefix and appends `.py` automatically.
* **Description** *(Optional)*: Explanatory notes documenting the script arguments, expected behavior, or environment requirements.

### Dialog Actions

* **CANCEL**: Dismiss the dialog without saving.
* **CREATE SCRIPT**: Register the script into PixelView's automation catalog.

---

## Managing Scripts (Edit & Delete)

To update metadata or remove an obsolete script:

* Click the **Actions** menu (**`...`**) on the target script row:

<a href="../../images/automation-scripts-context-menu.png" class="glightbox">
  <img src="../../images/automation-scripts-context-menu.png" alt="Script Actions Context Menu">
</a>

### 1. Editing Script Metadata

* Click **Edit** from the actions menu:

<a href="../../images/automation-scripts-edit-modal.png" class="glightbox">
  <img src="../../images/automation-scripts-edit-modal.png" alt="Edit Script Modal Dialog">
</a>

* Update the script **Name**, target **Filepath**, or **Description**.
* Click **UPDATE SCRIPT** to save your modifications.

### 2. Deleting a Script

* Click **Delete Script** from the actions menu.
* A browser confirmation dialog will prompt:
  ```text
  Are you sure you want to delete the script "[Script Name]"?
  ```
* Click **OK** to confirm. The script record will be permanently deleted from the inventory.

> [!WARNING]
> Deleting a script removes its reference from PixelView. Any scheduled workflows or automated jobs that invoke this script will fail to execute.

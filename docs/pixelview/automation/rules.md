# Automation Rules

The **Rules** section (`/rules`) under **Automation** defines event-driven routing logic in PixelView. 

Rules bridge incoming system events, monitoring alerts, or webhook triggers with automated execution workflows. When an external trigger (such as `mariadb-down` or `host_failure`) is received, an active rule immediately invokes its bound workflow without requiring manual intervention.

---

## Navigating to Rules

To access and manage your event-driven rules:

* In the left navigation sidebar under **Automation**, click **Rules**:

<a href="../../images/automation-rules-table.png" class="glightbox">
  <img src="../../images/automation-rules-table.png" alt="Automation Rules Overview Table">
</a>

### Rules Table Overview

The main table lists all configured automation rules:

| Column | Description |
| :--- | :--- |
| **ID/Name** | Name of the rule (e.g., `MariaBD-rules`) with an icon, and a copyable 8-character UUID chip with clipboard utility. |
| **Description** | Contextual explanation of the rule's purpose (e.g., `Test rule for automation bot`). |
| **Trigger** | Unique event trigger identifier (e.g., `mariadb-down`) that initiates workflow execution. |
| **Status** | Current operational status displayed as a colored badge (**Enabled** in green or **Disabled**). |
| **Actions** | Row context menu (**`...`**) providing editing and deletion options. |

### Table Toolbar Controls

* **Search / Global Filter**: Perform rapid full-text filtering across all rule names, triggers, and descriptions.
* **Column Filters**: Filter rows by specific attribute criteria.
* **Show/Hide Columns**: Customize table column visibility.
* **Density Toggle**: Switch between compact and comfortable row spacing.
* **Refresh**: Re-fetch the latest rules list from the backend API.
* **Add Rule (`+`)**: Launch the rule creation modal.

---

## Creating an Automation Rule

To register a new trigger-to-workflow rule:

* Click the orange **`+`** (Add Rule) button in the top-right table toolbar:

<a href="../../images/automation-rules-add-button.png" class="glightbox">
  <img src="../../images/automation-rules-add-button.png" alt="Add Rule Toolbar Button">
</a>

* The **Add Rule** modal dialog will open:

<a href="../../images/automation-rules-create-modal.png" class="glightbox">
  <img src="../../images/automation-rules-create-modal.png" alt="Add Rule Modal Dialog">
</a>

### Configuration Fields

* **Name** *(Required)*: Enter a distinctive name for the automation rule (e.g., `MariaBD-rules`).
* **Description** *(Optional)*: Provide an operational summary explaining what event this rule responds to and its target remediation path.
* **Trigger** *(Required)*: The event identifier that triggers this rule. 
  > [!IMPORTANT]
  > Trigger names must be unique across your environment and must not contain spaces (e.g., use snake_case or kebab-case like `mariadb-down` or `service_failure`).
* **Select Workflow** *(Required)*: Select the target automated workflow from the dropdown that should execute when the trigger occurs.
* **Enabled** *(Toggle)*: Toggle the switch to activate or deactivate the rule upon creation (default: active).

### Dialog Actions

* **CANCEL**: Dismiss the dialog without creating the rule.
* **CREATE RULE**: Save the rule and activate event-driven routing.

---

## Managing Rules (Edit & Delete)

To modify an existing rule or remove it:

* Click the **Actions** menu (**`...`**) on the target rule row:

<a href="../../images/automation-rules-context-menu.png" class="glightbox">
  <img src="../../images/automation-rules-context-menu.png" alt="Rule Actions Context Menu">
</a>

### 1. Editing a Rule

* Click **Edit** from the actions menu:

<a href="../../images/automation-rules-edit-modal.png" class="glightbox">
  <img src="../../images/automation-rules-edit-modal.png" alt="Edit Rule Modal Dialog">
</a>

* **Editable Fields**: You can update the **Name**, **Description**, bound **Workflow**, or toggle the **Enabled** switch to pause or resume automated execution.
* **Immutable Trigger**: Notice that the **Trigger** field is locked and disabled during edits to preserve routing consistency for existing webhooks and external event sources.
* **Actions**:
  * **CANCEL**: Close the editor without modifying values.
  * **RESET CHANGES**: Discard unsaved changes and reset inputs back to their saved state.
  * **SAVE CHANGES**: Persist updates to the rule.

### 2. Deleting a Rule

* Click **Delete** from the actions menu.
* A browser confirmation prompt will appear:
  ```text
  Are you sure you want to delete [Rule Name]?
  ```
* Click **OK** to permanently remove the rule.

> [!WARNING]
> Deleting a rule immediately ceases automated responses for its configured trigger. Any incoming alerts or bot events matching this trigger will no longer initiate remediation workflows.

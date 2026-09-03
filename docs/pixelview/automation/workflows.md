# Automation Workflows

The **Workflows** section (`/workflows`) under **Automation** provides a visual pipeline orchestration engine in PixelView. 

Workflows allow operators to chain multiple [Ansible Playbooks](playbooks.md) and [Python Scripts](scripts.md) into sequential, automated execution pipelines. These workflows can be triggered manually on demand via [Executions](executions.md), automatically invoked by event-driven [Rules](rules.md) for incident self-healing, or scheduled across multi-cloud infrastructure via [Patch Management](../patch-management/patchset.md).

---

## Navigating to Workflows

To view and manage your automation pipelines:

* In the left navigation sidebar under **Automation**, click **Workflows**:

<a href="../../images/automation-workflows-table.png" class="glightbox">
  <img src="../../images/automation-workflows-table.png" alt="Automation Workflows Overview Table">
</a>

### Workflows Table Overview

The main table lists all configured automation workflows:

| Column | Description |
| :--- | :--- |
| **Expand (`v`)** | Chevron toggle that expands the row to display associated playbooks, scripts, and descriptions. |
| **Name** | Workflow name (e.g., `ha-workflow`, `mariadb-restart-workflow`) with a flowchart icon and an 8-character copyable UUID chip with clipboard utility. |
| **Playbooks** | Number of Ansible playbooks included in this workflow pipeline. |
| **Scripts** | Number of Python automation scripts included in this workflow pipeline. |
| **Created At** | Timestamp marking when the workflow was created (`Month DD, YYYY at HH:MM AM/PM`). |
| **Actions** | Context action menu (**`...`**) providing deletion controls. |

### Table Toolbar Controls

* **Search / Global Filter**: Full-text filter across workflow names, IDs, and timestamps.
* **Column Filters**: Filter rows by specific columns.
* **Show/Hide Columns**: Customize table column visibility.
* **Density Toggle**: Toggle between compact and spacious row padding.
* **Refresh**: Re-fetch the latest workflows list from the backend API.
* **Add Workflow (`+`)**: Navigate to the visual workflow builder.

---

## Inspecting Workflow Components (Row Expansion)

To view the exact sequence of playbooks and scripts contained within a workflow:

* Click the expand chevron (**`v`**) next to any workflow row (e.g., `ha-workflow`):

<a href="../../images/automation-workflows-row-expand.png" class="glightbox">
  <img src="../../images/automation-workflows-row-expand.png" alt="Workflow Row Expand Button">
</a>

* The row expands inline to reveal its underlying tasks:

<a href="../../images/automation-workflows-row-expanded.png" class="glightbox">
  <img src="../../images/automation-workflows-row-expanded.png" alt="Expanded Workflow Details Panel">
</a>

### Details Breakdown

* **Associated Playbooks**: A numbered list of all Ansible playbooks executed in sequential order, along with their registered descriptions (e.g., `1. ha-playbook-info`).
* **Associated Scripts**: A numbered list of all Python scripts executed within the workflow, or a note indicating *No scripts associated*.
* **Description**: Custom workflow notes and documentation explaining the pipeline's operational purpose.

---

## Creating an Automation Workflow

To build a new multi-step automation pipeline:

* Click the orange **`+`** (Add Workflow) button on the table toolbar:

<a href="../../images/automation-workflows-add-button.png" class="glightbox">
  <img src="../../images/automation-workflows-add-button.png" alt="Add Workflow Toolbar Button">
</a>

* The visual **Create Workflow** page will open:

<a href="../../images/automation-workflows-create-page.png" class="glightbox">
  <img src="../../images/automation-workflows-create-page.png" alt="Create Workflow Visual Pipeline Builder">
</a>

### Pipeline Configuration

1. **Workflow Name** *(Required)*: Enter a unique, recognizable title for the workflow (e.g., `mariadb-restart-workflow`, `Install kubectl`).
2. **Description** *(Optional)*: Provide an operational summary explaining the sequence of tasks performed by the pipeline.

### Visual Flow Builder

PixelView provides a vertical timeline builder to sequence automated tasks between execution boundaries:

* **Start**: Green play indicator marking the entry point of the workflow.
* **Step Type**: Select whether the step executes an **Ansible Playbook** or a **Python Script**:
    * When **Playbook** is chosen, select from registered playbooks in [Playbooks](playbooks.md).
    * When **Script** is chosen, select from registered scripts in [Scripts](scripts.md).
* **Add Next Step (`+`)**: Click the **`+` Add next step** button along the timeline to append additional sequential steps.
* **Delete Step**: Click the trash can icon on any step row to remove it from the pipeline.
* **Stop**: Red indicator marking the successful conclusion of the workflow run.

### Page Actions

* **CANCEL**: Discard the pipeline and return to the workflows table.
* **CREATE WORKFLOW**: Validates all step configurations and saves the new workflow to the catalog.

---

## Managing Workflows (Delete)

To remove an obsolete workflow from the platform:

* In the table row, click the **Actions** menu (**`...`**):

<a href="../../images/automation-workflows-context-menu.png" class="glightbox">
  <img src="../../images/automation-workflows-context-menu.png" alt="Workflow Actions Context Menu">
</a>

* Click **Delete**.
* A browser confirmation dialog will prompt:
  ```text
  Are you sure you want to delete [Workflow Name]?
  ```
* Click **OK** to permanently remove the workflow.

> [!WARNING]
> Deleting a workflow removes its pipeline definition permanently. Any event-driven [Rules](rules.md), patchsets, or automated triggers linked to this workflow will no longer be able to execute.

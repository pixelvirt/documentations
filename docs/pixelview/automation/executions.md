# Automation Executions

The **Executions** section (`/executions`) under **Automation** provides a centralized execution console and real-time observability engine for all automated workflows, Ansible playbooks, and Python scripts across PixelView.

Operators can dispatch ad-hoc or scheduled jobs across multi-cloud infrastructure, monitor live task execution via Server-Sent Events (SSE) log streams, inspect granular task telemetry, and re-run failed jobs with automated retry management.

---

## Navigating to Executions

To access automation jobs and execution histories:

* In the left navigation sidebar under **Automation**, click **Executions**:

<a href="../../images/automation-executions-table.png" class="glightbox">
  <img src="../../images/automation-executions-table.png" alt="Executions Overview Table">
</a>

### Executions Table Overview

The main table lists all active and completed automation runs with 20-second background polling for synchronized real-time state tracking:

| Column | Description |
| :--- | :--- |
| **ID/Name** | Execution display name (e.g., `hm-7da84a15...`, `testing`) with a blue briefcase icon, clickable link to the detailed run dashboard, and copyable UUID chip. |
| **Workflow Name** | Name of the automation workflow executed (e.g., `Install kubectl`, `ha-workflow`, or `-` for standalone playbooks). |
| **Source** | Origin that dispatched the execution (`host-management`, `manual`, or `Automation` bot). |
| **Status** | Real-time execution status badge (`completed` in green, `failed` in red, `running`, `retrying`, or `pending`). |
| **Queue Name** | Dedicated runner or worker queue processing the execution (e.g., `automation`, `admin-admin`, `ha-nodes-queue`). |
| **User** | User account or automation bot that initiated the run (e.g., `admin@localhost`, `automationbot`). |
| **Created At** | Timestamp marking when the execution was dispatched (`YYYY-MM-DD HH:mm:ss`). |
| **Actions** | Context action menu (`...`) for retrying or deleting executions. |

### Table Toolbar Controls

The top-right toolbar provides dynamic Material React Table controls:

* **Search / Global Filter**: Instant full-text search across all execution names, IDs, workflows, and queues.
* **Column Filters**: Filter records by specific field values.
* **Show/Hide Columns**: Toggle table column visibility.
* **Density Toggle**: Switch between compact and standard row spacing.
* **Refresh**: Manually re-fetch the latest execution listings.
* **Create Execution (`+`)**: Launch the new job execution modal.

---

## Triggering an Execution (Create Jobs)

To manually dispatch a new automation run:

* Click the orange **`+`** (Create Execution) button on the table toolbar:

<a href="../../images/automation-executions-add-button.png" class="glightbox">
  <img src="../../images/automation-executions-add-button.png" alt="Create Execution Toolbar Button">
</a>

* The **Create Jobs** modal dialog will open:

<a href="../../images/automation-executions-create-modal.png" class="glightbox">
  <img src="../../images/automation-executions-create-modal.png" alt="Create Jobs Dialog">
</a>

### Configuration Fields

* **Name** *(Required)*: Enter a unique, recognizable identifier for the execution run.
* **Workflow** *(Required)*: Select the automated workflow containing the playbooks or scripts to run.
* **SSH Credential** *(Required)*: Choose the stored credential profile from [Credentials Management](../inventory/creds.md) to authenticate against target hosts.
* **Extra Variables (JSON)** *(Optional)*: Supply custom runtime variables to the playbook or script in valid JSON format (default: `{}`).
* **Retry Count** *(Default: 3)*: Configure maximum retry attempts upon task failure (range: `0` to `10`).
* **Server Source** *(Required)*: Choose the inventory origin for target hosts:
    * **Inventory Servers**: Target servers cataloged within PixelView's Cloud Catalogue.
    * **Cloud Servers**: Query live compute instances directly from integrated cloud providers (e.g., OpenStack regions).
    * **Host Groups**: Target pre-configured clusters defined in [Host Groups Management](../inventory/host-groups.md).
* **Target Servers Selection**: Select one or more target servers or select all in bulk.

### Dialog Actions

* **CANCEL**: Abort execution creation and close the dialog.
* **CREATE**: Submit and dispatch the execution to the assigned runner queue.

---

## Managing Executions (Retry & Delete)

* Locate the target execution row in the table and click the **Actions** menu (**`...`**):

<a href="../../images/automation-executions-context-menu.png" class="glightbox">
  <img src="../../images/automation-executions-context-menu.png" alt="Execution Context Actions Menu">
</a>

### Available Actions

* **Retry**: Re-runs the execution using the identical job configuration, host targets, and credentials. PixelView automatically appends `(retry)` or `(retry N)` to the name to maintain a clean audit history.
* **Delete Execution**: Prompts for confirmation (`Are you sure you want to delete the execution [name]?`) and permanently removes the execution record and its associated job logs.

---

## Inspecting Execution Details & Live Logs

To inspect per-host execution progress, playbook stdout/stderr, and runtime telemetry:

* Locate the execution row in the table (e.g., `hm-7da84a15-dc9a...`):

<a href="../../images/automation-executions-row-select.png" class="glightbox">
  <img src="../../images/automation-executions-row-select.png" alt="Selecting Execution Row">
</a>

* Click on the execution name or ID link to open the **Job Executions** dashboard (`/executions/:id`).

The dashboard displays the **Jobs List** on the left panel (listing all individual playbook or script jobs with status badges) and four detailed tabs on the right:

### 1. Details Tab

The **DETAILS** tab provides high-level run metadata and operational parameters:

<a href="../../images/automation-executions-details-tab.png" class="glightbox">
  <img src="../../images/automation-executions-details-tab.png" alt="Job Execution Details Tab">
</a>

* **Status Badge**: Current status of the selected job (`Completed`, `Failed`, `Running`, or `Pending`).
* **Playbook / Script Name**: The specific playbook or script executed (e.g., `install-kubectl.yaml`).
* **Job ID**: Unique UUID identifier for the individual job with quick-copy control.
* **Queue**: Runner queue assigned to execute this job (e.g., `automation`).
* **Retry**: Current retry attempt index (`0`).
* **Timestamps**: Exact creation and last updated timestamps.
* **Execution ID**: Parent execution identifier linking all concurrent jobs in this run.

### 2. Logs Tab (Real-Time SSE Stream)

The **LOGS** tab provides live, streaming console output of the execution:

<a href="../../images/automation-executions-logs-tab.png" class="glightbox">
  <img src="../../images/automation-executions-logs-tab.png" alt="Job Execution Logs Tab">
</a>

* **Live Stream Alert**: Displays active connection status (e.g., *Job completed. Log stream closed.*).
* **Task-by-Task Execution**: Real-time timestamps and Ansible task output:
    * Task dispatch: `TASK [Download kubectl binary]`
    * Host status: `ok: [119.9.94.30]`
    * Task outputs & stdout: `TASK [Show kubectl version] => {"kubectl_version": "v1.37.0"}`
* **PLAY RECAP**: Complete end-of-playbook summary showing host status counters (`ok`, `changed`, `unreachable`, `failed`, `skipped`, `rescued`, `ignored`).

### 3. Metadata Tab

The **METADATA** tab displays structured JSON telemetry for auditing and debugging:

<a href="../../images/automation-executions-metadata-tab.png" class="glightbox">
  <img src="../../images/automation-executions-metadata-tab.png" alt="Job Execution Metadata Tab">
</a>

* **Execution Configuration**: Records `playbook`, `queue_name`, `retry_count`, and `max_retries`.
* **Execution Results**: Displays `status`, `return_code` (`0` for success), and total execution `duration` in seconds.
* **Secret Protection**: Sensitive authentication data (such as `ansible_ssh_private_key`) is automatically masked with `***` in telemetry payloads.

### 4. Hosts Tab

The **HOSTS** tab displays the targeted host inventory configuration:

<a href="../../images/automation-executions-hosts-tab.png" class="glightbox">
  <img src="../../images/automation-executions-hosts-tab.png" alt="Job Execution Target Hosts Tab">
</a>

* **Target Nodes**: Array of all server IP addresses or hostnames targeted during the run (e.g., `["119.9.94.30"]`).
* **Host Variables**: Connection variables, remote login user (`ansible_user: "root"`), and port definitions.

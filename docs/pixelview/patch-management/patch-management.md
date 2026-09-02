# Patch Management

The **Patch Management** module in PixelView provides a complete, unified control plane for orchestrating operating system updates, software packages, security hotfixes, and custom automation workflows across your cloud infrastructure.

With automated dependency resolution, sequential workflow execution, rolling deployment strategies, and real-time host telemetry, Patch Management ensures systems remain secure, compliant, and operational without service disruption.

---

## Navigating to Patch Management

To access the patching module:

* From the left navigation sidebar in PixelView, expand the **Patch Management** section:

<a href="../../images/patch-sidebar.png" class="glightbox">
  <img src="../../images/patch-sidebar.png" alt="Patch Management Sidebar Navigation">
</a>

The module provides three operational views:
* **Overview (`/patching`)**: Live status dashboard displaying pending actions, top patchset activities, and recent runs.
* **Patchset (`/patchset`)**: Inventory of all created patch definitions, workflows, dependencies, and targeted hosts.
* **Planner (`/planner`)**: Scheduling engine for defining maintenance windows, rollout rates, and batch executions.

---

## Overview Dashboard

Clicking **Overview** opens the live operational summary of your patching estate:

<a href="../../images/patch-overview.png" class="glightbox">
  <img src="../../images/patch-overview.png" alt="Patching Overview Dashboard">
</a>

The overview dashboard is organized into three operational cards:

### Needs Attention Panel

The **Needs Attention** card at the top left immediately alerts administrators to failed or paused runs:

<a href="../../images/patch-needs-attention.png" class="glightbox">
  <img src="../../images/patch-needs-attention.png" alt="Needs Attention Card">
</a>

* **Action Items**: Lists individual hosts or workflows that encountered execution errors (e.g., `ha-node-1`).
* **Decision States**: Flags paused runs waiting for operator approval before proceeding to the next deployment phase.
* **Direct Remediation**: Click any flagged item to jump directly into the host diagnostics and execution logs.

### Recent Runs Stream (Planner Redirection)

The **Recent Runs** stream across the bottom provides a live audit trail of planner executions:

<a href="../../images/patch-recent-runs.png" class="glightbox">
  <img src="../../images/patch-recent-runs.png" alt="Recent Runs Stream">
</a>

* **Status Badges**: Real-time status indicators showing planner run outcomes (`Completed`, `Cancelled`, `Running`, `Failed`).
* **Run Metadata**: Displays the run title, associated patchset, target host group (e.g., `InstallKubectl — ha-host-gtoup`), timestamp, and the triggering user (`admin@localhost`).
* **Planner Redirection**:
  * **View All Link**: Click **View all** in the top-right corner of the card to navigate directly to the full **Planner** (`/planner`) schedule and batch management dashboard.
  * **Individual Run Tracking**: Click on any specific run in the list (e.g., `real-test`) to be redirected into the **Planner Run Execution** view, where you can monitor live per-host and per-workflow progress, inspect output logs, and review stage transitions.

### Top Patchsets Card

The **Top Patchsets** card at the top right ranks your most active patchsets by execution count:

<a href="../../images/patch-top-patchsets.png" class="glightbox">
  <img src="../../images/patch-top-patchsets.png" alt="Top Patchsets Card">
</a>

* Displays activity rankings and execution counts across your clusters (e.g., `InstallKubectl`, `Install kubectl`, `testpatchset`, `testing`).
* **Opening Patchset Details**: Click directly on any patchset name (such as **`InstallKubectl`**) to open its comprehensive configuration, attached workflows, and target host deployment tracking.

---

## Inspecting a Patchset: InstallKubectl

From the **Top Patchsets** card on the Overview dashboard, clicking **`InstallKubectl`** navigates directly to the **Patchset Details** dashboard:

<a href="../../images/patchset-details.png" class="glightbox">
  <img src="../../images/patchset-details.png" alt="Patchset Details View">
</a>

This view provides complete visibility into the patchset configuration, sequential workflows, prerequisite dependencies, and per-host deployment outcomes:

### Execution Metrics Summary
* **Workflows in this patchset**: Number of sequential workflows packaged into this patchset (e.g., `1`).
* **Depends on**: Number of prerequisite patchsets required before execution (e.g., `0`).
* **Hosts applied**: Total count of target hosts where this patchset was deployed (e.g., `3`).
* **Hosts succeeded**: Number of hosts where all workflows completed successfully (e.g., `3`).
* **Hosts failed**: Count of hosts that encountered execution failures (e.g., `0`).

### Configuration & Dependency Details
* **Details Card**: Displays description, execution priority level (`100`), timestamps, system UUID, and ownership labels.
* **Depends On Card**: Defines prerequisite patchset relationships that must execute first. Displays *No dependencies — this patchset can be applied on its own* when standalone.

From this details dashboard, you can follow two primary operational workflows:
* **Managing Workflows**: Inspecting or creating the automation playbooks executed by this patchset.
* **Managing Hosts**: Inspecting host telemetry, attaching credentials, and scoping cloud environments.

---

## Workflow Management Flow

### Inspecting Sequential Workflows

Inside the **InstallKubectl** details view, the **Workflows** panel on the right shows the exact playbooks and scripts assigned to run on each target host:

<a href="../../images/patchset-workflows.png" class="glightbox">
  <img src="../../images/patchset-workflows.png" alt="Patchset Workflows List">
</a>

* **Execution Order**: Lists workflows in their required execution sequence (e.g., `Install kubectl`, UUID `2b96a53d`).
* **Sequential Guarantee**: If an earlier step fails on a host, subsequent steps are halted according to the safety error policy.

---

### Viewing the Workflows Inventory

Clicking into the Workflows catalog displays all available automation workflows configured in the system:

<a href="../../images/automation-workflows.png" class="glightbox">
  <img src="../../images/automation-workflows.png" alt="Automation Workflows Table">
</a>

* **Workflows List**: Displays workflow names (e.g., `ha-workflow`, `mariadb-restart-workflow`, `Install kubectl`), associated playbook counts, script counts, and timestamps.
* **Expandable Assets**: Click the expand arrow (`>`) on any row to view associated Ansible playbooks and attached automation scripts.

---

### Creating a New Workflow

To create a new workflow for use in patchsets:

* Click the **`+`** (Add Workflow) button in the top-right table toolbar:

<a href="../../images/automation-add-workflow.png" class="glightbox">
  <img src="../../images/automation-add-workflow.png" alt="Add Workflow Button">
</a>

* The **Create Workflow** visual pipeline editor opens:

<a href="../../images/create-workflow.png" class="glightbox">
  <img src="../../images/create-workflow.png" alt="Create Workflow Visual Pipeline Builder">
</a>

**Workflow Pipeline Configuration:**
* **Workflow Name** *(Required)*: Enter a unique and descriptive name (e.g., `Install kubectl`).
* **Description**: Detail the operational purpose of the workflow.
* **Pipeline Sequence**:
  * **Start**: Entry point of workflow execution.
  * **Step Type**: Select whether the step executes an Ansible **Playbook** or a custom **Script**.
  * **Select Playbook / Script**: Choose the target asset from the dropdown.
  * **Add Next Step (`+`)**: Insert additional sequential steps into the pipeline.
  * **Stop**: Concludes the pipeline execution.
* Click **CREATE WORKFLOW** to save the workflow.

---

## Host Inspection & Cloud Management Flow

### Selecting a Target Host from Patchset

From the **InstallKubectl Details** dashboard, navigate to the **Hosts** table at the bottom:

<a href="../../images/patchset-details.png" class="glightbox">
  <img src="../../images/patchset-details.png" alt="Patchset Hosts Table">
</a>

* The table tracks per-host execution status (`ha-node-1`, `ha-node-2`, `ha-node-3`), management IP addresses, outcome badges (`Completed`), and timestamps.
* **Opening Host Details**: Click on any target host link (such as **`ha-node-2`**) to inspect its live health, hardware telemetry, and environment placement.

---

### Host Details & Telemetry Inspection

Clicking **`ha-node-2`** opens the comprehensive **Host Details** dashboard:

<a href="../../images/host-details.png" class="glightbox">
  <img src="../../images/host-details.png" alt="Host Details Dashboard">
</a>

**Telemetry Ribbon & Information Panels:**
* **Hardware Telemetry Ribbon**: Real-time metrics for CPU utilization, Memory usage, GPU accelerators, and detected Operating System (`Ubuntu`).
* **General Info**: Host Name (`ha-node-2`), UUID, Management IP Address (`119.9.94.22`), Device Type (`Virtual Machine`), and assigned Credential (`ha-hosts-creds`).
* **Location & Status**: Physical site/rack, operational status badge (`Active`), and operator notes.
* **Infrastructure Placement**: Parent Cloud (`ha-host-inventory`), Region (`ha-hosts-region-1`), and Zone (`ha-zone-london`).
* **Operational Statuses**: Live indicators for Patch Status and Automation Status.

---

### Host Administrative Actions

On the top-right toolbar of the host details page:

<a href="../../images/host-actions.png" class="glightbox">
  <img src="../../images/host-actions.png" alt="Host Action Controls">
</a>

* **Edit Host (Pencil Icon)**: Modify host display names, IP endpoints, device classifications, and notes.
* **Attach Credentials (Key Icon)**: Assign or update SSH keypairs and authentication profiles required for remote patch execution.
* **Delete Host (Trash Icon)**: Remove the host record and detach associated telemetry from PixelView.

---

### Cloud Environment Scoping

The top navigation bar provides breadcrumb navigation and cloud environment switching:

<a href="../../images/inventory-cloud-select.png" class="glightbox">
  <img src="../../images/inventory-cloud-select.png" alt="Cloud Selector & Add Cloud Button">
</a>

* **Breadcrumb Navigation**: Shows the infrastructure path (`ha-host-inventory > ha-hosts-region-1 > ha-zone-london > ha-node-2`).
* **Cloud Selector Dropdown**: Toggle between different cloud environments.
* **Add Cloud (`+ Add Cloud`)**: Register a new cloud environment or cluster partition.

---

### Registering a New Cloud Environment

Clicking **`+ Add Cloud`** opens the cloud registration dialog:

<a href="../../images/add-cloud-modal.png" class="glightbox">
  <img src="../../images/add-cloud-modal.png" alt="Add a New Cloud Dialog">
</a>

Configure the cloud parameters:

| Field | Requirement | Description |
| :--- | :--- | :--- |
| **Name** | Required | Unique identifier name for the cloud environment (e.g., `ha-host-inventory`). |
| **Description** | Optional | Purpose and details of the infrastructure cluster. |
| **Labels** | Optional | Key-value pairs for metadata tagging and dynamic patch targeting (e.g., `env: production`). |
| **Data** | Optional | Custom environment configuration parameters. |

Click **ADD CLOUD** to save and initialize the new environment.

# Ansible Runners

The **Runners** section (`/runners`) under **Automation** provides real-time visibility, capacity monitoring, and lifecycle management for the distributed execution workers running Ansible playbooks and automation tasks in PixelView.

Runners act as worker daemons that consume automation jobs from assigned message queues, execute playbooks against target infrastructure, stream real-time logs back to the platform, and report ongoing health and availability.

---

## Architecture & Worker Queues

PixelView uses a distributed queue-based worker architecture:

* **Dynamic Registration**: Rather than being manually provisioned via the web interface, Ansible Runners register dynamically when their daemon container or service starts up, announcing their unique `Agent ID` and designated `Queue Name`.
* **Queue Isolation**: Workloads can be partitioned across different queues (e.g., `automation`, `admin-admin`, `ha-nodes-queue`) allowing organizations to isolate tasks by network boundaries, compute capacity, or security clearance.
* **Heartbeat & Status Monitoring**: Active runners continuously emit periodic heartbeats (synchronized every 30 seconds in the UI) to signal whether they are `Idle`, `Busy`, or `Offline`.

---

## Navigating to Runners

To inspect runner health, queues, and active workloads:

* In the left navigation sidebar under **Automation**, click **Runners**:

<a href="../../images/automation-runners-table.png" class="glightbox">
  <img src="../../images/automation-runners-table.png" alt="Ansible Runners Overview Table">
</a>

### Runners Table Overview

The main table lists all registered runner instances with 30-second automated polling:

| Column | Description |
| :--- | :--- |
| **Agent** | Unique runner agent identifier (e.g., `runner-1`, `runner-2-py3asdf`) with a briefcase icon and an 8-character copyable UUID chip. |
| **Queue** | Name of the task queue consumed by this worker (e.g., `admin-admin`, `automation`). |
| **Timestamp** | Most recent heartbeat timestamp received from the runner (`YYYY-MM-DD HH:mm:ss`). |
| **Status** | Real-time operational state of the runner worker (`Idle`, `Busy`, `Online`, or `Offline`). |
| **Execution ID** | Clickable link with quick-copy utility pointing directly to the currently assigned or most recent execution run in [Executions](executions.md). |
| **Created At** | Initial registration timestamp when the runner first connected to PixelView. |
| **Updated At** | Timestamp marking the runner's last state or heartbeat synchronization. |
| **Actions** | Context action menu (**`...`**) for managing the runner record. |

### Table Toolbar Controls

* **Search / Global Filter**: Perform full-text filtering across agent names, queues, and execution IDs.
* **Column Filters**: Filter rows by specific status, queue, or timestamp ranges.
* **Show/Hide Columns**: Customize visible column headers.
* **Density Toggle**: Adjust table row padding between compact and relaxed spacing.
* **Refresh**: Instantly re-fetch runner statuses and heartbeats from the API.

---

## Workload Observability

When an automation job is dispatched from [Executions](executions.md), [Playbooks](playbooks.md), or [Rules](rules.md), PixelView routes the task to the designated queue:

1. **Active Job Assignment**: The runner consuming that queue picks up the execution, transitioning its status to active/busy.
2. **Direct Execution Inspection**: The **Execution ID** column displays the active run's UUID. Clicking this link navigates straight to `/executions/:executionId` where operators can view live Server-Sent Events (SSE) terminal output.
3. **Completion & Idle State**: Once all playbook tasks complete, the runner reports return codes, duration, and returns to `Idle` ready for subsequent tasks.

---

## Managing Runners (Decommissioning)

If a runner node has been permanently taken offline, scaled down, or decommissioned, its stale record can be cleaned up from the inventory:

* Click the **Actions** menu (**`...`**) on the target runner row:

<a href="../../images/automation-runners-context-menu.png" class="glightbox">
  <img src="../../images/automation-runners-context-menu.png" alt="Runner Actions Context Menu">
</a>

* Click **Delete Runner**.
* A confirmation prompt will appear:
  ```text
  Are you sure you want to delete the runner "[Agent ID]"?
  ```
* Click **OK** to remove the runner from the inventory.

> [!NOTE]
> Deleting a runner removes its historical registration record from the dashboard. If the runner daemon container is still running and re-connects, it will automatically register itself again upon its next heartbeat.

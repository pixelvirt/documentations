# Ansible Runners

The **Runners** section (`/runners`) under **Automation** provides real-time visibility, capacity monitoring, and lifecycle management for the distributed execution workers running Ansible playbooks and automation tasks in PixelView.

Runners act as worker daemons that consume automation jobs from assigned message queues, execute playbooks against target infrastructure, stream real-time logs back to the platform, and report ongoing health and availability.

---

## Architecture & Worker Queues

PixelView uses a distributed queue-based worker architecture designed for high scalability and security:

* **Dynamic Registration**: Rather than being manually provisioned via the web interface, Ansible Runners register dynamically when their daemon container or service starts up, announcing their unique `Agent ID` and designated `Queue Name`.
* **Queue Isolation**: Workloads can be partitioned across different queues (e.g., `automation`, `admin-admin`, `ha-nodes-queue`), allowing organizations to isolate tasks by network boundaries, compute capacity, or security clearance.
* **Heartbeat & Status Monitoring**: Active runners continuously emit periodic heartbeats (synchronized every 30 seconds in the UI) to signal whether they are `Idle`, `Busy`, or `Offline`.

---

## Runner Volume Mounts & External Playbook / Script Storage

PixelView decouples the automation platform from the physical files being executed:

<a href="../../images/automation-runners-queues.png" class="glightbox">
  <img src="../../images/automation-runners-queues.png" alt="Ansible Runner Subscribed to Automation Queue">
</a>

* **No Code Stored in Database**: Neither Ansible playbooks nor Python scripts are stored directly in PixelView.
* **Host Volume Mounting**: Automation files are maintained externally in a version-controlled Git repository (e.g., GitHub, GitLab) and cloned onto the runner host machine. When the runner container starts up, these directories are mounted into the container:
    * `/opt/pixelvirt/playbooks` &rarr; `/playbooks`
    * `/opt/pixelvirt/scripts` &rarr; `/scripts`
* **Execution by Reference via Queues**: When a job is dispatched from PixelView, the platform sends a message containing the catalog reference (e.g., `install-kubectl.yaml` or `cleanup.py`) over the designated message queue (such as the `automation` queue highlighted above). The assigned runner locates the file directly within its local container mount and executes it.

### Example Runner Deployment Configuration

Here is a production-grade Docker Compose configuration showing how playbooks and scripts are mounted into the runner container:

```yaml
version: '3.8'

services:
  ansible-runner:
    image: pixelvirt/ansible-runner:latest
    container_name: pixelvirt-runner-1
    restart: unless-stopped
    volumes:
      # Host directories cloned from GitHub mounted into the runner
      - /opt/pixelvirt/playbooks:/playbooks:ro
      - /opt/pixelvirt/scripts:/scripts:ro
      # Docker socket if the runner executes containerized tasks
      - /var/run/docker.sock:/var/run/docker.sock:ro
    environment:
      - AGENT_ID=runner-prod-1
      - QUEUE_NAME=automation
      - PIXELVIEW_URL=https://cloud.pixelvirt.com
      - PIXELVIEW_TOKEN=your-runner-authentication-token
```

> [!TIP]
> Mounting playbooks and scripts as read-only (`:ro`) ensures the runner cannot accidentally alter or corrupt the underlying Git repository during execution.

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

* **Active Job Assignment**: The runner consuming that queue picks up the execution, transitioning its status to active/busy.
* **Direct Execution Inspection**: The **Execution ID** column displays the active run's UUID. Clicking this link navigates straight to `/executions/:executionId` where operators can view live Server-Sent Events (SSE) terminal output.
* **Completion & Idle State**: Once all playbook tasks complete, the runner reports return codes, duration, and returns to `Idle` ready for subsequent tasks.

---

## Network Segmentation & Distributed Queue Topologies

PixelView runners eliminate the need to open inbound SSH or management ports from the central PixelView platform into protected enterprise networks:

* **Outbound-Only Worker Connectivity**: Runner daemons establish secure, outbound-only HTTPS and WebSocket connections to PixelView. They pull assigned execution tasks from their subscribed queues and stream telemetry outward, allowing deployment inside private VPCs, behind NAT gateways, or within air-gapped enclaves.
* **Queue-Based Workload Partitioning**:
    * **`automation`**: The default general-purpose queue for routine maintenance, diagnostics, and non-privileged operations.
    * **`admin-admin`**: A restricted high-privilege queue dedicated to root-level platform provisioning, hypervisor management, and kernel upgrades.
    * **Custom Enclave Queues** (e.g., `pci-dss-runners`, `dmz-edge-runners`): Isolate execution workers strictly to specific network subnets or regulatory compliance zones.
* **Health Heartbeats & Automatic Failover**:
    * Runners transmit a heartbeat every 30 seconds confirming resource availability and active daemon status.
    * If a runner misses consecutive heartbeats, its status transitions from `Idle` or `Busy` to `Offline`, alerting operators to worker node degradation.

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

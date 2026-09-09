# Backups Management

The **Backups** module in PixelView provides enterprise-grade backup orchestration, agent management, multi-protocol storage destination configuration, automated scheduling, and audit logging across heterogeneous IT environments.

By integrating the lightweight PixelView backup agent (`virtbackup-agent`) across Linux, Windows, and macOS endpoints, administrators can protect critical server volumes, directories, and databases with automated snapshot policies, scheduled deduplication jobs, and end-to-end restore verification.

---

## Navigating to Backups

To access the Backups subsystem:

* In the left navigation sidebar, click **Backups** to expand the menu, then select **Computers**:

<a href="../../images/backups-menu-select.png" class="glightbox">
  <img src="../../images/backups-menu-select.png" alt="Backups Sidebar Navigation & Menu Selection">
</a>

The Backups module contains five core sections:

1. [**Computers**](#computers-management): Manage registered host computers and deploy new backup agents.
2. [**Destinations**](#backup-destinations): Register remote storage targets including AWS S3 buckets and OpenStack Swift object containers.
3. [**Schedules**](#backup-schedules): Define automated snapshot frequencies, target source paths, and retention rules.
4. [**Backup Logs**](#backup-logs): Real-time and historical execution logs for backup jobs.
5. [**Restore Logs**](#restore-logs): Audit trail and progress tracking for data recovery operations.

---

## Computers Management

The **Computers** console (`/computers`) serves as the central inventory of all servers, workstations, and virtual machines enrolled in the backup fleet.

<a href="../../images/backups-computers-table.png" class="glightbox">
  <img src="../../images/backups-computers-table.png" alt="Computers Inventory Table Overview">
</a>

### Inventory Table Columns

| Column | Description |
| :--- | :--- |
| **Name** | Hostname or custom alias assigned to the computer. |
| **Status** | Current agent operational status (**Active**, **Offline**, or **Error**). |
| **Host** | Fully Qualified Domain Name (FQDN) or primary network identifier. |
| **IP Address** | Primary IPv4 / IPv6 address of the agent host. |
| **Created At** | Timestamp indicating when the computer was first enrolled in PixelView (`YYYY-MM-DD HH:mm`). |
| **Actions** | Context actions menu (`...`) for inspecting agent health, viewing local backup history, or removing the computer. |

### Table Toolbar Controls

* **Global Search**: Filter hosts instantly by name, IP address, or status.
* **Column Filters**: Filter computer records by specific attributes.
* **Show/Hide Columns**: Toggle table column visibility.
* **Density Toggle**: Switch between compact and expanded row displays.
* **Refresh**: Poll the latest agent status from the backup API.
* **Add Computer (`+`)**: Launch the agent deployment modal.

---

## Deploying the Backup Agent (Add Computer)

To register a new server into the backup ecosystem:

1. Click the orange **`+`** (Add item) button in the top-right toolbar:

<a href="../../images/backups-computers-add-button.png" class="glightbox">
  <img src="../../images/backups-computers-add-button.png" alt="Add Computer Toolbar Button">
</a>

2. The **Add Computer Agent** modal will appear with pre-configured, copyable CLI installation commands tailored for Linux, Windows, and macOS:

<a href="../../images/backups-computers-add-modal.png" class="glightbox">
  <img src="../../images/backups-computers-add-modal.png" alt="Add Computer Agent Installation Modal">
</a>

### Platform Installation Procedures

Each operating system tab provides copy-and-paste commands that automatically embed your current PixelView server URL and account API authentication key:

=== "Linux"

    ```bash
    mkdir -p agent-installer && cd agent-installer

    curl -LO https://virtbackup-agent.s3.ap-south-1.amazonaws.com/v1.0.0.zip

    unzip -o v1.0.0.zip

    sudo ./svc.sh install --url <SERVER_URL> --api_key <API_KEY>
    ```

    * **Uninstall**: `sudo ./svc.sh uninstall`
    * **Update**: `sudo ./updater.sh`

=== "Windows"

    Run the following in an elevated PowerShell session:

    ```powershell
    mkdir agent-installer; cd agent-installer

    Invoke-WebRequest -Uri "https://virtbackup-agent.s3.ap-south-1.amazonaws.com/v1.0.0.zip" -OutFile "v1.0.0.zip"

    Expand-Archive -Path .\v1.0.0.zip -DestinationPath . -Force

    .\config.cmd --url <SERVER_URL> --api_key <API_KEY>
    ```

    * **Uninstall**: `.\config.cmd remove`
    * **Update**: `.\config.cmd update`

=== "macOS"

    ```bash
    mkdir -p agent-installer && cd agent-installer

    curl -LO https://virtbackup-agent.s3.ap-south-1.amazonaws.com/v1.0.0.zip

    unzip -o v1.0.0.zip

    ./svc.sh install --url <SERVER_URL> --api_key <API_KEY>
    ```

    * **Uninstall**: `./svc.sh uninstall`
    * **Update**: `./updater.sh`

> [!TIP]
> After executing the install script on the host, the agent immediately handshakes with PixelView over HTTPS and will appear in the **Computers** inventory table within 30 seconds.

---

## Backup Destinations

The **Destinations** console (`/destinations`) manages the remote storage backends where backup archives and deduplicated chunks are encrypted and stored.

<a href="../../images/backups-destinations-overview.png" class="glightbox">
  <img src="../../images/backups-destinations-overview.png" alt="Backup Destinations Overview Table">
</a>

### Adding a Destination

1. Click the orange **`+`** (Add Destination) button in the toolbar:

<a href="../../images/backups-destinations-add-button.png" class="glightbox">
  <img src="../../images/backups-destinations-add-button.png" alt="Add Destination Toolbar Button">
</a>

2. The **Add Destination** configuration dialog opens:

<a href="../../images/backups-destinations-add-modal.png" class="glightbox">
  <img src="../../images/backups-destinations-add-modal.png" alt="Add Backup Destination Modal Dialog">
</a>

3. Configure destination parameters:
    * **Protocol**: Select either **AWS S3** or **OpenStack Swift**.
    * **Name**: Descriptive identifier for this storage target (e.g., `prod-s3-backup-vault`).
    * **Access Key ID**: AWS Access Key or cloud credentials identifier.
    * **Secret Access Key**: Secret access key for storage API access.
    * **Region**: AWS region (e.g., `us-east-1`, `ap-south-1`) or OpenStack region.
    * **Bucket / Container**: Name of the target S3 bucket or Swift storage container.
    * **Endpoint**: Custom S3-compatible endpoint URL (e.g., MinIO, Ceph RGW). Optional when using native AWS S3.
4. Click **ADD DESTINATION** to test connectivity and persist the endpoint.

---

## Backup Schedules

The **Schedules** page (`/backup-schedules`) controls automated recurring backup routines across computers and destinations:

<a href="../../images/backups-schedules-table.png" class="glightbox">
  <img src="../../images/backups-schedules-table.png" alt="Backup Schedules Table Overview">
</a>

### Schedule Configuration Fields

| Column | Description |
| :--- | :--- |
| **Computer** | Target computer agent executing the scheduled backup task. |
| **UUID** | Unique schedule plan identifier with one-click copy helper. |
| **Source** | File system path, volume mount, or directory configured for snapshotting (e.g., `/var/data`, `C:\Backups`). |
| **Destination** | Storage backend target where snapshots are sent. |
| **Frequency** | Execution cadence (**Hourly**, **Daily**, **Weekly**, or **Monthly**). |
| **Time (24H)** | Scheduled trigger time in 24-hour format (`HH:mm`). |
| **Actions** | Quick shortcuts to edit schedule rules, trigger immediate manual execution, or delete the schedule. |

---

## Backup Logs

The **Backup Logs** console (`/backup-logs`) provides centralized visibility into every backup job executed across the infrastructure:

<a href="../../images/backups-logs-overview.png" class="glightbox">
  <img src="../../images/backups-logs-overview.png" alt="Backup Execution Logs Overview Table">
</a>

### Log Columns

* **Computer**: Hostname of the executing computer.
* **Destination**: Storage target endpoint where the backup archive was saved.
* **Backup Type**: Type of backup performed (**Full**, **Incremental**, or **Differential**).
* **Status**: Execution state (**Completed**, **In Progress**, **Failed**, or **Cancelled**).
* **Frequency**: Plan trigger cadence.
* **Created At**: Job start timestamp.

---

## Restore Logs

The **Restore Logs** console (`/restore-logs`) records all data recovery requests and file restoration events:

<a href="../../images/backups-restore-logs-overview.png" class="glightbox">
  <img src="../../images/backups-restore-logs-overview.png" alt="Restore Execution Logs Table">
</a>

### Tracking Recovery Operations

* **Computer**: Target host where files or directories are being recovered.
* **Backup UUID**: Identifier of the source backup snapshot utilized for the recovery.
* **To**: Destination restore path on the host.
* **Created At**: Timestamp when the restore request was submitted.
* **Status**: Current restore process state (**Success**, **Restoring**, or **Failed**).

> [!IMPORTANT]
> When executing restores across production systems, ensure sufficient disk space is available at the target path before initiating the restore job.

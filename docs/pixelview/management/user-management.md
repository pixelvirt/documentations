# User & Group Management

The **Management** section in PixelView provides administrative controls for managing team members, organizing access groups, and configuring granular, module-level permissions.

---

## User Management

The **Users** page (`/users`) provides a centralized view of all registered accounts, enabling administrators to invite team members, adjust roles, toggle account statuses, and configure access control permissions.

### Overview & User Table

Navigate to **Management** > **Users** from the left navigation sidebar:

<a href="../../images/management-sidebar.png" class="glightbox">
  <img src="../../images/management-sidebar.png" alt="Management Navigation Sidebar">
</a>

The users table provides real-time account information:

| Column | Description |
| :--- | :--- |
| **Name** | Full display name of the user. |
| **Email** | Registered email address used for login and notifications. |
| **Status** | Current account state: <br> • `Enabled` (Green badge) — Active access permitted. <br> • `Disabled` (Grey badge) — Account is deactivated. |
| **Role** | System role assigned to the user (`Admin` or `User`). |
| **Actions** | Action menu (`...`) to edit or manage user profile settings. |

**Table Toolbar Controls:**
* **Search / Global Filter**: Quickly filter users across names, emails, and roles.
* **Toggle Column Filters**: Apply column-specific filters.
* **Show/Hide Columns**: Customize which columns are visible in the table.
* **Density Toggle**: Switch between compact and spacious row layouts.
* **Refresh**: Fetch the latest user records from the server.
* **Add User (`+`)**: Open the user invitation modal.

---

### Inviting a New User

To add a new team member to PixelView:

* Click the **`+`** (Add User) icon located in the top-right corner of the table toolbar:
  <a href="../../images/adduser.png" class="glightbox">
    <img src="../../images/adduser.png" alt="Add User Button">
  </a>
* In the **Invite User** modal dialog:
  <a href="../../images/invite-user.png" class="glightbox">
    <img src="../../images/invite-user.png" alt="Invite User Dialog">
  </a>
* Fill in the required fields:
  * **First Name** *(Required)*: The user's first name.
  * **Last Name** *(Required)*: The user's last name.
  * **Email** *(Required)*: A valid email address to receive account credentials.
  * **Role** *(Required)*: Select the system role from the dropdown:
    * **Admin**: Full administrative access across the platform.
    * **User**: Standard user with access scoped by the permissions matrix.
  * **Permissions**: Expand the permissions section to define module-level access (see [Granular Permissions Matrix](#granular-permissions-matrix)).
* Click **Send Invite**.

!!! note "Initial User Password"
    Upon sending the invitation, an initial password is generated following the convention:  
    `FirstName#123` *(e.g., if First Name is "Duke", the default password is `Duke#123`)*.  
    Users should change their password upon their first login under **Settings** > **Profile**.

---

### Updating an Existing User

Administrators can modify account details, toggle access status, and update permission levels at any time:

* Locate the user in the table and click the **`...`** icon in the **Actions** column.
* Select **Edit** to open the **Update User** modal:
  <a href="../../images/update-user.png" class="glightbox">
    <img src="../../images/update-user.png" alt="Update User">
  </a>
* Modify the desired parameters:
  * **First Name** / **Last Name**: Update the user's name.
  * **Role**: Switch between `Admin` and `User`.
  * **Status**: Toggle between `Enabled` and `Disabled` to instantly grant or revoke access.
  * **Permissions**: Adjust individual read/write capabilities across platform modules.
* Click **Update User** to save the changes.

---

### Granular Permissions Matrix

PixelView features a role-based access control (RBAC) permissions matrix that allows fine-grained access control per feature module.

<a href="../../images/user-permissions.png" class="glightbox">
  <img src="../../images/user-permissions.png" alt="Granular Permissions Matrix">
</a>

Permissions can be configured when **Inviting a User** or when **Updating a User**.

**Quick Bulk Actions:**
* **Set all to: Disabled**: Revokes access to all modules.
* **Set all to: Read Only**: Grants view-only access across all modules.
* **Set all to: Read/Write**: Grants full read, write, and execution capabilities.

**Supported Modules & Permission Levels:**

Each module can be independently set to **Disabled**, **Read Only**, or **Read/Write**:

| Module | Scope / Capabilities |
| :--- | :--- |
| **OpenStack** | Compute instances, flavors, keypairs, volumes, networks, routers, and quotas. |
| **Kubernetes** | Clusters, nodes, pods, deployments, services, namespaces, and ConfigMaps. |
| **Reporting** | Analytics, metrics utilization, and executive infrastructure reports. |
| **Cases** | Incident tickets, alert triage, comments, observables, and task management. |
| **Services** | Monitoring data sources and service configurations (Prometheus, Zabbix). |
| **Escalations** | Escalation policy workflows, timing levels, and alert routing paths. |
| **Automation** | Playbooks, workflows, rules, runners, scripts, and execution history. |
| **Inventory** | Catalogue discovery, clouds, regions, zones, servers, and network devices. |
| **Credentials** | Infrastructure API keys, SSH keys, and service secrets. |
| **Host Groups** | Server clustering, tags, and host categorization. |
| **Backup** | Computers, backup destinations, schedules, and restore logs. |
| **Patch Management** | OS patchsets, update planners, and rollout executions. |
| **JSON Bridge** | Custom JSON schema webhooks and external alert ingestion bridges. |

---

## Access Groups

The **Groups** page (`/groups`) allows administrators to organize team members into functional operational units (e.g., `admins`, `Approvers`, `L1 Reviewers`, `L2 Reviewers`) for collaborative incident management, alert assignments, and escalation routing.

### Overview & Groups Table

Navigate to **Management** > **Groups** from the left navigation sidebar:

<a href="../../images/groups.png" class="glightbox">
  <img src="../../images/groups.png" alt="Groups Navigation Sidebar">
</a>

The groups table displays all configured user groups:

| Column | Description |
| :--- | :--- |
| **Name** | Unique name identifier of the group. |
| **Updated At** | Timestamp indicating when the group or its member list was last modified. |
| **Actions** | Action menu (`...`) providing options to **View** or **Delete** the group. |

**Table Toolbar Controls:**
* **Search / Global Filter**: Search across group names.
* **Toggle Column Filters**: Filter specific table columns.
* **Show/Hide Columns**: Customize visible table headers.
* **Density Toggle**: Adjust between compact and relaxed row height.
* **Refresh**: Fetch the latest group records from the server.
* **Add Group (`+`)**: Open the group creation dialog.

---

### Creating a New Group

To create a new access group:

* Click the **`+`** (Add Group) button located in the top-right corner of the table toolbar:
  <a href="../../images/add-group-btn.png" class="glightbox">
    <img src="../../images/add-group-btn.png" alt="Add Group Button">
  </a>
* In the **Add Group** modal dialog:
  <a href="../../images/add-groups.png" class="glightbox">
    <img src="../../images/add-groups.png" alt="Add Group Dialog">
  </a>
* Fill in the group parameters:
  * **Group Name** *(Required)*: Enter a unique and descriptive name for the group (e.g., `admins`, `Approvers`).
  * **Add Members**: Open the dropdown to select one or multiple registered team members to add to the group.
* Click **Create** to save the new group.

---

### Managing Group Actions

In the Groups table, click the **`...`** icon under the **Actions** column for any group:

<a href="../../images/group-actions.png" class="glightbox">
  <img src="../../images/group-actions.png" alt="Group Actions Menu">
</a>

* **View (Eye Icon)**:  
  Opens the group management modal where you can:
  * Inspect the list of active members currently assigned to the group.
  * Add additional users to the group.
  * Remove members from the group.
* **Delete (Trash Icon)**:  
  Prompts a confirmation dialog (`Are you sure you want to delete group <name>?`). Confirming will permanently remove the group from the system.

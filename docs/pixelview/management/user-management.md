
# Management

The **Management** section in PixelView is the administrative hub for managing team members, access groups, automation bots, and permissions.

---

## Users

The **Users** page (`/users`) provides a centralized view of all registered accounts, enabling administrators to invite team members, adjust roles, toggle account statuses, and configure granular, module-level access control permissions.

### 1. Viewing and Managing Users

Navigate to **Management** > **Users** from the left navigation sidebar:

<a href="../../images/management-sidebar.png" class="glightbox">
  <img src="../../images/management-sidebar.png" alt="Management Navigation Sidebar">
</a>

The users table provides real-time information with the following columns:

| Column | Description |
| :--- | :--- |
| **Name** | Full display name of the user. |
| **Email** | Registered email address used for login and notifications. |
| **Status** | Current account state: <br> • `Enabled` (Green badge) — Active access permitted. <br> • `Disabled` (Grey badge) — Account is deactivated. |
| **Role** | System role assigned to the user (`Admin` or `User`). |
| **Actions** | Action menu (`...`) to edit or manage user profile settings. |

#### Table Toolbar Controls
* **Search / Global Filter**: Quickly filter users across names, emails, and roles.
* **Toggle Column Filters**: Apply column-specific filters.
* **Show/Hide Columns**: Customize which columns are visible in the table.
* **Density Toggle**: Switch between compact and spacious row layouts.
* **Refresh**: Fetch the latest user records from the server.
* **Add User (`+`)**: Open the user invitation modal.

---

### 2. Inviting a New User

To add a new team member to PixelView:

1. Click the **`+`** (Add User) icon located in the top-right corner of the table toolbar:
   <a href="../../images/adduser.png" class="glightbox">
     <img src="../../images/adduser.png" alt="Add User Button">
   </a>
2. The **Invite User** modal dialog will open:
   <a href="../../images/invite-user.png" class="glightbox">
     <img src="../../images/invite-user.png" alt="Invite User Dialog">
   </a>
3. Fill in the required fields:
   * **First Name** *(Required)*: The user's first name.
   * **Last Name** *(Required)*: The user's last name.
   * **Email** *(Required)*: A valid email address to receive account credentials.
   * **Role** *(Required)*: Select the system role from the dropdown:
     * **Admin**: Full administrative access across the platform.
     * **User**: Standard user with access scoped by the permissions matrix.
   * **Permissions**: Expand the permissions section to define module-level access (see [Granular Permissions Matrix](#4-granular-permissions-matrix)).
4. Click **Send Invite**.

!!! note "Initial User Password"
    Upon sending the invitation, an initial password is generated following the convention:  
    `FirstName#123` *(e.g., if First Name is "Duke", the default password is `Duke#123`)*.  
    Users should change their password upon their first login under **Settings** > **Profile**.

---

### 3. Updating an Existing User

Administrators can modify account details, toggle access status, and update permission levels at any time:

1. Locate the user in the table and click the **`...`** icon in the **Actions** column.
2. Select **Edit** to open the **Update User** modal:
   <a href="../../images/update-user.png" class="glightbox">
     <img src="../../images/update-user.png" alt="Update User">
   </a>
3. Modify the desired parameters:
   * **First Name** / **Last Name**: Update the user's name.
   * **Role**: Switch between `Admin` and `User`.
   * **Status**: Toggle between `Enabled` and `Disabled` to instantly grant or revoke access.
   * **Permissions**: Adjust individual read/write capabilities across platform modules.
4. Click **Update User** to save the changes.

---

### 4. Granular Permissions Matrix

PixelView features a role-based access control (RBAC) permissions matrix that allows fine-grained access control per feature module.

<a href="../../images/user-permissions.png" class="glightbox">
  <img src="../../images/user-permissions.png" alt="Granular Permissions Matrix">
</a>

Permissions can be configured when **Inviting a User** or when **Updating a User**.

#### Quick Bulk Actions
Use the top quick-selection buttons to bulk apply permissions across all modules at once:
* **Set all to: Disabled**: Revokes access to all modules.
* **Set all to: Read Only**: Grants view-only access across all modules.
* **Set all to: Read/Write**: Grants full read, write, and execution capabilities.

#### Supported Modules & Permission Levels

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

## Groups

The **Groups** page (`/groups`) allows administrators to organize team members into functional operational units (e.g., `admins`, `Approvers`, `L1 Reviewers`, `L2 Reviewers`) for collaborative incident management, alert assignments, and escalation routing.

---

### 1. Viewing Groups

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

#### Table Toolbar Controls
* **Search / Global Filter**: Search across group names.
* **Toggle Column Filters**: Filter specific table columns.
* **Show/Hide Columns**: Customize visible table headers.
* **Density Toggle**: Adjust between compact and relaxed row height.
* **Refresh**: Fetch the latest group records from the server.
* **Add Group (`+`)**: Open the group creation dialog.

---

### 2. Creating a New Group

To create a new access group:

1. Click the **`+`** (Add Group) button located in the top-right corner of the table toolbar:
   <a href="../../images/add-group-btn.png" class="glightbox">
     <img src="../../images/add-group-btn.png" alt="Add Group Button">
   </a>
2. The **Add Group** modal dialog will open:
   <a href="../../images/add-groups.png" class="glightbox">
     <img src="../../images/add-groups.png" alt="Add Group Dialog">
   </a>
3. Fill in the group parameters:
   * **Group Name** *(Required)*: Enter a unique and descriptive name for the group (e.g., `admins`, `Approvers`).
   * **Add Members**: Open the dropdown to select one or multiple registered team members to add to the group.
4. Click **Create** to save the new group.

---

### 3. Managing Group Actions

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

---

## Bots

The **Bots** section (`/bots`) manages automation bots and webhook endpoints for automated task remediation, alert triage, and escalation integration.

---

### 1. Viewing Bots

Navigate to **Management** > **Bots** from the left navigation sidebar:

<a href="../../images/bots.png" class="glightbox">
  <img src="../../images/bots.png" alt="Bots Navigation Sidebar">
</a>

The bots table displays all registered automation bots:

| Column | Description |
| :--- | :--- |
| **Name** | Identifier name of the bot. |
| **URL** | Webhook or API endpoint URL where the bot listens. |
| **Auth Token** | Masked authentication token with toggle-to-view (eye icon) and copy controls. |
| **API Key** | System-generated bot API key with toggle-to-view and copy controls. |
| **Type** | Execution engine: `StackStorm` or `Custom`. |
| **Actions** | Context menu (`...`) to **Delete** the bot. |

#### Table Toolbar Controls
* **Search / Global Filter**: Search across bot names and endpoints.
* **Toggle Column Filters**: Apply column-specific filters.
* **Show/Hide Columns**: Customize visible headers in the table.
* **Density Toggle**: Toggle compact / relaxed row spacing.
* **Refresh**: Reload the latest bot list from the server.
* **Add Bot (`+`)**: Open the bot registration modal.

---

### 2. Adding a Bot

To register a new bot:

1. Click the **`+`** (Add Bot) button in the top-right corner of the table toolbar:
   <a href="../../images/add-bot.png" class="glightbox">
     <img src="../../images/add-bot.png" alt="Add Bot Button">
   </a>
2. The **Add Bot** modal dialog will open:
   <a href="../../images/create-bot.png" class="glightbox">
     <img src="../../images/create-bot.png" alt="Add Bot Modal">
   </a>
3. Fill in the required parameters:
   * **Name** *(Required)*: Unique identifier name for the bot (e.g., `StackStorm Runner`, `Remediation Bot`).
   * **Bot URL** *(Required)*: Endpoint URL or IP where the bot is reachable.
   * **Bot Auth Token** *(Required)*: Authentication token for incoming/outgoing webhooks.
   * **Bot Type** *(Required)*: Select `StackStorm` or `Custom`.
4. Click **Create** to register the bot.

---

### 3. Managing Bot Secrets & Actions

* **Masked Credentials**: Both the **Auth Token** and **API Key** fields are masked by default (`••••••••`) for security. Click the **Eye Icon** to unmask, or click the **Copy Icon** to copy the token directly.
* **Deleting a Bot**: Click the **`...`** action menu in the target bot's row, select **Delete**, and confirm the prompt to permanently remove the bot:
  <a href="../../images/delete-bots.png" class="glightbox">
    <img src="../../images/delete-bots.png" alt="Delete Bot">
  </a>



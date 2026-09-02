# Automation Bots

The **Bots** section (`/bots`) in PixelView manages automation bots and automated webhook endpoints. These bots execute automated remediation workflows, trigger external actions, and handle alert escalation levels without requiring human intervention.

---

## Viewing Automation Bots

Navigate to **Management** > **Bots** from the left navigation sidebar:

<a href="../../images/bots.png" class="glightbox">
  <img src="../../images/bots.png" alt="Bots Navigation Sidebar">
</a>

The bots table provides an inventory of all configured automation bots:

| Column | Description |
| :--- | :--- |
| **Name** | Display name identifying the bot. |
| **URL** | Fully qualified endpoint URL where the bot service listens for events and payloads. |
| **Auth Token** | Authentication token used by PixelView to authenticate outgoing requests to the bot endpoint. Masked with confidential toggle & copy controls. |
| **API Key** | System-generated API key assigned to the bot. Masked with confidential toggle & copy controls. |
| **Type** | Bot execution engine type: <br> • `StackStorm` — Connects to a StackStorm automation action runner. <br> • `Custom` — Custom webhook handler or script integration. |
| **Actions** | Context menu (`...`) allowing administrators to **Delete** the bot. |

**Table Toolbar Controls:**
* **Search / Global Filter**: Search across bot names, types, and URLs.
* **Toggle Column Filters**: Filter specific columns.
* **Show/Hide Columns**: Customize visible headers in the table.
* **Density Toggle**: Toggle between compact and relaxed row height.
* **Refresh**: Fetch the latest bot list from the server.
* **Add Bot (`+`)**: Open the bot creation modal.

---

## Adding a New Bot

To register a new automation bot in PixelView:

* Click the **`+`** (Add Bot) button in the top-right corner of the table toolbar:
  <a href="../../images/add-bot.png" class="glightbox">
    <img src="../../images/add-bot.png" alt="Add Bot Button">
  </a>
* In the **Add Bot** modal dialog, configure the required parameters:
  <a href="../../images/create-bot.png" class="glightbox">
    <img src="../../images/create-bot.png" alt="Add Bot Modal">
  </a>
  * **Name** *(Required)*: A unique name for your bot (e.g., `StackStorm Runner`, `Disk Cleanup Bot`).
  * **Bot URL** *(Required)*: The fully qualified URL or IP endpoint where the bot can be reached.
  * **Bot Auth Token** *(Required)*: The secret token used for payload verification.
  * **Bot Type** *(Required)*: Select the bot engine from the dropdown:
    * **StackStorm**: Standard StackStorm automation integration.
    * **Custom**: Custom webhook endpoint or custom automation bot.
* Click **Create** (shows *Creating...* while registering).

---

## Managing Bot Secrets & Actions

### Viewing and Copying Tokens
Both the **Auth Token** and **API Key** columns use security masking by default (`••••••••`):
* Click the **Eye Icon** to temporarily reveal the plaintext token.
* Click the **Copy Icon** to copy the token directly to your clipboard.

### Deleting a Bot
* In the Bots table, click the **`...`** icon under the **Actions** column for the target bot.
* Select **Delete** (Trash Icon):
  <a href="../../images/delete-bots.png" class="glightbox">
    <img src="../../images/delete-bots.png" alt="Delete Bot">
  </a>
* Confirm the deletion prompt (`Are you sure you want to delete <Bot Name>?`).
* The bot will be permanently detached and removed from the system.

---

## Using Bots in Escalation Policies

Once registered, bots can be automatically assigned to alert escalation workflows:
* In **Escalations** > **Policies**, you can add an escalation level and choose **Assign to Automations**.
* Select the desired bot from the dropdown. When an unresolved incident reaches this escalation tier, PixelView automatically dispatches the alert payload to the bot's configured endpoint.
# Resolved Cases

The **Resolved Cases** section (`/resolved-cases`) under **Cases** serves as PixelView's persistent incident archive and operational retrospective console. 

Once an incident in [Open Cases](open-cases.md) or [My Cases](my-cases.md) is resolved—either manually by an on-call engineer or automatically via a self-healing [Automation Rule](../automation/rules.md)—it transitions into this repository. Teams use Resolved Cases for root cause analysis (RCA), compliance and SLA auditing, recurring pattern detection, and reopening prematurely closed alerts.

---

## Navigating to Resolved Cases

To review resolved incidents and historical post-mortems:

* In the left navigation sidebar under **Cases**, click **Resolved Cases**:

<a href="../../images/cases-resolved-cases-table.png" class="glightbox">
  <img src="../../images/cases-resolved-cases-table.png" alt="Resolved Cases Overview Table">
</a>

### Resolved Cases Table Overview

The table displays all closed incidents with 60-second automatic background polling:

| Column | Description |
| :--- | :--- |
| **Expand (`v`)** | Chevron toggle that expands the row to inspect comments and resolution notes inline. |
| **Title** | The incident summary or headline (e.g., `Service MariaDB down`). Clicking the title opens the complete historical case console. |
| **Hostname** | Affected infrastructure node IP or FQDN (e.g., `119.9.94.19`). |
| **Severity** | Peak incident severity recorded during the outage (**CRITICAL** in red, **HIGH** in orange, **MEDIUM** in yellow, **LOW** in blue). |
| **Duration** | Elapsed time since the incident was initially triggered (e.g., `2 days ago`). |
| **Created at** | Creation timestamp recording when the monitoring alert was captured. |
| **Assigned to** | The final operator or automated bot that remediated the incident (e.g., `AutomationBot`). |
| **Status** | Operational status confirming the case is `resolved`. |
| **Source** | Originating monitoring provider or integration (e.g., `ha-monitoring`). |
| **Actions** | Row context menu (**`...`**) providing quick actions. |

* Selecting a case row highlights the incident:

<a href="../../images/cases-resolved-cases-row-select.png" class="glightbox">
  <img src="../../images/cases-resolved-cases-row-select.png" alt="Selected Resolved Case Row">
</a>

### Table Toolbar Controls

* **Search / Global Filter**: Perform queries across resolved incident titles, hostnames, and sources.
* **Column Filters**: Filter historical incidents by severity tier or creation date.
* **Show/Hide Columns**: Customize table column visibility.
* **Density Toggle**: Switch between compact and spacious row height.
* **Refresh**: Query the backend API for the latest resolved cases.

---

## Re-Opening a Case

If an issue reoccurs after initial mitigation, or if an alert was closed prematurely before verification, engineers can reopen the incident immediately.

PixelView provides two methods to reopen a resolved case:

### Method 1: Row Context Menu

* Click the **Actions** menu (**`...`**) on the resolved case row:

<a href="../../images/cases-resolved-cases-context-menu.png" class="glightbox">
  <img src="../../images/cases-resolved-cases-context-menu.png" alt="Resolved Cases Row Actions Menu">
</a>

* Click **Re-open**.

### Method 2: Case Dashboard Header Button

* Click into any resolved case to open its detail dashboard.
* Click the blue **RE-OPEN** button in the upper-right corner of the header:

<a href="../../images/cases-resolved-cases-reopen-button.png" class="glightbox">
  <img src="../../images/cases-resolved-cases-reopen-button.png" alt="Case Header Re-Open Button">
</a>

> [!NOTE]
> Reopening a case transitions its operational status back to `triggered` or `open`, immediately restoring it to the active triage queue in [Open Cases](open-cases.md) and alerting assigned responders.

---

## Historical Retrospective & Audit Console (`/cases/:id`)

Clicking any resolved case title opens its full post-mortem console. Responders can review the complete timeline of actions taken during the outage across four tabs:

---

### Tab 1: DETAIL (Telemetry & Timeline Audit)

The **DETAIL** tab preserves the immutable chronological timeline of the incident:

<a href="../../images/cases-resolved-cases-detail-tab.png" class="glightbox">
  <img src="../../images/cases-resolved-cases-detail-tab.png" alt="Resolved Case Detail Tab and Investigation Timeline">
</a>

* **Investigation Timeline**: Review each step of the resolution lifecycle—from initial alert ingest (`Triggered`), email/chat dispatches (`Notification`), automated playbook execution (`Automation`), to task completions.
* **Activity Details**: Reference key incident identifiers (**Item UUID**, **Incident Key**, **Service Name**, and timestamps).
* **Trigger Event**: Inspect the exact JSON telemetry received from the monitoring provider at the moment the outage occurred.

---

### Tab 2: TASKS (Action Item Audit)

The **TASKS** tab displays all sub-tasks and checklists that were assigned and completed during remediation:

<a href="../../images/cases-resolved-cases-tasks-tab.png" class="glightbox">
  <img src="../../images/cases-resolved-cases-tasks-tab.png" alt="Resolved Case Tasks Tab">
</a>

* Provides operational accountability by showing which engineer or group handled specific recovery steps.

---

### Tab 3: COMMENTS (Resolution Notes & Post-Mortem)

The **COMMENTS** tab preserves the collaborative chat thread between operators, DBAs, and management:

<a href="../../images/cases-resolved-cases-comments-tab.png" class="glightbox">
  <img src="../../images/cases-resolved-cases-comments-tab.png" alt="Resolved Case Comments Tab">
</a>

* Review root cause hypotheses, terminal logs, and final handover summaries.
* Operators can continue posting follow-up post-mortem notes even after the case is closed.

---

### Tab 4: HISTORY (Pattern Correlation)

The **HISTORY** tab displays all historical alerts that have occurred on the same infrastructure node:

<a href="../../images/cases-resolved-cases-history-tab.png" class="glightbox">
  <img src="../../images/cases-resolved-cases-history-tab.png" alt="Similar Cases on This Host History Tab">
</a>

* **Similar cases on this host**: Shows past alerts for the current node (`119.9.94.19`), allowing reliability engineers to track recurring degradation, service flap frequencies, and verify whether a permanent fix has stabilized the host.

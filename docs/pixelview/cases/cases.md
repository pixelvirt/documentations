# Cases Overview

The **Cases** module in PixelView provides a centralized incident management and alert orchestration hub. It aggregates telemetry and alert triggers from diverse monitoring platforms—such as Prometheus, Alertmanager, Zabbix, and Nagios—as well as manually reported operational issues.

By integrating automated event-driven remediations with human-in-the-loop triage, PixelView empowers operations teams to minimize Mean Time to Acknowledge (MTTA) and Mean Time to Resolution (MTTR).

---

## Core Operational Workflows

The Cases section is organized into three specialized views catering to different stages of the incident lifecycle:

| Section | Role & Primary Function |
| :--- | :--- |
| [**Open Cases**](open-cases.md) | **Active Triage Console**: Centralizes all newly triggered, unacknowledged, and active alerts across your entire infrastructure. Supports manual case creation, severity prioritization, and batch operations. |
| [**My Cases**](my-cases.md) | **Personal Incident Workbench**: Isolates incidents assigned specifically to the authenticated operator or their immediate teams. Features 60-second automated polling and quick ownership transitions. |
| [**Resolved Cases**](resolved-cases.md) | **Incident Archive & Retrospective**: Retains a permanent record of all mitigated and closed issues. Supports post-mortems, SLA auditing, pattern correlation, and incident reopening. |

---

## Key Capabilities

### 1. Centralized Ingestion & Normalization
Incoming alerts from diverse monitoring tools are normalized into a unified schema detailing the impacted **Hostname**, **Service**, calculated **Severity** tier, and raw **Trigger Event** JSON payloads.

### 2. Deep-Dive Investigation Dashboard (`/cases/:id`)
Every case includes a dedicated four-tab investigation console:
* **DETAIL**: Chronological investigation timeline from initial ingest to bot notifications, paired with raw monitoring telemetry.
* **TASKS**: Granular sub-task delegation, allowing lead responders to assign action items to individual engineers or groups (e.g., `admins`).
* **COMMENTS**: Interactive, real-time threaded chat stream for team collaboration and post-mortem notes.
* **HISTORY**: Automated host-level pattern correlation showing past incidents on the same infrastructure node to detect flapping services.

### 3. Integrated Automation & Self-Healing
Cases natively integrate with PixelView's [Automation Rules](../automation/rules.md) and [Workflows](../automation/workflows.md). When an alert triggers, rules can automatically dispatch [Runners](../automation/runners.md) to execute remediation [Scripts](../automation/scripts.md) or Ansible [Playbooks](../automation/playbooks.md), resolving issues without human intervention.
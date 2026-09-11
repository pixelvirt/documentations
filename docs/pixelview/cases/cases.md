# Cases Overview

The **Cases** module in PixelView provides a centralized incident management and alert orchestration hub. It aggregates telemetry and alert triggers from diverse monitoring platforms—such as Prometheus, Alertmanager, Zabbix, and Nagios—as well as manually reported operational issues.

By integrating automated event-driven remediations with human-in-the-loop triage, PixelView empowers operations teams to minimize Mean Time to Acknowledge (MTTA) and Mean Time to Resolution (MTTR).

<a href="../../images/cases-overview.png" class="glightbox">
  <img src="../../images/cases-overview.png" alt="PixelView Cases Module Overview">
</a>

---

## Core Operational Workflows

The Cases module is organized into three specialized operational consoles catering to distinct stages of the incident lifecycle:

| Section | Role & Primary Function |
| :--- | :--- |
| [**Open Cases**](open-cases.md) | **Active Triage Console**: Centralizes all newly triggered, unacknowledged, and active alerts across your entire infrastructure. Supports manual case creation, severity prioritization, multi-select batch triage, and row expansion for inline quick comments. |
| [**My Cases**](my-cases.md) | **Personal Incident Workbench**: Isolates incidents assigned specifically to the authenticated operator or their immediate teams. Features 60-second automated polling, instant ownership handoffs, and focused resolution workflows. |
| [**Resolved Cases**](resolved-cases.md) | **Incident Archive & Retrospective**: Retains a permanent record of all mitigated and closed issues. Supports post-mortems, SLA auditing, pattern correlation across historical outages, and one-click incident reopening. |

---

## Incident Severity & SLA Matrix

PixelView provides standardized operational severity classifications to align team response speed with business impact:

| Severity Tier | UI Badge | Operational Definition | Target MTTA | Target MTTR | Automation & Escalation Behavior |
| :--- | :--- | :--- | :--- | :--- | :--- |
| **Critical** | Red (`critical`) | Catastrophic service outage, total datacenter failure, or critical database unavailability. | < 15 min | < 1 hour | Immediate dispatch to on-call engineers; automated remediation bots trigger at Level 0. |
| **High** | Orange (`high`) | Redundancy loss, severe latency degradation, or partial service disruption impacting multiple users. | < 30 min | < 4 hours | Escalation policy countdown initiates; notification dispatched via primary chat/email webhooks. |
| **Moderate** | Yellow (`moderate`) | Non-blocking service degradation, single node failure in HA cluster, or capacity threshold warnings. | < 2 hours | < 24 hours | Assigned to operational queue; standard escalation rules apply without emergency paging. |
| **Low** | Blue (`low`) | Minor operational anomaly, non-urgent background task warning, or routine telemetry deviation. | < 8 hours | < 72 hours | Logged for routine review during shift handovers; no automated paging triggered. |

---

## Incident Lifecycle Architecture

Every incident in PixelView follows a structured four-stage lifecycle:

* **Ingestion & Normalization**: Monitoring webhooks and API alerts are normalized into a unified incident schema containing target hostnames, impacted services, and calculated severity tiers (**Critical**, **High**, **Moderate**, **Low**).
* **Triage & Ownership**: Operational teams assess the incident via [Open Cases](open-cases.md), assign designated responder teams (e.g., `admins`), or execute bulk triage actions (**Acknowledge**, **Unacknowledge**, **Resolve**).
* **Investigation & Remediation**: Responders drill into the dedicated incident console (`/case/:id`) to review chronological investigation timelines, inspect raw monitoring telemetry, delegate structured sub-tasks, and collaborate via threaded chat.
* **Closure & Retrospective**: Once resolved, cases transition to [Resolved Cases](resolved-cases.md) for post-mortem analysis and SLA compliance reporting, with the ability to reopen if flapping is detected.

---

## Key Capabilities

### Centralized Ingestion and Normalization
Incoming alerts from disparate monitoring tools are transformed into consistent incident models capturing:
* Impacted **Hostname** (FQDN or IP address)
* Source monitoring integration (e.g., `ha-monitoring`, `Prometheus`, `Zabbix`)
* Standardized **Severity** levels
* Raw JSON **Trigger Event** payloads containing full contextual metrics

### Deep-Dive Investigation Console
Every incident provides a comprehensive investigation dashboard with four specialized workspaces:
* **DETAIL**: Chronological investigation timeline from initial ingest to bot notifications, paired with complete activity metadata and raw telemetry.
* **TASKS**: Granular sub-task delegation, allowing lead responders to create, edit, assign, and track action items for individual engineers or groups.
* **COMMENTS**: Real-time collaborative discussion thread for triage findings, terminal output snippets, and handover notes.
* **HISTORY**: Automated host-level correlation showing past incidents on the same infrastructure node to quickly detect flapping services.

### Integrated Automation and Self-Healing
Cases natively integrate with PixelView's [Automation Rules](../automation/rules.md) and [Workflows](../automation/workflows.md). When an alert triggers, rules can automatically dispatch [Runners](../automation/runners.md) to execute remediation [Scripts](../automation/scripts.md) or Ansible [Playbooks](../automation/playbooks.md), mitigating outages without requiring manual human intervention.
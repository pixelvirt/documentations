# Automation

The **Automation** subsystem in PixelView provides an event-driven infrastructure orchestration engine designed to automate routine maintenance, self-heal system incidents, enforce configuration consistency, and orchestrate complex multi-step deployments across distributed infrastructure.

PixelView decouples orchestration definitions from physical code execution: playbooks and scripts reside in your team's Git repositories and are mounted into containerized runner workers, while PixelView manages the catalog, visual pipeline composition, queue routing, and real-time observability.

<a href="../../images/automation-workflows.png" class="glightbox">
  <img src="../../images/automation-workflows.png" alt="PixelView Automation Overview">
</a>

---

## Core Automation Modules

PixelView Automation is structured into seven tightly integrated modules:

* [**Executions**](executions.md): The real-time operations console. Dispatch manual or scheduled runs, stream live task-by-task execution logs over Server-Sent Events (SSE), inspect per-host results, and manage automated retries.
* [**Playbooks**](playbooks.md): The Ansible playbook catalog. Reference infrastructure-as-code YAML definitions mounted directly into runner containers with full GitOps versioning support.
* [**Scripts**](scripts.md): The custom Python automation catalog. Register custom Python scripts for complex API tasks, database cleanups, and system health checks mounted into runner environments.
* [**Workflows**](workflows.md): The visual pipeline builder. Compose sequential automation pipelines chaining multiple playbooks and scripts into unified execution workflows.
* [**Rules**](rules.md): The event-driven trigger engine. Bind monitoring alerts, webhook notifications, and external triggers to automated workflows for hands-free incident self-healing.
* [**Runners**](runners.md): The worker fleet dashboard. Monitor active execution daemons, queue allocations (`automation`, `admin-admin`), heartbeat health, and capacity across your worker infrastructure.
* [**Automation Bots**](../management/bots.md): The automated webhook dispatcher. Integrate external StackStorm or custom remediation engines directly into multi-tier incident escalation policies.

---

## End-to-End Automation Lifecycle

PixelView's automation architecture operates in a clear, synchronized operational loop:

* **Author & Version Control in Git**: DevOps and systems engineers author Ansible playbooks (`.yml`) and Python scripts (`.py`) inside version-controlled repositories on GitHub or GitLab with code review, pull requests, and CI linting.
* **Mount into Runner Workers**: Repositories are cloned onto runner host machines and volume-mounted directly into runner daemon containers under `/playbooks` and `/scripts`.
* **Register Pointers in PixelView**: In [Playbooks](playbooks.md) and [Scripts](scripts.md), operators create catalog definitions that point to the mounted filepaths without uploading or duplicating code in the database.
* **Compose Visual Pipelines**: In [Workflows](workflows.md), operators assemble multi-step sequences combining playbooks and scripts with sequential dependencies.
* **Configure Event Triggers**: In [Rules](rules.md), operators configure event triggers (such as `mariadb-down` or `backup_failure`) to automatically fire specific workflows when alerts arrive.
* **Dispatch, Monitor & Audit**: In [Executions](executions.md), jobs are dispatched to dedicated runner queues. Operators monitor live terminal output with SSE streaming, review host telemetry, and retry failed tasks.

---

## Escalation & Incident Self-Healing Integration

PixelView Automation integrates directly with [Escalation Policies](../escalation/policies.md) and [Cases](../cases/cases.md):

* **Automated Bot Remediation**: Assign an automation bot to specific escalation levels. When an incident reaches that tier, the bot triggers the corresponding self-healing workflow before escalating to human on-call engineers.
* **Alert-to-Rule Binding**: Inbound alerts from monitoring sources (such as Zabbix or Prometheus) automatically match registered rule triggers, executing diagnostic playbooks or remediation scripts instantly.
* **Audit Trail & Governance**: Every execution records the initiating user or bot, target host inventories, execution durations, return codes, and masked credential telemetry for complete compliance tracking.
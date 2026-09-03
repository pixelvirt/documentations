# JSON Bridge (MySQL Query Editor)

The **JSON Bridge** (`/json-bridge`) provides an integrated, web-based database management console and ad-hoc SQL query execution engine directly inside PixelView.

It serves as a secure bridge between PixelView's relational database engines (MySQL and MariaDB) and a RESTful JSON data layer. System engineers, database administrators, and DevOps teams use JSON Bridge to inspect live system tables, diagnose service health, analyze pipeline state, and execute operational SQL queries without requiring external database GUI clients or opening inbound database ports on host firewalls.

---

## Navigating to JSON Bridge

To access the query editor:

* In the left navigation sidebar, click **JSON Bridge** (indicated by the `{ }` code icon):

<a href="../../images/json-bridge-editor-overview.png" class="glightbox">
  <img src="../../images/json-bridge-editor-overview.png" alt="JSON Bridge MySQL Query Editor Overview">
</a>

### Console Layout Overview

The JSON Bridge workspace is arranged into three primary sections:

1. **Top Controls & SQL Editor**: Schema selection, query naming, and a Monaco-powered SQL code editor.
2. **Action Toolbar**: Primary triggers to run, save, and clear queries.
3. **Saved Queries Panel**: A right-hand library for searching, loading, and managing reusable query templates.
4. **Results Area**: A dynamic table pane that renders query outputs with sorting, filtering, and pagination.

---

## Schema & Query Configuration

Before executing a query, define your target database schema and optional query identifier:

<a href="../../images/json-bridge-schema-selector.png" class="glightbox">
  <img src="../../images/json-bridge-schema-selector.png" alt="Schema Selector and Query Name Controls">
</a>

### Configuration Fields

* **Schema** *(Dropdown)*: Dynamically lists all database schemas available on the connected database cluster (e.g., `pixelview`). Selecting a schema sets the database context for all subsequent queries.
* **Query Name** *(Input)*: An optional name used to organize and identify the query (e.g., `stuck backups`, `show_database`, `active_cases`). A name is required when saving a query to your library.

---

## Monaco SQL Code Editor

The center canvas features an embedded Microsoft Monaco editor engine—the same editor that powers Visual Studio Code:

<a href="../../images/json-bridge-sql-editor.png" class="glightbox">
  <img src="../../images/json-bridge-sql-editor.png" alt="Monaco SQL Query Code Editor">
</a>

### Key Editor Features

* **Intelligent Auto-Completion**: Built-in keyword autocompletion for SQL commands (`SELECT`, `SHOW`, `FROM`, `WHERE`, `JOIN`, `GROUP BY`, `ORDER BY`, `COUNT`, `LIMIT`, etc.).
* **Code Snippets**: Quick-insert templates for standard operational patterns:
    * `SELECT * FROM … LIMIT 10;`
    * `SELECT … FROM … WHERE …;`
    * `SELECT COUNT(*) FROM …;`
* **Syntax Highlighting & Formatting**: Full syntax highlighting with line numbering, bracket matching, and indentation.
* **Context Chips**: Header indicators display the active language format (`SQL`) and the targeted database schema (`pixelview`).
* **Copy Query Tool**: Click the copy icon in the upper-right corner of the editor canvas to copy the SQL statement to your clipboard.

---

## Action Controls

Below the code editor, the action toolbar provides execution and persistence controls:

<a href="../../images/json-bridge-action-buttons.png" class="glightbox">
  <img src="../../images/json-bridge-action-buttons.png" alt="Query Action Buttons: Run, Save, Clear">
</a>

### Available Actions

* **RUN QUERY** *(Green)*: Dispatches the SQL statement to the JSON Bridge backend API. The query is executed against the selected database schema, and performance latency is measured and tracked in milliseconds (`elapsedMs`).
* **SAVE QUERY** *(Blue/Purple)*: Persists the current SQL statement, query name, and target schema to your personal saved queries library. If a query with the same name already exists, you will be prompted for overwrite confirmation.
* **CLEAR** *(Dark Outline)*: Resets the editor canvas and clears the query name input field.

---

## Saved Queries Library

The right-hand side panel acts as a persistent query manager for team operations:

<a href="../../images/json-bridge-saved-queries-panel.png" class="glightbox">
  <img src="../../images/json-bridge-saved-queries-panel.png" alt="Saved Queries Library Panel">
</a>

### Managing Saved Queries

* **Search Filter (`Filter queries...`)**: Perform instant real-time filtering across stored query names.
* **My Queries**: Lists your saved queries with total item counts (e.g., `show_database`).
* **Load a Query**: Click on any saved query name to populate the SQL editor immediately with the stored query text and automatically switch to its mapped database schema.
* **Delete a Query**: Remove obsolete or outdated queries with a single click, protected by a safety confirmation dialog.
* **Refresh**: Click the circular refresh icon in the panel header to re-synchronize the list with the server.

---

## Query Results & Telemetry

When a query finishes executing, results are rendered in the bottom **Results** pane:

<a href="../../images/json-bridge-results-area.png" class="glightbox">
  <img src="../../images/json-bridge-results-area.png" alt="Query Results Pane">
</a>

### Results Display Capabilities

* **Interactive Data Grid (`ReactTable`)**: Displays query outputs in a structured table layout with sortable column headers, pagination controls, and row density toggles.
* **Complex Column Support**: Safely formats aggregated columns (such as `COUNT(*)`), joined table identifiers (`u.username`), and clearly distinguishes `NULL` database values in italicized text.
* **Global Search & Column Filtering**: Filter query result rows directly within the browser without having to re-execute the SQL query.
* **Execution Diagnostics**: If a query encounters a syntax error or runtime exception, the results area displays the diagnostic error message returned directly by the MySQL/MariaDB database engine.

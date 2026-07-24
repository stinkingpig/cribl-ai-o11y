# Cribl App for AI Observability

The Cribl AI Observability app gives teams one place to search, investigate, and report on AI telemetry across models, tools, and environments. Built on Cribl’s AI Platform for Telemetry, the app lets you collect once, normalize once, protect sensitive data, visualize AI usage, cost, and potential issues. It lets you dig deep and investigate AI prompts, completion paths, and AI model performance.

## Key Features

| **Feature**                         | **Details**                                                                                                                  |
| ----------------------------------- | ---------------------------------------------------------------------------------------------------------------------------- |
| **Usage & Cost Dashboard**          | Spending trends, token consumption, top users and models, LLM efficiency anti-patterns, and an activity heatmap. Who is using which AI tools, how much they are using them, and the associated token and dollar costs over time.           |
| **Security & Compliance Dashboard** | Sensitive data exposure rates, security findings by type and user, tool-call audit, content risk indicators.                 |
| **Sessions & Traces**               | End-to-end trace visibility, with a filterable session list, span timeline, and full prompt/response/tool-call payloads.               |
| **AI Insights**                     | Conversational AI agent for querying AI usage and sensitive data issues across your org; Usage & Cost and Security personas. |
| **Org View**                        | Hierarchical roll-up of spend, tokens, and requests joined against an org hierarchy lookup.                                  |
| **Goals & Monitors**                | User-defined monitors are continuously evaluated against live data, with persistent session history.                             |

## Data Sources

The Setup Wizard lets admins onboard one or more AI data sources. Each source feeds one of two datasets.

| Source                                                                                                                       | Mechanism                                           | Dataset             |
| ---------------------------------------------------------------------------------------------------------------------------- | --------------------------------------------------- | ------------------- |
| [Claude Compliance](https://platform.claude.com/docs/en/api/compliance)                                                      | Claude compliance API                               | `AI-o11y`           |
| [Claude Code CLI](https://code.claude.com/docs/en/monitoring-usage)                                                          | OTLP gRPC                                           | `AI-o11y`           |
| [Claude Cowork](https://support.claude.com/en/articles/14477985-monitor-claude-cowork-activity-with-opentelemetry)           | OTLP HTTP                                           | `AI-o11y`           |
| [OpenAI Compliance](https://help.openai.com/en/articles/9261474-openai-compliance-platform-for-enterprise-and-edu-customers) | OpenAI compliance API                               | `AI-o11y`           |
| [OpenAI Codex CLI](https://learn.chatgpt.com/docs/config-file/config-advanced#observability-and-telemetry)                   | OTLP gRPC (OpenTelemetry traces)                    | `AI-o11y`           |
| [Cursor](https://cursor.com/docs/account/teams/admin-api)                                                                    | Cursor Admin API                                    | `AI-o11y-analytics` |
| [Tailscale Aperture](https://tailscale.com/docs/aperture/observe-and-export)                                                 | Exported logs                                       | `AI-o11y`           |

### Two-dataset architecture

| Dataset             | Contents                                                               | Used by                                                                  |
| ------------------- | ---------------------------------------------------------------------- | ------------------------------------------------------------------------ |
| `AI-o11y`           | Raw spans — full prompts, responses, tool calls, token counts, cost    | Sessions, Trace detail, Security dashboard, AI Insights security persona |
| `AI-o11y-analytics` | Privacy-safe aggregates — token counts, cost, model/user identity only | Usage & Cost dashboard, Org View, AI Insights consumption persona        |

The Cribl AI Observability app requires a Search Lakehouse engine. These datasets will be created in the selected engine by the setup process.

## Stream Pack

All Cribl Stream resources created by the Setup Wizard, e.g. sources, destinations, and pipelines, are bundled into a single self-contained pack named `observeai`. This keeps all app infrastructure isolated and makes it straightforward to deploy, update, or remove without affecting other Stream configurations.

## Access Control

App access and in-app feature access are governed separately. When App Platform RBAC is enabled, an administrator must first share the installed app with a user or team before they can open it.

| Roles                            | Available surfaces                                              | Manage goals | Configure |
| -------------------------------- | --------------------------------------------------------------- | ------------ | --------- |
| Built-in administrator or owner  | Every app feature                                               | Yes          | Yes       |
| User, editor, viewer, or unknown | Usage & Cost dashboard and consumption AI Insights persona only | No           | No        |

Full access is granted only for built-in role IDs: `admin`, `search_admin`, `stream_admin`, `lake_admin`, `edge_admin`, and `insights_admin`. Admin-looking custom role IDs are not elevated. Role lookup failures fail closed for full-app access and mutations.

## Installation

1. Log in to Cribl and then click on **Apps->View All**
2. Click **Add App->Import from Git**.
3. Paste `https://github.com/criblapps/cribl-ai-o11y.git` and `latest` for the release tag.
4. Click **Import**.

## Getting Started

### First-time setup

1. Navigate to **Settings → Setup** (admin only).
2. **Choose data sources** — select which AI tools to onboard (Aperture, Claude, OpenAI, Cursor, etc.).
3. **Select infrastructure** — pick a Search Lakehouse engine and a Stream config group.
4. **Configure datasets** — set dataset names (defaults to `AI-o11y` and `AI-o11y-analytics`).
5. **Upload org lookup** — optionally upload a CSV org hierarchy to enable the Org View.
6. **Deploy** — the wizard pushes all pipelines, datasets, and lookups to Cribl via API.

Wizard state is persisted in the Cribl KV store. An incomplete setup automatically redirects `canConfigure` users back to Setup on app load.

## Release Versions

| Version | Changes         |
| ------- | --------------- |
| 1.3.0   | Initial release |



# OffboardMe

OffboardMe is a Slack-native knowledge-capture agent built by **PaAmbTomaquetYOle** for the Slack agent hackathon.

When someone leaves a company, the visible handover usually covers accounts, tickets, and assets. The real loss is harder to see: decisions, workarounds, unfinished context, ownership knowledge, and the people who know how things actually work. OffboardMe turns that offboarding moment into a structured knowledge-retention workflow inside Slack.

## What It Does

OffboardMe helps teams preserve operational memory before it disappears:

- Starts an offboarding knowledge-capture flow from a Slack mention.
- Guides the departing teammate through a Slack interview.
- Pulls pending work from collaboration tools through MCP tools.
- Publishes workflow events through Kafka.
- Stores process state, interviews, dossiers, SOP candidates, and knowledge graph data in the backend.
- Exposes the development environment through Docker Compose and Cloudflare Tunnel.

## Why We Built It

Offboarding is usually treated as an administrative checklist. That misses the highest-value part of a departure: context.

OffboardMe focuses on the knowledge that is normally trapped in conversations and individual memory:

- What is still open?
- Who depends on this person?
- What decisions were made and why?
- Which recurring tasks need a new owner?
- Who can answer future questions about this area?

The goal is to make knowledge handover repeatable, searchable, and available to the next teammate.

## Architecture

The project is split into four repositories:

| Repository | Purpose |
| --- | --- |
| `slack-agent` | Slack app, Socket Mode listener, chat commands, guided interviews, SOP detection, Kafka publishing/consuming |
| `backend` | FastAPI service, durable offboarding state, Postgres persistence, Neo4j knowledge graph integration, Kafka consumers |
| `mcp-server` | MCP tools and prompts for Jira, Trello, Slack workspace search, dossier generation, and knowledge graph access |
| `infra` | Docker Compose stack, Cloudflare Tunnel integration, GitHub Actions deployment to the development LXC |

Runtime shape:

```text
Slack Workspace
    |
    | Socket Mode
    v
slack-agent
    |
    | MCP over HTTP
    v
mcp-server
    |
    | Kafka events / REST reads
    v
backend
    |
    +-- Postgres
    +-- Neo4j
    +-- Kafka
```

## Development Environment

The shared development environment runs in a single Docker LXC and is deployed through GitHub Actions.

Core services:

- `slack-agent` on port `3000`
- `backend` on port `8888` inside Docker, exposed locally as configured by infra
- `mcp-server` on port `8000`
- `kafka-ui` on port `8080`
- `neo4j` on port `7474`
- `postgres` on port `5432`
- `cloudflared` for external access through Cloudflare Zero Trust

Cloudflare routes are handled by the `infra` Compose stack and point to Docker service names, not host ports.

## Demo Flow

The Slack app supports controlled chat intents such as:

```text
@OffBoardMe help
```

```text
@OffBoardMe I need to book an interview with @teammate for their offboarding
```

```text
@OffBoardMe show Jira tasks for teammate
```

```text
@OffBoardMe show Trello cards for teammate
```

The offboarding command starts the knowledge-capture process and publishes the corresponding workflow event. Jira and Trello task lookups call MCP tools directly and render human-readable task lists in Slack.

## Project Memory

This workspace also includes a local `knowledge-base/` folder with architecture notes, decisions, changelog entries, backlog mapping, and repository summaries. It is intended as shared memory for future agents and contributors.

## Team

Contribution summaries are based on the project repositories and commit history available in this workspace.

<table>
  <tr>
    <td align="center" width="25%">
      <a href="https://github.com/Ki-re">
        <img src="https://avatars.githubusercontent.com/u/96847443?v=4" width="96" height="96" alt="Erik" />
      </a>
      <br />
      <strong>Erik</strong>
      <br />
      <a href="https://github.com/Ki-re">@Ki-re</a>
    </td>
    <td>
      Led the shared infrastructure work: Docker LXC development environment, Cloudflare Tunnel setup, GitHub Actions deployment flow, infra repository coordination, environment configuration, and final runtime validation.
    </td>
  </tr>
  <tr>
    <td align="center" width="25%">
      <a href="https://github.com/Yearsuck">
        <img src="https://avatars.githubusercontent.com/u/118774478?v=4" width="96" height="96" alt="Ernest Rull Turigas" />
      </a>
      <br />
      <strong>Ernest Rull Turigas</strong>
      <br />
      <a href="https://github.com/Yearsuck">@Yearsuck</a>
    </td>
    <td>
      Contributed across the Slack agent, backend, and MCP server, including offboarding workflow implementation, integration behavior, testing, and feature work around the knowledge-capture flow.
    </td>
  </tr>
  <tr>
    <td align="center" width="25%">
      <a href="https://github.com/luis-valdivieso">
        <img src="https://avatars.githubusercontent.com/u/114484527?v=4" width="96" height="96" alt="Luis Valdivieso" />
      </a>
      <br />
      <strong>Luis Valdivieso</strong>
      <br />
      <a href="https://github.com/luis-valdivieso">@luis-valdivieso</a>
    </td>
    <td>
      Contributed implementation work across the Slack agent, backend, and MCP server, including service behavior, integration support, and test coverage for the offboarding system.
    </td>
  </tr>
  <tr>
    <td align="center" width="25%">
      <a href="https://github.com/oorbea">
        <img src="https://avatars.githubusercontent.com/u/145404641?v=4" width="96" height="96" alt="Oriol Orbea" />
      </a>
      <br />
      <strong>Oriol Orbea</strong>
      <br />
      <a href="https://github.com/oorbea">@oorbea</a>
    </td>
    <td>
      Drove a large part of the application implementation across `slack-agent`, `backend`, and `mcp-server`, including architecture, domain workflows, Kafka event contracts, MCP integrations, and repository-level documentation.
    </td>
  </tr>
</table>

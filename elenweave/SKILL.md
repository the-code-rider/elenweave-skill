---
name: elenweave
description: Manage Elenweave boards through the local REST API and build rich visual explanations. Use when asked to create a board, create a board from node and edge data, rename or update a board, append nodes and edges, inspect board API responses, explain how code works visually, summarize recent commits as a diagram, or present an implementation plan as an explorable board.
---

# Elenweave

## Overview
Use this skill to perform project-scoped board operations against the local Elenweave server (`server/index.js`).

## URL Resolution
Resolve the API base URL in this order:
1. Use an explicit base URL provided by the user or task context.
2. Otherwise use `ELENWEAVE_BASE_URL` from environment.
3. Otherwise default to `http://127.0.0.1:8787`.

When reporting commands, include the resolved base URL explicitly.

## Quick Start
1. Ensure the server is running (`npm run server`).
2. Resolve base URL using the URL Resolution order above.
3. Ensure a project exists for the current repo and broad scenario (see Project Strategy).
4. Send JSON with `Content-Type: application/json`.
5. Load `references/endpoints.md` for request and response examples.
6. Load `references/rich-diagram.md` when building explanatory diagram boards.

## Endpoints
- `GET /api/projects`
- `POST /api/projects`
- `GET /api/projects/:projectId`
- `PATCH /api/projects/:projectId`
- `DELETE /api/projects/:projectId`
- `GET /api/projects/:projectId/boards`
- `POST /api/projects/:projectId/boards`
- `GET /api/projects/:projectId/boards/:boardId`
- `PATCH /api/projects/:projectId/boards/:boardId`
- `PUT /api/projects/:projectId/boards/:boardId`
- `DELETE /api/projects/:projectId/boards/:boardId`
- `POST /api/projects/:projectId/boards/:boardId/nodes`

## Project Strategy (Repo + Scenario)
Create or reuse one project per repository per broad scenario.
- Determine repo key from current workspace folder name unless user explicitly provides one.
- Determine scenario key from user intent (examples: `architecture`, `debugging`, `onboarding`, `integration`, `release-plan`).
- Use project name pattern: `<repoKey> - <Scenario Title>`.
- Call `GET /api/projects` and match by case-insensitive name first.
- If missing, create via `POST /api/projects` with `{ "name": "<repoKey> - <Scenario Title>" }`.
- Create multiple boards inside that project for focused subtopics.

## Workflows
### Ensure or create project
- Call `GET /api/projects`.
- Reuse matching project or create one with `POST /api/projects`.
- Return `project.id`, `project.name`, and timestamps.

### Create a board with data
- Create board: `POST /api/projects/:projectId/boards` with `{ "name": "Board Name" }`.
- Optionally include initial graph payload (`nodes`, `edges`, `nodeOrder`, `meta`, `notifications`) when creating.
- For full deterministic update, use `PUT /api/projects/:projectId/boards/:boardId`.

### Update an existing board
- Rename only: `PATCH /api/projects/:projectId/boards/:boardId` with `{ "name": "New Name" }`.
- Replace board state: `PUT /api/projects/:projectId/boards/:boardId` with full payload.
- Append incrementally: `POST /api/projects/:projectId/boards/:boardId/nodes` with `nodes` and `edges` arrays.

## Rich Diagram Composition
Use component combinations to make explanations scannable, visual, and decision-ready.

### Code explanation pattern
- Add one `html-text` node for intent or problem statement.
- Add one `CodeSnippet` node for the concrete excerpt.
- Add one `MermaidBlock` node to show flow/state at a glance.
- Add one `MarkdownBlock` node for edge cases plus pros and cons.
- Connect with directional edges: intent -> code -> diagram -> analysis.

### Data-flow or logic sequence pattern
- Create one stage node per step left-to-right (`html-text` or `MarkdownBlock`).
- Use `MermaidBlock` for canonical sequence or branching behavior.
- For stages that transform data, attach nearby `CodeSnippet` nodes.
- Use `SvgBlock` when Mermaid cannot express custom infographic needs (legends, domain icons, custom callouts).
- Use short edge labels (`input`, `transform`, `error`, `fallback`, `tradeoff`).

### Media-augmented pattern
- Use `ImageViewer` for architecture screenshots or diagrams when an external visual source adds context.
- Place image beside the related stage and connect with edge label `reference`.

## Guardrails
- Expect `404` (`ProjectNotFound` or `BoardNotFound`) for unknown ids.
- Expect `400` (`InvalidRequest`) for invalid ids or malformed JSON bodies.
- Prefer `PUT` when deterministic full-state updates are required.
- Prefer `POST /nodes` when only adding graph elements.
- Keep node IDs unique and stable during append operations.
- For API-created images, provide `data.src` as an accessible URL. Browser-local `assetId` flows are app-side only.

# Elenweave Board API Reference

Resolve base URL in this order:
1. User-specified URL
2. `ELENWEAVE_BASE_URL`
3. `http://127.0.0.1:8787`

## Project Endpoints

### `GET /api/projects`
List project metadata.

### `POST /api/projects`
Create a project.

Request:
```json
{ "name": "elenweave-app - architecture" }
```

### `GET /api/projects/:projectId`
Fetch project metadata and its board index.

### `PATCH /api/projects/:projectId`
Rename a project.

Request:
```json
{ "name": "elenweave-app - onboarding" }
```

### `DELETE /api/projects/:projectId`
Delete project and its boards.

## Board Endpoints (Project Scoped)

### `GET /api/projects/:projectId/boards`
List boards for a project.

### `POST /api/projects/:projectId/boards`
Create a board.

Request (empty board):
```json
{ "name": "Prompting Flow" }
```

Request (board with initial graph):
```json
{
  "name": "Prompting Flow",
  "nodes": [],
  "edges": [],
  "nodeOrder": [],
  "meta": null,
  "notifications": []
}
```

### `GET /api/projects/:projectId/boards/:boardId`
Fetch one board payload.

### `PATCH /api/projects/:projectId/boards/:boardId`
Rename a board.

Request:
```json
{ "name": "Prompting Flow v2" }
```

### `PUT /api/projects/:projectId/boards/:boardId`
Write full board state.

Request (shape):
```json
{
  "board": {
    "id": "board_x",
    "name": "Prompting Flow v2",
    "meta": null,
    "nodeOrder": [],
    "nodes": [],
    "edges": [],
    "notifications": []
  }
}
```

### `DELETE /api/projects/:projectId/boards/:boardId`
Delete a board.

### `POST /api/projects/:projectId/boards/:boardId/nodes`
Append nodes and edges without replacing full board.

Request:
```json
{
  "nodes": [{ "id": "n1", "type": "html-text", "text": "Start" }],
  "edges": []
}
```

## Project-First Flow (Repo + Scenario)
1. Resolve project name as `<repo> - <scenario>`.
2. `GET /api/projects` and match by name.
3. If missing, `POST /api/projects`.
4. `GET /api/projects/:projectId/boards`.
5. Create/update board through project-scoped board endpoints.

## Common Status Codes
- `200` success (`GET`, `PATCH`, `PUT`, `DELETE`, `POST /nodes`)
- `201` created (`POST /api/projects`, `POST /api/projects/:projectId/boards`)
- `400` invalid body or invalid id
- `404` project, board, or route not found
- `409` lock timeout conflict

# Rich Diagram Patterns for Elenweave

Use these patterns when creating explanatory boards through API payloads.

## Component Payload Templates

### CodeSnippet node
```json
{
  "id": "code_1",
  "type": "html",
  "component": "CodeSnippet",
  "x": 460,
  "y": 220,
  "w": 380,
  "h": 440,
  "props": { "title": "Parser", "filename": "parser.js", "language": "javascript" },
  "data": { "code": "function parse(v){ return JSON.parse(v); }" }
}
```

### MarkdownBlock node (edge cases + pros/cons)
```json
{
  "id": "md_1",
  "type": "html",
  "component": "MarkdownBlock",
  "x": 860,
  "y": 220,
  "w": 380,
  "h": 260,
  "props": { "title": "Trade-offs" },
  "data": {
    "markdown": "## Edge Cases\n- Empty payload\n- Duplicate ids\n\n## Pros\n- Deterministic writes\n\n## Cons\n- Larger payloads"
  }
}
```

### MermaidBlock node
```json
{
  "id": "mermaid_1",
  "type": "html",
  "component": "MermaidBlock",
  "x": 460,
  "y": 700,
  "w": 420,
  "h": 300,
  "props": { "title": "Request Flow" },
  "data": {
    "mermaid": "flowchart LR\n  A[Input] --> B{Valid?}\n  B -->|Yes| C[Persist]\n  B -->|No| D[Return 400]"
  }
}
```

### SvgBlock node
```json
{
  "id": "svg_1",
  "type": "html",
  "component": "SvgBlock",
  "x": 920,
  "y": 700,
  "w": 420,
  "h": 300,
  "props": { "title": "Custom Infographic" },
  "data": {
    "svg": "<svg viewBox='0 0 360 180' xmlns='http://www.w3.org/2000/svg'><rect x='24' y='30' width='88' height='32' fill='#b9e6ff'/><text x='30' y='50' font-size='12'>Fast path</text><rect x='134' y='30' width='88' height='32' fill='#ffe9ad'/><text x='140' y='50' font-size='12'>Fallback</text><rect x='244' y='30' width='88' height='32' fill='#ffc9c9'/><text x='250' y='50' font-size='12'>Failure</text></svg>"
  }
}
```

### ImageViewer node
```json
{
  "id": "img_1",
  "type": "html",
  "component": "ImageViewer",
  "x": 1260,
  "y": 220,
  "w": 320,
  "h": 200,
  "props": { "title": "System diagram", "alt": "Service boundaries" },
  "data": { "src": "https://example.com/diagram.png" }
}
```

## Sequence Recipe (Code + Mermaid + SVG + Markdown)
1. Add a start node (`html-text`) describing scope.
2. Add stage nodes for the core sequence.
3. Add `CodeSnippet` near each critical implementation stage.
4. Add `MermaidBlock` to summarize full logic and branching.
5. Add `SvgBlock` for custom infographic details Mermaid cannot express well.
6. Add `MarkdownBlock` for edge cases and pros/cons.
7. Use directional edges with labels (`input`, `transform`, `error`, `fallback`, `tradeoff`).

## Recipe: Explain How Code Works
1. Add a `html-text` root node titled `What this module does` with one-sentence intent.
2. Add `CodeSnippet` nodes for 2-4 key functions (entrypoint, branch logic, side effects, persistence).
3. Add one `MermaidBlock` that maps runtime flow and decision points.
4. Add a `MarkdownBlock` titled `Key invariants` listing assumptions, failure paths, and recovery behavior.
5. Connect intent -> function snippets -> flow -> invariants with short edge labels (`calls`, `guards`, `writes`, `returns`).
6. Keep snippets short and focused on the lines that explain behavior, not boilerplate.

## Recipe: Explain Previous Few Commits
1. Start with one `MarkdownBlock` timeline node summarizing the selected commit window (`N-2`, `N-1`, `N`).
2. Add one lane per commit using `html-text` nodes: `problem`, `change`, `impact`.
3. For each commit, attach a `CodeSnippet` node that shows the most representative diff hunk.
4. Add a `MermaidBlock` that compares before vs after behavior or control-flow changes.
5. Add one `MarkdownBlock` for risk/regression notes and test coverage gaps.
6. Link commits chronologically left-to-right and link each commit to the behavior comparison node.

## Recipe: Explain Implementation Plan
1. Add a `html-text` node for objective and non-goals.
2. Add phase nodes (`Phase 1`, `Phase 2`, `Phase 3`) using `MarkdownBlock` with deliverables and acceptance checks.
3. Add `CodeSnippet` nodes to show expected touch points per phase (file/function anchors).
4. Add a `MermaidBlock` dependency graph for sequencing and blockers.
5. Add a `MarkdownBlock` for risks, mitigations, and rollback strategy.
6. Use edge labels that express planning state (`depends-on`, `enables`, `validates`, `fallback`).

## Layout Rules
- Keep a primary left-to-right spine for sequence readability.
- Keep annotation nodes (code, mermaid, svg, markdown, image) near their owning stage.
- Prefer 6-14 nodes per diagram cluster before splitting into another chain.
- Keep each node single-purpose; avoid duplicated narrative.

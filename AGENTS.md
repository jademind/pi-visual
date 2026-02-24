# AGENTS.md — pi-visual memory

## Goal
Minimal Pi extension for visual enrichment of the **latest** output only.

## Product intent
- Enrich pure text output with useful visuals when possible.
- Render markdown-rich output in Pi overlay.
- Render Mermaid when available.
- Render simple charts from numeric markdown tables.
- Provide a stable latest visual state for external tools (e.g. status bars).

## Architecture (current)
- State model: **single latest snapshot** (no timeline/history).
- Capture sources: `assistant`, `toolResult`.
- Capture policy: dedup by content hash, bounded text size.
- Main action: `/pi-visualize`.
- Alias: `/pi-visual`.
- Widget: compact one-line status, toggleable.
- Export targets (stable):
  - `.pi/visual-cache/latest.json`
- Mermaid rendering: best-effort (`node_modules/.bin/mmdc` first, `npx` fallback).
- Failure mode: Mermaid errors are non-fatal; export still succeeds with text/chart enrichment.
- Security: cache writes constrained to `.pi/visual-cache` paths.
- Reliability: atomic write for `latest.json`.
- Dependencies: minimal (no extra chart libs).

## Commands (only)
- `/pi-visualize`
- `/pi-visual` (alias)
- `/pi-visual-clear`
- `/pi-visual-widget`
- `/pi-visual-test` (prompt template)

## Repository cleanup status
- Removed legacy command surface (`render`, `doctor`, `samples`, `export-svg`, etc.).
- Removed `samples/` directory.
- Tightened README + package files.
- Package dry-run currently passes.

## Quick test flow
1. `pi install .`
2. run `/pi-visual-test`
3. ask assistant to return the payload exactly
4. run `/pi-visualize`
5. verify:
   - inspector opens
   - `.pi/visual-cache/latest.json` exists

## Mermaid prerequisite (optional but recommended)
```bash
npm i -D @mermaid-js/mermaid-cli
```

## TODO
- Optimization pass: add per-section hashing (markdown/chart/mermaid) and skip regenerating unchanged inline assets to make repeated `/pi-visualize` runs near-instant.

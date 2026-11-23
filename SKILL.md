---
name: vue-fabric-sdk
description: Use when integrating @4xian/vue-fabric into a project, querying PaintBoard or tool configuration, explaining export or event behavior, or reading vue-fabric source code to diagnose SDK issues.
---

# Vue Fabric SDK

Use this skill for tasks about consuming or diagnosing the public `@4xian/vue-fabric` SDK from <https://github.com/4xian/vue-fabric>.

## Scope

- Install and integrate the SDK into Vue, ESM, or UMD projects
- Initialize the board and register tools
- Query board methods, options, events, and export features
- Query tool-specific configuration and usage
- Explain freehand drawing with `PenTool`
- Inspect source files to explain behavior or debug issues

## Source Priority

Always prefer real SDK sources in this order:

1. Public exports: `src/index.ts`
2. Structural lookup: CodeGraph when `.codegraph/` is present; otherwise use targeted source reads and text search
3. Option/data definitions: `types/index.d.ts`; only the types re-exported by `src/index.ts` are package-root named imports
4. Main board implementation: `src/core/PaintBoard.ts`
5. Tool implementations: `src/tools/*.ts`
6. Built-in UI helpers: `src/ui/Toolbar.ts`, `src/ui/ColorPicker.ts`
7. Defaults and runtime behavior: `src/utils/settings.ts`, `src/core/CanvasManager.ts`, `src/utils/export.ts`, `src/utils/UndoRedoManager.ts`, `src/utils/PersonTracker.ts`, `src/utils/layer.ts`
8. Behavior checks: `tests/integration/*.test.ts`, `tests/unit/**/*.test.ts`
9. Example usage: `README.md`, `demo/index.html`, `demo/esm/main.js`

Treat old prose docs as secondary if they disagree with current code. Read exact defaults from `settings.ts` and actual event payloads from emit sites; a custom-data interface is not automatically an event payload.

## Response Workflow

1. For install or setup questions:
   - Read `references/quickstart.md`
   - Then read `references/integration.md`
   - Return the smallest working example first

2. For board API or configuration questions:
   - Read `references/paint-board-api.md`
   - Confirm imports against `src/index.ts`, option shapes against `types/index.d.ts`, and methods against `src/core/PaintBoard.ts`

3. For tool usage or options:
   - Read `references/tools-api.md`
   - Then inspect the matching file under `src/tools/`

4. For toolbar, color picker, events, export, history, background image, or person tracking:
   - Read `references/toolbar-colorpicker.md`
   - Read `references/events-and-export.md`

5. For source-oriented debugging:
   - Read `references/source-map.md`
   - Then inspect the relevant implementation and tests

6. For bug diagnosis or behavior mismatch:
   - Read `references/troubleshooting.md`
   - Explain which part is confirmed by source and which part is inferred

## Answering Rules

- Start with the direct integration or diagnosis answer.
- Use exact exported names from the SDK, not renamed legacy aliases unless you explain them.
- The default board export and named `FabricPaint` export are the same class; `PaintBoard` is a consumer-selected alias.
- For option types not re-exported at the package root, infer them from constructors or methods instead of inventing named imports.
- When code and README differ, say that source wins.
- When behavior is inferred from implementation rather than stated in docs, say so explicitly.
- Prefer minimal runnable examples over broad boilerplate.
- When listing options, group them by object or feature instead of dumping one flat wall of text.

## Reference Files

- Install and lifecycle: `references/quickstart.md`
- Vue, ESM, and UMD integration: `references/integration.md`
- PaintBoard methods and options: `references/paint-board-api.md`
- Drawing tools and tool options: `references/tools-api.md`
- Toolbar and color picker: `references/toolbar-colorpicker.md`
- Events, export/import, history, helpers, person tracking: `references/events-and-export.md`
- Source map: `references/source-map.md`
- Common debugging paths: `references/troubleshooting.md`

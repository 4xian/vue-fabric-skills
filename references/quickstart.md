# Quick Start

## Install

`fabric` is a peer dependency and must be installed explicitly.

```bash
pnpm add @4xian/vue-fabric fabric
```

Expected peer version: `fabric@^6.0.0`.

## Default Import

The package default export is the board class. Consumers often alias it as `PaintBoard`.

```ts
import PaintBoard, { SelectTool, LineTool, PenTool } from "@4xian/vue-fabric";
import "@4xian/vue-fabric/style.css";
```

Named export is `FabricPaint`.

The package root re-exports selected types such as `FabricPaintOptions`, `ToolName`, and `TraceOptions`. Argument and per-tool option interfaces present in `types/index.d.ts` are not necessarily root exports; check `src/index.ts` before documenting a package-root type import.

## Minimum Lifecycle

1. Create a real DOM container.
2. `new PaintBoard(container, options)`.
3. Call `.init()`.
4. Register tools with `.registerTool(name, tool)`.
5. Activate one tool with `.setTool(name)`.
6. Destroy the board on teardown.

## Minimal ESM Example

```ts
import PaintBoard, { SelectTool, DragTool, LineTool, PenTool } from "@4xian/vue-fabric";
import "@4xian/vue-fabric/style.css";

const board = new PaintBoard("#canvas-container", {
  width: 1000,
  height: 600,
  zoomOrigin: "center",
  enableWheelZoom: false,
  autoResize: true,
  autoResizeMode: "canvas",
  pixelRatio: "auto",
}).init();

board
  .registerTool("select", new SelectTool())
  .registerTool("drag", new DragTool())
  .registerTool("line", new LineTool())
  .registerTool("pen", new PenTool())
  .setTool("select");
```

## First Background Image Call

Constructor typing includes `backgroundImage`, but current `init()` does not auto-apply it.

Call `setBackgroundImage(...)` yourself:

```ts
await board.setBackgroundImage({
  source: "/demo/static/draw-bg.png",
  scaleMode: "stretch",
  backgroundVpt: true,
});
```

## First Checks

- If styles look wrong, verify `@4xian/vue-fabric/style.css` is imported.
- If `addText` or `addImage` fails, verify `TextTool` or `ImageTool` was registered first.
- If wheel zoom does nothing, verify `enableWheelZoom: true`.
- If constructor `backgroundImage` appears ignored, that is current source behavior; use `setBackgroundImage(...)`.
- If responsive resizing must keep object points aligned to a stretched background, switch to `autoResizeMode: 'viewport'`; omit `referenceSize` for initial-container reference, or pass an explicit logical size.

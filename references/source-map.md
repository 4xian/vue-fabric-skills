# Source Map

Use this when the user asks where behavior really comes from.

## Public Entry and Types

- `src/index.ts`
  - public exports
- `types/index.d.ts`
  - public option names and data shapes

## Core

- `src/core/PaintBoard.ts`
  - board lifecycle
  - tool registration
  - background image handling
  - zoom-invariant visuals
  - resize / autoResize, including `autoResizeMode`, `autoResizeFit`, and `referenceSize`
  - import / export
  - helper visibility
  - `PersonTracker` creation
- `src/core/CanvasManager.ts`
  - zoom transform math
  - animated viewport transform path
  - wheel zoom binding
  - fit scale, content-anchor zoom, reference-size viewport fit, and reset-to-display viewport math
  - `canvas:zooming` / `canvas:zoomed` emission
- `src/core/EventBus.ts`
  - event subscription/dispatch

## Tools

- `src/tools/BaseTool.ts`
- `src/tools/SelectTool.ts`
- `src/tools/DragTool.ts`
- `src/tools/LineTool.ts`
- `src/tools/PolylineTool.ts`
- `src/tools/AreaTool.ts`
- `src/tools/CurveTool.ts`
- `src/tools/PenTool.ts`
- `src/tools/RectTool.ts`
- `src/tools/TextTool.ts`
- `src/tools/ImageTool.ts`

## Utilities

- `src/utils/export.ts`
  - JSON normalization
  - helper relink
  - event rebind after import
  - pen runtime event rebind after JSON import
- `src/utils/PersonTracker.ts`
  - markers, traces, ripple animation, zoom compensation
- `src/utils/UndoRedoManager.ts`
  - history snapshots
- `src/utils/layer.ts`
  - object layer assignment and stable ordering of helper / tracker families
- `src/utils/settings.ts`
  - defaults and `CustomType`
- `src/utils/areaEvents.ts`
  - area helper visibility and area runtime bindings
- `src/utils/rectEvents.ts`
  - rectangle runtime bindings

## UI

- `src/ui/Toolbar.ts`
  - built-in floating toolbar
- `src/ui/ColorPicker.ts`
  - standalone color picker

## Examples and Verification

- `demo/index.html`
  - richest browser demo
- `tests/unit/core/PaintBoard.test.ts`
- `tests/unit/core/CanvasManager.test.ts`
- `tests/integration/PaintBoard.test.ts`
- `tests/integration/PaintBoardZoomInvariant.test.ts`
- `tests/integration/PersonTracker.test.ts`
- `tests/integration/export.test.ts`

## High-Value Current Facts

- constructor `backgroundImage` option is typed but not auto-applied in `init()`
- `pixelRatio` is now display/backstore only
- `FabricPaintOptions` is re-exported from the package root, while internal types such as `AddTextOptions`, `AddImageOptions`, `BackgroundImageOptions`, and individual tool option interfaces are not
- `enableWheelZoom` defaults to off
- `CanvasManager` now initializes its zoom defaults from `FabricPaintOptions`
- `DragTool` is the default viewport-drag path
- `zoomAnimationDuration` defaults to `0` ms; positive values enable animation
- `zoomIn()`, `zoomOut()`, and `setZoom()` preserve the current content anchor after panning
- `resetZoom(zoomScale = 1)` resets to the display anchor and accepts a business zoom value
- resize-driven viewport resets and wheel zoom reuse the same `CanvasManager.setViewportTransform()` path
- helper API names are historical and affect more than `area`

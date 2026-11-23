# PaintBoard API

Source priority:

1. `src/index.ts`
2. `types/index.d.ts`
3. `src/core/PaintBoard.ts`
4. `src/core/CanvasManager.ts`

## Constructor

```ts
new PaintBoard(container: HTMLElement | string, options?: FabricPaintOptions)
```

## Core Options

`FabricPaintOptions`

- `width?: number`
- `height?: number`
- `backgroundColor?: string`
- `hoverCursor?: string`
- `moveCursor?: string`
- `zoomStep?: number`
- `minZoom?: number`
- `maxZoom?: number`
- `expandMargin?: number`
- `expandSize?: number`
- `zoomOrigin?: 'center' | 'topLeft'`
- `zoomAnimationDuration?: number`
- `enableWheelZoom?: boolean`
- `backgroundImage?: string | BackgroundImageOptions`
- `lineColor?: string`
- `fillColor?: string`
- `selection?: boolean`
- `preserveObjectStacking?: boolean`
- `perPixelTargetFind?: boolean`
- `targetFindTolerance?: number`
- `defaultShowHelpers?: boolean`
- `autoResize?: boolean`
- `autoResizeMode?: 'canvas' | 'viewport'`
- `autoResizeFit?: 'contain' | 'cover' | 'stretch'`
- `referenceSize?: { width: number; height: number }`
- `pixelRatio?: number | 'auto'`
- `lockObjectVisualSizeOnZoom?: boolean`
- `zoomInvariantExcludeTypes?: string[]`

Important current behavior:

- `backgroundImage` is typed but not auto-applied by `init()`
- `zoomStep` defaults to `0.2`, so current `zoomIn()` multiplies by `1.2` and `zoomOut()` divides by `1.2`
- `minZoom` defaults to `0.2`, `maxZoom` defaults to `3`
- `expandMargin` defaults to `50`, `expandSize` defaults to `200`
- `enableWheelZoom` defaults to `false`
- `zoomAnimationDuration` defaults to `0` ms; positive values enable zoom animation
- `pixelRatio` only affects clarity/backing store, not business zoom
- `autoResize` defaults to `true`, listens container changes, and preserves current zoom
- `autoResizeMode='canvas'` keeps the legacy behavior: display size becomes the new internal baseline
- `autoResizeMode='viewport'` keeps a fixed logical `referenceSize` and adapts it through viewport transform
- `autoResizeFit` defaults to `stretch` and only affects `autoResize=true` + `autoResizeMode='viewport'`; `contain` preserves full reference, `cover` fills and can crop, `stretch` scales X/Y independently
- missing `referenceSize` defaults to initial container size on first auto resize

## BackgroundImageOptions

- `source: string`
- `scaleMode?: 'fill' | 'fit' | 'stretch' | 'center' | 'repeat'`
- `opacity?: number`
- `backgroundVpt?: boolean`

Notes:

- `fill` can crop
- `fit` can letterbox
- `stretch` forces exact canvas coverage
- `repeat` currently does not tile; it keeps original image size at top-left

## Tool Lifecycle

- `init(): this`
- `registerTool(name: string, tool: BaseTool): this`
- `setTool(name: string): this`
- `destroy(): void`
- `currentToolName: string` (getter)

Common tool names include `select`, `drag`, `line`, `polyline`, `area`, `curve`, `pen`, `rect`, `text`, and `image`.

## Color and Zoom

- `setLineColor(color: string): this`
- `setFillColor(color: string): this`
- `zoomIn(origin?: ZoomOrigin): this`
- `zoomOut(origin?: ZoomOrigin): this`
- `setZoom(zoom: number | ZoomScale, origin?: ZoomOrigin): this`
- `resetZoom(zoomScale?: number | ZoomScale): this`
- `setZoomOrigin(origin: ZoomOrigin): this`
- `getZoom(): number`
- `getPixelRatio(): number`

Current zoom facts:

- `zoomIn()`, `zoomOut()`, and `setZoom()` with `zoomOrigin='center'` preserve the current canvas content center; panning with `DragTool` before zooming does not snap back to the container center
- `zoomOrigin='topLeft'` preserves the current canvas top-left anchor while zooming
- `zoomIn()`, `zoomOut()`, `setZoom()`, `resetZoom()`, wheel zoom, and viewport-fit resize/reset all land on the same viewport transform path
- animated zoom emits `canvas:zooming` during the transition and `canvas:zoomed` on completion
- `resetZoom()` resets the view to the display anchor and defaults to business zoom `1`
- `resetZoom(0.5)` resets the view to business zoom `0.5`
- wheel zoom follows the same origin rule when enabled

## Resize and Auto Resize

- `resize(width?, height?, reference?, origin?): this`
- `enableAutoResize(): this`
- `disableAutoResize(): this`

Current facts:

- resize changes display size and viewport, not object logical coordinates
- auto resize uses a 500ms debounce
- `reference` is used only when `autoResize=true` and `autoResizeMode='viewport'`
- default `autoResizeMode='canvas'` keeps compatibility with older resize behavior
- `autoResizeMode='viewport'` keeps object coordinates and stretch backgrounds aligned to the same logical reference size

## History

- `undo(): boolean`
- `redo(): boolean`
- `canUndo(): boolean`
- `canRedo(): boolean`
- `pauseHistory(): void`
- `resumeHistory(): void`
- `isHistoryPaused(): boolean`

History defaults exclude `line`, `polyline`, `area`, `curve`, `pen`, `text`, `image`, `rect`, and their drawing helpers. The board routes undo/redo through registered tools and canvas history.

## Canvas and Events

- `clear(): this` removes drawing objects and keeps the background image
- `originClear(): this` clears the canvas and restores its background image
- `on(event: string, callback: EventCallback): this`
- `off(event: string, callback?: EventCallback): this`

The public `EventBus` also supports `on`, `off`, `once`, `emit`, and `clear`. Use the exact import boundary in `src/index.ts`; names present in `types/index.d.ts` are not necessarily package-root exports.

## Background

- `setBackgroundImage(source: string | BackgroundImageOptions): Promise<this>`
- `clearBackgroundImage(): this`
- `getBackgroundImage(): FabricImage | null`

The background image is force-locked and sent to the bottom layer.

With `autoResizeMode='viewport'` and `backgroundVpt=true`, background scaling uses `referenceSize` instead of the changing display size. This keeps object points and background pixels aligned across page resizes.

## Export and Import

- `exportToJSON(options?: ExportJSONOptions | string[]): string`
- `importFromJSON(json: string | object): Promise<void>`
- `exportToImage(options?: ExportImageOptions | string): string`
- `exportToSVG(): string`

Current facts:

- `exportToJSON()` defaults to `excludeTypes: ['text', 'image']`
- zoom-invariant exports write logical values from `zoomInvariantBase`
- `importFromJSON()` re-links helpers, re-binds events, restores helper visibility, reapplies zoom compensation, and reflows object layers

## Data Queries and Helpers

- `getCanvas(): Canvas | null`
- `getObjects(): FabricObject[] | undefined`
- `getObjectById(id: string): FabricObject | null`
- `getCustomObjects(): Array<{ id: string; type: string; object: FabricObject }>`
- `getAreasData(): AreaCustomData[]`
- `getTextsData(): TextData[]`
- `showAllAreaHelpers(): this`
- `hideAllAreaHelpers(): this`
- `toggleAreaHelpers(): this`
- `isHelpersVisible(): boolean`

Helper API name is historical; it currently affects `area`, `curve`, `line`, `polyline`, and `rect`.

## Programmatic Text and Image APIs

Text:

- `addText(options: AddTextOptions)`
- `insertText(options: AddTextOptions)`
- `updateTextById(id, options): boolean`
- `batchInsertTexts(optionsList)`

Image:

- `addImage(options: AddImageOptions)`
- `insertImage(options: AddImageOptions)`
- `updateImageById(id, options): boolean`
- `batchInsertImages(optionsList)`

Delete:

- `removeById(id): boolean`
- `batchRemoveByIds(ids): Promise<{ removed; notFound } | boolean>`

`batchInsertTexts()` and `batchInsertImages()` resolve to `{ success, failed }` or `false` when the canvas or corresponding tool is unavailable. Entries in `success` are `{ id, object }`; entries in `failed` carry an optional `id` and an error string. `insertText()` / `insertImage()` update an existing matching object when its `id` is supplied, otherwise they insert it. `addText()` / `addImage()` always create through their registered tools.

These require `TextTool` or `ImageTool` to be registered first.

Current note:

- `AddTextOptions.layer` and `AddImageOptions.layer` are supported on insert APIs
- `updateTextById()` and `updateImageById()` also accept `layer` and reorder the canvas immediately

## Person Tracking

- `createPersonTracker(options?: TraceOptions): PersonTracker`
- `getPersonTracker(): PersonTracker | null`

The current board keeps a single active `PersonTracker` instance.

Current note:

- `TraceOptions.layer` defaults to `0`
- tracker families are reflowed together so trace path, ripple, and marker group keep stable stacking within the chosen layer

# Troubleshooting

Use this when behavior and expectation diverge.

## 1. Constructor `backgroundImage` is ignored

Current source explains it:

- `FabricPaintOptions.backgroundImage` exists in types
- auto-apply call inside `init()` is commented out

Answer:

- this is current behavior
- call `await board.setBackgroundImage(...)` manually

## 2. Background image does not exactly match canvas size

Check `scaleMode` first:

- `fill` can crop
- `fit` can leave blank space
- `stretch` forces exact canvas size
- `center` keeps original size
- `repeat` does not actually tile today

## 3. Wheel zoom does nothing

Check:

- `enableWheelZoom: true` was passed
- board was initialized after the option was set

Current source only binds `mouse:wheel` when `enableWheelZoom` is true.

## 4. Panning expectation does not match runtime

Check:

- whether `DragTool` is registered and active
- whether the user is holding `Ctrl` or `Meta`

Current `CanvasManager` middle-button/Alt panning code exists but is not wired by default.

## 5. Auto resize changes display but the user expects logical data rewrite

Current source behavior:

- resize / autoResize rebuild display size and viewport
- current business zoom is preserved
- object logical coordinates are not individually rewritten during resize
- default `autoResizeMode='canvas'` updates the internal baseline to the new display size
- `autoResizeMode='viewport'` keeps a fixed `referenceSize` and adapts via viewport transform

So if the user expects destructive geometry scaling, that is not current behavior.

If a stretched background must stay aligned with object points after page resize:

- set `autoResize: true`
- set `autoResizeMode: 'viewport'`
- set `autoResizeFit` to `contain`, `cover`, or `stretch`
- pass `referenceSize` when the background has a known logical size; otherwise the initial container size becomes the reference
- use `backgroundVpt: true` for the background image

## 6. Zoom jumps back after panning

Current source behavior:

- `zoomIn()`, `zoomOut()`, and `setZoom()` preserve the current canvas content center in `center` mode
- `topLeft` mode preserves the current canvas top-left anchor
- `resetZoom(zoomScale = 1)` intentionally resets to the display anchor

If zoom still jumps back, check whether the caller is using `resetZoom()` or changing `zoomOrigin`.

## 7. Exported JSON misses text or images

Check:

- whether `exportToJSON()` was called without options

Current default is:

```ts
excludeTypes: ["text", "image"];
```

Use:

```ts
board.exportToJSON({ excludeTypes: [] });
```

## 8. Zoom-invariant mode seems inconsistent

Check:

- `lockObjectVisualSizeOnZoom`
- `zoomInvariantExcludeTypes`
- whether the object is a background image or a `PersonTracker` object

Current source treats background and tracker visuals via separate paths.

## 9. Source and prose docs disagree

Use this order:

1. `src/index.ts`
2. `types/index.d.ts`
3. `src/core/PaintBoard.ts`
4. `src/core/CanvasManager.ts`
5. matching tool or utility source
6. tests

State clearly when the answer is source-derived.

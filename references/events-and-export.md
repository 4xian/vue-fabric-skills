# Events, Export, Helpers, and Person Tracking

## Board Events

Common board events confirmed by current source:

- `tool:changed`
- `history:changed`
- `object:selected`
- `selection:cleared`
- `object:created`
- `object:modified`
- `object:removed`
- `objects:deleted`
- `canvas:zooming`
- `canvas:zoomed`
- `canvas:panned`
- `canvas:cleared`
- `canvas:loaded`
- `canvas:expanded`
- `canvas:resized`
- `canvas:zooming` and `canvas:zoomed` are emitted by `CanvasManager`; `PaintBoard` also emits `canvas:zoomed` during viewport resize/reset
- `backgroundImage:set`
- `backgroundImage:cleared`
- `backgroundImage:error`
- `areaHelpers:shown`
- `areaHelpers:hidden`
- `mouse:move`
- `batch:textsInserted`
- `batch:imagesInserted`
- `batch:removed`

## Object Events

- `line:created`
- `line:selected`
- `line:clicked`
- `polyline:created`
- `polyline:selected`
- `polyline:clicked`
- `area:created`
- `area:selected`
- `area:clicked`
- `curve:created`
- `curve:selected`
- `curve:clicked`
- `pen:created`
- `pen:selected`
- `pen:clicked`
- `pen:modified`
- `rect:created`
- `rect:selected`
- `rect:clicked`
- `text:created`
- `text:changed`
- `text:selected`
- `text:clicked`
- `text:updated`
- `image:created`
- `image:selected`
- `image:clicked`
- `image:modified`
- `image:updated`

When asked for payload shape, prefer exported custom data types over prose guesses.

Current rectangle payload note:

- `rect:created`, `rect:selected`, and `rect:clicked` expose `RectCustomData.points`
- the four points are ordered clockwise from top-left without repeating the first point

## History

- history snapshots exclude the current background image
- undo and redo restore drawing objects while preserving that background image
- history restore keeps exactly one background image, including zoom-invariant viewport mode
- history is capped at 50 snapshots

## Export and Import

JSON:

```ts
const json = board.exportToJSON({
  excludeTypes: []
})
await board.importFromJSON(json)
```

Facts:

- default JSON export excludes `text` and `image`
- zoom-invariant exports use logical values from `zoomInvariantBase`
- import re-links helpers, restores runtime behavior, and reflows layer order
- rectangle JSON includes `points`; older rectangle JSON without it is hydrated from `startPoint`, `width`, and `height`
- `pen` objects export `drawId`, `layer`, `lineColor`, `strokeWidth`, and `createdAt`; import rebinds pen click/select/modify events with `perPixelTargetFind: true`

Image:

```ts
const dataUrl = board.exportToImage({
  format: 'png',
  quality: 1,
  multiplier: 2,
  download: false,
  filename: 'canvas'
})
```

Facts:

- `quality` follows Fabric's `0~1` semantics
- image export is a visual snapshot, not logical normalization

SVG:

```ts
const svg = board.exportToSVG()
```

## Helpers

Helper visibility methods:

- `showAllAreaHelpers()`
- `hideAllAreaHelpers()`
- `toggleAreaHelpers()`
- `isHelpersVisible()`

Despite the name, current implementation also affects `curve`, `line`, `polyline`, and `rect` helpers.

Current note:

- helper visibility changes preserve family layer ordering instead of pushing helpers to the absolute front

## PersonTracker

Board entry:

- `board.createPersonTracker(options?)`
- `board.getPersonTracker()`

Current option note:

- `TraceOptions.layer` is supported and defaults to `0`

Key public methods:

- `createMultiplePersons(persons)`
- `createSinglePerson(person)`
- `removePerson(id)`
- `createPersonTraces(id, person, traces)`
- `removePersonTraces(id)`
- `clearAll()`
- `clearAllPersons()`
- `clearAllTraces()`
- `getPersonById(id)`
- `getAllPersonIds()`
- `abortRendering()`
- `destroy()`

`removePerson(id)` returns `boolean`; `removePersonTraces(id)` returns `void`. Tracker creation methods are asynchronous where they load marker images or render batches.

Current zoom behavior:

- `canvas:zooming` fires on each animated zoom frame; `canvas:zoomed` fires when the final transform lands
- on `canvas:zooming`, `canvas:zoomed`, `canvas:panned`, and `canvas:resized`, tracker visuals are recalculated
- when zoom-invariant mode is enabled, trace stroke width, markers, labels, images, and ripple visuals are compensated too
- tracker families keep stable internal order: trace path below ripple below marker group

Common events:

- `person:created`
- `person:updated`
- `person:removed`
- `person:clicked`
- `person:statusChange`
- `persons:cleared`
- `persons:allCleared`
- `trace:shown`
- `trace:hidden`
- `traces:cleared`

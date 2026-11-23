# Tools API

All drawing tools inherit from `BaseTool` and must be registered before activation.

## BaseTool

Shared lifecycle:

- `bindCanvas(canvas, eventBus, paintBoard): void`
- `activate(): void`
- `deactivate(): void`
- `isDrawing(): boolean`
- `canUndoTool(): boolean`
- `canRedoTool(): boolean`
- `undo(): boolean`
- `redo(): boolean`
- `getPointer(opt): Point`
- `destroy(): void`

## Common Base Options

`BaseToolOptions`

- `defaultCursor?: string`
- `activeCursor?: string`
- `deactiveCursor?: string`
- `defaultLayer?: number`
- `hasBorders?: boolean`
- `hasControls?: boolean`
- `lockMovementX?: boolean`
- `lockMovementY?: boolean`
- `cornerStyle?: 'rect' | 'circle'`
- `cornerSize?: number`
- `cornerColor?: string`
- `cornerStrokeColor?: string`
- `padding?: number`
- `borderScaleFactor?: number`
- `continueDraw?: boolean`
- `disabeldClick?: boolean`

Current note:

- `defaultLayer` defaults to `0`
- for helper-based tools, the main object and its helper family are created on the same layer and reflowed together

## Tool-Specific Options

The following are constructor options beyond `BaseToolOptions`; use the exact `*ToolOptions` type from `types/index.d.ts` when reading source. Those option types are not re-exported from the package root.

- `AreaToolOptions`: `closeThreshold`, `pointRadius`, `labelFontSize`, `labelFillColor`, `pointFillColor`, `pointHoverColor`, `defaultShowHelpers`, `allowOverlap`, `enableFill`, `perPixelTargetFind`, `strokeWidth`, `helperStrokeWidth`
- `SelectToolOptions`: `allowSelection`
- `TextToolOptions`: `fontSize`, `fontFamily`, `fill`, `perPixelTargetFind`
- `CurveToolOptions`: `tension`, `pointRadius`, `closeThreshold`, `labelFontSize`, `labelFillColor`, `pointFillColor`, `pointHoverColor`, `defaultShowHelpers`, `enableFill`, `perPixelTargetFind`, `strokeWidth`, `helperStrokeWidth`
- `PenToolOptions`: `strokeWidth`, `decimate`, `perPixelTargetFind`
- `LineToolOptions` / `PolylineToolOptions`: `pointRadius`, `labelFontSize`, `labelFillColor`, `pointFillColor`, `pointHoverColor`, `defaultShowHelpers`, `perPixelTargetFind`, `strokeWidth`, `helperStrokeWidth`
- `RectToolOptions`: `enableFill`, `strokeWidth`, `perPixelTargetFind`, `labelFontSize`, `labelFillColor`, `defaultShowHelpers`
- `ImageToolOptions`: `defaultSelectable`, `defaultHasControls`, `defaultHasBorders`, `defaultLockMovement`, `defaultLockScaling`

Defaults such as colors, fill, helper visibility and selection behavior live in `src/utils/settings.ts`. Do not infer them from optional type properties.

## SelectTool

- register name: `select`
- extra option: `allowSelection?: boolean`

## DragTool

- register name: `drag`
- current viewport drag requires `Ctrl` or `Meta`
- use this, not `CanvasManager`, when the user asks about default panning

## LineTool

- register name: `line`
- two-click line creation
- `Escape` cancels current drawing

## PolylineTool

- register name: `polyline`
- left-click adds points
- `Enter` finishes drawing
- right-click also finishes drawing
- `Escape` cancels

## AreaTool

- register name: `area`
- left-click adds points
- closes when pointer returns near the first point
- `Escape` cancels

Important current note:

- historical docs that describe area as a line variant are stale

## CurveTool

- register name: `curve`
- public extra method: `setTension(value: number): void`
- left-click adds points
- `Enter` finishes an open curve
- returning near the first point closes the curve
- `Escape` cancels

## PenTool

- register name: `pen`
- draws freehand paths with Fabric `PencilBrush`
- default behavior is continuous drawing until another tool is selected
- extra options:
  - `strokeWidth?: number`
  - `decimate?: number`
  - `perPixelTargetFind?: boolean`
- public extra method:
  - `setStrokeWidth(width: number): void`
- default hit testing uses `perPixelTargetFind: true`, so clicking empty space inside a path bounding box should not trigger pen object events
- current source emits:
  - `pen:created`
  - `pen:selected`
  - `pen:clicked`
  - `pen:modified`

## RectTool

- register name: `rect`
- first click sets start point
- second click finishes rectangle
- `Escape` cancels
- `RectCustomData.points` contains four corners ordered clockwise from top-left: top-left, top-right, bottom-right, bottom-left
- moving or scaling the rectangle keeps `points` synchronized with its current bounds

## TextTool

- register name: `text`
- honors `defaultLayer` for click-created text
- public extras:
  - `setFontSize(size: number): void`
  - `setFontFamily(family: string): void`
  - `setTextColor(color: string): void`
  - `createTextAt(options: AddTextOptions)`
  - `createTextWithoutRender(options: AddTextOptions)`

Current note:

- `AddTextOptions.layer` is supported for programmatic creation

## ImageTool

- register name: `image`
- honors `defaultLayer` for inserted images
- public extras:
  - `openFileDialog(): void`
  - `addImageAt(options: AddImageOptions)`
  - `addImageWithoutRender(options: AddImageOptions)`
  - `setAngle(...)`
  - `setOpacity(...)`
  - `setPosition(...)`
  - `setScale(...)`
  - `setSize(...)`
  - `setSelectable(...)`
  - `setLockMovement(...)`
  - `setLockScaling(...)`
  - `setControls(...)`
  - `getImageById(...)`

Current note:

- image scale/size changes now interact with zoom-invariant compensation when enabled
- `AddImageOptions.layer` is supported for programmatic creation

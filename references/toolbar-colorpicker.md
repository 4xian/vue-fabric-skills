# Toolbar and ColorPicker

## Toolbar

Source: `src/ui/Toolbar.ts`

Constructor:

```ts
new Toolbar(board, options?: ToolbarOptions)
```

`ToolbarOptions`

- `tools?: string[]`
- `visible?: boolean`
- `draggable?: boolean`

Public methods:

- `init(): this`
- `setActiveTool(name: string): void`
- `setHelpersVisible(visible: boolean): void`
- `getHelpersVisible(): boolean`
- `show(): void`
- `hide(): void`
- `isVisible(): boolean`
- `setDraggable(draggable: boolean): void`
- `isDraggable(): boolean`
- `destroy(): void`

Supported toolbar items:

- `lineColor`
- `fillColor`
- `select`
- `drag`
- `line`
- `polyline`
- `area`
- `curve`
- `pen`
- `rect`
- `text`
- `image`
- `undo`
- `redo`
- `zoomIn`
- `zoomOut`
- `fitZoom`
- `download`
- `helpers`

The `ToolName` type also contains `uploadImage`, but the current `Toolbar` implementation has no case that renders that item. `image` is the implemented upload button and opens the registered `ImageTool` file dialog.

Current facts:

- `fitZoom` calls `resetZoom()`
- `image` button only triggers `ImageTool.openFileDialog()`
- `pen` is a normal tool button and calls `setTool('pen')`
- `helpers` toggles helper visibility for all current helper-bearing drawing types, not only area

## ColorPicker

Source: `src/ui/ColorPicker.ts`

Constructor:

```ts
new ColorPicker(options?: ColorPickerOptions)
```

`ColorPickerOptions`

- `defaultColor?: string`
- `onChange?: (color: string) => void`

Public methods:

- `getElement(): HTMLDivElement | null`
- `show(): void`
- `hide(): void`
- `toggle(): void`
- `setColor(color: string): void`
- `getColor(): string`
- `destroy(): void`

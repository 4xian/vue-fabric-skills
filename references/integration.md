# Integration Patterns

## Vue 3

```vue
<template>
  <div ref="container" class="canvas-container"></div>
</template>

<script setup lang="ts">
import { ref, onMounted, onUnmounted } from "vue";
import PaintBoard, {
  SelectTool,
  DragTool,
  AreaTool,
  PenTool,
  TextTool,
  ImageTool,
  Toolbar,
} from "@4xian/vue-fabric";
import "@4xian/vue-fabric/style.css";

const container = ref<HTMLElement | null>(null);
let board: InstanceType<typeof PaintBoard> | null = null;
let toolbar: Toolbar | null = null;

onMounted(async () => {
  board = new PaintBoard(container.value!, {
    width: 1000,
    height: 600,
    zoomOrigin: "center",
    autoResize: true,
    lockObjectVisualSizeOnZoom: false,
  }).init();

  board
    .registerTool("select", new SelectTool())
    .registerTool("drag", new DragTool())
    .registerTool("area", new AreaTool({ enableFill: true }))
    .registerTool("pen", new PenTool())
    .registerTool("text", new TextTool())
    .registerTool("image", new ImageTool())
    .setTool("select");

  await board.setBackgroundImage({
    source: "/demo/static/draw-bg.png",
    scaleMode: "stretch",
    backgroundVpt: true,
  });

  toolbar = new Toolbar(board).init();
});

onUnmounted(() => {
  toolbar?.destroy();
  board?.destroy();
});
</script>
```

## Plain ESM

```ts
import PaintBoard, {
  SelectTool,
  DragTool,
  PolylineTool,
  CurveTool,
  PenTool,
  Toolbar,
} from "@4xian/vue-fabric";
import "@4xian/vue-fabric/style.css";

const board = new PaintBoard("#canvas-container", {
  width: 1000,
  height: 600,
  zoomOrigin: "topLeft",
  enableWheelZoom: true,
}).init();

board
  .registerTool("select", new SelectTool())
  .registerTool("drag", new DragTool())
  .registerTool("polyline", new PolylineTool())
  .registerTool("curve", new CurveTool())
  .registerTool("pen", new PenTool())
  .setTool("select");

new Toolbar(board).init();
```

## UMD

Use `FabricPaint`, not `PaintBoard`.

```html
<link rel="stylesheet" href="node_modules/@4xian/vue-fabric/dist/style.css" />
<div id="canvas-container"></div>

<script src="node_modules/fabric/dist/fabric.js"></script>
<script src="node_modules/@4xian/vue-fabric/dist/vue-fabric.umd.js"></script>
<script>
  const { FabricPaint, SelectTool, AreaTool, PenTool, Toolbar } = VueFabric;

  const board = new FabricPaint("#canvas-container", {
    width: 1000,
    height: 600,
  }).init();

  board
    .registerTool("select", new SelectTool())
    .registerTool("area", new AreaTool({ enableFill: true }))
    .registerTool("pen", new PenTool())
    .setTool("select");

  new Toolbar(board).init();
</script>
```

## Integration Advice

- Use `DragTool` for viewport dragging; current `CanvasManager` middle-button panning is not enabled by default.
- Use `stretch` if the user expects the background image to match canvas size exactly.
- `fill` can crop and `fit` can leave blank space.
- `autoResize` preserves current business zoom and re-fits the current display size after a 500ms debounce.
- For responsive pages where object points must stay aligned with a stretched background, use `autoResize: true`, `autoResizeMode: 'viewport'`, `autoResizeFit: 'contain' | 'cover' | 'stretch'`, and usually `backgroundVpt: true`.
- In viewport mode, omit `referenceSize` to use the initial container size, or pass `{ width, height }` when the background has a known logical coordinate space.

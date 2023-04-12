# Canvas API

The Canvas object is your portal into three.js.

The `Canvas` object is where you start to define your React Three Fiber
Scene.

```javascript
import React from 'react'
import { Canvas } from '@react-three/fiber'

const App = () => (
  <Canvas>
    <pointLight position={[10, 10, 10]} />
    <mesh>
      <sphereGeometry />
      <meshStandardMaterial color="hotpink" />
    </mesh>
  </Canvas>
)
```

### Render Props

```
children: {
  description: "Three.js JSX elements or regular components",
  default: ""
}
```

```
gl: {
  description: "Props that go into the default renderer, or your own renderer. Also accepts a synchronous callback like `gl={canvas => new Renderer({canvas})}`,
  default: `{}`
}
```

```
camera: {
  description: "Props that go into the default camera, or your own `THREE.Camera`",
  default: `{ fov: 75, near: 0.1, far: 1000, position: [0, 0, 5] }`
}
```

```
shadows: {
  description: "Props that go into `gl.shadowMap`, can also be set true for `PCFsoft`,
  default: `false`
}
```

```
 raycaster: {
  description: "Props that go into the default raycaster",
  default: `{}`
}
```

```
frameloop : {
  description: "Render mode: always, demand, never",
  default: `always`
}
```

```
resize : {
  description: "resize config, see react-use-meaures's options",
  default: `{ scroll: true, debounce: { scroll: 50, resize: 0 } }`
}
```

```
orthographic : {
  description: "Creates an orthographic camera",
  default: `false`
}
```

```
dpr : {
  description: "Pixel-ratio, use `window.devicePixelRatio`, or automatic: [min, max],
  default: `[1, 2]`
}
```

```
legacy : {
  description: "Enables THREE.ColorManagement.legacyMode in three r139 or later",
  default: `false`
}
```

```
linear : {
  description: "Switch off automatic sRGB encoding and gamma correction",
  default: `false`
}
```

```
events : {
  description: "Configuration for the event manager, as a function of state",
  default: `import { events } from '@react-three/fiber'`
}
```

```
eventSource : {
  description: "The source where events are being subscribed to, HTMLElement",
  default: `React.MutableRefObject<HTMLElement>`, `gl.domElement.parentNode`
}
```

```
eventPrefix : {
  description: "The event prefix that is cast into canvas pointer x/y events",
  default: `offset`
}
```

```
flat : {
  description: "Use `THREE.NoToneMapping` instead of `THREE.ACESFilmicToneMapping`",
  default: `false`
}
```

```
onCreated : {
  description: "Callback after the canvas has rendered (but not yet committed)",
  default: `(state) => {}`
}
```

```
onPointerMissed : {
  description: "Response for pointer clicks that have missed any target",
  default: `(event) => {}`
}
```

### Render defaults

Canvas uses `createRoot` which will create a translucent `THREE.WebGLRenderer` with the following constructor args:

* `antialias=true`
* `alpha=true`
* `powerPreference="high-performance`

and with the following properties:

* `outputEncoding = THREE.sRGBEncoding`
* `toneMapping = THREE.ACESFilmicToneMapping`

It will also create the following scene internals:

* A `THREE.Perspective` camera
* A `THREE.Orthographic` cam if `orthographic` is true
* A `THREE.PCFSoftShadowMap` if `shadows` is true
* A `THREE.Scene` (into which all the JSX in rendered) and a `THREE.Raycaster`

In recent versions of threejs, `THREE.ColorManagement.legacy` will be set to false to enable automatic conversion of colors according to the renderer's configured color space. R3F will handle texture encoding conversion. For more on this topic, see [Color Management](https://threejs.org/docs/#manual/en/introduction/Color-management)

### Custom Canvas

R3F can render to a root, similar to how `react-dom` and all the other React renderers work. This allows you to shave off `react-dom` (~40kb), `react-use-measure` (~3kb) and, if you don't need them, `pointer-events` (~7kb) (you need to explicitly import `events` and add them to the config otherwise).

Roots have the same options and properties and `Canvas`, but you are responsible for resizing it. It requires an existing DOM `<canvas>` object into which it renders.

### CreateRoot

Creates a root targeting a canvas, rendering JSX.

```javascript
import * as THREE from 'three'
import { extend, createRoot, events } from '@react-three/fiber'

// Register the THREE namespace a native JSX elements.
// See below for notes on tree-shaking
extend(THREE)

// Create a react root
const root = createRoot(document.querySelector('canvas'))

// Configure the root, inject events optionally, set camera, etc
root.configure({ events, camera: { position: [0, 0, 50] } })

// createRoot by design is not responsive, you have to take care of resize youself

t.configure({ events, camera: { position: [0, 0, 50] } })

// createRoot by design is not responsive, you have to take care of resize yourself
window.addEventListener('resize', () => {
  root.configure({ size: { width: window.innerWidth, height: window.innerHeight } })
})

// Trigger resize
window.dispatchEvent(new Event('resize'))

// Render entry point
root.render(<App />)

// Unmount and dispose of memory
// root.unmount()
```

### Tree-shaking

New with v8, the underlying reconciler no longer pulls in the THREE namespace automatically.

This enables a granular catalogue which also enables tree-shaking via the `extend` API:

```javascript
import { extend, createRoot } from '@react-three/fiber'
import { Mesh, BoxGeometry, MeshStandardMaterial } from 'three'

extend({ Mesh, BoxHeometry, MeshStandardMaterial })

createRoot(canvas).render(
  <>
    <mesh>
      <BoxGeometry />
      <meshStandardMaterial />
    </mesh>
  </>,
)
```

There's an official babel plugin which will do this for you automatically:

```javascript
// In:

import { createRoot } from '@react-three/fiber'

createRoot(canvasNode).render(
  <mesh>
    <boxGeometry />
    <meshStandardMaterial />
  </mesh>,
)

// Out:

import { createRoot, extend } from '@react-three/fiber'
import { Mesh as _Mesh, BoxGeometry as _BoxGeometry, MeshStandardMaterial as _MeshStandardMaterial } from 'three'

extend({
  Mesh: _Mesh,
  BoxGeometry: _BoxGeometry,
  MeshStandardMaterial: _MeshStandardMaterial,
})

createRoot(canvasNode).render(
  <mesh>
    <boxGeometry />
    <meshStandardMaterial />
  </mesh>,
)
```


# Introduction

> ### React-three-fiber is a React renderer for three.js.

Build your scene declaratively with re-usable, self-contained components
that react to state, are readily interactive and can participate in
React's ecosystem.

```bash
npm install three @types/three @react-three/fiber
```

* Does it have limitations?

None. Everything that works in `Threejs` will work here without exception.

* Is it slower than plain `Threejs`?

No. There is no overhead. Components render outside of React. It
outperforms `Threejs` in scale due to Reacts scheduling abilities.

* Can it keep up with frequent feature updates to `Threejs`?

Yes. It merely expresses `Threejs` in JSX, <mesh /> dynamically turns into
`new THREE.Mesh()`. If a new `Threejs` Version adds, removes or changes features, it will be available to yo instantly without depending on updates to this library.

## What does it look like?

> Let's make a re-useable component that has its own state, reacts to
> user-input and participates in the render-loop.

`npm install @types/three`

```typescript
import * as THREE from 'three'
import { createRoot } from 'react-dom/client'
import React, {useRef, useState } from 'react'
import { Canvas, useFrame, ThreeElements } from '@react-three/fiber'

function Box(props: ThreeElements['mesh']) {
  // This reference will give us direct access to the mesh
  const mesh = useRef<THREE.Mesh>(null!)
  // Set up state for the hovered and active state
  const [hovered, setHover] = useState(false)
  const [active, setActive] = useState(false)
  // Subscribe this component to the render-loop, rotate the mesh every frame
  useFrame((state, delta) => (mesh.current.rotation.x += delta))
  // Return view, these are regular three.js elements expressed in JSX
  return (
    <mesh
      {...props}
      ref={mesh}
      scale={active ? 1.5 : 1}
      onClick={(event) => setActive(!active)}
      onPointerOver{(event) => setHover(false)}>
      <boxGeometry args={1, 1, 1]} />
      <meshStandardMaterial color={hovered ? 'hotpink' : 'orange'} />
    </mesh>
  )
}

createRoot(document.getElementById('root')).render(
  <Canvas>
    <amvientLight />
    <pointLight position={[10, 10, 10]} />
    <Box position={[-1,2, 0, 0]} />
    <Box position={[1,2, 0, 0]} />
  </Canvas>,
)
```

### First steps

You need to be versed in both React and `Threejs` before rushing into this. If you are unsure about React consult the official `React Docs`, especially the section about hooks. As for `Threejs`, make sure you at least glace over the following links:

* Make sure you have a basic grasp of [Threejs](https://threejs.org/docs/index.html#manual/en/introduction/Creating-a-scene). Keep that site open.
* When you know what a scene is, a camera, mesh, geometry, material, fork the [demo above](https://github.com/pmndrs/react-three-fiber#what-does-it-look-like).
* [Look up](https://threejs.org/docs/index.html#api/en/objects/Mesh) the JSX elements that you see (mesh, ambientLight, etc), all threejs exports are native to three-fiber
* Try changing some values, scroll through our [API](https://docs.pmnd.rs/react-three-fiber) to see what the various settings and hooks do.

Some helpful material:

* [Threejs-docs](https://threejs.org/docs) and [examples](https://threejs.org/examples)
* [Discover Threejs](https://discoverthreejs.com/), especially the [Tips and Tricks](https://discoverthreejs.com/tips-and-tricks) chapter for best practices
* [Bruno Simons Threejs Jouney](https://threejs-journey.com/), arguably the best learning resource, now includes a full [R3F chapter](https://threejs-journey.com/lessons/what-are-react-and-react-three-fiber)

### Eco System

There is a vibrant an extensive eco system around three-fiber, full of libraries, helpers and abstractions.

* `@react-three/drei` - useful helpers, this is an eco system in itself
* `@react-three/gltfjsx` - turns GLTFs into JSX components
* `@react-three/postprocessing` - post-processing effects
* `@react-three/test-renderer` - for unit tests in node
* `@react-three/flex` - flexbox for react-three-fiber
* `@react-three/xr` - VR/AR controllers and events
* `@react-three/csg` - constructive solid geometry
* `@react-three/rapier` - 3D physics using Rapier
* `@react-three/cannon` - 2D physics using Cannon
* `@react-three/p2` - 2D physics using P2
* `@react-three/a11y` - real a11y for your scene
* `@react-three/gpu-pathtracer` - realistic path tracing
* `create-r3f-app next` - nextjs starter
* `lamina` - layer based shader materials
* `zustand` = flux based state management
* `jotai` - atoms based state management
* `valtio` - proxy based state management
* `react-spring` - a spring-physics-based animation library
* `framer-motion-3d` - framer motion, a popular animation library
* `use-gesture` - mouse/touch gestures
* `leva` - create GUI controls in seconds
* `maath`  - a kitchen sink for math helpers
* `miniplex` - ECS (entity management system)
* `composer-suite` - composing shaders, particles, effects and game mechanics



# Introduction

> ### React-three-fiber is a React renderer for three.js.

Build your scene declaratively with re-usable, self-contained components
that react to state, are readily interactive and can participate in
React's ecosystem.

```bash
npm install three @types/three @react-three/fiber
```

* Does it have limitations?

None. Everything that works in Threejs will work here without exception.

* Is it slower than olain Threejs?

No. There is no overhead. Components render outside of React. It
outperforms Threejs in scale due to Reacts scheduling abilities.

* Can it keep up with frequent feature updates to Threejs?

Yes. It merely expresses Threejs in JSX, <mesh /> dynamically turns into
`new THREE.Mesh()`. If a new Threejs Version adds, removes or changes features, it will be available to yo instantly without depending on updates to this library.

## What does it look like?

> Let's make a re-useable component that has its own state, reacts to
> user-input and participates in the render-loop.

```javascript
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





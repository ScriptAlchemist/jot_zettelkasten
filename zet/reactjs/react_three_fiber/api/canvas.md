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

```json
children: {
  description: "Three.js JSX elements or regular components",
  default: ""
}
```

```json
gl: {
  description: "Props that go into the default renderer, or your own renderer. Also accepts a synchronous callback like `gl={canvas => new Renderer({canvas})}`,
  default: `{}`
}
```

```json
camera: {
  description: "Props that go into the default camera, or your own `THREE.Camera`",
  default: `{ fov: 75, near: 0.1, far: 1000, position: [0, 0, 5] }`
}
```

```json
shadows: {
  description: "Props that go into `gl.shadowMap`, can also be set true for `PCFsoft`,
  default: `false`
}
```

```json
 raycaster: {
  description: ,
  default:
}
```

```json
 : {
  description: ,
  default:
}
```

```json
 : {
  description: ,
  default:
}
```

```json
 : {
  description: ,
  default:
}
```

```json
 : {
  description: ,
  default:
}
```

```json
 : {
  description: ,
  default:
}
```

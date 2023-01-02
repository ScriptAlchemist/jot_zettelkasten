# Your First Scene

This guide will help you setup your first React Three Fiber scene and
introduce you to its core concepts.

This tutorial will assume some React knowledge.

## Setting up the Canvas

We'll start by importing the `<Canvas />` component from `@react-three/fiber` and putting it in our React tree. 

```javascript
import ReactDOM from 'react-dom'
import { Canvas } from '@react-three/fiber'

function App() {
  return (
    <div id="canvas-container">
      <Canvas />
    </div>
  )
}

ReactDOM.render(<App />, document.getElementById('root'))
```

The Canvas component does some important setup work behind the scenes:

* It sets up a `Scene` and a `Camera`, the basic building blocks necessary for rendering
* It renders our scene every frame, you do not need a traditional render-loop

Canvas is responsive to fit the parent node, so you can control how big it is by changing the parents width and height, in this case `#canvas-container`.

## Adding a Mesh Component

To actually see something in our scene, we'll add a lowercase `<mesh />` native element, which is the direct equivalent to `new THREE.Mesh()`.

```typescript
<Canvas>
  <mesh />
```

Note that we don't need to import anything, All three.js objects will be treated as native JSX elements, just like you can just write `<div />` or `<span />` in regular ReactDOM. The general rule is that Fiber components are available under the camel case version of their name in three.js.

A `Mesh` is a basic scene object in three.js, and it's used to hold the geometry and the material needed to represent a shape in 3D space. We'll create a new mesh using a `BoxGeometry` and a `MeshStandardMaterial` which `automatically attach` to their parent.

```typescript
<Canvas>
  <mesh>
    <boxGeometry />
    <meshStandardMaterial />
  </mesh>
```

Let's pause for a moment to understand exactly what is happening here. The code we just wrote is the equivalent to this three.js code:

```typescript
const scene = new THREE.Scene()
const camera = new THREE.PersepectiveCamera(75, width / height, 0.1, 1000)

const renderer = new THREE.WebGLRenderer()
renderer.setSize(width, height)
document.querySelector('#canvas-container').appendchild(renderer.domElement)

const mesh = new THREE.Mesh()
mesh.geometry = new THREE.BoxGeometry()
mesh.material = new THREE.MeshStandardMaterial()

scene.add(mesh)

function animate() {
  requestAnimationFrame(animate)
  renderer.render(scene, camera)
}

animate()
```

### Constructor arguments

According to the `docs for BoxGeometry` we can optionally pass three arguments for: width, length and depth:

* `new THREE.BoxGeometry(2, 2, 2)`

In order to do this in Fiber we use the `args` prop, which always takes an array whose items represent the constructor arguments.

* `<boxGeometry args={[2, 2, 2]} />`

Note that every time you change `args`, the object must be re-constructed!

#### Adding Lights

Next, we will add some lights to our scene, by putting these components into our canvas.

```typescript
<Canvas>
  <ambientLight intensity={0.1} />
  <directionalLight color="red" position={[0,0,5]} />
```

#### Props

This introduces us to the last fundamental concept of Fiber, how React `props` work on three.js objects. When you set any prop on a Fiber component, it will set the property of the same name on the three.js instance.

Let's focus on our `ambientLight`, whose documentation tells us that we can optionally construct it with a color, but it can also receive props.

* `<ambientLight intensity={0.1} />`

Which is the equivalent to:

```javascript
const light = new THREE.AmbientLight()
light.intensity = 0.1
```

#### Shortcuts

There are a few shortcuts for props that have a `.set()` method (colors, vectors, etc).

```javascript
const light = new THREE.DirectionalLight()
light.position.set(0, 0, 5)
light.color.set('red')
```

Which is the same as the following in JSX:

* `<directionalLight position={[0, 0, 5]} color="red />`

Please refer to the API for [a deeper explanation](https://docs.pmnd.rs/react-three-fiber/api/objects)

#### The result 

```javascript
<Canvas>
  <ambientLight intensity={0.1} />
  <directionalLight color="red" position={[0, 0, 5]} />
  <mesh>
    <boxGeometry />
    <meshStandardMaterial />
  </mesh>
</Canvas>
```

#### Exercise

- try different materials, like `MeshNormalMaterial` or `MeshBasicMaterial`, give them a color

- try different geometries, like `SphereGeometry` or `OctahedronGeometry`

- try changing the `position` on our `mesh` component, by setting the prop with the same name

- try extracting our mesh to a new component





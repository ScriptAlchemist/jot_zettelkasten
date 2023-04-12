# Objects, properties and constructor arguments
All the effective ways of using React Three Fiber

## Declaring Objects

You can use `three.js's entire object catalogue and all properties`. When in doubt, always consult the docs.

You could lay out an object like this:

```javascript
<mesh
  visible
  useData={{ hello: 'world' }}
  position={new THREE.Vector3(1, 2, 3 )}
  rotation={new THREE.Euler(Math.PI / 2, 0, 0)}
  geometry={new THREE.SphereGeometry(1, 16, 16)}
  material={new THREE.MeshBasixMaterial({ color: new THREE.Color('hotpink'),transparent: true })}
/>
```

The problem is that all of these properties will always be re-created. Instead, you should define properties declaratively.

```javascript
<mesh visible userData={{ hello: 'world' }} position={[1, 2, 3]} rotation={[Math.PI /2, 0, 0]}>
  <sphereGeometry args={[1, 16, 16]} />
  <meshStandardMaterial color="hotpink" transparent />
</mesh>
```

## Constructor Arguments

In three.js objects are classes that are instantiated. These classes can receive on-time constructor arguments (`new THEE.SphereGeometry(1, 32)`), and properties (`someObejct.visible = true`). In React Three Fiber, constructor arguments are always passed as an array via `args`. If args change later on, the object must naturally get reconstructed from scratch!

```javascript
<sphereGeometry args={[1, 32]} />
```

## Shortcuts

### Set

All properties whose underlying object has a `.set()` method can directly receive the same argument that `set` would otherwise take. for example `THREE.Color.set` can take a color string, so instead of `color={new THREE.Color('hotpink')}` you can simply write `color="hotpink"`. Some `set` methods take multiple arguments, for instance `THREE.Vector3`, give it an array in that case `position={[100, 0, 0]}`. 

```javascript
<mesh position={[1, 2, 3]} />
  <meshStandardMaterial color="hotpink" />
```

> If you do link up an existing object to a property, for instance
> a THREE.Vector3() to a position, be aware that this will end up
> copying the object in most cases as it calls .copy() on the target.
> This only applies to objects that expose both .set() and .copy()
> (Vectors, Eulers, Matrix, ...). If you link up an existing material or
> geometry on the other hand, it will overwrite, because more these
> objects do not have a .set() method.

### SetScalar

Properties that have a `setScalar` method (for instance `Vector3`) can be set like so:

```javascript
// Translates to <mesh scale={[1, 1, 1]} />
<mesh scale={1} />
```

## Piercing into nested properties

If you want to reach into nested attributes (for instance: `mesh.rotation.x`), just use dash-case.

```javascript
<mesh rotation-x={1} material-uniforms-resolution-value={[512, 512]} />
```

## Dealing with non-scene objects

You can put non-Object3D primitives (geometries, materials, etc) into the render tree as well. They take the same properties and constructor arguments they normally would.

You might be wondering why you would want to put something in the "scene" that normally would not be part of it, in a vanilla three.js app at least. For the same reason you declare any object: it becomes managed, reactive and auto-disposes. These objects are not technically part of the scene, but they "attach" to a parent which is.

### Attach

Use `attach` to bind objects to their parent. If you unmount the attached object it will be taken off its parent automatically.

The following attaches a material to the `material` property of a mesh and a geometry to the `geometry` property:

```javascript
<mesh>
  <meshBasicMaterial attach="material">
  <boxGeometry attach="geometry">
```

> All native elements ending with "material" receive attach="material",
> and all elements ending with "Geometry" receive attach="geometry". You
> do not strictly have to type it out!

```javascript
<mesh>
  <meshBasicMaterial />
  <boxGeometry />
```

You can also deeply nest attach through piercing. The following adds a buffer-attribute to `geometry.attributes.position` and then adds the buffer geometry to `mesh.geometry`.

```javascript
<mesh>
  <bufferGeometry>
    <bufferAttribute attach="attributes-position" count={v.length / 3} array={v} itemSize={3} />
```

#### More examples:

```javascript
// Attach bar to foo.a
<foo>
  <bar attach="a" />

// Attach bar to foo.a.b and foo.a.b.c (nexted object attach)
<foo>
  <bar attach="a-b" />
  <bar attach="a-b-c" />

// Attach bar to foo.a[0] and foo.a[1] (array attach is just object attach)
<foo>
  <bar attach="a-0" />
  <bar attach="a-1" />

// Attach bar to foo via explicit add/remove functions
<foo>
  <bar attach={(parent, self) => {
    parent.add(self)
    return () => parent.remove(self)
  }} />

// The same as a one liner
<foo>
  <bar attach={(parent, self) => (parent.add(self), () => parent.remove(self))} />
```

#### Real-world use cases:

Attaching to nested objects, for instance a shadow-camera:

```
- <directionalLight
-   castShadow
-   position={[2.5, 8, 5]}
-   shadow-mapSize={[1024, 1024]}
-   shadow-camera-far={50}
-   shadow-camera-left={-10}
-   shadow-camera-right={10}
-   shadow-camera-top={10}
-   shadow-camera-bottom={-10}
- />
+ <directionalLight castShadow position={[2.5, 8, 5]} shadow-mapSize={[1024, 1024]}>
+   <orthographicCamera attach="shadow-camera" args={[-10, 10, 10, -10]} />
+ </directionalLight>
```

Arrays must have explicit order, for instance multi-materials:

```javascript
<mesh>
  {colors.map((color, index) => <meshBasicMaterial key={index} attach={`material-${index}`} color={color} />}
</mesh>
```

## Putting already existing objects into the scene-graph

You can use the `primitive` placeholder for that. You can still give it properties or attach nodes to it. Never add the same object multiple times, this is not allowed in three.js! Primitives will not dispose of the object they carry on unmount. You are responsible for disposing of it!

```javascript
const mesh = new THREE.Mesh(geometry, material)

function Component() {
  return <primitive object={mesh} position={[10, 0, 0]} />
```

## Using 3rd-party objects declaratively

The `extend` function extends React Three Fiber's catalogue of JSX elements. Components added this way can then be referenced in the scene-graph using camel casing similar to other primitives.

```javascript
import { extend } from '@react-three/fiber'
import { OrbitControls, TransformControls } from 'three-stdlib'
extend({ OrbitControls, TransformControls })

// ...
return (
  <>
    <orbitControls />
    <transformControls />
```

If you're using TypeScript, you'll also need to [extend the JSX namespace](https://docs.pmnd.rs/react-three-fiber/tutorials/typescript#extending-jsx-intrinsic-elements)

## Disposal

Freeing resources is a manual chore in `three.js`, but React is aware of object-lifecycles, hence React Three Fiver will attempt to free resources for you by calling `object.dispose()`. If present, on all unmounted objects.

If you manage assets by yourself, globally or in a cache, this may not be what you want. You can switch it off by placing `dispose={null}` onto meshes, materials, etc, or even on parent containers like groups, it is now valid for the entire tree.

```javascript
const globalGeometry = new THREE.BoxGeometry()
const globalMaterial = new THREE.MeshBasicMaterial()

function Mesh() {
  return (
    <group dispose={null}>
      <mesh geometry={globalGeometry} material={globalMaterial} />
```



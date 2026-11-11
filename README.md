# Three.js Dist Builder

Build a classic `three.min.js` from modern Three.js (**r174+**). It exposes a global `THREE`, so you can use it in a plain `<script>` tag with no bundler or import map.

Since r161, the official Three.js package no longer ships `build/three.js` or `build/three.min.js`. This tool fills that gap for projects that load Three.js as a global, such as WordPress themes, legacy sites, or hosts that provide `THREE` to other scripts (for example, the `build.externals` option in [WebKineForge](#use-with-webkineforge)).

## Features

- Generates a `three.min.js` that exposes the global `THREE`
- Compatible with Three.js r174 and later
- One command to build; output goes to `dist/`

## Quick start

Requires Node.js and npm.

```sh
git clone https://github.com/<your-account>/threejs-dist-builder.git
cd threejs-dist-builder
npm install
npm run build
```

When the build finishes, you can find the generated files in the `dist/` directory.

## Usage

Load `three.min.js` with a plain `<script>` tag. No `import` is required for the core library:

```html
<script defer src="assets/js/min/three.min.js?ver=r175"></script>
<script defer src="assets/js/app.js"></script>
```

```js
// assets/js/app.js — `THREE` is available as a global
const scene = new THREE.Scene();
const camera = new THREE.PerspectiveCamera(40, window.innerWidth / window.innerHeight, 1, 100);
```

> The `?ver=r175` query string is optional. It only busts the browser cache when you upgrade Three.js.

### Using addons (OrbitControls, RoomEnvironment, etc.)

Addons are ES modules. Load `three.min.js` first, then load your own script as `type="module"`:

```html
<script defer src="assets/js/min/three.min.js?ver=r175"></script>
<script type="module" src="assets/js/app.js"></script>
```

```js
// assets/js/app.js
import { OrbitControls } from './jsm/controls/OrbitControls.js';
import { RoomEnvironment } from './jsm/environments/RoomEnvironment.js';

const renderer = new THREE.WebGLRenderer({ antialias: true });
renderer.setSize(window.innerWidth, window.innerHeight);
document.body.appendChild(renderer.domElement);

const pmremGenerator = new THREE.PMREMGenerator(renderer);

const scene = new THREE.Scene();
scene.background = new THREE.Color(0xbfe3dd);
scene.environment = pmremGenerator.fromScene(new RoomEnvironment(), 0.04).texture;

const camera = new THREE.PerspectiveCamera(40, window.innerWidth / window.innerHeight, 1, 100);
camera.position.set(5, 2, 8);

const controls = new OrbitControls(camera, renderer.domElement);
controls.target.set(0, 0.5, 0);
controls.enablePan = false;
controls.enableDamping = true;
controls.update();

renderer.setAnimationLoop(() => {
    controls.update();
    renderer.render(scene, camera);
});
```

Scripts marked `defer` and module scripts run in document order after the page is parsed, so `THREE` is always defined before your code runs.

## Compatibility

Three.js r174 and later.

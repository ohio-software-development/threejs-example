# threejs-example

This is a sample project for the accompanying WebGL Graphics Frameworks presentation for OUSDC.

The provided instructions are for Debian-based distros (tested on Ubuntu & Fedora).

## Setup
0. Clone this repo
1. Run `npm install`
2. Make sure no errors are thrown when you run `npm run dev`
3. Let's make a solar system! Go to `src/main.js` and write this
```js
import * as THREE from 'three';

const Colors = THREE.Color.NAMES;

// Scene, Camera, Renderer
const scene = new THREE.Scene();
const camera = new THREE.PerspectiveCamera(
    50, // FOV
    window.innerWidth / window.innerHeight, // Aspect Ratio
    0.1, // Near clipping plane
    1000 // Far clipping plane
);
const renderer = new THREE.WebGLRenderer({ antialias: true });
renderer.setSize(window.innerWidth, window.innerHeight);
document.body.appendChild(renderer.domElement);

// Add ambient light because the planets are really dim without
const directionalLight = new THREE.DirectionalLight(Colors.white, 1); // color and intensity
directionalLight.position.set(5, 5, 5).normalize();
scene.add(directionalLight);

// Sun
const sun = new THREE.Mesh(
    new THREE.SphereGeometry(1.4, 32, 32),
    new THREE.MeshBasicMaterial({ color: Colors.yellow })
);
scene.add(sun);

/*
=== Planet Facts ===
Diameter Size (million km):
- Sun: 1.392
- Jupiter: 0.139822
- Saturn:  0.116484
- Uranus:  0.050724
- Neptune: 0.049244
- Earth:   0.012742
- Venus:   0.01204
- Mars:    0.006779
- Mercury: 0.004880

Orbit Speed (km/s):
- Mercury: 47.87
- Venus:   35.02
- Earth:   29.78
- Mars:    24.08
- Jupiter: 13.07
- Saturn:  9.69
- Uranus:  6.81
- Neptune: 5.43
*/

// Planets
const planets = [];
const planetData = [
    { size: 0.2, distance: 3, speed: 47, color: Colors.gray }, // Mercury
    { size: 0.5, distance: 5, speed: 35, color: Colors.orange }, // Venus
    { size: 0.6, distance: 7, speed: 29, color: Colors.blue }, // Earth
    { size: 0.4, distance: 9, speed: 24, color: Colors.red }, // Mars
];

// Instantiate planets
planetData.forEach((data) => {
    const planet = new THREE.Mesh(
        new THREE.SphereGeometry(data.size, 32, 32), // Geometry
        new THREE.MeshStandardMaterial({ color: data.color }) // Material
    );
    scene.add(planet);
    planets.push({ mesh: planet, ...data });
});

// Camera Position - Place the camera in the corner of the simulation
camera.position.set(15, 10, 15); // x, y, z
camera.lookAt(0, 0, 0); // Looking at the sun

// Animation Loop
function animate() {
    requestAnimationFrame(animate);

    // Rotate planets around the sun
    planets.forEach((planet) => {
        const angle = Date.now() * 0.00004 * planet.speed;
        planet.mesh.position.x = Math.cos(angle) * planet.distance;
        planet.mesh.position.z = Math.sin(angle) * planet.distance;
    });

    renderer.render(scene, camera);
}
animate();

// Handle window resize
window.addEventListener('resize', () => {
    renderer.setSize(window.innerWidth, window.innerHeight);
    camera.aspect = window.innerWidth / window.innerHeight;
    camera.updateProjectionMatrix();
});
```
4. Re-run `npm run dev` and visit the webpage. You should see your newly created solar system!
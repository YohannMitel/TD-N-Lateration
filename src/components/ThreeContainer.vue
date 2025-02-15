<template>
  <div class="rounded shadow " ref="threeContainer" style="width: 100%; height: 100%;"></div>
</template>

<script setup>
import * as THREE from 'three';
import { onMounted, onUnmounted, ref, watch } from 'vue';
import { GLTFLoader } from 'three/examples/jsm/loaders/GLTFLoader';
// Importer OrbitControls depuis Three.js
import { OrbitControls } from 'three/examples/jsm/controls/OrbitControls';

const rotationSpeed = 0.002;
const rotation = ref(rotationSpeed);

// Define props to control visibility and opacity
const props = defineProps({
  opacity: {
    type: Boolean,
    default: false
  },
  bin: {
    type: Boolean,
    default: true
  },
  controllers: {
    type: Boolean,
    default: true
  },
  openState: {
    type: Boolean,
    default: false
  }
});

// Reference for the DOM container
const threeContainer = ref(null);
let renderer, scene, camera, controls, raycaster, hoveredObject, mouse = [];
const initialCamPos = { x: 0, y: 5, z: 20 };
// Mouse event handlers
function onMouseMove(event) {
  const rect = threeContainer.value.getBoundingClientRect();
  mouse.x = ((event.clientX - rect.left) / rect.width) * 2 - 1;
  mouse.y = -((event.clientY - rect.top) / rect.height) * 2 + 1;
}

// Handle window resize
const handleResize = () => {
  if (!renderer) return;
  camera.aspect = threeContainer.value.clientWidth / threeContainer.value.clientHeight;
  camera.updateProjectionMatrix();
  renderer.setSize(threeContainer.value.clientWidth, threeContainer.value.clientHeight);
};

const resetCamera = () => {
  camera.position.set(initialCamPos.x, initialCamPos.y, initialCamPos.z);
  camera.lookAt(0, 0, 0); // Recalibrer la direction de la caméra
  controls.reset(); // Réinitialiser les contrôles pour éviter qu'ils ne perturbent la position
};


onMounted(() => {
  if (!threeContainer.value) return;

  // Create the scene
  scene = new THREE.Scene();
  scene.background = new THREE.Color(0xeef8f6);

  raycaster = new THREE.Raycaster();
  mouse = new THREE.Vector2();

  // Add axes helper (X: Red, Y: Green, Z: Blue)
  const axesHelper = new THREE.AxesHelper(10); // Size of the axes
  scene.add(axesHelper);

  // Add ambient light
  const hemisphereLight = new THREE.HemisphereLight(0xffffff, 0x444444, 1);
  hemisphereLight.position.set(0, 50, 0);
  scene.add(hemisphereLight);

  // Add a camera
  camera = new THREE.PerspectiveCamera(
    75,
    threeContainer.value.clientWidth / threeContainer.value.clientHeight,
    0.1,
    1000
  );

  // Set up the renderer
  renderer = new THREE.WebGLRenderer({ antialias: true });
  renderer.setSize(threeContainer.value.clientWidth, threeContainer.value.clientHeight);
  threeContainer.value.appendChild(renderer.domElement);

  // Set camera position and direction
  camera.position.set(initialCamPos.x, initialCamPos.y, initialCamPos.z);
  camera.lookAt(0, 0, 0);

  // Ajouter OrbitControls pour déplacer la caméra
  controls = new OrbitControls(camera, renderer.domElement);
  controls.enableDamping = true; // Ajoute un effet de lissage aux mouvements de la caméra
  controls.dampingFactor = 0.25; // Facteur de lissage
  controls.screenSpacePanning = false; // Désactive le déplacement en dehors de l'espace de la caméra
  controls.maxDistance = 50; // Limite la distance de la caméra
  controls.minDistance = 5; // Limite la distance minimale de la caméra

  // Event listeners
  window.addEventListener('resize', handleResize);
  threeContainer.value.addEventListener('mousemove', onMouseMove);

  // Start the animation loop
  animate();

  // Update the controls in the animation loop
  function animate() {
    requestAnimationFrame(animate);
    controls.update(); // Met à jour les contrôles
    renderer.render(scene, camera);
  }
});

onUnmounted(() => {
  threeContainer.value.removeEventListener('mousemove', onMouseMove);

  window.removeEventListener('resize', handleResize);
  renderer.dispose();
});

defineExpose({
  resetCamera
});
</script>

<style scoped>
div {
  display: block;
  position: relative;
  overflow: hidden;
}
</style>

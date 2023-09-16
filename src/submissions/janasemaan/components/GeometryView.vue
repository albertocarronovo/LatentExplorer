<template>
  <div id="threejs-container"></div>
  <div class="compute-run-button">
    <!-- <ButtonInput tittle="Generate!" v-on:click="compute" /> -->
  </div>
</template>

<script setup>
// Imports;
import { onMounted, onUpdated } from "vue";
import * as THREE from "three";
import { OrbitControls } from "three/examples/jsm/controls/OrbitControls";

import { runCompute } from "@/scripts/compute.js";
import { Rhino3dmLoader } from "three/addons/loaders/3DMLoader.js";

import ButtonInput from "commoncomponents/ButtonInput.vue";

const loader = new Rhino3dmLoader();
loader.setLibraryPath("https://cdn.jsdelivr.net/npm/rhino3dm@0.15.0-beta/");

// Property coming from parent component
const props = defineProps(["data", "path"]);
const emits = defineEmits(["updateMetadata"]);
// Three js objects
let renderer, camera, scene, controls, geometry;

let width = 900;
let heigh = 700;

function init() {
  // rendeder
  
  renderer = new THREE.WebGLRenderer({ antialias: true });
  renderer.setSize(width, heigh);
  renderer.setPixelRatio(window.devicePixelRatio);
  document.getElementById("threejs-container").appendChild(renderer.domElement);

  // camera
  camera = new THREE.PerspectiveCamera(75, width / heigh, 0.1, 1000);
  camera.position.z = -30;
  camera.position.set(-100, 0, 3); //setup the right camera to start with!

  // scene
  scene = new THREE.Scene();
  scene.background = new THREE.Color(0.745,0.203,0.33);

  // orbit controls
  controls = new OrbitControls(camera, renderer.domElement);
  controls.autoRotate = true;

  // add a directional light
  const directionalLight = new THREE.DirectionalLight(0xffffff);
  directionalLight.intensity = 4;
  scene.add(directionalLight);

  const ambientLight = new THREE.AmbientLight();
  scene.add(ambientLight);

  // add fun shape
  animate();
}

async function compute() {
  console.log("Runnning compute... \ndata sent: ", props.data);

  // Save start time
  const startTime = performance.now();

  const doc = await runCompute(props.data, props.path);

  if (doc.metadata) {
    emits("updateMetadata", doc.metadata);
  }
  // clear objects from scene
  scene.traverse((child) => {
    if (!child.isLight) {
      scene.remove(child);
    }
  });

  const buffer = new Uint8Array(doc.toByteArray()).buffer;
  loader.parse(buffer, function (object) {
    ///////////////////////////////////////////////////////////////////////
    // add object graph from rhino model to three.js scene
    scene.add(object);
    console.log("Compute done");
  });

  // Save end time
  const endTime = performance.now();

  // Calculate the duration in milliseconds
  const duration = (endTime - startTime) / 1000;
  // Log the duration to the console
  console.log(`Gh script + geometry loading: ${duration.toFixed(3)} seconds`);
}


// for controls update
function animate() {
  requestAnimationFrame(animate);
  controls.update();
  renderer.render(scene, camera);
}

function onSliderChange(color) {
  // scene.clear();
  compute();
}

// This function runs at the beginning of the component lifecycle.
// More about Vue lifecycles: https://vuejs.org/guide/essentials/lifecycle.html#lifecycle-diagram
onMounted(() => {
  init();
  compute();
});

// This function runs when DOM updates.
onUpdated(() => {
  // text content should be the same as current `count.value`
  onSliderChange();
});
</script>

<style scoped>
.compute-run-button {
  align-content: center;
  text-align: center;
  color: brown;
}
</style>
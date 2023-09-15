<template>
  <div id="threejs-container"></div>
  <!-- <div class="compute-run-button">
    <ButtonInput tittle="Compute!" v-on:click="compute" />
  </div> -->
</template>

<script setup>
// Imports;
import { onMounted, onUpdated } from "vue";
import * as THREE from "three";
import { OrbitControls } from "three/examples/jsm/controls/OrbitControls";
import { TransformControls } from "three/examples/jsm/controls/TransformControls";
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

const widthRatio = 0.7;
const heightRatio = 0.85;
let width = window.innerWidth * widthRatio;
let heigh = window.innerHeight * heightRatio;


function init() {
  window.addEventListener("resize", onWindowResize, false);
  // rendeder
  renderer = new THREE.WebGLRenderer({ antialias: true });
  renderer.setSize(width, heigh);
  renderer.setPixelRatio(window.devicePixelRatio);
  renderer.shadowMap.enabled = true;
  document.getElementById("threejs-container").appendChild(renderer.domElement);

  // camera
  THREE.Object3D.DefaultUp = new THREE.Vector3( 0, 0, 1 );
  camera = new THREE.PerspectiveCamera(75, width / heigh, 0.1, 1000);
  camera.position.set(20, 20, 0);//setup the right camera to start with!
  camera.up.set(0, 0, 1);
  camera.lookAt(0, 0, 0);

  // scene
  scene = new THREE.Scene();
  scene.background = new THREE.Color(0xffffff);

  // orbit controls
  controls = new OrbitControls(camera, renderer.domElement);


  // create a spotlight with shadow camera parameters
  const spotlight = new THREE.SpotLight(0xffffff, 1, 100, Math.PI / 4);
  spotlight.position.set(-5, -5, 15);
  spotlight.castShadow = true;

  // set shadow map properties
  spotlight.shadow.mapSize.width = 1024;
  spotlight.shadow.mapSize.height = 1024;
  spotlight.shadow.camera.near = 0.1;
  spotlight.shadow.camera.far = 100;
  scene.add(spotlight);



  const ambientLight = new THREE.AmbientLight(0xffffff, 4);
  scene.add(ambientLight);

  // add floor material
  const shadowMaterial = new THREE.ShadowMaterial();
  shadowMaterial.opacity = 0.5;
  shadowMaterial.blending = THREE.MultiplyBlending;

  // add floor geometry
  var groundGeometry = new THREE.PlaneGeometry(30, 30);
  var ground = new THREE.Mesh(groundGeometry, shadowMaterial);
  ground.position.set(0,0,-1.8)
  ground.receiveShadow = true;
  scene.add(ground);

/*
  // add cube to show controls
  const geometry = new THREE.BoxGeometry(1, 2, 1);
  const material = new THREE.MeshNormalMaterial();
  const cube = new THREE.Mesh(geometry, material);
  cube.castShadow=true;
  scene.add(cube);
 
  // transform controls are equivalent to rhino gumball
  const transformControls = new TransformControls(camera, renderer.domElement);

  //   disable other controls when mving this transform control
  transformControls.addEventListener("change", animate);
  transformControls.addEventListener("mouseDown", function () {
    controls.enabled = false;
  });
  transformControls.addEventListener("mouseUp", function () {
    controls.enabled = true;
  });

  transformControls.attach(cube);
  scene.add(transformControls);
*/
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
    if (!child.isLight && child.isCompute) {
      scene.remove(child);
    }
  });


  const buffer = new Uint8Array(doc.toByteArray()).buffer;
  loader.parse(buffer, function (object) {

      object.isCompute = true;
      object.castShadow = true;

      object.children.forEach(child => {
        if (child.isMesh )
        {
          // add nice shininess!!
          let mat = child.material
          mat.roughness=0;
          mat.metalness=0.786;
        }
      })

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

function onWindowResize() {
  camera.aspect =
    (window.innerWidth * widthRatio) / (window.innerHeight * heightRatio);
  camera.updateProjectionMatrix();

  renderer.setSize(
    window.innerWidth * widthRatio,
    window.innerHeight * heightRatio
  );
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
}
</style>
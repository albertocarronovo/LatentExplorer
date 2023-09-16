<template>
  <div id="threejs-container"></div>
  <!-- <div class="compute-run-button">
    <ButtonInput tittle="Compute!" v-on:click="compute" />
  </div> -->
</template>

<script setup>
// Imports;
import { onMounted, onUpdated } from "vue";
import * as THREE from 'three';
import { GLTFLoader } from 'three/addons/loaders/GLTFLoader.js';
import { OrbitControls } from "three/examples/jsm/controls/OrbitControls";


// Property coming from parent component
const props = defineProps(["data", "path"]);
const emits = defineEmits(["updateMetadata"]);
const loader = new GLTFLoader();

// Three js objects
let renderer, camera, scene, controls;

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
  scene.background = new THREE.Color(0x252020);

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

  animate(); 
}

async function loadGLTFModel() {
  console.log("Loading GLTF model...");

  // Load the GLTF model
  loader.load(
    // Replace 'src\submissions\albertocarro\assets\y.gltf' with the actual path to your model
    'src/submissions/albertocarro/assets/y.gltf',
    (gltf) => {
      gltf.scene.traverse((child) => {
        if (child.isMesh) {
          // Customize material properties if needed
          const mat = child.material;
          mat.roughness = 0;
          mat.metalness = 0.786;
        }
      });

      // Add the loaded GLTF scene to your Three.js scene
      scene.add(gltf.scene);
      gltf.scene.position.set(0, 0, 0); // Adjust the position as needed
      // Adjust camera and controls to fit the new model
      fitCameraToSelection(camera, controls, [gltf.scene]);

      console.log("GLTF model loaded");
    },
    (xhr) => {
      console.log((xhr.loaded / xhr.total) * 100 + "% loaded");
    },
    (error) => {
      console.error("An error occurred while loading the GLTF model", error);
    }
  );
}

// Call the loadGLTFModel function in onMounted
onMounted(() => {
  init();
  loadGLTFModel(); // Load the GLTF model when the component is mounted
});

// for controls update
function animate() {
  requestAnimationFrame(animate);
  controls.update();
  renderer.render(scene, camera);
}

// // Assuming you have loaded the GLTF model into the variable 'gltf'
// function toggleMeshesVisibility(sliderValue) {
//   for (let i = 0; i <= 11; i++) {
//     const meshName = i.toString(); // Convert the index to a string to match mesh names
//     const mesh = gltf.scene.getObjectByName(meshName);

//     if (mesh) {
//       mesh.visible = i <= sliderValue; // Show meshes with indices up to sliderValue
//     }
//   }
// }


function fitCameraToSelection(camera, controls, selection, fitOffset = 1.2) {
  let box = new THREE.Box3();

  for (const object of selection) {
    if (object.isMesh) {
      box.expandByObject(object);
    } else {
      // If the object is not a mesh (like a group), calculate its bounding box recursively
      object.traverse(child => {
        if (child.isMesh) {
          box.expandByObject(child);
        }
      });
    }
  }

  let size = box.getSize(new THREE.Vector3());
  let center = box.getCenter(new THREE.Vector3());

  const maxSize = Math.max(size.x, size.y, size.z);
  const fitHeightDistance = maxSize / (2 * Math.atan(Math.PI * camera.fov / 360));
  const fitWidthDistance = fitHeightDistance / camera.aspect;
  const distance = fitOffset * Math.max(fitHeightDistance, fitWidthDistance);

  const direction = controls.target.clone()
    .sub(camera.position)
    .normalize()
    .multiplyScalar(distance);

  controls.maxDistance = distance * 10;
  controls.target.copy(center);

  camera.near = distance / 100;
  camera.far = distance * 100;
  camera.updateProjectionMatrix();

  camera.position.copy(controls.target).sub(direction);

  controls.update();
}

// function onSliderChange(color) {
//   // scene.clear();
//   compute();
// }

function onWindowResize() {
  camera.aspect =
    (window.innerWidth * widthRatio) / (window.innerHeight * heightRatio);
  camera.updateProjectionMatrix();

  renderer.setSize(
    window.innerWidth * widthRatio,
    window.innerHeight * heightRatio
  );
}

// // This function runs when DOM updates.
// onUpdated(() => {
//   // text content should be the same as current `count.value`
//   onSliderChange();
// });
</script>

<style scoped>

</style>
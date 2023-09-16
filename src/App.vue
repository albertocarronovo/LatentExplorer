<script setup>
// Imports from packages
import { ref, onMounted } from "vue";
import { loadRhino } from "@/scripts/compute.js";

import Albertocarro from "./submissions/albertocarro/Albertocarro.vue";
// import janasemaan from "./submissions/janasemaan/janasemaan.vue";

var submissions = [
  { name: "Latent Walk Mode", visible: ref(true) },
];

function toggleComponent(item) {
  submissions.forEach(
    (menuItem) => (menuItem.visible.value = item === menuItem)
  );
}

let aboutPageVisible = ref(true)

function aboutPage()
{
  submissions.forEach(submission => submission.visible.value = false)
  aboutPageVisible.visible.value = true;
}

onMounted(() => {
  // loadRhino();
  loadGLTFModel();
});
</script>


<template>

<div id="top-bar">
    <div>
       <span class="my-nav" @mouseenter="openNav">  &#9776;</span>
     </div>

    <div id="title-container">

      <h2 class="course-title" style="font-family: 'Roboto Mono', monospace;">
        Latent<br>Studio    
      </h2>
      <img class="logo-image" alt="Iaac logo" src="src\assets\ViewCapture20230907_003737.jpg" />
    </div>
</div>

<div id="mySidenav" class="sidenav" @mouseleave="closeNav">
    <a href="javascript:void(0)" class="closebtn" 
    :onclick="closeNav" 
    >&times;</a>
    <div class="left-sidebar">
      <h3 class="home-button" :class="{ selected: aboutPageVisible.value }" @click="aboutPage()">About</h3>
      <hr />

      <ul class="menu">
        <li v-for="(item, index) in submissions" :key="index" :class="{ selected: item.visible.value }"
          @click="toggleComponent(item)">
          {{ item.name }}
        </li>
      </ul>
    </div>
</div>


  <div class="container">
    <!-- <div class="left-sidebar">
      <hr />

      <h3>Submissions</h3>
      <ul class="menu">
        <li
          v-for="(item, index) in submissions"
          :key="index"
          :class="{ selected: item.visible.value }"
          @click="toggleComponent(item)"
        >
          {{ item.name }}
        </li>
      </ul>
    </div> -->
    <div class="content">
      <Albertocarro v-if="submissions[0].visible.value"></Albertocarro>
      <!-- <janasemaan v-if="submissions[1].visible.value"></janasemaan> -->
    </div>
  </div>

</template>


<style>
#app {
  font-family: Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  color: #2d7fd1;
}

#top-bar {
  display: flex;
  font-size: 1rem;
  align-items: safe;
  justify-content: space-between;
  background-color: #080808;

}

#title-container {
  display: flex;
  align-items: center;
  color: white;
  margin-right: 1.5rem;
}

hr {
  border: 1px solid #ff65f7;
}

.left-sidebar {
  margin: 5px 10px 2px 0px;
  padding: 5px;
}
.content {
  display: flex;
}

.logo-image {
  height: 3.25rem;
  padding: 0.5rem;
}

h2 {
  font-size: 1.125rem;
  font-weight: 600;
  letter-spacing: -0.05em;
  font-size: 1.125rem;
  font-weight: 600;
  letter-spacing: 0.01em;
}

.container {
  display: flex;
  flex-direction: row;
  height: 100vh;
  overflow: hidden;
  margin: 10px 0px;
}

.menu {
  display: flex;
  flex-direction: column;
  list-style: none;
  padding: 10px 0px;
  margin: 0px;
  width: 200px;
}

.menu li {
  padding: 10px 10px;
  margin: 2px 0px;
  cursor: pointer;
  transition: background-color 0.2s ease-in-out;
  -webkit-transition: background-color 0.2s ease-out;
  transition: background-color 0.2s ease-out;
  border-radius: 30px;
  border: solid transparent;
  font-weight: bolder;
}

.menu li:hover {
  background-color: #f2dd1c4e;
}

.selected {
  background-color: #2b302b;
}
</style>

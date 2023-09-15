<script setup>
// Imports from packages
import { ref, onMounted } from "vue";
import { loadRhino } from "@/scripts/compute.js";

import StudentA from "./submissions/Student A/StudentA.vue";
import StudentB from "./submissions/Student B/StudentB.vue";
import StudentC from "./submissions/Student C/StudentC.vue";
import Albertocarro from "./submissions/albertocarro/Albertocarro.vue";

var submissions = [
  { name: "Student a submission", visible: ref(true) },
  { name: "Student b submission", visible: ref(false) },
  { name: "Student c submission", visible: ref(false) },
  { name: "Alberto Carro submission", visible: ref(false) },
];

function toggleComponent(item) {
  submissions.forEach(
    (menuItem) => (menuItem.visible.value = item === menuItem)
  );
}

onMounted(() => {
  loadRhino();
});
</script>

<template>
  <div id="top-bar">
    <div id="title-container">
      <img class="logo-image" alt="Iaac logo" src="./assets/iaac-white.png" />
      <h2 class="course-title">
        Digital Tools for Cloud-based Data Management
      </h2>
    </div>
  </div>

  <div class="container">
    <div class="left-sidebar">
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
    </div>

    <div class="content">
      <StudentA v-if="submissions[0].visible.value"></StudentA>
      <StudentB v-if="submissions[1].visible.value"></StudentB>
      <StudentC v-if="submissions[2].visible.value"></StudentC>
      <Albertocarro v-if="submissions[3].visible.value"></Albertocarro>
    </div>
  </div>
</template>


<style>
#app {
  font-family: Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  color: #2c3e50;
}

#top-bar {
  display: flex;
  align-items: center;
  justify-content: space-between;
  background-color: #1c2127;
}

#title-container {
  display: flex;
  align-items: center;
  color: white;
  margin-right: 1.5rem;
}

hr {
  border: 1px solid #f2dd1c;
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
  background-color: #f2dd1c;
}
</style>

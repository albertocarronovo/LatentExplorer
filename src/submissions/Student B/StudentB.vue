<template>
  <div class="container">
    <h2>{{ title }}</h2>

    <SliderInput
      v-bind:title="secondSliderName"
      v-bind:min="1"
      v-bind:max="50"
      v-bind:step="1"
      v-on:updateValue="updateValue"
    />

    <StudentASlider
      v-bind:title="firstSliderName"
      v-bind:min="1"
      v-bind:max="500"
      v-bind:step="5"
      v-on:updateValue="updateValue"
    ></StudentASlider>

    <ToggleInput
      v-bind:title="toggleName"
      v-on:updateValue="updateLocal"
    ></ToggleInput>
    <p>Value received from Toggle: {{ runToggle }}</p>
  </div>

  <div id="content">
    <ThreeJSGeometryView :size="firstSlider" />
  </div>
</template>

<script setup>
import { ref } from "vue";

// Import student custom inputs
import StudentASlider from "./components/StudentASlider.vue";
import ThreeJSGeometryView from "./components/ThreeJSGeometryView.vue";

// Import common components
import ToggleInput from "commoncomponents/ToggleInput.vue";
import SliderInput from "commoncomponents/SliderInput.vue";

// Define props
const title = ref("Second student homework");

// Grasshopper input names
const firstSliderName = ref("Cube size");
const toggleName = ref("Common toggle");
const secondSliderName = ref("Common slider");

// Values to receive from user inputs components
const firstSlider = ref(10);
const secondSlider = ref(5);
var runToggle = ref(false);

function updateLocal(newValue) {
  runToggle.value = newValue;
}

function updateValue(newValue, parameterName) {
  if (parameterName === firstSliderName) {
    firstSlider.value = newValue;
  } else if (parameterName === secondSliderName) {
    secondSlider.value = newValue;
  }
}
</script>

<style scoped>
.container {
  display: flex;
  flex-direction: column;
  align-items: left;
  padding: 5px 10px;
  margin: 15px;
  width: 400px;
  height: 500px;
  border-radius: 5px;
  background-color: #f080806b;
}

#content {
  display: flex;
}
h2 {
  text-align: left;
}
img {
  max-width: 100%;
}
</style>
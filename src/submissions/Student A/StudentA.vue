<template>
  <div class="container">
    <h2>{{ title }}</h2>
    <StudentASlider
      v-bind:title="firstSliderName"
      v-bind:min="1"
      v-bind:max="5"
      v-bind:step="0.5"
      v-on:updateValue="updateValue"
    ></StudentASlider>

    <ToggleInput
      v-bind:title="toggleName"
      v-on:updateValue="updateLocal"
    ></ToggleInput>

    <SliderInput
      v-bind:title="secondSliderName"
      v-bind:min="1"
      v-bind:max="50"
      v-bind:step="1"
      v-on:updateValue="updateValue"
    />

    <p>Value received in App.vue: {{ runToggle }}</p>
  </div>
</template>

<script setup>
import { ref } from "vue";

// Import student custom inputs
import StudentASlider from "./components/StudentASlider.vue";

// Import common components
import ToggleInput from "commoncomponents/ToggleInput.vue";
import SliderInput from "commoncomponents/SliderInput.vue";

// Define props
const title = ref("Student A homework title");

// Grasshopper input names
const firstSliderName = ref("Custom student slider");
const toggleName = ref("Common toggle");
const secondSliderName = ref("Common slider");

// Values to receive from user inputs components
const firstSlider = ref(10);
const secondSlider = ref(1.4);
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
  justify-content: center;
  align-items: center;
  height: 85%;
  background-color: #ffe10060;
}
h2 {
  text-align: center;
}
img {
  max-width: 100%;
}
</style>
<template>
  <div class="gh-inputs-container">
    <h2 class="submission-title">{{ title }}</h2>
    <p>Web-tool for LCA estimations on façades.<br>Choose the material and the main<br>
      dimensions to get the kgCO2.<br><br>Note:<br> "Module" should be at least<br> double the "wall width". </p>
      
    <DropdownSelector
      v-bind:title="dropdownName"
      v-bind:options="colorOptions"
      v-bind:val="selectedColorIndex"
      v-on:updateSelection="updateDropdown"
    />

    <StudentCSlider
      v-bind:title="SliderName1"
      v-bind:min="3"
      v-bind:max="36"
      v-bind:step="1"
      v-bind:val="Slider1"
      v-on:updateValue="updateValue"
    />

    <StudentCSlider
      v-bind:title="SliderName2"
      v-bind:min="6"
      v-bind:max="16"
      v-bind:step="1"
      v-bind:val="Slider2"
      v-on:updateValue="updateValue"
    />

    <StudentCSlider
      v-bind:title="SliderName3"
      v-bind:min="6"
      v-bind:max="51"
      v-bind:step="3"
      v-bind:val="Slider3"
      v-on:updateValue="updateValue"
    />

    <StudentCSlider
      v-bind:title="SliderName4"
      v-bind:min="2.5"
      v-bind:max="10"
      v-bind:step="0.1"
      v-bind:val="Slider4"
      v-on:updateValue="updateValue"
    />

    <StudentCSlider
      v-bind:title="SliderName5"
      v-bind:min="0.5"
      v-bind:max="10"
      v-bind:step="0.1"
      v-bind:val="Slider5"
      v-on:updateValue="updateValue"
    />
    
    <StudentCSlider
      v-bind:title="SliderName6"
      v-bind:min="0.3"
      v-bind:max="5.0"
      v-bind:step="0.1"
      v-bind:val="Slider6"
      v-on:updateValue="updateValue"
    />

    <ToggleInput
      v-bind:title="toggleName"
      v-bind:val="runToggle"
      v-on:updateValue="updateLocal"
    ></ToggleInput>
    
  </div>
<!-- 
    <SliderInput
      v-bind:title="SliderName8"
      v-bind:min="1"
      v-bind:max="20"
      v-bind:step="1"
      v-bind:val="Slider8"
      v-on:updateValue="updateValue"
    />
 -->
    <div v-if="metadata.length > 0">
      <h2 class="submission-title">Received values:</h2>
      <ol>
        <li v-for="item in metadata" :key="item.name">
          {{ item.name }}: {{ item.value }}
        </li>
      </ol>
    </div>
  <div class="geometry-view">
    <GeometryView
      v-bind:data="computeData"
      v-bind:path="path"
      v-bind:visibility="runToggle"
      v-on:updateMetadata="receiveMetedata"
    />
  </div>
</template>

<script setup>
import { ref, computed } from "vue";

// Import components
import ToggleInput from "./components/ToggleInput.vue";
import StudentCSlider from "./components/StudentCSlider.vue";
import GeometryView from "./components/GeometryView.vue";
import DropdownSelector from "./components/DropdownSelector.vue";

// Define the tittle of your project
const title = ref("Foot.print(Finder)");

// Define Grasshopper input names
const SliderName1 = ref("Length"); //must match the Input name in your GH definition!
const Slider1 = ref(8); //default slider value

const SliderName2 = ref("Width"); //must match the Input name in your GH definition!
const Slider2 = ref(8); //default slider value

const SliderName3 = ref("Height"); //must match the Input name in your GH definition!
const Slider3 = ref(13); //default slider value

const SliderName4 = ref("Floor Height"); //must match the Input name in your GH definition!
const Slider4 = ref(3); //default slider value

const SliderName5 = ref("Module Size"); //must match the Input name in your GH definition!
const Slider5 = ref(2); //default slider value

const SliderName6 = ref("Wall Width"); //must match the Input name in your GH definition!
const Slider6 = ref(1); //default slider value

/* 
const firstSliderName = ref("Length1"); //must match the Input name in your GH definition!
const firstSlider = ref(2); //default slider value

const secondSliderName = ref("Width1"); //must match the Input name in your GH definition!
const secondSlider = ref(10); //default slider value

const firstSliderName = ref("Length1"); //must match the Input name in your GH definition!
const firstSlider = ref(2); //default slider value

const secondSliderName = ref("Width1"); //must match the Input name in your GH definition!
const secondSlider = ref(10); //default slider value
*/

const toggleName = ref("Corner");
var runToggle = ref(true);

function updateLocal(newValue) {
  runToggle.value = newValue;
}

const dropdownName = ref("Facade material");
const selectedColorIndex = ref(0);

const colorOptions = [
  { label: "Concrete", value: 0 },
  { label: "Timber", value: 1 },
  { label: "Brick", value: 2 },
  { label: "Steel", value: 3 },
  { label: "Aluminum", value: 4 },
];

const path = "./src/submissions/Student C/assets/LCA_SIMPLE - Copy.gh";

///.............................................

let data = ref({});
let metadata = ref([]);

function updateValue(newValue, parameterName) {
  if (parameterName === SliderName1.value) {
    Slider1.value = newValue;
  } else if (parameterName === SliderName2.value) {
    Slider2.value = newValue;
  } else if (parameterName === SliderName3.value) {
    Slider3.value = newValue;
  } else if (parameterName === SliderName4.value) {
    Slider4.value = newValue;
  } else if (parameterName === SliderName5.value) {
    Slider5.value = newValue;
  } else if (parameterName === SliderName6.value) {
    Slider6.value = newValue;
  }
}

function updateDropdown(newValue) {
  console.log(`updating dropdown value to: ${newValue}`);
  selectedColorIndex.value = newValue;
}

function receiveMetedata(newValue) {
  console.log(newValue);
  metadata.value = newValue;
}

// a computed ref. Vue will keep track of this and update it
const computeData = computed(() => {
  data = {
    [SliderName1.value]: Number(Slider1.value),
    [SliderName2.value]: Number(Slider2.value),
    [SliderName3.value]: Number(Slider3.value),
    [SliderName4.value]: Number(Slider4.value),
    [SliderName5.value]: Number(Slider5.value),
    [SliderName6.value]: Number(Slider6.value),
    [dropdownName.value]: Number(selectedColorIndex.value),
    [toggleName.value]: Number(runToggle.value)
  };
  return data;
});
</script>

<style scoped>
.gh-inputs-container {
  display: flex;
  flex-direction: column;
  font-size: 15px;
  height: 120%;
  align-items: flex-start;
  padding: 10px;
  margin: 10px;
  border: #dbdbdb solid 2px;
}
.geometry-view {
  display: flex;
  margin: 10px;
  border: none;
  min-width: 500px;
  position: inherit;
}

.submission-title {
  text-align: left;
  padding: 1px;
}

ol li {
  padding: 1px;
  margin-left: 30px;
  font-size: 12px;
  font-family: "Roboto Mono", monospace;
}

ol {
  background: #c2c2c25d;
  border-radius: 10px;
  padding: 5px 1px;
  margin-left: 1px;
  width: 200px;
  list-style-type: circle;
}
</style>
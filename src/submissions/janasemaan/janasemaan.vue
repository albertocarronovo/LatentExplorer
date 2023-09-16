<template>
  <div class="gh-inputs-container">
    <h2 style="color: white" class="submission-title">{{ title }}</h2>
    <img :src="imageUrl" alt="sample 3d print" width="111" height="111" >
    <p style="color: white"> Transform a secret message into a piece you can place on your table or hang on a wall! </p>
    

    

    <TextInput
      v-bind:title="stringInputName"
      v-bind:val="stringInput"
      v-on:updateValue="updateString"
    />

    
    <SliderInput
      v-bind:title="firstSliderName"
      v-bind:min="25"
      v-bind:max="300"
      v-bind:step="1"
      v-bind:val="firstSlider"
      v-on:updateValue="updateValue"
    />

    <SliderInput
    v-bind:title="secondSliderName"
      v-bind:min="0.1"
      v-bind:max="5"
      v-bind:step="0.1"
      v-bind:val="secondSlider"
      v-on:updateValue="updateValue"
    />


    <DropdownSelector
      v-bind:title="dropdownName"
      v-bind:options="materialOptions"
      v-on:updateSelection="updateDropdown"
    />

    <div v-if="metadata.length > 0">
      <h2 style="color: white" class="submission-title">Considerations for your piece:</h2>
      <ol>
        <li v-for="item in metadata" :key="item.name">
          {{ item.name}}: {{ item.value }}
        </li>
      </ol>
    </div>
  </div>

  

  <div class="geometry-view">
    <GeometryView
      v-bind:data="computeData"
      v-bind:path="path"
      v-on:updateMetadata="receiveMetedata"
    />
  </div>
</template>

<script setup>
import { ref, computed } from "vue";

// Import components
import SliderInput from "./components/SliderInput.vue";
import GeometryView from "./components/GeometryView.vue";
import DropdownSelector from "./components/DropdownSelector.vue";
import TextInput from "./components/TextInput.vue";
import def from './assets/janasemaan_encoded_art.gh'


const path = def

// Define the tittle of your project
const title = ref("Generate your own encrypted art!");



// Define Grasshopper input names
const stringInputName = ref('INPUT TEXT');
const stringInput = ref ('Hello World!');


const firstSliderName = ref("SQUARE SIDE SIZE"); //must match the Input name in your GH definition!
const firstSlider = ref(25); //default slider value

const secondSliderName = ref("DEPTH CHANGE INTENSITY"); //must match the Input name in your GH definition!
const secondSlider = ref(1); //default slider value

const dropdownName = ref("MATERIAL SELECTOR");
const selectedMaterialIndex = ref(0);

//const userInputTextName = ref("YOUR TEXT HERE");
//const userInputText = ref("Hello World!");

const materialOptions = [
  { label: "Generic", value: 0 },
  { label: "PLA 3D Print", value: 1 },
  { label: "CLAY 3D Print", value: 2 },

];

const imageUrl = new URL('./assets/sample.jpg', import.meta.url)

///.............................................

let data = ref({});
let metadata = ref([]);

function updateValue(newValue, parameterName) {
  if (parameterName === firstSliderName.value) {
    firstSlider.value = newValue;
  } else if (parameterName === secondSliderName.value) {
    secondSlider.value = newValue;
  }
}

function updateDropdown(newValue) {
  console.log(`updating dropdown value to: ${newValue}`);
  selectedMaterialIndex.value = newValue;
}

function updateString(newValue) {
  console.log(`updating string value to: ${newValue}`);
  stringInput.value = newValue;
}

function receiveMetedata(newValue) {
  console.log(newValue);
  metadata.value = newValue;
}

// a computed ref. Vue will keep track of this and update it
const computeData = computed(() => {
  data = {
    
    [firstSliderName.value]: Number(firstSlider.value),
    [secondSliderName.value]: Number(secondSlider.value),
    [stringInputName.value]: String(stringInput.value),
    [dropdownName.value]: Number(selectedMaterialIndex.value),
  };
  return data ;
});

</script>

<style scoped>
.gh-inputs-container {
  display: flex;
  flex-direction: column;
  height: 80%;
  width: 30%;
  align-items: flex-start;
  padding: 15px;
  margin: 16px;
  border: hsla(340, 100%, 55%, 0.954) solid 5px;
  border-radius: 25px;
  background-color: hsla(0, 0%, 0%, 0.954);
  

}
.geometry-view {
  height: 84%;
  width: 70%;
  border-style: dotted;
  border-color: #ffb1c3;
  background-color: #be3455;
  border-width: 5px;
  border-radius: 25px;
  margin: 12px;
  min-width: 200px;
  position: inherit;
}

.submission-title {
  text-align: left;
  padding: 1px;
}

ol li {
  padding: 1px;
  margin-left: 30px;
  font-family: "Roboto Mono", monospace;
}

ol {
  background: rgb(234, 135, 158);
  border-radius: 10px;
  padding: 5px 1px;
  margin-left: 1px;
  width: 330px;
  list-style-type: square;
}
</style>
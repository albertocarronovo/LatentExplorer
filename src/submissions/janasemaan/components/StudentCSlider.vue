<template>
  <div>
    <form class="definition-input">
        <label style="color: white" class="input-title" for="range-slider">{{ title }}: {{ sliderValue }}</label>
  
        <input type="range" class="modern-range"
        v-bind:min="min" v-bind:max="max" v-bind:step="step" v-bind:val="val" 
        v-model="sliderValue" 
        v-on:mouseup="sendValueUpate"
        />
    </form>
  </div>
  
  
  </template>

<script setup>
import { ref } from "vue";
// Define properties that you will be able to access from parent component. 
// Those properties will be binded from parent to child. 
// Available JavaScript types: https://developer.mozilla.org/en-US/docs/Web/JavaScript/Data_structures
const props = defineProps(['title', 'min', 'max', 'step', 'val'])

const titlec = ref(props.title)
// Define events that will be accessible from parent component
const emits = defineEmits(['updateValue'])
var sliderValue = ref(props.val)

function sendValueUpate()
{
  emits('updateValue', sliderValue.value, titlec.value)
}




</script>

<style scoped>
.modern-range {
  -webkit-appearance: none;
  width: 100%;
  background: #f099b5;
  height: 17px;
  border-radius: 15px;

  margin: 10px 0px;
}

.modern-range::-webkit-slider-thumb {
  -webkit-appearance: none;
  height: 30px;
  width: 15px;
  border-radius: 5px;
  background-color: black;
  cursor: pointer;
}
</style>
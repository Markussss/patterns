<script setup>
//@ts-check
import { computed, nextTick, ref, watch } from 'vue';


const pattern = ref([
  // seven vibrant hex colors that are not red, green, or blue:
  '#FFA500', // orange
  '#9400D3', // violet
  '#FFFF00', // yellow
  '#00FF00', // lime
  '#00FFFF', // aqua
  '#FF00FF', // fuchsia
  '#FF4500', // orangered



]);

const recalculate = ref(false);

const colorPicker = ref();
const rows = ref(50); // we have to scale columns and the square size to fill the screen perfectly, based only on the number of rows we want
const cols = computed(() => recalculate.value ? 0 : Math.floor(rows.value * (window.innerWidth / window.innerHeight)));
const squareHeight = computed(() => recalculate.value ? 0 : window.innerHeight / rows.value);
const squareWidth = computed(() => recalculate.value ? 0 : window.innerWidth / cols.value);

window.addEventListener('resize', () => {
  recalculate.value = true;
  nextTick(() => recalculate.value = false);
});

const squares = computed(() => {
  const result = [];
  for (let i = 0; i < cols.value * rows.value; i++) {
    result.push(pattern.value[i % pattern.value.length]);
  }
  return result;
});

const switchPatternColorFor = (index) => {
  colorPicker.value[index % pattern.value.length].click();
};

const setPatternLength = () => {
  const patternLength = prompt('Enter a valid number');
  if (patternLength) {
    pattern.value.length = patternLength;
  }
};


</script>
<template>
<div class="wrapper">
  <div
    @click.left="switchPatternColorFor(index)"
    @click.right.prevent="setPatternLength"
    v-for="(color, index) in squares"
    :style="{background: color}"
    class="square"
  />
</div>
<input v-for="(color, index) in pattern" type="color" ref="colorPicker" v-model="pattern[index]" class="hidden" />
</template>

<style>
* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
  overflow: hidden;
}
.wrapper {
  display: grid;
  grid-template-columns: repeat(v-bind(cols), 1fr);
}
.square {
  width: v-bind(squareWidth + 'px');
  height: v-bind(squareHeight + 'px');
}
.square:first-child
.hidden {
  display: none;
}
</style>

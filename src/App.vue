<script setup>
//@ts-check
import { computed, nextTick, onMounted, ref } from 'vue';
import JSColor from '@eastdesire/jscolor';

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

const colorPicker = ref();
onMounted(() => {
  // @ts-ignore
  colorPicker.value = new JSColor('#colorInput', {
    format: 'hex',
    onFineChange: function () {
      pattern.value[editingPatternIndex.value] = colorPicker.value.toString();
      console.log({
        editingPatternIndex: editingPatternIndex.value,
        color: colorPicker.value.toString(),
        pattern: pattern.value,
      })
    },
    onChange: function () {
      colorPicker.value.hide();
      pattern.value[editingPatternIndex.value] = colorPicker.value.toString();
    },
  });
});

const recalculate = ref(false);

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

const colorInput = ref();
const editingPatternIndex = ref(-1);
const switchPatternColorFor = (index) => {
  const fixedIndex = index % pattern.value.length;
  editingPatternIndex.value = fixedIndex;
  colorInput.value.value = pattern.value[fixedIndex];
  colorPicker.value.fromString(pattern.value[fixedIndex]);
  colorPicker.value.show();
};

const setPatternLength = () => {
  const patternLength = prompt('Velg hvor mange farger du vil ha i mønsteret:', pattern.value.length);
  if (patternLength) {
    pattern.value.length = patternLength;
  }
};


</script>
<template>
<input ref="colorInput" id="colorInput" style="display: none !important;">
<div class="wrapper" @click.right.prevent="setPatternLength">
  <div
    v-for="(color, index) in squares"
    @click.left="switchPatternColorFor(index)"
    :style="{background: color}"
    class="square"
  />
</div>
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
  display: none !important;
}
</style>

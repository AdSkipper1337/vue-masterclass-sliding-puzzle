<script setup>
import { ref, computed } from "vue";
const props = defineProps({
  positionX: Number,
  positionY: Number,
  imageX: Number,
  imageY: Number,
  openPosition: Object,
});
const emit = defineEmits(["move"]);
// How many tiles there are in both directions - for now it is 10 x 10
const tiling = ref(10);
const nextToOpen = computed(
  () =>
    Math.abs(props.positionX - props.openPosition.x) +
      Math.abs(props.positionY - props.openPosition.y) ===
    1
);
function move() {
  if (nextToOpen.value) emit("move");
}
</script>

<template>
  <div
    :class="{
      'sliding-puzzle-picture-tile': true,
      'sliding-puzzle-picture-tile-movable': nextToOpen,
    }"
    @click="move"
  ></div>
</template>

<style scoped>
.sliding-puzzle-picture-tile {
  position: absolute;
  /* positions the tile on the screen: normalized by the scale of the whole image */
  left: calc(v-bind(positionX) * 100% / v-bind(tiling));
  top: calc(v-bind(positionY) * 100% / v-bind(tiling));
  /* we are sizing down the tiles to be fractions of the whole */
  width: calc(100% / v-bind(tiling));
  height: calc(100% / v-bind(tiling));
  background-image: url("/gnosticism.png");
  background-size: calc(100% * v-bind(tiling));
  /* now we are offsetting the texture by the position x and y again */
  background-position-x: calc(
    v-bind(imageX) * calc(100% / (v-bind(tiling) - 1))
  );
  background-position-y: calc(
    v-bind(imageY) * calc(100% / (v-bind(tiling) - 1))
  );
  background-repeat: no-repeat;
  /* the rest are just styling and preventing unnesscary reactions */
  background-color: black;
  border-color: rgba(255, 255, 255, 0.6);
  border-width: 1px;
  border-style: solid;
  box-sizing: border-box;
  user-drag: none;
  -webkit-user-drag: none;
  user-select: none;
  -moz-user-select: none;
  -webkit-user-select: none;
  -ms-user-select: none;
  touch-action: none;
  /* finally we move the tile onto the front and add a scale up animation on hover */
  z-index: 2;
  transition: transform 0.2s, top 0.2s, left 0.2s;
}

.sliding-puzzle-picture-tile-movable {
  transform: scale(1);
  cursor: pointer;
}

.sliding-puzzle-picture-tile-movable:hover {
  z-index: 3;
  transform: scale(1.2);
}
</style>

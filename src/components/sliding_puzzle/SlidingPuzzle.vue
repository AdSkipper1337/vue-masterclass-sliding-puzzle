<script setup>
import SlidingPuzzleTile from "./SlidingPuzzleTile.vue";
import { ref } from "vue";

const xSize = 10;
const ySize = 10;
const openPosition = ref({ x: xSize - 1, y: ySize - 1 });

const grid = ref();
function createGrid() {
  const res = [];
  for (let y = 0; y < ySize; ++y) {
    const row = [];
    for (let x = 0; x < xSize; ++x) {
      if (y === openPosition.value.y && x === openPosition.value.x) continue;
      row.push({
        positionX: x,
        positionY: y,
        imageX: x,
        imageY: y,
      });
    }
    res.push(row);
  }
  grid.value = res;
}
createGrid();

let lastX = null;
let lastY = null;
function move(tile) {
  lastX = openPosition.value.x;
  lastY = openPosition.value.y;
  const oldX = tile.positionX;
  const oldY = tile.positionY;
  tile.positionX = openPosition.value.x;
  tile.positionY = openPosition.value.y;
  openPosition.value.x = oldX;
  openPosition.value.y = oldY;
}

function getRandomInt(max) {
  return Math.floor(Math.random() * max);
}

function mixStep() {
  const possibleMoves = [];
  for (let y = 0; y < ySize; ++y) {
    for (let x = 0; x < xSize; ++x) {
      const el = grid.value[y][x];
      if (!el) continue;
      if (el.positionX === lastX) continue;
      if (el.positionY === lastY) continue;
      const open =
        Math.abs(el.positionX - openPosition.value.x) +
          Math.abs(el.positionY - openPosition.value.y) ===
        1;
      if (open) possibleMoves.push(el);
    }
  }
  const randomMove = getRandomInt(possibleMoves.length);
  move(possibleMoves[randomMove]);
}

let timeout = null;
function mix(mixing) {
  if (mixing === undefined) mixing = 500;
  if (mixing === 0) return;
  mixStep();
  clearTimeout(timeout);
  timeout = setTimeout(() => {
    mix(mixing - 1);
  }, 20);
}

function solve() {
  clearTimeout(timeout);
  grid.value.forEach((row) =>
    row.forEach((element) => {
      element.positionX = element.imageX;
      element.positionY = element.imageY;
    })
  );
  openPosition.value.x = xSize - 1;
  openPosition.value.y = ySize - 1;
}
</script>

<template>
  <div class="sliding-puzzle-wrapper">
    <div class="sliding-puzzle-background">
      <template v-for="(row, indexY) in grid">
        <template v-for="(tile, indexX) in row" :key="indexY + '/' + indexX">
          <SlidingPuzzleTile
            :positionX="tile.positionX"
            :positionY="tile.positionY"
            :imageX="tile.imageX"
            :imageY="tile.imageY"
            :openPosition="openPosition"
            @move="move(tile)"
          ></SlidingPuzzleTile>
        </template>
      </template>
      <img
        src="/gnosticism.png"
        width="100%"
        class="sliding-puzzle-faded-image"
      />
    </div>
    <div class="sliding-puzzle-button-area">
      <div class="sliding-puzzle-button" @click="mix()">MIX</div>
      <div class="sliding-puzzle-button" @click="solve()">SOLVE</div>
    </div>
  </div>
</template>

<style scoped>
.sliding-puzzle-wrapper {
  margin: 12px;
}

.sliding-puzzle-button-area {
  width: 100%;
  justify-content: space-around;
  display: flex;
}

.sliding-puzzle-button {
  padding: 12px;
  background-color: white;
  color: black;
  box-sizing: border-box;
  border-radius: 8px;
  box-shadow: 0px 3px 12px 1px rgba(0, 0, 0, 1);
  cursor: pointer;
  font-family: Arial, Helvetica, sans-serif;
  font-size: x-large;
  min-width: 30%;
  text-align: center;
  transition: transform 0.2s, min-width 0.3s, background-color 0.5s, color 0.5s;
}

.sliding-puzzle-button:hover {
  background-color: darkblue;
  color: white;
  min-width: 50%;
  transform: scale(1.15);
}

.sliding-puzzle-background {
  margin: 12px;
  position: relative;
  box-sizing: border-box;
  box-shadow: 0px 3px 12px 1px rgba(0, 0, 0, 1);
}

.sliding-puzzle-faded-image {
  opacity: 0.1;
  user-drag: none;
  -webkit-user-drag: none;
  user-select: none;
  -moz-user-select: none;
  -webkit-user-select: none;
  -ms-user-select: none;
  touch-action: none;
  pointer-events: none;
}
</style>

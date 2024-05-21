<script setup>
import { onMounted, ref, watch } from 'vue';
import { useDatabase } from './DBConnection.vue';
import { useRoute } from 'vue-router';

const { isLoading, items, search, results, getItems } = useDatabase();
const route = useRoute();

const chosenItemId = route.params.id;
const mapArray = [];
let canvasMap = ref();
let ctx = ref();
const rows = 29;
const columns = 50;
const tileSize = 24;

getItems();

// set all fields of 2d array to 0
for (let i = 0; i < rows; i++) {
  mapArray[i] = [];
  for (let j = 0; j < columns; j++) {
    mapArray[i][j] = 0;
  }
}

// fill multiple fields of 2d array
function fillArray(direction, collumn, row, amount){
    if(direction == "x"){
        for(let i = collumn; i < collumn + amount; i++){
        mapArray[row-1][i-1] = 1;
        }
    }
    if(direction == "y"){
        for(let i = row; i < row + amount; i++){
        mapArray[i-1][collumn-1] = 1;
        }
    }
}

// match ids from url and item list
watch(isLoading, () => {
    for(let i=0; i<items.value.length; i++){
        if(items.value[i].id == chosenItemId){
            fillArray("x", items.value[i].mapCol, items.value[i].mapRow, 1);
        }
        drawMap();
    }
});

// draw on 2d canvas
const drawMap = () => {
    let img = document.getElementById("itemLocationImg");
    ctx = canvasMap.value.getContext("2d");
    ctx.clearRect(0, 0, canvasMap.width, canvasMap.height);
    for(let y=0; y < mapArray.length; y++){
        for(let x=0; x < mapArray[0].length; x++){
            if([mapArray[y][x]][0] == 1){
                ctx.drawImage(img, x * tileSize, y * tileSize, tileSize, tileSize);
            }
        }
    }
}





</script>

<template>
<h1>THIS IS MAP</h1>
<div class="canvas-wrap">
    <img id="itemLocationImg" src="../assets/redbutton.png" />
    <img class="map-img" src="../assets/map-img.jpg" />
    <canvas ref="canvasMap" width=1200 height=700 />
</div>
</template>

<style>
img{
    position: absolute;
    top: 0;
    left: 0;
    width: 1200px;
    height: 698px;
}
.map-img{
    position: absolute;
    top: 0;
    left: 0;
    width: 1200px;
    height: 698px;
}
canvas{
    position: absolute;
    top: 0;
    left: 0;
}
.canvas-wrap{
    position: relative;
    top: 0;
    left: 0;
}
</style>

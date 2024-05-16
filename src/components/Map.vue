<script setup>
import { onMounted, ref } from 'vue';
import { useDatabase } from './DBConnection.vue';

const { isLoading, items, search, results, getItems } = useDatabase()
const mapArray = [];
let canvasMap = ref();
let ctx = ref();
const rows = 29;
const columns = 50;
const tileSize = 24;

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
getItems()


fillArray("x", 2, 13, 1); // Alpakka silk
fillArray("x", 5, 19, 1); // Air
fillArray("x", 8, 19, 1); // Kos
fillArray("x", 5, 23, 1); // Cashmere premium
fillArray("x", 18, 17, 1); // Organic trio
fillArray("x", 19, 22, 1); // Peer gynt
fillArray("x", 19, 26, 1); // Lima
fillArray("x", 21, 28, 1); // Hannah
fillArray("x", 21, 25, 1); // Kid-silk
fillArray("x", 21, 23, 1); // Angel




// console.log(mapArray);


const drawMap = () => {
    ctx = canvasMap.value.getContext("2d");
    for(let y=0; y < mapArray.length; y++){
        for(let x=0; x < mapArray[0].length; x++){
            ctx.fillStyle = ["red", "blue"][mapArray[y][x]]
            ctx.fillRect(x * tileSize, y * tileSize, tileSize, tileSize)
        }
    }
}
onMounted(drawMap);

</script>

<template>
<h1>THIS IS MAP</h1>
<div class="canvas-wrap">
    <!-- <img src="../assets/map-img.jpg" /> -->
    <canvas ref="canvasMap" width=1200 height=700 />
</div>
<ul>
      <!-- iterates data from array and spits them out in a li -->
      <li v-for="item in items" :key="item.id">
        <!-- checks if item has been clicked and has correct id -> then spit out item description section -->
        {{ item.mapRow }}
        {{ item.mapCol }}

      </li>
    </ul>
</template>
<style>
img{
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
    opacity: 33%;
}
.canvas-wrap{
    position: relative;
    top: 0;
    left: 0;
}
</style>

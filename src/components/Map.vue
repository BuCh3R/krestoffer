<script setup>
import { RouterView } from 'vue-router';
import { ref, watch } from 'vue';
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
function fillArray(direction, collumn, row, amount, value){
    if(direction == "x"){
        for(let i = collumn; i < collumn + amount; i++){
        mapArray[row-1][i-1] = value;
        }
    }
    if(direction == "y"){
        for(let i = row; i < row + amount; i++){
        mapArray[i-1][collumn-1] = value;
        }
    }
}

// hard coordinates for route to item
function markArrayRoute(id){
    if(id == 0){
        // drops lima garn
        fillArray("y", 29, 15, 11, 2);
        fillArray("x", 14, 15, 15, 2);
        fillArray("y", 14, 15, 5, 2);
        fillArray("x", 14, 20, 4, 2);
        fillArray("y", 17, 20, 7, 2);
    }else if(id == 1){
        // hannah garn fra permin
        fillArray("x", 23, 25, 7, 2);
        fillArray("y", 23, 25, 4, 2)
    }else if(id == 2){
        // drops air garn
        fillArray("y", 29, 15, 11, 2);
        fillArray("x", 14, 15, 15, 2);
        fillArray("y", 14, 15, 7, 2);
        fillArray("x", 6, 21, 9, 2);
    }else if(id == 3){
        // cashmere premium garn fra lang yarns
        fillArray("y", 29, 15, 11, 2);
        fillArray("x", 14, 15, 15, 2);
        fillArray("y", 14, 15, 7, 2);
        fillArray("x", 6, 21, 9, 2);
    }else if(id == 4){
        // alpakka silk garn fra sandnes garn
        fillArray("y", 29, 15, 11, 2);
        fillArray("x", 14, 15, 15, 2);
        fillArray("y", 14, 13, 2, 2);
        fillArray("x", 4, 13, 11, 2);
    }else if(id == 5){
        // organic trio garn fra hjertegarn
        fillArray("y", 29, 15, 11, 2);
        fillArray("x", 19, 15, 10, 2);
    }else if(id == 6){
        // peer gynt garn fra sandnes garn
        fillArray("y", 29, 15, 11, 2);
        fillArray("x", 14, 15, 15, 2);
        fillArray("y", 14, 15, 7, 2);
        fillArray("x", 14, 21, 5, 2);
        fillArray("y", 18, 21, 2, 2);
    }else if(id == 7){
        // drops kid silk garn
        fillArray("x", 23, 25, 7, 2);
    }else if(id == 8){
        // kos garn fra sandnes garn
        fillArray("y", 29, 15, 11, 2);
        fillArray("x", 14, 15, 15, 2);
        fillArray("y", 14, 15, 7, 2);
        fillArray("x", 9, 21, 6, 2);
    }else if(id == 9){
        // angel mohair garn fra permin
        fillArray("y", 29, 23, 3, 2);
        fillArray("x", 23, 23, 7, 2);
    }
}


// match ids from url and item list
watch(isLoading, () => {
    for(let i=0; i<items.value.length; i++){
        if(items.value[i].id == chosenItemId){
            fillArray("x", items.value[i].mapCol, items.value[i].mapRow, 1, 1);
            markArrayRoute(chosenItemId);
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
            // draws img where value of 2d array index = 1
            if([mapArray[y][x]][0] == 1){
                ctx.drawImage(img, x * tileSize - (tileSize / 8), y * tileSize - (tileSize / 8), tileSize*1.2, tileSize*1.2);
            }
            // draw dotted line where value of 2d array index = 2
            if([mapArray[y][x]][0] == 2){
                ctx.fillStyle = ["", "", "#18AA04"][mapArray[y][x]]
                ctx.fillRect(x * tileSize, y * tileSize, tileSize/2, tileSize/2)
            }
        }
    }
}
</script>

<template>
    <p v-if="isLoading"></p>
    <div v-else>
        <div v-for="item in items" :key="item.id">
            <div v-if="item.id == route.params.id">
                <div class="header-shadow">
                    <div class="map-flex-container">
                        <div class="go-back">
                            <RouterLink class="inner-flex-wrap" to="/">
                                <div>
                                    <img class="back-arrow" src="../../public/black-arrow.png" alt="black back arrow icon">
                                </div>
                                <div>
                                    <p>Tilbage</p>
                                </div>
                            </RouterLink>
                        </div>
                        <div>
                            <div>
                                <img class="red-button" src="../assets/redbutton.png" alt="">
                            </div>
                            <div>
                                <h2>{{ item.name }}</h2>
                            </div>
                        </div>
                        <div>
                            <div>
                                <img class="blue-button" src="../../public/bluebutton.png" alt="blue button (recolor of krestoffer logo)">
                            </div>
                            <div>
                                <h2>Du er her</h2>
                            </div>
                        </div>
                    </div>
                </div>
            </div>
        </div>
    </div>
<div class="canvas-wrap">
    <img id="itemLocationImg" src="../assets/redbutton.png" />
    <img class="map-img" src="../../public/map-img.jpg" />
    <canvas ref="canvasMap" width=1200 height=700 />
</div>
</template>
<style>

</style>

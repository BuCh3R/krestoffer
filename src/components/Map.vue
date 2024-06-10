<script setup>
import { RouterView } from 'vue-router';
import { ref, watch } from 'vue';
import { useDatabase } from './DBConnection.vue';
import { useRoute } from 'vue-router';

const { isLoading, items, search, results, getItems } = useDatabase();
const route = useRoute();

getItems();
const chosenItemId = route.params.id;

let canvasMap = ref();
let ctx = ref();

const mapArray = ref([]);
const rows = 29;
const columns = 50;
const tileSize = 24;

// set all fields of 2d array to 0
for (let i = 0; i < rows; i++) {
    mapArray.value[i] = [];
  for (let j = 0; j < columns; j++) {
    mapArray.value[i][j] = "X";
  }
}

// fill multiple fields of 2d array
function fillArray(direction, collumn, row, amount, value){
    if(direction == "x"){
        for(let i = collumn; i < collumn + amount; i++){
            mapArray.value[row-1][i-1] = value;
        }
    }
    if(direction == "y"){
        for(let i = row; i < row + amount; i++){
            mapArray.value[i-1][collumn-1] = value;
        }
    }
}




function pathing(){

    // standard route
    fillArray("x", 3, 12, 14, " ");
    fillArray("x", 3, 16, 11, " ");
    fillArray("x", 15, 15, 15, " ");
    fillArray("x", 2, 21, 16, " ");
    fillArray("x", 2, 20, 10, " ");
    fillArray("x", 2, 22, 8, " ");

    fillArray("y", 3, 12, 5, " ");
    fillArray("y", 14, 12, 10, " ");
    fillArray("y", 17, 19, 10, " ");
    fillArray("y", 29, 15, 11, " ");
    fillArray("y", 23, 15, 14, " ");

    // path from standard route to items
    fillArray("x", 14, 19, 4, " ");
    fillArray("x", 23, 25, 6, " ");
    fillArray("x", 17, 16, 5, " ");
    fillArray("y", 22, 17, 12, " ");
    fillArray("y", 18, 19, 10, " ");
    fillArray("y", 2, 12, 5, " ");
}
pathing();

const solveMaze = () => {
    let mapArrayCopy = JSON.parse(JSON.stringify(mapArray.value));
    let start = findStartEnd(mapArrayCopy, "S");
    let end = findStartEnd(mapArrayCopy, "E");
    
    fillMaze(mapArrayCopy, start);
    followSolution(mapArrayCopy, end);
    mapArray.value = mapArrayCopy;
};

// track backwards from end to start, mark route with "O"
const followSolution = (maze, end) => {
    let height = maze.length;
    let width = maze[0].length;
    let cur_step = parseInt(maze[end[0]][end[1]]);
    
    maze[end[0]][end[1]] = "O";
    
    // while start not reached
    while (cur_step > 1) {
        let y = end[0];
        let x = end[1];
        let get_out = false;
        
        // check neighbors (up, right, down, left)
        for (let ny = -1; ny <= 1; ny++) {
            for (let nx = -1; nx <= 1; nx++) {
                if (Math.abs(ny) === Math.abs(nx) || y + ny < 0 || y + ny >= height || x + nx < 0 || x + nx >= width)
                    continue;
                
                // if neighbor is 1 less mark index for route = "O"
                if (maze[y + ny][x + nx] === (cur_step - 1).toString()) {
                    end = [y + ny, x + nx];
                    cur_step = parseInt(maze[end[0]][end[1]]);
                    maze[y + ny][x + nx] = "O";
                    get_out = true;
                    break;
                }
            }
            if (get_out) break;
        }
    }
};

// loops 2d array to find val, 
const findStartEnd = (maze, val) => {
    let height = maze.length;
    let width = maze[0].length;
    
    // loops height times width of 2d array
    for (let y = 0; y < height; y++) {
        for (let x = 0; x < width; x++) {
            if (maze[y][x] === val) {

                // check neighbors (up, right, down, left)
                for (let ny = -1; ny <= 1; ny++) {
                    for (let nx = -1; nx <= 1; nx++) {
                        if (Math.abs(ny) === Math.abs(nx) || y + ny < 0 || y + ny >= height || x + nx < 0 || x + nx >= width)
                            continue;

                        // if up, right, down or left = " " return coordinates
                        if (maze[y + ny][x + nx] === " ")
                            return [y + ny, x + nx];
                    }
                }
            }
        }
    }
};

//fill 2d array 1 node at a time increment by 1
const fillMaze = (maze, start) => {
    maze[start[0]][start[1]] = "1";
    let height = maze.length;
    let width = maze[0].length;
    let queue = start.slice();
    
    // while no empty path
    while (queue.length !== 0) {
        let y = queue.shift();
        let x = queue.shift();
        let cur_val = parseInt(maze[y][x]);
        
        // check neighbors (up, right, down, left)
        for (let ny = -1; ny <= 1; ny++) {
            for (let nx = -1; nx <= 1; nx++) {
                if (Math.abs(ny) === Math.abs(nx) || y + ny < 0 || y + ny >= height || x + nx < 0 || x + nx >= width)
                    continue;

                // path available then set to +1
                if (maze[y + ny][x + nx] === " ") {
                    maze[y + ny][x + nx] = (cur_val + 1).toString();
                    queue.push(y + ny);
                    queue.push(x + nx);
                }
            }
        }
    }
};

// match ids from url and item list
watch(isLoading, () => {
    for (let i=0; i<items.value.length; i++) {
        if (items.value[i].id == chosenItemId) {
            
            // set start and end coordinates of chosen item
            fillArray("x", 29, 26, 1, "S");
            fillArray("x", items.value[i].mapCol, items.value[i].mapRow, 1, "E");
            solveMaze();
            drawMap();
        }
    }
});

// draw on 2d canvas
const drawMap = () => {
    let img = document.getElementById("itemLocationImg");
    ctx = canvasMap.value.getContext("2d");
    ctx.clearRect(0, 0, canvasMap.width, canvasMap.height);
    for(let y=0; y < mapArray.value.length; y++){
        for(let x=0; x < mapArray.value[0].length; x++){

            // draws img where value of 2d array index = 1
            if([mapArray.value[y][x]][0] == "E"){
                ctx.drawImage(img, x * tileSize - (tileSize / 8), y * tileSize - (tileSize / 8), tileSize*1.2, tileSize*1.2);
            }

            // draw dotted line where value of 2d array index = "O" 
            if([mapArray.value[y][x]][0] == "O"){
                ctx.fillStyle = "#18AA04";
                ctx.fillRect(x * tileSize, y * tileSize, tileSize / 2, tileSize / 2)
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

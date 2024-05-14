<script setup>
import { ref } from 'vue';


</script>

<template>
<h1>THIS IS MAP</h1>
<div class="canvas-wrap">
    <img src="../assets/map-img.jpg" />
<canvas id="canvasMap" />
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
canvas{
    position: absolute;
    top: 0;
    left: 0;
    width: 1200px;
    height: 698px;
}
.canvas-wrap{
    position: relative;
    top: 0;
    left: 0;
}
</style>
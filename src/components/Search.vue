<script setup>
import { computed, ref, watch } from 'vue';
import { useDatabase } from './DBConnection.vue';
import { RouterView } from 'vue-router';

const { isLoading, items, search, results, getItems } = useDatabase();

let logoBox = ref();
let imgAnimation = ref();
let textAnimation = ref();
let searchContainer = ref();
let searchField = ref();
let hand = ref();

// frontpage animation on input click
function animateSearch(){
  searchContainer.value.classList.add("moveUp");
  searchField.value.classList.add("expand");
  logoBox.value.classList.add("moveLeft");
  imgAnimation.value.classList.add("flex-20");
  textAnimation.value.classList.add("flex-80");
  hand.value.classList.add("none");
}

// call getItems on search change
watch(search, () => {
  getItems();
});

</script>
<template>
  <div class="top-wrap">
    <div ref="logoBox" class="searchBodyBox">
      <div ref="imgAnimation" class="headerBox">
        <img class="imgLogo" src="../assets/redbutton.png" alt="logo, a red button">
      </div>
      <div ref="textAnimation" class="headerBox-text">
        <h2 class="frontpage-headtext">KreStoffer</h2>
      </div>
    </div>
  </div>
  <div ref="hand" class="hand-container">
    <div>
      <img src="../../public/hand.jpg">
    </div>
    <div class="hand-click">
      <img src="../../public/hand-click.jpg">
    </div>
  </div>
  <div ref="searchContainer" class="bodyBox">
    <div class="searchBox">
      <input v-on:click="animateSearch" ref="searchField" class="inputfield" type="text" v-model="search" placeholder="Søg efter vare her...">
    </div>
    <p v-if="isLoading"></p>
    <div v-else>
      <div v-for="item in items" :key="item.id">
        <div>
          <div>
            <img v-bind:src="item.itemImage" />
          </div>
          <div class="itemBox">
            <div class="imgBox"><img class="itemImg" v-bind:src="item.img"></div>
            <div class="textBox">
              <div class="itemName"> {{ item.name }} </div><br>
              <p>Pris</p>
              <div class="itemPrice" v-if="!item.isDiscount"> {{ item.price }} kr</div>
              <div class="itemPrice" v-else> <del class="itemDiscount">{{ item.price }} kr</del> {{ item.discountPrice }} kr</div><br><br>
              <div><RouterLink :to="{ path: `/Map/${item.id}` }"><button class="mapButton">Se på kort</button></RouterLink></div>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>
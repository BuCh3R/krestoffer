<script setup>
import { ref, watch } from 'vue';
import { RouterView } from 'vue-router';

const isLoading = ref(true);
const items = ref([]);
const search = ref("");
let results = ref([]);
let theid = ref();
let isItemChosen = ref(false);

// get api with included search attribute
const getItems = () => {
  fetch(`https://krestoffer-254e8-default-rtdb.europe-west1.firebasedatabase.app/items.json`, {
    method: 'GET'
  })
    .then((rawData) => {
      return rawData.json();
    })
    .then((data) => {
      items.value = data.items
      results = items.value.filter(obj => {
        return obj.name.toLowerCase().includes(search.value.toLowerCase());
      })
      items.value = results;
    })
    .finally(() => {
      isLoading.value = false;
    });

}

// call getItems on search change
watch(search, () => {
  isItemChosen.value = false;
  getItems();
});

// gets id from clicked item and stores it
const chosenItem = (id) => {
  getItems();
  isItemChosen.value = true;
  return theid = id;
}
</script>

<template>
  <div class="bodyBox">
  <div class="headerBox">
  <input class="inputfield" type="text" v-model="search" placeholder="Søg efter vare her..."></div>
  <p v-if="isLoading"></p>
  <div v-else>
      <!-- iterates data from array and spits them out in a li -->
      <div v-for="item in items" :key="item.id">
        <!-- checks if item has been clicked and has correct id -> then spit out item description section -->
        <div v-if="isItemChosen && theid==item.id">
            <!-- insert html here - for when an item is clicked -->
        </div>
        <!-- create html elements for each loop iteration -->
        <div v-if="!isItemChosen">
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
            <div><RouterLink to="/MapView"><button class="mapButton">Se på kort</button></RouterLink></div>
          </div>
          </div>
        </div>
      </div>
    
  </div>
</div>
</template>
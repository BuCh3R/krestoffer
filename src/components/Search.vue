<script setup>
import { ref, watch } from 'vue';

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
  <input type="text" v-model="search"></input>
  <p v-if="isLoading"></p>
  <div v-else>
    <ul>
      <!-- iterates data from array and spits them out in a li -->
      <li v-for="item in items" :key="item.id">
        <!-- checks if item has been clicked and has correct id -> then spit out item description section -->
        <div v-if="isItemChosen && theid==item.id">
            <!-- insert html here - for when an item is clicked -->
        </div>
        <!-- create html elements for each loop iteration -->
        <div v-if="!isItemChosen" v-on:click="chosenItem(item.id)">
          <div>
            <img v-bind:src="item.itemImage" />
          </div>
          <div>
            <div> {{ item.name }} </div>
            <div v-if="!item.isDiscount"> {{ item.price }} kr</div>
            <div v-else> <del>{{ item.price }} kr</del> {{ item.discountPrice }} kr</div>
            <img v-bind:src="item.img">
          </div>
        </div>
      </li>
    </ul>
  </div>
</template>

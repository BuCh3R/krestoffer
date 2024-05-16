<script setup>
import { ref, watch } from 'vue';
import { useDatabase } from './DBConnection.vue';

let theid = ref();
let isItemChosen = ref(false);

const { isLoading, items, search, results, getItems } = useDatabase()




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
            <div> {{ item.name }} </div>
            <div v-if="!item.isDiscount"> {{ item.price }} kr</div>
            <div v-else> <del>{{ item.price }} kr</del> {{ item.discountPrice }} kr</div>
            <!-- <img v-bind:src="item.img"> -->
          </div>
        </div>
      </li>
    </ul>
  </div>
</template>

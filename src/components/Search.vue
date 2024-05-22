<script setup>
import { ref, watch } from 'vue';
import { useDatabase } from './DBConnection.vue';
import { RouterView } from 'vue-router';

const { isLoading, items, search, results, getItems } = useDatabase();



// call getItems on search change
watch(search, () => {
  getItems();
});
</script>

<template>
  <div class="bodyBox">
  <div class="headerBox">
  <input class="inputfield" type="text" v-model="search" placeholder="Søg efter vare her..."></div>
  <p v-if="isLoading"></p>
  <div v-else>
      <!-- iterates data from array and spits them out in a li -->
      <div v-for="item in items" :key="item.id">
        <!-- create html elements for each loop iteration -->
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
          <!-- <RouterLink :to="{ path: `/Map/${item.id}` }">
            <div>
              <div> {{ item.name }} </div>
              <div v-if="!item.isDiscount"> {{ item.price }} kr</div>
              <div v-else> <del>{{ item.price }} kr</del> {{ item.discountPrice }} kr</div>
            </div>
          </RouterLink> -->

</template>
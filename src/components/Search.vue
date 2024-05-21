<script setup>
import { ref, watch } from 'vue';
import { useDatabase } from './DBConnection.vue';

const { isLoading, items, search, results, getItems } = useDatabase();

// call getItems on search change
watch(search, () => {
  getItems();
});
</script>

<template>
  <input type="text" v-model="search"></input>
  <p v-if="isLoading"></p>
  <div v-else>
    <ul>
      <!-- iterates data from array and spits them out in a li -->
      <li v-for="item in items" :key="item.id">
        <!-- create html elements for each loop iteration -->
          <RouterLink :to="{ path: `/Map/${item.id}` }">
            <div>
              <div> {{ item.name }} </div>
              <div v-if="!item.isDiscount"> {{ item.price }} kr</div>
              <div v-else> <del>{{ item.price }} kr</del> {{ item.discountPrice }} kr</div>
            </div>
          </RouterLink>
      </li>
    </ul>
  </div>
</template>

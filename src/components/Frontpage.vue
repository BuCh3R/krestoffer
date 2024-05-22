<script setup>
import { ref, watch } from 'vue';
import { RouterView } from 'vue-router';
</script>

<template>
  <div class="searchBodyBox">
  <div class="headerBox"><img class="imgLogo" src="/src/assets/logo.png" alt="logo, a red button and the name KreStoffer at the end"></div>
          <div class="searchBox">
            <RouterLink to="/SearchView"><button class="searchButton">Søg efter vare her...<img class="imgSearch" src="/src/assets/search.png" alt="search"></button></RouterLink></div>
        </div>
        
      
</template>
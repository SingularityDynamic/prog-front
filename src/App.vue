<script setup>
import { ref } from 'vue'
import SalesReporting from './components/SalesReporting.vue'
import SalesHeading from './components/SalesHeading.vue'

</script>

<template>
  <menu>
    <div class="menuHeader">
      <h3>Sales this session</h3>
    </div>
  </menu>
  <header>
    <div class="wrapper">
      <SalesHeading />
    </div>
  </header>

  <main>
    <div class="wrapper">
      <SalesReporting />
    </div>
  </main>
</template>



<style scoped>
header {
  line-height: 1.5;
}

@media (min-width: 1024px) {
  header {
    grid-column: 2 / 7;
    display: grid;
    grid-template-rows: subgrid;
    grid-template-columns: subgrid;
  }

  header .wrapper {
    grid-row: 1;
    grid-column: 1 / 7;
    display: grid;
    grid-template-rows: 1fr 2fr;
    grid-template-columns: subgrid;
  }

  main {
    grid-row: 2 / 5;
    grid-column: 1 / 6;
    display: grid;
    grid-template-rows: subgrid;
    grid-template-columns: subgrid;
  }

  main .wrapper {
    grid-row: 1 / 4;
    grid-column: 1 / 6;
    display: grid;
    grid-template-rows: 4em repeat(9, 1fr);
    grid-template-columns: subgrid;
  }

  menu {
    grid-row: 1 / 5;
    grid-column: 1;
    background-color: var(--rim-background);
    display: grid;
    grid-template-rows: subgrid;
    grid-template-columns: subgrid;
  }

  .menuHeader {
    display: grid;
    grid-template-rows: 6fr 1fr;
  }

  .menuHeader h3 {
    grid-row: 2;
  }
}
</style>

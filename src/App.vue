<script setup>
import List from './components/clinicalTrialsList.vue'
components:{
  List
}
</script>

<template>
  <nav class="navbar navbar-expand-lg bg-success text-white fixed-top">
    <div class="container-fluid">
      <h3>Clinical Trials List</h3>
      <form class="d-flex" role="search">
        <input class="form-control me-2" type="search" placeholder="Search" aria-label="Search">
        <button class="btn text-white btn-outline-danger" type="submit">Search</button>
      </form>
    </div>
  </nav>
  <header>
<h1>noor</h1>
    
  </header>

 <List/>
</template>

<style scoped>


</style>

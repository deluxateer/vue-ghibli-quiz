<template>
  <div id="app" class="h-full">
    <!-- <div id="nav">
      <router-link to="/">Home</router-link> |
      <router-link to="/about">About</router-link>
    </div> -->
    <div class="container mx-auto h-full bg-white px-10 py-10 rounded-lg shadow-xl overflow-y-auto">
      <router-view />
    </div>
  </div>
</template>

<script>
export default {
  name: 'app'
}
</script>

<style>
@media screen and (min-width: 768px) {
  /* .container {
    display: flex;
    flex-direction: column;
    justify-content: center;
    align-items: center;
  } */
}
</style>

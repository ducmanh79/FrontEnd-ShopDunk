<template>
  <div id="app">
    <MyNavigation />
    <router-view></router-view>
    <MyFooter />
  </div>
</template>

<script>
import MyNavigation from "./components/layout/MyNavigation.vue";
import MyFooter from "./components/layout/MyFooter.vue";
export default {
  name: 'App',
  components:{
      MyNavigation,
      MyFooter
  },
  data(){
    return{
    }
  }
}
</script>

<style scoped>
#app{
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  min-height: 100%;
  height: 100vh;
}
</style>

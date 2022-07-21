<template>
  <div>
    <MySlider />
    <MyPoster :posterList="poster" />
    <div class="container">
      <div class="row">
        <h2 class="text-center">iPhone</h2>
        <div
          class="p-3 col-6 col-md-3"
          v-for="(product, index) in products"
          :key="index"
          style="cursor:pointer"
          @click="toProduct(index)"
        >
          <MyProductGrid :product="product" />
        </div>
      </div>
      <div class="row">
        <h2 class="text-center">iPad</h2>
        <div
          class="p-3 col-6 col-sm-6 col-md-3"
          v-for="(product, index) in products"
          :key="index"
          style="cursor:pointer"
          @click="toProduct(index)"
        >
          <MyProductGrid :product="product" />
        </div>
      </div>
      <div class="row text-center">
        <h4 v-rainbow>Subscribe to our latest news</h4>
        <p>Special information</p>
      </div>
      <div class="row">
        <div class="col" style="display: flex">
          <b-form-input
            class="w-75"
            placeholder="Enter your name"
          ></b-form-input>
          <button class="btn btn-primary w-25 ms-3">Subscribe</button>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import MySlider from "./MySlider.vue";
import MyPoster from "./MyPoster.vue";
import MyProductGrid from "./MyProductGrid.vue";
import BaseRequest from '../../store/BaseRequest'
// import config from '../../config'
export default {
  name: "App",
  data() {
    return {
      products: [],
      poster: [
        require("../../assets/poster/AirPods-MB.jpg"),
        require("../../assets/poster/Care-MB.jpg"),
        require("../../assets/poster/Pencil-MB.jpg"),
      ],
    };
  },
  components: {
    MySlider,
    MyPoster,
    MyProductGrid,
  },
  methods: {
    toProduct(index) {
      this.$router.push({ name: "ProductDetail", params: { id: index+1 } });
    },
    getProducts(){
      BaseRequest.get('/products').then(response => {this.products = response.data.products})
    }
  },
  mounted(){
    this.getProducts()
  }
};
</script>

<style scoped>
template {
  width: 100%;
}
#app {
  width: 100%;
}
</style>

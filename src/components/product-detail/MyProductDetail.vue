<template>
  <div id="main">
    <div class="cat-nav">
      <div class="container category-nav text-start" v-if="data">
        <p>
          Home \ Products \
          <span style="color: #0066cc">{{ data.category }}</span>
        </p>
      </div>
    </div>
    <div class="container container-main">
      <div class="row">
        <div class="col-12 col-sm-6">
          <agile v-if="data" ref="main" :options="option" class="agile-main">
            <div
              class="slide"
              v-for="(image, index) in data.images"
              :key="index"
            >
              <img :src="baseURL + image.image_path" alt="" class="slide-img" />
            </div>
          </agile>

          <agile
            v-if="data"
            ref="thumbnails"
            :slides-to-show="3"
            :options="option2"
            class="agile-nav"
          >
            <div
              v-for="(image, index) in data.images"
              :key="index"
              @click="changeSlide(index)"
              class="slide-nav"
            >
              <img :src="baseURL + image.image_path" alt="" class="slide-img" />
            </div>
          </agile>
          <div class="agile-controller">
            <button @click="handlePrev">Prev</button>
            <button @click="handleNext">Next</button>
          </div>
        </div>
        <div class="col-12 col-sm-6 info">
          <div class="container text-start product-information" v-if="data">
            <div class="row">
              <h3>{{ data.product.title }}</h3>
            </div>
            <hr />
            <div class="row">
              <h3 style="font-weight: bold">
                {{ data.product.price | priceFormat }}
              </h3>
              <div id="color-selector">
                <p v-if="selected">
                  Selected color: {{ productToBeAdd.color }}
                </p>
                <ul>
                  <li id="green" @click="assignColor('green')">
                    <span></span>
                  </li>
                  <li id="blue" @click="assignColor('blue')"><span></span></li>
                  <li id="grey" @click="assignColor('grey')"><span></span></li>
                  <li id="black" @click="assignColor('black')">
                    <span></span>
                  </li>
                  <li id="yellow" @click="assignColor('yellow')">
                    <span></span>
                  </li>
                </ul>
              </div>
              <div id="memory-selector" class="mb-2">
                <p v-if="memSelected">
                  Selected memory of {{ productToBeAdd.memory }}
                </p>
                <ul>
                  <li>
                    <button
                      @click="assignMem('128 GB')"
                      class="btn btn-outline-primary"
                    >
                      128 GB
                    </button>
                  </li>
                  <li>
                    <button
                      @click="assignMem('256 GB')"
                      class="btn btn-outline-primary"
                    >
                      256 GB
                    </button>
                  </li>
                  <li>
                    <button
                      @click="assignMem('512 GB')"
                      class="btn btn-outline-primary"
                    >
                      512 GB
                    </button>
                  </li>
                  <li>
                    <button
                      @click="assignMem('1 TB')"
                      class="btn btn-outline-primary"
                    >
                      1 TB
                    </button>
                  </li>
                </ul>
                <button class="btn btn-outline-primary" @click="clearProduct">
                  Clear selected product
                </button>
              </div>
              <hr />
              <div class="input-group mb-3 w-75">
                <input
                  id="quantity"
                  v-model="productToBeAdd.quantity"
                  placeholder="quantity"
                  type="number"
                  class="form-control"
                  aria-describedby="button-addon1"
                />
                <button class="btn btn-primary" @click="confirmOrder">
                  Add to cart
                </button>
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import { VueAgile } from "vue-agile";
import { mapState, mapActions, mapGetters, mapMutations } from "vuex";
import BaseRequest from "../../store/BaseRequest";
import config from "../../config";
export default {
  components: {
    agile: VueAgile,
  },
  data() {
    return {
      data: null,
      baseURL: config.baseURL,
      productToBeAdd: {
        title: null,
        price: null,
        color: "green",
        memory: "128 GB",
        thumbnail: null,
        quantity: 1,
      },
      selected: true,
      memSelected: true,
      option: {
        navButtons: false,
        responsive: [
          {
            breakpoint: 600,
            settings: {
              dots: false,
            },
          },

          {
            breakpoint: 900,
            settings: {
              navButtons: false,
              dots: false,
              infinite: true,
            },
          },
        ],
      },
      option2: {
        navButtons: false,
        responsive: [
          {
            breakpoint: 600,
            settings: {
              dots: false,
              slidesToShow: 3,
            },
          },
          {
            breakpoint: 605,
            settings: {
              slidesToShow: 2,
            },
          },
          {
            breakpoint: 900,
            settings: {
              navButtons: false,
              dots: false,
              infinite: true,
              slidesToShow: 4,
            },
          },
        ],
      },
    };
  },
  methods: {
    changeSlide(index) {
      this.$refs.main.goTo(index);
      this.$refs.thumbnails.goTo(index);
    },
    handlePrev() {
      this.$refs.main.goToPrev();
      this.$refs.thumbnails.goToPrev();
    },
    handleNext() {
      this.$refs.main.goToNext();
      this.$refs.thumbnails.goToNext();
    },
    assignColor(col) {
      this.productToBeAdd.color = col;
      this.selected = true;
    },
    assignMem(mem) {
      this.productToBeAdd.memory = mem;
      this.memSelected = true;
    },
    clearProduct() {
      this.selected = false;
      this.memSelected = false;
    },
    confirmOrder() {
      this.createProduct(this.productToBeAdd);
      this.$router.push({ name: "Cart" });
    },
    getProduct() {
      BaseRequest.get("/products/" + this.$route.params.id).then((response) => {
        this.data = response.data;
        this.productToBeAdd.thumbnail = response.data.product.thumbnail;
        this.productToBeAdd.title = response.data.product.title;
        this.productToBeAdd.price = response.data.product.price;
      });
    },
    ...mapActions(["createProduct"]),
    ...mapMutations(["setNewProduct"]),
  },
  mounted() {
    this.getProduct();

  },
  computed: {
    ...mapGetters(["orders"]),
    ...mapState(["newProduct"]),
  },
  filters: {
    priceFormat(value) {
      return new Intl.NumberFormat("it-IT", {
        style: "currency",
        currency: "VND",
      }).format(value);
    },
  },
};
</script>

<style lang="scss" scoped>
#main {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
}
.slide-img {
  width: 100%;
}
.agile-nav {
  max-height: 400px;
  margin-top: 20px;
  .slide-nav {
    max-height: 150px;
    .slide-img {
      width: 150px;
      max-height: 150px;
      background-color: #fff;
    }
  }
}
.agile-main {
  background-color: #fff;
}
.category-nav {
  display: flex;
  justify-content: left;
  align-items: center;
  height: 50px;
}
.category-nav p {
  margin: 0;
}
.cat-nav {
  background-color: #fff;
}

@media only screen and (max-width: 768px) {
  .container-main {
    display: block;
  }
}
.slide {
  touch-action: none;
}
.agile-controller {
  display: flex;
  justify-content: space-between;
}
#color-selector ul {
  display: flex;
  padding: 0;
  li {
    margin: 0px 5px;
    list-style-type: none;
  }
  #green span {
    height: 25px;
    width: 25px;
    background-color: green;
    border-radius: 50%;
    display: inline-block;
  }

  #blue span {
    height: 25px;
    width: 25px;
    background-color: blue;
    border-radius: 50%;
    display: inline-block;
  }

  #grey span {
    height: 25px;
    width: 25px;
    background-color: grey;
    border-radius: 50%;
    display: inline-block;
  }

  #black span {
    height: 25px;
    width: 25px;
    background-color: black;
    border-radius: 50%;
    display: inline-block;
  }

  #yellow span {
    height: 25px;
    width: 25px;
    background-color: yellow;
    border-radius: 50%;
    display: inline-block;
  }
}
#memory-selector {
  ul {
    display: flex;
    padding: 0;
    li {
      list-style-type: none;
      margin: 0px 5px;
    }
  }
}
.input-group {
  .btn {
    background-color: #0066cc;
    color: white;
  }
}
</style>

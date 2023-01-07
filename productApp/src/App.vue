<script setup></script>

<template>
  <main>
    <!--<TheWelcome />-->
    <div class="center">
      <h1>Enter a query string</h1>
      <input
        type="text"
        placeholder="Search query"
        @input="fetchProducts"
        v-model="query"
        class="search"
      />
      <h2>Products</h2>
      <div class="productsContainer">
        <div class="internalContainer">
          <div class="product" v-for="product in products" :key="product.id">
            <p>
              {{ product.title }} - {{ product.price }}€ - {{ product.store }}
            </p>
          </div>
        </div>
      </div>
    </div>
  </main>
</template>

<script>
import axios from "axios";

// Get the base url from environment variables
const SERVER_URL =
  import.meta.env.BACKEND_SERVER_URL || "http://localhost:8000/";

const client = axios.create({
  baseURL: SERVER_URL,
  timeout: 5000,
});

export default {
  name: "App",

  data() {
    return {
      query: "",
      products: [],
    };
  },
  mounted() {
    this.fetchProducts();
  },
  methods: {
    fetchProducts(e) {
      // Create a request to backend products
      client
        .get("/api/v1/products", {
          params: {
            search: this.query,
          },
        })
        .then((response) => {
          this.products = response.data;
          return response;
        })
        .catch((error) => {
          return error;
        });
    },
  },
};
</script>

<style scoped>
.search {
  width:300px;
  height:50px;
  font-size: 15px;
  margin-left: 10px;
}

.center {
  background-color: rgb(53, 113, 49);
  height: 100%;
  left: auto;
  right: auto;
  width: 100%;
  margin: 0;
}

.internalContainer{
  padding: 20px;
}

.productsContainer {
  background-color: lightblue;
  height: 90%;
  width: 100%;
  top: 5%;
  z-index: 1;
  font-size: 20px;
}
</style>

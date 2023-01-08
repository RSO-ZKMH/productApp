<script setup></script>

<template>
  <main>
    <!--<TheWelcome />-->
    <div class="login" v-if='!isLoggedIn'>
      <h1>Welcome to the product app</h1>
      <p>
        This is a simple app to search for products. You can search for products
        by name, price, store, etc.
      </p>
      <p>
        To get started, please log in with your username and password.
      </p>
    </div>
    <div class="login" v-if='!isLoggedIn'>
      <h1>Log in</h1>
      <input type="text" v-model="username" placeholder="Username" /><br>
      <input type="password" v-model="password" placeholder="Password" />
      <button @click='login'>Log in</button>
    </div>
    <div class="center" v-if='isLoggedIn'>
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

// Get the base url from environment variables using process.env
const SERVER_URL = import.meta.env.VITE_BACKEND_URL || "products";
const USER_SERVICE_URL = import.meta.env.VITE_USER_SERVICE_URL || "user";

const clientProduct = axios.create({
  baseURL: SERVER_URL,
  timeout: 5000,
});
const clientUser = axios.create({
  baseURL: USER_SERVICE_URL,
  timeout: 5000,
});

export default {
  name: "App",

  data() {
    return {
      query: "",
      products: [],
      username: "",
      password: "",
      // Check if localstorage has a token
      isLoggedIn: localStorage.getItem("token") ? true : false,
    };
  },
  mounted() {
    this.fetchProducts();
  },
  methods: {
    login(e) {
      // Send a request to login with username and password
      clientUser
        .post("/api/v1/login", {
          username: this.username,
          password: this.password,
        })
        .then((response) => {
          // Save the response token in localstorage
          localStorage.setItem("token", response.data.token);
          this.isLoggedIn = true;
          return response;
        })
        .catch((error) => {
          return error;
        });

    },
    fetchProducts(e) {
      // Create a request to backend products
      clientProduct
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

.login {
  background-color: rgb(53, 113, 49);
  height: 100%;
  left: auto;
  right: auto;
  width: 100%;
  margin: 0;
  text-align: center;
  padding-top: 20px;
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

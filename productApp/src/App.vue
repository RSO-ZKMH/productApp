<script setup></script>

<template>
  <main>
    <!--<TheWelcome />-->
    <div class="login" v-if="!isLoggedIn">
      <h1>Welcome to the product app</h1>
      <p>
        This is a simple app to search for products. You can search for products
        by name, price, store, etc.
      </p>
      <p>To get started, please log in with your username and password.</p>
    </div>
    <div class="login" v-if="!isLoggedIn">
      <h1>Log in</h1>
      <input type="text" class="input" v-model="username" placeholder="Username" /><br />
      <input type="password" class="input" v-model="password" placeholder="Password" />
      <br>
      <button @click="login">Log in</button>
    </div>
    <div class="center" v-if="isLoggedIn">
      <div class="logout">
        <button @click="logout">Logout</button>
      </div>
      <h1>Enter a query string</h1>
      <input
        type="text"
        placeholder="Search query"
        v-model="query"
        class="search"
      />
      <button @click="fetchProducts">Search</button>

      <h2>Products</h2>
      <div class="productsContainer">
        <div class="internalContainer">
          <div class="product" v-for="product in products" :key="product.id">
            <p>
              {{ product.title }} - {{ product.price }}€ - {{ product.store }}
              <button @click="addBasket(product)" style="float-right">
                Add to shopping basket
              </button>
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
const BASKET_SERVICE_URL = import.meta.env.VITE_BASKET_SERVICE_URL || "shoppingBasket";

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
      timer: null,
    };
  },
  mounted() {
    this.fetchProducts();
  },
  methods: {
    login(e) {
      // Get the csrf token from the cookie
      const csrf_token = document.cookie
        .split("; ")
        .find((row) => row.startsWith("csrftoken="))
        .split("=")[1];
      // Send a request to login with username and password
      clientUser
        .post(
          "/api/v1/login",
          {
            username: this.username,
            password: this.password,
          },
          {
            headers: { "X-CSRFToken": csrf_token },
          }
        )
        .then((response) => {
          // Save the response token in localstorage
          localStorage.setItem("token", response.data.token);
          localStorage.setItem("userId", response.data.userId);
          this.isLoggedIn = true;
          return response;
        })
        .catch((error) => {
          return error;
        });
    },
    logout() {
      // Clear localstorage
      localStorage.clear();
      this.isLoggedIn = false;
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
    addBasket(product) {
      let productId = product.id;
      let userId = localStorage.getItem("id");



      // Create a request to backend shoppingBasket

    },
  },
};
</script>

<style scoped>
.search {
  width: 300px;
  height: 50px;
  font-size: 15px;
  margin-left: 10px;
}

.input {
  width: 200px;
  height: 50px;	
}
.center {
  height: 100%;
  left: auto;
  right: auto;
  width: 100%;
  margin: 0;
}

.login {
  height: 100%;
  left: auto;
  right: auto;
  width: 100%;
  margin: 0;
  text-align: center;
  padding-top: 20px;
}

.logout {
  margin: 10px;
  float: right;
}

.internalContainer {
  width: 75%;
  margin-left: 12.5%;
  margin-right: 12.5%;
}

.product {
  border: 1px solid black;
  padding: 10px;
  margin: 10px;
  align-items: center;
}

button {
  background-color: rgb(53, 113, 49);
  color: white;
  border: none;
  padding: 12px 28px;
  text-align: center;
  text-decoration: none;
  display: inline-block;
  font-size: 16px;
  margin: 4px 2px;
  cursor: pointer;
}

.productsContainer {
  height: 90%;
  width: 100%;
  top: 5%;
  z-index: 1;
  font-size: 20px;
}
</style>

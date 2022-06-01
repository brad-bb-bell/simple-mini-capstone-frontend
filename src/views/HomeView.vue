<script>
import axios from "axios";

export default {
  data: function () {
    return {
      message: "Welcome to the Denver Broncos Fan Page",
      newMessage: "",
      names: ["Russel Wilson", "Javonte Williams", "Jerry Jeudy"],
      newName: "",
      products: [],
      newProduct: {},
      currentProduct: {},
      editProduct: {},
    };
  },
  created: function () {
    this.indexProducts();
  },
  methods: {
    changeMessage: function () {
      this.message = this.newMessage;
      this.newMessage = "";
    },
    indexProducts: function () {
      axios.get("http://localhost:3000/products.json").then((response) => {
        this.products = response.data;
        console.log(response.data);
      });
    },
    createProduct: function () {
      console.log("Creating Product...");
      var params = this.newProduct;
      axios
        .post("http://localhost:3000/products.json", params)
        .then((response) => {
          console.log("Success", response.data);
          this.products.push(response.data);
        })
        .catch((error) => console.log(error.response));
      this.newProduct = "";
    },
    showProduct: function (product) {
      console.log("Show action for:", product.name);
      this.currentProduct = product;
      this.editProduct = this.currentProduct;
      document.querySelector("#product-details").showModal();
    },
    updateProduct: function () {
      console.log("Update action for:", this.editProduct.name);
      axios
        .patch("http://localhost:3000/products/" + this.editProduct.id + ".json", this.editProduct)
        .then((response) => {
          console.log("Success", response.data);
        });
    },
    destroyProduct: function () {
      console.log("Destroy action for:", this.currentProduct.name);
      axios.delete("http://localhost:3000/products/" + this.currentProduct.id + ".json").then((response) => {
        console.log("Success", response.data);
      });
      var index = this.products.indexOf(this.currentProduct);
      this.products.splice(index, 1);
    },
    addName: function () {
      this.names.push(this.newName);
      this.newName = "";
    },
  },
};
</script>

<template>
  <div class="home">
    <h1>{{ message }}</h1>
  </div>
  <p><input type="text" v-model="newMessage" /></p>
  <p><button v-on:click="changeMessage()">Change message</button></p>
  <div>
    <h2>Your favorite players</h2>
  </div>
  <div v-for="name in names" v-bind:key="name">
    <li>{{ name }}</li>
  </div>
  <p><input type="text" v-model="newName" /></p>
  <p><button v-on:click="addName()">Add another player</button></p>

  <div>
    <h1>Store</h1>
    <div>
      Name:
      <input type="text" v-model="newProduct.name" />
    </div>
    <div>
      Description:
      <input type="text" v-model="newProduct.description" />
    </div>
    <div>
      Price:
      <input type="text" v-model="newProduct.price" />
    </div>
    <div>
      Image URL:
      <input type="text" v-model="newProduct.image_url" />
    </div>
    <button v-on:click="createProduct()">Create Product</button>
    <div v-for="product in products" v-bind:key="product.id">
      <h2>{{ product.name }}</h2>
      <p>Price: ${{ product.price }}</p>
      <p><button v-on:click="showProduct(product)">More info</button></p>
      <img v-bind:src="product.image_url" :alt="product.name" />
    </div>

    <dialog id="product-details">
      <form method="dialog">
        <h1>Production info:</h1>
        <p>
          <b>{{ currentProduct.name }}</b>
        </p>
        <p>{{ currentProduct.description }}</p>
        <p>Price: ${{ currentProduct.price }}</p>
        <h1>Update info:</h1>
        <p>
          Name:
          <input type="text" v-model="editProduct.name" />
        </p>
        <p>
          Description:
          <input type="text" v-model="editProduct.description" />
        </p>
        <p>
          Price:
          <input type="text" v-model="editProduct.price" />
        </p>
        <p>
          Image URL:
          <input type="text" v-model="editProduct.image_url" />
        </p>
        <button v-on:click="updateProduct()">Save Updated Info</button>
        <button v-on:click="destroyProduct()">Delete Product</button>
        <p><button>Close</button></p>
      </form>
    </dialog>
  </div>
</template>

<style></style>

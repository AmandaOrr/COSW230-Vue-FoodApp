<template>
  <header>
    <div id="mainhead">
      <h1>{{ restaurantName }}</h1>
      <span id="navButtons">
        <button id="checkout">Checkout {{ cartItemCount }}</button>
        <button @click="isMenu = !isMenu" class="navButton">
          <span v-if="isMenu">Add a New Menu Item</span>
          <span v-else>Back to Home</span>
        </button>
      </span>
    </div>
  </header>
  <main v-if="isMenu">
    <h2 class="title">Choose from our Menu Items</h2>
    <div class="menu">
      <div v-for="item in items" :key="item.id" id="menuItem">
        <h3>{{ item.name }}</h3>
        <img :src="item.image" />
        <p>{{ item.description }}</p>
        <p>{{ formatPrice(item.price) }}</p>
        <button v-if="item.quantity > 0" @click="addToCart(item)">
          Add To Cart
        </button>
        <button v-else :disabled="isDisabled" class="OOS">Sold Out</button>
        <p v-if="item.quantity > 4">In stock</p>
        <p v-else-if="item.quantity <= 0">Sold Out</p>
        <p v-else>Almost Sold Out!</p>
      </div>
    </div>
  </main>
  <main v-else>
    <h2 class="title">Add a New Menu Item</h2>
    <form action="" method="POST" @submit.prevent="newItem">
      <label for="itemId"
        >Item ID <input type="number" v-model="addedItem.id" /></label
      ><br />
      <label for="itemName"
        >Item Name <input type="text" v-model="addedItem.name" /></label
      ><br />
      <label for="itemDescription"
        >Item Description<br /><textarea
          id="description"
          v-model="addedItem.description"
        ></textarea></label
      ><br />
      <label for="itemPrice"
        >Price <input type="number" v-model="addedItem.price" /></label
      ><br />
      <label for="itemQuantity"
        >Amount in Stock<input
          type="number"
          v-model="addedItem.quantity" /></label
      ><br />
      <button>Submit</button>
      <button @click="isMenu = !isMenu">Back to Menu</button>
    </form>
  </main>
  <footer></footer>
</template>

<script>
import axios from "axios";
export default {
  data: function () {
    return {
      items: [],
      restaurantName: "Food Wars in Real Life",
      cart: [],
      addedItem: {
        id: "",
        name: "",
        description: "",
        price: 1,
        quantity: 1,
        inStock: true,
      },
      isMenu: true,
    };
  }, //data end here
  computed: {
    formatPrice: function () {
      return function (price) {
        const dollars = price / 100; //price formatting
        return dollars.toLocaleString("en-US", {
          style: "currency",
          currency: "USD",
        }); // example $20.00
      };
    },
    cartItemCount() {
      return this.cart.length || "";
    },
  }, //computed ends here
  methods: {
    //to create a new menu item and push it to the connected json array
    newItem: function () {
      const newItem = {
        id: this.addedItem.id,
        name: this.addedItem.name,
        description: this.addedItem.description,
        quantity: this.addedItem.quantity,
        price: this.addedItem.price,
        category: this.addedItem.category,
      };
      this.items.push(newItem);
    },
    addToCart: function (item) {
      if (item.quantity > 0) {
        item.quantity--;
        this.cart.push(item);
      }
    },
  },
  created: function () {
    //adding the lifecycle method to fetch the data when its rendered
    axios.get("data.json").then((response) => {
      //telling it where to get the data, axios needs a promise thats a function
      this.items = response.data.items;
    });
  },
};
</script>

<style>
@import url("https://fonts.googleapis.com/css2?family=Gloria+Hallelujah&family=Pacifico&display=swap");
body {
  margin: 2% 0;
  background-image: url("https://coolwallpapers.me/picsup/5080069-sma-yukihira.png");
  background-position: center;
  background-size: cover;
  background-repeat: no-repeat;
  background-attachment: fixed;
  color: #3F2112;
  font-family: "Gloria Hallelujah", cursive;
}

#mainhead {
  display: flex;
  flex-direction: row;
  justify-content: space-evenly;
  align-content: center;
  background-color: #EF959D;
  border-radius: 7px;
  margin: 0 5%;
}

h1,
h2 {
  text-align: center;
  font-family: "Pacifico", cursive;
}

.title {
  color: #d9dbbcff;
  font-size: 200%;
  font-weight: lighter;
}

#checkout,
.navButton,
button {
  width: 100px;
  height: 50px;
  margin: 10px 0;
  border-radius: 7px;
  background-color: #D9DBBC;
}

#mainhead button {
  margin: 1%;
}

#navButtons {
  display: flex;
  flex-direction: row;
  align-items: center;
}

.OOS {
  text-decoration: line-through;
}

#menuItem,
form {
  background-color: #EF959D;
  opacity: 0.8;
  width: 45%;
  border-radius: 10px;
  padding: 2%;
  margin: 0 5% 2%;
  text-align: center;
}
/*

--celadon: #b8d8baff;
--beige: #d9dbbcff;
--salmon-pink: #ef959dff;
--wenge: #69585fff;
*/

form button {
  margin: 0 2%;
}

img {
  max-width: 100%;
  width: 70%;
  border-radius: 7px;
}
</style>
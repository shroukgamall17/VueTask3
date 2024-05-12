<template>
  <div id="mainapp" style="background-color: #f0f8ff;">
    <div class="d-flex align-items-center justify-content-between custom-style p-3">
      <a href="#" @click.prevent="iscartvisible=false" class="text-decoration-none text-dark">Products</a>
      <div class="d-flex align-items-center">
        <p class="mx-2">{{ cart.items.length }} <template v-if="cart.items.length === 1">item</template> <template v-else>items</template> with total [ {{ formatCurrency(getTotalPrice()) }} ]</p>
        <button class="btn btn-primary rounded-pill" @click="iscartvisible=true">Show Cart</button>
      </div>
    </div>
    <div class="row justify-content-center mt-3" v-if="!iscartvisible">
      <div class="col-md-3" v-for="product in products" :key="product.id">
        <div class="card mb-3 shadow" style="border-radius: 15px;">
          <img :src="product.image" :alt="product.name" class="card-img-top" :title="product.name"/>
          <div class="card-body">
            <h5 class="card-title">{{ product.name }}</h5>
            <p class="card-text">{{ product.description }}</p>
            <div class="d-flex justify-content-between align-items-center">
              <p :class="[product.instock >= 5 ? 'text-success' : (product.instock > 0 ? 'text-warning' : 'text-danger')]">{{ product.instock }} in Stock</p>
              <button class="btn btn-primary rounded-pill" :disabled="product.instock === 0" @click="addToCart(product)">Add to Cart</button>
            </div>
          </div>
        </div>
      </div>
    </div>
    <div class="p-3" v-else>
      <h4 class="text-center text-danger" v-if="cart.items.length === 0">Your Cart is empty,, you have to add products first ^_^</h4>
      <table class="table table-bordered table-striped text-center mb-0" v-else>
        <thead class="bg-primary text-white">
          <tr>
            <th>ID</th>
            <th>Name</th>
            <th>Price</th>
            <th>Actions</th>
            <th>Total Price</th>
          </tr>
        </thead>
        <tbody>
          <tr v-for="item in cart.items" :key="item.product.id">
            <td>{{ item.product.id }}</td>
            <td>{{ item.product.name }}</td>
            <td>{{ formatCurrency(item.product.price) }}</td>
            <td>
              <button class="btn btn-success rounded-pill" @click="increaseQuantity(item)" :disabled="item.product.instock === 0">+</button>
              <button class="btn btn-danger rounded-pill" @click="decreaseQuantity(item)">-</button>
            </td>
            <td>{{ formatCurrency(item.product.price * item.quantity) }}</td>
          </tr>
        </tbody>
        <tfoot class="bg-light">
          <tr>
            <th colspan="4">Total Price</th>
            <th colspan="2">{{ formatCurrency(getTotalPrice()) }}</th>
          </tr>
          <tr>
            <th colspan="4">Total Taxes (10%)</th>
            <th colspan="2">{{ formatCurrency(getTotalPrice() * 0.1) }}</th>
          </tr>
          <tr>
            <th colspan="4">Grand Total</th>
            <th colspan="2">{{ formatCurrency(getTotalPrice() + getTotalPrice() * 0.1) }}</th>
          </tr>
        </tfoot>
      </table>
    </div>
  </div>
</template>

<script>
import products from '../products'

export default {
  data() {
    return {
      products: products,
      iscartvisible: false,
      cart: {
        items: []
      }
    }
  },
  methods: {
    formatCurrency(value) {
      return Intl.NumberFormat("ar-SA", {
        style: "currency",
        currency: "SAR",
        minimumFractionDigits: 0
      }).format(value);
    },
    increaseQuantity(item) {
      item.quantity++;
      item.product.instock--;
    },
    decreaseQuantity(item) {
      item.quantity--;
      if (item.quantity === 0) {
        const index = this.cart.items.findIndex(i => i.product.id === item.product.id);
        if (index !== -1) {
          this.cart.items.splice(index, 1);
        }
      }
      item.product.instock++;
    },
    addToCart(product) {
      const existingItem = this.cart.items.find(i => i.product.id === product.id);
      if (existingItem) {
        existingItem.quantity++;
      } else {
        this.cart.items.push({ product: product, quantity: 1 });
      }
      product.instock--;
    },
    getTotalPrice() {
      return this.cart.items.reduce((total, item) => total + item.product.price * item.quantity, 0);
    }
  }
}
</script>

<style scoped>
.custom-style {
  background-color: #eee;
  color: #333;
  border: 1px solid #0077ff;
  border-radius: 10px;
  padding: 10px;
}

.card {
  border-radius: 15px;
}

.btn {
  border-radius: 15px;
}
</style>

<template>
    <div class="container-fluid p-4" style="background-color: #f0f8ff;" id="mainapp">
      <nav>
        <ul class="nav nav-tabs ">
          <li class="nav-item">
            <a class="nav-link rounded-pill" @click="isbookvisible = false" style="background-color: #98fb98;">Books</a>
          </li>
          <li class="nav-item">
            <a class="nav-link rounded-pill" @click="isbookvisible = true" style="background-color: #98fb98;">Wishlist</a>
          </li>
          <li> {{ list.items.length }}</li>
        </ul>
      </nav>
      <div class="row row-cols-1 row-cols-md-4 g-4" v-if="isbookvisible == false">
        <div class="col" v-for="book in books" :key="book.id">
          <div class="card h-100 shadow" style="background-color: #fafafa;">
            <div class="card-header">
              <h5 class="card-title text-center">{{book.name}}</h5>
            </div>
            <img :title="book.name" class="card-img-top img-fluid" alt="Book cover" :src="book.image">
            <div class="card-body text-center">
              <p class="card-text">Author: {{ book.author }}</p>
              <p class="card-text">Pages: {{ book.pages }}</p>
              <p class="card-text">Price: {{ formatCurrency(book.price.value) }}</p>
              <div class="card-footer">
                <button type="button" class="btn btn-primary rounded-pill" @click="addToWishList(book)"
                  :disabled="list.items.some(item => item.book.id === book.id)">
                  <span v-if="list.items.some(item => item.book.id === book.id)">Added Successfully ^_^</span>
                  <span v-else>Add to my Wishlist ^_^</span>
                </button>
              </div>
            </div>
          </div>
        </div>
      </div>
      <div class="d-flex align-items-baseline justify-content-between p-2 m-2" v-else>
        <h4 class="w-100 text-center text-danger" v-if="list.items.length === 0">Your Cart is empty,, you have to add books first^_^</h4>
        <table class="table table-bordered table-striped text-center" v-else>
          <thead>
            <tr>
                <th>Name</th>
              <th>Price</th>
              <th>Action</th>
            </tr>
          </thead>
          <tbody>
            <tr v-for="(item, index) in list.items" :key="index">
              <td>{{ item.book.name }}</td>
              <td>{{ formatCurrency(item.book.price.value) }}</td>
              <td><button class="btn btn-danger rounded-pill" @click="remove(index)">Remove</button></td>
            </tr>
          </tbody>
        </table>
      </div>
    </div>
  </template>
  
  <script>
  import books from '../books.js'
  export default {
    data() {
      return {
        books: books,
        isbookvisible: false,
        list: { items: [] }
      };
    },
    methods: {
      addToWishList(book) {
        if (!this.list.items.some(item => item.book.id === book.id)) {
          this.list.items.push({ book: book });
        }
        this.isbookvisible = false;
      },
      formatCurrency(value) {
        return Intl.NumberFormat("ar-SA", {
          style: "currency",
          currency: "SAR",
          minimumFractionDigits: 0
        }).format(value);
      },
      remove(index) {
        this.list.items.splice(index, 1);
      }
    }
  }
  </script>
  
  <style scoped>
  .card {
    border-radius: 15px;
  }
  
  .btn {
    border-radius: 15px;
  }
  </style>
  
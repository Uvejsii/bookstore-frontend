<script setup>
import {computed, onMounted, ref} from "vue";
import router from "@/router/index.js";

const books = ref([])
const searchInput = ref('')

onMounted(async () => {
  const res = await fetch('http://localhost:5251/books')
  const data = await res.json()

  books.value = await data
})

const goToAdminView = () => {
  router.push('/admin')
}

const goToBookDetailView = (bookId) => {
  router.push(`/book/${bookId}`)
}

const filteredBooks = computed(() => {
  return books.value.filter(book => book.name.toLowerCase().includes(searchInput.value.toLowerCase()))
})
</script>

<template>
  <div class="container my-3">
    <div class="d-flex justify-content-between mb-3">
      <button class="btn btn-primary" @click="goToAdminView">Go To Admin View</button>
      <input type="text" placeholder="Search..." v-model="searchInput">
    </div>
    <div class="row row-cols-1 row-cols-md-3 g-4">
      <div class="col" v-if="filteredBooks.length > 0" v-for="book in filteredBooks" :key="book.id">
        <div class="card">
          <img
              src="../assets/books.jpg"
              class="card-img-top img-fluid" alt="book image"
              @click="goToBookDetailView(book.id)"
          >
          <div class="card-body">
            <h5 class="card-title" @click="goToBookDetailView(book.id)">Title: {{ book.name }}</h5>
            <p class="card-text">Author: {{ book.author }}</p>
            <p class="card-text">Price: {{ book.price }} €</p>
            <p class="card-text">Pages: {{ book.pages }}</p>
          </div>
          <div class="card-footer">
            <button class="btn btn-primary w-100" @click="goToBookDetailView(book.id)">View Book</button>
          </div>
        </div>
      </div>
      <div class="col" v-else><p>Sorry, no books found</p></div>
    </div>
  </div>
</template>

<style scoped>
img, h5 {
  cursor: pointer;
}

.card-title {
  display: -webkit-box;
  -webkit-line-clamp: 1;
  -webkit-box-orient: vertical;
  overflow: hidden;
}
</style>
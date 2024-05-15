<script setup>
import {onMounted, ref} from "vue";
import {useRoute} from "vue-router";
import router from "@/router/index.js";

const route = useRoute()

const book = ref({})

onMounted(async () => {
  const res = await fetch(`http://localhost:5251/book/${route.params.id}`)
  if (res.ok) {
    const data = await res.json()
    book.value = data
  } else {
    console.error('Failed to fetch book:', res.status)
    book.value = {error: 'Book not found'}
  }
})

const goToHomePage = () => {
  router.push("/")
}
</script>

<template>
  <div class="container">
    <button class="btn btn-outline-dark my-4" @click="goToHomePage">Back to Home Page</button>
    <div class="row d-flex justify-content-center">
      <div class="card mb-3 col-9">
        <div class="row g-0">
          <div class="col-md-4">
            <img src="../assets/books.jpg" class="img-fluid rounded-start" alt="book image">
          </div>
          <div class="col-md-8">
            <div class="card-body">
              <h1 class="card-title"><span class="text-secondary">Title:</span> {{ book.name }}</h1>
              <h5 class="card-text"><span class="text-secondary">Author:</span> {{ book.author }}</h5>
              <h5 class="card-text"><span class="text-secondary">Price:</span> {{ book.price }} €</h5>
              <h5 class="card-text"><span class="text-secondary">Pages:</span> {{ book.pages }}</h5>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<style scoped>

</style>
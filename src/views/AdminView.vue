<script setup>
import {onMounted, ref} from "vue";
import {toast} from "vue3-toastify";
import "vue3-toastify/dist/index.css";

const books = ref([])

const addBookData = ref({
  name: '',
  author: '',
  pages: '',
  price: ''
})

const editBookData = ref({
  name: '',
  author: '',
  pages: '',
  price: ''
})

const selectedBookId = ref(null)

onMounted(async () => {
  await fetchBooks()
})

const fetchBooks = async () => {
  const res = await fetch('http://localhost:5251/books')
  const data = await res.json()

  books.value = await data
}

const addBook = async () => {
  const newBook = {
    name: addBookData.value.name,
    author: addBookData.value.author,
    pages: parseInt(addBookData.value.pages),
    price: parseInt(addBookData.value.price)
  }

  try {
    const res = await fetch('http://localhost:5251/book', {
      method: 'POST',
      headers: {
        'Content-Type': 'application/json'
      },
      body: JSON.stringify(newBook)
    })

    if (!res.ok) {
      throw new Error('Failed to add book')
    }

    await fetchBooks()
    toast.success('Added! You have successfully added the book!')
  } catch (error) {
    console.error('Error adding book', error)
    toast.error('Failed to add the book')
  }

  addBookData.value.name = ''
  addBookData.value.author = ''
  addBookData.value.pages = ''
  addBookData.value.price = ''
}

const editBook = async () => {
  const updatedBook = {
    name: editBookData.value.name,
    author: editBookData.value.author,
    pages: parseInt(editBookData.value.pages),
    price: parseInt(editBookData.value.price)
  }

  try {
    const res = await fetch(`http://localhost:5251/book/${selectedBookId.value}`, {
      method: 'PUT',
      headers: {
        'Content-type': 'application/json'
      },
      body: JSON.stringify(updatedBook)
    })

    if (!res.ok) {
      throw new Error('Failed to update')
    }

    await fetchBooks()
    toast.success('Edited, You have successfully edited the book!')
  } catch (error) {
    console.error('Error editing book', error)
    toast.error('Failed to update book')
  }

  editBookData.value.name = '';
  editBookData.value.author = '';
  editBookData.value.pages = '';
  editBookData.value.price = '';
  selectedBookId.value = null;
}

const startEdit = async (book) => {
  editBookData.value.name = book.name
  editBookData.value.author = book.author
  editBookData.value.pages = book.pages.toString()
  editBookData.value.price = book.price.toString()
  selectedBookId.value = book.id
}

const deleteBook = async (bookId) => {
  try {
    const res = await fetch(`http://localhost:5251/book/${bookId}`, {
      method: 'DELETE'
    })

    if (!res.ok) {
      throw new Error('Failed to delete book')
    }

    await fetchBooks()
    toast.success('Deleted! You have successfully deleted the book!')
  } catch (error) {
    console.error('Error deleting book:', error)
    toast.error('Failed to delete the book')
  }
}
</script>

<template>
  <div class="container">
    <RouterLink to="/">
      <button class="btn btn-primary mt-3">Go To Home View</button>
    </RouterLink>
    <!-- Modal -->
    <div class="modal fade" id="exampleModal" tabindex="-1" aria-labelledby="exampleModalLabel" aria-hidden="true">
      <div class="modal-dialog">
        <div class="modal-content">
          <div class="col-12 text-warning fw-bold form-container border border-warning rounded-3 bg-warning-subtle p-4">
            <h2 class="text-center">Edit a book</h2>
            <div class="inputs-container">
              <p class="m-0">Title</p>
              <input type="text" class="w-100" v-model="editBookData.name">
            </div>
            <div class="inputs-container my-3">
              <p class="m-0">Author</p>
              <input type="text" class="w-100" v-model="editBookData.author">
            </div>
            <div class="inputs-container">
              <p class="m-0">Pages</p>
              <input type="text" class="w-100" v-model="editBookData.pages">
            </div>
            <div class="inputs-container mt-3">
              <p class="m-0">Price</p>
              <input type="text" class="w-100" v-model="editBookData.price">
            </div>
            <button class="btn btn-warning w-100 mt-4 fw-bold"
                    data-bs-dismiss="modal"
                    @click="editBook()">Save Changes
            </button>
          </div>
        </div>
      </div>
    </div>
<!-- Modal -->
    <div class="row d-flex justify-content-center mt-5">
      <div class="col-6 text-primary fw-bold form-container border border-primary rounded-4 bg-primary-subtle p-4">
        <h2 class="text-center">Add a book</h2>
        <div class="inputs-container">
          <p class="m-0">Title</p>
          <input type="text" class="w-100" v-model="addBookData.name">
        </div>
        <div class="inputs-container my-3">
          <p class="m-0">Author</p>
          <input type="text" class="w-100" v-model="addBookData.author">
        </div>
        <div class="inputs-container">
          <p class="m-0">Pages</p>
          <input type="text" class="w-100" v-model="addBookData.pages">
        </div>
        <div class="inputs-container mt-3">
          <p class="m-0">Price</p>
          <input type="text" class="w-100" v-model="addBookData.price">
        </div>
        <button class="btn btn-primary w-100 mt-4 fw-bold"
                @click="addBook()">Add Book
        </button>
      </div>
    </div>
    <hr>
    <h1 class="text-center">Available Books</h1>
    <div class="row row-cols-1 row-cols-md-3 g-4 my-4">
      <div class="col" v-if="books.length > 0" v-for="book in books" :key="book.id">
        <div class="card">
          <img
              src="../assets/books.jpg"
              class="card-img-top img-fluid" alt="book image"
          >
          <div class="card-body">
            <h5 class="card-title">Title: {{ book.name }}</h5>
            <p class="card-text">Author: {{ book.author }}</p>
            <p class="card-text">Price: {{ book.price }} €</p>
            <p class="card-text">Pages: {{ book.pages }}</p>
          </div>
          <div class="card-footer d-flex gap-3">
            <button class="btn btn-danger w-50 fw-bold"
                    @click="deleteBook(book.id)">
              Delete Book
            </button>
            <button class="btn btn-warning w-50 fw-bold"
                    data-bs-toggle="modal" data-bs-target="#exampleModal"
                    @click="startEdit(book)">
              Edit Book
            </button>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<style scoped>
.card-title {
  display: -webkit-box;
  -webkit-line-clamp: 1;
  -webkit-box-orient: vertical;
  overflow: hidden;
}
</style>
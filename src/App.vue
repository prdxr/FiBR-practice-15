<template>
  <div class="container mt-5">
    <h1 class="text-center mb-4">Моя коллекция</h1>
    
    <div class="row">
      <div class="col-md-6 offset-md-3">
        <form @submit.prevent="addBook" class="input-group mb-4">
          <input 
            v-model="newBook" 
            type="text" 
            class="form-control" 
            placeholder="Название"
            required
          >
          <button class="btn btn-primary" type="submit">Добавить</button>
        </form>
      </div>
    </div>

    <div class="row">
      <div 
        v-for="book in books" 
        :key="book.id" 
        class="col-md-4 mb-3"
      >
        <div class="card h-100">
          <div class="card-body">
            <h5 class="card-title">{{ book.title }}</h5>
            <button 
              @click="removeBook(book.id)" 
              class="btn btn-sm btn-danger"
            >
              Удалить
            </button>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref } from 'vue'

const books = ref([])
const newBook = ref('')

const addBook = () => {
  books.value.push({
    id: Date.now(),
    title: newBook.value
  })
  newBook.value = ''
}

const removeBook = (id) => {
  books.value = books.value.filter(book => book.id !== id)
}
</script>
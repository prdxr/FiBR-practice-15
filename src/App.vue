<template>
  <div class="container mt-5">
    <h1 class="text-center mb-4">Моя коллекция</h1>

    <!-- Форма добавления нового элемента -->
    <div class="row justify-content-center mb-5">
      <div class="col-md-8">
        <form @submit.prevent="addItem" class="card p-4">
          <div class="mb-3">
            <label for="title" class="form-label">Название</label>
            <span class="form-required">*</span>
            <input
              type="text"
              class="form-control"
              id="title"
              v-model="title"
              required
            />
          </div>
          <div class="mb-3">
            <label for="description" class="form-label">Описание</label>
            <textarea
              class="form-control"
              id="description"
              rows="2"
              v-model="description"
            ></textarea>
          </div>
          <div class="mb-3">
            <label for="imageUrl" class="form-label">URL фото</label>
            <input
              type="url"
              class="form-control"
              id="imageUrl"
              v-model="imageUrl"
              placeholder="https://example.com/image.jpg"
            />
          </div>
          <button type="submit" class="btn btn-primary">Добавить</button>
        </form>
      </div>
    </div>

    <!-- Кнопки сортировки -->
    <div class="d-flex justify-content-center gap-2 mb-4">
      <button
        class="btn"
        :class="sortBy === 'date' ? 'btn-dark' : 'btn-outline-secondary'"
        @click="sortBy = 'date'"
      >
        По дате (новые)
      </button>
      <button
        class="btn"
        :class="sortBy === 'title' ? 'btn-dark' : 'btn-outline-secondary'"
        @click="sortBy = 'title'"
      >
        По названию
      </button>
    </div>

    <!-- Сетка карточек -->
    <div v-if="sortedItems.length === 0" class="text-center text-muted">
      Коллекция пуста. Добавьте первый элемент!
    </div>
    <div v-else class="row g-4">
      <div v-for="item in sortedItems" :key="item.id" class="col-md-6 col-lg-4">
        <div class="card h-100 shadow-sm">
          <img
            :src="item.imageUrl"
            class="card-img-top"
            :alt="item.title"
            style="height: 200px; object-fit: cover;"
            @error="handleImageError"
          />
          <div class="card-body">
            <h5 class="card-title">{{ item.title }}</h5>
            <p class="card-text text-muted">{{ item.description || 'Нет описания' }}</p>
            <button class="btn btn-sm btn-danger" @click="removeItem(item.id)">
              Удалить
            </button>
          </div>
          <div class="card-footer text-muted small">
            Добавлено: {{ new Date(item.createdAt).toLocaleString() }}
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, watch, computed } from 'vue'

// Состояние для элементов коллекции
const items = ref([])

// Поля формы
const title = ref('')
const description = ref('')
const imageUrl = ref('')
const sortBy = ref('date') // 'date' или 'title'

// Загрузка из localStorage при инициализации
const saved = localStorage.getItem('collection')
if (saved) {
  try {
    items.value = JSON.parse(saved)
  } catch (e) {
    console.error('Ошибка загрузки из localStorage', e)
    items.value = []
  }
}

// Автосохранение в localStorage при изменении items
watch(items, (newVal) => {
  localStorage.setItem('collection', JSON.stringify(newVal))
}, { deep: true })

// Добавление нового элемента
const addItem = () => {
  if (!title.value.trim()) return
  const newItem = {
    id: Date.now(),
    title: title.value.trim(),
    description: description.value.trim(),
    // Если URL не указан, используем заглушку
    imageUrl: imageUrl.value.trim() || 'https://via.placeholder.com/300?text=No+Image',
    createdAt: Date.now()
  }
  items.value.push(newItem)
  // Очистка формы
  title.value = ''
  description.value = ''
  imageUrl.value = ''
}

// Удаление элемента
const removeItem = (id) => {
  items.value = items.value.filter(item => item.id !== id)
}

// Сортировка элементов
const sortedItems = computed(() => {
  const copy = [...items.value]
  if (sortBy.value === 'title') {
    return copy.sort((a, b) => a.title.localeCompare(b.title))
  } else {
    return copy.sort((a, b) => b.createdAt - a.createdAt)
  }
})

// Обработчик ошибки загрузки изображения
const handleImageError = (e) => {
  e.target.src = 'https://via.placeholder.com/300?text=Image+not+found'
}
</script>

<style scoped>
.form-required {
  color: red;
}
</style>
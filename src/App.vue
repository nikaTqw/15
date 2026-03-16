<template>
  <div class="container mt-5">
    <h1 class="text-center mb-4">Моя коллекция книг</h1>

    <!-- Форма добавления -->
    <div class="row">
      <div class="col-lg-8 offset-lg-2">
        <div class="card shadow-sm mb-4">
          <div class="card-header bg-primary text-white">
            <h5 class="mb-0">Добавить новую книгу</h5>
          </div>
          <div class="card-body">
            <form @submit.prevent="addBook">
              <div class="row g-3">
                <div class="col-md-6">
                  <input
                    v-model="newBook.title"
                    type="text"
                    class="form-control"
                    placeholder="Название книги"
                    required
                  />
                </div>
                <div class="col-md-6">
                  <input
                    v-model="newBook.author"
                    type="text"
                    class="form-control"
                    placeholder="Автор"
                    required
                  />
                </div>
                <div class="col-md-8">
                  <input
                    v-model="newBook.description"
                    type="text"
                    class="form-control"
                    placeholder="Краткое описание"
                    required
                  />
                </div>
                <div class="col-md-4">
                  <input
                    v-model="newBook.image"
                    type="url"
                    class="form-control"
                    placeholder="URL фото (https://...)"
                  />
                </div>
                <div class="col-12">
                  <button type="submit" class="btn btn-primary w-100">
                    ➕ Добавить книгу
                  </button>
                </div>
              </div>
            </form>
          </div>
        </div>
      </div>
    </div>

    <!-- Сортировка -->
    <div class="row mb-4">
      <div class="col-md-4 offset-md-8">
        <select class="form-select" v-model="sortBy">
          <option value="title">Сортировать по названию</option>
          <option value="author">Сортировать по автору</option>
          <option value="newest">Сначала новые</option>
          <option value="oldest">Сначала старые</option>
        </select>
      </div>
    </div>

    <!-- Сетка книг -->
    <div class="row g-4">
      <div v-for="book in sortedBooks" :key="book.id" class="col-md-6 col-lg-4">
        <div class="card h-100 shadow-sm">
          <!-- Фото книги -->
          <img
            :src="
              book.image || 'https://via.placeholder.com/400x200?text=Нет+фото'
            "
            class="card-img-top"
            :alt="book.title"
            style="height: 200px; object-fit: cover"
          />

          <div class="card-body">
            <h5 class="card-title">{{ book.title }}</h5>
            <h6 class="card-subtitle mb-2 text-muted">{{ book.author }}</h6>
            <p class="card-text">{{ book.description }}</p>
          </div>

          <div
            class="card-footer bg-transparent d-flex justify-content-between align-items-center"
          >
            <small class="text-muted">
              🕒 {{ new Date(book.date).toLocaleDateString() }}
            </small>
            <button
              @click="removeBook(book.id)"
              class="btn btn-sm btn-outline-danger"
            >
              ✕ Удалить
            </button>
          </div>
        </div>
      </div>
    </div>

    <!-- Пустое состояние -->
    <div v-if="books.length === 0" class="text-center py-5">
      <img
        src="https://img.freepik.com/premium-psd/white-bookshelf-with-minimalist-decor-isolated-transparent-background_1092965-15618.jpg?semt=ais_hybrid&w=740"
        class="mb-3"
        alt="Empty"
      />
      <h4>Коллекция пуста</h4>
      <p class="text-muted">Добавьте первую книгу с помощью формы выше</p>
    </div>
  </div>
</template>

<script setup>
import { ref, computed } from "vue";

// Состояние
const books = ref([]);
const sortBy = ref("newest");

// Новая книга
const newBook = ref({
  title: "",
  author: "",
  description: "",
  image: "",
});

// Добавление книги
const addBook = () => {
  books.value.push({
    id: Date.now(),
    ...newBook.value,
    date: Date.now(),
  });

  // Очистка формы
  newBook.value = {
    title: "",
    author: "",
    description: "",
    image: "",
  };
};

// Удаление книги
const removeBook = (id) => {
  if (confirm("Удалить книгу из коллекции?")) {
    books.value = books.value.filter((book) => book.id !== id);
  }
};

// Сортировка книг
const sortedBooks = computed(() => {
  const sorted = [...books.value];

  switch (sortBy.value) {
    case "title":
      return sorted.sort((a, b) => a.title.localeCompare(b.title));
    case "author":
      return sorted.sort((a, b) => a.author.localeCompare(b.author));
    case "newest":
      return sorted.sort((a, b) => b.date - a.date);
    case "oldest":
      return sorted.sort((a, b) => a.date - b.date);
    default:
      return sorted;
  }
});
</script>

<style>
body {
  /* background: linear-gradient(135deg, #667eea 0%, #764ba2 100%); */
  min-height: 100vh;
  padding: 20px 0;
}

.card {
  transition:
    transform 0.3s,
    box-shadow 0.3s;
  border: none;
  cursor: pointer;
}

.card:hover {
  transform: translateY(-5px);
  box-shadow: 0 10px 20px rgba(0, 0, 0, 0.2) !important;
}

.card-img-top {
  border-top-left-radius: 0.5rem;
  border-top-right-radius: 0.5rem;
}

.btn-outline-danger:hover {
  background: #dc3545;
  color: white;
}
</style>

<script setup>
import { ref, onMounted, computed } from "vue";

const posts = ref([]);
const error = ref(null);
const loading = ref(true);
const searchTerm = ref("");

const fetchPosts = async () => {
  try {
    const response = await fetch("https://jsonplaceholder.typicode.com/posts");
    if (!response.ok) {
      throw new Error("Error en la solicitud: " + response.statusText);
    }
    posts.value = await response.json();
  } catch (err) {
    error.value = err.message;
  } finally {
    loading.value = false;
  }
};

const filteredPosts = computed(() => {
  if (!searchTerm.value) {
    return posts.value;
  }
  return posts.value.filter((post) =>
    post.title.toLowerCase().includes(searchTerm.value.toLowerCase()),
  );
});

onMounted(() => {
  fetchPosts();
});
</script>

<template>
  <div>
    <h1>Lista de Posts</h1>

    <div class="search-bar">
      <input
        v-model="searchTerm"
        type="text"
        placeholder="Buscar por título..."
      />
    </div>

    <p v-if="loading">Cargando...</p>
    <p v-else-if="error">Error: {{ error }}</p>

    <ul v-else>
      <li v-for="post in filteredPosts" :key="post.id">
        <h2>{{ post.title }}</h2>
        <p>{{ post.body }}</p>
      </li>
      <li v-if="filteredPosts.length === 0">No se encontraron resultados</li>
    </ul>
  </div>
</template>

<style>
body {
  font-family: Arial, sans-serif;
  background-color: #f4f4f4;
  margin: 0;
  padding: 20px;
}

h1 {
  color: #2c3e50;
  text-align: center;
}

.search-bar {
  display: flex;
  justify-content: center;
  margin: 20px 0;
}

.search-bar input {
  width: 300px;
  padding: 10px 15px;
  border: 1px solid #ccc;
  border-radius: 20px;
  outline: none;
  font-size: 16px;
  transition: 0.3s;
}

.search-bar input:focus {
  border-color: #2c3e50;
  box-shadow: 0 0 5px rgba(44, 62, 80, 0.5);
}

li {
  list-style-type: none;
  margin: 10px 0;
  padding: 15px;
  border: 1px solid #ddd;
  border-radius: 20px;
  background-color: #fff;
  box-shadow: 0 2px 4px rgba(0, 0, 0, 0.05);
}
</style>

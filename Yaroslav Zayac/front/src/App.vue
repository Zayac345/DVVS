<template>
  <div class="post-management">
    <h1>Post Management</h1>

    <div class="post-form">
      <h2>Create/Edit Post</h2>
      <form @submit.prevent="savePost">
        <label for="title">Title:</label>
        <input type="text" v-model="newPost.title" required>

        <label for="description">Description:</label>
        <textarea v-model="newPost.description" required></textarea>

        <label for="author">Author:</label>
        <input type="text" v-model="newPost.author" required>

        <button type="submit">Save Post</button>
      </form>
    </div>

    <div class="all-posts">
      <h2>All Posts</h2>
      <ul>
        <li v-for="(post, index) in posts" :key="index" class="post-item">
          <strong>{{ post.title }}</strong> - {{ post.description }} - {{ post.author }}
          <button @click="editPost(index)">Edit</button>
          <button @click="removePost(index)">Remove</button>
        </li>
      </ul>
    </div>
  </div>
</template>

<script setup>
import { ref, onMounted } from 'vue';
import axios from 'axios';

const posts = ref([]);
const newPost = ref({ title: '', description: '', author: '' });
let editingIndex = ref(null);

const fetchPosts = async () => {
  try {
    const response = await axios.get('http://localhost:8080/posts');
    posts.value = response.data;
  } catch (error) {
    console.error(error);
  }
};

const savePost = async () => {
  try {
    if (editingIndex.value !== null) {
      const response = await axios.put(`http://localhost:8080/posts/${posts.value[editingIndex.value].id}`, newPost.value);
      posts.value[editingIndex.value] = response.data;
      editingIndex.value = null;
    } else {
      const response = await axios.post('http://localhost:8080/posts', newPost.value);
      posts.value.push(response.data);
    }

    newPost.value = { title: '', description: '', author: '' };
  } catch (error) {
    console.error(error);
  }
};

const editPost = (index) => {
  newPost.value = { ...posts.value[index] };
  editingIndex.value = index;
};

const removePost = async (index) => {
  try {
    const response = await axios.delete(`http://localhost:8080/posts/${posts.value[index].id}`);
    if (response.status === 200) {
      posts.value.splice(index, 1);
    } else {
      console.error('Failed to delete post');
    }
  } catch (error) {
    console.error(error);
  }
};

onMounted(fetchPosts);
</script>
<style scoped>
/* Загальні стилі для всього компонента */
.post-management {
  font-family: 'Arial', sans-serif;
  color: #333;
  padding: 30px;
  background-color: #f9f9f9;
  border-radius: 8px;
  box-shadow: 0 4px 8px rgba(0, 0, 0, 0.1);
}

h1 {
  font-size: 2.5em;
  color: #333;
  margin-bottom: 20px;
  text-align: center;
}

h2 {
  font-size: 1.8em;
  margin-bottom: 15px;
  color: #4A90E2;
}

/* Стилі для форми */
.post-form {
  background-color: #fff;
  padding: 20px;
  border-radius: 8px;
  box-shadow: 0 4px 8px rgba(0, 0, 0, 0.05);
  margin-bottom: 30px;
}

.post-form label {
  font-size: 1em;
  margin-bottom: 8px;
  display: block;
  font-weight: bold;
  color: #555;
}

.post-form input, .post-form textarea {
  width: 100%;
  padding: 12px;
  margin: 10px 0 20px 0;
  border-radius: 4px;
  border: 1px solid #ddd;
  font-size: 1em;
  box-sizing: border-box;
}

.post-form textarea {
  min-height: 150px;
  resize: vertical;
}

.post-form button {
  background-color: #4A90E2;
  color: white;
  padding: 12px 20px;
  border: none;
  border-radius: 4px;
  font-size: 1.1em;
  cursor: pointer;
  transition: background-color 0.3s ease;
}

.post-form button:hover {
  background-color: #357ABD;
}

/* Стилі для списку постів */
.all-posts {
  background-color: #fff;
  padding: 20px;
  border-radius: 8px;
  box-shadow: 0 4px 8px rgba(0, 0, 0, 0.05);
}

.all-posts ul {
  list-style-type: none;
  padding: 0;
}

.all-posts .post-item {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 10px 0;
  border-bottom: 1px solid #eee;
}

.all-posts .post-item:last-child {
  border-bottom: none;
}

.all-posts .post-item strong {
  font-size: 1.2em;
  color: #333;
}

.all-posts .post-item button {
  background-color: #f44336;
  color: white;
  padding: 8px 16px;
  border: none;
  border-radius: 4px;
  font-size: 1em;
  cursor: pointer;
  transition: background-color 0.3s ease;
}

.all-posts .post-item button:hover {
  background-color: #d32f2f;
}

.all-posts .post-item button:first-child {
  background-color: #4CAF50;
}

.all-posts .post-item button:first-child:hover {
  background-color: #388E3C;
}
</style>


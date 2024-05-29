<template>
  <div class="ultimate">
    <div class="isi">
    <div class="header">
      <h1>ARTIKELKU</h1>
    <form @submit.prevent="save">
      <span>
      <input type="text" v-model="form.title" /><br />
      <textarea v-model="form.content"></textarea><br />
      </span>
      <button type="submit" class="save-button">Save</button>
    </form>
  </div>
  <div class="main">
    <ul>
      
        
      <li class ="li1" v-for="article in articles" :key="article.id">
        <div class="sec1">
        {{ article.title }}<br />
        {{ article.content }} <br />
        </div>
        <span class="sec2">
        <button class= "mn edit-button"@click="edit(article)">Edit</button>
        <button class= "mn delete-button"@click="deleteArticle(article.id)">Delete</button>
        </span>
      </li>
      
    </ul>
    <button class="loadbut" @click="load">Load</button>
    </div>
  </div>
  </div>
</template>

<script>
import { ref, onMounted, reactive } from 'vue';
import axios from 'axios';

export default {
  setup() {
    const form = reactive({
      id: null,
      title: '',
      content: '',
    });

    const articles = ref([]);

    async function load() {
      try {
        const response = await axios.get('http://localhost:3000/articles');
        articles.value = response.data;
      } catch (error) {
        console.error('Error loading articles:', error);
      }
    }

    async function save() {
      try {
        const url = form.id
          ? `http://localhost:3000/articles/${form.id}`
          : 'http://localhost:3000/articles';
        const method = form.id ? 'put' : 'post';
        const response = await axios[method](url, form);
        
        if (method === 'put') {
          articles.value = articles.value.map((article) => {
            if (article.id === response.data.id) {
              return response.data;
            }
            return article;
          });
        } else {
          articles.value.push(response.data);
        }

        // Assuming successful save, update UI and reset form
        form.id = null;
        form.title = '';
        form.content = '';
      } catch (error) {
        console.error('Error saving article:', error);
      }
    }

    async function deleteArticle(id) {
      try {
        await axios.delete(`http://localhost:3000/articles/${id}`);
        articles.value = articles.value.filter((article) => article.id !== id);
      } catch (error) {
        console.error('Error deleting article:', error);
      }
    }

    function edit(article) {
      form.id = article.id;
      form.title = article.title;
      form.content = article.content;
    }

    onMounted(load);

    return { form, articles, save, edit, load, deleteArticle };
  },
};
</script>

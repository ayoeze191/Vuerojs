<script setup lang="ts">
import { ref, watchEffect } from 'vue'
import axios from 'axios'
// import { createMetaManager } from 'vue-meta';
import { useSeoMeta, useHead, useServerSeoMeta } from '@vueuse/head'
import { RouterLink } from 'vue-router'

interface Photos {
  id: number
  title: string
  albumId: number
  url: string
  thumbnailUrl: string
}

const tasks = ref<Photos[]>([])
const fetchTasks = async () => {
  axios
    .get('https://jsonplaceholder.typicode.com/photos')
    .then((res) => {
      console.log(res)
      tasks.value = res.data.slice(0, 20)
    })
    .catch((err) => {
      console.log(err, 'error tit wa')
    })
}
watchEffect(fetchTasks)

const deleteTask = (id: number) => {
  const newtasks = tasks.value.filter((task) => task.id !== id)
  axios
    .delete(`https://jsonplaceholder.typicode.com/photos/${id}`)
    .then((res) => {
      console.log(res.data, res.status)
      tasks.value = [...newtasks]
    })
    .catch((err) => console.log(err))
}
useHead({
  title: 'My Amazing site',
  meta: [
    {
      property: 'og:title',
      content: 'Your Page Title',
    },
    {
      property: 'og:description',
      content: 'Your Page Description',
    },
    {
      property: 'og:image',
      content: 'URL to Your Image',
    },
    {
      property: 'og:url',
      content: 'URL to Your Page',
    },
    {
      name: 'twitter:card',
      content: 'summary_large_image',
    },
  ],
})
</script>

<template>
  <LandingLayout theme="light">
    <h1 class="header">Photos</h1>
    <div v-if="tasks.length > 0" class="Wrapper">
      <ul v-for="task in tasks" :key="task.id" class="card">
        <!-- Here we are linking to the article detail page with a dynamic "slug" parameter -->
        <RouterLink class="" :to="`/tasks/${task.id}`">
          <h3>
            {{ task.title }}
          </h3>
          <div>
            {{}}
            <img :src="task.url" alt="" />
          </div>
        </RouterLink>
        <button class="task-actions" @click="deleteTask(task.id)">delete</button>
      </ul>
    </div>
    <div v-else class="tasksWrapper">No Tasks!! Please Try Adding Photos</div>
  </LandingLayout>
</template>


<style lang="scss">
.header {
  background-color: #333; /* Background color of the header */
  color: #fff; /* Text color */
  text-align: center;
  padding: 20px 0;
}
.Wrapper {
  width: 100%;
  display: grid;
  gap: 1rem;
  grid-template-columns: 1fr 1fr 1fr 1fr;
  // background-color: red;
}
.card-list {
  width: 100%;
  flex-direction: row;
  display: flex;
  flex-wrap: wrap;
  gap: 20px; /* Adjust the gap between cards */
  justify-content: center; /* Center cards horizontally */
}

/* Style for each individual card */
.card {
  width: 250px;
  background-color: #fff;
  border: 1px solid #ccc;
  border-radius: 5px;
  box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
  padding: 20px;
}
</style>
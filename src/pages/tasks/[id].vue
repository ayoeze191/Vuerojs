<script setup lang="ts">
import { onUpdated, ref, watchEffect } from 'vue'
import axios from 'axios'
// import { createMetaManager } from 'vue-meta';
import { useHead } from '@vueuse/head'

import { useRoute } from 'vue-router'

interface Photo {
  id: number
  title: string
  albumId: number
  url: string
  thumbnailUrl: string
}

const loading = ref(false)
const route = useRoute()

const task = ref<Photo | null>(null)

const fetchTask = async () => {
  const id = (route.params?.id as string) ?? ''
  loading.value = true
  axios
    .get(`https://jsonplaceholder.typicode.com/photos/${id}`)
    .then((res) => {
      console.log(res.data)
      task.value = res.data
    })
    .catch((err) => {
      console.log(err, 'error tit wa')
    })
}
watchEffect(fetchTask)

onUpdated(() => {
  useHead({
    title: 'My blog',
    meta: [
      {
        property: 'og:title',
        content: task.value?.title,
      },
      {
        property: 'og:description',
        content: 'Your Page Description',
      },
      {
        property: 'og:image',
        content: task.value?.url,
      },
    ],
  })
})
</script>

<template>
  <LandingLayout theme="light">
    <!-- {{ task?.title }} -->
    <div class="post">
      <h2>{{ task?.title }}</h2>
      <div class="meta">
        <span>Author: John Doe</span> |
        <span>Date: September 15, 2023</span>
      </div>
      <img :src="task?.url" :alt="task?.title" />
    </div>
  </LandingLayout>
</template>


<style lang="scss">
.post {
  width: 300px;
  background-color: #fff;
  border: 1px solid #ccc;
  border-radius: 5px;
  box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
  padding: 20px;
  margin: 20px auto; /* Center the post horizontally */
}

/* Style for post title */
.post h2 {
  font-size: 24px;
  margin: 0;
}

/* Style for post author and date */
.post .meta {
  font-size: 14px;
  color: #666;
  margin-top: 10px;
}

/* Style for post image */
.post img {
  max-width: 100%;
  height: auto;
  margin-top: 10px;
}

/* Style for post body */
.post p {
  font-size: 16px;
  line-height: 1.5;
  margin-top: 10px;
}
</style>
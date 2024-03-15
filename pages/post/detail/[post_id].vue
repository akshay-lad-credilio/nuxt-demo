<template>
  <div>
    <div v-if="post">
      <h2>{{ post?.title }}</h2>
      <p>{{ post?.body }}</p>
    </div>
    <div v-else>Loading...</div>
  </div>
</template>
<script lang="ts" setup>
import { ref } from 'vue';

const post = ref({})
const route = useRoute()
const postId = route.params.post_id;
const fetchData = async () => {
  try {
    const response = await fetch(`https://jsonplaceholder.typicode.com/posts/${postId}`);
    const data = await response.json();
    post.value = data
  } catch (error) {
    console.error('Error fetching data:', error);
  }
};
fetchData()
</script>

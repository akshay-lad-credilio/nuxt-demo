<template>
  <div>

    <UContainer v-if="post">
      <div>
        <p class="font-semibold inline-block w-20">Id:</p>
        <p class="inline-block w-80">{{ post.id }}</p>
      </div>
      <div>
        <p class="font-semibold inline-block w-20">User Id:</p>
        <p class="inline-block w-80">{{ post.userId }}</p>
      </div>
      <div>
        <p class="font-semibold inline-block w-20 align-top">Title:</p>
        <p class="inline-block w-80">{{ post.title }}</p>
      </div>
      <div>
        <p class="font-semibold inline-block w-20 align-top">Body:</p>
        <p class="inline-block w-80">{{ post.body }}</p>
      </div>
    </UContainer>
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

<template>
  <div>
    <UProgress animation="carousel" class="my-4" v-if="isLoaderShow" />
    <template v-else>
      <div class="flex">
        <div class="w-20">
          <p class="font-semibold">Id:</p>
        </div>
        <div class="grow">
          <p>{{ post.id }}</p>
        </div>
      </div>

      <div class="flex">
        <div class="w-20">
          <p class="font-semibold">User Id:</p>
        </div>
        <div class="grow">
          <p>{{ post.userId }}</p>
        </div>
      </div>

      <div class="flex">
        <div class="w-20">
          <p class="font-semibold">Title:</p>
        </div>
        <div class="grow">
          <p>{{ post.title }}</p>
        </div>
      </div>

      <div class="flex">
        <div class="w-20">
          <p class="font-semibold">Body:</p>
        </div>
        <div class="grow">
          <p>{{ post.body }}</p>
        </div>
      </div>
    </template>
  </div>
</template>
<script lang="ts" setup>
interface Post {
  id: number,
  userId: number,
  title: string,
  body: string
}
const isLoaderShow = ref(true)
const post = ref({} as Post)
const route = useRoute()
const postId = route.params.post;
const fetchData = async () => {
  try {
    isLoaderShow.value = true
    const response = await fetch(`https://jsonplaceholder.typicode.com/posts/${postId}`);
    post.value = await response.json();
    isLoaderShow.value = false
  } catch (error) {
    console.error('Error fetching data:', error);
  }
};

fetchData()
</script>

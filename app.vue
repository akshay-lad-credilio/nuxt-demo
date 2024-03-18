<template>
  <UContainer>
    <UHorizontalNavigation :links="links" class="border-b border-gray-200 dark:border-gray-800 mb-4" />
    <NuxtPage />
  </UContainer>
</template>
<script lang="ts" setup>
const links = [{
  label: 'Posts',
  icon: 'i-heroicons-home',
  to: '/posts'
},
{
  label: 'Employee',
  icon: 'i-heroicons-user',
  to: '/employee'
}]
</script>
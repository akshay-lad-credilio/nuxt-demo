<template>
  <div>

    <UTable :rows="posts" :columns="columnData" :ui="{ td: { base: 'max-w-[0] truncate' } }">
    </UTable>

    <div class="flex justify-end px-3 py-3.5 border-t border-gray-200 dark:border-gray-700">
      <UPagination v-model="currentPage" :page-count="itemsPerPage" :total="totalPosts" :ui="{
      wrapper: 'flex items-center gap-1',
      rounded: '!rounded-full min-w-[32px] justify-center',
      default: {
        activeButton: {
          variant: 'outline'
        }
      }
    }" />
    </div>
  </div>
</template>

<script lang="ts" setup>
import { ref } from 'vue';

const posts = ref([]);
const columnData = ref([
  {
    key: 'id',
    label: 'Id',
  },
  {
    key: 'userId',
    label: 'UserId',
  },
  {
    key: 'title',
    label: 'Title',
  },
  {
    key: 'body',
    label: 'Body',
    class: 'truncate'
  }
]);
const currentPage = ref(1);
const itemsPerPage = 10;
const totalPosts = ref(0);

const fetchData = async () => {
  try {
    const response: any = await fetch(`https://jsonplaceholder.typicode.com/posts?_page=${currentPage.value}&_limit=${itemsPerPage}`);
    const data = await response.json();
    posts.value = data
    totalPosts.value = Math.ceil(parseInt(response.headers.get('x-total-count')));
  } catch (error) {
    console.error('Error fetching data:', error);
  }
};
watch(currentPage, () => {
  fetchData()
});
fetchData()


</script>
<style>
.ellipsis {
  white-space: nowrap;
  overflow: hidden;
  text-overflow: ellipsis;
}
</style>

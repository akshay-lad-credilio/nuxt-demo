<template>
    <div>
        <UProgress animation="carousel" class="my-4" v-if="isLoaderShow" />
        <div class="grid grid-cols-3 gap-4">
            <NuxtLink v-for="post in posts" :key="post.id" class="p-4 rounded bg-gray-100 cursor-pointer"
                :to="`posts/${post.id}`">
                <h2 class="font-semibold truncate border-b border-gray-300 pb-2">{{ post.title }}</h2>
                <p class="pt-2 truncate">{{ post.body }}</p>
            </NuxtLink>
        </div>
        <div class="flex justify-end mt-4 px-3 py-3.5 border-t border-gray-200 border-gray-700">
            <UPagination v-model="currentPage" @click="changePage" :page-count="itemsPerPage" :total="totalPosts" :ui="{
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
interface Post {
    id: number,
    userId: number,
    title: string,
    body: string
}
const router = useRouter();
let posts: Array<Post> = [];
let isLoaderShow = ref(true)
let currentPage = 1;
const itemsPerPage = 10;
let totalPosts = 0;
const postDetail = (post: Post) => {
    router.push(`/posts/${post.id}`);
}
const changePage = () => {
    fetchData()
}
const fetchData = async () => {
    try {
        isLoaderShow.value = true
        const response: any = await fetch(`https://jsonplaceholder.typicode.com/posts?_page=${currentPage}&_limit=${itemsPerPage}`);
        const data: Array<Post> = await response.json();
        isLoaderShow.value = false
        posts = data
        totalPosts = parseInt(response.headers.get('x-total-count'));
    } catch (error) {
        console.error('Error fetching data:', error);
    }
};

fetchData()
</script>
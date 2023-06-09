<template>
    <div class="app">
        <h1>Страница с постами</h1>
        <my-input placeholder="Поиск..." v-model="searchQuery" />
        <div class="app__btns">
            <my-button v-on:click="showDialog">Создать пост</my-button>
            <my-select v-model="selectedSort" :options="sortOptions" />
        </div>
        <MyDialog v-model:show="dialogVisible">
            <PostForm v-on:create="createPost" />
        </MyDialog>
        <PostList
            v-if="!isPostsLoading"
            v-bind:posts="sortedAndSearchedPosts"
            v-on:remove="removePost"
        />
        <div v-else>Идёт загрузка...</div>
        <div class="page__wrapper">
            <div
                v-for="pageNumber in totalPages"
                :key="pageNumber"
                class="page"
                :class="{
                    'current-page': page === pageNumber,
                }"
                @click="changePage(pageNumber)"
            >
                {{ pageNumber }}
            </div>
        </div>
    </div>
</template>

<script>
import PostForm from '@/components/PostForm.vue';
import PostList from '@/components/PostList.vue';
import MyDialog from './components/UI/MyDialog.vue';
import axios from 'axios';

export default {
    components: {
        PostForm,
        PostList,
        MyDialog,
    },

    data() {
        return {
            posts: [],
            dialogVisible: false,
            isPostsLoading: false,
            selectedSort: '',
            searchQuery: '',
            page: 1,
            limit: 10,
            totalPages: 0,
            sortOptions: [
                { value: 'title', name: 'По названию' },
                { value: 'body', name: 'По Содержимому' },
            ],
        };
    },
    methods: {
        createPost(post) {
            this.posts.push(post);
            this.dialogVisible = false;
        },
        removePost(post) {
            this.posts = this.posts.filter((p) => p.id !== post.id);
        },
        showDialog() {
            this.dialogVisible = true;
        },
        changePage(pageNumber) {
            this.page = pageNumber;
            this.fetchPosts();
        },
        async fetchPosts() {
            try {
                this.isPostsLoading = true;
                setTimeout(async () => {
                    const response = await axios.get(
                        'https://jsonplaceholder.typicode.com/posts',
                        {
                            params: {
                                _page: this.page,
                                _limit: this.limit,
                            },
                        }
                    );
                    this.totalPages = Math.ceil(
                        response.headers['x-total-count'] / this.limit
                    );
                    this.posts = response.data;
                    this.isPostsLoading = false;
                }, 100);
            } catch (error) {
                alert('Ошибка');
            }
        },
    },
    mounted() {
        this.fetchPosts();
    },
    computed: {
        sortedPosts() {
            return [...this.posts].sort((post1, post2) =>
                post1[this.selectedSort]?.localeCompare(
                    post2[this.selectedSort]
                )
            );
        },
        sortedAndSearchedPosts() {
            return this.sortedPosts.filter((post) =>
                post.title
                    .toLowerCase()
                    .includes(this.searchQuery.toLowerCase())
            );
        },
    },
};
</script>

<style>
* {
    margin: 0;
    padding: 0;
    box-sizing: border-box;
}

.app {
    padding: 20px;
}

.app__btns {
    margin: 15px 0;
    display: flex;
    justify-content: space-between;
}
.page__wrapper {
    display: flex;
    margin-top: 15px;
}
.page {
    border: 1px solid black;
    padding: 10px;
    cursor: pointer;
}
.current-page {
    border: 2px solid green;
}
</style>

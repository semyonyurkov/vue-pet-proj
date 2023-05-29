<template>
    <div v-if="posts.length > 0">
        <h3>Список пользователей</h3>
        <transition-group name="user-list">
            <PostItem
                v-for="post in posts"
                v-bind:post="post"
                v-bind:key="post.id"
                v-on:remove="$emit('remove', post)"
            />
        </transition-group>
    </div>
    <h2 v-else style="color: brown">Список постов пуст</h2>
</template>

<script>
import PostItem from './PostItem.vue';
export default {
    props: {
        posts: {
            type: Array,
            required: true,
        },
    },
    components: { PostItem },
};
</script>

<style scoped>
.user-list-item {
    display: inline-block;
    margin-right: 10px;
}
.user-list-enter-active,
.user-list-leave-active {
    transition: all 0.4s ease;
}
.user-list-enter-from,
.user-list-leave-to {
    opacity: 0;
    transform: translateX(130px);
}
.user-list-move {
    transition: transform 0.4s ease;
}
</style>

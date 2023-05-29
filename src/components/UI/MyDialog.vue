<template>
    <div v-if="show === true" class="dialog" v-on:click.stop="hideDialog">
        <div v-on:click.stop class="dialog__content">
            <slot></slot>
        </div>
    </div>
</template>

<script>
export default {
    name: 'my-dialog',
    props: {
        show: {
            type: Boolean,
            default: false,
        },
    },
    methods: {
        hideDialog() {
            this.$emit('update:show', false);
        },
    },
};
</script>

<style scoped>
.dialog {
    top: 0;
    bottom: 0;
    right: 0;
    left: 0;
    background: rgba(0, 0, 0, 0.5);
    position: fixed;
    display: flex;
}

.dialog__content {
    margin: 0 auto;
    background: white;
    border-radius: 12px;
    min-height: 50px;
    min-width: 300px;
    padding: 20px;
    height: 250px;
}
</style>

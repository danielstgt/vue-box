<template>
    <component :is="wrapper">
        <slot v-for="(item, index) in items.data" :item="item" :index="index" :items="items"></slot>
    </component>
</template>

<script>
export default {
    props: ['wrapper'],

    inject: ['items']
}
</script>

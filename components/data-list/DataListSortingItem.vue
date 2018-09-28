<template>
    <div class="flex items-center cursor-pointer hover:text-grey-dark" :class="{ 'flex-row-reverse' : arrowSide === 'left' }" @click="switchOrderByDirection(param)">
        {{ name }}

        <svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 256 512" class="fill-current text-blue h-3" :class="arrowMargin" v-show="sorting.orderBy === param && sorting.orderByDirection === 'asc'"><path d="M88 166.059V468c0 6.627 5.373 12 12 12h56c6.627 0 12-5.373 12-12V166.059h46.059c21.382 0 32.09-25.851 16.971-40.971l-86.059-86.059c-9.373-9.373-24.569-9.373-33.941 0l-86.059 86.059c-15.119 15.119-4.411 40.971 16.971 40.971H88z"></path></svg>

        <svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 256 512" class="fill-current text-blue h-3" :class="arrowMargin" v-show="sorting.orderBy === param && sorting.orderByDirection === 'desc'"><path d="M168 345.941V44c0-6.627-5.373-12-12-12h-56c-6.627 0-12 5.373-12 12v301.941H41.941c-21.382 0-32.09 25.851-16.971 40.971l86.059 86.059c9.373 9.373 24.569 9.373 33.941 0l86.059-86.059c15.119-15.119 4.411-40.971-16.971-40.971H168z"></path></svg>
    </div>
</template>

<script>
export default {
    props: ['name', 'param', 'arrowSide'],

    data() {
        return {
            sortSetting: {
                orderBy: this.sorting.orderBy,
                orderByDirection: this.sorting.switchOrderByDirection,
            }
        }
    },

    inject: ['sorting'],

    computed: {
        arrowMargin() {
            return this.arrowSide === 'left' ? 'mr-2' : 'ml-2';
        }
    },

    methods: {
        switchOrderByDirection(name) {
            this.sortSetting.orderBy = name;
            this.sortSetting.orderByDirection = this.sortSetting.orderByDirection === 'asc' ? 'desc' : 'asc';

            this.$emit('sorted', this.sortSetting);
        }
    }
}
</script>

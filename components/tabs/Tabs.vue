<template>
    <div class="mt-4">
        <ul class="list-reset flex justify-between border-b-2 text-grey-dark">
            <li class="flex w-full text-center cursor-pointer" v-for="(tab, index) in tabs" :key="index" :class="{ '-mb-px' : tab.isActive }" @click="selectTab(tab)">
                <div class="flex-1 w-full bg-white inline-block py-4" :class="{ 'text-grey-darkest relative' : tab.isActive }">
                    <div class="w-full absolute border-b-2 pin-b pin-l pin-r" style="border-color: #68c1ee;" v-show="tab.isActive"></div>
                    {{ tab.name }}
                </div>
            </li>
        </ul>

        <div class="pt-20">
            <slot></slot>
        </div>
    </div>
</template>

<script>
    export default {
        data() {
            return {
                tabs: []
            }
        },

        methods: {
            selectTab(selectedTab) {
                this.tabs.forEach(tab => {
                    tab.isActive = (tab.name == selectedTab.name);
                });
            }
        },

        mounted() {
            this.tabs = this.$children;
        }
    }
</script>

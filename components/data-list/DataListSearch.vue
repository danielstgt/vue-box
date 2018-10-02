<template>
    <div class="flex mt-6 mb-4">
        <div class="flex-grow relative">
            <div class="absolute pin-y pin-l ml-3 flex items-center">
                <svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 512 512" class="fill-current h-4" :class="[searchQuery ? 'text-green' : 'text-grey-light']"><path d="M505 442.7L405.3 343c-4.5-4.5-10.6-7-17-7H372c27.6-35.3 44-79.7 44-128C416 93.1 322.9 0 208 0S0 93.1 0 208s93.1 208 208 208c48.3 0 92.7-16.4 128-44v16.3c0 6.4 2.5 12.5 7 17l99.7 99.7c9.4 9.4 24.6 9.4 33.9 0l28.3-28.3c9.4-9.4 9.4-24.6.1-34zM208 336c-70.7 0-128-57.2-128-128 0-70.7 57.2-128 128-128 70.7 0 128 57.2 128 128 0 70.7-57.2 128-128 128z"></path></svg>
            </div>

            <input class="pt-2 pr-9 pb-2 pl-9 text-grey-darker rounded border w-full z-10" type="text" :placeholder="placeholder" v-model="searchQuery">

            <div class="absolute pin-y pin-r mr-3 flex items-center" v-show="searchQuery">
                <svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 512 512" class="fill-current text-grey h-4 cursor-pointer hover:text-red" @click="searchQuery = ''"><path d="M256 8C119 8 8 119 8 256s111 248 248 248 248-111 248-248S393 8 256 8zm121.6 313.1c4.7 4.7 4.7 12.3 0 17L338 377.6c-4.7 4.7-12.3 4.7-17 0L256 312l-65.1 65.6c-4.7 4.7-12.3 4.7-17 0L134.4 338c-4.7-4.7-4.7-12.3 0-17l65.6-65-65.6-65.1c-4.7-4.7-4.7-12.3 0-17l39.6-39.6c4.7-4.7 12.3-4.7 17 0l65 65.7 65.1-65.6c4.7-4.7 12.3-4.7 17 0l39.6 39.6c4.7 4.7 4.7 12.3 0 17L312 256l65.6 65.1z"></path></svg>
            </div>
        </div>

        <pa-dropdown-item v-if="filters.length">
            <button class="pa-btn pa-btn-white ml-2 h-full focus:shadow-none">
                <svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 512 512" class="fill-current h-3 mr-0 md:mr-1" :class="[hasFiltersApplied ? 'text-green' : 'text-grey-dark']"><path d="M487.976 0H24.028C2.71 0-8.047 25.866 7.058 40.971L192 225.941V432c0 7.831 3.821 15.17 10.237 19.662l80 55.98C298.02 518.69 320 507.493 320 487.98V225.941l184.947-184.97C520.021 25.896 509.338 0 487.976 0z"></path></svg>
                <span class="hidden md:inline-block">Filter</span>
            </button>

            <ul class="list-reset py-2 px-4 text-sm" slot="dropdown-content">
                <li class="cursor-pointer flex items-center justify-end group leading-loose" :class="{ 'font-bold text-blue' : getFilterActiveStatus(filter.param) }" @click="toggleFilterStatus(filter.param)" v-for="filter in filters">
                    <span class="group-hover:text-blue">{{ filter.name }}</span>

                    <svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 512 512" class="fill-current h-3 ml-2 group-hover:text-blue" :class="[getFilterActiveStatus(filter.param) ? 'text-blue' : 'text-grey-light']"><path d="M173.898 439.404l-166.4-166.4c-9.997-9.997-9.997-26.206 0-36.204l36.203-36.204c9.997-9.998 26.207-9.998 36.204 0L192 312.69 432.095 72.596c9.997-9.997 26.207-9.997 36.204 0l36.203 36.204c9.997 9.997 9.997 26.206 0 36.204l-294.4 294.401c-9.998 9.997-26.207 9.997-36.204-.001z"></path></svg>
                </li>
            </ul>
        </pa-dropdown-item>
    </div>
</template>

<script>
export default {
    props: ['filters', 'placeholder'],

    data() {
        return {
            searchQuery: '',
            filterStatuses: this.filters,
        }
    },

    computed: {
        filteredQuery() {
            let filters = {};

            this.filterStatuses.forEach(({param, active}) => filters[param] = active);

            return filters;
        },

        hasFiltersApplied() {
            return Object.keys(this.filteredQuery).some(filter => this.filteredQuery[filter] === 1);
        }
    },

    watch: {
        searchQuery() {
            this.$emit('searched', this.searchQuery);
        },

        filterStatuses: {
            handler: function () {
                this.$emit('filtered', this.filteredQuery);
            },

            deep: true
        }
    },

    methods: {
        getFilterActiveStatus(filterParam) {
            let filter = this.filterStatuses.find(({param}) => param === filterParam);

            return filter.hasOwnProperty('active') ? !! filter.active : false;
        },

        toggleFilterStatus(filterParam) {
            let filter = this.filterStatuses.find(({param}) => param === filterParam);

            Vue.set(filter, 'active', (filter.hasOwnProperty('active') ? (1-filter.active) : 1));
        }
    }
}
</script>

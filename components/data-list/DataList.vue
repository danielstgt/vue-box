<template>
    <div>
        <slot name="search" :set-search-query="setSearchQuery" :set-search-filters="setSearchFilters"></slot>

        <div class="relative min-h-24">
            <transition name="fade" enter-active-class="duration-0-4 animated fadeIn" leave-active-class="duration-0-2 animated fadeOut">
                <div :class="{ 'opacity-15' : loading }" v-show="data.length">
                    <slot name="sorting" :set-sorting="setSorting"></slot>
                </div>
            </transition>

            <pa-spinner :loading-text="loaderText" class="absolute pin flex items-center justify-center z-40" v-show="loading"></pa-spinner>

            <slot name="list" :loading="loading"></slot>

            <transition name="fade" enter-active-class="duration-0-4 animated fadeIn" leave-active-class="duration-0-2 animated fadeOut">
                <div class="absolute pin flex flex-col items-center justify-center text-grey" v-show="hasNoSearchResults">
                    <svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 512 512" class="fill-current text-grey-lightest w-8 h-8"><path d="M505 442.7L405.3 343c-4.5-4.5-10.6-7-17-7H372c27.6-35.3 44-79.7 44-128C416 93.1 322.9 0 208 0S0 93.1 0 208s93.1 208 208 208c48.3 0 92.7-16.4 128-44v16.3c0 6.4 2.5 12.5 7 17l99.7 99.7c9.4 9.4 24.6 9.4 33.9 0l28.3-28.3c9.4-9.4 9.4-24.6.1-34zM208 336c-70.7 0-128-57.2-128-128 0-70.7 57.2-128 128-128 70.7 0 128 57.2 128 128 0 70.7-57.2 128-128 128z"></path></svg>

                    {{ noSearchResultsText || 'Keine Suchergebnisse' }}
                </div>
            </transition>
        </div>

        <slot name="paginator" :data-set="dataSet" :get-data="getData"></slot>
    </div>
</template>

<script>
export default {
    props: ['dataSource', 'initialSorting', 'initialSortingDirection', 'loaderText', 'noSearchResultsText', 'scrollToTop'],

    data() {
        return {
            loading: true,
            data: [],
            dataSet: null,
            orderBy: this.initialSorting,
            orderByDirection: this.initialSortingDirection,
            searchQuery: '',
            searchFilters: {},
            searchDelay: true,
        }
    },

    provide() {
        const items = {};
        const sorting = {};

        Object.defineProperty(items, 'data', {
            enumerable: true,
            get: () => this.data,
        });

        Object.defineProperty(sorting, 'orderBy', {
            enumerable: true,
            get: () => this.orderBy,
        });

        Object.defineProperty(sorting, 'orderByDirection', {
            enumerable: true,
            get: () => this.orderByDirection,
        });

        return { items, sorting };
    },

    computed: {
        isSearching() {
            return !! this.searchQuery || Object.keys(this.searchFilters).length;
        },

        hasNoSearchResults() {
            return this.isSearching && ! this.data.length && ! this.loading;
        }
    },

    watch: {
        searchFilters: {
            handler: function () {
                this.loading = true;
                this.getData();
            },

            deep: true
        }
    },

    methods: {
        getData(page) {
            this.loading = true;

            Promise.all([
                this.makeRequest(page),
                new Promise(resolve => setTimeout(resolve, this.searchDelay ? 1000 : 0)),
            ]).then(response => {
                this.data = response[0].data;
                this.dataSet = _.omit(response[0], 'data');

                if (! this.isSearching && this.scrollToTop) {
                    window.scroll({
                        top: 0,
                        behavior: 'smooth'
                    });
                }

                this.loading = false;
                this.searchDelay = true;
            });
        },

        makeRequest(page) {
            return axios.get(this.dataSource, {
                    params: {
                        page: page,
                        search: this.searchQuery,
                        order_by: this.orderBy,
                        order_by_direction: this.orderByDirection,
                        ...this.searchFilters,
                    }
                }).then(({ data }) => data);
        },

        performSearch: _.debounce(function () {
            this.getData();
        }, 500),

        setSearchQuery(str) {
            this.searchQuery = str;
        },

        setSearchFilters(obj) {
            this.searchFilters = obj;
        },

        setSorting(obj) {
            this.orderBy = obj.orderBy;
            this.orderByDirection = obj.orderByDirection;
        }
    },

    mounted() {
        this.$watch(vm => [vm.searchQuery].join(), val => {
            this.searchDelay = false;
            this.loading = true;
            this.performSearch();
        });

        this.$watch(vm => [vm.orderBy, vm.orderByDirection].join(), val => {
            this.loading = true;
            this.getData();
        });

        this.getData();
    }
}
</script>

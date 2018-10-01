<template>
    <ul class="list-reset flex items-center justify-center my-4" role="navigation" v-if="needsPagination">
        <!-- Previous Page Link -->
        <li aria-disabled="true" aria-label="Zurück" v-show="page === 1">
            <span class="pa-btn pa-btn-white font-normal pointer-events-none mr-1 text-grey hover:bg-white" aria-hidden="true">&laquo; Zurück</span>
        </li>

        <li v-show="page !== 1">
            <button class="pa-btn pa-btn-white font-normal mr-1 focus:shadow-none" rel="prev" aria-label="Zurück" @click.prevent="setPage(page-1)">&laquo; Zurück</button>
        </li>

        <!-- Pagination Elements -->
        <template v-for="element in paginationElements">
            <!-- "Three Dots" Separator -->
            <li class="hidden md:block" aria-disabled="true" v-show="Object.keys(element)[0] === 'dots'">
                <span class="pa-btn pa-btn-white font-normal pointer-events-none mx-1 hover:bg-white">...</span>
            </li>

            <!-- Links -->
            <li class="hidden md:block" aria-current="page" v-show="Object.keys(element)[0] == page">
                <span class="pa-btn pa-btn-white font-normal pointer-events-none bg-grey-lighter mx-1 hover:bg-white">{{ Object.keys(element)[0] }}</span>
            </li>

            <li class="hidden md:block" v-show="Object.keys(element)[0] != page && Object.keys(element)[0] !== 'dots'">
                <button class="pa-btn pa-btn-white font-normal mx-1 focus:shadow-none" @click.prevent="setPage(parseInt(Object.keys(element)[0]))">{{ Object.keys(element)[0] }}</button>
            </li>
        </template>

        <!-- Next Page Link -->
        <li v-show="page !== dataSet.last_page">
            <button class="pa-btn pa-btn-white font-normal ml-1 focus:shadow-none" rel="next" aria-label="Vor" @click.prevent="setPage(page+1)">Vor &raquo;</button>
        </li>

        <li aria-disabled="true" aria-label="Vor" v-show="page === dataSet.last_page">
            <span class="pa-btn pa-btn-white font-normal pointer-events-none ml-1 text-grey hover:bg-white" aria-hidden="true">Vor &raquo;</span>
        </li>
    </ul>
</template>

<script>
export default {
    props: ['dataSet'],

    data() {
        return {
            page: 1,
            prevUrl: false,
            nextUrl: false,
            elements: {},
        }
    },

    watch: {
        dataSet() {
            this.buildElements();

            this.page = this.dataSet.current_page;

            this.prevUrl = this.dataSet.prev_page_url;
            this.nextUrl = this.dataSet.next_page_url;
        }
    },

    computed: {
        needsPagination() {
            return !! this.prevUrl || !! this.nextUrl;
        },

        paginationElements() {
            return [
                ...(this.elements.first ? this.elements.first : []),
                _.isArray(this.elements.slider) ? { dots: '...' } : null,
                ...(this.elements.slider ? this.elements.slider : []),
                _.isArray(this.elements.last) ? { dots: '...' } : null,
                ...(this.elements.last ? this.elements.last : []),
            ].filter(l => l);
        }
    },

    methods: {
        buildElements() {
            if (this.dataSet.last_page < 12) {
                return this.getSmallerSlider();
            }

            return this.getUrlSlider();
        },

        getSmallerSlider() {
            this.elements = {
                first: this.getUrlRange(1, this.dataSet.last_page),
                slider: null,
                last: null,
            }
        },

        getUrlSlider() {
            let windowSpan = 6;

            if (! this.dataSet.last_page > 1) {
                this.elements = {
                    first: null,
                    slider: null,
                    last: null,
                }
            }

            if (this.dataSet.current_page <= windowSpan) {
                return this.getSliderTooCloseToBeginning(windowSpan);
            } else if (this.dataSet.current_page > (this.dataSet.last_page - windowSpan)) {
                return this.getSliderTooCloseToEnding(windowSpan);
            }

            return this.getFullSlider(3);
        },

        getSliderTooCloseToBeginning(windowSpan) {
            this.elements = {
                first: this.getUrlRange(1, windowSpan+2),
                slider: null,
                last: this.getUrlRange(this.dataSet.last_page-1, this.dataSet.last_page),
            }
        },

        getSliderTooCloseToEnding(windowSpan) {
            this.elements = {
                first: this.getUrlRange(1, 2),
                slider: null,
                last: this.getUrlRange(this.dataSet.last_page-(windowSpan+2), this.dataSet.last_page),
            }
        },

        getFullSlider(windowSpan) {
            this.elements = {
                first: this.getUrlRange(1, 2),
                slider: this.getAdjacentUrlRange(windowSpan),
                last: this.getUrlRange(this.dataSet.last_page-1, this.dataSet.last_page)
            }
        },

        getAdjacentUrlRange(windowSpan) {
            return this.getUrlRange(this.dataSet.current_page-windowSpan, this.dataSet.current_page+windowSpan);
        },

        getUrlRange(start, end) {
            let range = [];

            for (var i = start; i <= end; i++) {
                range.push({ [i]: this.dataSet.path + '?page=' + i });
            }

            return range;
        },

        setPage(page) {
            this.page = page;

            this.$emit('changed', page);
        }
    }
}
</script>

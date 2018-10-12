<template>
    <div>
        <transition name="fade" enter-active-class="duration-0-4 animated fadeIn" leave-active-class="duration-0-4 animated fadeOut">
            <div class="text-center text-grey">
                <div class="pa-spinner inline-flex">
                    <div class="pa-spinner-circle1"></div>
                    <div class="pa-spinner-circle2 ml-1"></div>
                    <div class="pa-spinner-circle3 ml-1"></div>
                </div>

                <p>{{ loadingText }}</p>
            </div>
        </transition>
    </div>
</template>

<script>
export default {
    props: ['loadingText'],
}
</script>

<style></style>

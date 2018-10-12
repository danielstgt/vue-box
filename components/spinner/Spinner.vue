<template>
    <div>
        <transition name="fade" enter-active-class="duration-0-4 animated fadeIn" leave-active-class="duration-0-4 animated fadeOut">
            <div class="text-center text-grey">
                <div class="vb-spinner inline-flex">
                    <div class="vb-spinner-circle1"></div>
                    <div class="vb-spinner-circle2 ml-1"></div>
                    <div class="vb-spinner-circle3 ml-1"></div>
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

<style>
.vb-spinner > div {
    width: 18px;
    height: 18px;
    @apply .bg-green .rounded-full;
    animation: spinner-bounce-delay 1.4s infinite ease-in-out both, color-animation 2.8s infinite ease-in-out both;
}

.vb-spinner .vb-spinner-circle1 {
    animation-delay: -0.32s;
}

.vb-spinner .vb-spinner-circle2 {
    animation-delay: -0.16s;
}

@keyframes spinner-bounce-delay {
    0%, 80%, 100% {
        transform: scale(0.3);
    }

    40% {
        transform: scale(1.0);
    }
}
</style>

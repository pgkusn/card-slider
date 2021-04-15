<template>
    <div class="container">
        <div :class="['slider', { 'sm': screens.sm, 'md': screens.md }]">
            <transition-group name="flip-list">
                <div
                    v-for="img in showImgs"
                    :key="img.index"
                    class="slide"
                    @click="changeSlide(img.index)"
                >
                    <img :src="img.src" alt="">
                </div>
            </transition-group>
        </div>
        <div class="nav">
            <button @click="changeSlide(currentIndex - 1)">
                Prev
            </button>
            <button @click="changeSlide(currentIndex + 1)">
                Next
            </button>
        </div>
    </div>
</template>

<script>
import { computed, ref, watchEffect } from 'vue';
import { throttle } from 'lodash';
import data from '@/data.json';
import { useMediaSensor, screens } from '@/composition/mediaSensor.js';

export default {
    name: 'App',
    setup () {
        const currentIndex = ref(0);
        const slidesPerView = ref(1);
        const imgs = computed(() => data);
        const allImgs = computed(() => {
            const newImgs = [];
            let count = 0;
            while (newImgs.length < slidesPerView.value + 6) {
                for (const img of imgs.value) {
                    newImgs.push({
                        index: count++,
                        src: img.src
                    });
                }
            }
            return newImgs;
        });
        const showImgs = computed(() => {
            const start = currentIndex.value - 4;
            return allImgs.value.slice(start).concat(allImgs.value.slice(0, start));
        });
        const changeSlide = throttle(index => {
            const lastIndex = allImgs.value.length - 1;
            currentIndex.value = index < 0 ? lastIndex : index > lastIndex ? 0 : index;
        }, 300);

        // response
        useMediaSensor();
        watchEffect(() => {
            slidesPerView.value = screens.sm ? 3 : screens.md ? 5 : 1;
        });

        return {
            imgs,
            allImgs,
            showImgs,
            currentIndex,
            changeSlide,
            screens
        };
    }
};
</script>

<style lang="scss">
* {
    margin: 0;
}
.container {
    overflow: hidden;
}
.slider {
    display: flex;
    margin-left: calc(-4 * 100%);
    width: 100%;
    &.sm {
        margin-left: calc(-3.5 * 50%);
    }
    &.md {
        margin-left: calc(-2.5 * 25%);
    }
}
.slide {
    margin: 10px;
    width: calc(100% - 20px);
    flex-shrink: 0;
    &:first-child,
    &:last-child {
        visibility: hidden;
    }
    > img {
        width: 100%;
        object-fit: cover;
    }
    @at-root .slider.sm > .slide {
        width: calc(50% - 20px);
        cursor: pointer;
    }
    @at-root .slider.md > .slide {
        width: calc(25% - 20px);
    }
}
.nav {
    display: flex;
    justify-content: center;
    > button + button {
        margin-left: 1rem;
    }
}
.flip-list-move {
    transition: transform .5s;
}
</style>

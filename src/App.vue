<template>
    <div class="container">
        <div class="slider">
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
import { computed, ref } from 'vue';
import { throttle } from 'lodash';
import data from '@/data.json';

export default {
    name: 'App',
    setup () {
        const currentIndex = ref(0);
        const imgs = computed(() => data);
        const allImgs = computed(() => {
            const newImgs = [];
            let count = 0;
            while (newImgs.length < 5 + 4) {
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

        return {
            imgs,
            allImgs,
            showImgs,
            currentIndex,
            changeSlide
        };
    }
};
</script>

<style lang="scss">
* {
    margin: 0;
}
.container {
    position: relative;
    overflow: hidden;
    margin: 0 auto;
    max-width: 1200px;
    height: 465px;
}
.slider {
    position: absolute;
    left: calc(50% - 600px);
    display: flex;
    width: 1200px;
    transform: translateX(calc(-2.5 * 25%));
}
.slide {
    margin: 10px;
    width: calc(25% - 20px);
    flex-shrink: 0;
    &:first-child,
    &:last-child {
        visibility: hidden;
    }
    > img {
        width: 100%;
        object-fit: cover;
    }
}
.nav {
    position: absolute;
    bottom: 0;
    left: 0;
    display: flex;
    width: 100%;
    justify-content: center;
    > button + button {
        margin-left: 1rem;
    }
}
.flip-list-move {
    transition: transform .5s;
}
</style>

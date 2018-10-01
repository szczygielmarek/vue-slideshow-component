<template>
    <div class="slideshow slideshow--clip" @keydown.37="prev" @keydown.39="next" tabindex="-1">
        <div ref="wrapper" class="slideshow__wrapper"
            @mousedown="lock" @touchstart="lock" 
            @mouseup="move" @touchend="move"
            @transitionend="handleTransitionEnd">

            <Slider v-for="(image, index) in gallery" :key="index"
                :media="image"
                :className="(index == 0) ? 'slideshow__slide slideshow__slide--visible' : 'slideshow__slide'">
            </Slider>

        </div>

        <button @click="prev" class="slideshow__arrow slideshow__arrow--prev" type="text" aria-label="Previous">&larr;</button>
        <button @click="next" class="slideshow__arrow slideshow__arrow--next" type="text" aria-label="Next">&rarr;</button>
    </div>
</template>

<script>
import Slider from './Slide.vue';

export default {
    name: 'slideshow',
    components: {
        Slider
    },
    data() {
        return {
            width: 0,
            height: 0,
            slides: [],
            firstSlide: null,
            lastSlide: null,
            x0: null,
            locked: false
        }
    },
    props: {
        gallery: {
            type: Array,
            required: true
        }
    },
    computed: {
        sliderLength() {
            return this.slides.length;
        }
    },
    mounted() {
        let { $el } = this;
        let { wrapper } = this.$refs;

        this.slides = $el.querySelectorAll(".slideshow__slide");
        
        this.width = wrapper.clientWidth;
        this.height = wrapper.clientHeight;
        $el.style.setProperty('--width', `${this.width}px`);
        $el.style.setProperty('--height', `${this.height}px`);

        this.init();
    },
    methods: {
        init() {
            let { wrapper } = this.$refs;

            this.firstSlide = wrapper.firstChild;
            let firstClone = this.firstSlide.cloneNode(true);
            firstClone.classList.add("clone");
            firstClone.classList.remove("slideshow__slide--visible");
            
            this.lastSlide = wrapper.lastChild;
            let lastClone = this.lastSlide.cloneNode(true);
            lastClone.classList.add("clone");  
            lastClone.classList.add("slideshow__slide--visible");
            
            wrapper.appendChild(firstClone);
            wrapper.prepend(lastClone);
        },
        prev() {
            let target = this.findLastElemByClassName(this.slides, "slideshow__slide--visible");
            target && target.classList.remove('slideshow__slide--visible');
        },
        next() {
            let target = this.findLastElemByClassName(this.slides, "slideshow__slide--visible");
            target && target.nextElementSibling.classList.add('slideshow__slide--visible');
        },
        handleTransitionEnd(e) {
            if(e.target.classList.contains("clone")) {
                
                this.$el.classList.remove("slideshow--clip");
                this.removeClassesFrom(this.slides, "slideshow__slide--visible");
                e.target.classList.remove("slideshow__slide--visible");
                this.firstSlide.classList.add('slideshow__slide--visible');
               
               setTimeout(() => {
                    this.$el.classList.add("slideshow--clip");
                }, 25);

            } else if(e.target.previousElementSibling.classList.contains("clone")) {
                
                this.$el.classList.remove("slideshow--clip");
                this.addClassesTo(this.slides, "slideshow__slide--visible");
            
                setTimeout(() => {
                    this.$el.classList.add("slideshow--clip");
                }, 25);

            }
        },
        addClassesTo(collection, className) {
            collection.forEach((slide) => {
                slide.classList.add(className);
            });
        },
        removeClassesFrom(collection, className) {
            collection.forEach((slide) => {
                slide.classList.remove(className);
            });
        },
        findElemsByClassName(collection, className) {
            return Array.from(collection).filter(el => {
                return el.classList.contains(className);
            });
        },
        findLastElemByClassName(collection, className) {
            let target = this.findElemsByClassName(collection, className);
            return target[target.length - 1];
        },
        unify(e) {
            return e.changedTouches ? e.changedTouches[0] : e
        },
        lock(e) {
            this.x0 = this.unify(e).clientX;
            this.locked = true
        },
        move(e) {
            if(this.locked) {
                var dx = this.unify(e).clientX - this.x0,
                    s = Math.sign(dx),
                    f = +( s * dx / this.width ).toFixed(2);

                var distance = s * f;
                if(distance > -0.05 && distance < 0.05) return false;
                
                if( distance < 0 ) {
                    this.prev();
                } else {
                    this.next();
                }

                this.x0 = null;
                this.locked = false;
            }
        }
    }
}
</script>

<style lang="scss">

    .slideshow {
        --width: 100vw;
        --height: 100vh;

        position: relative;
        z-index: 1;
        width: 100%;
        height: 100%;
    }
    .slideshow__wrapper {
        position: relative;
        width: 100%;
        height: 100%;

        -webkit-user-select: none;
        -khtml-user-select: none;
        -moz-user-select: none;
        -o-user-select: none;
        user-select: none;
    }
    .slideshow__slide {
        position: absolute;
        top: 0;
        left: 0;
        width: 100%;
        height: 100%;

        clip: rect(0px, 0px, var(--height), 0px);

        .slideshow--clip & {
            transition: clip 0.6s cubic-bezier(0.84, 0.44, 0.165, 0.84);
        }
    }
    .slideshow__slide--visible {
        clip: rect(0px, var(--width), var(--height), 0px);
    }
    
    .slideshow__arrow {
        position: absolute;
        top: 50%;
        transform: translateY(-50%);

        border: none;
        box-shadow: none;
        background-color: transparent;
        color: #fff;
        font-size: 1.5rem;
        cursor: pointer;
    }
    .slideshow__arrow--prev {
        left: 1rem;
    }
    .slideshow__arrow--next {
        right: 1rem;
    }

</style>

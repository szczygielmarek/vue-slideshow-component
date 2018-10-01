<template>
    <figure :class="className" class="slide">

        <img class="slide__img" :src="media" alt="">

    </figure>
</template>

<script>
export default {
    name: 'slide',
    data() {
        return {

        }
    },
    props: {
        className: {
            type: String,
            default: ''
        },
        media: {
            type: String,
            required: true
        }
    }    
}
</script>

<style>

    .slide {
        width: 100%;
        height: 100%;
    }
    .slide__img {
        object-fit: cover;
        width: 100%;
        height: 100%;
    }

</style>


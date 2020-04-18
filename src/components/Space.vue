<template>
    <svg fill="black">
        <cell v-for="point in coordinates"
              :key=point[2]
              :xpos=10*point[0] :ypos=10*point[1]></cell>
        <cell color="grey" :xpos=10*cursor[0] :ypos=10*cursor[1]></cell>
    </svg>
</template>

<script>
    import Cell from './Cell.vue'

    export default {
        name: 'Space',
        components: {
            Cell
        },
        props: {
            coordinates: Array,
            cursor: Array
        }
    }
</script>

<style scoped>
    svg {
        height: calc(100vh - 30px);
        width: calc(100vw - 30px);
        cursor: pointer;
    }
</style>

<template>
    <g>
        <cell v-for="([i, j]) in definition" :key="`C_${i}_${j}`" :xpos=xpos+i :ypos=ypos+j :color="color"/>
    </g>
</template>

<script>
    import Cell from './Cell.vue'

    export default {
        name: "CellCursor",
        components: {
            Cell
        },
        props: {
            xpos: Number,
            ypos: Number,
            definition: Array,
            color: {
                type: String,
                default: "black"
            }
        }
    }
</script>

<style scoped>

</style>
<template>
    <rect :x="10*xpos" :y="10*ypos" width="9" height="9" :fill="color"/>
</template>

<script>
    export default {
        name: "Cell",
        props: {
            xpos: Number,
            ypos: Number,
            color: {
                type: String,
                default: "black"
            }
        }
    }
</script>

<style scoped>

</style>
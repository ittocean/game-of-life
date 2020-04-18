<template>
    <div class="legend">
        <div>
            <span class="command">{{running ? 'Pause' : 'Play'}} = </span>
            <span class="command-key">S/Space Keys</span></div>
        <div>
            <span class="command">
                Speed Down/Speed Up ({{1000 / gcd(1000, speed)}}:{{speed / gcd(1000, speed)}}) =
            </span>
            <span class="command-key">A/D Keys</span>
        </div>
        <div><span class="command">Toggle Cursor ({{cursorType}}) = </span><span class="command-key">W Key</span></div>
        <div><span class="command">Clear Space = </span><span class="command-key">C Key</span></div>
    </div>
</template>

<script>
    export default {
        name: "Legend",
        props: {
            cursorType: String,
            running: Boolean,
            speed: Number
        },
        methods: {
            gcd(a, b) {
                return b ? this.gcd(b, a % b) : a;
            },
        }
    }
</script>

<style scoped>

    .legend {
        font-family: Consolas, serif;
        font-size: 12pt;
    }

    .legend div {
        margin: 10px 15px 0 5px;
        display: inline-block;
    }

    .legend .command {
    }

    .legend .command-key {
        font-weight: bold;
        color: darkblue;
    }

</style>
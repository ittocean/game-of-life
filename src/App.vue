<template>
    <div id="app">
        <Space ref="space" :columns="columns" :rows="rows"
               :cursor-type=cursorType :running=running :speed=speed></Space>
        <Legend :cursor-type=cursorType :running=running :speed=speed></Legend>
    </div>
</template>

<script>
    import Space from './components/Space.vue'
    import Legend from './components/Legend.vue'

    export default {
        name: 'App',
        components: {
            Space, Legend
        },
        data: () => {
            return {
                drawing: false,
                handler: null,
                cursorType: 'pointer',
                cursorTypes: ['pointer',
                    'glider0', 'glider1', 'glider2', 'glider3',
                    'spaceship0', 'spaceship1', 'spaceship2', 'spaceship3'],
                rows: 80,
                columns: 160,
                speed: 200,
                running: false
            }
        },
        created() {
            window.addEventListener('keydown', this.keyPressed);
        },
        methods: {
            keyPressed(event) {
                switch (event.code) {
                    case 'Space':
                    case 'KeyS':
                        this.pauseResume();
                        break;
                    case 'KeyC':
                        this.clear();
                        break;
                    case 'KeyD':
                        this.speedUp();
                        break;
                    case 'KeyA':
                        this.slowDown();
                        break;
                    case 'KeyW':
                        this.toggleCursor();
                        break;
                }
            },
            pauseResume() {
                this.running = !this.running;
            },
            slowDown() {
                this.pauseResume();
                this.speed = Math.min(this.speed + 50, 1000);
                this.pauseResume();
            },
            speedUp() {
                this.pauseResume();
                this.speed = Math.max(this.speed - 50, 50);
                this.pauseResume();
            },
            toggleCursor() {
                const typeIndex = this.cursorTypes.indexOf(this.cursorType);
                this.cursorType = this.cursorTypes[(typeIndex + 1) % this.cursorTypes.length]
            },
            clear() {
                this.$refs.space.clear();
            }
        }
    }
</script>

<style>
    #app {
    }
</style>

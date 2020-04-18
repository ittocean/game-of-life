<template>
    <div id="app" @mousedown="startDrawing" @mousemove="draw" @mouseup="stopDrawing">
        <Space :coordinates="coordinates()" :cursor="cursor"/>
    </div>
</template>

<script>
    import Space from './components/Space.vue'

    //step();

    export default {
        name: 'App',
        components: {
            Space
        },
        created() {
            window.addEventListener('keypress', this.keyPressed)
        },
        data: () => {
            return {
                drawing: false,
                handler: null,
                content: {},
                cursor: [0, 0]
            }
        },
        methods: {
            startDrawing(event) {
                this.drawing = true;
                this.draw(event)
            },
            stopDrawing() {
                this.drawing = false;
            },
            draw(event) {
                const i = Math.floor(event.offsetX / 10);
                const j = Math.floor(event.offsetY / 10);
                this.cursor = [i, j];
                if (!this.drawing) {
                    return;
                }
                this.content[i] = (this.content[i] || []);
                this.content[i][j] = true;
                this.$forceUpdate();
            },
            keyPressed(event) {
                switch (event.code) {
                    case 'Enter':
                    case 'Space':
                        this.pauseResume()
                        break;
                    case 'KeyD':
                        this.clear();
                        break;
                }
            },
            pauseResume() {
                if (this.handler !== null) {
                    clearInterval(this.handler)
                    this.handler = null;
                } else {
                    const self = this;
                    this.handler = setInterval(() => self.tick(), 100)
                }
            },
            clear() {
                this.content = {};
            },
            tick() {
                const nextContent = {};
                this.coordinates().forEach(point => {
                    const community = this.getCommunityCoordinates(point);
                    community.filter(this.isAlive).forEach(member => {
                        nextContent[member[0]] = nextContent[member[0]] || [];
                        nextContent[member[0]][member[1]] = true;
                    })
                });
                this.content = nextContent;
            },
            coordinates() {
                return Object.keys(this.content)
                    .map(i => Object.keys(this.content[i])
                        .filter(j => this.content[i][j])
                        .map(j => [Number.parseInt(i), Number.parseInt(j), `${i}_${j}`])
                    ).flatMap(m => m);
            },
            getSurrounding(point) {
                const [i, j] = point;
                return [
                    [i - 1, j - 1],
                    [i - 0, j - 1],
                    [i + 1, j - 1],
                    [i + 1, j - 0],
                    [i + 1, j + 1],
                    [i - 0, j + 1],
                    [i - 1, j + 1],
                    [i - 1, j - 0]
                ].filter(this.inRange);
            },
            getCommunityCoordinates(point) {
                return [[point], ...this.getSurrounding(point)];
            },
            inRange(p) {
                return p[0] >= 0 && p[0] < 800 && p[1] >= 0 && p[1] < 600;
            },
            isAlive(point) {
                const content = this.content;
                const currentAlive = (content[point[0]] || [])[point[1]];
                const neighbors = this.getSurrounding(point)
                    .map(([i, j]) => (content[i] || [])[j] | 0)
                    .reduce((sum, bit) => sum + bit, 0);
                return currentAlive ?
                    neighbors > 1 && neighbors < 4 :
                    neighbors === 3;
            }
        }
    }
</script>

<style>
    #app {
    }
</style>

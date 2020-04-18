<template>
    <svg :viewBox="'0 0 ' +  columns * 10 + ' ' + rows * 10"
         preserveAspectRatio="xMinYMin meet"
         @mousedown="startDrawing" @mousemove="draw" @mouseup="stopDrawing"
         :style="calcStyle()">
        <!--        <cell v-once v-for="([key, [i, j]]) in eachCell()" :key=key-->
        <!--              :xpos=i :ypos=j color="lightgrey"/>-->
        <cell v-for="([key, [i,j]]) in coordinates()" :key=key :xpos=i :ypos=j></cell>
        <cell-cursor color="orange" :xpos=cursorPos[0] :ypos=cursorPos[1] :definition=cursorDefinition()></cell-cursor>
    </svg>
</template>

<script>
    import Cell from './Cell.vue'
    import CellCursor from './CellCursor.vue'

    export default {
        name: 'Space',
        components: {
            Cell, CellCursor
        },
        data: () => {
            return {
                content: {},
                cursorPos: [0, 0],
                drawing: false,
                handler: null,
            }
        },
        props: {
            rows: Number,
            columns: Number,
            speed: Number,
            cursorType: String,
            running: Boolean
        },
        created() {
            const self = this;
            this.handler = setTimeout(() => self.tick(), this.speed);
        },
        methods: {
            calcStyle() {
                return {
                    height: (this.rows * 10) + 'px',
                    width: (this.columns * 10) + 'px'
                }
            },
            * eachCell() {
                for (let column = 0; column < this.columns; column++) {
                    for (let row = 0; row < this.rows; row++) {
                        yield [`G_${column}_${row}`, [column, row]];
                    }
                }
            },
            clear() {
                this.content = {};
            },
            startDrawing(event) {
                this.drawing = true;
                this.draw(event)
            },
            stopDrawing() {
                this.drawing = false;
            },
            draw(event) {
                const column = Math.floor(event.offsetX / 10);
                const row = Math.floor(event.offsetY / 10);
                this.cursorPos = [column, row];
                if (!this.drawing) {
                    return;
                }
                this.cursorDefinition()
                    .map(([offset_i, offset_j]) => [offset_i + column, offset_j + row])
                    .filter(this.inRange)
                    .forEach(([i, j]) => this.content[`${i}_${j}`] = [i, j]);
                this.$forceUpdate();
            },
            tick() {
                if (this.running) {
                    const nextContent = {};
                    Object.values(this.content).forEach(point => {
                        const community = this.getCommunityCoordinates(point);
                        community.filter(this.isAlive).forEach(member => {
                            nextContent[`${member[0]}_${member[1]}`] = [member[0], member[1]];
                        })
                    });
                    this.content = nextContent;
                }
                const self = this;
                this.handler = setTimeout(() => self.tick(), this.speed);
            },
            coordinates() {
                return Object.entries(this.content);
            },
            getSurrounding(point) {
                const [i, j] = point;
                return [
                    [i - 1, j - 1], [i - 0, j - 1],
                    [i + 1, j - 1], [i + 1, j - 0],
                    [i + 1, j + 1], [i - 0, j + 1],
                    [i - 1, j + 1], [i - 1, j - 0]
                ].filter(this.inRange);
            },
            getCommunityCoordinates(point) {
                return [[point], ...this.getSurrounding(point)];
            },
            inRange(point) {
                const [i, j] = point;
                return i >= 0 && i < this.columns && j >= 0 && j < this.rows;
            },
            isAlive(point) {
                const currentAlive = this.content[`${point[0]}_${point[1]}`];
                const neighbors = this.getSurrounding(point)
                    .map(([i, j]) => !!(this.content[`${i}_${j}`]) | 0)
                    .reduce((sum, bit) => sum + bit, 0);
                return currentAlive ?
                    neighbors > 1 && neighbors < 4 :
                    neighbors === 3;
            },
            cursorDefinition() {
                let cfi;
                let cfj;
                [cfi, cfj] = this.calculateCoEfficients();
                switch (this.cursorType) {
                    case 'glider0':
                    case 'glider1':
                    case 'glider2':
                    case 'glider3':
                        return [[0, 0], [cfi, 0], [cfi * 2, 0], [0, cfj], [cfi, cfj * 2]];
                    case 'spaceship0':
                    case 'spaceship1':
                    case 'spaceship2':
                    case 'spaceship3':
                        return [[0, 0], [cfi, 0], [cfi * 2, 0], [cfi * 3, 0],
                            [0, cfj], [0, cfj * 2],
                            [cfi, cfj * 3], [cfi * 4, cfj], [cfi * 4, cfj * 3]];
                    case 'pointer':
                    default:
                        return [[0, 0]];
                }
            },
            calculateCoEfficients() {
                let index = Number.parseInt(this.cursorType.substr(-1));
                return [index % 2 === 0 ? -1 : 1, index / 2 < 1 ? -1 : 1];
            }
        }
    }
</script>

<style scoped>
    svg {
        cursor: pointer;
        border: 1px solid grey;
    }
</style>

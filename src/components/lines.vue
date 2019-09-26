<template>
    <svg ref="lines-svg" id="lines-svg" xmlns="http://www.w3.org/2000/svg">
       <g>
           <path v-for="path in lines" :d="'M' + path.start.x + ' ' + path.start.y + ' L' + path.end.x + ' ' + path.end.y" stroke="red" stroke-width="10" />
           <path v-if="shouldDrawLine" :d="'M' + this.currentLine.start.x + ' ' + this.currentLine.start.y + ' L' + mousePos.x + ' ' + mousePos.y" stroke-width="10" stroke="green" />
       </g>
    </svg>
</template>
<script>
import { bus } from '../main.js'
export default {
    name: 'pedal-lines',
    data() {
        return {
            lines: {},
            shouldLog: true,
            currentLineId: null,
            currentLine: {
                start: {x: 0, y: 0},
            },
            mousePos: {},
            shouldDrawLine : false,
        }
    },
    methods: {
        onClickedOnInput() {
            bus.$on('line-start', (element) => {
                console.log("Pedal id: " + element.id)
                this.shouldDrawLine = true
                this.startLine(element)
            })
        },

        onReleasedOverInput() {
            bus.$on('line-end', (element) => {
                console.log("Input field id: " + element.id)
                this.shouldDrawLine = false
                this.endLine(element)
            })
        },

        onNewMousePosition () {
            bus.$on('mouse-move', (position) => {
                this.mousePos = position
            })
        },


        startLine(element) {
            let startPos = {}
            startPos.x = element.x
            startPos.y = element.y

            this.currentLine = {}
            this.currentLine.start = startPos
        },

        log(message) {
            if (this.shouldLog == true) {
                console.log(message)
            }
        },

        endLine(element) { 
            if (this.currentLine != null) { // do we have the start point

                let endPos = {}
                endPos.x = element.x
                endPos.y = element.y

                this.log("saving under the id: " + element.id)
                
                if (this.lines[element.id] ==  undefined) { // Indexing by inputs, as they can only have one line
                    this.log("it was undefined indeed")
                    this.lines[element.id] = {}
                }

                this.lines[element.id].start = this.currentLine.start
                this.lines[element.id].end = endPos

                this.currentLine = null
                this.$forceUpdate()

            }else {
                console.warn("there was no currentLine")
            }
        },
    },

    mounted() { // Subscribing to the events
        this.onNewMousePosition()
        this.onClickedOnInput()
        this.onReleasedOverInput()
    },
}
    
</script>

<style lang="scss">
svg { 
    pointer-events: none;
    position: fixed;
    top:0; 
    left:0;
    height:100%;
    width:100%;
}

</style>

<template>
    <svg ref="lines-svg" id="lines-svg" xmlns="http://www.w3.org/2000/svg">
       <g>
           <path v-for="path in lines" :d='
                "M " + path.start.x + ",  " + path.start.y + " " 
                + "C " + getBezierStart(path.start).x + ",  " + getBezierStart(path.start).y + " "
                + getBezierEnd(path.end).x + ",  " + getBezierEnd(path.end).y + " " 
                + path.end.x + ",  " + path.end.y'
                stroke-width="10" stroke="red" fill="none" />

           <path v-if="shouldDrawLine" :d='
                "M " + currentLine.start.x + ",  " + currentLine.start.y + " " 
                + "C " + getBezierStart(currentLine.start).x + ",  " + getBezierStart(currentLine.start).y + " "
                + getBezierEnd(mousePos).x + ",  " + getBezierEnd(mousePos).y + " " 
                + mousePos.x + ",  " + mousePos.y'
                stroke-width="10" stroke="red" fill="none" />
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
            lineBezierOffset: 175,
            currentLine: {
                start: {x: 0, y: 0},
            },
            mousePos: {},
            shouldDrawLine : false,
            endPointIndex: {},
        }
    },
    methods: {
        getBezierStart(start) { 
            let startPoint= {
                x : start.x + this.lineBezierOffset,
                y : start.y
            }
 
            return startPoint
        },

        getBezierEnd(end) {
            let endPoint = {
                x : end.x - this.lineBezierOffset,
                y : end.y
            }
            return endPoint
        },

        getBezierToMouse() {
            let bez = "M " + this.currentLine.start.x + ",  " + this.currentLine.start.y + " " 
                + "C " + this.getBezierPoints().pointA.x + ",  " + this.getBezierPoints().pointA.y + " "
                + this.getBezierPoints().pointB.x + ",  " + this.getBezierPoints().pointB.y + " " 
                + mousePos.x + ",  " + mousePos.y
            return bez
        },

        onClickedOnInput() {
            bus.$on('line-start', (element) => {
                this.shouldDrawLine = true
                this.startLine(element)
            })
        },

        onReleasedOverInput() {
            bus.$on('line-end', (element) => {
                this.shouldDrawLine = false
                this.endLine(element)
            })
        },

        onNewMousePosition () {
            bus.$on('mouse-move', (position) => {
                this.mousePos = position
            })
        },

        onUpdateLineEnd () { // input fields
            bus.$on("update-line-end", (lineEnd) => {
                let index = lineEnd.id
                if (this.lines[index] != undefined) {
                    this.lines[index].end.x = lineEnd.x
                    this.lines[index].end.y = lineEnd.y
                    this.$forceUpdate()
                }
            })
        },

        onUpdateLineStart () { // output fields
            bus.$on("update-line-start", (lineStart) => {
                let indices = this.getIndexFromOutput(lineStart.id)
                if (indices != undefined) {
                    for(let index of indices) {
                        this.lines[index].start.x = lineStart.x
                        this.lines[index].start.y = lineStart.y
                    }
                    this.$forceUpdate()
                }
            })
        },

        getIndexFromOutput (outputId) {
            return this.endPointIndex[outputId]
        },

        startLine(element) {
            let startPos = {}
            startPos.x = element.x
            startPos.y = element.y

            this.currentLine = {}
            this.currentLine.start = startPos
            this.currentLine.id = element.id
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

                if (this.endPointIndex[this.currentLine.id] == undefined) {
                    this.endPointIndex[this.currentLine.id] = []
                }

                this.endPointIndex[this.currentLine.id].push(element.id)

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
        this.onUpdateLineStart()
        this.onUpdateLineEnd()
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

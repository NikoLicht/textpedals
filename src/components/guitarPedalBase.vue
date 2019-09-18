<template>
    <vue-draggable-resizable
        class="pedal-base"
        @dragging="onDrag"
        :parent="true"
        :drag-handle="'.handle'"
        >
        <div class="handle">handle</div>
        <h1> {{ title }} </h1>
            <div class="input-box cancel-drag" v-on:mouseup="isChild">
            <h1 class="input-label">INPUT</h1>
            <div id="inputTexts" v-for="input in limitedInputs" v-bind:key="input">
                <h2 class="input"> {{ input }} </h2>
            </div>
        </div>

        <div class="output-box cancel-drag" v-on:mousedown="isParent">
            <h1 class="output-label">OUTPUT</h1>
            <h2 id="output"> {{ outputText }} </h2>
        </div>
    </vue-draggable-resizable>
</template>

<script>
import { bus } from '../main.js'
import VueDraggableResizable from 'vue-draggable-resizable'

export default {
    name: 'PedalBase',
    props: {
        title: String,
        color: String,
        inputs: Object,
        chainParent: Object,
        chainChild: Array,
        maxInputs: Number,
        connectors: Object,
    },

    data: function () {
        return {
            output: '',
            id: '',
            inputConnections: {},
            width: 0,
            height: 0,
            limitedInputs: [],
            x: 0,
            y: 0
        }
    },

    components: {
        VueDraggableResizable
    },

    watch: {
        inputs: function () {
            console.log('guitarpedalbase update is called, due to change in input')
            this.update()
        },
    },

    computed: {
        outputText() {
            this.debuglog("outputText", this.output)
            return this.output
        }
    },

    methods: {
        fillLimitedInputs: function () {
            let count = 0
            this.limitedInputs = []
            Object.keys(this.inputs).forEach((input) => {
                if (count > this.maxInputs) {
                    console.log("adding the input " + input.text + " to limitedInputs")
                    this.limitedInputs.push(input.text)
                }
                count ++
            })
            this.debuglog("filllimitedInputs", "this is the limited inputs, with length: " + this.limitedInputs.length)
        },

        changeText: function (inputArray) { // This is the funtion that will generate a single string that is the output
            return "init-string"
        },

        sendOutput: function (newOutput) {
            bus.$emit('new-output', newOutput)
        },

        isParent: function () {
            bus.$emit('is-parent', this.id)
        },

        isChild: function () {
            bus.$emit('is-child', this.id)
        },

        calculateId: function () {
            let newId = ''
            function s4 () {
                return Math.floor((1 + Math.random()) * 0x10000)
                    .toString(16)
                    .substring(1)
            }
            newId = s4() + s4() + '-' + s4() + '-' + s4() + '-' + s4() + '-' + s4() + s4() + s4()
            this.id = newId
        },

        saveOutput: function (outputPackage) {
            this.output = outputPackage.text // this is where it happens
            this.sendOutput(outputPackage)
            return outputPackage
        },

        createOutputPackage: function(text) {
            let outputPackage = {}
            outputPackage.text = text
            outputPackage.id = this.id
            return outputPackage
        },

        isExistant: function(text) {
            let testResult = true

            if ( text == null ){
                return testResult
            } else if (text == undefined) {
                return testResult
            } else if (text == '') {
                return testResult
            }
            return false
        },

        debuglog: function (parent, message) {
            console.log("%c   " + this.title + ", " + parent + ": " + message + "  ", 'background: #ffd480; color: #000')
        },

        onDrag: function (x, y) {
            this.x = x
            this.y = y
        },

        update: function () {
            let tempOut = this.changeText(this.inputs) // Calculates the output
            this.debuglog("update", this.output)

            let finalOutput = this.createOutputPackage(tempOut) // Creating the output package
            this.saveOutput(finalOutput) // Sending the output (emitting)
            return this.output // Returning as well
        },

    },

    mounted () {
        this.calculateId()
        this.fillLimitedInputs()
        this.update()
    },

    created () { // here subscribing should happen
        console.log("Created " + this.title);
        if (!this.isExistant(this.inputs)) {
            bus.$on('chain-output', (chainOutput) => { // Subscribing to the chain-output
                if (this.id in chainOutput.recievers) { // If the output is meant for this pedal
                    this.inputs[chainOutput.id] = chainOutput.text
                }
            })
        }
        else {
            console.log("there was no input")
        }

        bus.$on('pedal-family', (family) => { // Subscribing to the pedal-family event
            if (this.id == family.parent) {
                this.chainChild = family.child
            }
            else if (this.id == family.child) {

                if (this.inputs == undefined) {
                    this.inputs = {}
                }
                this.inputs[family.parent] = family.output
                this.chainParent = family.parent
            }

            this.update()
        })
    }
}

</script>

<style scoped lang="scss">
.pedal-base {
    min-height: 335px;
    width: 250px;
    padding-bottom: 1em;
    background-color: #fc8403;
    -webkit-user-select: none;  /* Chrome all / Safari all */
    -moz-user-select: none;     /* Firefox all */
    -ms-user-select: none;      /* IE 10+ */
    user-select: none;          /* Likely future */      border-radius: 25px;

    .input-box, .output-box{
        border-radius: 25px;
        background-color: #f5b46e;
        width: 80%;
        margin-left: auto;
        margin-right: auto;
        min-height: 100px;
        -webkit-user-select: none;  /* Chrome all / Safari all */
        -moz-user-select: none;     /* Firefox all */
        -ms-user-select: none;      /* IE 10+ */
        user-select: none;          /* Likely future */      border-radius: 25px;
    }

    h1{
        padding-top: 1em;
        font-size: 1em;
        color: white;
    }
    .handle{
        background-color: #a89371;
        width: 80%;
        height: 25px;
        border-radius: 25px;
        margin-top: 1em;
        color: white;
        margin-left: auto;
        margin-right: auto;
    }
}

</style>

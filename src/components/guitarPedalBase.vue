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
            <div id="inputTexts" v-for="input in inputs" v-bind:key="input">
                <h2 class="input"> {{ input }} </h2>
            </div>
        </div>

        <div class="output-box cancel-drag" v-on:mousedown="isParent">
            <h1 class="output-label">OUTPUT</h1>
            <h2 id="output"> {{ output }} </h2>
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
        maxInputs: Number
    },
    data: function () {
        return {
            output: '',
            id: '',
            width: 0,
            height: 0,
            x: 0,
            y: 0
        }
    },
    components: {
        VueDraggableResizable
    },
    watch: {
        inputs: function (val) {
            console.log(val)
            console.log('i updated my inputs')
            this.output = this.changeText(this.inputs)
            let finalOutput = {}
            finalOutput.id = this.id
            finalOutput.text = this.output
            this.sendOutput(finalOutput)
            return this.output
        }
    },
    methods: {
        changeText: function (inputArray) {
            return this.saveOutput("a string") 
        },
        sendOutput: function (newOutput) {
            bus.$emit('new-output', newOutput)
        },
        isParent: function () {
            bus.$emit('is-parent', this.id)
        },
        isoutput
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
            console.log("setting output")
        },
        saveOutput (result) {
            this.output = result
            this.sendOutput(result)
            return result
        },
        isEmptyOrNullOrUndefined: function(text) {
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
        onDrag: function (x, y) {
            this.x = x
            this.y = y
        }
    },
    mounted () {
        this.calculateId()
        this.output = this.changeText(this.inputs)
        return this.output
    },
    created () {
        bus.$on('chain-output', (chainOutput) => {
            if (this.id in chainOutput.recievers) {
                console.log('the pedal is me')
                this.inputs[chainOutput.id] = chainOutput.text
                console.log(this.inputs)
            }
        })
    }
}

</script>

<style scoped lang="scss">
.pedal-base {
    min-height: 335px;
    width: 250px;
    padding-bottom: 1em;
    background-color: firebrick;
    border-radius: 25px;

    .input-box, .output-box{
        border-radius: 25px;
        background-color: lightPink;
        width: 80%;
        margin-left: auto;
        margin-right: auto;
    }

    h1{
        padding-top: 1em;
        font-size: 1em;
        color: white;
    }
    .handle{
        background-color: orange;
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

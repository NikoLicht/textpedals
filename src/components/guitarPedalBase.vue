<template>
    <vue-draggable-resizable
        class="pedal-base"
        @dragging="onDrag"
        @dragstop="onDragStop"
        :parent="true"
        :drag-handle="'.handle'"
        >
        <div class="handle">handle</div>
        <h1 class="pedal-title"> {{ title }} </h1>

        <div class="input-fields">
            <InputField class="single-input-field" v-for="inputField in inputFields" :title="inputField.title" type="string" :parentId="id" :parentName="title" >
            </InputField>
        </div>

        <div ref="start" id="start" class="output-box cancel-drag" v-on:mousedown="isParent">
            <h1 class="output-label">OUTPUT</h1>
            <h2 id="output"> {{ outputText }} </h2>
        </div>
    </vue-draggable-resizable>
</template>

<script>
import { bus } from '../main.js'
import VueDraggableResizable from 'vue-draggable-resizable'
import InputField from './inputField.vue'

export default {
    name: 'PedalBase',
    props: {
        title: String,
        color: String,
        chainParent: Object,
        maxInputs: Number,
        inputFields: Array,
        connectors: Object,
    },

    data: function () {
        return {
            id: '',
            inputs: {},
            output: '',
            limitedInputs: [],
            recieverOfoutput : "",
            width: 0,
            height: 0,
            x: 0,
            y: 0,
            shouldLog: false,
        }
    },

    components: {
        VueDraggableResizable,
        InputField
    },

    computed: {
        outputText() {
            this.debuglog("outputText", this.output)
            return this.output
        }
    },

    methods: {
        /* 
            ChangeText function takes the inputs and returns an output
            This is the function to overwrite when making a new textPedal 
        */
        changeText: function (inputArray) {
            return "init-string"
        },

        sendOutput: function (newOutput) {
            bus.$emit('new-output', newOutput)
        },

        log: function (message) {
            if (this.shouldLog = true) {
                console.log(message)
            }
        },

        getPositionOfOutput: function (element) {
            let positions = {}
            positions.x = element.getBoundingClientRect().right
            positions.y = element.getBoundingClientRect().top
            positions.id = this.id
            return positions
        },

        isParent: function () {
            bus.$emit('is-parent', this.id)
            bus.$emit('line-start', this.getPositionOfOutput(this.$refs.start))
        },

        calculateId: function () {
            let newId = ''
            function s4 () {
                return Math.floor((1 + Math.random()) * 0x10000)
                    .toString(16)
                    .substring(1)
            }
            newId = s4() + s4() + '-' + s4() + '-' + s4() + '-' + s4() + '-' + s4() + s4() + s4()
            return newId
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
            this.log("%c   " + this.title + ", " + parent + ": " + message + "  ", 'background: #ffd480; color: #000')
        },

        onDrag: function (x, y) {
            this.x = x
            this.y = y
            // This is where we should handle moving all the connected strings
        },
        
        onDragStop: function () {
            this.log("drag ended")
            bus.$emit('line-start', this.getPositionOfOutput(this.$refs.start))
        },

        update: function () {
            let tempOut = this.changeText(this.inputs) // Calculates the output 

            let finalOutput = this.createOutputPackage(tempOut) // Creating the output package
            this.saveOutput(finalOutput) // Sending the output (emitting)
            return this.output // Returning as well
        },

        onUpdateRequest: function (updateRequest) {
            this.debuglog("update-parent-event", updateRequest)

            if (this.id == updateRequest.recieverId) { // If this is the reviever of the update Request
                // What needs to happen here is that the input for that component must be updated. 
                this.log("---- updateRequest -----")
                this.log("now setting inputs [" + updateRequest.senderName + "] to " + updateRequest.text)
                this.log(updateRequest)
                this.log("---- updateRequest -----")

                this.inputs[updateRequest.senderName] = updateRequest.text

                this.update()
            }
        },

        onPedalFamily: function (family) { 
            if (this.id == family.parent) {
                this.recieverOfoutput = family.child // recieverOfOutput becomes the id of the input  on another pedal
            }
        },
    },

    mounted () {
        this.id = this.calculateId()
        this.log(this.title + "- updated(): this.inputFields : " + this.inputFields)

        for (let inputField of this.inputFields) { //  This should set up every input field, such that it is known
            this.inputs[inputField.title] = "no Input"
            this.log(this.title + "- updated() : set inputs[" + inputField.title + " to: " + "no-Input")
        }

        this.update()
    },

    created () { // here subscribing should happen
        this.log("Created " + this.title);
        bus.$on('pedal-family', (family) => this.onPedalFamily(family))
        bus.$on("update-parent", (updateRequest) => this.onUpdateRequest(updateRequest))

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
    border-radius: 25px;

    .pedal-title{
        font-size: 2em;
        margin: 0.5em;
        padding: 0;
        color: white;
    }

    .output-box{
        border-radius: 25px;
        background-color: #f5b46e;
        width: 80%;
        margin-left: auto;
        margin-right: auto;
        font-size: 0.7em;
        -webkit-user-select: none;  /* Chrome all / Safari all */
        -moz-user-select: none;     /* Firefox all */
        -ms-user-select: none;      /* IE 10+ */
        border-radius: 25px;
    }

    .input-fields{
        border-radius: 25px;
        width: 80%;
        margin-left: auto;
        margin-right: auto;
        -webkit-user-select: none;  /* Chrome all / Safari all */
        -moz-user-select: none;     /* Firefox all */
        -ms-user-select: none;      /* IE 10+ */

        .input-wrapper {
            background-color: #f5b46e;
            width: 100%;
            border-radius: 25px;

            .input-name {
                color: white;
                font-size: 1em;
                font-weight: normal;
            }
        }

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

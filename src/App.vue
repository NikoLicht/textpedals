<template>
    <div @mousemove="mouseMoveEvent" id="app">
        <reversePedal :inputFields="reversePedalInputs" title="Reverse Pedal" ></reversePedal>
        <reverbPedal :inputFields="reverbPedalInputs" title="Reverb Pedal" ></reverbPedal>
        <wahPedal :inputFields="wahPedalInputs" title="Wah Wah" ></wahPedal>
        <wordMixPedal :inputFields="wordMixInputs" title="Mixer"></wordMixPedal>
        <sourcePedal :inputFields="sourceInputs" titel="Source Pedal"></sourcePedal> 

        <pedalLines></pedalLines>

    </div>
    </template>

<script>
import pedalLines from  './components/lines.vue'
import reversePedal from './components/reversePedal.vue'
import sourcePedal from './components/sourcePedal.vue'
import reverbPedal from './components/reverbPedal.vue'
import wahPedal from './components/wahPedal.vue'
import wordMixPedal from './components/wordmixPedal.vue'
import { bus } from './main.js'

export default {
    name: 'app',
    components: {
        pedalLines,
        reversePedal,
        sourcePedal,
        wahPedal,
        wordMixPedal,
        reverbPedal,
    },
    data () {
        return {
            inputStrings: {
                'master': 'I am cool',
            },
            pedals: {},
            shouldLog: false,
            currentParent: null,
            latestOutput: {},
            reversePedalInputs: [
                {
                    title: "mainInput",
                    type: "string",
                    // Add an ID here instead, such that we can see them in the guitar pedal base and move the lines accordingly
                },
            ],
            sourceInputs: [
                {
                    title: "textField",
                    type: "string"
                },
            ],
            wahPedalInputs: [
                {
                    title: "mainInput",
                    type: "string"
                },
            ],
            reverbPedalInputs: [
                {
                    title: "mainInput",
                    type: "string",
                }
            ],
            wordMixInputs: [
                {
                    title: "Input A",
                    type: "string",
                },
                {
                    title: "Input B",
                    type: "string",
                }
            ]

        }
    },
    methods: {
        setChild (childId) {
            this.log('setting child id: ' + childId)
            if (this.currentParent != null) {
                // TODO: check if the parent already has the child

                if (!(this.currentParent in this.pedals)) {
                    this.pedals[this.currentParent] = {}
                }
                this.pedals[this.currentParent][childId] = {}
                this.pedals[this.currentParent][childId].id = childId
                this.setRelationship(this.currentParent, childId) // Letting the pedals know about eachother
                this.currentParent = null
            }
            else{
                console.warn("There was no parent to assign to child")
            }
        },
        setParent (parentId) {
            this.log('setting parent id: ' + parentId)
            this.currentParent = parentId
        },
        setRelationship(parentId, childId) {
            let family = {}
            family.parent = parentId
            family.child = childId
            family.output = this.latestOutput[parentId];
            bus.$emit('pedal-family', family)
        },
        passOutput (output) { // Sends on the information to everyone who needs it.
            this.log('App revcieved the input: ' + output.text)

            let chainOutput = {} // Creating the output object
            chainOutput.text = output.text // adding the text
            chainOutput.id = output.id // Adding the id
            chainOutput.recievers = this.pedals[output.id] // Setting the recievers to the recievers of the pedal

            if (chainOutput.recievers != undefined) {
                bus.$emit('chain-output', chainOutput) // If there are recievers, then send the information
            } else {
                console.warn("tried to send event to all recievers, but there were not any") // Else just print this warning
            }
        },

        log: function (message) {
            if (this.shouldLog == true) {
                console.log(message)
            }
        },

        createSVGCanvas () {
            let svg = document.createElementNS("http://www.w3.org/2000/svg", "svg");
        },

        mouseMoveEvent(event) {
            let newPos = {}
            newPos.x = event.pageX
            newPos.y = event.pageY
            bus.$emit("mouse-move", newPos)
        },
    },

    created () {
        this.createSVGCanvas()

        bus.$on('is-child', (childId) => {
            this.setChild(childId)
        })
        bus.$on('is-parent', (parentId) => {
            this.setParent(parentId)
        })
        bus.$on('new-output', (output) => {
            this.log('recieved the output - ' + output.text)
            this.latestOutput[output.id] = output.text
            this.passOutput(output)
        })
    }

}

</script>

<style lang="scss">
body{
    padding:0;
    margin: 0;
}
#app {
    margin: 0;
    padding: 0;
    width: 100%;
    height: 100%;
    height: 100vh;
    background-color: #20ca95;
    font-family: 'Avenir', Helvetica, Arial, sans-serif;
    -webkit-font-smoothing: antialiased;
    text-align: center;
    color: #2c3e50;
    position: relative
}
</style>

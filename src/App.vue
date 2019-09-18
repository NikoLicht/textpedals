<template>
    <div id="app">
        <reversePedal :inputs="inputStrings" :maxInputs="2"  title="Reverse Pedal" ></reversePedal>
        <testPedal :inputs="inputStrings"  :maxInputs="2" title="Test Pedal" ></testPedal>
    </div>
    </template>

<script>
import reversePedal from './components/reversePedal.vue'
import testPedal from './components/testPedal.vue'
import { bus } from './main.js'

export default {
    name: 'app',
    components: {
        testPedal,
        reversePedal
    },
    data () {
        return {
            inputStrings: {
                'master': 'dog',
            },
            pedals: {},
            currentParent: null,
            latestOutput: {},
        }
    },
    methods: {
        setChild (childId) {
            console.log('setting child id: ' + childId)
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
        },
        setParent (parentId) {
            console.log('setting parent id: ' + parentId)
            this.currentParent = parentId
        },
        setRelationship(parentId, childId) {
            let family = {}
            family.parent = parentId
            family.child = childId
            family.output = this.latestOutput[parentId]; // Where and how do i keep track of what the different pedals have outputted? 
            bus.$emit('pedal-family', family)
        },
        passOutput (output) { // Sends on the information to everyone who needs it.
            console.log('App revcieved the input: ' + output.text)

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
    },

    created () {
        bus.$on('is-child', (childId) => {
            this.setChild(childId)
        })
        bus.$on('is-parent', (parentId) => {
            this.setParent(parentId)
        })
        bus.$on('new-output', (output) => {
            console.log('recieved the output - ' + output.text)
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

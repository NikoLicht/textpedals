<template>
    <div id="app">
        <testPedal :inputs="inputStrings" :maxInputs="4" title="test pedal" ></testPedal>
        <reversePedal :inputs="inputStrings" :maxInputs="4" title="reverse Pedal" ></reversePedal>
    </div>
</template>

<script>
import reversePedal from './components/reversePedal.vue'
import wordmixPedal from './components/wordmixPedal.vue'
import wahPedal from './components/wahPedal.vue'
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
                'mister': 'cat',
                'maestro': 'butter'
            },
            pedals: {},
            currentParent: null,
            latestOutput: null
        }
    },
    methods: {
        newWord () {
            console.log(`dont just click on things`)
        },

        setChild (childId) {
            console.log('setting child id: ' + childId)
            if (this.currentParent != null) {
                // TODO: check if the parent already has the child

                if (!(this.currentParent in this.pedals)) {
                    this.pedals[this.currentParent] = {}
                }
                this.pedals[this.currentParent][childId] = {}
                this.pedals[this.currentParent][childId].id = childId

                console.log(this.pedals)
                console.log('connected a child with a parent')
                this.currentParent = null
            }
        },
        setParent (parentId) {
            console.log('setting parent id: ' + parentId)
            this.currentParent = parentId
        },
        passOutput (output) { // Sends on the information to everyone who needs it.
            console.log('App revcieved the input: ' + output.text)
            let chainOutput = {}
            chainOutput.text = output.text
            chainOutput.id = output.id
            chainOutput.recievers = this.pedals[output.id]
            if (chainOutput.recievers != undefined) {
                bus.$emit('chain-output', chainOutput)
            } else {
                console.warn("tried to send event to all recievers, but there were not any")
            }
        }

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
            this.latestOutput = output.text
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

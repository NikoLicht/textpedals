<template>
    <div id="app" v-on:click="newWord" >
        <reversePedal :inputs="inputStrings"  title="Reverser Pedal" ></reversePedal>
        <wordmixPedal :inputs="inputStrings"  title="Word Mixer Pedal" ></wordmixPedal>
    </div>
</template>

<script>
import reversePedal from './components/reversePedal.vue'
import wordmixPedal from './components/wordmixPedal.vue'
import { bus } from './main.js'

export default {
    name: 'app',
    components: {
        reversePedal,
        wordmixPedal
    },
    data () {
        return {
            inputStrings: ['testin a string', 'javacript code run'],
            pedals: {},
            currentParent: null
        }
    },
    methods: {
        newWord () {
            this.inputStrings.push(this.inputStrings[this.inputStrings.lenght - 1] + this.inputStrings[0][1])
        },

        setChild (childId) {
            console.log('setting child id: ' + childId)
            if (this.currentParent != null) {
                // TODO: check if the parent already has the child

                if (!(this.currentParent in this.pedals)) {
                    this.pedals[this.currentParent] = {}
                }
                this.pedals[this.currentParent][childId] = childId
                this.currentParent = null
            }
        },
        setParent (parentId) {
            console.log('setting parent id: ' + parentId)
            this.currentParent = parentId
        }

    },

    created () {
        // bus.$on('calculated-new-output',
        bus.$on('is-child', (childId) => {
            this.setChild(childId)
        })
        bus.$on('is-parent', (parentId) => {
            this.setParent(parentId)
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
}
</style>

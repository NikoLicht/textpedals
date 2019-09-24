<template>
    <div class="input-wrapper" v-on:mouseup="isChild">
        <h1 ref="lineEnd" class="input-name"> {{title}} </h1>
    </div>
</template>

<script>
import { bus } from '../main.js'

export default {
    name: 'InputField',
    props: {
        title: String,
        type: String,
        parentId: String,
        parentName: String,
    },

    data: function () {
        return {
            value: "",
            connectedComponentId: "",
            id: "",
            shouldLog: false,
        }
    },

    computed: {
        getValue: function() {
            return this.value
        },
    },

    methods: {

        // Just a helper-function, that checks of something is null, undefined or ""
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

        log: function (message) {
            if (this.shouldLog = true) {
                console.log(message)
            }
        },

        isChild: function () {
            bus.$emit('is-child', this.id)
        },

        getPositionOfInput(element){
            let positions = {}
            positions.x = element.getBoundingClientRect().left
            positions.y = element.getBoundingClientRect().top // consider getting the lowest 
            return positions
        },

        sendPosition() {
            bus.$emit('line-end', this.getPositionOfInput(this.$refs.lineEnd))
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
        
        subscribeToFamilyEvent: function () {
            bus.$on('chain-output', (chainOutput) => { // Subscribing to the chain-output
                if (this.id in chainOutput.recievers) { // If the output is meant for this pedal
                    this.value = chainOutput.text // Saving the output of the other pedal as value
                    this.sendPosition() // Emits the position for the lines
                    this.log(this.title + ": this output was meant for me")
                }
                else {
                    this.log(this.parentName + " - " + this.title + ": this output was not meant for me")
                }
            })

            bus.$on('pedal-family', (family) => { // Subscribing to the pedal-family event
                if (this.id == family.child) {
                    this.connectedComponentId = family.output
                }
                this.updateParent()
            })
        },

        // When new information have been passed to the 
        updateParent: function () {
            let updateObject = {}
            updateObject.recieverId = this.parentId
            updateObject.senderId = this.id
            updateObject.text = this.value
            updateObject.senderName = this.title

            bus.$emit("update-parent", updateObject) // sending object with all information
        },

    },

    created () { // this should be where we subscribe
        this.id = this.calculateId()
        this.subscribeToFamilyEvent()
        this.updateParent()
    }
}

</script>

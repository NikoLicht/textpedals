<template>
    <div v-on:mouseup="isChild" class="input-wrapper">
        <h1 ref="lineEnd" class="input-name"> 
            {{title}} 
        </h1>
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
        inputFieldId: String,
    },

    data: function () {
        return {
            value: "",
            connectedComponentId: "",
            inputFieldShouldLog: false,
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
            if (this.inputFieldShouldLog == true) {
                console.log(message)
            }
        },

        isChild: function () {
            bus.$emit('is-child', this.inputFieldId)
            this.sendPosition() // Emits the position for the lines
        },

        getPositionOfInput: function (thisInputField){ // This is the position of the 
            let position = {}
            position.x = thisInputField.getBoundingClientRect().left
            position.y = thisInputField.getBoundingClientRect().top // consider getting the lowest 
            position.id = this.inputFieldId
            return position
        },

        onNewPosition: function () {
            bus.$on("update-inputField", (updateRequest) => {
                if (updateRequest.id == this.inputFieldId) {
                    this.updateLine()
                }
            })
        },

        updateLine: function () {
            bus.$emit('update-line-end', this.getPositionOfInput(this.$refs.lineEnd))
        },

        sendPosition: function() {
            bus.$emit('line-end', this.getPositionOfInput(this.$refs.lineEnd))
        },

        subscribeToFamilyEvent: function () {
            bus.$on('chain-output', (chainOutput) => { // Subscribing to the chain-output
                if (this.inputFieldId in chainOutput.recievers) { // If the output is meant for this pedal
                    this.value = chainOutput.text // Saving the output of the other pedal as value
                    this.log(this.title + ": this output was meant for me")
                }
                else {
                    this.log(this.parentName + " - " + this.title + ": this output was not meant for me")
                }
            })

            bus.$on('pedal-family', (family) => { // Subscribing to the pedal-family event
                if (this.inputFieldId == family.child) {
                    this.connectedComponentId = family.parent
                }
                this.updateParent()
            })
        },

        // When new information have been passed to the 
        updateParent: function () {
            let updateObject = {}
            updateObject.recieverId = this.parentId
            updateObject.senderId = this.inputFieldId
            updateObject.text = this.value
            updateObject.senderName = this.title

            bus.$emit("update-parent", updateObject) // sending object with all information
        },

    },

    created () { // this should be where we subscribe
        this.onNewPosition()
        this.subscribeToFamilyEvent()
        this.updateParent()
    }
}

</script>
